# Lab 1 Submission — Version Control & Advanced Git

**Student:** Diana Minnakhmetova
**Date:** 06-02-2026

---

## Task 1

[X] All command outputs for object inspection.

[X] A 1–2 sentence explanation of what each object type represents.

[X] Analysis of how Git stores repository data.

[X] Example of blob, tree, and commit object content.

### 1.1 Sample Commits Created

Created a test file and committed it:

```bash
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % echo "Test content" > test.txt
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % git add test.txt
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % cat test.txt
Test content
```

### 1.2 Git Objects Inspection

**Step 1: Get commit hash**

```bash
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % git log --oneline -1
97fa04a (HEAD -> feature/lab2, origin/main, origin/HEAD, main) feat: add PR template
```

**Step 2: Inspect commit (get tree hash)**
```bash
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % git cat-file -t HEAD
commit
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % git cat-file -p HEAD
tree 93a278bb933629b424592fd082690049d49a8d1f
parent 97fa04a9ab52ac0a6cc7f3ce6a80cba705a0122e
author d.minnakhmetova <d.minnakhmetova@innopolis.university> 1770405471 +0300
committer d.minnakhmetova <d.minnakhmetova@innopolis.university> 1770405471 +0300
gpgsig -----BEGIN SSH SIGNATURE-----
 U1NIU0lHAAAAAQAAADMAAAALc3NoLWVkMjU1MTkAAAAg9/03d56TF63uUejbUr94J4SWx4
 cXBXCdoIz4Vu9bq7EAAAADZ2l0AAAAAAAAAAZzaGE1MTIAAABTAAAAC3NzaC1lZDI1NTE5
 AAAAQFJitl9mCpddPCjvF18k9dZUmsINFJc3mR8lm2DjO8uy1QoTowku+L4oKGFheUTx8A
 kJ2Yj3TQum7oWFYV3xow4=
 -----END SSH SIGNATURE-----

Add test file
```

**Step 3: Inspect tree (get blob hash)**
```bash
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % git cat-file -t 93a278bb933629b424592fd082690049d49a8d1f
tree
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % git cat-file -p 93a278bb933629b424592fd082690049d49a8d1f
040000 tree 2ab7b3c7679e8cb698ff11802dc937457138ca32    .github
100644 blob 6e60bebec0724892a7c82c52183d0a7b467cb6bb    README.md
040000 tree a1061247fd38ef2a568735939f86af7b1000f83c    app
040000 tree eb79e5a468ab89b024bd4f3ed867c6a3954fe1f0    labs
040000 tree d3fb3722b7a867a83efde73c57c49b5ab3e62c63    lectures
100644 blob 2eec599a1130d2ff231309bb776d1989b97c6ab2    test.txt
```

**Step 4: Inspect blob (get content)**
```bash
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % git cat-file -t 2eec599a1130d2ff231309bb776d1989b97c6ab2
blob
dminnakhmetova@MacBook-Air-Diana-3 DevOps-Intro % git cat-file -p 2eec599a1130d2ff231309bb776d1989b97c6ab2
Test content
```

### 1.3 Analysis

#### What each object represents:

**Blob:** Raw file content. Git stores the actual data ("Test content") as a blob, identified by its SHA-1 hash. Identical content always produces the same hash, enabling deduplication.

**Tree:** Directory snapshot. A tree is a list of blobs and subtrees with their names and permissions. It's like a filesystem directory listing. Each tree points to specific blob versions, capturing the exact state of files at one moment.

**Commit:** Historical wrapper. A commit contains:
  - A tree (snapshot of all files)
  - Parent commits (creating a chain)
  - Metadata (author, timestamp, message)
  - Cryptographic signature for integrity

**How Git stores data:**

Git uses a **content-addressable storage** model. Each object (blob, tree, commit) is hashed (SHA-1), and the hash becomes its permanent ID. Git stores these objects in `.git/objects/`. The directed acyclic graph structure — where commits point to parents—creates the history chain. Because hashes are deterministic based on content, identical files share the same blob across commits and any tampering changes the hash.
