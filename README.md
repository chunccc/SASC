Suspend-aware Segment Cleaning 
==============================
* Maintainer: Seungyong Cheon (chuncsy@gmail.com)
* Reference: Dongil Park, Seungyong Cheon, Youjip Won, "Suspend-aware Segment Cleaning in Log-structured File System",  In Proceedings of USENIX HotStorage'15, Santa Clara, USA, July 6-7, 2015
* http://esos.hanyang.ac.kr/files/publication/conferences/international/sasc.pdf

------------------------------
Suspend-aware Segment Cleaning is advanced segment cleaning policy of Log-structured File System in Android platform device.
Traditional background segment cleaning scheme in Log-structured File System is periodically activated (e.g. 30 sec.). It consolidates one free segment per each execution, and only execueted when the storage device is idle. On the other hand, most of the mobile devices have suspend feature to save battery. After LCD screen turned off, the device is entered to the suspend mode in a few second (1.8 sec. on the average). During the suspend mode, all tasks are frozen, including segment cleaning thread. Therefore, there's no chance to do segment cleaning if excessive I/O request and suspend are repeated.
SASC awares the screen-off signal and then adjust segment cleaning period before entering suspend mode. It uses *Virtual Segment Cleaning* to improve segment cleaning speed and *Utilization-based Segment Cleaning* to adjust segment cleaning period from 300ms to 900ms by the storage utilization. SASC consilidates x6 more free segments than stock segment cleaning does, and could prevent foreground segment cleaning which cause excessive I/O latency.
