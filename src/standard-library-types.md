# Standard Library Types

It is worth reading through the documentation for common standard library
types—such as [`Vec`], [`Option`], [`Result`], and [`Rc`]—to find interesting
functions that can sometimes be used to improve performance.

[`Vec`]: https://doc.rust-lang.org/std/vec/struct.Vec.html
[`Option`]: https://doc.rust-lang.org/std/option/enum.Option.html
[`Result`]: https://doc.rust-lang.org/std/result/enum.Result.html
[`Rc`]: https://doc.rust-lang.org/std/rc/struct.Rc.html

## `Vec`

[`Vec::remove`] removes an element at a particular index and shifts all
subsequent elements one to the left, which makes it O(n). [`Vec::swap_remove`]
replaces an element at a particular index with the final element, which does
not preserve ordering, but is O(1).

[`Vec::retain`] efficiently removes multiple items from a `Vec`. There is an
equivalent method for other collection types such as `String`, `HashSet`, and
`HashMap`.

[`Vec::remove`]: https://doc.rust-lang.org/std/vec/struct.Vec.html#method.remove
[`Vec::swap_remove`]: https://doc.rust-lang.org/std/vec/struct.Vec.html#method.swap_remove
[`Vec::retain`]: https://doc.rust-lang.org/std/vec/struct.Vec.html#method.retain

## `Option` and `Result`

[`Option::ok_or`] converts an `Option` into a `Result`, and is passed an `err`
parameter that is used if the `Option` value is `None`. `err` is computed
eagerly. If its computation is expensive, you should instead use
[`Option::ok_or_else`], which computes the error value lazily via a closure.
For example, this:
```rust
# fn expensive() {}
# let o: Option<u32> = None;
let r = o.ok_or(expensive()); // always evaluates `expensive()`
```
should be changed to this:
```rust
# fn expensive() {}
# let o: Option<u32> = None;
let r = o.ok_or_else(|| expensive()); // evaluates `expensive()` only when needed
```
[**Example**](https://github.com/rust-lang/rust/pull/50051/commits/5070dea2366104fb0b5c344ce7f2a5cf8af176b0).

[`Option::ok_or`]: https://doc.rust-lang.org/std/option/enum.Option.html#method.ok_or
[`Option::ok_or_else`]: https://doc.rust-lang.org/std/option/enum.Option.html#method.ok_or_else

There are similar alternatives for [`Option::map_or`], [`Option::unwrap_or`],
[`Result::or`], [`Result::map_or`], and [`Result::unwrap_or`].

[`Option::map_or`]: https://doc.rust-lang.org/std/option/enum.Option.html#method.map_or
[`Option::unwrap_or`]: https://doc.rust-lang.org/std/option/enum.Option.html#method.unwrap_or
[`Result::or`]: https://doc.rust-lang.org/std/result/enum.Result.html#method.or
[`Result::map_or`]: https://doc.rust-lang.org/std/result/enum.Result.html#method.map_or
[`Result::unwrap_or`]: https://doc.rust-lang.org/std/result/enum.Result.html#method.unwrap_or

## `Rc`

[`Rc::make_mut`] provides clone-on-write semantics for `Rc`. It makes a mutable
reference to an `Rc`. If the refcount is greater than one, it will `clone` the
inner value to ensure unique ownership; otherwise, it will modify the original
value. It is not needed often, but it can be extremely useful on occasion.
[**Example 1**](https://github.com/rust-lang/rust/pull/65198/commits/3832a634d3aa6a7c60448906e6656a22f7e35628),
[**Example 2**](https://github.com/rust-lang/rust/pull/65198/commits/75e0078a1703448a19e25eac85daaa5a4e6e68ac).

[`Rc::make_mut`]: https://doc.rust-lang.org/std/rc/struct.Rc.html#method.make_mut

