library(shiny)
library(readxl)
library(dplyr)
library(plotly)
library(shinyjqui)
library(shinyWidgets) 

ui <- fluidPage(
  titlePanel("Volcano Plot Interactive Viewer"),
  sidebarLayout(
    sidebarPanel(
      fileInput("input_file", "Upload Excel File (.xlsx)", accept = ".xlsx"),
      numericInput("log2fc_cutoff", "Log2 Fold Change Cutoff", value = 1),
      numericInput("padj_cutoff", "Adjusted p-value Cutoff", value = 0.05),
      tags$hr(),
      numericInput("x_limit_min", "X-axis Min", value = NA, step = 0.1, width = "50%"),
      numericInput("x_limit_max", "X-axis Max", value = NA, step = 0.1, width = "50%"),
      numericInput("y_limit_min", "Y-axis Min", value = NA, step = 0.1, width = "50%"),
      numericInput("y_limit_max", "Y-axis Max", value = NA, step = 0.1, width = "50%"),
      numericInput("dot_size", "Dot Size", value = 6, min = 1, step = 1, width = "100%"),
      colorPickr("deg_color", "DEGs Color", selected = "orange"),
      colorPickr("bg_color", "Background Color", selected = "grey")
    ),
    mainPanel(
      jqui_resizable(
        plotlyOutput("volcano_plot", height = "600px", width = "100%")
      )
    )
  )
)

server <- function(input, output, session) {
  input_data <- reactive({
    req(input$input_file)
    read_excel(input$input_file$datapath)
  })
  
  observeEvent(input$input_file, {
    req(input_data())
    
    filtered_data <- input_data() %>%
      filter(padj <= input$padj_cutoff)
    
    updateNumericInput(session, "x_limit_min", value = -max(abs(input_data()$log2FoldChange)) * 1.05)
    updateNumericInput(session, "x_limit_max", value = max(abs(input_data()$log2FoldChange)) * 1.05)
    updateNumericInput(session, "y_limit_min", value = 0)
    updateNumericInput(session, "y_limit_max", value = max(-log10(filtered_data$padj)) * 1.05)
  })
  
  filtered_data <- reactive({
    req(input_data())
    input_data() %>%
      dplyr::filter(abs(log2FoldChange) >= input$log2fc_cutoff, padj <= input$padj_cutoff)
  })
  
  output$volcano_plot <- renderPlotly({
    req(input_data())
    
    # Add a new column to the data frame for the color
    input_data() %>%
      mutate(color = ifelse(abs(log2FoldChange) >= input$log2fc_cutoff & padj <= input$padj_cutoff, input$deg_color, input$bg_color))
    
    # Filter the data frame based on the cutoffs and create separate data frames for orange and grey dots
    df_pass <- input_data() %>% dplyr::filter(abs(log2FoldChange) >= input$log2fc_cutoff & padj <= input$padj_cutoff)
    df_fail <- input_data() %>% dplyr::filter(!(abs(log2FoldChange) >= input$log2fc_cutoff & padj <= input$padj_cutoff))
    
    p <- plot_ly() %>%
      add_trace(x = ~df_pass$log2FoldChange, y = ~-log10(df_pass$padj), text = ~df_pass$gene, type = "scatter", mode = "markers",
                marker = list(color = input$deg_color, size = input$dot_size)) %>%
      add_trace(x = ~df_fail$log2FoldChange, y = ~-log10(df_fail$padj), text = ~df_fail$gene, type = "scatter", mode = "markers",
                marker = list(color = input$bg_color, size = input$dot_size)) %>%
      layout(title = "Volcano Plot",
             xaxis = list(title = "Log2 Fold Change", range = c(-max(abs(input_data()$log2FoldChange)), max(abs(input_data()$log2FoldChange))),
                          showline = TRUE, linewidth = 2, linecolor = "black", autorange = FALSE, 
                          range = c(input$x_limit_min, input$x_limit_max)),
             yaxis = list(title = "-log10 Adjusted p-value", range = c(0, max(-log10(input_data()$padj))),
                          showline = TRUE, linewidth = 2, linecolor = "black", autorange = FALSE,
                          range = c(input$y_limit_min, input$y_limit_max)),
             hovermode = "closest",
             shapes = list(
               list(type = "line", x0 = -input$log2fc_cutoff, x1 = -input$log2fc_cutoff, y0 = 0, y1 = 1,
                    line = list(color = "red", width = 1, dash = "dash"), yref = "paper"),
               list(type = "line", x0 = input$log2fc_cutoff, x1 = input$log2fc_cutoff, y0 = 0, y1 = 1,
                    line = list(color = "red", width = 1, dash = "dash"), yref = "paper"),
               list(type = "line", x0 = -max(abs(input_data()$log2FoldChange)), x1 = max(abs(input_data()$log2FoldChange)), y0 = -log10(input$padj_cutoff), y1 = -log10(input$padj_cutoff),
                    line = list(color = "red", width = 1, dash = "dash"), xref = "x", yref = "y")
             ))
    
    # Set the x and y axis limits based on user input
    if (!is.na(input$x_limit_min)) {
      p <- p %>% layout(xaxis = list(range = c(input$x_limit_min, input$x_limit_max)))
    }
    if (!is.na(input$y_limit_min)) {
      p <- p %>% layout(yaxis = list(range = c(input$y_limit_min, input$y_limit_max)))
    }
    
    return(p)
    
  })
}
 
shinyApp(ui, server)
                          