#!/usr/bin/env bash

if ! [ "$1" == "" ]; then
    hugo new content -k post content/posts/$1.md
    nvim content/posts/$1.md
fi
