---
title: Getting up to speed with WGPU
layout: ../../layouts/BlogPostLayout.astro
author: kristf
date: 2025/02/01
description: In this post we will get to know the very basics regarding wgpu with Rust. We will mainly focus on the base setup and opening a window.
---

Well, well, well.

Wgpu with Rust it is.

Before we get started though, let te state it clearly that I'm by no means a Wgpu or even Rust master. At the time of writing this post I know just barely enough to open a window with Wgpu and do some stuff in it.

The reason I first wanted to learn Wgpu was to build a CAD app.

That's another story for another day, the point is that I found it really hard to find a proper tutorial online, or just basically anything that would walk me through the steps it takes to make something useful with Wgpu.

So... This post will aim to be your guide.

Most of the code here will be pretty similar to what's [here](https://sotrh.github.io/learn-wgpu/beginner/tutorial1-window/#boring-i-know). That's because that is the tutorial I used back in the day. I will aim to explain things a bit more though.

Let's get started by presenting the libraries we will be using:

```toml
# cargo.toml
[dependencies]
winit = { version = "0.29", features = ["rwh_05"] }
env_logger = "0.10"
log = "0.4"
wgpu = "22.0"
pollster = "0.3"
```

That's pretty much all we will need, so how about we get started with some code?

In your `src` directory create a file named `lib.rs`. Inside, we will start with a package named `winit` which will take care of creating the window for us and managing the event loop.

```rust
use winit::{
    event::*,
    event_loop::{ControlFlow, EventLoop},
    window::WindowBuilder,
};

pub fn run() {
    env_logger::init();
    let event_loop = EventLoop::new().unwrap();
    let window = WindowBuilder::new().build(&event_loop).unwrap();

    event_loop.set_control_flow(ControlFlow::Wait);

    event_loop
        .run(move |event, control_flow| match event {
            Event::WindowEvent { event, window_id } if window_id == window.id() => match event {
                WindowEvent::CloseRequested => {
                    println!("adios");
                    control_flow.exit();
                }
                _ => {}
            },
            _ => {}
        })
        .unwrap();
}
```

First, we set up `env_logger` to make Wgpu errors a bit more tame.

Then we create our `event_loop` and `window`.

I set the `control_flow` to `ControlFlow::Wait` which just basically means that the app can pause rendering making it a lot more CPU friendly(But, you should not do this if you are planning on doing a game).

The next interesting thing is out `move |...|` [closure](https://doc.rust-lang.org/rust-by-example/fn/closures.html) inside of `run`. It makes sure that `window` will only live as long as out event loop is running.

In `if window_id == window.id()` I just make sure that our `event` comes from the current window. And if someone closes the window we exit the `control_flow`.

Pretty straight forward.
