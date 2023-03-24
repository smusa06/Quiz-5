# Quiz 5
 libname quiz5  "/home/u62340691/EPILARGEDATA";
 
 
/*Save modified datasets in separate class directory*/
libname ex "/home/u62340691/EPILARGEDATA";

*Question 1.	Using the class datasets create a frequency table of blood-types based on lab tests ordered from 2003-2005.
Hints/Details:
•	Blood group typing lab test indicated by NSERVICE.SvcStmWID=10564. 
•	Date of lab test indicated by NSERVICE.svcOrderedDtm 
•	Result of blood typing is in NLABSERVICE.LabSingleResult. 
•	Lab tests in the NSERVICE table and the corresponding results in the NLABSERVICE table can be linked together by matching on svcWID/labSvcWID.
;

*LETS CHECK THE DATASETS OUT;
-proc contents data=quiz5.nlabservice varnum;
run;



proc contents data=quiz5.nservice varnum;
run;



*CREATE DATASET COPIES OF THE 2 DATASETS;

data LABSERVICECOPY;
set quiz5.nlabservice;
run;
* 1257239;

DATA NSERVICECOPY;
SET quiz5.nservice;
RUN;


*_______________________________________________________________;

*SORT DATAS BY THE UNIQUE IDENTIFIERS;

PROC SORT DATA= NSERVICECOPY;
BY svcWID;
RUN;

*RENAME DATA TO GIVE SIMILAR VAR NAME;
PROC SORT DATA= LABSERVICECOPY OUT=LABSERVICECOPY2 (RENAME=(LABSVCWID=SVCWID));
BY labSvcWID;
RUN;
*n=1257239;
*______________________________________________________________;


*MERGE THEM BY SVCWID/LABSVCWID;

DATA MERGED;
MERGE LABSERVICECOPY2 NSERVICECOPY;
BY SVCWID;
RUN;

* n=1257239;
*_____________________________CREATE NEW DATASET WITH BLOODTYPE ID and year between 2003-2005_______________________________;
DATA TRASH;
SET MERGED;
where (sVCSTMWID=10564) and 2003 <= year(datepart(svcOrderedDtm))<= 2005;
RUN;
*N=644;

*____________create subdataset to rename dates into just years so that the freq table is simpler and more summarised___________;

DATA TRy1;
SET trash;
if year(datepart(svcOrderedDtm)) = 2003 then serviceorderdate = 2003;
else if year(datepart(svcOrderedDtm)) = 2004 then serviceorderdate = 2004;
else if year(datepart(svcOrderedDtm)) = 2005 then serviceorderdate= 2005;
*else serviceorderdate = '.';
*N=644;
*____________FINAL FREQTABLE _____________;
PROC FREQ data= try1;
TABLES LabSingleResult*serviceorderdate;
* serviceorderdate;
run;

PROC FREQ data= try1;
TABLES LabSingleResult*serviceorderdate/ norow nocol nopercent;
* serviceorderdate;
run;