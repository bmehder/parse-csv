<script>
	import Papa from 'papaparse'

	const ACH_KEY = 'Invoice Num'
	const QB_KEY = 'Num'

	const ACH_AMOUNT = 'Debit'
	const QB_AMOUNT = 'Amount'

	const achDisplayColumns = [
		'Invoice Num',
		'Product Group',
		'Debit',
		'Credit',
		'Open Balance',
	]

	const qbDisplayColumns = [
		'Vendor',
		'Num',
		'Date',
		'Due Date',
		'Amount',
		'Open Balance',
	]

	const achNumericColumns = ['Debit', 'Credit', 'Open Balance']

	const qbNumericColumns = ['Amount', 'Open Balance']

	let achRows = $state([])
	let qbRows = $state([])

	let hideZeroDollarInvoices = $state(false)

	const normalize = value => {
		const normalized = String(value ?? '')
			.trim()
			.toLowerCase()

		return normalized.replace(/\.0$/, '')
	}

	const parseAmount = value => {
		const cleaned = String(value ?? '')
			.replace(/\$/g, '')
			.replace(/,/g, '')
			.trim()

		return Number(cleaned) || 0
	}

	const money = value =>
		new Intl.NumberFormat('en-US', {
			style: 'currency',
			currency: 'USD',
		}).format(value)

	const parseFile = async file => {
		const text = await file.text()

		return new Promise((resolve, reject) => {
			Papa.parse(text, {
				header: true,
				skipEmptyLines: true,
				complete: result => resolve(result.data),
				error: reject,
			})
		})
	}

	const hasValue = row => column => normalize(row[column]) !== ''

	const loadAchFile = async event => {
		const file = event.target.files[0]

		if (!file) return

		const rows = await parseFile(file)

		achRows = rows.filter(row => hasValue(row)(ACH_KEY))
	}

	const loadQbFile = async event => {
		const file = event.target.files[0]

		if (!file) return

		const rows = await parseFile(file)

		qbRows = rows.filter(row => hasValue(row)(QB_KEY))
	}

	const sumColumn = (rows, column) =>
		rows.reduce((total, row) => total + parseAmount(row[column]), 0)

	const sumColumns = (rows, columns) =>
		Object.fromEntries(columns.map(column => [column, sumColumn(rows, column)]))

	let achTotal = $derived(sumColumn(achRows, ACH_AMOUNT))

	let qbTotal = $derived(sumColumn(qbRows, QB_AMOUNT))

	let difference = $derived(achTotal - qbTotal)

	let missingFromQb = $derived.by(() => {
		const qbKeys = new Set(qbRows.map(row => normalize(row[QB_KEY])))

		return achRows.filter(row => {
			const amount = parseAmount(row[ACH_AMOUNT])

			if (hideZeroDollarInvoices && amount === 0) {
				return false
			}

			return !qbKeys.has(normalize(row[ACH_KEY]))
		})
	})

	let missingFromAch = $derived.by(() => {
		const achKeys = new Set(achRows.map(row => normalize(row[ACH_KEY])))

		return qbRows.filter(row => {
			const amount = parseAmount(row[QB_AMOUNT])

			if (hideZeroDollarInvoices && amount === 0) {
				return false
			}

			return !achKeys.has(normalize(row[QB_KEY]))
		})
	})

	let missingFromQbTotals = $derived(sumColumns(missingFromQb, achNumericColumns))

	let missingFromAchTotals = $derived(sumColumns(missingFromAch, qbNumericColumns))

	let missingFromQbTotal = $derived(missingFromQbTotals[ACH_AMOUNT] ?? 0)

	let missingFromAchTotal = $derived(missingFromAchTotals[QB_AMOUNT] ?? 0)

	let netDifference = $derived(missingFromQbTotal - missingFromAchTotal)

	let hasFiles = $derived(achRows.length > 0 && qbRows.length > 0)

	const isNumericColumn = column =>
		achNumericColumns.includes(column) || qbNumericColumns.includes(column)

	const formatCell = (row, column) => {
		if (isNumericColumn(column)) {
			return money(parseAmount(row[column]))
		}

		return row[column] ?? ''
	}
</script>

<div class="uploads">
	<label>
		ACH File
		<input type="file" accept=".csv" onchange={loadAchFile} />
	</label>

	<label>
		QuickBooks File
		<input type="file" accept=".csv" onchange={loadQbFile} />
	</label>
</div>

{#if hasFiles}
	<label class="checkbox">
		<input type="checkbox" bind:checked={hideZeroDollarInvoices} />

		Hide zero-dollar invoices
	</label>

	<section class="summary">
		<h2>Summary</h2>

		<table>
			<tbody>
				<tr>
					<th>ACH Total</th>
					<td>{money(achTotal)}</td>
				</tr>

				<tr>
					<th>QuickBooks Total</th>
					<td>{money(qbTotal)}</td>
				</tr>

				<tr>
					<th>Difference</th>
					<td>{money(difference)}</td>
				</tr>

				<tr>
					<th>Missing From QB</th>
					<td>{money(missingFromQbTotal)}</td>
				</tr>

				<tr>
					<th>Missing From ACH</th>
					<td>{money(missingFromAchTotal)}</td>
				</tr>

				<tr>
					<th>Net Difference</th>
					<td>
						<strong>{money(netDifference)}</strong>
					</td>
				</tr>
			</tbody>
		</table>
	</section>

	<section>
		<h2>
			Missing From QuickBooks ({missingFromQb.length})
		</h2>

		<table>
			<thead>
				<tr>
					{#each achDisplayColumns as column}
						<th>{column}</th>
					{/each}
				</tr>
			</thead>

			<tbody>
				{#each missingFromQb as row}
					<tr>
						{#each achDisplayColumns as column}
							<td>
								{formatCell(row, column)}
							</td>
						{/each}
					</tr>
				{/each}
			</tbody>

			<tfoot>
				<tr>
					<th>Total</th>
					<th></th>

					{#each achNumericColumns as column}
						<th>
							{money(missingFromQbTotals[column])}
						</th>
					{/each}
				</tr>
			</tfoot>
		</table>
	</section>

	<section>
		<h2>
			Missing From ACH ({missingFromAch.length})
		</h2>

		<table>
			<thead>
				<tr>
					{#each qbDisplayColumns as column}
						<th>{column}</th>
					{/each}
				</tr>
			</thead>

			<tbody>
				{#each missingFromAch as row}
					<tr>
						{#each qbDisplayColumns as column}
							<td>
								{formatCell(row, column)}
							</td>
						{/each}
					</tr>
				{/each}
			</tbody>

			<tfoot>
				<tr>
					<th>Total</th>
					<th></th>
					<th></th>
					<th></th>

					{#each qbNumericColumns as column}
						<th>
							{money(missingFromAchTotals[column])}
						</th>
					{/each}
				</tr>
			</tfoot>
		</table>
	</section>
{/if}

<style>
	.uploads {
		display: grid;
		gap: 1rem;
		margin-bottom: 2rem;
	}

	label {
		display: grid;
		gap: 0.25rem;
	}

	.checkbox {
		display: flex;
		align-items: center;
		gap: 0.5rem;
		margin-bottom: 2rem;
	}

	input[type='checkbox'] {
		width: auto;
	}

	input[type='file'] {
		padding: 0.5rem;
	}

	table {
		width: 100%;
		border-collapse: collapse;
		margin-block: 1rem 2rem;
		font-variant-numeric: tabular-nums;
	}

	th,
	td {
		padding: 0.5rem;
		border: 1px solid var(--color-slate-500);
		text-align: left;
	}

	th {
		background: var(--color-slate-100);
		color: black;
	}

	tfoot th {
		background: var(--color-slate-200);
		color: black;
	}

	td {
		vertical-align: top;
	}
</style>
