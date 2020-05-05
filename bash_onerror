#!/usr/bin/env bash

# if set +e is enabled, script will be exited after trapped
set +e

BASH_ONERROR_onerror_help() {
  echo "$(basename "$0") <bash file> <args>" 1>&2
  echo "$(basename "$0") [bash options...] -- <bash file> <args>" 1>&2
}
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
  script_path="$(cd $(dirname $0) && pwd)/$(basename $0)"
  default_argn="$#"
  end_offset=0
  bash_args=()
  if [[ ! -e $1 ]]; then
    for arg in "$@"; do
      if [[ $arg == "--" ]]; then
        bash_args=(${@:1:$end_offset})
        shift $(($end_offset + 1))
        break
      fi
      ((end_offset++))
    done
  fi
  if [[ $# -lt 1 ]] || [[ "$end_offset" == "$default_argn" ]]; then
    BASH_ONERROR_onerror_help
    exit 1
  fi

  target_file="$1"
  shift 1

  script="source $script_path"
  tmpdir=$(mktemp -d "/tmp/$(basename $0).$$.tmp.XXXXXX")
  tmp_script_filepath="$tmpdir/$(basename $target_file)"
  # NOTE: first line is maybe 'source xxxx #!/bin/bash' due to match source code line number
  {
    echo -n "$script "
    cat "$target_file"
  } >"$tmp_script_filepath"
  bash "${bash_args[@]}" "$tmp_script_filepath" "$@"
  exit $?
fi
if [[ -v BASH_ONERROR_BASE_VARIABLE_DATA_FILEPATH ]]; then
  return
fi
BASH_ONERROR_BASE_VARIABLE_DATA_FILEPATH="$(mktemp "/tmp/$(basename $0).$$.tmp.XXXXXX")"
(
  set -o posix
  set
) >"$BASH_ONERROR_BASE_VARIABLE_DATA_FILEPATH"

if set -o | grep -q 'xtrace.*on'; then
  BASH_ONERROR_XTRACE_OPT_FLAG="on"
fi
BASH_ONERROR_readline() {
  (
    shopt -s expand_aliases
    if type rlwrap >/dev/null 2>&1; then
      alias rlwrap='rlwrap'
    else
      alias rlwrap=''
    fi

    local ret
    ret=$(rlwrap bash -c 'read -p "> " -r line; printf '"'"'%s\n'"'"' "$line"')
    printf '%s' "$ret"
  )
}
BASH_ONERROR_onerror() {
  set +x
  local BASH_ONERROR_LINENO_="$1"
  local BASH_ONERROR_status="$2"
  shift 2

  local BASH_ONERROR_trap_SIGINT_bk
  BASH_ONERROR_trap_SIGINT_bk="$(trap -p SIGINT)"
  if type rlwrap >/dev/null 2>&1; then
    trap '' SIGINT
  else
    trap 'echo ""' SIGINT
  fi

  printf '\033[90m%*s\033[00m\n' "$(tput cols)" '' | tr ' ' "="
  echo 1>&2 -e "\\033[90m[TRAP ERR]\\033[00m"
  echo 1>&2 -e "\\033[31m[✘ $BASH_ONERROR_status] $BASH_COMMAND\\033[00m"
  echo 1>&2 -e "\\033[35m$(basename ${BASH_SOURCE[1]}):$BASH_ONERROR_LINENO_\\033[00m"
  cat -n "${BASH_SOURCE[1]}" | grep --color=always -C 5 '^ *'"$BASH_ONERROR_LINENO_"'\s.*$'

  echo 1>&2 -e "\\033[92m[new variables]\\033[00m"
  echo 1>&2 -ne "\\033[92m"
  diff -c "$BASH_ONERROR_BASE_VARIABLE_DATA_FILEPATH" <(
    set -o posix
    set
  ) | grep '^+' | grep -E -v '^\+ (COLUMNS|LINENO|LINES|FUNCNAME|BASH_ONERROR_)'
  echo 1>&2 -ne "\\033[00m"

  echo 1>&2 -e "\\033[93m[prompt command]: <bash command>, q|exit|quit, c|continue\\033[00m"
  echo 1>&2 -e "\\033[94m[wd] $(pwd)\\033[00m"
  printf '\033[90m%*s\033[00m\n' "$(tput cols)" '' | tr ' ' "="

  local line
  while :; do
    line=$(BASH_ONERROR_readline)
    if [[ "$line" =~ ^(q|exit|quit)$ ]]; then
      exit
    fi
    if [[ "$line" =~ ^(c|continue)$ ]]; then
      break
    fi
    if [[ -n "$line" ]]; then
      eval "$line" || true
    fi
  done

  [[ -n "$BASH_ONERROR_trap_SIGINT_bk" ]] && eval "$BASH_ONERROR_trap_SIGINT_bk"
  if [[ "$BASH_ONERROR_XTRACE_OPT_FLAG" == "on" ]]; then
    set -x
  fi
  return 0
}
trap 'BASH_ONERROR_onerror "$LINENO" "$?"' ERR
