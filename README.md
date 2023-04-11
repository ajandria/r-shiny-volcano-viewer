# Volcano Plot Interactive Viewer

This Shiny app allows users to interactively visualize gene expression data in the form of a volcano plot. Users can upload their data in Excel (.xlsx) format, and customize various plot parameters, such as axis limits, dot size, and colors.

## Features

- Upload gene expression data in Excel (.xlsx) format
- Set custom log2 fold change and adjusted p-value cutoffs
- Adjust the X and Y axis limits
- Customize dot size
- Choose colors for differentially expressed genes (DEGs) and background
- View gene names while hovering over data points

## App Structure

The app consists of a `ui` and `server` function. 

### UI

The user interface is composed of a title panel, a sidebar panel containing input controls, and a main panel displaying the interactive volcano plot. The input controls include:

- File input for uploading an Excel file
- Numeric inputs for log2 fold change and adjusted p-value cutoffs
- Numeric inputs for X and Y axis limits
- Numeric input for dot size
- Color pickers for DEGs and background colors

### Server

The server function contains several reactive expressions and an output for rendering the plot. Key components of the server function include:

- Reactive expression for reading the uploaded Excel file
- Reactive expression for filtering the data based on user-defined cutoffs
- Observe event for updating the default axis limits based on the uploaded data
- Render function for creating the interactive volcano plot using the `plotly` package

## Dependencies

The app relies on the following R packages:

- `shiny`: For building the web application
- `readxl`: For reading Excel files
- `dplyr`: For data manipulation
- `plotly`: For creating interactive plots
- `shinyjqui`: For resizable plots
- `shinyWidgets`: For additional input controls (color pickers)

## Usage

To run the app, you need to have R installed on your system. Install the required packages if you haven't already, and then run the following command in the R console:

```R
shiny::runGitHub("ajandria/r-shiny-volcano-viewer")
```

After running the command, the app should launch in your default web browser.

## Example Data Format

The expected input data should be in Excel (.xlsx) format, with the following columns:

- gene: Gene name
- log2FoldChange: Log2 fold change values
- padj: Adjusted p-values

An example row in the data might look like this:

gene        | log2FoldChange | padj
---------------------------------------
GeneA       | 1.5            | 0.01
