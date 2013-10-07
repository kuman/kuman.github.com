---
layout: post
title: "iTunes connectのアプリレポートを自動取得"
description: ""
published: false
category: 
tags: []
---
{% include JB/setup %}

#!/bin/sh

AUTO_INGES_PATH=~/jenkins-jobs/getItunesConnectInfo/Autoingestion
VENDOR_ID=85085948
USER=brooks_i
PASS=brooks
TYPE="Sales Daily Summary"
DATE=`date --date="2 days ago" +"%Y%m%d"`
FILENAME_COMPRESSED="S_D_"$VENDOR_ID"_"$DATE".txt.gz"
FILENAME="S_D_"$VENDOR_ID"_"$DATE".txt"
SQLITE_FILE=~/jenkins-jobs/getItunesConnectInfo/report.sqlite

echo $FILENAME_COMPRESSED
echo $AUTO_INGES_PATH/$FILENAME_COMPRESSED

# パス移動
cd $AUTO_INGES_PATH

# 本日のログ・ファイルが存在しているか確認
if [ ! -e $AUTO_INGES_PATH/$FILENAME ]; then
    echo "Update report data"
    # レポートを取得する
    java Autoingestion $USER $PASS $VENDOR_ID $TYPE $DATE
    # レポートを解凍する
    gunzip $FILENAME_COMPRESSED
    # 1行目を削除する
    sed -i -e "1d" $FILENAME
    # Sqlite3にインサートする
    sqlite3 -separator $'\t' $SQLITE_FILE ".import $AUTO_INGES_PATH/$FILENAME logs"
else
    echo "Report file already exists."
fi

sqlite3 $SQLITE_FILE "select * from logs;"

CSV_FILE=/var/lib/jenkins/workspace/getItunesConnectReport/daily.csv
sqlite3 $SQLITE_FILE "select Title, Units from logs ORDER BY \`End Date\` desc LIMIT 1;" |sed -e "s/|/\n/g" > $CSV_FILE

# Tableを作成
# sqlite3 $SQLITE_FILE "DROP TABLE logs;"
# sqlite3 $SQLITE_FILE "CREATE TABLE logs ('Provider' text,'Provider Country' text,'SKU' text, 'Developer' text,'Title' text,'Version' text, 'Product Type Identifier' text, 'Units' integer,'Developer Proceeds' integer,'Begin Date' date,'End Date' date,'Customer Currency' text,'Country Code' text,'Currency of Proceeds' text,'Apple Identifier' text,'Customer Price' text,'Promo Code' text,'Parent Identifier' text,'Subscription' text,'Period' integer, UNIQUE('SKU','Title','Version','Product Type Identifier','Units','Developer Proceeds','Begin Date','Customer Currency','Country Code','Currency of Proceeds','Apple Identifier','Customer Price','Promo Code','Parent Identifier','Subscription','Period'));"
