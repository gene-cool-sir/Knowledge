# git 运行脚本

```bash
#!/bin/bash

_projects_ignore=("." ".." ".idea" "logs" "code-maker" "datafactory")
rootPath=$(pwd)
enable_summary=1
enable_command_check=1

function log_error() {
  echo -e " $(date '+%Y-%m-%d %T')  \033[31m   [ERROR]   :   $* \033[0m"
}

function log_success() {
  echo -e " $(date '+%Y-%m-%d %T')  \033[32m   [SUCCESS] :   $* \033[0m"
}

function log_warning() {
  echo -e " $(date '+%Y-%m-%d %T')  \033[33m   [WARN]    :   $* \033[0m"
}

function log_normal() {
  echo -e " $(date '+%Y-%m-%d %T')               :   $* \033[0m"
}

function isInArray() {
  local module=$1
  shift
  local read_array=$@
  for ignore in ${read_array[@]}; do
    if [[ ${ignore} == ${module} ]]; then
      return 0
    fi
  done
  return 1
}

function moduleExecuteCmd() {
  local module=$1
  shift
  log_normal "start to execute command [$*] in module [${module}]"
  eval $*
  local returnValue=$(echo $?)
  if [[ ! ${returnValue} -eq 0 ]]; then
    log_error "module [${module}] execute command [$*] error"
    return 1
  fi
  return 0
}

error_module=()
external_ignore=()
only_execute_modules=()
declare -i count=0

function push_external_ignore_module() {
  local module_name=$1
  shift
  module_name=${module_name%/*}
  isInArray ${module_name} ${external_ignore}
  isIn=$(echo $?)
  if [[ ${isIn} -eq 1 ]]; then
    external_ignore[count]=${module_name}
    let count++
  fi
}

function push_only_execute_module() {
  local module_name=$1
  shift
  module_name=${module_name%/*}
  isInArray ${module_name} ${only_execute_modules}
  isIn=$(echo $?)
  if [[ ${isIn} -eq 1 ]]; then
    only_execute_modules[count]=${module_name}
    let count++
  fi
}

function executeSummary() {
  if [[ ${enable_summary} -eq 1 ]] && [[ ${#error_module[@]} -gt 0 ]]; then
    echo "--------------summary-----------------"
    for ((i = 0; i < ${#error_module[@]}; i++)); do
      log_error ${error_module[i]}
    done
  fi
}

function command_check() {
  $(command -v $1 >/dev/null 2>&1)
  canExecute=$(echo $?)
  if [[ ! ${canExecute} -eq 0 ]]; then
    log_error "[$1] is not a command,[$*] can not execute"
    exit 1
  fi
}

function doExecuteCmd() {
  local module_name=$1
  shift
  cd ${module_name}
  echo ""
  log_success "change directory [${module_name}]"
  moduleExecuteCmd ${module_name} $*
  returnValue=$(echo $?)
  if [[ ${enable_summary} -eq 1 ]] && [[ ! ${returnValue} -eq 0 ]]; then
    error_module+=("module [${file}] execute [$*] error")
  fi
  echo ""
  cd ${rootPath}
}

function only_execute(){
  if [[ ${#only_execute_modules[@]} -gt 0 ]];
    then
      for ((i = 0;i < ${#only_execute_modules[@]}; i++));do
        doExecuteCmd ${only_execute_modules[i]} $*
      done
    # only execute then exit
    exit 0
  fi
}

function executeCmd() {
  cd ${rootPath}
  if [[ ${enable_command_check} -eq 1 ]]; then
    command_check $*
  fi
  only_execute $*
  for file in $(ls); do
    isInArray ${file} ${_projects_ignore[@]}
    local isIgnore=$(echo $?)
    if [[ ${isIgnore} -eq 1 ]] && [[ -d ${file} ]]; then
      isInArray ${file} ${external_ignore[@]}
      local ext_ignore=$(echo $?)
      if [[ ${ext_ignore} -eq 1 ]]; then
        doExecuteCmd ${file} $*
      else
        log_success "module ${file} is ignored"
      fi
    fi
  done
  executeSummary
}

# parse command options
while getopts 'e:o:sc' OPT; do
  case ${OPT} in
  e)
    push_external_ignore_module ${OPTARG}
    ;;
  o)
    # only execute specific modules
    push_only_execute_module ${OPTARG}
    ;;
  s)
    # disable_summary
    enable_summary=0
    ;;
  c)
    # disable command check
    enable_command_check=0
    ;;
  esac
done

# skip command options
shift $((OPTIND - 1))
executeCmd $*

```
