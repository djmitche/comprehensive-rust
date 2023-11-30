---
minutes: 10
---
# Async Traits

Async methods in traits are supported as of Rust 1.75.
The crate [async_trait](https://docs.rs/async-trait/latest/async_trait/)
provides a workaround before that version.

```rust,editable,compile_fail
use async_trait::async_trait;
use std::time::Instant;
use tokio::time::{sleep, Duration};
use std::collections::HashMap;

#[async_trait]
trait DB {
    async fn get(&self, key: &str) -> Option<&String>;
}

#[derive(Default)]
struct InMemory {
    data: HashMap<String, String>,
}

#[async_trait]
impl DB for InMemory {
    async fn get(&self, key: &str) -> Option<&String> {
        sleep(Duration::from_millis(1)).await;
        self.data.get(key)
    }
}

#[tokio::main]
async fn main() {
    let db = InMemory::default();
    println!("foo => {:?}", db.get("foo").await);
}
```

<details>  

* `async_trait` is easy to use, but note that it's using heap allocations to
  achieve this. This heap allocation has performance overhead.

* The challenges in language support for `async trait` are deep Rust and
  probably not worth describing in-depth. Niko Matsakis did a good job of
  explaining them in [this
  post](https://smallcultfollowing.com/babysteps/blog/2019/10/26/async-fn-in-traits-are-hard/)
  if you are interested in digging deeper.

</details>
