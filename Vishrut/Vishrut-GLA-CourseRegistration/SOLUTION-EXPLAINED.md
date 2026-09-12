# Practice Examination – 1 | Solution Walkthrough (Vishrut)

**Subject:** Application Build and Release Automation Lab (BCSE1522)
**Prefix used:** `Vishrut-GLA` ✅

> Ye file sirf tumhare samajhne aur examiner ko **viva/demo** dene ke liye hai.
> Isse repo me daal sakte ho ya reference ke liye apne paas rakh sakte ho.
> **Important:** Roll Number abhi placeholder hai (`<YourUniversityRollNumber>`) — `course_info.txt` me apna asli roll number daal dena submit karne se pehle.

---

## Q1 — Linux Environment and File Management (5 Marks)

```bash
mkdir Vishrut-GLA-CourseRegistration
cd Vishrut-GLA-CourseRegistration
mkdir src docs backup

cat > course_info.txt << 'EOF'
Student Name: Vishrut
Roll Number: <YourUniversityRollNumber>
Subject Name: Application Build and Release Automation Lab
Project Name: Online Course Registration System
Semester: 5th Semester
EOF

pwd            # current working directory dikhata hai
cd Vishrut-GLA-CourseRegistration   # project dir me navigate
ls -la         # directory contents
cat course_info.txt    # file content
find . -print | sed -e 's;[^/]*/;  ;g'   # tree-jaisa structure (tree na ho to yeh chalega)
```

**Viva tip:** `pwd` = "print working directory". `find ... | sed` ek chhota trick hai `tree` command ki tarah dikhne ke liye jab `tree` installed na ho.

---

## Q2 — Git Repository and Version Control (6 Marks)

```bash
git init
git config user.name "Vishrut"
git config user.email "vishrut@example.com"

git status          # abhi kuch bhi tracked nahi hai
git add .           # sab files stage
git status          # ab "Changes to be committed" dikhega
git commit -m "Initial commit: project structure and course_info.txt"
```

Phir file modify ki (course/program name + project description add kiya):

```bash
# course_info.txt me add karo:
# Course/Program: B.Tech Computer Science Engineering
# Project Description: A web-based Online Course Registration System ...

git status          # "modified: course_info.txt"
git diff             # exact changes line-by-line dikhata hai (+ / -)
git add course_info.txt
git commit -m "Update course_info.txt: add course/program name and project description"
git log --oneline --graph --all      # commit history
```

**Viva tip:** `git diff` staging se pehle changes dikhata hai; commit hone ke baad `git log -p` se history me diff dekh sakte ho.

---

## Q3 — Feature Branch Development (7 Marks)

```bash
git branch course-search
git checkout course-search        # (ya: git switch course-search)

# src/ ke andar course_search.txt banao module description ke saath

git add src/course_search.txt
git commit -m "Add course_search.txt: implement Course Search Module description"

git branch                         # sab branches list
git branch --show-current          # current branch ka naam

git checkout main                  # main pe wapas
ls src/    # <- yahan error aayega ya file missing dikhegi
```

**Important nuance (examiner ko batana):** Jab tum `main` pe wapas jaate ho, `src/course_search.txt` wahan **nahi hoga** — kyunki wo commit sirf `course-search` branch pe hai, `main` pe nahi. Yehi cheez prove karti hai ki **feature independently develop hua**, bina `main` ko touch kiye. Isko `git branch -v` ya `git log main..course-search` se bhi dikhaya ja sakta hai.

---

## Q4 — Merging and Conflict Resolution (8 Marks)

### Part A — Merge Feature

```bash
git checkout main
git merge course-search -m "Merge branch 'course-search' into main"

ls src/                       # course_search.txt ab main pe bhi hai
cat src/course_search.txt
git log --oneline --graph --all
```

Note: Agar `main` pe branch banane ke baad koi naya commit nahi hua tha, to Git **fast-forward merge** karega (seedha pointer aage badha dega, merge commit nahi banega). Ye bhi ek valid merge hai — bas examiner ko bata dena "yeh fast-forward merge hai kyunki main aage nahi badha tha."

### Part B — Generate Conflict

```bash
git checkout -b course-update
# course_info.txt ki "Semester:" line ko modify karo (version A)
git add course_info.txt
git commit -m "course-update: clarify semester as Odd Semester 2026-27"

git checkout main
# usi "Semester:" line ko DIFFERENT tarike se modify karo (version B)
git add course_info.txt
git commit -m "main: clarify semester as 5th Sem CSE Section A"

git merge course-update -m "Merge branch 'course-update' into main"
# --> CONFLICT (content): Merge conflict in course_info.txt

git status     # "both modified: course_info.txt"
cat course_info.txt   # conflict markers <<<<<<< ======= >>>>>>> dikhenge
```

File kuch aisi dikhegi conflict ke time:

```
<<<<<<< HEAD
Semester: 5th Sem (CSE - Section A)
=======
Semester: 5th Semester (Odd Semester - 2026-27)
>>>>>>> course-update
```

**Manually resolve karo** — dono lines ko ek meaningful line me combine karo aur `<<<<<<<`, `=======`, `>>>>>>>` markers **poori tarah hata do**:

```
Semester: 5th Sem (CSE - Section A), Odd Semester 2026-27
```

```bash
git add course_info.txt
git commit -m "Resolve merge conflict in course_info.txt: combine semester details from both branches"

git status                          # "nothing to commit, working tree clean"
git log --oneline --graph --all --decorate
```

**Viva tip (bahut important):** Examiner conflict resolution ka process hi sabse zyada dekhta/poochta hai. Bata sakte ho:
1. Kyun conflict aaya (dono branches ne same line different tarah se edit ki).
2. `<<<<<<< HEAD` = tumhari current branch ka version, `=======` separator, `>>>>>>> course-update` = incoming branch ka version.
3. Tumne file manually edit karke decide kiya ki final content kya hoga, markers hataye, `add` + `commit` kiya.

---

## Q5 — Final Demonstration (4 Marks)

Examiner ko ek saath ye commands chala ke dikhao:

```bash
find . -not -path './.git*' -print | sed -e 's;[^/]*/;  ;g'   # project structure
git status                                                    # clean working tree
git log --oneline --graph --all --decorate                    # full history with branches
git branch -a                                                 # all branches exist (main, course-search, course-update)
cat src/course_search.txt                                     # merged feature present
cat course_info.txt                                           # conflict resolved cleanly
```

Final commit graph aisa dikhega:

```
*   Resolve merge conflict in course_info.txt (main)
|\
| * course-update: clarify semester as Odd Semester 2026-27 (course-update)
* | main: clarify semester as 5th Sem CSE Section A
|/
* Add course_search.txt (course-search)
* Update course_info.txt: add course/program name and project description
* Initial commit: project structure and course_info.txt
```

---

## Before you submit

1. `course_info.txt` me apna **real University Roll Number** daal do (abhi placeholder hai).
2. Ye poora folder apni GitHub repo me apne naam ke folder (`Vishrut/`) ke andar copy karke push karo (steps neeche chat me diye hain).
3. Practical exam me har command khud terminal me type/run karke examiner ko dikhana — sirf file dikhana kaafi nahi hota, wo live demo maangte hain.
