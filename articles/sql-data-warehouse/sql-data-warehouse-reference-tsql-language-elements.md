<properties
   pageTitle="Transact-SQL-Sprachelemente für SQL Data Warehouse | Microsoft Azure"
   description="Eine Liste mit Links zu Referenzinhalten für die Transact-SQL-Sprachelemente für SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/28/2016"
   ms.author="barbkess;sonyama"/>

# Sprachelemente

## Kernelemente

- [Syntaxkonventionen](https://msdn.microsoft.com/library/ms177563.aspx)
- [Benennungsregeln für Objekte](https://msdn.microsoft.com/library/ms175874.aspx)
- [Reservierte Schlüsselwörter](https://msdn.microsoft.com/library/ms189822.aspx)
- [Sortierungen](https://msdn.microsoft.com/library/ff848763.aspx)
- [Kommentare](https://msdn.microsoft.com/library/ms181627.aspx)
- [Konstanten](https://msdn.microsoft.com/library/ms179899.aspx)
- [Datentypen](https://msdn.microsoft.com/library/ms187752.aspx)
- [EXECUTE](https://msdn.microsoft.com/library/ms188332.aspx)
- [Ausdrücke](https://msdn.microsoft.com/library/ms190286.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx)
- [Problemumgehung für die IDENTITY-Eigenschaft](https://msdn.microsoft.com/library/ms186775.aspx)
- [PRINT](https://msdn.microsoft.com/library/ms176047.aspx)
- [USE](https://msdn.microsoft.com/library/ms188366.aspx)

## Batches, Ablaufsteuerung und Variablen

- [BEGIN...END](https://msdn.microsoft.com/library/ms190487.aspx)
- [BREAK](https://msdn.microsoft.com/library/ms181271.aspx)
- [DECLARE @local\_variable](https://msdn.microsoft.com/library/ms188927.aspx)
- [IF...ELSE](https://msdn.microsoft.com/library/ms182717.aspx)
- [RAISERROR](https://msdn.microsoft.com/library/ms178592.aspx)
- [SET@local\_variable](https://msdn.microsoft.com/library/ms189484.aspx)
- [THROW](https://msdn.microsoft.com/library/ee677615.aspx)
- [TRY...CATCH](https://msdn.microsoft.com/library/ms175976.aspx)
- [WHILE](https://msdn.microsoft.com/library/ms178642.aspx)

## Operatoren
- [\+ (Addition)](https://msdn.microsoft.com/library/ms178565.aspx)
- [\+ (Verketten von Zeichenfolgen)](https://msdn.microsoft.com/library/ms177561.aspx)
- [- (Negativ)](https://msdn.microsoft.com/library/ms189480.aspx)
- [- (Subtraktion)](https://msdn.microsoft.com/library/ms189518.aspx)
- [* (Multiplikation)](https://msdn.microsoft.com/library/ms176019.aspx)
- [/ (Division)](https://msdn.microsoft.com/library/ms175009.aspx)
- [Modulo](https://msdn.microsoft.com/library/ms190279.aspx)

## Platzhalterzeichen für den Abgleich
- [= (Gleich)](https://msdn.microsoft.com/library/ms175118.aspx)
- [> (Größer als)](https://msdn.microsoft.com/library/ms178590.aspx)
- [< (Kleiner als)](https://msdn.microsoft.com/library/ms179873.aspx)
- [>= (Größer als oder gleich)](https://msdn.microsoft.com/library/ms181567.aspx)
- [<= (Kleiner als oder gleich)](https://msdn.microsoft.com/library/ms174978.aspx)
- [<> (Ungleich)](https://msdn.microsoft.com/library/ms176020.aspx)
- [!= (Ungleich)](https://msdn.microsoft.com/library/ms190296.aspx)
- [AND](https://msdn.microsoft.com/library/ms188372.aspx)
- [BETWEEN](https://msdn.microsoft.com/library/ms187922.aspx)
- [EXISTS](https://msdn.microsoft.com/library/ms188336.aspx)
- [IN](https://msdn.microsoft.com/library/ms177682.aspx)
- [IS [NOT] NULL](https://msdn.microsoft.com/library/ms188795.aspx)
- [LIKE](https://msdn.microsoft.com/library/ms179859.aspx)
- [NOT](https://msdn.microsoft.com/library/ms189455.aspx)
- [OR](https://msdn.microsoft.com/library/ms188361.aspx)

### Bitweise Operatoren

- [& (Bitweises AND)](https://msdn.microsoft.com/library/ms174965.aspx)
- [| (Bitweises OR)](https://msdn.microsoft.com/library/ms186714.aspx)
- [^ (Bitweises exklusives OR)](https://msdn.microsoft.com/library/ms190277.aspx)
- [~ (Bitweises NOT)](https://msdn.microsoft.com/library/ms173468.aspx)
- [^= (Bitweises exklusives OR EQUALS)](https://msdn.microsoft.com/library/cc627413.aspx)
- [| = (Bitweises OR EQUALS)](https://msdn.microsoft.com/library/cc627409.aspx)
- [&= (Bitweises AND EQUALS)](https://msdn.microsoft.com/library/cc627427.aspx)

## Functions

- [@@DATEFIRST](https://msdn.microsoft.com/library/ms187766.aspx)
- [@@ERROR](https://msdn.microsoft.com/library/ms188790.aspx)
- [@@LANGUAGE](https://msdn.microsoft.com/library/ms177557.aspx)
- [@@SPID](https://msdn.microsoft.com/library/ms189535.aspx)
- [@@TRANCOUNT](https://msdn.microsoft.com/library/ms187967.aspx)
- [@@VERSION](https://msdn.microsoft.com/library/ms177512.aspx)
- [ABS](https://msdn.microsoft.com/library/ms189800.aspx)
- [ACOS](https://msdn.microsoft.com/library/ms178627.aspx)
- [ASCII](https://msdn.microsoft.com/library/ms177545.aspx)
- [ASIN](https://msdn.microsoft.com/library/ms181581.aspx)
- [ATAN](https://msdn.microsoft.com/library/ms181746.aspx)
- [ATN2](https://msdn.microsoft.com/library/ms173854.aspx)
- [BINARY\_CHECKSUM](https://msdn.microsoft.com/library/ms173784.aspx)
- [CASE](https://msdn.microsoft.com/library/ms181765.aspx)
- [CAST und CONVERT](https://msdn.microsoft.com/library/ms187928.aspx)
- [CEILING](https://msdn.microsoft.com/library/ms189818.aspx)
- [CHAR](https://msdn.microsoft.com/library/ms187323.aspx)
- [CHARINDEX](https://msdn.microsoft.com/library/ms186323.aspx)
- [CHECKSUM](https://msdn.microsoft.com/library/ms189788.aspx)
- [COALESCE](https://msdn.microsoft.com/library/ms190349.aspx)
- [COL\_NAME](https://msdn.microsoft.com/library/ms174974.aspx)
- [COLLATIONPROPERTY](https://msdn.microsoft.com/library/ms190305.aspx)
- [CONCAT](https://msdn.microsoft.com/library/hh231515.aspx)
- [COS](https://msdn.microsoft.com/library/ms188919.aspx)
- [COT](https://msdn.microsoft.com/library/ms188921.aspx)
- [COUNT](https://msdn.microsoft.com/library/ms175997.aspx)
- [COUNT\_BIG](https://msdn.microsoft.com/library/ms190317.aspx)
- [CUME\_DIST](https://msdn.microsoft.com/library/hh231078.aspx)
- [CURRENT\_TIMESTAMP](https://msdn.microsoft.com/library/ms188751.aspx)
- [CURRENT\_USER](https://msdn.microsoft.com/library/ms176050.aspx)
- [DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx)
- [DATALENGTH](https://msdn.microsoft.com/library/ms173486.aspx)
- [DATEADD](https://msdn.microsoft.com/library/ms186819.aspx)
- [DATEDIFF](https://msdn.microsoft.com/library/ms189794.aspx)
- [DATEFROMPARTS](https://msdn.microsoft.com/library/hh213228.aspx)
- [DATENAME](https://msdn.microsoft.com/library/ms174395.aspx)
- [DATEPART](https://msdn.microsoft.com/library/ms174420.aspx)
- [DATETIME2FROMPARTS](https://msdn.microsoft.com/library/hh213312.aspx)
- [DATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213233.aspx)
- [DATETIMEOFFSETFROMPARTS](https://msdn.microsoft.com/library/hh231077.aspx)
- [DAY](https://msdn.microsoft.com/library/ms176052.aspx)
- [DB\_ID](https://msdn.microsoft.com/library/ms186274.aspx)
- [DB\_NAME](https://msdn.microsoft.com/library/ms189753.aspx)
- [DEGREES](https://msdn.microsoft.com/library/ms178566.aspx)
- [DENSE\_RANK](https://msdn.microsoft.com/library/ms173825.aspx)
- [DIFFERENCE](https://msdn.microsoft.com/library/ms188753.aspx)
- [EOMONTH](https://msdn.microsoft.com/library/hh213020.aspx)
- [ERROR\_MESSAGE](https://msdn.microsoft.com/library/ms190358.aspx)
- [ERROR\_NUMBER](https://msdn.microsoft.com/library/ms175069.aspx)
- [ERROR\_PROCEDURE](https://msdn.microsoft.com/library/ms188398.aspx)
- [ERROR\_SEVERITY](https://msdn.microsoft.com/library/ms178567.aspx)
- [ERROR\_STATE](https://msdn.microsoft.com/library/ms180031.aspx)
- [EXP](https://msdn.microsoft.com/library/ms179857.aspx)
- [FIRST\_VALUE](https://msdn.microsoft.com/library/hh213018.aspx)
- [FLOOR](https://msdn.microsoft.com/library/ms178531.aspx)
- [GETDATE](https://msdn.microsoft.com/library/ms188383.aspx)
- [GETUTCDATE](https://msdn.microsoft.com/library/ms178635.aspx)
- [HAS\_DBACCESS](https://msdn.microsoft.com/library/ms187718.aspx)
- [HASHBYTES](https://msdn.microsoft.com/library/ms174415.aspx)
- [INDEXPROPERTY](https://msdn.microsoft.com/library/ms187729.aspx)
- [ISDATE](https://msdn.microsoft.com/library/ms187347.aspx)
- [ISNULL](https://msdn.microsoft.com/library/ms184325.aspx)
- [ISNUMERIC](https://msdn.microsoft.com/library/ms186272.aspx)
- [LAG](https://msdn.microsoft.com/library/hh231256.aspx)
- [LAST\_VALUE](https://msdn.microsoft.com/library/hh231517.aspx)
- [LEAD](https://msdn.microsoft.com/library/hh213125.aspx)
- [LEFT](https://msdn.microsoft.com/library/ms177601.aspx)
- [LEN](https://msdn.microsoft.com/library/ms190329.aspx)
- [LOG](https://msdn.microsoft.com/library/ms190319.aspx)
- [LOG10](https://msdn.microsoft.com/library/ms175121.aspx)
- [LOWER](https://msdn.microsoft.com/library/ms174400.aspx)
- [LTRIM](https://msdn.microsoft.com/library/ms177827.aspx)
- [MAX](https://msdn.microsoft.com/library/ms187751.aspx)
- [MIN](https://msdn.microsoft.com/library/ms179916.aspx)
- [MONTH](https://msdn.microsoft.com/library/ms187813.aspx)
- [NCHAR](https://msdn.microsoft.com/library/ms182673.aspx)
- [NTILE](https://msdn.microsoft.com/library/ms175126.aspx)
- [NULLIF](https://msdn.microsoft.com/library/ms177562.aspx)
- [OBJECT\_ID](https://msdn.microsoft.com/library/ms190328.aspx)
- [OBJECT\_NAME](https://msdn.microsoft.com/library/ms186301.aspx)
- [OBJECTPROPERTY](https://msdn.microsoft.com/library/ms176105.aspx)
- [OIBJECTPROPERTYEX](https://msdn.microsoft.com/library/ms188390.aspx)
- [ODBCS-Skalarfunktionen](https://msdn.microsoft.com/library/bb630290.aspx)
- [OVER-Klausel](https://msdn.microsoft.com/library/ms189461.aspx)
- [PARSENAME](https://msdn.microsoft.com/library/ms188006.aspx)
- [PATINDEX](https://msdn.microsoft.com/library/ms188395.aspx)
- [PERCENTILE\_CONT](https://msdn.microsoft.com/library/hh231473.aspx)
- [PERCENTILE\_DISC](https://msdn.microsoft.com/library/hh231327.aspx)
- [PERCENT\_RANK](https://msdn.microsoft.com/library/hh213573.aspx)
- [PI](https://msdn.microsoft.com/library/ms189512.aspx)
- [POWER](https://msdn.microsoft.com/library/ms174276.aspx)
- [QUOTENAME](https://msdn.microsoft.com/library/ms176114.aspx)
- [RADIANS](https://msdn.microsoft.com/library/ms189742.aspx)
- [RAND](https://msdn.microsoft.com/library/ms177610.aspx)
- [RANK](https://msdn.microsoft.com/library/ms176102.aspx)
- [REPLACE](https://msdn.microsoft.com/library/ms186862.aspx)
- [REPLICATE](https://msdn.microsoft.com/library/ms174383.aspx)
- [REVERSE](https://msdn.microsoft.com/library/ms180040.aspx)
- [RIGHT](https://msdn.microsoft.com/library/ms177532.aspx)
- [ROUND](https://msdn.microsoft.com/library/ms175003.aspx)
- [ROW\_NUMBER](https://msdn.microsoft.com/library/ms186734.aspx)
- [RTRIM](https://msdn.microsoft.com/library/ms178660.aspx)
- [SCHEMA\_ID](https://msdn.microsoft.com/library/ms188797.aspx)
- [SCHEMA\_NAME](https://msdn.microsoft.com/library/ms175068.aspx)
- [SERVERPROPERTY](https://msdn.microsoft.com/library/ms174396.aspx)
- [SESSION\_USER](https://msdn.microsoft.com/library/ms177587.aspx)
- [SIGN](https://msdn.microsoft.com/library/ms188420.aspx)
- [SIN](https://msdn.microsoft.com/library/ms188377.aspx)
- [SMALLDATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213396.aspx)
- [SOUNDEX](https://msdn.microsoft.com/library/ms187384.aspx)
- [SPACE](https://msdn.microsoft.com/library/ms187950.aspx)
- [SQL\_VARIANT\_PROPERTY](https://msdn.microsoft.com/library/ms178550.aspx)
- [SQRT](https://msdn.microsoft.com/library/ms176108.aspx)
- [SQUARE](https://msdn.microsoft.com/library/ms173569.aspx)
- [STATS\_DATE](https://msdn.microsoft.com/library/ms190330.aspx)
- [STDEV](https://msdn.microsoft.com/library/ms190474.aspx)
- [STDEVP](https://msdn.microsoft.com/library/ms176080.aspx)
- [STR](https://msdn.microsoft.com/library/ms189527.aspx)
- [STUFF](https://msdn.microsoft.com/library/ms188043.aspx)
- [SUBSTRING](https://msdn.microsoft.com/library/ms187748.aspx)
- [SUM](https://msdn.microsoft.com/library/ms187810.aspx)
- [SUSER\_SNAME](https://msdn.microsoft.com/library/ms174427.aspx)
- [SWITCHOFFSET](https://msdn.microsoft.com/library/bb677244.aspx)
- [SYSDATETIME](https://msdn.microsoft.com/library/bb630353.aspx)
- [SYSDATETIMEOFFSET](https://msdn.microsoft.com/library/bb677334.aspx)
- [SYSTEM\_USER](https://msdn.microsoft.com/library/ms179930.aspx)
- [SYSUTCDATETIME](https://msdn.microsoft.com/library/bb630387.aspx)
- [TAN](https://msdn.microsoft.com/library/ms190338.aspx)
- [TERTIARY\_WEIGHTS](https://msdn.microsoft.com/library/ms186881.aspx)
- [TIMEFROMPARTS](https://msdn.microsoft.com/library/hh213398.aspx)
- [TODATETIMEOFFSET](https://msdn.microsoft.com/library/bb630335.aspx)
- [TYPE\_ID](https://msdn.microsoft.com/library/ms181628.aspx)
- [TYPE\_NAME](https://msdn.microsoft.com/library/ms189750.aspx)
- [TYPEPROPERTY](https://msdn.microsoft.com/library/ms188419.aspx)
- [UNICODE](https://msdn.microsoft.com/library/ms180059.aspx)
- [UPPER](https://msdn.microsoft.com/library/ms180055.aspx)
- [USER](https://msdn.microsoft.com/library/ms186738.aspx)
- [USER\_NAME](https://msdn.microsoft.com/library/ms188014.aspx)
- [VAR](https://msdn.microsoft.com/library/ms186290.aspx)
- [VARP](https://msdn.microsoft.com/library/ms188735.aspx)
- [YEAR](https://msdn.microsoft.com/library/ms186313.aspx)
- [XACT\_STATE](https://msdn.microsoft.com/library/ms189797.aspx)

## Transaktionen

- [Transaktionen](https://msdn.microsoft.com/library/mt204031.aspx)

## Diagnosesitzungen

- [CREATE DIAGNOSTICS SESSION](https://msdn.microsoft.com/library/mt204029.aspx)

## Prozeduren

- [sp\_addrolemember](https://msdn.microsoft.com/library/ms187750.aspx)
- [sp\_columns](https://msdn.microsoft.com/library/ms176077.aspx)
- [sp\_configure](https://msdn.microsoft.com/library/ms188787.aspx)
- [sp\_datatype\_info\_90](https://msdn.microsoft.com/library/mt204014.aspx)
- [sp\_droprolemember](https://msdn.microsoft.com/library/ms188369.aspx)
- [sp\_execute](https://msdn.microsoft.com/library/ff848746.aspx)
- [sp\_executesql](https://msdn.microsoft.com/library/ms188001.aspx)
- [sp\_fkeys](https://msdn.microsoft.com/library/ms175090.aspx)
- [sp\_pdw\_add\_network\_credentials](https://msdn.microsoft.com/library/mt204011.aspx)
- [sp\_pdw\_database\_encryption](https://msdn.microsoft.com/library/mt219360.aspx)
- [sp\_pdw\_database\_encryption\_regenerate\_system\_keys](https://msdn.microsoft.com/library/mt204033.aspx)
- [sp\_pdw\_log\_user\_data\_masking](https://msdn.microsoft.com/library/mt204023.aspx)
- [sp\_pdw\_remove\_network\_credentials](https://msdn.microsoft.com/library/mt204038.aspx)
- [sp\_pkeys](https://msdn.microsoft.com/library/ms189813.aspx)
- [sp\_prepare](https://msdn.microsoft.com/library/ff848808.aspx)
- [sp\_special\_columns\_100](https://msdn.microsoft.com/library/mt204025.aspx)
- [sp\_sproc\_columns](https://msdn.microsoft.com/library/ms182705.aspx)
- [sp\_statistics](https://msdn.microsoft.com/library/ms173842.aspx)
- [sp\_tables](https://msdn.microsoft.com/library/ms186250.aspx)
- [sp\_unprepare](https://msdn.microsoft.com/library/ff848735.aspx)



## SET-Anweisungen

- [SET ANSI\_DEFAULTS](https://msdn.microsoft.com/library/ms188340.aspx)
- [SET ANSI\_NULL\_DFLT\_OFF](https://msdn.microsoft.com/library/ms187356.aspx)
- [SET ANSI\_NULL\_DFLT\_ON](https://msdn.microsoft.com/library/ms187375.aspx)
- [SET ANSI\_NULLS](https://msdn.microsoft.com/library/ms188048.aspx)
- [SET ANSI\_PADDING](https://msdn.microsoft.com/library/ms187403.aspx)
- [SET ANSI\_WARNINGS](https://msdn.microsoft.com/library/ms190368.aspx)
- [SET ARITHABORT](https://msdn.microsoft.com/library/ms190306.aspx)
- [SET ARITHIGNORE](https://msdn.microsoft.com/library/ms184341.aspx)
- [SET CONCAT\_NULL\_YIELDS\_NULL](https://msdn.microsoft.com/library/ms176056.aspx)
- [SET DATEFIRST](https://msdn.microsoft.com/library/ms181598.aspx)
- [SET DATEFORMAT](https://msdn.microsoft.com/library/ms189491.aspx)
- [SET FMTONLY](https://msdn.microsoft.com/library/ms173839.aspx)
- [SET IMPLICIT\_TRANSACITONS](https://msdn.microsoft.com/library/ms187807.aspx)
- [SET LOCK\_TIMEOUT](https://msdn.microsoft.com/library/ms189470.aspx)
- [SET NUMBERIC\_ROUNDABORT](https://msdn.microsoft.com/library/ms188791.aspx)
- [SET QUOTED\_IDENTIFIER](https://msdn.microsoft.com/library/ms174393.aspx)
- [SET ROWCOUNT](https://msdn.microsoft.com/library/ms188774.aspx)
- [SET TEXTSIZE](https://msdn.microsoft.com/library/ms186238.aspx)
- [SET TRANSACTION ISOLATION LEVEL](https://msdn.microsoft.com/library/ms173763.aspx)
- [SET XACT\_ABORT](https://msdn.microsoft.com/library/ms188792.aspx)


## Nächste Schritte
Weitere Referenzinformationen finden Sie unter [SQL Data Warehouse-Referenz – Übersicht][].

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse-Referenz – Übersicht]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!---HONumber=AcomDC_0629_2016-->