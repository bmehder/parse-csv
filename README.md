

# Parse CSV

A browser-based tool for comparing two CSV exports and identifying discrepancies.

Originally built to compare ACH payment data against QuickBooks invoice exports, this project helps users quickly determine which records exist in one file but not the other.

## Features

- Upload two CSV files
- Compare records between datasets
- Identify records missing from either file
- Calculate totals and differences
- Optionally hide zero-dollar invoices
- Runs entirely in the browser
- No server required
- No data leaves your computer

## How It Works

The application compares two CSV exports:

- **ACH File** — payment data
- **QuickBooks File** — accounting data

Records are matched using invoice numbers.

After comparison, the application reports:

- Total value of each file
- Overall difference between files
- Records missing from QuickBooks
- Records missing from the ACH file
- Net discrepancy

## Using the App

1. Export your ACH data as a CSV file.
2. Export your QuickBooks invoice data as a CSV file.
3. Upload both files.
4. Review the summary.
5. Investigate any missing records.

If desired, enable **Hide zero-dollar invoices** to remove invoices that have no financial impact from the results.

## Privacy

All processing happens locally in your browser.

Your CSV files are never uploaded to a server.

## Development

This project is built with:

- Svelte 5
- SvelteKit
- Papa Parse
- Tailwind CSS v4

### Install

```bash
npm install
```

### Run

```bash
npm run dev
```

### Build

```bash
npm run build
```

## Project Structure

Most of the application's logic lives in:

```txt
src/lib/components/ParseCSV.svelte
```

This component is responsible for:

- Parsing uploaded CSV files
- Normalizing invoice identifiers
- Matching records between datasets
- Calculating totals and differences
- Rendering comparison tables

## Contributing

Contributions are welcome.

Potential areas for improvement include:

- Support for additional CSV formats
- Configurable matching fields
- Exporting discrepancy reports
- Improved filtering and search
- Automated tests
- Accessibility enhancements

## License

MIT