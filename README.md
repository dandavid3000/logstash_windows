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

* An example of how to read json file and output them to the console:
	* Put a config file `test.conf`.
 
	 ```
	  input 
	  {
	      file 
	      {
		  path => ["C:/Users/Dan/Desktop/logstash-6.5.4/test2.json"]
	    type => "json"
		  start_position => "beginning"
	    codec => "json"
	      }
	  }

	  output
	  { 
	      stdout { codec => rubydebug }
	  }
	 ```
	  * Create `test2.json` file. Make sure that the input is a string. Enter to end a line and save it. For example:
	  ```
	  {name:"John", age: 30, city:"New York"}
	  ```
  
	  ```
	  {"doc":{"msid":"ff58138daa82769338c1e0b54c1c1570", "ratio_waterscout":0.38773, "alert_a":false, "capacity_waterscout_raw":1331.0000000001, "alert_temperature_soil":false, "alert_temperature_smec300_raw":false, "alert_moisture_datashaped":false, "_id":"d62824211a19b496c1172dca79103815", "alert_ec_smec300_raw":false, "alert_battery_carte":false, "a":0.92322, "capacity_waterscout_calibrated":1588.13427, "battery_carte":100.0000000001, "deviceID":"CDCB7C", "alert_b":false, "alert_rfu":false, "_rev":"1-856bc525e8c4cc42befb7acb91f41851", "alert_rfu_max":false, "ec_smec300_raw":1e-10, "rfu":1e-10, "moisture_datashaped":10.61411, "ec_soil":1e-10, "error_out_of_range":[], "firmwareVersion":"51.5", "alert_capacity_waterscout_calibrated":false, "null_fields":[], "alert_ratio_waterscout":false, "b":359.32845, "deviceType":"62_WATERSCOUT_TEMP_EC_SMEC300", "created_at":"2019-01-10T13:30:08.000Z", "alert_capacity_waterscout_raw":false, "temperature_soil":9.50141, "ranking_moisture_datashaped":1.0000000001, "alert_ranking_moisture_datashaped":false, "temperature_smec300_raw":2652.0000000001, "error_communication_error":[], "rfu_max":26.66667, "alert_ec_soil":false}}
	  ```

	 ```
	 {"doc":{"msid":"ff58138daa82769338c1e0b54c1c1570","ratio_waterscout":0.38773,"alert_a":false,"capacity_waterscout_raw":1331.0000000001,"alert_temperature_soil":false,"alert_temperature_smec300_raw":false,"alert_moisture_datashaped":false,"_id":"d62824211a19b496c1172dca79103815","alert_ec_smec300_raw":false,"alert_battery_carte":false,"a":0.92322,"capacity_waterscout_calibrated":1588.13427,"battery_carte":100.0000000001,"deviceID":"CDCB7C","alert_b":false,"alert_rfu":false,"_rev":"1-856bc525e8c4cc42befb7acb91f41851","alert_rfu_max":false,"ec_smec300_raw":1e-10,"rfu":1e-10,"moisture_datashaped":10.61411,"ec_soil":1e-10,"error_out_of_range":[],"alert_capacity_waterscout_calibrated":false,"null_fields":[],"alert_ratio_waterscout":false,"b":359.32845,"created_at":"2019-01-10T13:30:08.000Z","alert_capacity_waterscout_raw":false,"temperature_soil":9.50141,"ranking_moisture_datashaped":1.0000000001,"alert_ranking_moisture_datashaped":false,"temperature_smec300_raw":2652.0000000001,"error_communication_error":[],"rfu_max":26.66667,"alert_ec_soil":false}}
	 ```
* A little advanced example is to check some fields in the doc file and then do some modification. With the configuration, I am going to check if doc.error_communication_error[0] (which contains an array) exists. If yes/no, I add cm_flag and cm_count values. In addition, I also check if there are doc.firmwareVersion and doc.deviceType or not. If they don't exist, I add them with defaul values. The last part is to convert values into the data type that I prefer. ** Please notice that convert and add_field should be in 2 different mutate**
	* `.conf`
	
	```
	  input 
	{
	    file 
	    {
		path => ["C:/Users/Dan/Desktop/logstash-6.5.4/test2.json"]
			type => "json"
		start_position => "beginning"
			codec => "json"
	    }
	}
	filter {
		if [doc][error_communication_error][0][SensorName]{
			mutate {
				add_field => { "cm_flag" => true}
				add_field => { "cm_count" => 1}
			}
		} else {
			mutate {
				add_field => { "cm_flag" => false}
				add_field => { "cm_count" => 0}
			  }
		}


		if ![doc][firmwareVersion]{
			mutate {
				add_field => {"[doc][firmwareVersion]" => 0}
			}
		}

		if ![doc][deviceType]{
			mutate {
				add_field => {"[doc][deviceType]" => "unknown"}
			}
		}

		mutate {
			convert => { "cm_flag" => "boolean"}
			convert => { "cm_count" => "integer"}
			convert => { "[doc][firmwareVersion]" => "float"}
		}
	}
	output
	{ 
	    stdout { codec => rubydebug }
	}
	```
  
	* `.json`

	 ```
	{"doc":{"msid":"ff58138daa82769338c1e0b54c1c1570","ratio_waterscout":0.38773,"alert_a":false,"capacity_waterscout_raw":1331.0000000001,"alert_temperature_soil":false,"alert_temperature_smec300_raw":false,"alert_moisture_datashaped":false,"_id":"d62824211a19b496c1172dca79103815","alert_ec_smec300_raw":false,"alert_battery_carte":false,"a":0.92322,"capacity_waterscout_calibrated":1588.13427,"battery_carte":100.0000000001,"deviceID":"CDCB7C","alert_b":false,"alert_rfu":false,"_rev":"1-856bc525e8c4cc42befb7acb91f41851","alert_rfu_max":false,"ec_smec300_raw":1e-10,"rfu":1e-10,"moisture_datashaped":10.61411,"ec_soil":1e-10,"error_out_of_range":[],"alert_capacity_waterscout_calibrated":false,"null_fields":[],"alert_ratio_waterscout":false,"b":359.32845,"created_at":"2019-01-10T13:30:08.000Z","alert_capacity_waterscout_raw":false,"temperature_soil":9.50141,"ranking_moisture_datashaped":1.0000000001,"alert_ranking_moisture_datashaped":false,"temperature_smec300_raw":2652.0000000001,"error_communication_error":[],"rfu_max":26.66667,"alert_ec_soil":false}}
	 ```
	 
	 * Result
	 ![Result](/img1.PNG)
 


