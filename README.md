# Table Of Contents

* [Setup](#setup)
  - [Step 0 - New Package](#0.-Create-a-new-package-file)
  - [Step 1 - DESCRIPTION]()
  - [Step 2 - Package Loading]()
  - [Step 3 - TODO Management]()
  - [Step 4 - Data/]()
  - [Step 5 - .Rbuildignore]()
* [Analysis]()
  - [Step 1 - Writing .Rmd]()
  - [Step 2 - New Functions]()
  - [Step 3 - Unit Tests]()
  - [Step 4 - Iteration]()
  - [Step 5 - Documentation]()
  - [Step 6 - Documentation Compilation]()
* [References]()
* [Clean Code]()
  - [Step 1 - Devtools Check]()
  - [Step 2 - Lintr]()
  - [Step 3 - README]()
  - [Step 4 - CHANGELOG]()
* [Tips]()

# SETUP

[Return To Table Of Contents](#table-of-contents)

<center>

<img src="./img/dia1.png">

</center>

## 0. Create a new package file

[Return To Table Of Contents](#table-of-contents)

> file --> New Project --> New/Existing Directory --> R Package

## 1. Fill out description file

[Return To Table Of Contents](#table-of-contents)

```yaml
Package: workflow
Title: A Robust workflow for software driven data analysis
Version: 0.0.1
Authors@R: 
    person(given = "Bryan",
           family = "Jenks",
           role = c("aut", "cre"),
           email = "bryanjenks@protonmail.com",
           comment = c(ORCID = "0000-0002-9604-3069"))
Description: A Robust document for discussing a great way to structure analysis.
License: MIT file LICENSE # The license can be written out in the 'LICENSE' File
Encoding: UTF-8
LazyData: true
```

## 2. use the package loading script

[Return To Table Of Contents](#table-of-contents)

This way it just loops over a vector of the packages and installs what isnt alread installed and loads what is installed so it is available for the RMarkdown product.

```r
# Package names
packages <- c("magrittr", "here", "DiagrammeR", "todor")

# Install packages not yet installed
installed_packages <- packages %in% rownames(installed.packages())
if (any(installed_packages == FALSE)) {
  install.packages(packages[!installed_packages])
}

# Packages loading
lapply(packages, library, character.only = TRUE) %>%
  invisible()
```

## 3. TODO management

[Return To Table Of Contents](#table-of-contents)

If you have multiple files or a large RMarkdown document and you use commented `<!-- TODO/BUG/FIXME/HACK -->` items and want to see where all of them are then use the `todor` package with the following snippet

```r
# Create a vector of document paths in the current directory (use with HERE() package)
# This is great for multiple R markdown documents
docs <- dir(pattern = "*.Rmd") %>%
    as.vector()
todor::todor(file = docs)

# A less hacky way of checking a whole PACKAGE for TODO's is just the built in function:
todor::todor_package()
```

## 4. Create Data/

[Return To Table Of Contents](#table-of-contents)

Create the `Data/` directory to hold raw data files that will be cleaned and processed by `R` scripts in the `R/` directory for the RMarkdown document when sourced.

## 5. Update .Rbuildignore

[Return To Table Of Contents](#table-of-contents)

When building a package for installation and reproducablilty have the build process ignore certain files, driectories and other things that it shouldn't touch during the build process

**OPTIONAL** 

if keeping the package in `GIT` version control then also update the `.gitignore`


# ANALYSIS

[Return To Table Of Contents](#table-of-contents)

<center>

<img src="./img/dia2.png">

</center>

## 1. Begin Writing Your Content

[Return To Table Of Contents](#table-of-contents)

In your RMarkdown Document you can begin filling in your content with what ever template or way you prefer to write in your document. There are many ways to convey the results and workflow of your analysis, you have a package, a single stand alone RMarkdown document, a bookdown book, HTML output only, theres a million ways to perofrm an analysis and this is just going to be a document about some of the more common parts of the workflow with nuances left to personalization and preference.

Never use `require()` or `library()` in a packaged analysis, put these items in the DESCRIPTION file as imports or suggests to import them

For local file management in the `.Rproj` project directory, i and many many others prefer to use the `here` package that uses the project root directory as the relative root and use relative directory references to reference other files in your package.

## 2. Create New R Function as needed

[Return To Table Of Contents](#table-of-contents)

functions into seperate `R` script files in `R/` and if there are a lot of functions group their filenames with some sort of convention that groups them `AAA_Function.R`

## 3. Write Unit Tests

[Return To Table Of Contents](#table-of-contents)

To start using unit tests `devtools::use_testthat()`

to run all current tests `Ctrl + Shift + T` or `devtools::test()`

## 4. Test, Fix, Iterate

[Return To Table Of Contents](#table-of-contents)

Run your tests on your developing functions and fix any **ERRORS**, **WARNINGS**, or **NOTES** that come up 

## 5. Document Completed R Functions

[Return To Table Of Contents](#table-of-contents)

Use `roxygen2` documentation on all functions script files in `R/` 

- **First line:** Title
- **Second line:** Description
- **Subsequent lines:** Details

[Documentation Info](http://r-pkgs.had.co.nz/man.html)

## 6. Compile Your Documentaiton

[Return To Table Of Contents](#table-of-contents)

Run `devtools::document()` (or press `Ctrl + Shift + D` in RStudio) to compile your documents into function documentation that appears in the `man/` directory and the NAMESPACE that contains all `@export` functions.

# REFERENCES

[Return To Table Of Contents](#table-of-contents)

Writing a bibliography for your R packages

```r
# automatically create a bib database for R packages
knitr::write_bib(c(
  .packages(), 'bookdown', 'knitr', 'rmarkdown'
), 'packages.bib')
```

in your `yaml` portion of the RMarkdown document you can use a `yaml` array to contain multiple `.bib` files to have one solely for your R Packages that are generated from the code chunk above and also any other cited sources you wish to compile manually or otherwise. like so:

```yaml
bibliography: [cited.bib, packages.bib]
```

# CLEAN CODE

[Return To Table Of Contents](#table-of-contents)

<center>

<img src="./img/dia3.png">

</center>

## 1. Devtools Check

[Return To Table Of Contents](#table-of-contents)

To check if your pacakge is ready for distribution and installable use: 

`devtools::check()`, or press `Ctrl + Shift + E` in RStudio. to check your package for ERRORS, WARNINGS, or NOTES

## 2. Lintr

[Return To Table Of Contents](#table-of-contents)

use `lintr` for linting your R code

```r
# Good suggestions for making legible and consistently formatted code 
lintr::lint_package()
```

## 3. README

[Return To Table Of Contents](#table-of-contents)

Use `README.md` file for github or just general user info, even keep an `.Rmd` document that compiles to a markdown document if you so wish that can be used to explain the package to users in a medium --> long form format so the user knows what to do to reproduce the analysis or use the package.

## 4. NEWS/CHANGELOG

[Return To Table Of Contents](#table-of-contents)

Use `NEWS.md` as the CHANGELOG for your package

i start all changelogs with semantic versioning from the 'Keep a change log project' a snippet of that changelog might look like this:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## Summary

> Given a version number MAJOR.MINOR.PATCH, increment the:
>
> 1. MAJOR version when you make incompatible API changes,
> 2. MINOR version when you add functionality in a backwards compatible manner, and
> 3. PATCH version when you make backwards compatible bug fixes.
>
> Additional labels for pre-release and build metadata are available as extensions to the MAJOR.MINOR.PATCH format.

### Guiding Principles

> Changelogs are for _humans_, not machines.
> There should be an entry for every single version.
> The same types of changes should be grouped.
> Versions and sections should be linkable.
> The latest version comes first.
> The release date of each version is displayed.
> Mention whether you follow [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

### Types of changes

> Added for new features.
> Changed for changes in existing functionality.
> Deprecated for soon-to-be removed features.
> Removed for now removed features.
> Fixed for any bug fixes.
> Security in case of vulnerabilities.

## [Unreleased]

### Added

-

### Changed

-

### Deprecated

-

### Removed

-

### Fixed

-

### Security

-

## [0.0.1] - 2020-01-22

### Added

- Core functionality of a-z alphabet translation to Elder Futhark
- all special characters, numeric, or otherwise all ignored as pass through
- New function `runes_table()` create a 3 column data frame with the unicode sequence, transcription, and character of the entire Elder Futhark alphabet to be used for inputting into documents, reference, or any other purpose

### Changed

- new parameter to `runes()` `hide=FALSE` is now the default option but when set to true, the English 'x' & 'q' characters will not pass through the function and appear at all since there is no equivalent rune.
    + added unit tests to support this new parameter option

### Deprecated

-

### Removed

-

### Fixed

-

### Security

-


```

# TIPS

[Return To Table Of Contents](#table-of-contents)

- Using `# TEXT -----` inside an R code chunk adds it to the table of contents of the RMarkdown document
