# utl_randomly_shuffle_the_rows_of_a_table
Randomly shuffle the rows of a table.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.


    Randomly shuffle the rows of a table

    WPS Proc R
    
    ee  Paul's Hash solution on end
    Paul Dorfman
    sashole@bellsouth.net

    see
    https://tinyurl.com/y7bs4tco
    https://stackoverflow.com/questions/51332793/how-can-i-randomize-a-csv-file-in-r

    Sowmya S Manian
    https://stackoverflow.com/users/5371744/sowmya-s-manian

    INPUT
    =====

     SD1.HAVE total obs=19

       SEQ    NAME       SEX    AGE    HEIGHT    WEIGHT

         1    Alfred      M      14     69.0      112.5
         2    Alice       F      13     56.5       84.0
         3    Barbara     F      13     65.3       98.0
         4    Carol       F      14     62.8      102.5
         5    Henry       M      14     63.5      102.5
        ...

     EXAMPLE OUTPUT
     --------------

      WORK.WANT total obs=19

       SEQ    NAME       SEX    AGE    HEIGHT    WEIGHT

        16    Robert      M      12     64.8      128.0
         1    Alfred      M      14     69.0      112.5
        12    Judy        F      14     64.3       90.0
        17    Ronald      M      15     67.0      133.0
         9    Jeffrey     M      13     62.5       84.0


    PROCESS (WORKING CODE)
    ======================

        want<-have[sample(1:nrow(have)),];


    OUTPUT
    ======

     WORK.WANT total obs=19

      SEQ    NAME       SEX    AGE    HEIGHT    WEIGHT

       16    Robert      M      12     64.8      128.0
        1    Alfred      M      14     69.0      112.5
       12    Judy        F      14     64.3       90.0
       17    Ronald      M      15     67.0      133.0
        9    Jeffrey     M      13     62.5       84.0
        2    Alice       F      13     56.5       84.0
       19    William     M      15     66.5      112.0
       14    Mary        F      15     66.5      112.0
        7    Jane        F      12     59.8       84.5
        4    Carol       F      14     62.8      102.5
        5    Henry       M      14     63.5      102.5
        8    Janet       F      15     62.5      112.5
        6    James       M      12     57.3       83.0
       10    John        M      12     59.0       99.5
       11    Joyce       F      11     51.3       50.5
       18    Thomas      M      11     57.5       85.0
       13    Louise      F      12     56.3       77.0
        3    Barbara     F      13     65.3       98.0
       15    Philip      M      16     72.0      150.0


    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
      retain seq;
      set sashelp.class;
      seq=_n_;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    %utl_submit_wps64('
    libname sd1 "d:/sd1";
    options set=R_HOME "C:/Program Files/R/R-3.3.2";
    libname wrk  sas7bdat "%sysfunc(pathname(work))";
    proc r;
    submit;
    source("C:/Program Files/R/R-3.3.2/etc/Rprofile.site", echo=T);
    library(haven);
    have<-read_sas("d:/sd1/have.sas7bdat");
    head(have);
    want<-have[sample(1:nrow(have)),];
    endsubmit;
    import r=want data=wrk.want;
    run;quit;
    ');


    s

    *____             _
    |  _ \ __ _ _   _| |
    | |_) / _` | | | | |
    |  __/ (_| | |_| | |
    |_|   \__,_|\__,_|_|

    ;


    Roger,

    A nice one-liner. Here's another one (sans proc and quit):

    proc sql ;
      create table want as select * from sashelp.class order ranuni(1) ;
    quit ;

    OTOH, advantage can be taken of the fact that the items in a hash object instance are stored randomly by their key-values, so we can simply "extrude" the data set through the h

    data _null_ ;
      dcl hash h (dataset:"sashelp.class") ;
      h.definekey  (all:"y") ;
      h.definedata (all:"y") ;
      h.definedone () ;
      h.output (dataset:"want") ;
      stop ;
      set sashelp.class ;
    run ;

    However, it lets the distribution to its own internal devices. Instead, we can shuffle more pointedly:

    data want ;
      dcl hash h (ordered:"a") ;
      h.definekey ("_iorc_") ;
      h.definedata ("_n_") ;
      h.definedone () ;
      dcl hiter ih ("h") ;
      do _n_ = 1 to n ;
        _iorc_ = ranuni(1) ;
        h.add() ;
      end ;
      do while (ih.next() = 0) ;
        set sashelp.class point=_n_ nobs=n ;
        output ;
      end ;
      stop ;
    run ;

    Best regards



