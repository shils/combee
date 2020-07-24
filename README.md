combee
=====

## Installation

```sh
npm i -g combee
```

## Usage

```sh
combee --redis=redis://localhost:6379 --queues=foo,bar
combee::> combee.listQueues()
[ { name: 'foo' }, { name: 'bar' } ]

combee::> combee.foo.stats()
undefined
combee::> { waiting: 1,
  active: 0,
  succeeded: 0,
  failed: 0,
  delayed: 0,
  newestJob: 6 }

combee::> combee.foo.list()
undefined
combee::> []

combee::> combee.foo.list('waiting')
undefined
combee::> [ { id: '6',
    data: { yolo: 'solo' },
    options: { timestamp: 1561948255429, stacktraces: [] },
    status: 'created' } ]

combee::> combee.foo.createJob({yolo:'yolo'})
undefined
combee::> { id: '7',
  data: { yolo: 'yolo' },
  options: { timestamp: 1561948417944, stacktraces: [] },
  status: 'created' }

combee::> combee.foo.list('waiting')
undefined
combee::> [ { id: '7',
    data: { yolo: 'yolo' },
    options: { timestamp: 1561948417944, stacktraces: [] },
    status: 'created' },
  { id: '6',
    data: { yolo: 'solo' },
    options: { timestamp: 1561948255429, stacktraces: [] },
    status: 'created' } ]

combee::> combee.foo.removeJobs('waiting', { 'data.yolo': 'solo' })
undefined
combee::> removed 1 jobs

combee::> combee.foo.list('waiting')
undefined
combee::> [ { id: '7',
    data: { yolo: 'yolo' },
    options: { timestamp: 1561948417944, stacktraces: [] },
    status: 'created' } ]

combee::> combee.foo.find('waiting', {'data.yolo': 'yolo'})

combee::> combee.foo.count('waiting', {'data.yolo': 'yolo'})

combee::> combee.foo.distinct('waiting', 'data.yolo', {})
```

### Streaming operations

Not streaming in the Node.js `stream` sense, but a fluent `AsyncIterable/Iterator` based API. 

```
combee::> for (let i = 0; i < 10; i++) { combee.foo.createJob({ x: i, y: 2 * i }) }

// remove all waiting jobs where data.x + data.y < 10, log when done
combee::> combee.foo.iterate('waiting').filter((job) => job.data.x + job.data.y < 10).forEach((job) => job.remove()).then(() => console.log('done'))

// collect all waiting jobs where data.x is even in an array
combee::> let evenX; combee.foo.iterate('waiting').filter((job) => job.data.x % 2 === 0).toArray().then((res) => evenX = res)
```
