---
title: "Ref - Useful Functions"
author: "Jonny Saunders"
date: "October 5, 2017"
output: 
  md_document:
    preserve_yaml: true
    toc: true
    toc_depth: 2
order: 100
---

-   [Workspace](#workspace)
-   [Objects](#objects)
-   [Iteration](#iteration)

Workspace
=========

<table>
<thead>
<tr class="header">
<th>Function</th>
<th>Function function</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><code>getwd()</code></td>
<td>Return your working directory</td>
</tr>
<tr class="even">
<td><code>setwd()</code></td>
<td>Assign a working directory to use for the current session</td>
</tr>
<tr class="odd">
<td><code>ls()</code></td>
<td>List the objects in memory</td>
</tr>
<tr class="even">
<td><code>rm(x)</code></td>
<td>Remove an object from the workspace (in this example, <code>x</code>)</td>
</tr>
<tr class="odd">
<td><code>rm(list=ls())</code></td>
<td>Remove all objects from the workspace</td>
</tr>
<tr class="even">
<td><code>history()</code></td>
<td>Show the last 25 commands</td>
</tr>
</tbody>
</table>

Objects
=======

<table>
<thead>
<tr class="header">
<th>Function</th>
<th>Function function</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>typeof</td>
<td>Get object's base type</td>
</tr>
<tr class="even">
<td>pryr::otype</td>
<td>Get object's type system (base, S3, S4, RC)</td>
</tr>
</tbody>
</table>

Iteration
=========

<table>
<thead>
<tr class="header">
<th>Function</th>
<th>Function function</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><code>txtProgressBar</code></td>
<td>Keep track of how things are going</td>
</tr>
</tbody>
</table>
