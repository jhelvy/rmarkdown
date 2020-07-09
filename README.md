This fork fixes the outdated font awesome icons issue in the navbar:
https://github.com/rstudio/rmarkdown/issues/1554

To install this version of rmarkdown with the icon fix until the bug is fixed use:
`devtools::install_github('jhelvy/rmarkdown')`

Following the advice in the open issue, replace the `navbar_icon_dependencies()` function in the `html_dependencies.R` file with this:
```
navbar_icon_dependencies <- function(navbar) {

    # read the navbar source
    source <- read_utf8(navbar)

    # find icon references
    res <- regexec('<(span|i) +class *= *("|\') *(fab fa|fas fa|ion ion)-', source)
    matches <- regmatches(source, res)
    libs <- c()
    for (match in matches) {
        if (length(match) > 0)
            libs <- c(libs, match[[4]])
    }
    libs <- unique(libs)

    # return their dependencies
    html_dependencies_fonts("fab fa" %in% libs | "fas fa" %in% libs,"ion ion" %in% libs)
}
```

And replace the `navbar_link_text()` function in the `html_document.R` file with this:
```
navbar_link_text <- function(x, ...) {

    if (!is.null(x$icon)) {
        tagList(tags$span(class = paste(x$icon)), " ", x$text, ...)
    }
    else
        tagList(x$text, ...)
}
```

After making these changes, you have to spell out the entire font awesome icon type in the site menu, e.g. `fab fa-python` instead of just `fa-python`.
