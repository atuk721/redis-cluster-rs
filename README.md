[![MIT licensed](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE) [![](http://meritbadge.herokuapp.com/redis_cluster_rs)](https://crates.io/crates/redis_cluster_rs)

This is a Rust implementation for Redis cluster library.

Documentation is available at [here](https://docs.rs/redis_cluster_rs/0.1.6/redis_cluster_rs/).

This library extends redis-rs library to be able to use cluster.
Client impletemts traits of ConnectionLike and Commands.
So you can use redis-rs's access methods.
If you want more information, read document of redis-rs.

Note that this library is currently not have features of Pubsub.

# Example

```rust
extern crate redis_cluster_rs;

use redis_cluster_rs::{Client, Commands};

fn main() {
    let nodes = vec!["redis://127.0.0.1:6379/", "redis://127.0.0.1:6378/", "redis://127.0.0.1:6377/"];
    let client = Client::open(nodes).unwrap();
    let connection = client.get_connection().unwrap();

    let _: () = connection.set("test", "test_data").unwrap();
    let res: String = connection.get("test").unwrap();

    assert_eq!(res, "test_data");
}
```

# Pipelining

```rust
extern crate redis_cluster_rs;

use redis_cluster_rs::{Client, PipelineCommands, pipe};

fn main() {
    let nodes = vec!["redis://127.0.0.1:6379/", "redis://127.0.0.1:6378/", "redis://127.0.0.1:6377/"];
    let client = Client::open(nodes).unwrap();
    let connection = client.get_connection().unwrap();

    let key = "test";

    let _: () = pipe()
        .rpush(key, "123").ignore()
        .ltrim(key, -10, -1).ignore()
        .expire(key, 60).ignore()
        .query(&connection).unwrap();
}
```
