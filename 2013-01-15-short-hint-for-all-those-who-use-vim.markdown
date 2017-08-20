---
author: Piotr Król
layout: post
post_title: "Short hint for all those who use vim and pathogen"
post_date: 2013-01-15 11:51:00+01:00
categories: [Productivity, Vim, Linux]
published: true
---

Today, after updating my wokspace to latest version I encounter below error during vim running:
```
Error detected while processing function pathogen#runtime_append_all_bundles:
line 1:
E121: Undefined variable: source_path
E116: Invalid arguments for function string(source_path).') to pathogen#incubate('.string(source_path.'/{}').')') 
E116: Invalid arguments for function 4_warn
Press ENTER or type command to continue
```
Quick look on pathogen runtime_append_all_bundles function and I found:
```bash
call s:warn('Change pathogen#runtime_append_all_bundles('.string(a:1).') to pathogen#incubate('.string(a:1.'/{}').')')
```
So simply replacing:
```bash
call pathogen#runtime_append_all_bundles()
```
with:
```bash
call pathogen#incubate() in $HOME/.vimrc fix the problem.
```
