---
title: Fixing stale NULL state in MySQL
project: MySQL
activity_date: "2026-07-28"
summary: "I reported the bug and submitted a patch in May 2023; the fix shipped in MySQL 9.7.2 in July 2026, with a bonus lesson in patience."
credit_quote: Our thanks to Shun Yi and the team from Alibaba for the contribution.
links:
  - label: "Bug #110962 and patch"
    url: https://bugs.mysql.com/bug.php?id=110962
  - label: MySQL 9.7.2 release notes
    url: https://dev.mysql.com/doc/relnotes/mysql/9.7/en/news-9-7-2.html
---

## The problem

Stale NULL-handling state after a trigger-related `UPDATE` could cause subsequent valid statements to produce warnings or errors. The bug came from state surviving beyond the operation it belonged to.

## My contribution

I reported the issue and submitted a patch while working on MySQL at Alibaba Cloud. The public bug record contains the reproduction and patch.

## From report to release

- **May 2023:** reported the bug and uploaded the patch.
- **July 2026:** the fix appeared in MySQL 9.7.2, released on 28 July.

The official release notes acknowledge Shun Yi and the team from Alibaba.
