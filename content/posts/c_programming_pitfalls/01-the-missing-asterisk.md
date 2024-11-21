+++
title = """The missing asterisk"""
description = """"""

date = 2024-11-21
draft = false

[taxonomies]
tags = ["programming", "C", "rust", "C pitfalls"]

+++

Recently I stumbled over a refactoring error: I forgot adding an asterisk to change the pointed-to value. It took me about half an hour of debugging to find it, and when I found it I just thought "Well. This was just unnecessary". So I decided to mention this 'pitfall' in a small blog post. The code referenced in this post can be found in this [repository](https://github.com/ckrenslehner/c_programming_pitfalls/tree/main/the_missing_asterisk).

# A minimal C example

As an example, I want to consider a function, which tries to retrieve the next message from a fictional message storage. Inside the function, some logic determines if there is a message available or not. The message retrieval can fail, so the return value should signal the overall success.
If there is a message available, the function sets the value of `message` accordingly. Otherwise, it sets the value of `message` to NULL.

{{ add_src_to_code_block(src="main.c") }}
``` C
{{ remote_text(src="https://raw.githubusercontent.com/ckrenslehner/c_programming_pitfalls/refs/heads/main/the_missing_asterisk/main_infinite.c") }}
```

So far so good, right? Well not exactly. As commented in `get_next_message` the asterisk is missing. Due to the missing `*` the value of the pointer itself is changed on the function stack. So instead of terminating, we now have an infinite loop in `main`.
This error is obvious if you see it, but it is easy to overlook. Furthermore, the compiler does not even throw a warning. Just recently, I had to debug such an error, which can be cumbersome if the error is in a deeply nested code block.

# Comparison to Rust

The function `get_next_message` in the C code does the following:
- It checks if there is an existing message.
- It return this optional message via an output parameter.
- It does not use the return function, because retrieving the message can fail. So the return value signals, if the message retrieval is successful.
  
The Rust standard library provides special enums for representing optional values and success or failure:
- `Option`
- `Result`

I really want to recommend these YouTube videos to get familiar with `Option` and `Result`:
- [Rust's Most Important Containers 📦 10 Useful Patterns](https://www.youtube.com/watch?v=f82wn-1DPas)
- [A Simpler Way to See Results](https://www.youtube.com/watch?v=s5S2Ed5T-dc)

Rewriting the code from [the c example](#a-minimal-c-example) in rust looks roughly like this:

{{ add_src_to_code_block(src="main.rs") }}
``` rust
{{ remote_text(src="https://raw.githubusercontent.com/ckrenslehner/c_programming_pitfalls/refs/heads/main/the_missing_asterisk/src/main.rs") }}
```
I want to mention some aspects I really like about this sample code:
- Using `Option` inside `Result` clearly signals, that the function might fail. In case of success it is also clearly visible, that the returned value is optional.
- `Option` and `Result` wrap the values. Rust forces the user to unwrap these to get the wrapped value again. Therefore, checking the result is mandatory to access the inner return value.