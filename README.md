# Poll

Generalized implementation of polling, with support for timeout and polling interval.

## Example

``` ruby
result = Poll.(interval_milliseconds: 100, timeout_milliseconds: 500) do
  some_query()
end
```

## Overview

- The block is executed in a loop until it returns a value
- Polling will continue indefinitely unless a timeout is specified
- The polling interval allows throttling of requests

## Usage

### Polling Terminates When the Block Returns a Value

``` ruby
result = Poll.() do
  :something # Polling terminates immediately
end
```

### No Polling Interval

When no polling interval is specified, block re-executes immediately at the conclusion of the previous cycle.

``` ruby
result = Poll.(timeout_milliseconds: 500) do
  nil
end
```

This can have adverse effects if the resource being polled cannot withstand being flooded with such a quick succession of requests.

### With a Polling Interval

When a polling interval is specified, block re-executes once per polling interval rather than executing immediately at the conclusion of the previous cycle.

This can relieve pressure from a resource that might otherwise be flooded with such a quick succession of requests if there were no polling interval specified. The polling interval can provide a pause between executions of the block.

``` ruby
result = Poll.(interval_milliseconds: 100, timeout_milliseconds: 500) do
  nil # Polls for 500 milliseconds, with a 100 millisecond pause between cycles
end
```

If the cycle execution time is greater than the polling interval time, the block is re-executed immediately at the conclusion of the previous cycle.

``` ruby
result = Poll.(interval_milliseconds: 100, timeout_milliseconds: 500) do
  sleep 101/1000.0 # Cycle is longer than the polling interval. No pause between executions
  nil
end
```

### No Timeout

Without specifying a timeout, polling will continue indefinitely while the block returns no result.

``` ruby
result = Poll.() do
  nil # Infinite loop with a no pause between execution cycles
end
```

``` ruby
result = Poll.(interval_milliseconds: 100) do
  nil # Infinite loop with a 100 millisecond pause between execution cycles
end
```

### With a Timeout

When a timeout is specified, polling will continue only as long as the duration of the timeout.

``` ruby
result = Poll.(timeout_milliseconds: 500) do
  nil # Loops for 500 milliseconds
end

## License

The `poll` library is released under the [MIT License](https://github.com/eventide-project/poll/blob/master/MIT-License.txt).
