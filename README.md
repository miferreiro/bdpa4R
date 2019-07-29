
[![CRAN\_Status\_Badge](http://www.r-pkg.org/badges/version/bdpar)](https://cran.r-project.org/package=bdpar)
[![rstudio mirror
downloads](http://cranlogs.r-pkg.org/badges/bdpar)](https://github.com/metacran/cranlogs.app)
[![](https://cranlogs.r-pkg.org/badges/grand-total/bdpar)](https://cran.r-project.org/package=bdpar)
[![Build
Status](https://travis-ci.org/miferreiro/bdpar.svg?branch=develop)](https://travis-ci.org/miferreiro/bdpar)
[![lifecycle](https://img.shields.io/badge/lifecycle-maturing-blue.svg)](https://www.tidyverse.org/lifecycle/#maturing)
[![codecov](https://codecov.io/gh/miferreiro/bdpar/branch/develop/graph/badge.svg)](https://codecov.io/gh/miferreiro/bdpar)

# Big Data Preprocessing Architecture

<div style="text-align: justify">

bdpar is a a tool to easily build customized data flows to pre-process
large volumes of information from different sources. To this end, bdpar
allows to (i) easily use and create new functionalities and (ii) develop
new data source extractors according to the user needs. Additionally,
the package provides by default a predefined data flow to extract and
preprocess the most relevant information (tokens, dates, … ) from some
textual sources (SMS, emails, tweets, YouTube comments).

</div>

## Description 📄

<div style="text-align: justify">

In order to get the most out of the customization capabilities, the
application has two modes of execution of the *Pipes*:

  - Simple mode.
  - Advanced mode.

</div>

-----

#### *Simple mode*

<div style="text-align: justify">

The simple mode allows the tools to be executed through a single
function in which the configuration file (with the option to use the
default configuration file, **configurationsTemplate.ini** in the case
that the value of the parameter is NULL), the option to edit the input
configuration file, the path of the folder that contains the files to be
preprocessed, the flow of selected *Pipes* and the mode in which that
the types of *Instances* are created.

</div>

``` r
output <- pipeline_execute(configurationFilePath = NULL, 
                           editConfigurationFile = FALSE,
                           filesPath = "folderWithFiles",
                           pipe = SerialPipes$new(),
                           instanceFactory = InstanceFactory$new())
```

#### *Advanced mode*

<div style="text-align: justify">

The advanced mode allows (i) improve the customization of the tool and
(ii) have a better control over the different stages of the preprocess.
Specifically, it provides greater versatility in cases that want to
modify the created object, for example, adding a function or an atribute
that the user needs to realize their own preprocessing.

</div>

``` r
bdpar_object <- Bdpar$new(configurationFilePath = NULL,
                          editConfigurationFile = FALSE) 
bdpar_object$proccess_files(filesPath,
                            pipe = SerialPipes$new(), 
                            instanceFactory = InstanceFactory$new())
```

<div style="text-align: justify">

The configuration file is used to store the different configuration
parameters of the pipes used in the preprocessing. For example, to
indicate the keys used to work with the APIs that require it (such as
YouTube or Twitter) as well as various configuration parameters that
allow to customize the behavior of the application such as the choice of
text format to use in case there are multipart emails (plain text or
text in html format). It is important to keep in mind that if the
parameters are not needed, the value can be omitted. The description of
the structure of the configuration file can be accessed through the
package help interface (?Bbp4aR). It is important to indicate that the
tool has a default template that can be modified by the user through the
parameter *editConfigurationFile*, in both simple and advanced mode.

The following is the template that the configuration file
(**configurationsTemplate.ini**) have initially:

</div>

``` ini
[twitter] 
ConsumerKey = <<consumer_key>>
ConsumerSecret = <<consumer_secret>>
AccessToken = <<access_token>>
AccessTokenSecret = <<access_token_secret>>

[youtube] 
app_id = <<app_id>>
app_password = <<app_password>>

[eml] 
PartSelectedOnMPAlternative= <<part_selected>> (text/html or text/plain)
 
[resourcesPath]

resourcesAbbreviationsPath = <<resources_abbreviations_path>>
resourcesContractionsPath = <<resources_contractions_path>>
resourcesInterjectionsPath = <<resources_interjections_path>>
resourcesSlangsPath = <<resources_slangs_path>>
resourcesStopWordsPath = <<resources_stop_words_path>>
 
 
[CSVPath]
outPutTeeCSVPipePath = <<out_put_TeeCSVPipe_path>>

[cache] 
cachePathTwtid = <<cache_path_twtid>>
cachePathYtbid = <<cache_path_ytbid>>
```

<div style="text-align: justify">

Regarding the flow of pipes used, the application provides a default
flow implemented in the *SerialPipes* class. This method has been
implemented in such a way that it picks up the exceptions thrown by the
flow defined in the superclass, that is, the *pipeAll* method of the
*TypePipe* class. However, in order to adapt to the needs of each user,
the application allows the design of new preprocessing flows. For this,
it is necessary to create a class that inherits from *TypePipe* and
implements the *pipeAll* method.

</div>

<div style="text-align: justify">

Once a new preprocessing flow has been created, the user can both use
and customize the 18 *Pipes* included by default in the application, as
well as define *Pipes* that implement new functionalities. For this, it
is necessary to create a class that inherits from *PipeGeneric* and
implements the new functionality within the *pipe* method. In the case
of using *Pipes* by default, you can consult more information in the
package documentation through the command ?bdpar.

</div>

<div style="text-align: justify">

In case you want to introduce different types of extensions, you will
first need to create a class that inherits from the *Instance* class
which implements the abstract methods: *obtainSource* and *obtainDate*.
In addition, you must create a subclass that overrides the
*createInstance* method of the *InstanceFactory* class, which comes by
default, to decide on what is based to create one type of *Instance* or
another. It should be noted that although normally it is decided
according to the extension of the file, the user can decide other
criterion according to their needs.

On the other hand, the types of the files which are implemented by
default are:

</div>

|       File type       | Extension |
| :-------------------: | :-------: |
|          SMS          |   .tsms   |
|         Email         |   .eml    |
|       ID Tweet        |  .twtid   |
| ID comment of YouTube |  .ytbid   |

## Operation mode ⚙

![Figure 1. Pipelining Operation
process](additional-material/PipelineProcess.png)

*Figure 1. Pipelining Operation process*

![Figure 2. Pipelining Operation
process](additional-material/PipelineExample.png)

*Figure 2. Pipelining Operation example*

## Pre-requisites 📋

#### Required software

  - **R (\>= 3.5.0)**
  - **Python 2.7**

#### Required libraries

  - **Imports**:

|        | R Libraries |       |
| :----: | :---------: | :---: |
|  ini   |  magrittr   | pipeR |
| purrr  |     R6      | rlist |
| svMisc |    tools    | utils |

  - **Suggests**:

|           | R Libraries |           |
| :-------: | :---------: | :-------: |
|   cld2    |    knitr    |   readr   |
|    rex    |    rjson    | rmarkdown |
|  rtweet   |   stringi   |  stringr  |
| textutils |    tuber    |           |

##### Suggested configuration for not USA people

<div style="text-align: justify">

In order to succesfully handle files following UTF-8 enconding, it is
recommended to configure R environment with the parameters locale
parameters “en\_US.UTF-8” (see Sys.getlocale() to check the default
locale). Additionally, use Sys.setlocale() function to modify your R
locale value.

</div>

## Installation 🔧

#### Install the development version from GitHub:

``` r
devtools::install_github('miferreiro/bdpar')
```

## Build with 🛠️

  - [RStudio](https://www.rstudio.com/)
      - The programming environment was
    used.
  - [roxygen2](https://cran.r-project.org/web/packages/roxygen2/index.html)
      - Uses to generate the documentation.

## Contributing 🖇️

<div style="text-align: justify">

Please, read the
<a href = https://github.com/miferreiro/bdpar/blob/master/CONTRIBUTING.md>CONTRIBUTING.md</a>
for details of our code of conduct, and the process to send us pull
requests.

</div>

## Wiki 📖

You can find much more about how to use this project in our
[Wiki](https://github.com/miferreiro/bdpar/wiki).

## Version 📌

We use [SemVer](http://semver.org/) for versioning. For all available
versions, look at the [tags in this
repository](https://github.com/miferreiro/bdpar/tags).

## Authors ✒️

  - **Miguel Ferreiro Díaz** - *Developer* -
    [miferreiro](https://github.com/miferreiro)
  - **David Ruano Ordás** - *Project Manager* -
    [drordas](https://github.com/drordas)
  - **Tomás R. Cotos Yañez** - *Project Manager* -
    [tomas-cotos](https://github.com/tomas-cotos)

You can see the list of all contributors on
<https://github.com/miferreiro/bdpar/contributors>.

## Licence ⚖

This project is under the License GPL-3.
