# cyderes-repo
This repository contains Cyderes skills challenge docs.
<br>
Prerequisites:
<br>
1. I have installed latest version of Github and Logstash on windows machine.
2. Created repository inside Github named "cyderes-repo"
3. Created one file inside logstash configuration folder named "my-config.conf"
4. Installed latest version of Java on my windows machine. Current version is using bundled JDK.
<br>
<br>
Logstash Configuration File
<br>
<br>
input {
  stdin {}
}

filter {
  # Parse syslog-style message
  grok {
    match => {
      "message" => "<%{INT:priority}>%{INT:version} %{TIMESTAMP_ISO8601:timestamp} %{HOSTNAME:host} %{DATA:application} %{NUMBER:pid} - - alertname=\"%{DATA:alertname}\" computername=\"%{DATA:computername}\" computerip=\"%{IP:computerip}\" severity=\"%{INT:severity}\""
    }
  }

  # Convert severity to integer (optional normalization)
  mutate {
    convert => { "severity" => "integer" }
  }

  # Rename fields for normalization
  mutate {
    rename => { "alertname"     => "event_type" }
    rename => { "computername"  => "hostname" }
    rename => { "computerip"    => "ip_address" }
  }

  # Add normalized field structure
  mutate {
    add_field => {
      "[normalized][event_type]" => "%{event_type}"
      "[normalized][hostname]"   => "%{hostname}"
      "[normalized][ip_address]" => "%{ip_address}"
      "[normalized][severity]"   => "%{severity}"
      "[normalized][source]"     => "%{application}"
      "[normalized][timestamp]"  => "%{timestamp}"
    }
  }

  # Clean up original fields if desired
  mutate {
    remove_field => [ "host", "message", "application", "pid", "version", "priority", "alertname", "computername", "computerip" ]
  }
}

output {
  stdout {
    codec => rubydebug
  }
}
<br>
<br>
Explanation of logstash configuration file:
<br>
<br>
This Logstash configuration defines a simple pipeline for ingesting, processing, and outputting log data. Here's a short description of the data flow:
<br>
<br>
Data Ingestion and Output in Logstash
<br>
<br>
In this solution, Logstash ingests log data from the standard input (stdin). Each incoming log message is parsed using a grok filter to extract structured fields from a syslog-style message format. The mutate filters are then used to convert the severity field to an integer, rename several fields for consistency, and organize them under a new nested structure called normalized. After the transformation, unnecessary original fields are removed to streamline the output. Finally, the processed data is output to the standard output (stdout) using the rubydebug codec, which formats the output in a readable, structured format for debugging or inspection.
<br>
<br>
Summary
<br>
This config:
1. Ingests raw logs via the terminal.
2. Parses syslog messages with a fixed format.
3. Normalizes field names and creates a clean, nested data structure.
4. Prints the result for inspection.
OUTPUT:
<br>
<br>





