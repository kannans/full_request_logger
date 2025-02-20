# Full Request Logger

Easy access to full request logs via a web UI. The recorder attaches to the existing Rails.logger instance, 
and captures a copy of each log line into a per-thread buffer. When the request is over, the middleware makes
the recorder flush all the log lines that were recorded for that request as a compressed batch to an expiring Redis key.

Thus you no longer have to grep through log files or wrestle with logging pipelines to instantly see all the
log lines relevant to a request you just made. This is ideal for when you're testing a feature in the wild with
production-levels of data, which may reveal performance or other issues that you didn't catch in development.

## Installation

```ruby
# Gemfile
gem 'full_request_logger'
```

## Configuration

Add to development.rb and/or production.rb. Default time-to-live (TTL) for reach recorded request is 10 minutes,
and the default Redis storage is assumed to live on localhost, but both can be overwritten. Only configuration needed
is the enabled setting.

```ruby
config.full_request_logger.enabled = true
config.full_request_logger.ttl     = 1.hour
config.full_request_logger.redis   = { host: "127.0.0.1", port: 36379, timeout: 1 }
```

The request logs access can be protected behind http basic by adding the following credentials
(using `./bin/rails credentials:edit --environment production`):

```
full_request_logger:
  name: someone
  password: something
```

## Usage

Access request logs via `/rails/conductor/full_request_logger/request_logs/:id` where id is the X-Request-Id.

## License

Full Request Logger is released under the [MIT License](https://opensource.org/licenses/MIT).
