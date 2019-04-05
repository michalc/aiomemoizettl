# aiomemoizettl [![CircleCI](https://circleci.com/gh/michalc/aiomemoizettl.svg?style=svg)](https://circleci.com/gh/michalc/aiomemoizettl) [![Test Coverage](https://api.codeclimate.com/v1/badges/8dcbbf352c803cb8147d/test_coverage)](https://codeclimate.com/github/michalc/aiomemoizettl/test_coverage)

Memoize asyncio Python calls with a per-result TTL


## Installation

```base
pip install aiomemoizettl
```


## Usage

For a coroutine whose arguments are hashable, you can create a _memoized_ version by passing it to `memoize_ttl`, along with a function that converts its return value to a TTL.

For example, the below

```python
import asyncio
from aiomemoizettl import memoize_ttl

async def main():
    memoized = memoize_ttl(coro, get_ttl=lambda result: result['ttl'])
    results = await asyncio.gather(*[
        memoized(1),
        memoized(2),
    ])
    await asyncio.sleep(1)

    results = await asyncio.gather(*[
        memoized(1),
        memoized(2),  # Will used the cached value of `coro(2)`
    ])

async def coro(value):
    print('Inside coro', value)
    return {'ttl': value, 'some-other': 'data'}

loop = asyncio.get_event_loop()
loop.run_until_complete(main())
loop.close()
```

will output

```
Inside coro 1
Inside coro 2
Inside coro 1
```
