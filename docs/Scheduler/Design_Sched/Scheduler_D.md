- theory Scheduler_D
- imports ["../../Base_Ops/Design_BsOps/ReadyQue_D"](https://zf-zhangfeng.github.io/ARINC653P1-5Conformity/Base_Ops/Design_BsOps/ReadyQue_D)

<html>
<head>
<title>Scheduler_D.thy</title>
</head>
<body bgcolor="#ffffff">



<pre><font color="#333333"><span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   1 </font></span><font color="#cc0000">(*</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   2 </font></span><font color="#cc0000">The two-level Scheduler for ARINC 653 P1-5 is designed and created by </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   3 </font></span><font color="#cc0000">  ZHANG Feng from Nanyang Technological University.</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   4 </font></span><font color="#cc0000">*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">   5 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   6 </font></span><font color="#006699"><strong>section</strong></font><font color="#6600cc">&lsaquo;</font><font color="#6600cc">A</font><font color="#6600cc"> </font><font color="#6600cc">T</font><font color="#6600cc">w</font><font color="#6600cc">o</font><font color="#6600cc">-</font><font color="#6600cc">L</font><font color="#6600cc">e</font><font color="#6600cc">v</font><font color="#6600cc">e</font><font color="#6600cc">l</font><font color="#6600cc"> </font><font color="#6600cc">S</font><font color="#6600cc">c</font><font color="#6600cc">h</font><font color="#6600cc">e</font><font color="#6600cc">d</font><font color="#6600cc">u</font><font color="#6600cc">l</font><font color="#6600cc">e</font><font color="#6600cc">r</font><font color="#6600cc">&rsaquo;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   7 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   8 </font></span><font color="#006699"><strong>theory</strong></font> Scheduler_D
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   9 </font></span><font color="#009966"><strong>imports</strong></font> <font color="#ff00cc">&quot;../../Base_Ops/Design_BsOps/ReadyQue_D&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  10 </font></span><font color="#009966"><strong>begin</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  11 </font></span>                  
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  12 </font></span><font color="#cc0000">(*1. if the cur process is HM, then it's state will not be ready, only running or stop</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  13 </font></span><font color="#cc0000">  2. if the cur process is not HM, it may be ready</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  14 </font></span><font color="#cc0000">  3. In NORMAL mode,  MAIN and INIT process are STOPED</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  15 </font></span><font color="#cc0000">  4.*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  16 </font></span><font color="#cc0000">(*input:  1. readyQue</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  17 </font></span><font color="#cc0000">          2. part id</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  18 </font></span><font color="#cc0000">          3. created</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  19 </font></span><font color="#cc0000">          4. curMode</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  20 </font></span><font color="#cc0000">          5. procSta</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  21 </font></span><font color="#cc0000">          6. lockLvl</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  22 </font></span><font color="#cc0000">          7. curProc</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  23 </font></span><font color="#cc0000">          8. curPriority</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  24 </font></span><font color="#cc0000">          9. deadline</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  25 </font></span><font color="#cc0000">         10. timer</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  26 </font></span><font color="#cc0000">output: 1. readyQue 2. procSta 3. selected process ID</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  27 </font></span><font color="#cc0000">*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  28 </font></span><font color="#cc0000">(*</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  29 </font></span><font color="#cc0000">      setCurRun2Faulted = setProcSta' f_procSta curProc_id FAULTED;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  30 </font></span><font color="#cc0000">      readyQDelCurRun = readyDel_D f_readyQ part_id curProc_priority curProc_id;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  31 </font></span><font color="#cc0000">      setHMReady = setProcSta' setCurRun2Faulted HM_PROC_ID READY;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  32 </font></span><font color="#cc0000">      addHM2ReadyQ = readyAddHead_D readyQDelCurRun part_id 1 HM_PROC_ID*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  33 </font></span><font color="#006699"><strong>function</strong></font> schedProc_D <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  34 </font></span>  <font color="#ff00cc">&quot;(PART_ID_TYPE &#8640; (PROC_ID_TYPE list list)) &rArr; PART_ID_TYPE &rArr; </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  35 </font></span><font color="#ff00cc">      (PART_ID_TYPE &times; PROC_ID_TYPE &#8640; PROC_ATTR_TYPE) &rArr; </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  36 </font></span><font color="#ff00cc">      (PART_ID_TYPE &#8640; OPERATING_MODE_TYPE) &rArr; (PART_ID_TYPE &times; PROC_ID_TYPE &#8640; PROC_STATE_TYPE) &rArr;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  37 </font></span><font color="#ff00cc">      (PART_ID_TYPE &#8640; LOCK_LVL_TYPE) &rArr; (PART_ID_TYPE &#8640; PROC_ID_TYPE)&rArr;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  38 </font></span><font color="#ff00cc">      (PART_ID_TYPE &times; PROC_ID_TYPE &#8640; DDL_TIME_TYPE) &rArr; TIMER_TYPE &rArr;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  39 </font></span><font color="#ff00cc">      (PART_ID_TYPE &times; PROC_ID_TYPE &#8640; PROC_STATE_TYPE) &times; PROC_ID_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  40 </font></span>  <font color="#ff00cc">&quot;schedProc_D f_readyQ part_id f_createdProcs f_curMode f_procSta f_lockLvl f_curProc f_deadline tk =</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  41 </font></span><font color="#ff00cc">  (let curPartMode = the(f_curMode part_id); curProc_id = the(f_curProc part_id);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  42 </font></span><font color="#ff00cc">      ddl_curProc_id = case f_deadline (part_id, curProc_id) of Some t &rArr; t;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  43 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  44 </font></span><font color="#ff00cc">      setCurRun2Ready = (case f_procSta (part_id, curProc_id) of </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  45 </font></span><font color="#ff00cc">        Some RUNNING &rArr; setProcSta' f_procSta (part_id, curProc_id) READY |</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  46 </font></span><font color="#ff00cc">        _ &rArr; f_procSta);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  47 </font></span><font color="#ff00cc">      setHMReady = setProcSta' setCurRun2Ready (part_id, HM_PROC_ID) READY;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  48 </font></span><font color="#ff00cc">      addHM2ReadyQ = readyAddHead_D f_readyQ part_id 1 HM_PROC_ID in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  49 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  50 </font></span><font color="#ff00cc">    (if curPartMode = COLD_START &or; curPartMode = WARM_START then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  51 </font></span><font color="#ff00cc">        (if ENABLE_HM &and; f_createdProcs (part_id, HM_PROC_ID) &ne; None &and; </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  52 </font></span><font color="#ff00cc">              setCurRun2Ready (part_id, HM_PROC_ID) = Some READY then </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  53 </font></span><font color="#ff00cc">            (setCurRun2Ready, HM_PROC_ID)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  54 </font></span><font color="#ff00cc">         else if setCurRun2Ready (part_id, MAIN_PROC_ID) = Some READY then </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  55 </font></span><font color="#ff00cc">                 (setCurRun2Ready, MAIN_PROC_ID)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  56 </font></span><font color="#ff00cc">         else (setCurRun2Ready, IDLE_PROC_ID)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  57 </font></span><font color="#ff00cc">        )</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  58 </font></span><font color="#ff00cc">    else if curPartMode = NORMAL then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  59 </font></span><font color="#ff00cc">        (if ENABLE_HM &and; f_createdProcs (part_id, HM_PROC_ID) &ne; None &and; </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  60 </font></span><font color="#ff00cc">              setCurRun2Ready (part_id, HM_PROC_ID) = Some READY then </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  61 </font></span><font color="#ff00cc">            (setCurRun2Ready, HM_PROC_ID)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  62 </font></span><font color="#ff00cc">        else if the(f_lockLvl part_id) &gt; 0 then (setCurRun2Ready, curProc_id)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  63 </font></span><font color="#ff00cc">        else if curProc_id &ne; IDLE_PROC_ID &and; </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  64 </font></span><font color="#ff00cc">                PROC_ATTR_TYPE.TIME_CAPACITY(the(f_createdProcs (part_id, curProc_id))) &ne; -1 &and; </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  65 </font></span><font color="#ff00cc">                Int.nat ddl_curProc_id &le; tk then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  66 </font></span><font color="#ff00cc">                (if ENABLE_HM &and; f_createdProcs (part_id, HM_PROC_ID) &ne; None then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  67 </font></span><font color="#ff00cc">                    schedProc_D addHM2ReadyQ part_id f_createdProcs f_curMode setHMReady f_lockLvl f_curProc  f_deadline tk</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  68 </font></span><font color="#ff00cc">                else (setCurRun2Ready, schedAlgRT_D f_readyQ part_id)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  69 </font></span><font color="#ff00cc">                )</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  70 </font></span><font color="#ff00cc">        else </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  71 </font></span><font color="#ff00cc">          (setCurRun2Ready, schedAlgRT_D f_readyQ part_id)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  72 </font></span><font color="#ff00cc">        )</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  73 </font></span><font color="#ff00cc">    else (f_procSta, IDLE_PROC_ID)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  74 </font></span><font color="#ff00cc">    )  </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  75 </font></span><font color="#ff00cc">  )&quot;</font> <font color="#006699"><strong>by</strong></font> auto
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  76 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  77 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  78 </font></span><font color="#006699"><strong>definition</strong></font> schedule_D <font color="#000000"><strong>::</strong></font> <font color="#ff00cc">&quot;'a StaProc_sched_scheme &rArr; V_CORE_ID_TYPE &rArr; 'a StaProc_sched_scheme&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  79 </font></span>  <font color="#ff00cc">&quot;schedule_D s core_id =</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  80 </font></span><font color="#ff00cc">  (let t = timer_' s; f_readyQue = readyQue (partitions_' s); newPartID = schedPart core_id (int t);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  81 </font></span><font color="#ff00cc">      f_createdProcs = createdProcs (procs_' s); f_curMode = curMode (partitions_' s);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  82 </font></span><font color="#ff00cc">      f_procSta = procSta (procs_' s); </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  83 </font></span><font color="#ff00cc">      f_lockLvl = lockLvl (partitions_' s); f_curProc = curProc (partitions_' s); </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  84 </font></span><font color="#ff00cc">      f_deadline = deadline (procs_' s);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  85 </font></span><font color="#ff00cc">      sched_proc_newPart = schedProc_D f_readyQue newPartID f_createdProcs f_curMode f_procSta </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  86 </font></span><font color="#ff00cc">                              f_lockLvl f_curProc f_deadline t in </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  87 </font></span><font color="#ff00cc">  s&#10631;partitions_' := (partitions_' s)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  88 </font></span><font color="#ff00cc">      &#10631;curPart := (curPart(partitions_' s))(core_id := Some newPartID),</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  89 </font></span><font color="#ff00cc">      curProc := (curProc (partitions_' s))(newPartID := Some (snd sched_proc_newPart))&#10632;,</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  90 </font></span><font color="#ff00cc">    procs_' := (procs_' s)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  91 </font></span><font color="#ff00cc">      &#10631;procSta := setProcSta' (fst sched_proc_newPart) (newPartID, (snd sched_proc_newPart)) RUNNING&#10632;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  92 </font></span><font color="#ff00cc">    &#10632;)&quot;</font>  
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  93 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  94 </font></span>  
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  95 </font></span><font color="#009966"><strong>end</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  96 </font></span>
</font></pre>
</body>
</html>
