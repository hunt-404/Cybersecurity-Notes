___
**Stage 1 — HTB fundamentals, weeks 1–4**

Start with Starting Point. HTB itself describes Starting Point as a linear progression from basic service interaction through full foothold + privilege-escalation machines.

1. Meow
2. Fawn
3. Dancing
4. Redeemer
5. Explosion
6. Preignition
7. Mongod
8. Synced
9. Appointment
10. Sequel
11. Crocodile
12. Responder
13. Three
14. Ignition
15. Bike
16. Funnel
17. Pennyworth
18. Tactics
19. Archetype
20. Oopsie
21. Vaccine
22. Unified
23. Included
24. Markup
25. Base

Do not worry that these are too easy. Your objective is to learn what FTP, SMB, Redis, MongoDB, SQL, HTTP, WinRM, SSH and similar services actually look like during enumeration.

For these 25 only, using the provided educational material is fine.

---

**Stage 2 — classic enumeration and privilege-escalation machines, weeks 5–12**

Now stop following walkthroughs from the beginning.

26. Lame
27. Legacy
28. Blue
29. Devel
30. Jerry
31. Nibbles
32. Bashed
33. Shocker
34. Optimum
35. Beep
36. Sense
37. SolidState
38. Valentine
39. Sunday
40. Poison
41. Bastard

These are older machines, so some vulnerabilities are dated. That is intentional. They force you to learn the basic rhythm of enumeration → vulnerability identification → foothold → privilege escalation before the attack paths become complicated. HTB's catalog still contains these retired machines, including Lame, Legacy, Devel, Optimum, Beep, Bastard, Arctic, Granny and others.

Don't spend months here. They are fundamentals, not a model of a modern enterprise.

---

**Stage 3 — modern Linux / web-heavy OSCP preparation, weeks 13–24**

Now move into machines from the current TJ Null / NetSecFocus PWK V3 preparation list. The current sheet was updated July 18, 2026.

42. Busqueda
43. Sau
44. Soccer
45. Keeper
46. BoardLight
47. Networked
48. CozyHosting
49. Editorial
50. Help
51. Broker
52. Magic
53. Pandora
54. Monitored
55. Builder
56. LinkVortex
57. Dog
58. Usage
59. UpDown
60. Intentions
61. Titanic
62. Browsed

This is where you should become much better at web enumeration, source-code discovery, vhosts, credentials, file disclosure, command injection, SSRF, application logic, SSH keys, Linux permissions, sudo/SUID mistakes and service misconfiguration.

For example, Sau specifically exercises SSRF → command execution → Linux privilege escalation, while UpDown chains web/source-code enumeration, PHP behavior and privilege escalation.

By machine 60, `nmap → enumerate → find attack surface → obtain shell → enumerate local system → privilege escalate` should feel like a routine rather than a collection of commands.

---

**Stage 4 — Windows, weeks 25–32**

63. ServMon
64. Access
65. Support
66. Jeeves
67. Manager
68. Mailing
69. Heist
70. StreamIO
71. Intelligence
72. Aero
73. Administrator
74. Certified

Pay particular attention to SMB, WinRM, Windows services, PowerShell, credentials, NTLM, scheduled tasks, registry configuration, service permissions, local privilege escalation and file/share enumeration.

Do not treat Windows as “Linux with different commands.” Learn Windows administration while you attack Windows.

By this point I would also start PEN-200 and follow OffSec's 24-week curriculum in parallel.

---

**Stage 5 — Active Directory, weeks 33–40**

This phase is extremely important for the present OSCP+.

75. Active
76. Forest
77. Sauna
78. Return
79. Timelapse
80. Cicada
81. Escape
82. Monteverde
83. Cascade
84. Blackfield
85. Flight
86. TheFrizz
87. Fluffy
88. Puppy
89. Voleur
90. Signed
91. Eighteen
92. Tombwatcher