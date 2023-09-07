# utl-attempt-to-call-fcmp-containing-pokelong-from-sql
    %let pgm=utl-attempt-to-call-fcmp-containing-pokelong-from-sql;

     Attempt to call fcmp containing pokelong from sql

     PROBLEM

         Set all values in an array to 0 without looping.

         Note this requires the array elements to be sequential. ie val1-val7.
         Only temporary arrays are guaranteed to hve sequential elements.
         Should be ok for small permanent arrays.

       When I tried to call fcmp containing pokelong from sql it failed.
       However there was no error or warning,
       However, when pcmp pokelong is called from a datastep it works.

       1 sql call fcmp sql issue
         Mark pointed out that this is a SQL issue not a fcmp issue (see 2 below)

       2 datastep call fcmp works (g0od to know)
         mkeintz@outlook.com

     github
     https://tinyurl.com/y3d2ypn7
     https://github.com/rogerjdeangelis/utl-attempt-to-call-fcmp-containing-pokelong-from-sql

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */
     options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
      input
       id type $ val1 val2 val3 val4 val5 val6 val7;
    cards4;
    1 a 10 13 8 10 15 5 11
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                     |                                                                  */
    /* SD1.HAVE total obs=1                                | SD1.WANT                                                         */
    /*                                                     |                                                                  */
    /*                         INPUT                       |                          OUTPUT                                  */
    /*  ================================================== |   ==================================================             */
    /*  ID  TYPE  VAL1  VAL2  VAL3  VAL4  VAL5  VAL6  VAL7 |   ID  TYPE  VAL1  VAL2  VAL3  VAL4  VAL5  VAL6  VAL7             */
    /*                                                     |                                                                  */
    /*   1   a     10    13     8    10    15     5    11  |    1   a      0     0     0     0     0     0     0              */
    /*                                                     |                                                                  */
    /**************************************************************************************************************************/

    /*             _ _    __                                  _   _
    / |   ___ __ _| | |  / _| ___ _ __ ___  _ __    ___  __ _| | (_)___ ___ _   _  ___
    | |  / __/ _` | | | | |_ / __| `_ ` _ \| `_ \  / __|/ _` | | | / __/ __| | | |/ _ \
    | | | (_| (_| | | | |  _| (__| | | | | | |_) | \__ \ (_| | | | \__ \__ \ |_| |  __/
    |_|  \___\__,_|_|_| |_|  \___|_| |_| |_| .__/  |___/\__, |_| |_|___/___/\__,_|\___|
                                           |_|             |_|
    */

    options cmplib=work.functions;

    proc fcmp outlib=work.functions.samples;
     function popAry (adr $ );
      call pokelong(repeat(put(0,rb8.),6),adr,96);
    return(val1);
    endsub;
    run;quit;

    proc sql;
    update sd1.have
    set val1 = popAry (addrlong(val1))
    ;quit;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* FAILS                                                                                                                  */
    /*                                                                                                                        */
    /* LOG (no error or warning)                                                                                              */
    /*                                                                                                                        */
    /*  1 proc sql;                                                                                                           */
    /*  2 update sd1.have                                                                                                     */
    /*  3 set val1 = popAry (addrlong(val1))                                                                                  */
    /*  4 ;                                                                                                                   */
    /*  1  quit;                                                                                                              */
    /*  NOTE: 1 row was updated in SD1.HAVE.                                                                                  */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  SD1.HAVE total obs=1                                                                                                  */
    /*                                                                                                                        */
    /*               Set to                                                                                                   */
    /*               Missing      Expected these to be zero                                                                   */
    /*               -------   --------------------------------------------                                                   */
    /*   ID    TYPE    VAL1    VAL2    VAL3    VAL4    VAL5    VAL6    VAL7                                                   */
    /*                                                                                                                        */
    /*    1     a        .      13       8      10      15       5      11                                                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*   _       _            _                         _ _    __                                           _
      __| | __ _| |_ __ _ ___| |_ ___ _ __     ___ __ _| | |  / _| ___ _ __ ___  _ __   __      _____  _ __| | _____
     / _` |/ _` | __/ _` / __| __/ _ \ `_ \   / __/ _` | | | | |_ / __| `_ ` _ \| `_ \  \ \ /\ / / _ \| `__| |/ / __|
    | (_| | (_| | || (_| \__ \ ||  __/ |_) | | (_| (_| | | | |  _| (__| | | | | | |_) |  \ V  V / (_) | |  |   <\__ \
     \__,_|\__,_|\__\__,_|___/\__\___| .__/   \___\__,_|_|_| |_|  \___|_| |_| |_| .__/    \_/\_/ \___/|_|  |_|\_\___/
                                     |_|                                        |_|
    */

    data want;

      set sd1.have;
      _n_ = popAry (addrlong(val1));

    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* WORK.WANT total obs=1                                                                                                  */
    /*                                                                                                                        */
    /*                                       Set to zero                                                                      */
    /*                      ====================================================                                              */
    /* Obs    ID    TYPE    VAL1    VAL2    VAL3    VAL4    VAL5    VAL6    VAL7                                              */
    /*                                                                                                                        */
    /*  1      1     a        0       0       0       0       0       0       0                                               */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
et all values in an array to 0 without looping
