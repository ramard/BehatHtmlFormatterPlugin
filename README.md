## BehatHtmlFormatterPlugin

Behat 3 extension for generating HTML reports from your test results (custom version of the original from emuse: https://github.com/dutchiexl/BehatHtmlFormatterPlugin).
The changes I have made only relate to the Twig report (see below).

[![Latest Stable Version](https://poser.pugx.org/roydude/behat-html-formatter/v/stable)](https://packagist.org/packages/roydude/behat-html-formatter) [![Total Downloads](https://poser.pugx.org/roydude/behat-html-formatter/downloads)](https://packagist.org/packages/roydude/behat-html-formatter) [![Latest Unstable Version](https://poser.pugx.org/roydude/behat-html-formatter/v/unstable)](https://packagist.org/packages/roydude/behat-html-formatter) [![License](https://poser.pugx.org/roydude/behat-html-formatter/license)](https://packagist.org/packages/roydude/behat-html-formatter)

### Twig report

![Twig Screenshot](http://i.imgur.com/ytnIvPV.png)
![Twig Screenshot](http://i.imgur.com/PSdfL3Y.png)


## How?

* The tool can be installed easily with composer.
* Defining the formatter in the `behat.yml` file
* Modifying the settings in the `behat.yml`file

## Installation

### Prerequisites

This extension requires:

* PHP 5.3.x or higher
* Behat 3.x or higher

### Through composer

The easiest way to keep your suite updated is to use [Composer](http://getcomposer.org>):

#### Install with composer:

```bash
$ composer require --dev roydude/behat-html-formatter
```

#### Install using `composer.json`

Add BehatHtmlFormatterPlugin to the list of dependencies inside your `composer.json`.

```json
{
    "require": {
        "behat/behat": "3.*@stable",
        "roydude/behat-html-formatter": "0.1.*",
    },
    "minimum-stability": "dev",
    "config": {
        "bin-dir": "bin/"
    }
}
```

Then simply install it with composer:

```bash
$ composer install --dev --prefer-dist
```

You can read more about Composer on its [official webpage](http://getcomposer.org).

## Basic usage

Activate the extension by specifying its class in your `behat.yml`:

```json
# behat.yml
default:
  suites:
    ... # All your awesome suites come here

  formatters:
    html:
      output_path: %paths.base%/build/html/behat

  extensions:
    roydude\BehatHTMLFormatter\BehatHTMLFormatterExtension:
      name: html
      renderer: Twig
      file_name: index
      print_args: true
      print_outp: true
      loop_break: true
```

## Configuration

* `output_path` - The location where Behat will save the HTML reports. The path defined here is relative to `%paths.base%` and, when omitted, will be default set to the same path.
* `renderer` - The engine that Behat will use for rendering, thus the types of report format Behat should output (multiple report formats are allowed, separate them by commas). Allowed values are:
 * *Twig* A new and more modern format based on Twig.
* `file_name` - (Optional) Behat will use a fixed filename and overwrite the same file after each build. By default, Behat will create a new HTML file using a random name (*"renderer name"*_*"date hour"*).
* `print_args` - (Optional) If set to `true`, Behat will add all arguments for each step to the report. (E.g. Tables).
* `print_outp` - (Optional) If set to `true`, Behat will add the output of each step to the report. (E.g. Exceptions).
* `loop_break` - (Optional) If set to `true`, Behat will add a separating break line after each execution when printing Scenario Outlines.

## Blocked tests
To mark a test as skipped make sure it exits with PendingExcpetion, i.e.:

```php
throw new PendingException ("This scenario is blocked by bug MYPROJ-101");
```

## Screenshot

The facility exists to embed a screenshot into test failures.

Currently png is the only supported image format.

In order to embed a screenshot, you will need to take a screenshot using your favourite webdriver and store it in the following filepath format:

results/html/assets/screenshots/{{feature_name}}/{{scenario_name}}.png

The feature_name and scenario_name variables will need to be the relevant item names without spaces.

Below is an example of FeatureContext methods which will produce an image file in the above format:

```php

        /**
         * @BeforeScenario
         *
         * @param BeforeScenarioScope $scope
         *
         */
        public function setUpTestEnvironment($scope)
        {
            $this->currentScenario = $scope->getScenario();
        }

        /**
         * @AfterStep
         *
         * @param AfterStepScope $scope
         */
        public function afterStep($scope)
        {
            //if test has failed, and is not an api test, get screenshot
            if(!$scope->getTestResult()->isPassed())
            {
                //create filename string
                $featureFolder = str_replace(' ', '', $scope->getFeature()->getTitle());

                $scenarioName = $this->currentScenario->getTitle();
                $fileName = str_replace(' ', '', $scenarioName) . '.png';

                //create screenshots directory if it doesn't exist
                if (!file_exists('results/html/assets/screenshots/' . $featureFolder)) {
                    mkdir('results/html/assets/screenshots/' . $featureFolder);
                }

                //take screenshot and save as the previously defined filename
                $this->driver->takeScreenshot('results/html/assets/screenshots/' . $featureFolder . '/' . $fileName);
            }
        }

```

Note that the currentScenario variable will need to be at class level and generated in the @BeforeScenario method as Behat does not currently support obtaining the current Scenario in the @AfterStep method, where the screenshot is generated

## Issue Submission

When you need additional support or you discover something *strange*, feel free to [Create a new issue](https://github.com/roydude/BehatHtmlFormatterPlugin/issues/new).

## License and Authors

Authors: https://github.com/roydude/BehatHtmlFormatterPlugin/contributors

## To do
* Consider adding buttons for Skipped and Undefined.
* Simplify screenshots.
