# Read the code: Git

> preface: I want to use English to write essays in the future, since it may help more people, and it can help me to practice my English writing. Great deal! :-)
But my first language is Chinese, so there maybe something that I cannot express quite well. Please forgive me. :-)

## Intro

There maybe a series about reading the code of many open source repository, since the code bases are actually what we made in practice, so I think it would be very helpful to grasp key ideas in those repos. The next question is why git? I think there are two reasons:

1. It is written in C, which is a language that I am familiar with. And C is a simple language, there are no fancy features like OOP, so it is easy to understand. I can just focus on the algorithm and the data structure in it.
2. Git is a very popular tool, and it is widely used in the industry. So it is very helpful to understand how it works.
3. My research topic is about version control, so it is very helpful to understand the code of git.

## Find the entry point

The first thing to read code of a tool, I think, is to find the entry point of the program. I don't know whether I missed it, I didn't find something like `main()` in git repo, so I just try my most familiar thing in git: `git commit`. 

Search by commit, and you will find `commit.h` in the root directory of the repo. If there is anything elsewhere call something like `commit()` function in `commit.c`, then that is probably the entry point of the program. Then I was going to found where is `commit.c` file. That's easy, if we lookup the reference of `struct commit` then we will find it in `builtin/commit.c`. Ah ha, that's where git call it's `commit` function: `cmd_commit()`. Next, as you trace back, you would finally find
the entry point of the program: `cmd_main()` in `git.c`. It's so obvious that I missed it. :-)

> Here I want to talk something bothered me a lot when I read the code. Clangd was continuously giving me error about type not defined in `commit.h` header file. I was so confused, since I didn't encounter any mistake when I make the project and generate the compile_commands.json file for clangd by simply run:

```bash
bear -- make -j16
```

> So why did clangd complain about it? I read through the Makefile to see whether there implicitly define the include path but clangd failed to parse it. Then I found "git-compat-util.h", it defined/declared something that clangd complained about, like `uint32_t`, `uint64_t`, `size_t`, etc. So I just include it in `commit.h` and the error disappeared. Then I suddenly realized that `.c` is the minimal compilation unit, while `.h` is not. When I go to `builtin/commit.c`, I found it exactly
> `#include "git-compt-util.h"`. I don't know why git didn't include it in `commit.h`, it is so weird. :-(

## Let's start from the entry point



