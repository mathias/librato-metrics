Librato Metrics
=======

A convenient Ruby wrapper for the Librato Metrics API.

## Installation

On your shell:

    gem install librato-metrics

Then, in your application or script:

    require 'librato/metrics'

## Quick Start

If you are looking for the quickest possible route to getting a data into Metrics, you only need two lines:

    Librato::Metrics.authenticate 'email', 'api_key'
    Librato::Metrics.save :my_metric => 42

Unspecified metrics will send a *gauge*, but if you need to send a different metric type or include additional properties, simply use a hash:

    Librato::Metrics.save :my_metric => {:type => :counter, :value => 1002, :source => 'myapp'}

While this is all you need to get started, this probably isn't the most performant option for you, so read on...

## Authentication

Make sure you have [an account for Metrics](https://metrics.librato.com/) and then authenticate with your email and API key (on your account page):

    Librato::Metrics.authenticate 'email', 'api_key'

## Sending Metrics

If you are sending very many metrics or sending them very often, it will be much higher performance to bundle them up and send them in batches. Use `Queue` for this.

Queue up a simple gauge metric named `temperature`:

    queue = Librato::Metrics::Queue.new
    queue.add :temperature => 32.2

Queue up a gauge (`load`) and a counter (`visits`):

    queue.add :load => 2.2, :visits => {:type => :counter, :value => 400}

Queue up a metric with a specified source:

    queue.add :cpu => {:source => 'app1', :value => 92.6}

A complete [list of metric attributes](http://dev.librato.com/v1/metrics) is available in the [API documentation](http://dev.librato.com/v1).

Send all queued metrics:

    queue.save

## Benchmarking

If you have operations in your application you want to record execution time for, you can use the `#time` method:

    queue.time :my_measurement do
      # do work...
    end

If you need extra attributes for the measurement, simply add them on:

    queue.time :my_measurement, :source => 'app1' do
      # do work...
    end

## Querying Metrics

Get name and properties for all metrics you have in the system:

    metrics = Librato::Metrics.list

Get only metrics whose name includes 'time':

    metrics = Librato::Metrics.list :name => 'time'

## Querying Metric Data

Get attributes for metric 'temperature':

    data = Librato::Metrics.fetch :temperature

Get the 20 most recent data points for 'temperature':

    data = Librato::Metrics.fetch :temperature, :count => 20

Get the 20 most recent data points for 'temperature' from a specific source:

    data = Librato::Metrics.fetch :temperature, :count => 20, :source => 'app1'

Get the 20 most recent 15 minute data point rollups for 'temperature':

    data = Librato::Metrics.fetch :temperature, :count => 20, :resolution => 900

There are many more options supported for querying, take a look at the [REST API docs](http://dev.librato.com/v1/get/gauges/:name) for more details.

## Contribution

* Check out the latest master to make sure the feature hasn't been implemented or the bug hasn't been fixed yet.
* Check out the issue tracker to make sure someone already hasn't requested it and/or contributed it.
* Fork the project and submit a pull request from a feature or bugfix branch.
* Please include specs. This is important so we don't break your changes unintentionally in a future version.
* Please don't modify the Rakefile, version, or history. If you do change these files, please isolate a separate commit so we can cherry-pick around it.

## Copyright

Copyright (c) 2011-2012 [Librato Inc.](http://librato.com) See LICENSE for details.