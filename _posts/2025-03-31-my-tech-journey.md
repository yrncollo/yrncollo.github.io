---
title: "My Journey into Tech"
date: 2025-03-31 
image: /assets/img/Posts/tech_journey/tech_journey.png
categories: []
tags: []     # TAG names should always be lowercase
---
> "Tech is a never-ending rabbit hole, and the deeper you go, the more you realize you’ve barely scratched the surface."
{: .prompt-danger }

## Getting Started with Tech
I started my journey in tech with web development, learning HTML, CSS, and JavaScript. At first, I was excited by the ability to create websites and interactive pages, but something was missing. I wanted to go deeper, to understand how systems worked at a lower level. That’s when I discovered cybersecurity. I found myself fascinated by how security vulnerabilities were exploited, and I wanted to learn how to defend against them. Web development was fun, but I realized that I wanted to engage in something more technical and challenging, something that would push my limits.

> "Web development was fun, but hacking felt like a superpower."
{: .prompt-info }

For two years, I immersed myself in cybersecurity, competing in Capture The Flag `(CTF)` challenges and learning how to exploit memory safety vulnerabilities. Reverse engineering became a passion. I loved taking things apart and figuring out how they worked from the inside. I spent countless hours on Hack The Box, solving boot-to-root challenges and honing my skills in offensive security. The thrill of breaking into systems ethically and understanding security weaknesses fascinated me. It was like solving a never-ending puzzle, where each challenge taught me something new about how software, networks, and operating systems worked at a fundamental level.

## Deep Dive into Linux

When I was deep into cybersecurity, I was using `Kali Linux` since it was built for penetration testing. It had all the tools I needed, but over time, I wanted more control over my system. That led me to `Arch Linux`. I switched to Arch as my daily driver and never looked back. Arch gave me the ability to build a system exactly the way I wanted, and I enjoyed the process of setting everything up manually. Unlike other pre-configured distributions, Arch allowed me to understand every part of my operating system, from package management to kernel configurations.

> "Arch Linux doesn’t break. It just challenges you to fix it."
{: .prompt-tip }

As I progressed, I realized I needed to go even deeper into Linux. That’s when I took on the Linux From Scratch (LFS) project. I built my own Linux system from the ground up, compiling everything manually, configuring the system, and learning how everything fit together. It was intense but incredibly rewarding. I spent weeks ensuring that every component was correctly compiled and properly configured. I gained a deep appreciation for how operating systems work and how everything, from bootloaders to init systems, connects together.

However, I quickly realized that LFS wasn’t practical as a daily driver. Maintaining it meant spending all my time manually updating packages and applying patches since there was no package manager. Every minor update required careful manual intervention, and I simply didn’t have the time to keep it up. So, I decided to stick with Arch Linux, which gave me the flexibility and control I loved while still being maintainable. To this day, Arch remains my primary OS. 

I’ve been using Linux for five years, with three years dedicated to Arch. It strikes a perfect balance between control and convenience, and I’ve grown to appreciate the rolling release model that keeps my system up to date.

> "LFS taught me patience. Arch Linux taught me resilience."
{: .prompt-info }


During this journey, I also became a big fan of `VIM`. Mastering VIM improved my efficiency and productivity, making it an essential tool in my workflow. The moment I stopped using arrow keys, I knew I had truly leveled up. I use a blank keyboard, by the way.

## Discovering Rust

While working in cybersecurity, I kept encountering `memory safety vulnerabilities`. These security flaws were responsible for a huge number of exploits, and I wanted to find a better way to write secure software. That’s when I discovered Rust. Rust wasn’t just another programming language, it was designed specifically to prevent the types of vulnerabilities I had been exploiting in CTF challenges. This immediately caught my interest, and I knew I had to learn it.

Rust caught my attention because it offered memory safety without sacrificing performance. Unlike C or C++, it had built-in protections against common security issues like `buffer overflows` and `use-after-free` bugs. I knew this was the language I needed to master. Learning Rust was challenging at first. The ownership system, borrow checker, and lifetimes were unlike anything I had encountered before. But as I got deeper into the language, I realized how powerful it was. Rust forced me to write safe, efficient code while maintaining control over system resources.

> "Rust doesn’t have garbage collection. It has a borrow checker that makes sure you suffer in a structured way."
{: .prompt-tip }

I dove into Rust, learning its unique ownership system, lifetimes, and borrow checker. It wasn’t easy, but I loved the challenge. Rust forced me to think differently about programming more securely and more efficiently. I started building projects to solidify my understanding and explore the language’s capabilities. With every project, I gained a deeper appreciation for Rust’s design philosophy and the security guarantees it provides.

## Rust Projects & What I'm Working on Now

Since transitioning to Rust, I build several projects, including an e-commerce platform. Each project helps me deepen my understanding of Rust’s ecosystem and best practices. But I want to go even lower-level, closer to the system itself. Rust is more than just a safe systems language. It’s a powerful tool that lets me explore the depths of networking, cryptography, and operating systems.

Beyond production-level projects, I also engage in “`recreational programming`,” where I rewrite existing projects in Rust just for the sake of learning. This isn’t about building commercial applications, it’s about pushing my skills, testing Rust’s capabilities, and improving my understanding of low-level programming. Rewriting working software in Rust helps me internalize Rust’s best practices and gives me a way to deeply explore its nuances without the pressure of building something for production use.

> "Some people relax by watching movies. I relax by rewriting a working project in Rust for no reason."
{: .prompt-info }

Right now(As I am writing this blog), I’m working on low-level networking, specifically building a [`proof-of-concept DNS server`](https://github.com/yrncollo/dns_from_scratch). This project pushes my knowledge of network protocols, system programming, and Rust’s async capabilities. It’s exciting to work at this level, where every byte and performance optimization matters. Networking has always interested me, and writing a DNS server from scratch allows me to see how these protocols actually work under the hood. It’s one thing to use existing libraries, but building things from scratch forces me to truly understand the mechanics of internet infrastructure.

## Looking Ahead

My journey has taken me from web development to cybersecurity, a deep dive into Linux, and now Rust programming. Each step has shaped how I think about technology and problem-solving. Moving forward, I want to continue exploring low-level programming, networking, and security. Rust has opened up a world of possibilities for me, and I’m eager to see where this path leads next. The more I learn, the more I realize how much more there is to explore, and that’s what keeps me motivated.

Additionally, I have been writing blogs about my experiences and projects, though I haven’t always been consistent. Looking ahead, I want to change that and start regularly documenting my work, especially on open-source projects. Writing helps me solidify my understanding, share knowledge, and contribute back to the community. I want my blog to be a resource for others who are learning Rust, cybersecurity, and Linux, just as I once relied on the writings of others to help me grow.

> "If I write consistently, maybe one day my blog will be quoted in a Stack Overflow answer. Heheheee"
{: .prompt-danger }

This is just the beginning. My journey in tech is ongoing, and I look forward to pushing my limits further, solving harder problems, and sharing what I learn along the way.

Cheers ;)

