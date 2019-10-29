*read raw data;
%macro state;
%let statelist =
30440 30441 30442 30570 30571 30572 96972;
%local p statelist;
%do p=1 %to %sysfunc(countw(&statelist));
%let sl = %scan(&statelist,&p);
	%put &sl;

data edotpt&sl.;
set "C:\Users\xux67\Downloads\sas codes\eddiagfreq&sl..sas7bdat";
run;

data ipotpt&sl.;
set "C:\Users\xux67\Downloads\sas codes\ipdiagfreq&sl..sas7bdat";
run;
data edotpt&sl.;
set edotpt&sl.;
count1=servicecount;
drop servicecount;
run;
data ipotpt&sl.;
set ipotpt&sl.;
count1=servicecount;
drop servicecount;
run;
%end;
%mend;
%state

*append datasets of each diagnosis into one single dataset;
%macro state;
%let statelist =
30440 30441 30442 30570 30571 30572 96972;
%local p statelist;
%do p=1 %to %sysfunc(countw(&statelist));
%let sl = %scan(&statelist,&p);
	%put &sl;
%if &p=1 %then %do;
data edotpt;
set edotpt&sl.;
run;
data ipotpt;
set ipotpt&sl.;
run;
%end;

%else %do;
data edotpt;
set edotpt edotpt&sl.;
run;
data ipotpt;
set ipotpt ipotpt&sl.;
run;
%end;

%end;

%mend;
%state

*get the sum of count of services of all diagnoses;
proc sql;
create table edotptfreq as
select sum(count1) as cnt, year, county, month
from edotpt
group by year, county, month;
quit;

proc sql;
create table ipotptfreq as
select sum(count1) as cnt, year, county, month
from ipotpt
group by year, county, month;
quit;

data ipotptfreq;
set ipotptfreq;
if county="" then delete;
run;

data edotptfreq;
set edotptfreq;
if county="" then delete;
run;

*save as xlsx files;
PROC EXPORT data= edotptfreq OUTFILE="c:\Users\xux67\Downloads\ED_each_diag_cnt_0916.xlsx" 
            DBMS=xlsx 
			REPLACE;
     SHEET="ED"; 
RUN;

PROC EXPORT data= ipotptfreq OUTFILE="c:\Users\xux67\Downloads\IP_each_diag_cnt_0916.xlsx" 
            DBMS=xlsx 
			REPLACE;
     SHEET="IP"; 
RUN;


PROC import out= edotptany dataFILE="c:\Users\xux67\Downloads\ED_each_diag_cnt_0916adj.xlsx" 
            DBMS=xlsx 
			REPLACE;
     SHEET="ED"; 
RUN;

PROC import out= ipotptany dataFILE="c:\Users\xux67\Downloads\IP_each_diag_cnt_0916adj.xlsx" 
            DBMS=xlsx 
			REPLACE;
     SHEET="IP"; 
RUN;

*change the layout (long to wide);
%macro state;
%let statelist =
30440 30441 30442 30570 30571 30572 96972 any;
%local p statelist;
%do p=1 %to %sysfunc(countw(&statelist));
%let sl = %scan(&statelist,&p);
	%put &sl;
data edotpt1&sl;
set edotpt&sl;
rename count1=cnt;
drop diag;
run;

data edotpt1&sl;
set edotpt1&sl;
rename cnt=cnt_&sl.;
run;

%end;

%mend;
%state

%macro state;
%let statelist =
30440 30441 30442 30570 30571 30572 96972;
%local p statelist;
%do p=1 %to %sysfunc(countw(&statelist));
%let sl = %scan(&statelist,&p);
	%put &sl;
data edotpt1&sl;
set edotpt&sl;
rename count1=cnt;
drop diag;
run;

data edotpt1&sl;
set edotpt1&sl;
rename cnt=cnt_&sl.;
run;

data ipotpt1&sl;
set ipotpt&sl;
rename count1=cnt;
drop diag;
run;

data ipotpt1&sl;
set ipotpt1&sl;
rename cnt=cnt_&sl.;
run;
proc sort data=ipotpt1&sl.;
by year county month;
run;
proc sort data=edotpt1&sl.;
by year county month;
run;
%end;

%mend;
%state

proc sort data=edotptany;
by year county month;
run;

data alledfreq;
merge edotpt130440 edotpt130441 edotpt130442 edotpt130570 edotpt130571 edotpt130572 edotpt196972 edotptany;
by year county month;
run;
proc sort data=ipotptany;
by year county month;
run;

data allipfreq;
merge ipotpt130440 ipotpt130441 ipotpt130442 ipotpt130570 ipotpt130571 ipotpt130572 ipotpt196972 ipotptany;
by year county month;
run;

data allipfreq;
set allipfreq;
if cnt=. then delete;
run;

data alledfreq;
set alledfreq;
if cnt=. then delete;
run;

*output and save as xlsx files;

PROC EXPORT data= allipfreq OUTFILE="c:\Users\xux67\Downloads\All_IP_freq_0916.xlsx" 
            DBMS=xlsx 
			REPLACE;
     SHEET="IP"; 
RUN;

PROC EXPORT data= alledfreq OUTFILE="c:\Users\xux67\Downloads\All_ED_freq_0916.xlsx" 
            DBMS=xlsx 
			REPLACE;
     SHEET="ED"; 
RUN;
