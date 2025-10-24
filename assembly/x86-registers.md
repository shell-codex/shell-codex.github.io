---
layout: assembly
title: x86 Registers - Shellcodex
permalink: /assembly/x86-registers.html
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
  <h1>x86 Registers</h1>
  <div>
    <table id="syscalltable">
      <thead>
        <tr>
          <th>4-Byte Registers</th>
          <th>Bytes 0-1</th>
          <th>Byte 0</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>eax</td>
          <td>ax</td>
          <td>ah, al</td>
        </tr>
        <tr>
          <td>ecx</td>
          <td>cx</td>
          <td>ch, cl</td>
        </tr>
        <tr>
          <td>edx</td>
          <td>dx</td>
          <td>dh, dl</td>
        </tr>
        <tr>
          <td>ebx</td>
          <td>bx</td>
          <td>bh, bl</td>
        </tr>
        <tr>
          <td>esi</td>
          <td>si</td>
          <td>sil</td>
        </tr>
        <tr>
          <td>edi</td>
          <td>di</td>
          <td>dil</td>
        </tr>
        <tr>
          <td>esp</td>
          <td>sp</td>
          <td>spl</td>
        </tr>
        <tr>
          <td>ebp</td>
          <td>bp</td>
          <td>bpl</td>
        </tr>
      </tbody>
    </table>
  </div>
</body>