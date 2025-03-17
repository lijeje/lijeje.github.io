---
layout: post
title: "SQLite database is locked 해결방법"
categories:
  - sql
tags:
  - SQL
  - database locked
---

# SQLite Database is Locked 오류 해결법: 쉽게 해결하는 방법

**Subversion(SVN)을 사용하면서 가장 자주 겪을 수 있는 오류 중 하나가 "SQLite database is locked" 입니다. 
이 오류는 팀 프로젝트에서 협업을 하면서 여러 사용자가 SVN을 동기화하려 할 때 자주 발생하며
커밋이나 업데이트가 제대로 되지 않아 불편을 초래할 수 있습니다.**


## 1.cleanup 실행
SQLite 데이터베이스가 잠겨 있으면 SVN 작업이 중단되므로 가장먼저 cleanup을 실행합니다. 

## 2.wc.db 확인
cleanup으로 문제가 해결되지 않는다면 SVN 작업 디렉토리 내에 위치한 .svn 폴더의 wc.db 파일을 확인하고, 잠금 정보가 있는지 검토가 필요합니다. 
![SQLite Database open](/assets/images/SQLite_project_open.jpg)



파일-프로젝트열기에서 lock이 발생한 경로의 wc.db파일을 선택 합니다. 

그 후 sql 실행에서 두가지 작업을 완료해야합니다. 

![SQLite exc](/assets/images/SQLite_project_open.jpg)


1. DELETE FROM WORK_QUEUE;
작업 대기열 초기화: WORK_QUEUE 테이블은 SVN에서 수행해야 할 작업을 대기열로 저장하는 곳입니다. 이 테이블은 SVN 클라이언트가 동기화할 때 각 작업을 추적합니다.
왜 삭제하나?: 이 명령은 대기 중인 작업이 있다고 SQLite 데이터베이스에서 "작업 대기열"을 비워주기 위해 사용됩니다. 만약 작업이 중단되었거나 대기열이 잠겨서 새로운 작업을 시작할 수 없을 때, 이 명령을 통해 대기열을 초기화하고 다시 동기화할 수 있게 만듭니다.
2. DELETE FROM WC_LOCK;
잠금 해제: WC_LOCK 테이블은 SVN 작업 디렉토리에 대한 잠금 상태를 관리하는 곳입니다. SVN이 특정 파일이나 디렉토리를 잠금 상태로 두는 이유는 다른 사용자가 동시에 동일한 작업을 처리하지 않도록 하여 충돌을 방지하기 위함입니다.
왜 삭제하나?: 만약 작업 중에 잠금이 풀리지 않거나, 작업이 비정상적으로 중단되어 잠금 정보가 남아 있다면, 이 명령은 잠금을 해제하여 다른 사용자가 SVN 작업을 정상적으로 진행할 수 있도록 합니다.

## 3.이클립스 재시작
만약 DELETE FROM WORK_QUEUE 조차 실행할 수 없이 잠겨있다면?
이클립스를 종료 후 다시 시작해야 합니다.

읽어주셔서 감사합니다!
