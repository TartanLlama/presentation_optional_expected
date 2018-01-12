### Functional exceptionless error handling with optional and expected

#### Simon Brand, Senior Software Engineer, Codeplay Software Ltd.

#### 2018-01-12

---

## Agenda

- Exceptions
- `std::optional`
- `std::expected`
- Functional extensions
- Functional theory

---

# catpicz.io

---

## The £1,000,000 function

```cpp
image get_cute_cat (const image& img);
```

---

## Exceptions

```cpp
image get_cute_cat (const image& img) {
    return add_rainbow(
             make_smaller(
               make_eyes_sparkle(
                 add_bow_tie(
                   crop_to_cat(img))));
}
```

---

## `std::optional`

```cpp
std::optional<image> get_cute_cat (const image& img) {
    auto cropped = find_cat(img);
    if (!cropped) {
      return std::nullopt;
    }

    auto with_tie = add_bow_tie(*cropped);
    if (!with_tie) {
      return std::nullopt;
    }

    auto with_sparkles = make_eyes_sparkle(*with_tie);
    if (!with_sparkles) {
      return std::nullopt;
    }

    return add_rainbow(make_smaller(*with_sparkles));
 }
```

---

## `std::expected`

```cpp
std::expected<image, error_code> get_cute_cat (const image& img) {
    auto cropped = find_cat(img);
    if (!cropped) {
      return no_cat_found;
    }

    auto with_tie = add_bow_tie(*cropped);
    if (!with_tie) {
      return cannot_see_neck;
    }

    auto with_sparkles = make_eyes_sparkle(*with_tie);
    if (!with_sparkles) {
      return cat_has_eyes_shut;
    }

    return add_rainbow(make_smaller(*with_sparkles));
 }
```

---

## `map` and `and_then`

```cpp
tl::optional<image> get_cute_cat (const image& img) {
    return crop_to_cat(img)
           .and_then(add_bow_tie)
           .and_then(make_eyes_sparkle)
           .map(make_smaller)
           .map(add_rainbow);
}
```

---

## Original code

```cpp
image get_cute_cat (const image& img) {
    return add_rainbow(
             make_smaller(
               make_eyes_sparkle(
                 add_bow_tie(
                   crop_to_cat(img))));
}
```

---


# The theory

---

# Functor

- A context.
- An operation to unwrap stored values, manipulate them, and wrap the result back in the context.

---

## `map`

```cpp
template <class T>
struct optional {
    template <class U>
    optional<U> map (std::function<U(T)>);
};
```

---

## Example

```cpp
tl::optional<int> opt_i = maybe_get_int();
opt_i.map(add_12)
     .map([](int i) { return i * 42; })
     .map(some_function_object());
```

---


## Monad

- A context. |
- An operation to compose operations which return values wrapped in the context. |

---

## `and_then`

```cpp
template <class T>
struct optional {
    template <class U>
    optional<U> and_then (std::function<optional<U>(T)>);
};
```

---

## Example

```cpp
tl::optional<int> opt_i = maybe_get_int();
opt_i.and_then(try_sqrt)
     .and_then([](int i) { return tl::optional<int>(42); })
     .and_then(divide_exactly_by(42));
```

---

# The end!

---

## Resources

- https://github.com/TartanLlama/optional
- https://github.com/TartanLlama/expected
- https://wg21.tartanllama.xyz/monadic-optional/
- https://blog.tartanllama.xyz/optional-expected/
- http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html

---
