# utl-exploratory-forcasting-sunspot-activity-with-r-auto-arima-time-series
    %let pgm=utl-exploratory-forcasting-sunspot-activity-with-r-auto-arima-time-series;

    Exploratory forcasting sunspot activity with r auto arima function time series

    github
    http://tinyurl.com/3pm9zpuf
    https://github.com/rogerjdeangelis/utl-exploratory-forcasting-sunspot-activity-with-r-auto-arima-time-series

    Hi Resolution  output Graph
    http://tinyurl.com/3pm9zpuf
    https://github.com/rogerjdeangelis/utl-exploratory-forcasting-sunspot-activity-with-r-auto-arima-time-series

    Related repos
    https://github.com/rogerjdeangelis/utl_time_series_analysis_of_sunspots_in_sas_and_r
    https://github.com/rogerjdeangelis/utl-cubic-spline-interpolation-for-missing-values-in-a-time-series-by-group
    https://github.com/rogerjdeangelis/utl-fill-in-gaps-in-time-series-by-groups
    https://github.com/rogerjdeangelis/utl-forecast-the-next-four-months-using-a-moving-average-time-series
    https://github.com/rogerjdeangelis/utl-number-of-shoppers-in-store-every_5-minutes-time-series

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                  SUNSPOT ACTIVITY  AUTO REGRESSIVE 2 AR(2) MODEL                                                       */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*          1970      1980      1990      2000      2010      2020      2030                                              */
    /*         ---+---------+---------+---------+---------+---------+---------+---                                            */
    /*         |                                                                 |                                            */
    /*         |                  SUNSPOT ELEVEN YEAR CYCLE                      |                                            */
    /*         |                                                                 |                                            */
    /*         |    Note the downward linear trend is contiued in the forecast   |                                            */
    /*         |                                                                 |                                            */
    /*         |-------------------------------------------------+---------------|                                            */
    /*      15 +                         o-observed              |  p=forcasted  + 15                                         */
    /*         |                                                 |               |                                            */
    /*         |            |o                                   |               |                                            */
    /*         |            | o      o o                         |               |                                            */
    /*   O     |            |          |                         |               |                                            */
    /*   B     |            |         o|         o               |               |                                            */
    /*   S  10 +           oo          |        o|o              |               + 10                                         */
    /*   E     |            |          |o      o |               |               |                                            */
    /*   R     |            |       o  |         |           o   |    pp         |                                            */
    /*   V     |          o |  o       |         | o        |    |   p |p        |                                            */
    /*   E     |            |          |      o  |         oo  o |  p  | p       |                                            */
    /*   D     |            |   o      | o       |  o       |    | p   |  ppp    |                                            */
    /*       5 +            |          |         |   o      |    |     |         +  5                                         */
    /*         |         o  |          |  o      |          |   o|p    |         |                                            */
    /*         |            |    ooo   |   ooo   |    o   o |    p     |         |                                            */
    /*         |        o   |          |         |     ooo  |    |     |         |                                            */
    /*         |            |          |         |          |    |     |         |                                            */
    /*         |            |          |         |          |    |     |         |                                            */
    /*       0 +            |          |         |          |    |     |         +  0                                         */
    /*         |            |          |         |          |    |     |         |                                            */
    /*         ---+---------+---------+---------+---------+------+--+---------+---                                            */
    /*          1970      1980      1990      2000      2010      2020      2030                                              */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    %utl_submit_r64x('
    spots=c(2.9,4.2,7.6,9.9,9.9,13.1,12.1,7.6,5.9,3.7,3.5,3.7,8.4,12.8,
    11.2,12.7,8.9,5.8,4.4,3.7,3.1,3.6,6.6,9.3,10.1,10.5,9.8,7.1,
    5.9,4.7,3.5,2.7,2.5,2.5,3.4,6.7,6.7,6.9,8.0,6.4,3.9);
    yer<-c(1976:2016);
    yrspots<-as.data.frame(cbind(yer,spots));
    yrspots;
    unlink("d:/rds/spots.rds");
    saveRDS(yrspots,file="d:/rds/spots.rds");
    ');

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  d:/rds/spots.rds                                                                                                      */
    /*                                                                                                                        */
    /*      yer spots                                                                                                         */
    /*  1  1976   2.9                                                                                                         */
    /*  2  1977   4.2                                                                                                         */
    /*  3  1978   7.6                                                                                                         */
    /*  4  1979   9.9                                                                                                         */
    /*  5  1980   9.9                                                                                                         */
    /*  6  1981  13.1                                                                                                         */
    /*  7  1982  12.1                                                                                                         */
    /*  ...                                                                                                                   */
    /*  37 2012   6.7                                                                                                         */
    /*  38 2013   6.9                                                                                                         */
    /*  39 2014   8.0                                                                                                         */
    /*  40 2015   6.4                                                                                                         */
    /*  41 2016   3.9                                                                                                         */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */


    %utl_submit_r64x('
    library(SASxport);
    library(forecast);
    yrspots=readRDS("d:/rds/spots.rds");
    yrspots;
    fit = auto.arima(yrspots[,2]);
    summary(fit);
    str(fit);
    pdf("d:/pdf/autoarima.pdf");
    fcast <- forecast(fit, h=12);
    plot(fcast);
    coef  <-fit$coef;
    fitres<-as.data.frame(cbind(yer=yrspots[,1],observed=fit$x,fitted=fit$fitted,residual=fit$residuals));
    uni   <-as.data.frame(t(c(loglik=fit$loglik,sigma2=fit$sigma2,aic=fit$aic,nobs=fit$nobs,bic=fit$bic,aicc=fit$aicc)));
    vec   <-as.data.frame(rbind(t(fit$model$phi),t(fit$mode$a)));
    colnames(vec)=c("phi","a");
    vec;
    matP  <-as.data.frame(fit$model$P);
    matT  <-as.data.frame(fit$model$T);
    matV  <-as.data.frame(fit$model$V);
    matPn <-as.data.frame(fit$model$Pn);
    forcst<-cbind(yer=c(2017:2028),as.data.frame(fcast));
    coef<-as.data.frame(coef);
    write.xport(uni,vec,coef,fitres,forcst,matP,matT,matV,matPn,file="d:/xpt/lib.xpt");
    ');

    libname xpt xport "d:/xpt/lib.xpt";
    proc contents data=xpt._all_;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  PRINTED OUTPUT                                                                                                        */
    /*                                                                                                                        */
    /*  Coefficients:                                                                                                         */
    /*           ar1      ar2      ma1    mean                                                                                */
    /*        1.5251  -0.8157  -0.4799  6.6044                                                                                */
    /*  s.e.  0.1047   0.0902   0.1430  0.4415                                                                                */
    /*                                                                                                                        */
    /*  sigma^2 = 2.55:  log likelihood = -76.32                                                                              */
    /*  AIC=162.63   AICc=164.35   BIC=171.2                                                                                  */
    /*                                                                                                                        */
    /*  Training set error measures:                                                                                          */
    /*                      ME     RMSE      MAE       MPE    MAPE      MASE                                                  */
    /*  Training set 0.0226336 1.517066 1.200728 -5.934932 20.5228 0.7189988                                                  */
    /*                      ACF1                                                                                              */
    /*  Training set -0.07837561                                                                                              */
    /*                                                                                                                        */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                                                                                        */
    /*  DATAFRAMES AND V5 export files                                                                                        */
    /*                                                                                                                        */
    /*  Physical Name  d:\xpt\lib.xpt                                                                                         */
    /*                                                                                                                        */
    /*             Member                                                                                                     */
    /*  #  Name    Type     Vars                                                                                              */
    /*                                                                                                                        */
    /*  1  UNI     DATA      6     Overall fit AIC log likelyhood                                                             */
    /*  2  VEC     DATA      2     Phi values (measure of randomness)                                                         */
    /*  3  COEF    DATA      1     Coeficeents of model intercept betas mean                                                  */
    /*  4  FITRES  DATA      4     Fit to observed data                                                                       */
    /*  5  FORCST  DATA      6     predicted 2017-3038                                                                        */
    /*                                                                                                                        */
    /*  6  MATP    DATA      2     Intermediate matrices                                                                      */
    /*  7  MATT    DATA      2                                                                                                */
    /*  8  MATV    DATA      2                                                                                                */
    /*  9  MATPN   DATA      2                                                                                                */
    /*                                                                                                                        */
    /*  libname xpt xport "d:/xpt/lib.xpt";                                                                                   */
    /*                                                                                                                        */
    /*  proc print data=xpt.uni;run;quit;                                                                                     */
    /*  proc print data=xpt.vec;run;quit;                                                                                     */
    /*  proc print data=xpt.coef;run;quit;                                                                                    */
    /*  proc print data=xpt.fitres;run;quit;                                                                                  */
    /*  proc print data=xpt.forcst;run;quit;                                                                                  */
    /*                                                                                                                        */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                                                                                        */
    /*  UNI                              fit                                                                                  */
    /*  ===                            smaller is                                                                             */
    /*                            better for vomparing                                                                        */
    /*  Obs     LOGLIK      SIGMA2      AIC            NOBS      BIC        AICC                                              */
    /*                                                                                                                        */
    /*    1    -76.3161    2.55030    162.632            41     171.200    164.346                                            */
    /*                                                                                                                        */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                                                                                        */
    /*  VEC                                                                                                                   */
    /*  ===                                                                                                                   */
    /*  Obs       PHI          A                                                                                              */
    /*                                                                                                                        */
    /*    1     1.52513    -0.81574   Phi near 0 indicates a random ptocess                                                   */
    /*    2    -2.70442     1.17936                                                                                           */
    /*                                                                                                                        */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                                                                                        */
    /*  COEF                                                                                                                  */
    /*  ====                                                                                                                  */
    /*  Obs      COEF                                                                                                         */
    /*                                                                                                                        */
    /*    1     1.52513   intercept                                                                                           */
    /*    2    -0.81574    beta(t-1)                                                                                          */
    /*    3    -0.47990    beta(t-2)                                                                                          */
    /*    4     6.60442    mean                                                                                               */
    /*                                                                                                                        */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                                                                                        */
    /*  FITRES                                                                                                                */
    /*  ======                                                                                                                */
    /*                                                                                                                        */
    /*   Obs     YER    OBSERVED     FITTED    RESIDUAL                                                                       */
    /*                                                                                                                        */
    /*     1    1976       2.9       4.6854    -1.78544                                                                       */
    /*     2    1977       4.2       3.8316     0.36837                                                                       */
    /*     3    1978       7.6       5.9019     1.69813                                                                       */
    /*     4    1979       9.9       9.3111     0.58894                                                                       */
    /*     5    1980       9.9      10.5372    -0.63717                                                                       */
    /*   ...                                                                                                                  */
    /*    39    2014       8.0      7.35748     0.64252                                                                       */
    /*    40    2015       6.4      8.18339    -1.78339                                                                       */
    /*    41    2016       3.9      6.01006    -2.11006                                                                       */
    /*                                                                                                                        */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                                                                                        */
    /*  FORCST                                                                                                                */
    /*  ======                                                                                                                */
    /*                                                                                                                        */
    /*  Obs     YER    POINT_FO     LO_80      HI_80       LO_95      HI_95                                                   */
    /*                                                                                                                        */
    /*    1    2017     3.65920    1.61261     5.7058     0.52921     6.7892                                                  */
    /*    2    2018     4.31870    1.35820     7.2792    -0.20900     8.8464                                                  */
    /*    3    2019     5.52095    2.15907     8.8828     0.37940    10.6625                                                  */
    /*    4    2020     6.81656    3.38570    10.2474     1.56951    12.0636                                                  */
    /*    5    2021     7.81179    4.37143    11.2521     2.55022    13.0734                                                  */
    /*    6    2022     8.27276    4.70416    11.8414     2.81506    13.7305                                                  */
    /*    7    2023     8.16395    4.38687    11.9410     2.38741    13.9405                                                  */
    /*    8    2024     7.62196    3.68404    11.5599     1.59943    13.6445                                                  */
    /*    9    2025     6.88412    2.88630    10.8819     0.76999    12.9983                                                  */
    /*   10    2026     6.20095    2.20058    10.2013     0.08291    12.3190                                                  */
    /*   11    2027     5.76091    1.74572     9.7761    -0.37979    11.9016                                                  */
    /*   12    2028     5.64709    1.58088     9.7133    -0.57163    11.8658                                                  */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                  _               _     _ _     _
     _ __    ___  _   _| |_ _ __  _   _| |_  | (_)___| |_
    | `__|  / _ \| | | | __| `_ \| | | | __| | | / __| __|
    | |    | (_) | |_| | |_| |_) | |_| | |_  | | \__ \ |_
    |_|     \___/ \__,_|\__| .__/ \__,_|\__| |_|_|___/\__|
                           |_|
    */

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  List of 18                                                                                                            */
    /*   $ coef     : Named num [1:4] 1.525 -0.816 -0.48 6.604                                                                */
    /*    ..- attr(*, "names")= chr [1:4] "ar1" "ar2" "ma1" "intercept"                                                       */
    /*   $ sigma2   : num 2.55                                                                                                */
    /*   $ var.coef : num [1:4, 1:4] 0.010962 -0.007913 -0.008567 -0.000715 -0.007913 ...                                     */
    /*    ..- attr(*, "dimnames")=List of 2                                                                                   */
    /*    .. ..$ : chr [1:4] "ar1" "ar2" "ma1" "intercept"                                                                    */
    /*    .. ..$ : chr [1:4] "ar1" "ar2" "ma1" "intercept"                                                                    */
    /*   $ mask     : logi [1:4] TRUE TRUE TRUE TRUE                                                                          */
    /*   $ loglik   : num -76.3                                                                                               */
    /*   $ aic      : num 163                                                                                                 */
    /*   $ arma     : int [1:7] 2 1 0 0 1 0 0                                                                                 */
    /*   $ residuals: Time-Series [1:41] from 1 to 41: -1.785 0.368 1.698 0.589 -0.637 ...                                    */
    /*   $ call     : language auto.arima(y = yrspots[, 2], x = list(x = c(2.9, 4.2, 7.6, 9.9, 9.9, 13.1,  1                  */
    /*  2.1, 7.6, 5.9, 3.7, 3.5, 3.7, 8.4, 1| __truncated__ ...                                                               */
    /*   $ series   : chr "yrspots[, 2]"                                                                                      */
    /*   $ code     : int 0                                                                                                   */
    /*   $ n.cond   : int 0                                                                                                   */
    /*   $ nobs     : int 41                                                                                                  */
    /*   $ model    :List of 10                                                                                               */
    /*    ..$ phi  : num [1:2] 1.525 -0.816                                                                                   */
    /*    ..$ theta: num -0.48                                                                                                */
    /*    ..$ Delta: num(0)                                                                                                   */
    /*    ..$ Z    : num [1:2] 1 0                                                                                            */
    /*    ..$ a    : num [1:2] -2.7 1.18                                                                                      */
    /*    ..$ P    : num [1:2, 1:2] 0 0 0 0                                                                                   */
    /*    ..$ T    : num [1:2, 1:2] 1.525 -0.816 1 0                                                                          */
    /*    ..$ V    : num [1:2, 1:2] 1 -0.48 -0.48 0.23                                                                        */
    /*    ..$ h    : num 0                                                                                                    */
    /*    ..$ Pn   : num [1:2, 1:2] 1 -0.48 -0.48 0.23                                                                        */
    /*   $ bic      : num 171                                                                                                 */
    /*   $ aicc     : num 164                                                                                                 */
    /*   $ x        : Time-Series [1:41] from 1 to 41: 2.9 4.2 7.6 9.9 9.9 13.1 12.1 7.6 5.9 3.7 ...                          */
    /*   $ fitted   : Time-Series [1:41] from 1 to 41: 4.69 3.83 5.9 9.31 10.54 ...                                           */
    /*   - attr(*, "class")= chr [1:3] "forecast_ARIMA" "ARIMA" "Arima"                                                       */
    /*          phi          a                                                                                                */
    /*  1  1.525128 -0.8157444                                                                                                */
    /*  2 -2.704420  1.1793645                                                                                                */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
 
