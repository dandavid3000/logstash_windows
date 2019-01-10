> This guide shows to install and do some simple examples with logstash on Windows

## Table of contents
* [Why?](#why)
* [Installation](#installation)
* [Examples](#examples)

## Why

* To save time before setting up your real configuration file on the deployed system. For example, you need to run logstash to parse files from db and push them to ElasticSearch.
One process takes hours to finish just to know if your configuration file is correct or not
* To reduce panic mode with we start with logstash. I was always in panic mode while working with logstash but when you have neccessary tool to test it one by one. No more worries

## Installation

* Java environment and environment variable should be installed.
* Get the latetest version from here : https://www.elastic.co/downloads/logstash
  * Extract the downloaded file directly to where you want to put logstash
  * You can use `Command Prompt`, `Windows PowerShell`, or `MINGW64` to run logstash.
  * Be patient because it takes time to run logstash
* To run logstash you need:
  * A configuration file `.conf`. For example, `a.conf`
  * Command to execute: `bin/logstash -f a.conf`. It's better to put an absolute address to call logstash instead of `bin/logstash`
  
## Examples

* The most basic example 
  * You can find from the internet is to get what you input as outputs. Here's the configuration from .conf file. You need to execute logstash and type something in the console to observe the result
    ```
    input {
      # Input from the console.
      stdin{}
    }

    filter {
        # Add filter here. We dont need filter at the moment
    }

    output {
        # Output to the console.
        stdout {
                codec => "rubydebug"
        }
    }
    ```
  * As you can see the `.conf` file needs at least 3 parts `input`, `filter`, and `output`

* An example of how to read json file and output them to the console.
