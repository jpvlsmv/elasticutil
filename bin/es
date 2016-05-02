#!/bin/bash

METHOD=GET
HOST=localhost
PORT=9200
DATA=""
COMMAND=_cluster/health

while [ ${#} -gt 0 ];do 
  OPTERR=0;
  OPTIND=1;
  getopts "p:h:d:vnGDPO" arg;
  case "$arg" in
        p) PORT=$OPTARG             ;;
        h) HOST=$OPTARG             ;;
        d) DATA=$OPTARG             ;;
        v) VERBOSE=-v               ;;
        n) NOOP=echo                ;;
        G) METHOD=GET               ;;
        D) METHOD=DELETE            ;;
        P) METHOD=PUT               ;;
        O) METHOD=POST              ;;
        \?) SET+=("$1")             ;;
        *) echo "Coding error: '-$arg' is not handled by case">&2 ;;
  esac;
  shift;
  [ "" != "$OPTARG" ] && shift;
done
[ ${#SET[@]} -gt 0 ] && set "" "${SET[@]}" && shift

# validate and compactify data
if [ "$DATA" ]
then
  DATA="-d $(echo $DATA | jq -c .)"
fi

[ "$@" ] && COMMAND="$@"
SEP='?'
[[ $COMMAND =~ [$SEP] ]] && SEP='&'

echo "${NOOP:+NOT }Performing [$METHOD] on [$HOST]:[$PORT] at [$COMMAND] with [$DATA]"

$NOOP curl $VERBOSE -X$METHOD $DATA http://$HOST:$PORT/$COMMAND$SEP"pretty&human"
