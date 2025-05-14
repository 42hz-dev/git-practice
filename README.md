# 🧪 git-practice

Git 브랜치 전략 및 커맨드 실습용 저장소입니다.  
기능 단위 브랜치 개발 → 팀 브랜치 → 최종 develop 브랜치로 통합되는 구조를 따릅니다.

---

## 📂 브랜치 구조

```plaintext
develop/
├── worker1                 ← worker1 (메인 브랜치)
│   ├── feature-A           ← 기능 구현 브랜치
│   ├── feature-B
│   └── feature-C
├── worker2                 ← worker2 (메인 브랜치)
│   ├── feature-A
│   └── feature-B
├── worker3                 ← worker3 (메인 브랜치)
│   └── feature-A
└── worker4                 ← worker4 (메인 브랜치)
    └── feature-A
```

## 🛠 작업 흐름 요약
1. 각 작업자는 develop 브랜치에서 자신의 메인 브랜치(workerX)를 생성
2. 작업자는 workerX 브랜치에서 기능 단위 브랜치(feature-A, feature-B 등)를 생성 후 작업
3. 기능 작업 완료 후, workerX 브랜치에 merge
4. 팀 작업 통합 시, workerX 브랜치를 develop 브랜치에 merge
5. 작업 종료된 feature 브랜치는 로컬 및 원격에서 삭제

## 🧾 Git Command List
### 📘 로그 및 상태 확인
```bash
git log                           # 커밋 로그 전체 확인
git log --oneline                 # 간결한 로그 보기
git log -n 5                      # 최근 5개의 커밋만 보기
git log <파일명>                 # 특정 파일 변경 이력 보기
git status                        # 현재 상태 확인
```

### 📥 스테이징 및 커밋
```bash
git add .                         # 모든 변경 파일 추가
git add file1.txt file2.txt       # 특정 파일 추가
git commit -m "메시지"            # 커밋
```

### 🚀 브랜치 생성 및 전환
```bash
git checkout <브랜치명>           # 브랜치 전환
git checkout -b worker1           # 새 브랜치 생성 후 전환
```

### 🔀 병합 및 삭제
```bash
git merge <브랜치명>              # 브랜치 병합
git branch -d <브랜치명>          # 로컬 브랜치 삭제
git push origin --delete <브랜치명> # 원격 브랜치 삭제
```

### ☁️ Push / Pull
```bash
git push -u origin <브랜치명>     # 원격 저장소로 Push
git pull origin <브랜치명>        # 원격 저장소에서 Pull
```

### ☁️ Push / Pull
```bash
git reflog                        # HEAD 이동 이력 확인
git reset --hard HEAD@{1}        # 이전 상태로 복구
git reset --soft <커밋 해시>      # 커밋만 되돌림 (작업 파일 유지)
git reset --mixed <커밋 해시>     # 커밋 + 스테이지 취소
git reset --hard <커밋 해시>      # 커밋 + 작업 파일 모두 취소
```

## ✅ 참고 사항
feature 브랜치는 원격 저장소에 꼭 올릴 필요 없음




# Git 협업 브랜치 전략 (with worker 브랜치)

이 문서는 협업 중 `main`과 개인 작업 브랜치(`worker`)를 활용한 Git 워크플로우에 대해 설명합니다.

## ✅ 브랜치 구조

- `main`: 모든 팀원이 공유하는 안정된 코드 베이스
- `worker`: 개인 작업용 브랜치 (ex. `minwoo`, `worker` 등)

---

## 📌 기본 작업 흐름

### 1. `main` 브랜치 최신화

```bash
git checkout main
git pull origin main
```

2. 작업용 브랜치(worker)로 이동 후 최신화
```bash
git checkout worker
git merge origin/main
```
main의 최신 내용을 작업 브랜치에 반영합니다.
또는 git rebase origin/main도 가능하지만, 커밋 히스토리가 깔끔해지는 대신 충돌 시 복잡할 수 있습니다.

3. 작업 진행
작업 코드를 작성하고 커밋합니다.
```bash
git add .
git commit -m "작업 내용"
```
4. 중간에 main에 변경이 생겼는지 확인
```bash
git fetch origin main
git merge origin/main
```
다른 사람이 main에 푸시한 경우 충돌이 발생할 수 있으며, 충돌 시 해결 후 커밋합니다.

5. 작업 완료 후 worker 브랜치 푸시
```bash
git push origin worker
```
자신이 작업한 코드를 원격 worker 브랜치에 푸시합니다.

6. main으로 돌아와 최신화
```bash
git checkout main
git pull origin main
```

7. main에 worker 브랜치 병합
```bash
git merge worker --no-ff
--no-ff를 통해 병합 커밋을 남깁니다. 나중에 누가 어떤 브랜치를 병합했는지 확인하기 쉽습니다.
```

8. 최종적으로 main 푸시
```bash
git push origin main
```

## 🔍 정리

| 상황              | 명령어                                                                 | 설명                                      |
|-------------------|------------------------------------------------------------------------|-------------------------------------------|
| 작업 전           | `git checkout main`<br>`git pull origin main`                          | `main` 브랜치 최신화                      |
| 브랜치 이동       | `git checkout worker`                                                  | 작업 브랜치로 이동                        |
| 최신 내용 반영    | `git merge origin/main`<br>or<br>`git rebase origin/main`              | `main`에서 수정된 내용 반영               |
| 작업 중간 최신화  | `git fetch origin main`<br>+<br>`git merge origin/main`<br>or<br>`rebase` | 작업 중 `main` 변경 내용 동기화           |
| 작업 완료         | `git push origin worker`                                               | 자신의 작업 브랜치에 푸시                 |
| 병합              | `git merge worker --no-ff`                                             | `main`에 병합 (병합 커밋 남기기)          |
| 최종 푸시         | `git push origin main`                                                 | `main`에 최종 변경사항 푸시               |
