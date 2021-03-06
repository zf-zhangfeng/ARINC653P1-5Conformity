- theory Time
- imports  ["../Scheduler/Design_Sched/Scheduler_D"](https://zf-zhangfeng.github.io/ARINC653P1-5Conformity/Scheduler/Design_Sched/Scheduler_D)
           ["../Base_Ops/Design_BsOps/ProcessAct_D"](https://zf-zhangfeng.github.io/ARINC653P1-5Conformity/Base_Ops/Design_BsOps/ProcessAct_D)
           ["./Process"](https://zf-zhangfeng.github.io/ARINC653P1-5Conformity/Services_R/Process)

<html>
<head>
<title>HealthMonitor.thy</title>
</head>
<body bgcolor="#ffffff">



<pre><font color="#333333"><span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   1 </font></span><font color="#cc0000">(*</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   2 </font></span><font color="#cc0000">The Health Monitoring services for ARINC 653 P1-5 are first created by </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   3 </font></span><font color="#cc0000">  LIANG Jin from Beihang University and ZHANG Feng from Nanyang Technological University. </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   4 </font></span><font color="#cc0000">GAO Lijie from Zhejiang University revises some errors.</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">   5 </font></span><font color="#cc0000">*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   6 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   7 </font></span><font color="#006699"><strong>section</strong></font><font color="#6600cc">&lsaquo;</font><font color="#6600cc">S</font><font color="#6600cc">e</font><font color="#6600cc">r</font><font color="#6600cc">v</font><font color="#6600cc">i</font><font color="#6600cc">c</font><font color="#6600cc">e</font><font color="#6600cc">s</font><font color="#6600cc"> </font><font color="#6600cc">o</font><font color="#6600cc">f</font><font color="#6600cc"> </font><font color="#6600cc">H</font><font color="#6600cc">e</font><font color="#6600cc">a</font><font color="#6600cc">l</font><font color="#6600cc">t</font><font color="#6600cc">h</font><font color="#6600cc"> </font><font color="#6600cc">M</font><font color="#6600cc">o</font><font color="#6600cc">n</font><font color="#6600cc">i</font><font color="#6600cc">t</font><font color="#6600cc">o</font><font color="#6600cc">r</font><font color="#6600cc">i</font><font color="#6600cc">n</font><font color="#6600cc">g</font><font color="#6600cc">&rsaquo;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   8 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   9 </font></span><font color="#006699"><strong>theory</strong></font> HealthMonitor
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  10 </font></span>  <font color="#009966"><strong>imports</strong></font> <font color="#ff00cc">&quot;../Scheduler/Design_Sched/Scheduler_D&quot;</font>  <font color="#ff00cc">&quot;../Base_Ops/Design_BsOps/WaitQue_D&quot;</font> <font color="#ff00cc">&quot;./Process&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  11 </font></span><font color="#009966"><strong>begin</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  12 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  13 </font></span><font color="#006699"><strong>definition</strong></font> hmDetectedError <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  14 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; V_CORE_ID_TYPE &rArr; DETECTED_ERROR_TYPE &rArr; 'a STATE_scheme &times; RETURN_CODE_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  15 </font></span>  <font color="#ff00cc">&quot;hmDetectedError s core_id error &equiv; hmDetectedError_ProcAct s core_id error&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  16 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  17 </font></span><font color="#cc0000">(*===REPORT_APPLICATION_MESSAGE service===*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  18 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  19 </font></span><font color="#006699"><strong>definition</strong></font> transmitMSG <font color="#000000"><strong>::</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  20 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; Message &rArr; 'a STATE_scheme&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  21 </font></span>  <font color="#ff00cc">&quot;transmitMSG s msg &equiv; s&#10631;hm_' := (hm_' s)&#10631;errorMsgL := Some msg&#10632;&#10632;&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  22 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  23 </font></span><font color="#006699"><strong>definition</strong></font> REPORT_APPLICATION_MESSAGE <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  24 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; V_CORE_ID_TYPE &rArr; Message &rArr; MSG_SZ_TYPE &rArr; 'a STATE_scheme &times; RETURN_CODE_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  25 </font></span>  <font color="#ff00cc">&quot;REPORT_APPLICATION_MESSAGE s core_id msg mlength &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  26 </font></span><font color="#ff00cc">  let cur_part_id = curPartID s core_id in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  27 </font></span><font color="#ff00cc">   if mlength &gt; MAX_MSG_SZ_VALUE then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  28 </font></span><font color="#ff00cc">     (s, INVALID_PARAM)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  29 </font></span><font color="#ff00cc">   else </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  30 </font></span><font color="#ff00cc">      (transmitMSG s msg, NO_ERROR)&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  31 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  32 </font></span><font color="#cc0000">(*===CREATE_ERROR_HANDLER service===*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  33 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  34 </font></span><font color="#006699"><strong>definition</strong></font> setErrorProcAttr <font color="#000000"><strong>::</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  35 </font></span>  <font color="#ff00cc">&quot;PROC_ATTR_TYPE &rArr; SYSTEM_ADD_TYPE &rArr; STACK_SZ_TYPE &rArr; PROC_ATTR_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  36 </font></span>  <font color="#ff00cc">&quot;setErrorProcAttr pattr entry_point stack_sz&equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  37 </font></span><font color="#ff00cc">    let</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  38 </font></span><font color="#ff00cc">       attr_set_priorty = pattr&#10631;BASE_PRIORITY := MAX_PRIORITY_VALUE&#10632;;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  39 </font></span><font color="#ff00cc">       attr_set_entry = attr_set_priorty&#10631;ENTRY_POINT := entry_point&#10632;;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  40 </font></span><font color="#ff00cc">       attr_set_stack = attr_set_entry&#10631;STACK_SZ := stack_sz&#10632;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  41 </font></span><font color="#ff00cc">    in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  42 </font></span><font color="#ff00cc">      attr_set_stack</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  43 </font></span><font color="#ff00cc">  &quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  44 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  45 </font></span><font color="#006699"><strong>definition</strong></font> setErrorProcListEmpty <font color="#000000"><strong>::</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  46 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; PART_ID_TYPE &rArr; 'a STATE_scheme&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  47 </font></span>  <font color="#ff00cc">&quot;setErrorProcListEmpty s part_id &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  48 </font></span><font color="#ff00cc">     s&#10631;hm_' := (hm_' s)&#10631;errorList := (errorList(hm_' s))(part_id:= None)&#10632;&#10632;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  49 </font></span><font color="#ff00cc">  &quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  50 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  51 </font></span><font color="#006699"><strong>definition</strong></font> CREATE_ERROR_HANDLER <font color="#000000"><strong>::</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  52 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; V_CORE_ID_TYPE &rArr; PROC_ATTR_TYPE  &rArr; SYSTEM_ADD_TYPE &rArr; STACK_SZ_TYPE &rArr; 'a STATE_scheme &times; RETURN_CODE_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  53 </font></span>  <font color="#ff00cc">&quot;CREATE_ERROR_HANDLER s core_id pattr entry_point stack_sz =</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  54 </font></span><font color="#ff00cc">  (let cur_part_id = curPartID s core_id;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  55 </font></span><font color="#ff00cc">       cur_proc_id = curProcID_Core s core_id in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  56 </font></span><font color="#ff00cc">     if getProcAttr s (the cur_part_id, HM_PROC_ID) &ne; None then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  57 </font></span><font color="#ff00cc">        (s , NO_ACTION)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  58 </font></span><font color="#ff00cc">     else if stack_sz &gt; MAX_STACK_SZ_VALUE then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  59 </font></span><font color="#ff00cc">        (s , INVALID_CONFIG)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  60 </font></span><font color="#ff00cc">     else if the(getcurPartMode s core_id) = NORMAL then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  61 </font></span><font color="#ff00cc">        (s , INVALID_MODE)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  62 </font></span><font color="#ff00cc">     else (let </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  63 </font></span><font color="#ff00cc">              attr_set_priority_entry_stack = setErrorProcAttr pattr entry_point stack_sz;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  64 </font></span><font color="#ff00cc">              s_created_eh = createProc s (the cur_part_id, the cur_proc_id) attr_set_priority_entry_stack;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  65 </font></span><font color="#ff00cc">              s_errotListEmpty = setErrorProcListEmpty s_created_eh (the cur_part_id) in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  66 </font></span><font color="#ff00cc">            (s_errotListEmpty , NO_ERROR)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  67 </font></span><font color="#ff00cc">           )</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  68 </font></span><font color="#ff00cc">  )&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  69 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  70 </font></span><font color="#cc0000">(*===GET_ERROR_STATUS service===*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  71 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  72 </font></span><font color="#006699"><strong>definition</strong></font> GET_ERROR_STATUS <font color="#000000"><strong>::</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  73 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; V_CORE_ID_TYPE &rArr; 'a STATE_scheme &times; REPORTED_ERROR_TYPE option &times; RETURN_CODE_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  74 </font></span>  <font color="#ff00cc">&quot;GET_ERROR_STATUS s core_id &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  75 </font></span><font color="#ff00cc">  let cur_part_id = curPartID s core_id;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  76 </font></span><font color="#ff00cc">      cur_proc_id = curProcID_Core s core_id  in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  77 </font></span><font color="#ff00cc">    if (the cur_proc_id) &ne; HM_PROC_ID then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  78 </font></span><font color="#ff00cc">        (s , None , INVALID_CONFIG)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  79 </font></span><font color="#ff00cc">    else if errorList(hm_' s)(the cur_part_id) = None then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  80 </font></span><font color="#ff00cc">        (s , None ,  NO_ACTION)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  81 </font></span><font color="#ff00cc">    else</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  82 </font></span><font color="#ff00cc">        let reportEr = getError s (the cur_part_id);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  83 </font></span><font color="#ff00cc">            s_clearFstEr = deleteReportedErro s core_id;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  84 </font></span><font color="#ff00cc">            s_clearFstEr_rst = rstHMStatuCalled s_clearFstEr (the cur_part_id) in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  85 </font></span><font color="#ff00cc">          (s_clearFstEr_rst, reportEr, NO_ERROR)&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  86 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  87 </font></span><font color="#cc0000">(*===RAISE_APPLICATION_ERROR service===*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  88 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  89 </font></span><font color="#006699"><strong>definition</strong></font> RAISE_APPLICATION_ERROR <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  90 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; V_CORE_ID_TYPE &rArr; PROC_ERROR_CODE_TYPE &rArr; string &rArr; 'a STATE_scheme &times; RETURN_CODE_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  91 </font></span>  <font color="#ff00cc">&quot;RAISE_APPLICATION_ERROR s core_id error_code msg &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  92 </font></span><font color="#ff00cc">  let cur_part_id = curPartID s core_id;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  93 </font></span><font color="#ff00cc">      cur_proc_id = curProcID_Core s core_id;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  94 </font></span><font color="#ff00cc">      reported_error = packRptError error_code (the cur_part_id, the cur_proc_id) msg in </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  95 </font></span><font color="#ff00cc">  if length msg &ge; MAX_MSG_SZ_VALUE then (s, INVALID_PARAM)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  96 </font></span><font color="#ff00cc">  else if error_code &ne; PROC_ERROR_CODE_TYPE.APP_ERROR then (s, INVALID_PARAM)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  97 </font></span><font color="#ff00cc">  else </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  98 </font></span><font color="#ff00cc">    if the cur_proc_id = HM_PROC_ID &or; getProcAttr s (the cur_part_id, HM_PROC_ID) = None then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  99 </font></span><font color="#ff00cc">       hmDetectedError s core_id PART_ERROR0</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066"> 100 </font></span><font color="#ff00cc">    else </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 101 </font></span><font color="#ff00cc">      let s_setError = setError s (the cur_part_id) reported_error in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 102 </font></span><font color="#ff00cc">        if getProcSta s (the cur_part_id, HM_PROC_ID) &ne; Some RUNNING then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 103 </font></span><font color="#ff00cc">         let s_setError_called = setHMCalled s_setError (the cur_part_id);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 104 </font></span><font color="#ff00cc">                s_setError_calledStaus = setHMStatuCalled s_setError_called (the cur_part_id);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066"> 105 </font></span><font color="#ff00cc">                s_setError_aloneHM = procAlone s_setError_calledStaus (the cur_part_id, HM_PROC_ID);     </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 106 </font></span><font color="#ff00cc">                s_setError_alone_rdy_HM = procReady s_setError_aloneHM (the cur_part_id, HM_PROC_ID) in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 107 </font></span><font color="#ff00cc">          (schedule_Abs s_setError_alone_rdy_HM core_id, NO_ERROR)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 108 </font></span><font color="#ff00cc">        else</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 109 </font></span><font color="#ff00cc">          (s_setError, NO_ERROR)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066"> 110 </font></span><font color="#ff00cc">  &quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 111 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 112 </font></span><font color="#cc0000">(*===CONFIGURE_ERROR_HANDLER service===*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 113 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 114 </font></span><font color="#006699"><strong>definition</strong></font> setConcurConf <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066"> 115 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; V_CORE_ID_TYPE &rArr; HM_CONCURRENCY_TYPE &rArr; 'a STATE_scheme&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 116 </font></span>  <font color="#ff00cc">&quot;setConcurConf s core_id hct &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 117 </font></span><font color="#ff00cc">  let cur_part_id = curPartID s core_id in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 118 </font></span><font color="#ff00cc">  s&#10631;hm_' := (hm_' s)&#10631;concurConf := (concurConf(hm_' s))(the cur_part_id := Some hct)&#10632;&#10632;&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 119 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066"> 120 </font></span><font color="#006699"><strong>definition</strong></font> resetConcurConf <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 121 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; V_CORE_ID_TYPE &rArr; 'a STATE_scheme&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 122 </font></span>  <font color="#ff00cc">&quot;resetConcurConf s core_id &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 123 </font></span><font color="#ff00cc">  let cur_part_id = curPartID s core_id in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 124 </font></span><font color="#ff00cc">  s&#10631;hm_' := (hm_' s)&#10631;concurConf := (concurConf(hm_' s))(the cur_part_id := None)&#10632;&#10632;&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066"> 125 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 126 </font></span><font color="#006699"><strong>definition</strong></font> CONFIGURE_ERROR_HANDLER <font color="#000000"><strong>::</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 127 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; V_CORE_ID_TYPE &rArr; HM_CONCURRENCY_TYPE &rArr; 'a STATE_scheme &times; RETURN_CODE_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 128 </font></span>  <font color="#ff00cc">&quot;CONFIGURE_ERROR_HANDLER s core_id hct &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 129 </font></span><font color="#ff00cc">  let cur_part_id = curPartID s core_id in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066"> 130 </font></span><font color="#ff00cc">   if getProcAttr s (the cur_part_id, HM_PROC_ID)  = None then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 131 </font></span><font color="#ff00cc">      (s , NO_ACTION)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 132 </font></span><font color="#ff00cc">   else if the(getcurPartMode s core_id) = NORMAL then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 133 </font></span><font color="#ff00cc">      (s , INVALID_MODE)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 134 </font></span><font color="#ff00cc">   else if partBlngCore (the cur_part_id) &ne; Some core_id then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066"> 135 </font></span><font color="#ff00cc">      (s , INVALID_CONFIG)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 136 </font></span><font color="#ff00cc">   else (setConcurConf s core_id hct, NO_ERROR)&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 137 </font></span>   
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 138 </font></span><font color="#009966"><strong>end</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000"> 139 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066"> 140 </font></span>
</font></pre>
</body>
</html>
