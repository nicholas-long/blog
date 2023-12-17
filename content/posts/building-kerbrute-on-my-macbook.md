+++
title = 'Building Kerbrute for My Macbook VMs'
date = 2023-12-17T14:00:28-06:00
draft = false
+++

# Kerbrute
When pentesting active directory boxes, sometimes you have to brute force some domain accounts.
It is possible to do this with netexec ( formerly crackmapexec ), but Kerbrute is usually faster and produces less noise.
I liked using kerbrute for password spraying.

Unfortunately, kerbrute did not have a build available for arm64.
The only builds were for x86 and x64.
The last release from project was a few years ago, maybe before the era where ARM macbooks became so good at running VMs.
Fortunately though, it is a go program, which means it should be portable enough to build on any platform relatively easily.
I don't need it to run on my macbook natively as a Darwin program, but I do need it to run within a linux VM running on arm64.

# Investigating Makefiles
I made a makefile once in C class at university, and that was the extent of my makefile experience.
Changing the makefile for kerbrute looked simple enough, though.
There are only two architectures listed in the definitions:
```make
TARGET=./dist
ARCHS=amd64 386 
GOOS=windows linux darwin
PACKAGENAME="github.com/ropnop/kerbrute"
```

These are used to pass as environment variables to go when doing the build:
```make
linux: ## Make Linux x86 and x64 Binaries
	@for ARCH in ${ARCHS}; do \
		echo "Building for linux $${ARCH}..." ; \
		GOOS=linux GOARCH=$${ARCH} go build -a -ldflags ${LDFLAGS} -o ${TARGET}/kerbrute_linux_$${ARCH} || exit 1 ;\
	done; \
	echo "Done."
```

# Building
Adding the architecture to the list at the top of the file causes builds to be created.
```
ARCHS=amd64 386 arm64
```
Since I had already forked the repository, I quickly made a [pull request](https://github.com/ropnop/kerbrute/pull/71) to the project.
However, upon later inspection, it looks like some other people also had the same idea:
- https://github.com/ropnop/kerbrute/pull/63
- https://github.com/ropnop/kerbrute/pull/62/files

It looks like the project is not really maintained anymore, and a fork will have to be used from now on in order to build for macbooks.

```bash
git clone https://github.com/nicholas-long/kerbrute
cd kerbrute
make all
```
