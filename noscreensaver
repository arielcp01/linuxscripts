#!/bin/bash

createcfg()
{
  USER=$(echo $USERINFO | cut -d: -f1)
  GROUP=$(id -g -n $USER)
  if [ "$TYPE" = "D" ]; then
     mkdir $CFGDIR
     chown $USER:$GROUP $CFGDIR
  fi
  echo "plasma-desktop" > $FILECFGCHK
  chown $USER:$GROUP $FILECFGCHK 
}

checkblacklist()
{
  CFGDIR=$FILE/.noscreensaver
  FILECFGCHK=$CFGDIR/$FILECFG
  #echo $FILECFGCHK
  if [ -d $CFGDIR ]; then
    if [ -f $FILECFGCHK ]; then
      for WBLIST in $( cat $FILECFGCHK ); 
      do
	if [ "$WBLIST" = "$WNAME" ]; then
	   #echo "WBLIST: $WBLIST"
	   #echo "WNAME $WNAME"
	   #echo "WINDOW: $WINDOW"
	   #echo "SCREEN: $SCREEN"
	   return 1
	fi
      done   
    else
      TYPE="F"
      createcfg
    fi
  else
    TYPE="D"
    createcfg
  fi
return 0
}

exec > /dev/null 2>&1
DELAY=$1
FILECFG="blacklist.cfg"
export DISPLAY=:0

if [ -z "$1" ];then
    DELAY=30
fi

while :
do
    for USERINFO in $(getent passwd)
    do
      FILE=$(echo $USERINFO | cut -d: -f6)
      AUTHORITY=$FILE/.Xauthority
      if [ -f $AUTHORITY ];then
	  export XAUTHORITY=$AUTHORITY
	  WID=$(xdotool getactivewindow)
	  if [[ $WID -ge 1 ]];then
	      WINDOW=$(echo $(xwininfo -id $WID -stats | \
			      egrep '(Width|Height):' | \
			      awk '{print $NF}') | \
		      sed -e 's/ /x/')
	      SCREEN=$(xdpyinfo | grep -m1 dimensions | awk '{print $2}')
	      if [ "$WINDOW" = "$SCREEN" ]; then
		 WNAME=$(xdotool getwindowname $WID)
		 checkblacklist
		 result=$?
		 if [[ $result -eq 0 ]];then
		    xdotool key meta
		 fi
	      fi
	  fi
      fi
    done
    sleep $DELAY
done



