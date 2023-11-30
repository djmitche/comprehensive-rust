---
minutes: 5
---
# Futures

A future represents an operation that may not be complete yet. An executor polls
a future until it is finished. We can implement a simple executor ourselves:

```rust,editable,compile_fail
#![feature(noop_waker)]
use std::{future::Future, pin::pin, task};

async fn count_to(count: i32) {
    for i in 1..=count {
        println!("Count is: {i}!");
    }
}

fn main() {
    let mut fut = pin!(std::future::pending::<i32>());
    let waker = task::Waker::noop();
    let mut cx = task::Context::from_waker(&waker);
    while let task::Poll::Pending = fut.as_mut().poll(&mut cx) {
        println!("future is still pending");
    }
}
```

<details>

Don't dwell on the `pin!`, `Context`, or `Waker`. We will return to these, and
to the implementation details of Future, in the "Implementation" segment.
Students should observe that `main` is calling the `poll` method repeatedly
until it returns `Poll::Ready`. When run, this example shows that a `poll()`
typically makes as much progress as possible before returning. In this case, it
prints all of the counts.

Comparing `Future` to other async languages:

 * Python has a similar model in its `asyncio`. However, its `Future` type is
   callback-based, and not polled. Async Python programs require a "loop",
   similar to a runtime in Rust.

 * JavaScript's `Promise` is similar, but again callback-based. The language
   runtime implements the event loop, so many of the details of Promise
   resolution are hidden.

 * Rust Futures are "lazy" in that they do not do anything (not even start an
   I/O operation) unless there is an executor polling them. This differs from JS
   Promises, for example, which will run to completion even if they are never
   used.

</details>
