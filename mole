a one eyed end, [06.09.2023 12:57]
#/usr/local/bin/bash

# 1. Projekt IOS
# login: xzhdan00
# MOLE wrapper

POSIXLY_CORRECT=yes
mostUsedFlag=0
listFlag=0
secretlogFlag=0
dateA="1970-01-01_00-00-00"
dateB=$(date "+%Y-%m-%d_%H-%M-%S")
dateAFlag=0
dateBFlag=0
helpFlag=0
groupFlag=0
groupName=""
fileSetFlag=0

set -e

function printError  ### function to output an error to stderr and terminate the script
{
  case $1 in
    1)
      echo "Error: Incorrect date format" >&2
      exit 1;;
    2)
      echo "Error: Wrong date" >&2
      exit 1;;
    3)
      echo "Error: File does not exist" >&2
      exit 1;;
    4)
      echo "Error: Config not set" >&2
      exit 1;;
    5) 
      echo "Error: Argument is missing" >&2
      exit 1;;
    6)
      echo "Error: Nothing found" >&2
      exit 1;;
    7)
      echo "Error: There are no files opened before with MOLE" >&2
      exit 1;;
    8)
      echo "Error: MOLE_RC directory does not exist" >&2
      exit 1;;
    9)
      echo "Error: There are no files opened before with MOLE in this directory" >&2
      exit 1;;
  esac
}

function showInfo ### output of the manual for using the script
{
  echo "   _________________________________________________________________________________________________________________"
  echo "###                                                                                                                 ###"
  echo "###                                         Mole (Makes One’s Life Easier)                                          ###"
  echo "###           -wrapper pro efektivní použití textového editoru s možností automatického výběru nejčastěji           ###"
  echo "###                                       či posledně modifikovaného souboru.                                       ###"
  echo "###_________________________________________________________________________________________________________________###"
  echo "###                                                                                                                 ###"
  echo "###                                               Nápověda k použití:                                               ###"
  echo "###                                                                                                                 ###"
  echo "###                 mole -h – Vypíše nápovědu k použití skriptu.                                                    ###"
  echo "###                                                                                                                 ###"
  echo "###                 mole [-g GROUP] FILE – Zadaný soubor bude otevřen.                                              ###"
  echo "###           Pokud byl zadán přepínač -g, dané otevření souboru bude zároveň přiřazeno do                          ###"  
  echo "###           skupiny s názvem GROUP. GROUP může být název jak existující, tak nové skupiny.                        ###"
  echo "###                                                                                                                 ###"
  echo "###                 mole list [FILTERS] [DIRECTORY] – Skript zobrazí seznam souborů,                                ###"
  echo "###           které byly v daném adresáři otevřeny (editovány) pomocí skriptu.                                      ###"
  echo "###                                                                                                                 ###"
  echo "###                 mole [-m] [FILTERS] [DIRECTORY] – Pokud DIRECTORY odpovídá existujícímu adresáři,               ###"
  echo "###           skript z daného adresáře vybere soubor, který má být otevřen. Pokud byl zadán argument -m,            ###"
  echo "###           tak skript vybere soubor, který byl pomocí skriptu otevřen (editován) nejčastěji.                     ###"
  echo "###_________________________________________________________________________________________________________________###"
  echo "###                                                                                                                 ###"

a one eyed end, [06.09.2023 12:57]
echo "###               *pokud tento skript používáte poprvé, nastavte editor v proměnné EDITOR a také                    ###" 
  echo "###                              nastavte název souboru pro LOG v proměnné MOLE_RC                                  ###"
  echo "###_________________________________________________________________________________________________________________###"
  exit 1
}

function checkDate ### function to check date for existence and format
{
  if [[ $1 =~ ^[0-9]{4}-[0-9]{2}-[0-9]{2}$ ]]; then
    day=$(echo "$1" | cut -d'-' -f3)
    month=$(echo "$1" | cut -d'-' -f2)
    year=$(echo "$1" | cut -d'-' -f1) 
    if [[ $month == 01  $month == 03  $month == 05  $month == 07  $month == 08  $month == 10  $month == 12 ]]; then
      maxDay=31
    elif [[ $month != 02 ]]; then
      maxDay=30
    elif [[ $((year % 4)) == 0 ]]; then
      maxDay=29
    else
      maxDay=28
    fi
    if [[ $year -gt 2023  $month -gt 12  $day -gt $maxDay ]]; then
      printError "2"
    fi
  else 
    printError "1"
  fi
}

function edit_file ### function for opening a file with an editor and further logging
{
  if [[ -e "$DIR/$fileName" ]]; then
    $EDITOR "$fileName"
    if [[ $groupFlag == 1 ]]; then 
      echo "$groupName|$DIR|$fileName|$(date +%Y-%m-%d_%H-%M-%S)" >> "$MOLE_RC"
      exit 0
    else  
      echo "|$DIR|$fileName|$(date +%Y-%m-%d_%H-%M-%S)" >> "$MOLE_RC"
      exit 0
    fi
  else
    printError "3"
  fi
}

function makeList ### function outputs list with defined filters
{
  unique_files=($(cat "$MOLE_RC" | awk -F'|' -v date1="$dateA" -v date2="$dateB" -v group="$groupName" -v dir="$DIR" 'index($1, group) && $4 >= date1 && $4 <= date2 && $2 == dir { print $3 }' | sort -u))
  if [[ -z "${unique_files[0]}" ]]; then
    printError "6"
  fi
  for file in "${unique_files[@]}"; do
    groups=($(cat "$MOLE_RC" | awk -F'|' -v date1="$dateA" -v date2="$dateB" -v group="$groupName" -v dir="$DIR" 'index($1, group) && $4 >= date1 && $4 <= date2 && $2 == dir { print $0 }' | grep "|$DIR|$file|" | awk -F'|' '{print $1}' | sort -u))
    printf "%s: %s\n" "$file" "${groups[*]}"
  done
}

function makeSecretLog ### function for log filtering and its further packing into a .bz2 file
{
  unique_files=($(cat "$MOLE_RC" | awk -F'|' -v date1="$dateA" -v date2="$dateB" -v group="$groupName" -v dir="$DIR" 'index($1, group) && $4 >= date1 && $4 <= date2 && $2 == dir { print $3 }' | sort -u))
  if [[ -z "${unique_files[0]}" ]]; then
    printError "6"
  fi
  for file in "${unique_files[@]}"; do
    dates=($(cat "$MOLE_RC" | awk -F'|' -v date1="$dateA" -v date2="$dateB" -v group="$groupName" -v dir="$DIR" 'index($1, group) && $4 >= date1 && $4 <= date2 && $2 == dir && $3 == "'"${file}"'" { print $4 }' | sort -u | tr '\n' '; '))
    DATETIME=$(date "+%Y-%m-%d_%H-%M-%S")
    if [[ !( -d ~/.mole ) ]]; then
      mkdir ~/.mole
    fi
    printf "%s/%s; %s\n" "$DIR" "$file" "$dates" | bzip2 -c >> ~/.mole/log_"$USER"_"$DATETIME".bz2
  done
}

function flagController ### a function that manipulates the flags and performs the necessary actions based on them
{
  if [[ $helpFlag == 1 ]]; then
    showInfo
  fi
  configCheck
  if [[ $dateAFlag == 1 && $dateBFlag == 1 ]]; then 
    if [[ $(date -d "$dateA" +%s) -gt $(date -d "$dateB" +%s) ]]; then
      printError "2"
    fi
  fi
  if [[ $listFlag == 1 ]]; then
    makeList
    exit 0
  fi
  if [[ $secretlogFlag == 1 ]]; then ###
    makeSecretLog
    exit 0
    fi
}

function configCheck ### function to check if config is set or not
{
  if [[ $MOLE_RC == "" ]]; then
    printError "4"
  fi
  if [[ !( -d "$(cd "$(dirname "$MOLE_RC")" && pwd)" ) ]]; then
    printError "8"
  fi
  if [[ $EDITOR == "" ]]; then
    if [[ $VISUAL == "" ]]; then
    EDITOR="vi"
    else
      EDITOR=$VISUAL
    fi
  fi
}

a one eyed end, [06.09.2023 12:57]
function findMostUsed ### function to find the file in a particular directory that has been used the most, with filters
{
  fileName=$(cat "$MOLE_RC" | awk -F'|' -v date1="$dateA" -v date2="$dateB" -v group="$groupName" -v dir="$DIR" '
  index($1, group) && $4 >= date1 && $4 <= date2 && $2 == dir { print $3 }' |
  sort | uniq -c | sort -nr | head -n 1 | awk '{print $2}')
  if [[ -z $fileName ]]; then
    printError "9"
  fi
  edit_file
}

function findLastUsed ### a function to find the file in a specific directory that was used last, with filters
{
  fileName=$(tac "$MOLE_RC" | awk -F'|' -v date1="$dateA" -v date2="$dateB" -v group="$groupName" '
  index($1, group) && $4 >= date1 && $4 <= date2 { print $0 }' |
  grep -m 1 -F -e "$DIR" |
  awk -F'|' '{ print $3 }')
  if [[ -z $fileName ]]; then
    printError "9"
  fi
  edit_file
}

function parseArg ### function to process arguments given when running the script
{
  while [[ $1 == "-g"  $1 == "-m"  $1 == "list"  $1 == "secret-log"  $1 == "-a"  $1 == "-b"  $1 == "-h" ]]; do
    case $1 in
      -g)
      if [ -z "$2" ]; then
        printError "5"
      fi
        groupName="$2"
        groupFlag=1
        shift 2
        ;;
      -m)
        mostUsedFlag=1
        shift
        ;;
      list)
        listFlag=1
        shift
        ;;
      secret-log)
        secretlogFlag=1
        shift
        ;;
      -a)
        if [ -z "$2" ]; then
          printError "5"
        fi
        dateAFlag=1
        dateA="$2"
        checkDate "$dateA"
        shift 2;;
      -b)
        if [ -z "$2" ]; then
          printError "5"
        fi
        dateBFlag=1
        dateB="$2"
        checkDate "$dateB"
        shift 2;;
      -h)
        helpFlag=1
        shift 
        ;;
      *)
        break
        ;;
    esac
  done
if [[ $# == 0 ]]; then
  DIR=$(pwd)
else
  if [[ -f $1 ]]; then
    fileName=$(echo "$1" | sed 's/.*\///')
    DIR="$(cd "$(dirname "$1")" && pwd)"
    fileSetFlag=1
  else
    if [[ -e $1 ]]; then
    DIR=$1
    else 
    printError "3"
    fi
  fi
fi
}


function main ### main function
{
  parseArg "$@"
  flagController
  if [[ !( -e $MOLE_RC ) ]]; then
    touch $MOLE_RC
  fi
  if [[ $fileSetFlag == 1 ]]; then
    edit_file
  else
  if [[ !( -s $MOLE_RC ) ]]; then
    printError "7"
  fi 
  if [[ $mostUsedFlag == 0 ]]; then
    findLastUsed
  else
    findMostUsed
  fi
  fi
}

main "$@"