# utl-set_26-flages-based-on_26-complex-queries-fast-muti-core-processing
Set 26 flags based on 26 complex queries fast muti core processing
    SAS Forum: Set 26 flags based on 26 complex queries fast muti core processing

    github
    https://tinyurl.com/tgjmrhk
    https://github.com/rogerjdeangelis/utl-set_26-flages-based-on_26-complex-queries-fast-muti-core-processing

    SAS Forum
    https://communities.sas.com/t5/SAS-Programming/Do-Loop-within-proc-SQL/m-p/613108

    SAS should optimize this repetitive code passing code to multiple cores.
    This should be faster thab looping?
    If it does not then consider data partitioning and multi-tasking (use systask).

    * This should be fast on a  inexpensive desktop with 16/32 cores;

     *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    * I assign letters in my autoexex;
    %put &=letters;

    LETTERS=A B C D E F G H I J K L M N O P Q R S T U V W X Y Z

    * create 26 variables, a-z and populate with ones and zeros;;

    data have;
      retain id o_diab clm_from;
      array bin &letters;
      do id=1 to 5;
          o_diab= int(uniform(1234)<.5);
          clm_from=int(15*uniform(1234))+1;
          do over bin;
             bin = (uniform(1234)<.5);
          end;
          output;
      end;
    run;quit;


    WORK.HAVE total obs=5

      ID    O_DIAB    CLM_FROM    A    B    C    D    E    F    G    H    I    J    K    L    M    N    O    P    Q    R    S    T    U    V    W    X    Y    Z

       1       1          2       1    1    1    1    1    1    1    1    1    1    1    0    0    0    1    1    1    0    1    1    1    1    1    0    1    0
       2       0          4       0    1    1    0    0    0    0    0    0    1    1    0    0    1    1    0    1    1    0    0    1    0    0    0    0    1
       3       1          5       0    0    0    0    1    0    1    1    0    0    1    1    0    0    1    0    1    0    1    0    1    1    1    1    0    0
       4       1         14       1    1    0    0    0    1    1    0    0    1    0    0    1    0    1    0    1    0    0    0    0    1    0    0    1    1
       5       0         11       0    0    0    0    1    1    1    1    1    1    0    0    1    0    1    1    0    0    0    0    0    1    1    0    1    0

    RULE (set 26 flage somthing like this)

    26 sets of 3 clauses

      if a=0 and diab=0  then select the  min(clm_from) else .
      if a=1 and diab=1  then select the  max(clm_from) else
      if max(clm_from) - min(clm_from) >= 7 then set flfa=1 else flga=0

      if b=0 and diab=0  then select the  min(clm_from) else .
      if b=1 and diab=1  then select the  max(clm_from) else
      if max(clm_from) - min(clm_from) >= 7 then set flfa=1 else flgb=0
     ...

      if z=0 and diab=0  then select the  min(clm_from) else .
      if z=1 and diab=1  then select the  max(clm_from) else
      if max(clm_from) - min(clm_from) >= 7 then set flfa=1 else flgz=0

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;

    WORK.WANT total obs=1

     FLGA  FLGB  FLGC  FLGD  FLGE  FLGF  FLGG  FLGH  FLGI  FLGJ  FLGK  FLGL  FLGM  FLGN  FLGO  FLGP  FLGQ  FLGR  FLGS  FLGT  FLGU  FLGV  FLGW  FLGX  FLGY  FLGZ

       1     0     0     0     0     1     1     0     0     0     0     0     1     0     0     0     0     0     0     0     0     1     0     0     1     0


    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    * do the distinct separately:

    %array(vs,values=&letters);

    options ls=171;
    proc sql;

      create
         table want(keep=Flg:) as

      select
         %do_over(vs,phrase=%str(
           min(case when ? =0 and o_diab=0 then clm_from else . end) as firstDT_o_diab?
          ,max(case when ? =1 and o_diab=1 then clm_from else . end) as lastDT_o_diab?
          ,(case when calculated lastDT_o_diab? - calculated firstDT_o_diab? >=7 then 1 else 0 end) as Flg?), between=comma)
        from
           have

    ;quit;

    * generated code in the log (you need to turn debugging off. I use macro utlnopts/utlopts);
    %utlnopts;
    %do_over(vs,phrase=%str(
      data _null_;
         bln='0D0A'x;
         putlog ",min(case when ? =0 and o_diab=0 then clm_from else . end) as firstDT_o_diab?" /
                ",max(case when ? =1 and o_diab=1 then clm_from else . end) as lastDT_o_diab?"  /
                ",(case when calculated lastDT_o_diab? - calculated firstDT_o_diab? >=7 then 1 else 0 end) as Flg?" / bln
         ;))
    run;quit;
    %utlopts;

    *                        _
      ___ ___  _ __  _   _  | | ___   __ _
     / __/ _ \| '_ \| | | | | |/ _ \ / _` |
    | (_| (_) | |_) | |_| | | | (_) | (_| |
     \___\___/| .__/ \__, | |_|\___/ \__, |
              |_|    |___/           |___/
    ;

    * see end of message on generated this code;

    proc sql;

      create
         table want(keep=Flg:) as

      select
          min(case when A =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabA
         ,max(case when A =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabA
         ,(case when calculated lastDT_o_diabA - calculated firstDT_o_diabA >=7 then 1 else 0 end) as FlgA

         ,min(case when B =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabB
         ,max(case when B =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabB
         ,(case when calculated lastDT_o_diabB - calculated firstDT_o_diabB >=7 then 1 else 0 end) as FlgB

         ,min(case when C =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabC
         ,max(case when C =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabC
         ,(case when calculated lastDT_o_diabC - calculated firstDT_o_diabC >=7 then 1 else 0 end) as FlgC

         ,min(case when D =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabD
         ,max(case when D =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabD
         ,(case when calculated lastDT_o_diabD - calculated firstDT_o_diabD >=7 then 1 else 0 end) as FlgD

         ,min(case when E =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabE
         ,max(case when E =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabE
         ,(case when calculated lastDT_o_diabE - calculated firstDT_o_diabE >=7 then 1 else 0 end) as FlgE

         ,min(case when F =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabF
         ,max(case when F =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabF
         ,(case when calculated lastDT_o_diabF - calculated firstDT_o_diabF >=7 then 1 else 0 end) as FlgF

         ,min(case when G =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabG
         ,max(case when G =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabG
         ,(case when calculated lastDT_o_diabG - calculated firstDT_o_diabG >=7 then 1 else 0 end) as FlgG

         ,min(case when H =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabH
         ,max(case when H =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabH
         ,(case when calculated lastDT_o_diabH - calculated firstDT_o_diabH >=7 then 1 else 0 end) as FlgH

         ,min(case when I =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabI
         ,max(case when I =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabI
         ,(case when calculated lastDT_o_diabI - calculated firstDT_o_diabI >=7 then 1 else 0 end) as FlgI

         ,min(case when J =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabJ
         ,max(case when J =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabJ
         ,(case when calculated lastDT_o_diabJ - calculated firstDT_o_diabJ >=7 then 1 else 0 end) as FlgJ

         ,min(case when K =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabK
         ,max(case when K =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabK
         ,(case when calculated lastDT_o_diabK - calculated firstDT_o_diabK >=7 then 1 else 0 end) as FlgK

         ,min(case when L =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabL
         ,max(case when L =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabL
         ,(case when calculated lastDT_o_diabL - calculated firstDT_o_diabL >=7 then 1 else 0 end) as FlgL

         ,min(case when M =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabM
         ,max(case when M =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabM
         ,(case when calculated lastDT_o_diabM - calculated firstDT_o_diabM >=7 then 1 else 0 end) as FlgM

         ,min(case when N =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabN
         ,max(case when N =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabN
         ,(case when calculated lastDT_o_diabN - calculated firstDT_o_diabN >=7 then 1 else 0 end) as FlgN

         ,min(case when O =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabO
         ,max(case when O =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabO
         ,(case when calculated lastDT_o_diabO - calculated firstDT_o_diabO >=7 then 1 else 0 end) as FlgO

         ,min(case when P =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabP
         ,max(case when P =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabP
         ,(case when calculated lastDT_o_diabP - calculated firstDT_o_diabP >=7 then 1 else 0 end) as FlgP

         ,min(case when Q =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabQ
         ,max(case when Q =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabQ
         ,(case when calculated lastDT_o_diabQ - calculated firstDT_o_diabQ >=7 then 1 else 0 end) as FlgQ

         ,min(case when R =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabR
         ,max(case when R =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabR
         ,(case when calculated lastDT_o_diabR - calculated firstDT_o_diabR >=7 then 1 else 0 end) as FlgR

         ,min(case when S =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabS
         ,max(case when S =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabS
         ,(case when calculated lastDT_o_diabS - calculated firstDT_o_diabS >=7 then 1 else 0 end) as FlgS

         ,min(case when T =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabT
         ,max(case when T =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabT
         ,(case when calculated lastDT_o_diabT - calculated firstDT_o_diabT >=7 then 1 else 0 end) as FlgT

         ,min(case when U =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabU
         ,max(case when U =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabU
         ,(case when calculated lastDT_o_diabU - calculated firstDT_o_diabU >=7 then 1 else 0 end) as FlgU

         ,min(case when V =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabV
         ,max(case when V =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabV
         ,(case when calculated lastDT_o_diabV - calculated firstDT_o_diabV >=7 then 1 else 0 end) as FlgV

         ,min(case when W =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabW
         ,max(case when W =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabW
         ,(case when calculated lastDT_o_diabW - calculated firstDT_o_diabW >=7 then 1 else 0 end) as FlgW

         ,min(case when X =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabX
         ,max(case when X =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabX
         ,(case when calculated lastDT_o_diabX - calculated firstDT_o_diabX >=7 then 1 else 0 end) as FlgX

         ,min(case when Y =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabY
         ,max(case when Y =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabY
         ,(case when calculated lastDT_o_diabY - calculated firstDT_o_diabY >=7 then 1 else 0 end) as FlgY

         ,min(case when Z =0 and o_diab=1 then clm_from else . end) as firstDT_o_diabZ
         ,max(case when Z =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabZ
         ,max(case when Y =0 and o_diab=1 then clm_from else . end) as lastDT_o_diabY
         ,(case when calculated lastDT_o_diabY - calculated firstDT_o_diabY >=7 then 1 else 0 end) as FlgZ
        from
           have

    ;quit;


