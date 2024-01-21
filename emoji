#!/usr/bin/env bash

url="https://www.unicode.org/Public/emoji/latest/emoji-test.txt"
db="${HOME}/.emoji/emoji.txt"

export LC_ALL=en_US.UTF-8

function init() {
    [ ! -f "${db}" ] \
        && mkdir -p "$(dirname "${db}")" \
        && update
}

function http_get() {
    curl -sS "${url}"
}

function http_head() {
    curl -sS --HEAD "${url}" \
        | grep -i "Last-Modified" \
        | cut -f 2- -d " "
}

function outdated() {
    echo -n "local:  " && cat "${db}.head"
    echo -n "remote: " && http_head
}

function update() {
    http_get \
        | grep "^.....  " | grep ";" | grep "fully-qualified" \
        | sed "s,# ,#," | cut -f 2- -d "#" | cut -c 1 > "${db}" \
            && http_head > "${db}.head" \
            && cat "${db}" | wc -l | tr -d " " > "${db}.lines"
}

function shuffle() {
    lines="$(cat "${db}.lines")"
    line="$((${RANDOM} % ${lines} + 1))"
    cat "${db}" | head -n "${line}" | tail -n 1
}

function copy() {
    case "${OSTYPE}" in
        darwin*)
            pbcopy
            ;;
        linux*)
            xclip -selection clipboard
            ;;
        cygwin*)
            cat > /dev/clipboard
            ;;
        *)
            cat
            return 1
    esac
    return 0
}

function clipboard() {
    read emoji
    echo -n "${emoji}" | copy \
        && echo "${emoji} copied to clipboard." >&2 \
        || echo -e "\nERROR: Clipboard not supported on ${OSTYPE}, use 'emoji shuffle' instead." >&2
}

init

case "${1}" in
    outdated|update|shuffle)
        "${1}"
        ;;
    *)
        shuffle | clipboard
esac
