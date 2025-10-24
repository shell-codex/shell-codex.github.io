---
layout: assembly
title: x86 Syscall Table - Shellcodex
permalink: /assembly/x86-syscall-table.html
---

<head>
  <meta charset="UTF-8">
  <style>
    h1 { text-align: center; }
    .header-links { text-align: center; margin-bottom: 20px; }
    .header-links a { margin: 0 10px; text-decoration: none; color: blue; }
    input { display: block; margin: 0 auto 20px auto; padding: 8px; width: 50%; }
    table { border-collapse: collapse; width: 100%; font-size: 12px; }
    th, td { border: 1px solid #7c848eff; padding: 6px; text-align: left; }
    th { background-color: #7c848eff; }
    tr:nth-child(even) { background-color: #214353ff; }
    tr:hover { background-color: #45778eff; }
  </style>
</head>
<body>
  <h1>x86 Syscall Table</h1>
  <input type="text" id="searchBox" onkeyup="filterTable()" placeholder="Search for syscall...">
  <table id="syscallTable">
    <thead>
      <tr>
        <th>syscall</th>
        <th>number</th>
        <th>eax</th>
        <th>ebx</th>
        <th>ecx</th>
        <th>edx</th>
        <th>esi</th>
        <th>edi</th>
        <th>ebp</th>
      </tr>
    </thead>
    <tbody>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/restart_syscall.2.html" target="_blank">restart_syscall</a></td>
        <td>0</td>
        <td>0x00</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/exit.2.html" target="_blank">exit</a></td>
        <td>1</td>
        <td>0x01</td>
        <td>int error_code</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fork.2.html" target="_blank">fork</a></td>
        <td>2</td>
        <td>0x02</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/read.2.html" target="_blank">read</a></td>
        <td>3</td>
        <td>0x03</td>
        <td>unsigned int fd</td>
        <td>char *buf</td>
        <td>size_t count</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/write.2.html" target="_blank">write</a></td>
        <td>4</td>
        <td>0x04</td>
        <td>unsigned int fd</td>
        <td>const char *buf</td>
        <td>size_t count</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/open.2.html" target="_blank">open</a></td>
        <td>5</td>
        <td>0x05</td>
        <td>const char *filename</td>
        <td>int flags</td>
        <td>umode_t mode</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/close.2.html" target="_blank">close</a></td>
        <td>6</td>
        <td>0x06</td>
        <td>unsigned int fd</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/waitpid.2.html" target="_blank">waitpid</a></td>
        <td>7</td>
        <td>0x07</td>
        <td>pid_t pid</td>
        <td>int *stat_addr</td>
        <td>int options</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/creat.2.html" target="_blank">creat</a></td>
        <td>8</td>
        <td>0x08</td>
        <td>const char *pathname</td>
        <td>umode_t mode</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/link.2.html" target="_blank">link</a></td>
        <td>9</td>
        <td>0x09</td>
        <td>const char *oldname</td>
        <td>const char *newname</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/unlink.2.html" target="_blank">unlink</a></td>
        <td>10</td>
        <td>0x0a</td>
        <td>const char *pathname</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/execve.2.html" target="_blank">execve</a></td>
        <td>11</td>
        <td>0x0b</td>
        <td>const char *name</td>
        <td>const char *const *argv</td>
        <td>const char *const *envp</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/chdir.2.html" target="_blank">chdir</a></td>
        <td>12</td>
        <td>0x0c</td>
        <td>const char *filename</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/time.2.html" target="_blank">time</a></td>
        <td>13</td>
        <td>0x0d</td>
        <td>time_t *tloc</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mknod.2.html" target="_blank">mknod</a></td>
        <td>14</td>
        <td>0x0e</td>
        <td>const char *filename</td>
        <td>umode_t mode</td>
        <td>unsigned dev</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/chmod.2.html" target="_blank">chmod</a></td>
        <td>15</td>
        <td>0x0f</td>
        <td>const char *filename</td>
        <td>umode_t mode</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/lchown.2.html" target="_blank">lchown</a></td>
        <td>16</td>
        <td>0x10</td>
        <td>const char *filename</td>
        <td>uid_t user</td>
        <td>gid_t group</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/break.2.html" target="_blank">break</a></td>
        <td>17</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/oldstat.2.html" target="_blank">oldstat</a></td>
        <td>18</td>
        <td>0x12</td>
        <td>const char *filename</td>
        <td>struct __old_kernel_stat*statbuf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/lseek.2.html" target="_blank">lseek</a></td>
        <td>19</td>
        <td>0x13</td>
        <td>unsigned int fd</td>
        <td>off_t offset</td>
        <td>unsigned int origin</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getpid.2.html" target="_blank">getpid</a></td>
        <td>20</td>
        <td>0x14</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mount.2.html" target="_blank">mount</a></td>
        <td>21</td>
        <td>0x15</td>
        <td>char *dev_name</td>
        <td>char *dir_name</td>
        <td>char *type</td>
        <td>unsigned long flags</td>
        <td>void *data</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/umount.2.html" target="_blank">umount</a></td>
        <td>22</td>
        <td>0x16</td>
        <td>char *name</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setuid.2.html" target="_blank">setuid</a></td>
        <td>23</td>
        <td>0x17</td>
        <td>uid_t uid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getuid.2.html" target="_blank">getuid</a></td>
        <td>24</td>
        <td>0x18</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/stime.2.html" target="_blank">stime</a></td>
        <td>25</td>
        <td>0x19</td>
        <td>time_t *tptr</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/ptrace.2.html" target="_blank">ptrace</a></td>
        <td>26</td>
        <td>0x1a</td>
        <td>long request</td>
        <td>long pid</td>
        <td>unsigned long addr</td>
        <td>unsigned long data</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/alarm.2.html" target="_blank">alarm</a></td>
        <td>27</td>
        <td>0x1b</td>
        <td>unsigned int seconds</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/oldfstat.2.html" target="_blank">oldfstat</a></td>
        <td>28</td>
        <td>0x1c</td>
        <td>unsigned int fd</td>
        <td>struct __old_kernel_stat*statbuf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/pause.2.html" target="_blank">pause</a></td>
        <td>29</td>
        <td>0x1d</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/utime.2.html" target="_blank">utime</a></td>
        <td>30</td>
        <td>0x1e</td>
        <td>char *filename</td>
        <td>struct utimbuf*times</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/stty.2.html" target="_blank">stty</a></td>
        <td>31</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/gtty.2.html" target="_blank">gtty</a></td>
        <td>32</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/access.2.html" target="_blank">access</a></td>
        <td>33</td>
        <td>0x21</td>
        <td>const char *filename</td>
        <td>int mode</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/nice.2.html" target="_blank">nice</a></td>
        <td>34</td>
        <td>0x22</td>
        <td>int increment</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man3/ftime.3.html" target="_blank">ftime</a></td>
        <td>35</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sync.2.html" target="_blank">sync</a></td>
        <td>36</td>
        <td>0x24</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/kill.2.html" target="_blank">kill</a></td>
        <td>37</td>
        <td>0x25</td>
        <td>pid_t pid</td>
        <td>int sig</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/rename.2.html" target="_blank">rename</a></td>
        <td>38</td>
        <td>0x26</td>
        <td>const char *oldname</td>
        <td>const char *newname</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mkdir.2.html" target="_blank">mkdir</a></td>
        <td>39</td>
        <td>0x27</td>
        <td>const char *pathname</td>
        <td>umode_t mode</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/rmdir.2.html" target="_blank">rmdir</a></td>
        <td>40</td>
        <td>0x28</td>
        <td>const char *pathname</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/dup.2.html" target="_blank">dup</a></td>
        <td>41</td>
        <td>0x29</td>
        <td>unsigned int fildes</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/pipe.2.html" target="_blank">pipe</a></td>
        <td>42</td>
        <td>0x2a</td>
        <td>int *fildes</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/times.2.html" target="_blank">times</a></td>
        <td>43</td>
        <td>0x2b</td>
        <td>struct tms*tbuf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/prof.2.html" target="_blank">prof</a></td>
        <td>44</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/brk.2.html" target="_blank">brk</a></td>
        <td>45</td>
        <td>0x2d</td>
        <td>unsigned long brk</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setgid.2.html" target="_blank">setgid</a></td>
        <td>46</td>
        <td>0x2e</td>
        <td>gid_t gid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getgid.2.html" target="_blank">getgid</a></td>
        <td>47</td>
        <td>0x2f</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/signal.2.html" target="_blank">signal</a></td>
        <td>48</td>
        <td>0x30</td>
        <td>int sig</td>
        <td>__sighandler_t handler</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/geteuid.2.html" target="_blank">geteuid</a></td>
        <td>49</td>
        <td>0x31</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getegid.2.html" target="_blank">getegid</a></td>
        <td>50</td>
        <td>0x32</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/acct.2.html" target="_blank">acct</a></td>
        <td>51</td>
        <td>0x33</td>
        <td>const char *name</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/umount2.2.html" target="_blank">umount2</a></td>
        <td>52</td>
        <td>0x34</td>
        <td>char *name</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/lock.2.html" target="_blank">lock</a></td>
        <td>53</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/ioctl.2.html" target="_blank">ioctl</a></td>
        <td>54</td>
        <td>0x36</td>
        <td>unsigned int fd</td>
        <td>unsigned int cmd</td>
        <td>unsigned long arg</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fcntl.2.html" target="_blank">fcntl</a></td>
        <td>55</td>
        <td>0x37</td>
        <td>unsigned int fd</td>
        <td>unsigned int cmd</td>
        <td>unsigned long arg</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mpx.2.html" target="_blank">mpx</a></td>
        <td>56</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setpgid.2.html" target="_blank">setpgid</a></td>
        <td>57</td>
        <td>0x39</td>
        <td>pid_t pid</td>
        <td>pid_t pgid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man3/ulimit.3.html" target="_blank">ulimit</a></td>
        <td>58</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/oldolduname.2.html" target="_blank">oldolduname</a></td>
        <td>59</td>
        <td>0x3b</td>
        <td>struct oldold_utsname*name</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/umask.2.html" target="_blank">umask</a></td>
        <td>60</td>
        <td>0x3c</td>
        <td>int mask</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/chroot.2.html" target="_blank">chroot</a></td>
        <td>61</td>
        <td>0x3d</td>
        <td>const char *filename</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/ustat.2.html" target="_blank">ustat</a></td>
        <td>62</td>
        <td>0x3e</td>
        <td>unsigned dev</td>
        <td>struct ustat*ubuf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/dup2.2.html" target="_blank">dup2</a></td>
        <td>63</td>
        <td>0x3f</td>
        <td>unsigned int oldfd</td>
        <td>unsigned int newfd</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getppid.2.html" target="_blank">getppid</a></td>
        <td>64</td>
        <td>0x40</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getpgrp.2.html" target="_blank">getpgrp</a></td>
        <td>65</td>
        <td>0x41</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setsid.2.html" target="_blank">setsid</a></td>
        <td>66</td>
        <td>0x42</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sigaction.2.html" target="_blank">sigaction</a></td>
        <td>67</td>
        <td>0x43</td>
        <td>int sig</td>
        <td>conststruct old_sigaction*act</td>
        <td>struct old_sigaction*oact</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sgetmask.2.html" target="_blank">sgetmask</a></td>
        <td>68</td>
        <td>0x44</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/ssetmask.2.html" target="_blank">ssetmask</a></td>
        <td>69</td>
        <td>0x45</td>
        <td>int newmask</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setreuid.2.html" target="_blank">setreuid</a></td>
        <td>70</td>
        <td>0x46</td>
        <td>uid_t ruid</td>
        <td>uid_t euid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setregid.2.html" target="_blank">setregid</a></td>
        <td>71</td>
        <td>0x47</td>
        <td>gid_t rgid</td>
        <td>gid_t egid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sigsuspend.2.html" target="_blank">sigsuspend</a></td>
        <td>72</td>
        <td>0x48</td>
        <td>int history0</td>
        <td>int history1</td>
        <td>old_sigset_t mask</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sigpending.2.html" target="_blank">sigpending</a></td>
        <td>73</td>
        <td>0x49</td>
        <td>old_sigset_t *set</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sethostname.2.html" target="_blank">sethostname</a></td>
        <td>74</td>
        <td>0x4a</td>
        <td>char *name</td>
        <td>int len</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setrlimit.2.html" target="_blank">setrlimit</a></td>
        <td>75</td>
        <td>0x4b</td>
        <td>unsigned int resource</td>
        <td>struct rlimit*rlim</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getrlimit.2.html" target="_blank">getrlimit</a></td>
        <td>76</td>
        <td>0x4c</td>
        <td>unsigned int resource</td>
        <td>struct rlimit*rlim</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getrusage.2.html" target="_blank">getrusage</a></td>
        <td>77</td>
        <td>0x4d</td>
        <td>int who</td>
        <td>struct rusage*ru</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/gettimeofday.2.html" target="_blank">gettimeofday</a></td>
        <td>78</td>
        <td>0x4e</td>
        <td>struct timeval*tv</td>
        <td>struct timezone*tz</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/settimeofday.2.html" target="_blank">settimeofday</a></td>
        <td>79</td>
        <td>0x4f</td>
        <td>struct timeval*tv</td>
        <td>struct timezone*tz</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getgroups.2.html" target="_blank">getgroups</a></td>
        <td>80</td>
        <td>0x50</td>
        <td>int gidsetsize</td>
        <td>gid_t *grouplist</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setgroups.2.html" target="_blank">setgroups</a></td>
        <td>81</td>
        <td>0x51</td>
        <td>int gidsetsize</td>
        <td>gid_t *grouplist</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/select.2.html" target="_blank">select</a></td>
        <td>82</td>
        <td>0x52</td>
        <td>int n</td>
        <td>fd_set *inp</td>
        <td>fd_set *outp</td>
        <td>fd_set *exp</td>
        <td>struct timeval*tvp</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/symlink.2.html" target="_blank">symlink</a></td>
        <td>83</td>
        <td>0x53</td>
        <td>const char *oldname</td>
        <td>const char *newname</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/oldlstat.2.html" target="_blank">oldlstat</a></td>
        <td>84</td>
        <td>0x54</td>
        <td>const char *filename</td>
        <td>struct __old_kernel_stat*statbuf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/readlink.2.html" target="_blank">readlink</a></td>
        <td>85</td>
        <td>0x55</td>
        <td>const char *path</td>
        <td>char *buf</td>
        <td>int bufsiz</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/uselib.2.html" target="_blank">uselib</a></td>
        <td>86</td>
        <td>0x56</td>
        <td>const char *library</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/swapon.2.html" target="_blank">swapon</a></td>
        <td>87</td>
        <td>0x57</td>
        <td>const char *specialfile</td>
        <td>int swap_flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/reboot.2.html" target="_blank">reboot</a></td>
        <td>88</td>
        <td>0x58</td>
        <td>int magic1</td>
        <td>int magic2</td>
        <td>unsigned int cmd</td>
        <td>void *arg</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/readdir.2.html" target="_blank">readdir</a></td>
        <td>89</td>
        <td>0x59</td>
        <td>unsigned int fd</td>
        <td>struct old_linux_dirent*dirent</td>
        <td>unsigned int count</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mmap.2.html" target="_blank">mmap</a></td>
        <td>90</td>
        <td>0x5a</td>
        <td>unsigned long addr</td>
        <td>unsigned long len</td>
        <td>unsigned long prot</td>
        <td>unsigned long flags</td>
        <td>unsigned long fd</td>
        <td>unsigned long off</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/munmap.2.html" target="_blank">munmap</a></td>
        <td>91</td>
        <td>0x5b</td>
        <td>unsigned long addr</td>
        <td>size_t len</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/truncate.2.html" target="_blank">truncate</a></td>
        <td>92</td>
        <td>0x5c</td>
        <td>const char *path</td>
        <td>long length</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/ftruncate.2.html" target="_blank">ftruncate</a></td>
        <td>93</td>
        <td>0x5d</td>
        <td>unsigned int fd</td>
        <td>unsigned long length</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fchmod.2.html" target="_blank">fchmod</a></td>
        <td>94</td>
        <td>0x5e</td>
        <td>unsigned int fd</td>
        <td>umode_t mode</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fchown.2.html" target="_blank">fchown</a></td>
        <td>95</td>
        <td>0x5f</td>
        <td>unsigned int fd</td>
        <td>uid_t user</td>
        <td>gid_t group</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getpriority.2.html" target="_blank">getpriority</a></td>
        <td>96</td>
        <td>0x60</td>
        <td>int which</td>
        <td>int who</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setpriority.2.html" target="_blank">setpriority</a></td>
        <td>97</td>
        <td>0x61</td>
        <td>int which</td>
        <td>int who</td>
        <td>int niceval</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man3/profil.3.html" target="_blank">profil</a></td>
        <td>98</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/statfs.2.html" target="_blank">statfs</a></td>
        <td>99</td>
        <td>0x63</td>
        <td>const char *pathname</td>
        <td>struct statfs*buf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fstatfs.2.html" target="_blank">fstatfs</a></td>
        <td>100</td>
        <td>0x64</td>
        <td>unsigned int fd</td>
        <td>struct statfs*buf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/ioperm.2.html" target="_blank">ioperm</a></td>
        <td>101</td>
        <td>0x65</td>
        <td>unsigned long from</td>
        <td>unsigned long num</td>
        <td>int turn_on</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/socketcall.2.html" target="_blank">socketcall</a></td>
        <td>102</td>
        <td>0x66</td>
        <td>int call</td>
        <td>unsigned long *args</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/syslog.2.html" target="_blank">syslog</a></td>
        <td>103</td>
        <td>0x67</td>
        <td>int type</td>
        <td>char *buf</td>
        <td>int len</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setitimer.2.html" target="_blank">setitimer</a></td>
        <td>104</td>
        <td>0x68</td>
        <td>int which</td>
        <td>struct itimerval*value</td>
        <td>struct itimerval*ovalue</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getitimer.2.html" target="_blank">getitimer</a></td>
        <td>105</td>
        <td>0x69</td>
        <td>int which</td>
        <td>struct itimerval*value</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/stat.2.html" target="_blank">stat</a></td>
        <td>106</td>
        <td>0x6a</td>
        <td>const char *filename</td>
        <td>struct __old_kernel_stat*statbuf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/lstat.2.html" target="_blank">lstat</a></td>
        <td>107</td>
        <td>0x6b</td>
        <td>const char *filename</td>
        <td>struct __old_kernel_stat*statbuf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fstat.2.html" target="_blank">fstat</a></td>
        <td>108</td>
        <td>0x6c</td>
        <td>unsigned int fd</td>
        <td>struct __old_kernel_stat*statbuf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/olduname.2.html" target="_blank">olduname</a></td>
        <td>109</td>
        <td>0x6d</td>
        <td>struct oldold_utsname*name</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/iopl.2.html" target="_blank">iopl</a></td>
        <td>110</td>
        <td>0x6e</td>
        <td>unsigned int level</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/vhangup.2.html" target="_blank">vhangup</a></td>
        <td>111</td>
        <td>0x6f</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/idle.2.html" target="_blank">idle</a></td>
        <td>112</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/vm86old.2.html" target="_blank">vm86old</a></td>
        <td>113</td>
        <td>0x71</td>
        <td>struct vm86_struct*v86</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/wait4.2.html" target="_blank">wait4</a></td>
        <td>114</td>
        <td>0x72</td>
        <td>pid_t upid</td>
        <td>int *stat_addr</td>
        <td>int options</td>
        <td>struct rusage*ru</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/swapoff.2.html" target="_blank">swapoff</a></td>
        <td>115</td>
        <td>0x73</td>
        <td>const char *specialfile</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sysinfo.2.html" target="_blank">sysinfo</a></td>
        <td>116</td>
        <td>0x74</td>
        <td>struct sysinfo*info</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/ipc.2.html" target="_blank">ipc</a></td>
        <td>117</td>
        <td>0x75</td>
        <td>unsigned int call</td>
        <td>int first</td>
        <td>unsigned long second</td>
        <td>unsigned long third</td>
        <td>void *ptr</td>
        <td>long fifth</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fsync.2.html" target="_blank">fsync</a></td>
        <td>118</td>
        <td>0x76</td>
        <td>unsigned int fd</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sigreturn.2.html" target="_blank">sigreturn</a></td>
        <td>119</td>
        <td>0x77</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/clone.2.html" target="_blank">clone</a></td>
        <td>120</td>
        <td>0x78</td>
        <td>unsigned long clone_flags</td>
        <td>unsigned long newsp</td>
        <td>void *parent_tid</td>
        <td>void *child_tid</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setdomainname.2.html" target="_blank">setdomainname</a></td>
        <td>121</td>
        <td>0x79</td>
        <td>char *name</td>
        <td>int len</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/uname.2.html" target="_blank">uname</a></td>
        <td>122</td>
        <td>0x7a</td>
        <td>struct old_utsname*name</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/modify_ldt.2.html" target="_blank">modify_ldt</a></td>
        <td>123</td>
        <td>0x7b</td>
        <td>int func</td>
        <td>void *ptr</td>
        <td>unsigned long bytecount</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/adjtimex.2.html" target="_blank">adjtimex</a></td>
        <td>124</td>
        <td>0x7c</td>
        <td>struct timex*txc_p</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mprotect.2.html" target="_blank">mprotect</a></td>
        <td>125</td>
        <td>0x7d</td>
        <td>unsigned long start</td>
        <td>size_t len</td>
        <td>unsigned long prot</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sigprocmask.2.html" target="_blank">sigprocmask</a></td>
        <td>126</td>
        <td>0x7e</td>
        <td>int how</td>
        <td>old_sigset_t *nset</td>
        <td>old_sigset_t *oset</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/create_module.2.html" target="_blank">create_module</a></td>
        <td>127</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/init_module.2.html" target="_blank">init_module</a></td>
        <td>128</td>
        <td>0x80</td>
        <td>void *umod</td>
        <td>unsigned long len</td>
        <td>const char *uargs</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/delete_module.2.html" target="_blank">delete_module</a></td>
        <td>129</td>
        <td>0x81</td>
        <td>const char *name_user</td>
        <td>unsigned int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/get_kernel_syms.2.html" target="_blank">get_kernel_syms</a></td>
        <td>130</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/quotactl.2.html" target="_blank">quotactl</a></td>
        <td>131</td>
        <td>0x83</td>
        <td>unsigned int cmd</td>
        <td>const char *special</td>
        <td>qid_t id</td>
        <td>void *addr</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getpgid.2.html" target="_blank">getpgid</a></td>
        <td>132</td>
        <td>0x84</td>
        <td>pid_t pid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fchdir.2.html" target="_blank">fchdir</a></td>
        <td>133</td>
        <td>0x85</td>
        <td>unsigned int fd</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/bdflush.2.html" target="_blank">bdflush</a></td>
        <td>134</td>
        <td>0x86</td>
        <td>int func</td>
        <td>long data</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sysfs.2.html" target="_blank">sysfs</a></td>
        <td>135</td>
        <td>0x87</td>
        <td>int option</td>
        <td>unsigned long arg1</td>
        <td>unsigned long arg2</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/personality.2.html" target="_blank">personality</a></td>
        <td>136</td>
        <td>0x88</td>
        <td>unsigned int personality</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/afs_syscall.2.html" target="_blank">afs_syscall</a></td>
        <td>137</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setfsuid.2.html" target="_blank">setfsuid</a></td>
        <td>138</td>
        <td>0x8a</td>
        <td>uid_t uid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setfsgid.2.html" target="_blank">setfsgid</a></td>
        <td>139</td>
        <td>0x8b</td>
        <td>gid_t gid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/_llseek.2.html" target="_blank">_llseek</a></td>
        <td>140</td>
        <td>0x8c</td>
        <td>unsigned int fd</td>
        <td>unsigned long offset_high</td>
        <td>unsigned long offset_low</td>
        <td>loff_t *result</td>
        <td>unsigned int origin</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getdents.2.html" target="_blank">getdents</a></td>
        <td>141</td>
        <td>0x8d</td>
        <td>unsigned int fd</td>
        <td>struct linux_dirent*dirent</td>
        <td>unsigned int count</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/_newselect.2.html" target="_blank">_newselect</a></td>
        <td>142</td>
        <td>0x8e</td>
        <td>int n</td>
        <td>fd_set *inp</td>
        <td>fd_set *outp</td>
        <td>fd_set *exp</td>
        <td>struct timeval*tvp</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/flock.2.html" target="_blank">flock</a></td>
        <td>143</td>
        <td>0x8f</td>
        <td>unsigned int fd</td>
        <td>unsigned int cmd</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/msync.2.html" target="_blank">msync</a></td>
        <td>144</td>
        <td>0x90</td>
        <td>unsigned long start</td>
        <td>size_t len</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/readv.2.html" target="_blank">readv</a></td>
        <td>145</td>
        <td>0x91</td>
        <td>unsigned long fd</td>
        <td>conststruct iovec*vec</td>
        <td>unsigned long vlen</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/writev.2.html" target="_blank">writev</a></td>
        <td>146</td>
        <td>0x92</td>
        <td>unsigned long fd</td>
        <td>conststruct iovec*vec</td>
        <td>unsigned long vlen</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getsid.2.html" target="_blank">getsid</a></td>
        <td>147</td>
        <td>0x93</td>
        <td>pid_t pid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fdatasync.2.html" target="_blank">fdatasync</a></td>
        <td>148</td>
        <td>0x94</td>
        <td>unsigned int fd</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/_sysctl.2.html" target="_blank">_sysctl</a></td>
        <td>149</td>
        <td>0x95</td>
        <td>struct __sysctl_args*args</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mlock.2.html" target="_blank">mlock</a></td>
        <td>150</td>
        <td>0x96</td>
        <td>unsigned long start</td>
        <td>size_t len</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/munlock.2.html" target="_blank">munlock</a></td>
        <td>151</td>
        <td>0x97</td>
        <td>unsigned long start</td>
        <td>size_t len</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mlockall.2.html" target="_blank">mlockall</a></td>
        <td>152</td>
        <td>0x98</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/munlockall.2.html" target="_blank">munlockall</a></td>
        <td>153</td>
        <td>0x99</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sched_setparam.2.html" target="_blank">sched_setparam</a></td>
        <td>154</td>
        <td>0x9a</td>
        <td>pid_t pid</td>
        <td>struct sched_param*param</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sched_getparam.2.html" target="_blank">sched_getparam</a></td>
        <td>155</td>
        <td>0x9b</td>
        <td>pid_t pid</td>
        <td>struct sched_param*param</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sched_setscheduler.2.html" target="_blank">sched_setscheduler</a></td>
        <td>156</td>
        <td>0x9c</td>
        <td>pid_t pid</td>
        <td>int policy</td>
        <td>struct sched_param*param</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sched_getscheduler.2.html" target="_blank">sched_getscheduler</a></td>
        <td>157</td>
        <td>0x9d</td>
        <td>pid_t pid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sched_yield.2.html" target="_blank">sched_yield</a></td>
        <td>158</td>
        <td>0x9e</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sched_get_priority_max.2.html" target="_blank">sched_get_priority_max</a></td>
        <td>159</td>
        <td>0x9f</td>
        <td>int policy</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sched_get_priority_min.2.html" target="_blank">sched_get_priority_min</a></td>
        <td>160</td>
        <td>0xa0</td>
        <td>int policy</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sched_rr_get_interval.2.html" target="_blank">sched_rr_get_interval</a></td>
        <td>161</td>
        <td>0xa1</td>
        <td>pid_t pid</td>
        <td>struct timespec*interval</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/nanosleep.2.html" target="_blank">nanosleep</a></td>
        <td>162</td>
        <td>0xa2</td>
        <td>struct timespec*rqtp</td>
        <td>struct timespec*rmtp</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mremap.2.html" target="_blank">mremap</a></td>
        <td>163</td>
        <td>0xa3</td>
        <td>unsigned long addr</td>
        <td>unsigned long old_len</td>
        <td>unsigned long new_len</td>
        <td>unsigned long flags</td>
        <td>unsigned long new_addr</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setresuid.2.html" target="_blank">setresuid</a></td>
        <td>164</td>
        <td>0xa4</td>
        <td>uid_t ruid</td>
        <td>uid_t euid</td>
        <td>uid_t suid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getresuid.2.html" target="_blank">getresuid</a></td>
        <td>165</td>
        <td>0xa5</td>
        <td>uid_t *ruidp</td>
        <td>uid_t *euidp</td>
        <td>uid_t *suidp</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/vm86.2.html" target="_blank">vm86</a></td>
        <td>166</td>
        <td>0xa6</td>
        <td>unsigned long cmd</td>
        <td>unsigned long arg</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/query_module.2.html" target="_blank">query_module</a></td>
        <td>167</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/poll.2.html" target="_blank">poll</a></td>
        <td>168</td>
        <td>0xa8</td>
        <td>struct pollfd*ufds</td>
        <td>unsigned int nfds</td>
        <td>int timeout_msecs</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/nfsservctl.2.html" target="_blank">nfsservctl</a></td>
        <td>169</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setresgid.2.html" target="_blank">setresgid</a></td>
        <td>170</td>
        <td>0xaa</td>
        <td>gid_t rgid</td>
        <td>gid_t egid</td>
        <td>gid_t sgid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getresgid.2.html" target="_blank">getresgid</a></td>
        <td>171</td>
        <td>0xab</td>
        <td>gid_t *rgidp</td>
        <td>gid_t *egidp</td>
        <td>gid_t *sgidp</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/prctl.2.html" target="_blank">prctl</a></td>
        <td>172</td>
        <td>0xac</td>
        <td>int option</td>
        <td>unsigned long arg2</td>
        <td>unsigned long arg3</td>
        <td>unsigned long arg4</td>
        <td>unsigned long arg5</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/rt_sigreturn.2.html" target="_blank">rt_sigreturn</a></td>
        <td>173</td>
        <td>0xad</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/rt_sigaction.2.html" target="_blank">rt_sigaction</a></td>
        <td>174</td>
        <td>0xae</td>
        <td>int sig</td>
        <td>conststruct sigaction*act</td>
        <td>struct sigaction*oact</td>
        <td>size_t sigsetsize</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/rt_sigprocmask.2.html" target="_blank">rt_sigprocmask</a></td>
        <td>175</td>
        <td>0xaf</td>
        <td>int how</td>
        <td>sigset_t *nset</td>
        <td>sigset_t *oset</td>
        <td>size_t sigsetsize</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/rt_sigpending.2.html" target="_blank">rt_sigpending</a></td>
        <td>176</td>
        <td>0xb0</td>
        <td>sigset_t *set</td>
        <td>size_t sigsetsize</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/rt_sigtimedwait.2.html" target="_blank">rt_sigtimedwait</a></td>
        <td>177</td>
        <td>0xb1</td>
        <td>const sigset_t *uthese</td>
        <td>siginfo_t *uinfo</td>
        <td>conststruct timespec*uts</td>
        <td>size_t sigsetsize</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/rt_sigqueueinfo.2.html" target="_blank">rt_sigqueueinfo</a></td>
        <td>178</td>
        <td>0xb2</td>
        <td>pid_t pid</td>
        <td>int sig</td>
        <td>siginfo_t *uinfo</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/rt_sigsuspend.2.html" target="_blank">rt_sigsuspend</a></td>
        <td>179</td>
        <td>0xb3</td>
        <td>sigset_t *unewset</td>
        <td>size_t sigsetsize</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/pread64.2.html" target="_blank">pread64</a></td>
        <td>180</td>
        <td>0xb4</td>
        <td>char *buf size_t count</td>
        <td>loff_t pos</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/pwrite64.2.html" target="_blank">pwrite64</a></td>
        <td>181</td>
        <td>0xb5</td>
        <td>const char *buf size_t count</td>
        <td>loff_t pos</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/chown.2.html" target="_blank">chown</a></td>
        <td>182</td>
        <td>0xb6</td>
        <td>const char *filename</td>
        <td>uid_t user</td>
        <td>gid_t group</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getcwd.2.html" target="_blank">getcwd</a></td>
        <td>183</td>
        <td>0xb7</td>
        <td>char *buf</td>
        <td>unsigned long size</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/capget.2.html" target="_blank">capget</a></td>
        <td>184</td>
        <td>0xb8</td>
        <td>cap_user_header_t header</td>
        <td>cap_user_data_t dataptr</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/capset.2.html" target="_blank">capset</a></td>
        <td>185</td>
        <td>0xb9</td>
        <td>cap_user_header_t header</td>
        <td>const cap_user_data_t data</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sigaltstack.2.html" target="_blank">sigaltstack</a></td>
        <td>186</td>
        <td>0xba</td>
        <td>const stack_t *uss</td>
        <td>stack_t *uoss</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sendfile.2.html" target="_blank">sendfile</a></td>
        <td>187</td>
        <td>0xbb</td>
        <td>int out_fd</td>
        <td>int in_fd</td>
        <td>off_t *offset</td>
        <td>size_t count</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getpmsg.2.html" target="_blank">getpmsg</a></td>
        <td>188</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/putpmsg.2.html" target="_blank">putpmsg</a></td>
        <td>189</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/vfork.2.html" target="_blank">vfork</a></td>
        <td>190</td>
        <td>0xbe</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/ugetrlimit.2.html" target="_blank">ugetrlimit</a></td>
        <td>191</td>
        <td>0xbf</td>
        <td>unsigned int resource</td>
        <td>struct rlimit*rlim</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mmap2.2.html" target="_blank">mmap2</a></td>
        <td>192</td>
        <td>0xc0</td>
        <td>unsigned long addr</td>
        <td>unsigned long len</td>
        <td>unsigned long prot</td>
        <td>unsigned long flags</td>
        <td>unsigned long fd</td>
        <td>unsigned long pgoff</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/truncate64.2.html" target="_blank">truncate64</a></td>
        <td>193</td>
        <td>0xc1</td>
        <td>loff_t length</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/ftruncate64.2.html" target="_blank">ftruncate64</a></td>
        <td>194</td>
        <td>0xc2</td>
        <td>loff_t length</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/stat64.2.html" target="_blank">stat64</a></td>
        <td>195</td>
        <td>0xc3</td>
        <td>const char *filename</td>
        <td>struct stat64*statbuf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/lstat64.2.html" target="_blank">lstat64</a></td>
        <td>196</td>
        <td>0xc4</td>
        <td>const char *filename</td>
        <td>struct stat64*statbuf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fstat64.2.html" target="_blank">fstat64</a></td>
        <td>197</td>
        <td>0xc5</td>
        <td>unsigned long fd</td>
        <td>struct stat64*statbuf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/lchown32.2.html" target="_blank">lchown32</a></td>
        <td>198</td>
        <td>0xc6</td>
        <td>const char *filename</td>
        <td>uid_t user</td>
        <td>gid_t group</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getuid32.2.html" target="_blank">getuid32</a></td>
        <td>199</td>
        <td>0xc7</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getgid32.2.html" target="_blank">getgid32</a></td>
        <td>200</td>
        <td>0xc8</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/geteuid32.2.html" target="_blank">geteuid32</a></td>
        <td>201</td>
        <td>0xc9</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getegid32.2.html" target="_blank">getegid32</a></td>
        <td>202</td>
        <td>0xca</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setreuid32.2.html" target="_blank">setreuid32</a></td>
        <td>203</td>
        <td>0xcb</td>
        <td>uid_t ruid</td>
        <td>uid_t euid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setregid32.2.html" target="_blank">setregid32</a></td>
        <td>204</td>
        <td>0xcc</td>
        <td>gid_t rgid</td>
        <td>gid_t egid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getgroups32.2.html" target="_blank">getgroups32</a></td>
        <td>205</td>
        <td>0xcd</td>
        <td>int gidsetsize</td>
        <td>gid_t *grouplist</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setgroups32.2.html" target="_blank">setgroups32</a></td>
        <td>206</td>
        <td>0xce</td>
        <td>int gidsetsize</td>
        <td>gid_t *grouplist</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fchown32.2.html" target="_blank">fchown32</a></td>
        <td>207</td>
        <td>0xcf</td>
        <td>unsigned int fd</td>
        <td>uid_t user</td>
        <td>gid_t group</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setresuid32.2.html" target="_blank">setresuid32</a></td>
        <td>208</td>
        <td>0xd0</td>
        <td>uid_t ruid</td>
        <td>uid_t euid</td>
        <td>uid_t suid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getresuid32.2.html" target="_blank">getresuid32</a></td>
        <td>209</td>
        <td>0xd1</td>
        <td>uid_t *ruidp</td>
        <td>uid_t *euidp</td>
        <td>uid_t *suidp</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setresgid32.2.html" target="_blank">setresgid32</a></td>
        <td>210</td>
        <td>0xd2</td>
        <td>gid_t rgid</td>
        <td>gid_t egid</td>
        <td>gid_t sgid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getresgid32.2.html" target="_blank">getresgid32</a></td>
        <td>211</td>
        <td>0xd3</td>
        <td>gid_t *rgidp</td>
        <td>gid_t *egidp</td>
        <td>gid_t *sgidp</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/chown32.2.html" target="_blank">chown32</a></td>
        <td>212</td>
        <td>0xd4</td>
        <td>const char *filename</td>
        <td>uid_t user</td>
        <td>gid_t group</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setuid32.2.html" target="_blank">setuid32</a></td>
        <td>213</td>
        <td>0xd5</td>
        <td>uid_t uid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setgid32.2.html" target="_blank">setgid32</a></td>
        <td>214</td>
        <td>0xd6</td>
        <td>gid_t gid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setfsuid32.2.html" target="_blank">setfsuid32</a></td>
        <td>215</td>
        <td>0xd7</td>
        <td>uid_t uid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setfsgid32.2.html" target="_blank">setfsgid32</a></td>
        <td>216</td>
        <td>0xd8</td>
        <td>gid_t gid</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/pivot_root.2.html" target="_blank">pivot_root</a></td>
        <td>217</td>
        <td>0xd9</td>
        <td>const char *new_root</td>
        <td>const char *put_old</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mincore.2.html" target="_blank">mincore</a></td>
        <td>218</td>
        <td>0xda</td>
        <td>unsigned long start</td>
        <td>size_t len</td>
        <td>unsigned char *vec</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/madvise.2.html" target="_blank">madvise</a></td>
        <td>219</td>
        <td>0xdb</td>
        <td>unsigned long start</td>
        <td>size_t len_in</td>
        <td>int behavior</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getdents64.2.html" target="_blank">getdents64</a></td>
        <td>220</td>
        <td>0xdc</td>
        <td>unsigned int fd</td>
        <td>struct linux_dirent64*dirent</td>
        <td>unsigned int count</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fcntl64.2.html" target="_blank">fcntl64</a></td>
        <td>221</td>
        <td>0xdd</td>
        <td>unsigned int fd</td>
        <td>unsigned int cmd</td>
        <td>unsigned long arg</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/gettid.2.html" target="_blank">gettid</a></td>
        <td>224</td>
        <td>0xe0</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/readahead.2.html" target="_blank">readahead</a></td>
        <td>225</td>
        <td>0xe1</td>
        <td>loff_t offset size_t count</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setxattr.2.html" target="_blank">setxattr</a></td>
        <td>226</td>
        <td>0xe2</td>
        <td>const char *pathname</td>
        <td>const char *name</td>
        <td>const void *value</td>
        <td>size_t size</td>
        <td>int flags</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/lsetxattr.2.html" target="_blank">lsetxattr</a></td>
        <td>227</td>
        <td>0xe3</td>
        <td>const char *pathname</td>
        <td>const char *name</td>
        <td>const void *value</td>
        <td>size_t size</td>
        <td>int flags</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fsetxattr.2.html" target="_blank">fsetxattr</a></td>
        <td>228</td>
        <td>0xe4</td>
        <td>int fd</td>
        <td>const char *name</td>
        <td>const void *value</td>
        <td>size_t size</td>
        <td>int flags</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getxattr.2.html" target="_blank">getxattr</a></td>
        <td>229</td>
        <td>0xe5</td>
        <td>const char *pathname</td>
        <td>const char *name</td>
        <td>void *value</td>
        <td>size_t size</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/lgetxattr.2.html" target="_blank">lgetxattr</a></td>
        <td>230</td>
        <td>0xe6</td>
        <td>const char *pathname</td>
        <td>const char *name</td>
        <td>void *value</td>
        <td>size_t size</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fgetxattr.2.html" target="_blank">fgetxattr</a></td>
        <td>231</td>
        <td>0xe7</td>
        <td>int fd</td>
        <td>const char *name</td>
        <td>void *value</td>
        <td>size_t size</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/listxattr.2.html" target="_blank">listxattr</a></td>
        <td>232</td>
        <td>0xe8</td>
        <td>const char *pathname</td>
        <td>char *list</td>
        <td>size_t size</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/llistxattr.2.html" target="_blank">llistxattr</a></td>
        <td>233</td>
        <td>0xe9</td>
        <td>const char *pathname</td>
        <td>char *list</td>
        <td>size_t size</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/flistxattr.2.html" target="_blank">flistxattr</a></td>
        <td>234</td>
        <td>0xea</td>
        <td>int fd</td>
        <td>char *list</td>
        <td>size_t size</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/removexattr.2.html" target="_blank">removexattr</a></td>
        <td>235</td>
        <td>0xeb</td>
        <td>const char *pathname</td>
        <td>const char *name</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/lremovexattr.2.html" target="_blank">lremovexattr</a></td>
        <td>236</td>
        <td>0xec</td>
        <td>const char *pathname</td>
        <td>const char *name</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fremovexattr.2.html" target="_blank">fremovexattr</a></td>
        <td>237</td>
        <td>0xed</td>
        <td>int fd</td>
        <td>const char *name</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/tkill.2.html" target="_blank">tkill</a></td>
        <td>238</td>
        <td>0xee</td>
        <td>pid_t pid</td>
        <td>int sig</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sendfile64.2.html" target="_blank">sendfile64</a></td>
        <td>239</td>
        <td>0xef</td>
        <td>int out_fd</td>
        <td>int in_fd</td>
        <td>loff_t *offset</td>
        <td>size_t count</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/futex.2.html" target="_blank">futex</a></td>
        <td>240</td>
        <td>0xf0</td>
        <td>u32 *uaddr</td>
        <td>int op</td>
        <td>u32 val</td>
        <td>struct timespec*utime</td>
        <td>u32 *uaddr2</td>
        <td>u32 val3</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sched_setaffinity.2.html" target="_blank">sched_setaffinity</a></td>
        <td>241</td>
        <td>0xf1</td>
        <td>pid_t pid</td>
        <td>unsigned int len</td>
        <td>unsigned long *user_mask_ptr</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sched_getaffinity.2.html" target="_blank">sched_getaffinity</a></td>
        <td>242</td>
        <td>0xf2</td>
        <td>pid_t pid</td>
        <td>unsigned int len</td>
        <td>unsigned long *user_mask_ptr</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/set_thread_area.2.html" target="_blank">set_thread_area</a></td>
        <td>243</td>
        <td>0xf3</td>
        <td>struct user_desc*u_info</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/get_thread_area.2.html" target="_blank">get_thread_area</a></td>
        <td>244</td>
        <td>0xf4</td>
        <td>struct user_desc*u_info</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/io_setup.2.html" target="_blank">io_setup</a></td>
        <td>245</td>
        <td>0xf5</td>
        <td>unsigned nr_events</td>
        <td>aio_context_t *ctxp</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/io_destroy.2.html" target="_blank">io_destroy</a></td>
        <td>246</td>
        <td>0xf6</td>
        <td>aio_context_t ctx</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/io_getevents.2.html" target="_blank">io_getevents</a></td>
        <td>247</td>
        <td>0xf7</td>
        <td>aio_context_t ctx_id</td>
        <td>long min_nr</td>
        <td>long nr</td>
        <td>struct io_event*events</td>
        <td>struct timespec*timeout</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/io_submit.2.html" target="_blank">io_submit</a></td>
        <td>248</td>
        <td>0xf8</td>
        <td>aio_context_t ctx_id</td>
        <td>long nr</td>
        <td>struct iocb* *iocbpp</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/io_cancel.2.html" target="_blank">io_cancel</a></td>
        <td>249</td>
        <td>0xf9</td>
        <td>aio_context_t ctx_id</td>
        <td>struct iocb*iocb</td>
        <td>struct io_event*result</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fadvise64.2.html" target="_blank">fadvise64</a></td>
        <td>250</td>
        <td>0xfa</td>
        <td>loff_t offset size_t len</td>
        <td>int advice</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/exit_group.2.html" target="_blank">exit_group</a></td>
        <td>252</td>
        <td>0xfc</td>
        <td>int error_code</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/lookup_dcookie.2.html" target="_blank">lookup_dcookie</a></td>
        <td>253</td>
        <td>0xfd</td>
        <td>char *buf size_t len</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/epoll_create.2.html" target="_blank">epoll_create</a></td>
        <td>254</td>
        <td>0xfe</td>
        <td>int size</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/epoll_ctl.2.html" target="_blank">epoll_ctl</a></td>
        <td>255</td>
        <td>0xff</td>
        <td>int epfd</td>
        <td>int op</td>
        <td>int fd</td>
        <td>struct epoll_event*event</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/epoll_wait.2.html" target="_blank">epoll_wait</a></td>
        <td>256</td>
        <td>0x100</td>
        <td>int epfd</td>
        <td>struct epoll_event*events</td>
        <td>int maxevents</td>
        <td>int timeout</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/remap_file_pages.2.html" target="_blank">remap_file_pages</a></td>
        <td>257</td>
        <td>0x101</td>
        <td>unsigned long start</td>
        <td>unsigned long size</td>
        <td>unsigned long prot</td>
        <td>unsigned long pgoff</td>
        <td>unsigned long flags</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/set_tid_address.2.html" target="_blank">set_tid_address</a></td>
        <td>258</td>
        <td>0x102</td>
        <td>int *tidptr</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/timer_create.2.html" target="_blank">timer_create</a></td>
        <td>259</td>
        <td>0x103</td>
        <td>const clockid_t which_clock</td>
        <td>struct sigevent*timer_event_spec</td>
        <td>timer_t *created_timer_id</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/timer_settime.2.html" target="_blank">timer_settime</a></td>
        <td>260</td>
        <td>0x104</td>
        <td>timer_t timer_id</td>
        <td>int flags</td>
        <td>conststruct itimerspec*new_setting</td>
        <td>struct itimerspec*old_setting</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/timer_gettime.2.html" target="_blank">timer_gettime</a></td>
        <td>261</td>
        <td>0x105</td>
        <td>timer_t timer_id</td>
        <td>struct itimerspec*setting</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/timer_getoverrun.2.html" target="_blank">timer_getoverrun</a></td>
        <td>262</td>
        <td>0x106</td>
        <td>timer_t timer_id</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/timer_delete.2.html" target="_blank">timer_delete</a></td>
        <td>263</td>
        <td>0x107</td>
        <td>timer_t timer_id</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/clock_settime.2.html" target="_blank">clock_settime</a></td>
        <td>264</td>
        <td>0x108</td>
        <td>const clockid_t which_clock</td>
        <td>conststruct timespec*tp</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/clock_gettime.2.html" target="_blank">clock_gettime</a></td>
        <td>265</td>
        <td>0x109</td>
        <td>const clockid_t which_clock</td>
        <td>struct timespec*tp</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/clock_getres.2.html" target="_blank">clock_getres</a></td>
        <td>266</td>
        <td>0x10a</td>
        <td>const clockid_t which_clock</td>
        <td>struct timespec*tp</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/clock_nanosleep.2.html" target="_blank">clock_nanosleep</a></td>
        <td>267</td>
        <td>0x10b</td>
        <td>const clockid_t which_clock</td>
        <td>int flags</td>
        <td>conststruct timespec*rqtp</td>
        <td>struct timespec*rmtp</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/statfs64.2.html" target="_blank">statfs64</a></td>
        <td>268</td>
        <td>0x10c</td>
        <td>const char *pathname</td>
        <td>size_t sz</td>
        <td>struct statfs64*buf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fstatfs64.2.html" target="_blank">fstatfs64</a></td>
        <td>269</td>
        <td>0x10d</td>
        <td>unsigned int fd</td>
        <td>size_t sz</td>
        <td>struct statfs64*buf</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/tgkill.2.html" target="_blank">tgkill</a></td>
        <td>270</td>
        <td>0x10e</td>
        <td>pid_t tgid</td>
        <td>pid_t pid</td>
        <td>int sig</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/utimes.2.html" target="_blank">utimes</a></td>
        <td>271</td>
        <td>0x10f</td>
        <td>char *filename</td>
        <td>struct timeval*utimes</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fadvise64_64.2.html" target="_blank">fadvise64_64</a></td>
        <td>272</td>
        <td>0x110</td>
        <td>loff_t offset loff_t len</td>
        <td>int advice</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/vserver.2.html" target="_blank">vserver</a></td>
        <td>273</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mbind.2.html" target="_blank">mbind</a></td>
        <td>274</td>
        <td>0x112</td>
        <td>unsigned long start</td>
        <td>unsigned long len</td>
        <td>unsigned long mode</td>
        <td>unsigned long *nmask</td>
        <td>unsigned long maxnode</td>
        <td>unsigned flags</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/get_mempolicy.2.html" target="_blank">get_mempolicy</a></td>
        <td>275</td>
        <td>0x113</td>
        <td>int *policy</td>
        <td>unsigned long *nmask</td>
        <td>unsigned long maxnode</td>
        <td>unsigned long addr</td>
        <td>unsigned long flags</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/set_mempolicy.2.html" target="_blank">set_mempolicy</a></td>
        <td>276</td>
        <td>0x114</td>
        <td>int mode</td>
        <td>unsigned long *nmask</td>
        <td>unsigned long maxnode</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mq_open.2.html" target="_blank">mq_open</a></td>
        <td>277</td>
        <td>0x115</td>
        <td>const char *u_name</td>
        <td>int oflag</td>
        <td>umode_t mode</td>
        <td>struct mq_attr*u_attr</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mq_unlink.2.html" target="_blank">mq_unlink</a></td>
        <td>278</td>
        <td>0x116</td>
        <td>const char *u_name</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mq_timedsend.2.html" target="_blank">mq_timedsend</a></td>
        <td>279</td>
        <td>0x117</td>
        <td>mqd_t mqdes</td>
        <td>const char *u_msg_ptr</td>
        <td>size_t msg_len</td>
        <td>unsigned int msg_prio</td>
        <td>conststruct timespec*u_abs_timeout</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mq_timedreceive.2.html" target="_blank">mq_timedreceive</a></td>
        <td>280</td>
        <td>0x118</td>
        <td>mqd_t mqdes</td>
        <td>char *u_msg_ptr</td>
        <td>size_t msg_len</td>
        <td>unsigned int *u_msg_prio</td>
        <td>conststruct timespec*u_abs_timeout</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mq_notify.2.html" target="_blank">mq_notify</a></td>
        <td>281</td>
        <td>0x119</td>
        <td>mqd_t mqdes</td>
        <td>conststruct sigevent*u_notification</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mq_getsetattr.2.html" target="_blank">mq_getsetattr</a></td>
        <td>282</td>
        <td>0x11a</td>
        <td>mqd_t mqdes</td>
        <td>conststruct mq_attr*u_mqstat</td>
        <td>struct mq_attr*u_omqstat</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/kexec_load.2.html" target="_blank">kexec_load</a></td>
        <td>283</td>
        <td>0x11b</td>
        <td>unsigned long entry</td>
        <td>unsigned long nr_segments</td>
        <td>struct kexec_segment*segments</td>
        <td>unsigned long flags</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/waitid.2.html" target="_blank">waitid</a></td>
        <td>284</td>
        <td>0x11c</td>
        <td>int which</td>
        <td>pid_t upid</td>
        <td>struct siginfo*infop</td>
        <td>int options</td>
        <td>struct rusage*ru</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/add_key.2.html" target="_blank">add_key</a></td>
        <td>286</td>
        <td>0x11e</td>
        <td>const char *_type</td>
        <td>const char *_description</td>
        <td>const void *_payload</td>
        <td>size_t plen</td>
        <td>key_serial_t ringid</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/request_key.2.html" target="_blank">request_key</a></td>
        <td>287</td>
        <td>0x11f</td>
        <td>const char *_type</td>
        <td>const char *_description</td>
        <td>const char *_callout_info</td>
        <td>key_serial_t destringid</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/keyctl.2.html" target="_blank">keyctl</a></td>
        <td>288</td>
        <td>0x120</td>
        <td>int option</td>
        <td>unsigned long arg2</td>
        <td>unsigned long arg3</td>
        <td>unsigned long arg4</td>
        <td>unsigned long arg5</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/ioprio_set.2.html" target="_blank">ioprio_set</a></td>
        <td>289</td>
        <td>0x121</td>
        <td>int which</td>
        <td>int who</td>
        <td>int ioprio</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/ioprio_get.2.html" target="_blank">ioprio_get</a></td>
        <td>290</td>
        <td>0x122</td>
        <td>int which</td>
        <td>int who</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/inotify_init.2.html" target="_blank">inotify_init</a></td>
        <td>291</td>
        <td>0x123</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/inotify_add_watch.2.html" target="_blank">inotify_add_watch</a></td>
        <td>292</td>
        <td>0x124</td>
        <td>int fd</td>
        <td>const char *pathname</td>
        <td>u32 mask</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/inotify_rm_watch.2.html" target="_blank">inotify_rm_watch</a></td>
        <td>293</td>
        <td>0x125</td>
        <td>int fd</td>
        <td>__s32 wd</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/migrate_pages.2.html" target="_blank">migrate_pages</a></td>
        <td>294</td>
        <td>0x126</td>
        <td>pid_t pid</td>
        <td>unsigned long maxnode</td>
        <td>const unsigned long *old_nodes</td>
        <td>const unsigned long *new_nodes</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/openat.2.html" target="_blank">openat</a></td>
        <td>295</td>
        <td>0x127</td>
        <td>int dfd</td>
        <td>const char *filename</td>
        <td>int flags</td>
        <td>umode_t mode</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mkdirat.2.html" target="_blank">mkdirat</a></td>
        <td>296</td>
        <td>0x128</td>
        <td>int dfd</td>
        <td>const char *pathname</td>
        <td>umode_t mode</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mknodat.2.html" target="_blank">mknodat</a></td>
        <td>297</td>
        <td>0x129</td>
        <td>int dfd</td>
        <td>const char *filename</td>
        <td>umode_t mode</td>
        <td>unsigned dev</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fchownat.2.html" target="_blank">fchownat</a></td>
        <td>298</td>
        <td>0x12a</td>
        <td>int dfd</td>
        <td>const char *filename</td>
        <td>uid_t user</td>
        <td>gid_t group</td>
        <td>int flag</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/futimesat.2.html" target="_blank">futimesat</a></td>
        <td>299</td>
        <td>0x12b</td>
        <td>int dfd</td>
        <td>const char *filename</td>
        <td>struct timeval*utimes</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fstatat64.2.html" target="_blank">fstatat64</a></td>
        <td>300</td>
        <td>0x12c</td>
        <td>int dfd</td>
        <td>const char *filename</td>
        <td>struct stat64*statbuf</td>
        <td>int flag</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/unlinkat.2.html" target="_blank">unlinkat</a></td>
        <td>301</td>
        <td>0x12d</td>
        <td>int dfd</td>
        <td>const char *pathname</td>
        <td>int flag</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/renameat.2.html" target="_blank">renameat</a></td>
        <td>302</td>
        <td>0x12e</td>
        <td>int olddfd</td>
        <td>const char *oldname</td>
        <td>int newdfd</td>
        <td>const char *newname</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/linkat.2.html" target="_blank">linkat</a></td>
        <td>303</td>
        <td>0x12f</td>
        <td>int olddfd</td>
        <td>const char *oldname</td>
        <td>int newdfd</td>
        <td>const char *newname</td>
        <td>int flags</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/symlinkat.2.html" target="_blank">symlinkat</a></td>
        <td>304</td>
        <td>0x130</td>
        <td>const char *oldname</td>
        <td>int newdfd</td>
        <td>const char *newname</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/readlinkat.2.html" target="_blank">readlinkat</a></td>
        <td>305</td>
        <td>0x131</td>
        <td>int dfd</td>
        <td>const char *pathname</td>
        <td>char *buf</td>
        <td>int bufsiz</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fchmodat.2.html" target="_blank">fchmodat</a></td>
        <td>306</td>
        <td>0x132</td>
        <td>int dfd</td>
        <td>const char *filename</td>
        <td>umode_t mode</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/faccessat.2.html" target="_blank">faccessat</a></td>
        <td>307</td>
        <td>0x133</td>
        <td>int dfd</td>
        <td>const char *filename</td>
        <td>int mode</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/pselect6.2.html" target="_blank">pselect6</a></td>
        <td>308</td>
        <td>0x134</td>
        <td>int n</td>
        <td>fd_set *inp</td>
        <td>fd_set *outp</td>
        <td>fd_set *exp</td>
        <td>struct timespec*tsp</td>
        <td>void *sig</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/ppoll.2.html" target="_blank">ppoll</a></td>
        <td>309</td>
        <td>0x135</td>
        <td>struct pollfd*ufds</td>
        <td>unsigned int nfds</td>
        <td>struct timespec*tsp</td>
        <td>const sigset_t *sigmask</td>
        <td>size_t sigsetsize</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/unshare.2.html" target="_blank">unshare</a></td>
        <td>310</td>
        <td>0x136</td>
        <td>unsigned long unshare_flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/set_robust_list.2.html" target="_blank">set_robust_list</a></td>
        <td>311</td>
        <td>0x137</td>
        <td>struct robust_list_head*head</td>
        <td>size_t len</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/get_robust_list.2.html" target="_blank">get_robust_list</a></td>
        <td>312</td>
        <td>0x138</td>
        <td>int pid</td>
        <td>struct robust_list_head* *head_ptr</td>
        <td>size_t *len_ptr</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/splice.2.html" target="_blank">splice</a></td>
        <td>313</td>
        <td>0x139</td>
        <td>int fd_in</td>
        <td>loff_t *off_in</td>
        <td>int fd_out</td>
        <td>loff_t *off_out</td>
        <td>size_t len</td>
        <td>unsigned int flags</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sync_file_range.2.html" target="_blank">sync_file_range</a></td>
        <td>314</td>
        <td>0x13a</td>
        <td>loff_t offset loff_t nbytes</td>
        <td>unsigned int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/tee.2.html" target="_blank">tee</a></td>
        <td>315</td>
        <td>0x13b</td>
        <td>int fdin</td>
        <td>int fdout</td>
        <td>size_t len</td>
        <td>unsigned int flags</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/vmsplice.2.html" target="_blank">vmsplice</a></td>
        <td>316</td>
        <td>0x13c</td>
        <td>int fd</td>
        <td>conststruct iovec*iov</td>
        <td>unsigned long nr_segs</td>
        <td>unsigned int flags</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/move_pages.2.html" target="_blank">move_pages</a></td>
        <td>317</td>
        <td>0x13d</td>
        <td>pid_t pid</td>
        <td>unsigned long nr_pages</td>
        <td>const void * *pages</td>
        <td>const int *nodes</td>
        <td>int *status</td>
        <td>int flags</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getcpu.2.html" target="_blank">getcpu</a></td>
        <td>318</td>
        <td>0x13e</td>
        <td>unsigned *cpup</td>
        <td>unsigned *nodep</td>
        <td>struct getcpu_cache*unused</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/epoll_pwait.2.html" target="_blank">epoll_pwait</a></td>
        <td>319</td>
        <td>0x13f</td>
        <td>int epfd</td>
        <td>struct epoll_event*events</td>
        <td>int maxevents</td>
        <td>int timeout</td>
        <td>const sigset_t *sigmask</td>
        <td>size_t sigsetsize</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/utimensat.2.html" target="_blank">utimensat</a></td>
        <td>320</td>
        <td>0x140</td>
        <td>int dfd</td>
        <td>const char *filename</td>
        <td>struct timespec*utimes</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/signalfd.2.html" target="_blank">signalfd</a></td>
        <td>321</td>
        <td>0x141</td>
        <td>int ufd</td>
        <td>sigset_t *user_mask</td>
        <td>size_t sizemask</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/timerfd_create.2.html" target="_blank">timerfd_create</a></td>
        <td>322</td>
        <td>0x142</td>
        <td>int clockid</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/eventfd.2.html" target="_blank">eventfd</a></td>
        <td>323</td>
        <td>0x143</td>
        <td>unsigned int count</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fallocate.2.html" target="_blank">fallocate</a></td>
        <td>324</td>
        <td>0x144</td>
        <td>int mode loff_t offset</td>
        <td>loff_t len</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/timerfd_settime.2.html" target="_blank">timerfd_settime</a></td>
        <td>325</td>
        <td>0x145</td>
        <td>int ufd</td>
        <td>int flags</td>
        <td>conststruct itimerspec*utmr</td>
        <td>struct itimerspec*otmr</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/timerfd_gettime.2.html" target="_blank">timerfd_gettime</a></td>
        <td>326</td>
        <td>0x146</td>
        <td>int ufd</td>
        <td>struct itimerspec*otmr</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/signalfd4.2.html" target="_blank">signalfd4</a></td>
        <td>327</td>
        <td>0x147</td>
        <td>int ufd</td>
        <td>sigset_t *user_mask</td>
        <td>size_t sizemask</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/eventfd2.2.html" target="_blank">eventfd2</a></td>
        <td>328</td>
        <td>0x148</td>
        <td>unsigned int count</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/epoll_create1.2.html" target="_blank">epoll_create1</a></td>
        <td>329</td>
        <td>0x149</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/dup3.2.html" target="_blank">dup3</a></td>
        <td>330</td>
        <td>0x14a</td>
        <td>unsigned int oldfd</td>
        <td>unsigned int newfd</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/pipe2.2.html" target="_blank">pipe2</a></td>
        <td>331</td>
        <td>0x14b</td>
        <td>int *fildes</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/inotify_init1.2.html" target="_blank">inotify_init1</a></td>
        <td>332</td>
        <td>0x14c</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/preadv.2.html" target="_blank">preadv</a></td>
        <td>333</td>
        <td>0x14d</td>
        <td>unsigned long fd</td>
        <td>conststruct iovec*vec</td>
        <td>unsigned long vlen</td>
        <td>unsigned long pos_l</td>
        <td>unsigned long pos_h</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/pwritev.2.html" target="_blank">pwritev</a></td>
        <td>334</td>
        <td>0x14e</td>
        <td>unsigned long fd</td>
        <td>conststruct iovec*vec</td>
        <td>unsigned long vlen</td>
        <td>unsigned long pos_l</td>
        <td>unsigned long pos_h</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/rt_tgsigqueueinfo.2.html" target="_blank">rt_tgsigqueueinfo</a></td>
        <td>335</td>
        <td>0x14f</td>
        <td>pid_t tgid</td>
        <td>pid_t pid</td>
        <td>int sig</td>
        <td>siginfo_t *uinfo</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/perf_event_open.2.html" target="_blank">perf_event_open</a></td>
        <td>336</td>
        <td>0x150</td>
        <td>struct perf_event_attr*attr_uptr</td>
        <td>pid_t pid</td>
        <td>int cpu</td>
        <td>int group_fd</td>
        <td>unsigned long flags</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/recvmmsg.2.html" target="_blank">recvmmsg</a></td>
        <td>337</td>
        <td>0x151</td>
        <td>int fd</td>
        <td>struct mmsghdr*mmsg</td>
        <td>unsigned int vlen</td>
        <td>unsigned int flags</td>
        <td>struct timespec*timeout</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fanotify_init.2.html" target="_blank">fanotify_init</a></td>
        <td>338</td>
        <td>0x152</td>
        <td>unsigned int flags</td>
        <td>unsigned int event_f_flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/fanotify_mark.2.html" target="_blank">fanotify_mark</a></td>
        <td>339</td>
        <td>0x153</td>
        <td>unsigned int flags __u64 mask</td>
        <td>int dfd const char *pathname</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/prlimit64.2.html" target="_blank">prlimit64</a></td>
        <td>340</td>
        <td>0x154</td>
        <td>pid_t pid</td>
        <td>unsigned int resource</td>
        <td>conststruct rlimit64*new_rlim</td>
        <td>struct rlimit64*old_rlim</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/name_to_handle_at.2.html" target="_blank">name_to_handle_at</a></td>
        <td>341</td>
        <td>0x155</td>
        <td>int dfd</td>
        <td>const char *name</td>
        <td>struct file_handle*handle</td>
        <td>int *mnt_id</td>
        <td>int flag</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/open_by_handle_at.2.html" target="_blank">open_by_handle_at</a></td>
        <td>342</td>
        <td>0x156</td>
        <td>int mountdirfd</td>
        <td>struct file_handle*handle</td>
        <td>int flags</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/clock_adjtime.2.html" target="_blank">clock_adjtime</a></td>
        <td>343</td>
        <td>0x157</td>
        <td>const clockid_t which_clock</td>
        <td>struct timex*utx</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/syncfs.2.html" target="_blank">syncfs</a></td>
        <td>344</td>
        <td>0x158</td>
        <td>int fd</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sendmmsg.2.html" target="_blank">sendmmsg</a></td>
        <td>345</td>
        <td>0x159</td>
        <td>int fd</td>
        <td>struct mmsghdr*mmsg</td>
        <td>unsigned int vlen</td>
        <td>unsigned int flags</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/setns.2.html" target="_blank">setns</a></td>
        <td>346</td>
        <td>0x15a</td>
        <td>int fd</td>
        <td>int nstype</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/process_vm_readv.2.html" target="_blank">process_vm_readv</a></td>
        <td>347</td>
        <td>0x15b</td>
        <td>pid_t pid</td>
        <td>conststruct iovec*lvec</td>
        <td>unsigned long liovcnt</td>
        <td>conststruct iovec*rvec</td>
        <td>unsigned long riovcnt</td>
        <td>unsigned long flags</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/process_vm_writev.2.html" target="_blank">process_vm_writev</a></td>
        <td>348</td>
        <td>0x15c</td>
        <td>pid_t pid</td>
        <td>conststruct iovec*lvec</td>
        <td>unsigned long liovcnt</td>
        <td>conststruct iovec*rvec</td>
        <td>unsigned long riovcnt</td>
        <td>unsigned long flags</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/kcmp.2.html" target="_blank">kcmp</a></td>
        <td>349</td>
        <td>0x15d</td>
        <td>pid_t pid1</td>
        <td>pid_t pid2</td>
        <td>int type</td>
        <td>unsigned long idx1</td>
        <td>unsigned long idx2</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/finit_module.2.html" target="_blank">finit_module</a></td>
        <td>350</td>
        <td>0x15e</td>
        <td>int fd</td>
        <td>const char *uargs</td>
        <td>int flags</td>
        <td>-</td><td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sched_setattr.2.html" target="_blank">sched_setattr</a></td>
        <td>351</td>
        <td>0x15f</td>
        <td>pid_t pid</td>
        <td>struct sched_attr *attr</td>
        <td>unsigned int flags</td>
        <td>-</td><td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/sched_getattr.2.html" target="_blank">sched_getattr</a></td>
        <td>352</td>
        <td>0x160</td>
        <td>pid_t pid</td>
        <td>struct sched_attr *attr</td>
        <td>unsigned int size</td>
        <td>unsigned int flags</td>
        <td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/renameat2.2.html" target="_blank">renameat2</a></td>
        <td>353</td>
        <td>0x161</td>
        <td>int olddirfd</td>
        <td>const char *oldpath</td>
        <td>int newdirfd</td>
        <td>const char *newpath</td>
        <td>unsigned int flags</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/seccomp.2.html" target="_blank">seccomp</a></td>
        <td>354</td>
        <td>0x162</td>
        <td>unsigned int op</td>
        <td>unsigned int flags</td>
        <td>void *uargs</td>
        <td>-</td><td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/getrandom.2.html" target="_blank">getrandom</a></td>
        <td>355</td>
        <td>0x163</td>
        <td>void *buf</td>
        <td>size_t buflen</td>
        <td>unsigned int flags</td>
        <td>-</td><td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/memfd_create.2.html" target="_blank">memfd_create</a></td>
        <td>356</td>
        <td>0x164</td>
        <td>const char *name</td>
        <td>unsigned int flags</td>
        <td>-</td><td>-</td><td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/kexec_file_load.2.html" target="_blank">kexec_file_load</a></td>
        <td>357</td>
        <td>0x165</td>
        <td>int kernel_fd</td>
        <td>int initrd_fd</td>
        <td>unsigned long cmdline_len</td>
        <td>const char *cmdline_ptr</td>
        <td>unsigned long flags</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/bpf.2.html" target="_blank">bpf</a></td>
        <td>358</td>
        <td>0x166</td>
        <td>int cmd</td>
        <td>union bpf_attr *attr</td>
        <td>unsigned int size</td>
        <td>-</td><td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/execveat.2.html" target="_blank">execveat</a></td>
        <td>359</td>
        <td>0x167</td>
        <td>int dirfd</td>
        <td>const char *pathname</td>
        <td>const char *const argv[]</td>
        <td>const char *const envp[]</td>
        <td>int flags</td>
        <td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/userfaultfd.2.html" target="_blank">userfaultfd</a></td>
        <td>360</td>
        <td>0x168</td>
        <td>int flags</td>
        <td>-</td><td>-</td><td>-</td><td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/membarrier.2.html" target="_blank">membarrier</a></td>
        <td>361</td>
        <td>0x169</td>
        <td>int cmd</td>
        <td>int flags</td>
        <td>-</td><td>-</td><td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/mlock2.2.html" target="_blank">mlock2</a></td>
        <td>362</td>
        <td>0x16a</td>
        <td>const void *addr</td>
        <td>size_t len</td>
        <td>int flags</td>
        <td>-</td><td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/copy_file_range.2.html" target="_blank">copy_file_range</a></td>
        <td>363</td>
        <td>0x16b</td>
        <td>int fd_in</td>
        <td>loff_t *off_in</td>
        <td>int fd_out</td>
        <td>loff_t *off_out</td>
        <td>size_t len</td>
        <td>unsigned int flags</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/preadv2.2.html" target="_blank">preadv2</a></td>
        <td>364</td>
        <td>0x16c</td>
        <td>int fd</td>
        <td>const struct iovec *iov</td>
        <td>int iovcnt</td>
        <td>long pos_l</td>
        <td>long pos_h</td>
        <td>int flags</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/pwritev2.2.html" target="_blank">pwritev2</a></td>
        <td>365</td>
        <td>0x16d</td>
        <td>int fd</td>
        <td>const struct iovec *iov</td>
        <td>int iovcnt</td>
        <td>long pos_l</td>
        <td>long pos_h</td>
        <td>int flags</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/pkey_mprotect.2.html" target="_blank">pkey_mprotect</a></td>
        <td>366</td>
        <td>0x16e</td>
        <td>void *addr</td>
        <td>size_t len</td>
        <td>int prot</td>
        <td>int pkey</td>
        <td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/pkey_alloc.2.html" target="_blank">pkey_alloc</a></td>
        <td>367</td>
        <td>0x16f</td>
        <td>unsigned long flags</td>
        <td>unsigned long init_val</td>
        <td>-</td><td>-</td><td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/pkey_free.2.html" target="_blank">pkey_free</a></td>
        <td>368</td>
        <td>0x170</td>
        <td>int pkey</td>
        <td>-</td><td>-</td><td>-</td><td>-</td><td>-</td>
      </tr>
      <tr>
      <td><a href="https://man7.org/linux/man-pages/man2/statx.2.html" target="_blank">statx</a></td>
        <td>383</td>
        <td>0x17f</td>
        <td>int dirfd</td>
        <td>const char *pathname</td>
        <td>int flags</td>
        <td>unsigned int mask</td>
        <td>struct statx *statxbuf</td>
        <td>-</td>
      </tr>
    </tbody>
  </table>

  <script>
    function filterTable() {
      var input = document.getElementById("searchBox");
      var filter = input.value.toLowerCase();
      var table = document.getElementById("syscallTable");
      var trs = table.getElementsByTagName("tr");
      for (var i = 1; i < trs.length; i++) {
        var tds = trs[i].getElementsByTagName("td");
        if (tds.length > 0) {
          var syscallName = tds[0].textContent || tds[0].innerText;
          if (syscallName.toLowerCase().indexOf(filter) > -1) {
            trs[i].style.display = "";
          } else {
            trs[i].style.display = "none";
          }
        }
      }
    }
  </script>
</body>
