#!/bin/bash
#
# Copyright (C) 2020 yeoldegrove
# This file is part of yadpass <https://github.com/yeoldegrove/yadpass>.
#
# yadpass is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# yadpass is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with yadpass.  If not, see <http://www.gnu.org/licenses/>.
#
###
### This is a little hacky GTK frontend for pass built using yad (a zenity clone).
### It let's you query for passwords for your git cloned pass repository in ~/.password-store.
### The found entires can be clipboarded by wl-copy/xclip or shown in cleartext on the screen.
### It will work with X11 and Wayland and was initially designed for phosh on pinephone and Librem 5.
###

# define password store location
password_stores=(${HOME}/.password-store)
# set DISPLAY if not exported
test -z ${DISPLAY} && export DISPLAY=:0
# distinguish between wayland and X11
clip_prog="wl-copy -n"
pgrep Xorg >/dev/null && clip_prog="xclip -rmlastnl -l 1"

do_input() {
  input=""
  input=$(yad --auto-kill --no-markup --title "yadpass" --text "search password" --form --field="")
  input=${input//|/}
  if [ "${?}" -eq "1" ]; then
    exit 0
  fi
  do_search
}

do_search() {
  output=""
  output=$(for password_store in "${password_stores[@]}"; do find ${password_store} -iname '*.gpg' -iname "*${input}*" -print0 ; done | xargs -0)
  output=${output//.gpg/}
  output=${output//${HOME}\/.password-store\//}
  test -z ${output} && do_input
  do_search_output
}

do_search_output() {
  action=""
  selection=$(
  for match in ${output}; do
    echo ${match}
  done | yad --auto-kill --no-markup --title "yadpass" --text "Found the following passwort entries ..." --list --column "matches"
  ) action="${?}"
  if [ "${action}" -eq "1" ]; then
    exit 0
  fi
  selection=${selection//|/}
  do_selection_choose
}

do_selection_choose() {
  test -z ${selection} && do_search_output
  action=0
  action=$(yad --auto-kill --no-markup --title "yadpass" --text "you selected ${selection}.\nChoose an action ..." --form --field="Action::"CB "password to clipboard!login to clipboard!url to clipboard!show complete entry (cleartext)")
  if [ "${?}" -eq "1" ]; then
    exit 0
  fi
  action=${action//|/}
  entry=$(pass show ${selection})
  password=$(head -1 <<< "${entry}")
  login=$(grep '^login:' <<< "${entry}")
  login=${login//login: /}
  url=$(grep '^url:' <<< "${entry}")
  url=${url//url: /}

  case $action in
    0)
      do_search
      ;;
    1)
      exit 0
      ;;
    "password to clipboard")
      # ${clip_prog} -selection clipboard -rmlastnl <<< "${password}"
      ${clip_prog} <<< "${password}"
      ;;
    "login to clipboard")
      ${clip_prog} <<< "${login}"
      ;;
    "url to clipboard")
      ${clip_prog} <<< "${url}"
      ;;
    "show complete entry (cleartext)")
      clip=$(yad --auto-kill --no-markup --selectable-labels --title "yadpass" --text "select to copy to clipboard ..." --list --column "${selection}" <<< $entry)
      if [ "${?}" -eq "1" ]; then
        exit 0
      fi
      clip=${clip//|/}
      clip=${clip//login: /}
      clip=${clip//url: /}
      ${clip_prog} <<< "${clip}"
      ;;
  esac
  exit 0
}

do_input
