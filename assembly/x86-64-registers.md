---
layout: assembly
title: x86-64 Registers - Shellcodex
permalink: /assembly/x86-64-registers.html
---

<head>
  <meta charset="UTF-8">
  <style>
    h1 { text-align: center; }
    .header-links { text-align: center; margin-bottom: 20px; }
    .header-links a { margin: 0 10px; text-decoration: none; color: blue; }
    table { border-collapse: collapse; width: 100%; font-size: 14px; }
    th, td { border: 1px solid #7c848eff; padding: 6px; text-align: left; }
    th { background-color: #7c848eff; }
    tr:nth-child(even) { background-color: #214353ff; }
    tr:hover { background-color: #45778eff; }
  </style>
</head>
<body>
  <h1>x86-64 Registers</h1>
  <div>
    <table id="syscalltable">
      <thead>
        <tr>
          <th>8-Byte Registers</th>
          <th>Bytes 0-3</th>
          <th>Bytes 0-1</th>
          <th>Byte 0</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>rax</td>
          <td>eax</td>
          <td>ax</td>
          <td>ah, al</td>
        </tr>
        <tr>
          <td>rcx</td>
          <td>ecx</td>
          <td>cx</td>
          <td>ch, cl</td>
        </tr>
        <tr>
          <td>rdx</td>
          <td>edx</td>
          <td>dx</td>
          <td>dh, dl</td>
        </tr>
        <tr>
          <td>rbx</td>
          <td>ebx</td>
          <td>bx</td>
          <td>bh, bl</td>
        </tr>
        <tr>
          <td>rsi</td>
          <td>esi</td>
          <td>si</td>
          <td>sil</td>
        </tr>
        <tr>
          <td>rdi</td>
          <td>edi</td>
          <td>di</td>
          <td>dil</td>
        </tr>
        <tr>
          <td>rsp</td>
          <td>esp</td>
          <td>sp</td>
          <td>spl</td>
        </tr>
        <tr>
          <td>rbp</td>
          <td>ebp</td>
          <td>bp</td>
          <td>bpl</td>
        </tr>
        <tr>
          <td>r8</td>
          <td>r8d</td>
          <td>r8w</td>
          <td>r8b</td>
        </tr>
        <tr>
          <td>r9</td>
          <td>r9d</td>
          <td>r9w</td>
          <td>r9b</td>
        </tr>
        <tr>
          <td>r10</td>
          <td>r10d</td>
          <td>r10w</td>
          <td>r10b</td>
        </tr>
        <tr>
          <td>r11</td>
          <td>r11d</td>
          <td>r11w</td>
          <td>r11b</td>
        </tr>
        <tr>
          <td>r12</td>
          <td>r12d</td>
          <td>r12w</td>
          <td>r12b</td>
        </tr>
        <tr>
          <td>r13</td>
          <td>r13d</td>
          <td>r13w</td>
          <td>r13b</td>
        </tr>
        <tr>
          <td>r14</td>
          <td>r14d</td>
          <td>r14w</td>
          <td>r14b</td>
        </tr>
        <tr>
          <td>r15</td>
          <td>r15d</td>
          <td>r15w</td>
          <td>r15b</td>
        </tr>
      </tbody>
    </table>
  </div>
</body>