theory suspQue_D

imports [WaitQue_D](https://zf-zhangfeng.github.io/ARINC653P1-5Conformity/Base_Ops/Design_BsOps/WaitQue_D)


<html>
<head>
<title>suspQue_D.thy</title>
</head>
<body bgcolor="#ffffff">



<pre><font color="#333333"><span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   1 </font></span><font color="#cc0000">(*</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   2 </font></span><font color="#cc0000">These operations on the suspended queue of ARINC 653 is created by </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   3 </font></span><font color="#cc0000">  ZHANG Feng from Nanyang Technological University.</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   4 </font></span><font color="#cc0000">*)</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">   5 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   6 </font></span><font color="#006699"><strong>section</strong></font><font color="#6600cc">&lsaquo;</font><font color="#6600cc">O</font><font color="#6600cc">p</font><font color="#6600cc">e</font><font color="#6600cc">r</font><font color="#6600cc">a</font><font color="#6600cc">t</font><font color="#6600cc">i</font><font color="#6600cc">o</font><font color="#6600cc">n</font><font color="#6600cc">s</font><font color="#6600cc"> </font><font color="#6600cc">o</font><font color="#6600cc">n</font><font color="#6600cc"> </font><font color="#6600cc">S</font><font color="#6600cc">u</font><font color="#6600cc">s</font><font color="#6600cc">p</font><font color="#6600cc">e</font><font color="#6600cc">n</font><font color="#6600cc">d</font><font color="#6600cc">e</font><font color="#6600cc">d</font><font color="#6600cc"> </font><font color="#6600cc">P</font><font color="#6600cc">r</font><font color="#6600cc">o</font><font color="#6600cc">c</font><font color="#6600cc">e</font><font color="#6600cc">s</font><font color="#6600cc">s</font><font color="#6600cc"> </font><font color="#6600cc">Q</font><font color="#6600cc">u</font><font color="#6600cc">e</font><font color="#6600cc">u</font><font color="#6600cc">e</font><font color="#6600cc">s</font><font color="#6600cc">&rsaquo;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   7 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   8 </font></span><font color="#006699"><strong>theory</strong></font> suspQue_D
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">   9 </font></span><font color="#009966"><strong>imports</strong></font> WaitQue_D
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  10 </font></span><font color="#009966"><strong>begin</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  11 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  12 </font></span><font color="#006699"><strong>definition</strong></font> suspQueAdd_D <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  13 </font></span>  <font color="#ff00cc">&quot;'a StaProc_scheme &rArr; PART_ID_TYPE &times; PROC_ID_TYPE &rArr; 'a StaProc_scheme&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  14 </font></span>  <font color="#ff00cc">&quot;suspQueAdd_D s pp = </font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  15 </font></span><font color="#ff00cc">  (let cid = the (procBlngCore pp); tq = suspdQue(cores_' s); susp_diff = suspDiff(rscPendProcs_' s);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  16 </font></span><font color="#ff00cc">      tq_wd = waitQ_SuspQAdd tq susp_diff cid pp  in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  17 </font></span><font color="#ff00cc">  if the(susp_diff pp) &gt; 0 then</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  18 </font></span><font color="#ff00cc">    s&#10631;cores_' := (cores_' s)&#10631;suspdQue := fst tq_wd&#10632;,</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  19 </font></span><font color="#ff00cc">      rscPendProcs_' := (rscPendProcs_' s)&#10631;suspDiff := snd tq_wd&#10632;&#10632;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  20 </font></span><font color="#ff00cc">  else s)&quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  21 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  22 </font></span><font color="#006699"><strong>definition</strong></font> suspQueDel_D <font color="#000000"><strong>::</strong></font> 
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  23 </font></span>  <font color="#ff00cc">&quot;'a StaProc_scheme &rArr; PART_ID_TYPE &times; PROC_ID_TYPE &rArr; 'a StaProc_scheme&quot;</font> <font color="#009966"><strong>where</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  24 </font></span>  <font color="#ff00cc">&quot;suspQueDel_D s pp =</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  25 </font></span><font color="#ff00cc">  (let cid = the (procBlngCore pp); vpwl = suspdQue (cores_' s); pd = suspDiff(rscPendProcs_' s);</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  26 </font></span><font color="#ff00cc">    new_vp_pd = waitQ_SuspQDel vpwl pd cid pp  in</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  27 </font></span><font color="#ff00cc">  s&#10631;cores_' := (cores_' s)&#10631;suspdQue := fst new_vp_pd&#10632;,</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  28 </font></span><font color="#ff00cc">    rscPendProcs_' := (rscPendProcs_' s)&#10631;suspDiff := snd new_vp_pd&#10632;&#10632;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  29 </font></span><font color="#ff00cc">  )</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff990066">  30 </font></span><font color="#ff00cc">  &quot;</font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  31 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  32 </font></span>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  33 </font></span><font color="#009966"><strong>end</strong></font>
<span style="background:#ffffffff; border-right:solid 2px black; margin-right:5px; "><font color="#ff000000">  34 </font></span>
</font></pre>
</body>
</html>
