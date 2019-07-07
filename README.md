This crate is a fork of 
[linked-hash-map](https://github.com/contain-rs/linked-hash-map) that builds on
top of [hashbrown](https://github.com/rust-lang/hashbrown) to implement more up
to date versions of `LinkedHashMap` `LinkedHashSet`, and `LruCache`.

The most important API change is that when a `LinkedHashMap` is used as a LRU
cache, it allows you to do things like this to avoid repeated key hashing and
lookups:

``` rust
let mut lru_cache = LinkedHashMap::new();
let key = "key";
// Try to find my expensive to construct and hash key
let cached_val = match lru_cache.raw_entry_mut(key) {
    RawEntryMut::Occupied(occupied) => {
        // Cache hit, move entry to the back.
        occupied.to_back();
        occupied.get_mut()
    }
    RawEntryMut::Vacant(vacant) => {
        // Insert expensive to construct key and expensive to compute value,
        // automatically inserted at the back.
        vacant.insert(key.clone(), 42)
    }
};
```

This crate contains a decent amount of unsafe code from handling its internal
linked list, and the unsafe code has diverged quite a lot from the original
`linked-hash-map` implementation.  As such, it should probably receive a lot
more review and testing before you place any kind of serious trust in it.

## Credit

There is a huge amount of code in this crate that is copied verbatim from
`linked-hash-map` and `hashbrown`, especially tests, associated types like
iterators, and things like `Debug` impls.

## License

This library is licensed the same as
[linked-hash-map](https://github.com/contain-rs/linked-hash-map), it is licensed
under either of:

* MIT license [LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT
* Apache License 2.0 [LICENSE-APACHE](LICENSE-APACHE) or https://opensource.org/licenses/Apache-2.0

at your option.
