- theory Time
- imports  ["../Scheduler/Design_Sched/Scheduler_D"](https://zf-zhangfeng.github.io/ARINC653P1-5Conformity/Scheduler/Design_Sched/Scheduler_D)
           ["../Base_Ops/Design_BsOps/ProcessAct_D"](https://zf-zhangfeng.github.io/ARINC653P1-5Conformity/Base_Ops/Design_BsOps/ProcessAct_D)

<html>
<head>
<title>Time.thy</title>
</head>
<body bgcolor="#ffffff">



<pre><font color="#333333"><span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   1 </font></span><font color="#cc0000">(*</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   2 </font></span><font color="#cc0000">The Timing services for ARINC 653 P1-5 are first created by </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   3 </font></span><font color="#cc0000">  GAO Lijie from Zhejiang University.</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   4 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">   5 </font></span><font color="#cc0000">And this part is reviewed and corrected by ZHANG Feng from Nanyang Technological University.</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   6 </font></span><font color="#cc0000">*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   7 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   8 </font></span><font color="#006699"><strong>section</strong></font><font color="#6600cc">&lsaquo;</font><font color="#6600cc">S</font><font color="#6600cc">e</font><font color="#6600cc">r</font><font color="#6600cc">v</font><font color="#6600cc">i</font><font color="#6600cc">c</font><font color="#6600cc">e</font><font color="#6600cc">s</font><font color="#6600cc"> </font><font color="#6600cc">o</font><font color="#6600cc">f</font><font color="#6600cc"> </font><font color="#6600cc">T</font><font color="#6600cc">i</font><font color="#6600cc">m</font><font color="#6600cc">i</font><font color="#6600cc">n</font><font color="#6600cc">g</font><font color="#6600cc"> </font><font color="#6600cc">M</font><font color="#6600cc">a</font><font color="#6600cc">n</font><font color="#6600cc">a</font><font color="#6600cc">g</font><font color="#6600cc">e</font><font color="#6600cc">m</font><font color="#6600cc">e</font><font color="#6600cc">n</font><font color="#6600cc">t</font><font color="#6600cc">&rsaquo;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   9 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  10 </font></span><font color="#006699"><strong>theory</strong></font> Time
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  11 </font></span><font color="#009966"><strong>imports</strong></font>  <font color="#ff00cc">&quot;../Scheduler/Design_Sched/Scheduler_D&quot;</font> <font color="#ff00cc">&quot;../Base_Ops/Design_BsOps/ProcessAct_D&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  12 </font></span><font color="#009966"><strong>begin</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  13 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  14 </font></span><font color="#cc0000">(*=== TIMED_WAIT service ===*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  15 </font></span><font color="#006699"><strong>definition</strong></font> TIMED_WAIT <font color="#000000"><strong>::</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  16 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; V_CORE_ID_TYPE &rArr; PROC_ID_TYPE &rArr; DELAY_TIME_TYPE &rArr; 'a STATE_scheme &times; RETURN_CODE_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  17 </font></span>  <font color="#ff00cc">&quot;TIMED_WAIT s core_id proc_id delay_time &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  18 </font></span><font color="#ff00cc">    let</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  19 </font></span><font color="#ff00cc">      cur_part_id = curPartID s core_id; cur_proc_id = curProcID_Core s core_id</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  20 </font></span><font color="#ff00cc">    in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  21 </font></span><font color="#ff00cc">       if (the cur_proc_id) = HM_PROC_ID &or; getProcUserPLMtx s (the cur_part_id, the cur_proc_id) &ne; None then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  22 </font></span><font color="#ff00cc">         (s , INVALID_MODE)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  23 </font></span><font color="#ff00cc">       else if delay_time &ge; INVALID_TIMEOUT_VALUE &or; Int.int delay_time = INFINITE_TIMEOUT then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  24 </font></span><font color="#ff00cc">          (s, INVALID_PARAM)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  25 </font></span><font color="#ff00cc">       else</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  26 </font></span><font color="#ff00cc">          if delay_time = 0 then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  27 </font></span><font color="#ff00cc">            let</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  28 </font></span><font color="#ff00cc">                s_alone = procAlone s (the cur_part_id, the cur_proc_id);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  29 </font></span><font color="#ff00cc">                s_setAddWaitDiff = readyAdd s_alone (the cur_part_id, the cur_proc_id)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  30 </font></span><font color="#ff00cc">            in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  31 </font></span><font color="#ff00cc">               (schedule_Abs s_setAddWaitDiff core_id, NO_ERROR)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  32 </font></span><font color="#ff00cc">          else </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  33 </font></span><font color="#ff00cc">            let</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  34 </font></span><font color="#ff00cc">               s_alone = procAlone s (the cur_part_id, the cur_proc_id);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  35 </font></span><font color="#ff00cc">               set_cur_proc_sta = setCurProcSta s core_id WAITING;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  36 </font></span><font color="#ff00cc">               s_addTimeQ = setAddWaitDiff set_cur_proc_sta  (the cur_part_id, the cur_proc_id) (Int.int delay_time)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  37 </font></span><font color="#ff00cc">            in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  38 </font></span><font color="#ff00cc">              (schedule_Abs s_addTimeQ core_id, NO_ERROR)&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  39 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  40 </font></span><font color="#cc0000">(*=== PERIODIC_WAIT service ===*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  41 </font></span><font color="#006699"><strong>definition</strong></font> PERIODIC_WAIT <font color="#000000"><strong>::</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  42 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; V_CORE_ID_TYPE &rArr; 'a STATE_scheme &times; RETURN_CODE_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  43 </font></span>  <font color="#ff00cc">&quot;PERIODIC_WAIT s core_id &equiv; periodic_wait s core_id&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  44 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  45 </font></span><font color="#cc0000">(*=== GET_TIME service ===*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  46 </font></span><font color="#006699"><strong>definition</strong></font> GET_TIME <font color="#000000"><strong>::</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  47 </font></span>  <font color="#ff00cc">&quot;'a StaPart_Sched_scheme &rArr; SYSTEM_TIME_TYPE &times; RETURN_CODE_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  48 </font></span>  <font color="#ff00cc">&quot;GET_TIME s &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  49 </font></span><font color="#ff00cc">   let</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  50 </font></span><font color="#ff00cc">      system_clock = Int.int(sysClk s)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  51 </font></span><font color="#ff00cc">    in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  52 </font></span><font color="#ff00cc">      (system_clock, NO_ERROR)&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  53 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  54 </font></span><font color="#cc0000">(*=== REPLENISH service ===*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  55 </font></span><font color="#006699"><strong>definition</strong></font> REPLENISH <font color="#000000"><strong>::</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  56 </font></span>  <font color="#ff00cc">&quot;'a StaProc_scheme &rArr; V_CORE_ID_TYPE &rArr; BUDGET_TIME &rArr; 'a StaProc_scheme &times; RETURN_CODE_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  57 </font></span>  <font color="#ff00cc">&quot;REPLENISH s core_id budget_time &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  58 </font></span><font color="#ff00cc">   let </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  59 </font></span><font color="#ff00cc">      cur_part_id = curPartID s core_id; cur_proc_id = curProcID_Core s core_id;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  60 </font></span><font color="#ff00cc">      curRlsp = the(getRlsPoint s (the cur_part_id, the cur_proc_id)); </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  61 </font></span><font color="#ff00cc">      curDdl = the(getProcDDL s (the cur_part_id, the cur_proc_id));</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  62 </font></span><font color="#ff00cc">      proc_attr = the(getProcAttr s (the cur_part_id, the cur_proc_id)); </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  63 </font></span><font color="#ff00cc">      proc_period = PROC_ATTR_TYPE.PERIOD proc_attr;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  64 </font></span><font color="#ff00cc">      nextRlsp = curRlsp + proc_period; newDdl = curRlsp + budget_time  in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  65 </font></span><font color="#ff00cc">    if (the cur_proc_id) = HM_PROC_ID &or; the(getPartMode s (the cur_part_id)) &ne; NORMAL &or; curDdl = -1 then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  66 </font></span><font color="#ff00cc">      (s, NO_ACTION)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  67 </font></span><font color="#ff00cc">    else if isPeriodic s (the cur_part_id, the cur_proc_id) &and; (newDdl &ge; nextRlsp &or; budget_time = -1) then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  68 </font></span><font color="#ff00cc">      (s, INVALID_MODE)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  69 </font></span><font color="#ff00cc">    else if Int.nat budget_time &ge; INVALID_TIMEOUT_VALUE then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  70 </font></span><font color="#ff00cc">      (s, INVALID_PARAM)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  71 </font></span><font color="#ff00cc">    else </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  72 </font></span><font color="#ff00cc">      if budget_time = -1 then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  73 </font></span><font color="#ff00cc">          (setProcDDL s (the cur_part_id, the cur_proc_id) (-1), NO_ERROR)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  74 </font></span><font color="#ff00cc">      else </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  75 </font></span><font color="#ff00cc">          (setProcDDL s (the cur_part_id, the cur_proc_id) newDdl, NO_ERROR)&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  76 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  77 </font></span><font color="#006699"><strong>definition</strong></font> GET_PROCESS_ID <font color="#000000"><strong>::</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  78 </font></span>  <font color="#ff00cc">&quot;'a StaProc_sched_scheme &rArr; V_CORE_ID_TYPE &rArr; PROC_NAME_TYPE &rArr; 'a StaProc_sched_scheme &times; PROC_ID_TYPE option &times; RETURN_CODE_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  79 </font></span>  <font color="#ff00cc">&quot;GET_PROCESS_ID s core_id proc_name  =</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  80 </font></span><font color="#ff00cc">  (let </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  81 </font></span><font color="#ff00cc">      cur_part_id = curPartID s core_id;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  82 </font></span><font color="#ff00cc">      curProcID = getProcIDByName s (the cur_part_id) proc_name in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  83 </font></span><font color="#ff00cc">  if curProcID = None then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  84 </font></span><font color="#ff00cc">     (s ,None, INVALID_CONFIG)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  85 </font></span><font color="#ff00cc">  else (s, curProcID, NO_ERROR))&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  86 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  87 </font></span><font color="#009966"><strong>end</strong></font>
</font></pre>
</body>
</html>
