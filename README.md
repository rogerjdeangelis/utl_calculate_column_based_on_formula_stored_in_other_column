# utl_calculate_column_based_on_formula_stored_in_other_column
Calculate column based on formula stored in other column.   Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Calculate column based on formula stored in other column

    see github
    https://tinyurl.com/y8dujfrr
    https://github.com/rogerjdeangelis/utl_calculate_column_based_on_formula_stored_in_other_column

    see
    https://tinyurl.com/yb2df72p
    https://stackoverflow.com/questions/51899432/r-calculate-column-based-on-formula-stored-in-other-column


      TWO SOLUTIONS

          1. R
          2. SAS

    This has to be a small data problem. I doubt you could have
    100,000,000 different formulas. This code should be competitive?


    INPUT
    =====

     SD1.HAVE total obs=12
                                                                      | RULES
                                    EFF_            LIST_    FORMER_  | =====
                FORMULA            PRICE     QTY    PRICE     PRICE   | (EFF_PRICE-LIST_PRICE)*QTY
                                                                      |
     (EFF_PRICE-LIST_PRICE)*QTY     10.0     100    12.00      0.00   | -200 = (10 -  12) * 100
     (EFF_PRICE-LIST_PRICE)*QTY      0.6    1000     0.85      0.65   | -250 = (.6 - .85) *1000
     (EFF_PRICE-LIST_PRICE)*QTY      0.6    1000     0.85      0.65   |
     (EFF_PRICE-LIST_PRICE)*QTY      0.6    1000     0.85      0.65   |
     (EFF_PRICE-LIST_PRICE)*QTY      0.6    1000     0.85      0.65   |
     (EFF_PRICE-LIST_PRICE)*QTY      0.6    1000     0.85      0.65   |
     (EFF_PRICE-LIST_PRICE)*QTY      0.6    1000     0.85      0.65   |
                                                                      |
     (FORMER_PRICE-LIST_PRICE)*QTY   6.0     100     7.00      5.50   | (FORMER_PRICE-LIST_PRICE)*QTY
     (FORMER_PRICE-LIST_PRICE)*QTY   6.0     100     7.00      5.50   | -150 = (5.5 - 7) * 100
     (FORMER_PRICE-LIST_PRICE)*QTY   6.0     100     7.00      5.50   |
     (FORMER_PRICE-LIST_PRICE)*QTY   6.0     100     7.00      5.50   |
     (FORMER_PRICE-LIST_PRICE)*QTY   6.0     100     7.00      5.50   |


    PROCESS
    =======

      1. R (working code see below for full solution)

         have<-read_sas("d:/sd1/have.sas7bdat");
         attach(have);
         have$NEWVAR<-eval(parse(text = have$FORMULA));

      2. SAS

         data _null_;
           set sd1.have;
           by formula;
           if last.formula then do;
              call symputx('formula',formula);
              rc=dosubl('
               data catch;
                 set sd1.have(where=(formula="&formula"));
                 newVar=&formula;
               run;quit;
               proc append base=want data=catch;
               run;quit;
             ');
           end;
         run;quit;


    OUTPUT
    ======

     WORK.WANT total obs=12

                                                    EFF_            LIST_    FORMER_
                 FORMULA                DIN_PIN    PRICE     QTY    PRICE     PRICE     NEWVAR

      (EFF_PRICE-LIST_PRICE)*QTY       12345678     10.0     100    12.00      0.00      -200
      (EFF_PRICE-LIST_PRICE)*QTY           2789      0.6    1000     0.85      0.65      -250
      (EFF_PRICE-LIST_PRICE)*QTY           2789      0.6    1000     0.85      0.65      -250
      (EFF_PRICE-LIST_PRICE)*QTY           2789      0.6    1000     0.85      0.65      -250
      (EFF_PRICE-LIST_PRICE)*QTY           2789      0.6    1000     0.85      0.65      -250
      (EFF_PRICE-LIST_PRICE)*QTY           2789      0.6    1000     0.85      0.65      -250
      (EFF_PRICE-LIST_PRICE)*QTY           2789      0.6    1000     0.85      0.65      -250

      (FORMER_PRICE-LIST_PRICE)*QTY    23456789      6.0     100     7.00      5.50      -150
      (FORMER_PRICE-LIST_PRICE)*QTY    23456789      6.0     100     7.00      5.50      -150
      (FORMER_PRICE-LIST_PRICE)*QTY    23456789      6.0     100     7.00      5.50      -150
      (FORMER_PRICE-LIST_PRICE)*QTY    23456789      6.0     100     7.00      5.50      -150
      (FORMER_PRICE-LIST_PRICE)*QTY    23456789      6.0     100     7.00      5.50      -150

    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;


    proc datasets lib=work kill;
    run;quit;

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have(index=(formula)); ** aded an index -> probably not needed;
        retain formula;
        input din_pin       :8.
              eff_price     :9.4
              qty           :8.
              list_price    :9.4
              former_price  :9.4
              formula       :$50.
        ;
    cards4;
    12345678 10.0 100 12.00 0.00 (EFF_PRICE-LIST_PRICE)*QTY
    2789 0.6 1000 0.85 0.65 (EFF_PRICE-LIST_PRICE)*QTY
    2789 0.6 1000 0.85 0.65 (EFF_PRICE-LIST_PRICE)*QTY
    2789 0.6 1000 0.85 0.65 (EFF_PRICE-LIST_PRICE)*QTY
    2789 0.6 1000 0.85 0.65 (EFF_PRICE-LIST_PRICE)*QTY
    2789 0.6 1000 0.85 0.65 (EFF_PRICE-LIST_PRICE)*QTY
    2789 0.6 1000 0.85 0.65 (EFF_PRICE-LIST_PRICE)*QTY
    23456789 6.0 100 7.00 5.50 (FORMER_PRICE-LIST_PRICE)*QTY
    23456789 6.0 100 7.00 5.50 (FORMER_PRICE-LIST_PRICE)*QTY
    23456789 6.0 100 7.00 5.50 (FORMER_PRICE-LIST_PRICE)*QTY
    23456789 6.0 100 7.00 5.50 (FORMER_PRICE-LIST_PRICE)*QTY
    23456789 6.0 100 7.00 5.50 (FORMER_PRICE-LIST_PRICE)*QTY
    ;;;;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __  ___
    / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/

    ;

    1. R
    ====

    %utl_submit_r64('
    library(haven);
    have<-read_sas("d:/sd1/have.sas7bdat");
    attach(have);
    have$NEWVAR<-eval(parse(text = have$FORMULA));
    save.image("d:/rda/work.Rdata");
    ');

    * create sas dataset from R dataframe;
    proc iml;
      submit / R;
      load("d:/rda/work.Rdata");
      endsubmit;
      run importdatasetfromr("work.want_r","have");
    run;quit;

    proc print data=want_r;
    run;quit;

    2. SAS
    ======

    data _null_;
      set sd1.have;
      by formula;
      if last.formula then do;
         call symputx('formula',formula);
         rc=dosubl('
          data catch;
            set sd1.have(where=(formula="&formula"));
            newVar=&formula;
          run;quit;
          proc append base=want data=catch;
          run;quit;
        ');
      end;
    run;quit;

    proc print data=want;
    run;quit;

