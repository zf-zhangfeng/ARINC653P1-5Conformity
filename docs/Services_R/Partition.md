- theory Partition
- imports  ["../Base_Ops/Design_BsOps/ProcessAct_D"](https://zf-zhangfeng.github.io/ARINC653P1-5Conformity/Base_Ops/Design_BsOps/ProcessAct_D)

<html>
<head>
<title>Partition.thy</title>
</head>
<body bgcolor="#ffffff">



<pre><font color="#333333"><span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   1 </font></span><font color="#cc0000">(*</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   2 </font></span><font color="#cc0000">The Interpartition Communication services for ARINC 653 P1-5 are first created by </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   3 </font></span><font color="#cc0000">  GAO Lijie from Zhejiang University.</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   4 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">   5 </font></span><font color="#cc0000">And this part is reviewed and corrected by ZHANG Feng from Nanyang Technological University.</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   6 </font></span><font color="#cc0000">*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   7 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   8 </font></span><font color="#006699"><strong>section</strong></font><font color="#6600cc">&lsaquo;</font><font color="#6600cc">S</font><font color="#6600cc">e</font><font color="#6600cc">r</font><font color="#6600cc">v</font><font color="#6600cc">i</font><font color="#6600cc">c</font><font color="#6600cc">e</font><font color="#6600cc">s</font><font color="#6600cc"> </font><font color="#6600cc">o</font><font color="#6600cc">f</font><font color="#6600cc"> </font><font color="#6600cc">P</font><font color="#6600cc">a</font><font color="#6600cc">r</font><font color="#6600cc">t</font><font color="#6600cc">i</font><font color="#6600cc">t</font><font color="#6600cc">i</font><font color="#6600cc">o</font><font color="#6600cc">n</font><font color="#6600cc"> </font><font color="#6600cc">M</font><font color="#6600cc">a</font><font color="#6600cc">n</font><font color="#6600cc">a</font><font color="#6600cc">g</font><font color="#6600cc">e</font><font color="#6600cc">m</font><font color="#6600cc">e</font><font color="#6600cc">n</font><font color="#6600cc">t</font><font color="#6600cc">&rsaquo;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   9 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  10 </font></span><font color="#006699"><strong>theory</strong></font> Partition
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  11 </font></span><font color="#009966"><strong>imports</strong></font>  <font color="#ff00cc">&quot;../Base_Ops/Design_BsOps/ProcessAct_D&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  12 </font></span><font color="#009966"><strong>begin</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  13 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  14 </font></span><font color="#cc0000">(*Services*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  15 </font></span><font color="#006699"><strong>definition</strong></font> GET_PARTITION_STATUS <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  16 </font></span>  <font color="#ff00cc">&quot;'a StaProc_scheme &rArr; V_CORE_ID_TYPE &rArr; (PART_STATUS_TYPE option &times; RETURN_CODE_TYPE)&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  17 </font></span>  <font color="#ff00cc">&quot;GET_PARTITION_STATUS s core_id &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  18 </font></span><font color="#ff00cc">  let cur_part_id = curPartID s core_id in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  19 </font></span><font color="#ff00cc">  if cur_part_id &ne; None then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  20 </font></span><font color="#ff00cc">      (Some (getPartStatus s (the cur_part_id)), NO_ERROR)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  21 </font></span><font color="#ff00cc">  else</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  22 </font></span><font color="#ff00cc">      (None, INVALID_MODE)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  23 </font></span><font color="#ff00cc">  &quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  24 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  25 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  26 </font></span><font color="#006699"><strong>definition</strong></font> SET_PARTITION_MODE <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  27 </font></span>  <font color="#ff00cc">&quot;'a STATE_scheme &rArr; V_CORE_ID_TYPE &rArr; OPERATING_MODE_TYPE &rArr; 'a STATE_scheme &times; RETURN_CODE_TYPE&quot;</font>  <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  28 </font></span>  <font color="#ff00cc">&quot;SET_PARTITION_MODE s core_id oper_mode &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  29 </font></span><font color="#ff00cc">  let cur_part_id = the (curPartID s core_id); cur_mode = the(getPartMode s  cur_part_id) in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  30 </font></span><font color="#ff00cc">  if oper_mode = NORMAL &and; cur_mode = NORMAL then (s, NO_ACTION)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  31 </font></span><font color="#ff00cc">  else if oper_mode = WARM_START &and; cur_mode = COLD_START then (s, INVALID_MODE)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  32 </font></span><font color="#ff00cc">  else </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  33 </font></span><font color="#ff00cc">    (let s_setMode = setPartMode s cur_part_id oper_mode in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  34 </font></span><font color="#ff00cc">    if oper_mode = IDLE then (s_setMode, NO_ERROR)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  35 </font></span><font color="#ff00cc">    else  </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  36 </font></span><font color="#ff00cc">      if oper_mode = WARM_START &or; oper_mode = COLD_START then (partRestart s_setMode core_id, NO_ERROR)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  37 </font></span><font color="#ff00cc">      else (set_part_mode_normal s_setMode core_id, NO_ERROR))&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  38 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  39 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  40 </font></span><font color="#009966"><strong>end</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  41 </font></span>
</font></pre>
</body>
</html>
