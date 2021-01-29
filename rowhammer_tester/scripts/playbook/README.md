# Playbook

*playbook.py* provides a mechanism to configure a variety of rowhammer related workloads that can be executed against the platform.  Each individual workload is represented by a payload generator.  This class generates the payload to send to the platform and processes the results.

The configuration files are represented as python dictionaries.  See examples provided.

Here are the parameters currently supported by *playbook*:

*inversion_divisor* and *inversion_mask*

These represent the inputs to the inverters, to control which rows get the pattern and its inverse.

*payload_generator*

This is the name of the payload generator class to use

*payload_generator_config*

This is the configuration specific to the payload generator

*row_pattern*

This is the pattern to store to the non-inverted rows.


## Available Payload Generators

### RowListPayloadGenerator

A simple payload generator that can use a RowGenerator class to generate rows, and then generate a payload that hammers that list of rows.  It can also insert refreshes at the appropriate points.  Here are
the configs that can be used in *payload_generator_config* for this payload generator:

*row_generator*

This is the class used to generate the rows.  Currently, only EvenRowGenerator is available.

*row_generator_config*

The configuration for the row generator.

*read_count*

Number of hammers per row.

*refresh=True|False*

Should refresh be enabled?

*verbose=True|False*

Should verbose output be generated?

### HammerTolerancePayloadGenerator

Payload generator for measuring and characterizing hammer tolerance.  It can provide information about how many rows and bits are susceptible to row hammer.  It can also provide information about where the susceptible bits are located.

The data is generated by doing a series of double-sided hammers against the available group of victim rows.  The double-sided hammers increase in intensity based on *read_count_step*.  Here are the configs that can be specified in *payload_generator_config* for this payload generator:

*verbose=True|False*

Should the output be verbose?

*row_mapping*

How are the rows in the bank physically arranged?  Three options are available: *TrivialRowMapping*, *TypeARowMapping* and *TypeBRowMapping*.

*nr_rows*

Number of rows to conduct the experiment over.  This is the number of aggressor rows.  Victim rows will be 2 fewer than this number.  For example, to get stats for 32 victim rows, use 34 as the parameter value.

*read_count_step*

This is how much to increment the hammer count between multiple trials for the same row.  This is expressed in the number of hammers on each side (i.e. total number of hammers is 2x this value).

*iters_per_row*

Number of times the hammer count is incremented for each row.

The results are a series of histograms with the appropriate labels.

## Available Row Generators

### EvenRowGenerator

Generates a cluster of even numbered rows.  Uses the row mapping passed down from the PayloadGenerator.  Here are the configs that can be specified in *row_generator_config*:

*nr_rows*

Number of rows to generate

*max_row*

Maximum row number to use.  The rows numbers chosen are mod *max_row*.