
theory ReadyQue_D

imports ["../../DataStructure"](https://zf-zhangfeng.github.io/ARINC653P1-5Conformity/DataStructure)

<html>
<head>
<title>ReadyQue_D.thy</title>
</head>
<body bgcolor="#ffffff">



<pre><font color="#333333"><span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   1 </font></span><font color="#cc0000">(*</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   2 </font></span><font color="#cc0000">These operations on the ready queue of ARINC 653 are created by </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   3 </font></span><font color="#cc0000">  ZHANG Feng from Nanyang Technological University.</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   4 </font></span><font color="#cc0000">*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">   5 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   6 </font></span><font color="#006699"><strong>section</strong></font><font color="#6600cc">&lsaquo;</font><font color="#6600cc">O</font><font color="#6600cc">p</font><font color="#6600cc">e</font><font color="#6600cc">r</font><font color="#6600cc">a</font><font color="#6600cc">t</font><font color="#6600cc">i</font><font color="#6600cc">o</font><font color="#6600cc">n</font><font color="#6600cc">s</font><font color="#6600cc"> </font><font color="#6600cc">o</font><font color="#6600cc">n</font><font color="#6600cc"> </font><font color="#6600cc">R</font><font color="#6600cc">e</font><font color="#6600cc">a</font><font color="#6600cc">d</font><font color="#6600cc">y</font><font color="#6600cc"> </font><font color="#6600cc">P</font><font color="#6600cc">r</font><font color="#6600cc">o</font><font color="#6600cc">c</font><font color="#6600cc">e</font><font color="#6600cc">s</font><font color="#6600cc">s</font><font color="#6600cc"> </font><font color="#6600cc">Q</font><font color="#6600cc">u</font><font color="#6600cc">e</font><font color="#6600cc">u</font><font color="#6600cc">e</font><font color="#6600cc">s</font><font color="#6600cc">&rsaquo;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   7 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   8 </font></span><font color="#006699"><strong>theory</strong></font> ReadyQue_D
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   9 </font></span><font color="#009966"><strong>imports</strong></font> <font color="#ff00cc">&quot;../../DataStructure&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  10 </font></span><font color="#009966"><strong>begin</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  11 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  12 </font></span><font color="#006699"><strong>value</strong></font> <font color="#ff00cc">&quot;[]#([]#([1::int]#[]))&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  13 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  14 </font></span><font color="#cc0000">(*There is a premis that: this pll exists this a priority whose process list is not empty*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  15 </font></span><font color="#006699"><strong>primrec</strong></font> hiestPrority_D <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  16 </font></span>  <font color="#ff00cc">&quot;(PROC_ID_TYPE list list) &rArr; PRIORITY_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  17 </font></span>  <font color="#ff00cc">&quot;hiestPrority_D [] = 0&quot;</font> <font color="#000000"><strong>|</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  18 </font></span>  <font color="#ff00cc">&quot;hiestPrority_D (pl#pls) = </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  19 </font></span><font color="#ff00cc">    (if pl &ne; [] then 0 else hiestPrority_D pls + 1)&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  20 </font></span><font color="#cc0000">(*</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  21 </font></span><font color="#cc0000">definition hiestPrority_D :: </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  22 </font></span><font color="#cc0000">  &quot;(PROC_ID_TYPE list list) &rArr; PRIORITY_TYPE&quot; where</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  23 </font></span><font color="#cc0000">  &quot;hiestPrority_D pll &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  24 </font></span><font color="#cc0000">  THE prio. &forall;prio'&lt;prio. pll!prio' = [] &and; pll!prio &ne; []&quot; </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  25 </font></span><font color="#cc0000">*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  26 </font></span><font color="#006699"><strong>value</strong></font> <font color="#ff00cc">&quot;hiestPrority_D [[], [], [2::int]]&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  27 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  28 </font></span><font color="#006699"><strong>definition</strong></font> fstProc_HiestPrority_D <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  29 </font></span>  <font color="#ff00cc">&quot;(PART_ID_TYPE &#8640; (PROC_ID_TYPE list list)) &rArr; PART_ID_TYPE &rArr; PROC_ID_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  30 </font></span>  <font color="#ff00cc">&quot;fstProc_HiestPrority_D readyQ part_id = </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  31 </font></span><font color="#ff00cc">  ((the(readyQ part_id))!(hiestPrority_D (the(readyQ part_id))))!0&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  32 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  33 </font></span><font color="#006699"><strong>definition</strong></font> readyQEmpty_D <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  34 </font></span>  <font color="#ff00cc">&quot;(PART_ID_TYPE &#8640; (PROC_ID_TYPE list list)) &rArr; PART_ID_TYPE &rArr; bool&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  35 </font></span>  <font color="#ff00cc">&quot;readyQEmpty_D readyQ part_id &equiv; (&forall;nth. (the(readyQ part_id))!nth = [])&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  36 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  37 </font></span><font color="#006699"><strong>primrec</strong></font> readyL_del <font color="#000000"><strong>::</strong></font> <font color="#ff00cc">&quot;PROC_ID_TYPE list &rArr; PROC_ID_TYPE &rArr; PROC_ID_TYPE list&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  38 </font></span>  <font color="#ff00cc">&quot;readyL_del [] pid = []&quot;</font> <font color="#000000"><strong>|</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  39 </font></span>  <font color="#ff00cc">&quot;readyL_del (p#pl) pid = (if p = pid then pl else p # readyL_del pl pid)&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  40 </font></span>  
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  41 </font></span><font color="#cc0000">(*input: the num is the position of the proc in the list*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  42 </font></span><font color="#006699"><strong>definition</strong></font> readyDel_D <font color="#000000"><strong>::</strong></font>  
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  43 </font></span>  <font color="#ff00cc">&quot;(PART_ID_TYPE &#8640; (PROC_ID_TYPE list list)) &rArr;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  44 </font></span><font color="#ff00cc">      PART_ID_TYPE &rArr; PRIORITY_TYPE &rArr; PROC_ID_TYPE &rArr;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  45 </font></span><font color="#ff00cc">  PART_ID_TYPE &#8640; (PROC_ID_TYPE list list)&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  46 </font></span>  <font color="#ff00cc">&quot;readyDel_D readyQ part_id proc_priority proc_id =</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  47 </font></span><font color="#ff00cc">  (let rll = the (readyQ part_id);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  48 </font></span><font color="#ff00cc">      rl = rll!proc_priority in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  49 </font></span><font color="#ff00cc">  readyQ(part_id := Some (rll[proc_priority := readyL_del rl proc_id])))&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  50 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  51 </font></span><font color="#cc0000">(*FIFO*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  52 </font></span><font color="#006699"><strong>definition</strong></font> readyAddTail_D <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  53 </font></span>  <font color="#ff00cc">&quot;(PART_ID_TYPE &#8640; (PROC_ID_TYPE list list)) &rArr;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  54 </font></span><font color="#ff00cc">    PART_ID_TYPE &rArr; PRIORITY_TYPE &rArr; PROC_ID_TYPE &rArr;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  55 </font></span><font color="#ff00cc">  (PART_ID_TYPE &#8640; (PROC_ID_TYPE list list))&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  56 </font></span>  <font color="#ff00cc">&quot;readyAddTail_D readyQ part_id proc_priority proc_id =</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  57 </font></span><font color="#ff00cc">  (let rll = the (readyQ part_id);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  58 </font></span><font color="#ff00cc">       rl = rll!proc_priority in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  59 </font></span><font color="#ff00cc">  readyQ(part_id := Some (rll[proc_priority := rl @ [proc_id]])))&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  60 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  61 </font></span><font color="#006699"><strong>definition</strong></font> readyDel_s_D <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  62 </font></span>  <font color="#ff00cc">&quot;'a StaProc_sched_scheme &rArr; PART_ID_TYPE &rArr;  PROC_ID_TYPE &rArr; 'a StaProc_sched_scheme&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  63 </font></span>  <font color="#ff00cc">&quot;readyDel_s_D s part_id proc_id =</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  64 </font></span><font color="#ff00cc">  (let ll = the ((readyQue(partitions_' s)) part_id); </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  65 </font></span><font color="#ff00cc">      proc_priority = the((curPriority(procs_' s)) (part_id, proc_id)) in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  66 </font></span><font color="#ff00cc">  s&#10631;partitions_' := (partitions_' s)&#10631;readyQue := readyDel_D (readyQue(partitions_' s)) part_id proc_priority proc_id&#10632;&#10632;)&quot;</font>  
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  67 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  68 </font></span><font color="#006699"><strong>definition</strong></font> readyAddHead_D <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  69 </font></span>  <font color="#ff00cc">&quot;(PART_ID_TYPE &#8640; (PROC_ID_TYPE list list)) &rArr;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  70 </font></span><font color="#ff00cc">    PART_ID_TYPE &rArr; PRIORITY_TYPE &rArr; PROC_ID_TYPE &rArr;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  71 </font></span><font color="#ff00cc">  (PART_ID_TYPE &#8640; (PROC_ID_TYPE list list))&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  72 </font></span>  <font color="#ff00cc">&quot;readyAddHead_D readyQ part_id proc_priority proc_id =</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  73 </font></span><font color="#ff00cc">  (let rll = the (readyQ part_id);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  74 </font></span><font color="#ff00cc">       rl = rll!proc_priority in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  75 </font></span><font color="#ff00cc">  readyQ(part_id := Some (rll[proc_priority := proc_id # rl])))&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  76 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  77 </font></span><font color="#006699"><strong>definition</strong></font> readySeek_D <font color="#000000"><strong>::</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  78 </font></span>  <font color="#ff00cc">&quot;(PART_ID_TYPE &#8640; (PROC_ID_TYPE list list)) &rArr; PART_ID_TYPE &rArr;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  79 </font></span><font color="#ff00cc">  (PROC_ID_TYPE option)&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  80 </font></span>  <font color="#ff00cc">&quot;readySeek_D readyQ part_id=</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  81 </font></span><font color="#ff00cc">  (if &forall;proc_priority. (the (readyQ part_id))!proc_priority = [] then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  82 </font></span><font color="#ff00cc">       None</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  83 </font></span><font color="#ff00cc">  else Some (fstProc_HiestPrority_D readyQ part_id))&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  84 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  85 </font></span><font color="#006699"><strong>definition</strong></font> schedAlgRT_D <font color="#000000"><strong>::</strong></font>  
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  86 </font></span>  <font color="#ff00cc">&quot;(PART_ID_TYPE &#8640; (PROC_ID_TYPE list list)) &rArr; PART_ID_TYPE &rArr; PROC_ID_TYPE&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  87 </font></span>  <font color="#ff00cc">&quot;schedAlgRT_D readyQ part_id &equiv;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  88 </font></span><font color="#ff00cc">  if readySeek_D readyQ part_id = None then </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  89 </font></span><font color="#ff00cc">     IDLE_PROC_ID</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  90 </font></span><font color="#ff00cc">  else the (readySeek_D readyQ part_id)&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  91 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  92 </font></span><font color="#009966"><strong>end</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  93 </font></span>
</font></pre>
</body>
</html>
