---
title: "[Git] Git Branch 기초"
date: 2021-05-06 09:41:00 +0900
categories: [Development, Git]
tags: [git, branch]
---

# [Git] git branch 활용 기초편

## Branch 명령어

- `git branch`
   - `git branch`: 존재하는 브랜치의 모든 목록을 보여준다.  
   - `git branch <branch_name>`: 이름이 `<branch_name>`인 브랜치를 생성한다.
- `git checkout`  
   - `git checkout <branch_name>`: 브랜치`<branch_name>`으로  작업 브랜치를 전환해준다.  
   - `git checkout -b <branch_name>`: `branch_name`을 생성하고 해당 브랜치로 작업 공간을 전환해준다.
- `git merge`     
   - `git merge <branch_name>`:  현재 브랜치에 `<branch_name>`브랜치의 커밋들을 병합해준다.

## 활용 예시

1. `git branch feature/signup`
   : 회원가입 페이지에서 올바르지 않은 입력에 대하여 예외처리를 해주는 기능을 만들 것이므로 `feature/signup`이라는 브랜치를 만들어주자  
   `git branch` 명령어로 존재하는 브랜치의 목록을 볼 수 있다.
2. `git checkout feature/signup`
   : 체크아웃 명령어로 `feature/signup`브랜치로 이동해주자.  
   `1`, `2`의 명령어를 묶어서 `git checkout -b feature/signup`로 한번에 실행할 수 있다.
3. `signup`브랜치에서 작업을 수행하자
4. 작업이 끝났다면 `feature/signup`브랜치에서 커밋을 올리고 푸시해주자
   : `git commit -m "Improve sign-up UI/UX"`, `git push`
5. 마스터 브랜치로 이동한 후 `feature/signup` 브랜치를 merge해주자.
   : `git checkout main`, `git merge signup`
6. merge한 내용을 push해주자
   : `git push`
7. 작업이 끝난 `signup`브랜치를 삭제해주자.
   : `git branch -d signup`
8. 원격저장소(깃허브)에서도 브랜치를 삭제해주자.
   : `git push origin --delete signup`

이번 경우에는 새로 브랜치를 따고 `main`브랜치에서는 작업을 진행하지 않아 merge 할 때 충돌이 발생하지 않은 경우라 간단하게 진행할 수 있었다. 하지만 `merge`를 해줄 때 `main`브랜치에서도 작업이 이루어져 있는 상태라면 충돌이 발생할 수 있다. 다음엔 충돌이 일어났을 때 어떻게 관리해 주어야 하는지에 대해 정리해보겠다.