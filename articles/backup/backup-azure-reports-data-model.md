---
title: Adatmodell az Azure Backup
description: Ez a cikk ismerteti a Power BI adatok modell részletei az Azure Backup-jelentésekhez.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
origin.date: 06/26/2017
ms.date: 08/08/2018
ms.author: v-junlch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c6160570644da108ba713e8229b38f9587495c92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337578"
---
# <a name="data-model-for-azure-backup-reports"></a>Adatmodell az Azure Backup-jelentésekhez
Ez a cikk ismerteti a Power BI adatmodell az Azure Backup-jelentések készítéséhez. A data modellel meglévő jelentések kapcsolódó mezők alapján szűrheti, és további táblákat és mezőket a modellben is fontosabb, létrehozhatja saját jelentéseit. 

## <a name="creating-new-reports-in-power-bi"></a>Új jelentések létrehozásához a Power bi-ban
A Power BI használatával, amelyet testreszabási funkcióival biztosít [jelentések készítését az adatmodell](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Az Azure Backup data modellel
Az adatmodell részeként a következő mezőket segítségével jelentéseket hozhat létre, és a meglévő jelentések testreszabása.

### <a name="alert"></a>Riasztás
Ez a táblázat alapszintű mezők és az aggregációhoz különböző riasztási kapcsolódó mezők fölé.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Egész szám |A kijelölt időszakban létrehozott riasztások számát |
| %ActiveAlertsCreatedInPeriod |Százalék |Aktív riasztások a kiválasztott időszakban százaléka |
| %CriticalAlertsCreatedInPeriod |Százalék |Kritikus riasztások a kiválasztott időszakban százaléka |
| AlertOccurrenceDate |Dátum |A riasztás létrehozásának dátuma |
| AlertSeverity |Text |A példában a kritikus riasztás súlyossága |
| AlertStatus |Text |A riasztás, ha például aktív állapot |
| AlertType |Text |A generált riasztások, például a biztonsági mentés típusa |
| AlertUniqueId |Text |A generált riasztások egyedi azonosítója |
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítésének ideje |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Tizedes tört szám |Átlagos idő (percben) a kijelölt időszakra vonatkozó riasztás feloldása |
| EntityState |Text |A riasztás például aktív, a törölt objektum aktuális állapotát |

### <a name="backup-item"></a>Biztonsági másolati elem
Ez a táblázat alapszintű mezők és az aggregációhoz különböző biztonsági mentési elem kapcsolódó mezők fölé.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #BackupItems |Egész szám |Biztonsági másolati elemek száma |
| #UnprotectedBackupItems |Egész szám |Biztonsági másolati elemek száma a védelem leállítása, vagy biztonsági másolatok azonban még nem indult el biztonsági mentések konfigurálása|
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítésének ideje |
| BackupItemFriendlyName |Text |Biztonságimásolat-elem rövid neve |
| BackupItemId |Text |Biztonsági mentési elem azonosítója |
| BackupItemName |Text |Biztonsági másolati elem nevét |
| BackupItemType |Text |Biztonsági másolati elemek például a virtuális gép, fájlmappa típusa |
| EntityState |Text |Például aktív, a törölt biztonsági másolati elem objektum jelenlegi állapota |
| LastBackupDateTime |Dátum/idő |Kiválasztott biztonsági mentési elem legutóbbi biztonsági mentés időpontja |
| LastBackupState |Text |Kiválasztott biztonsági másolati elem, például sikeres, sikertelen volt a legutóbbi biztonsági mentés állapota |
| LastSuccessfulBackupDateTime |Dátum/idő |Utolsó sikeres biztonsági mentés idején a kiválasztott biztonsági mentési elem |
| ProtectionState |Text |A biztonsági másolati elem, például védett, ProtectionStopped aktuális védelmi állapotát |

### <a name="calendar"></a>Naptár
Ez a táblázat részletesen naptár kapcsolatos mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| Dátum |Dátum |Adatok szűrése a kiválasztott dátum |
| DateKey |Text |Minden egyes dátum elemhez tartozó egyedi kulcs |
| DayDiff |Tizedes tört szám |Például a nap-szűrési adatok különbség, 0 azt jelzi, hogy az aktuális napra vonatkozó adatokat, -1 azt jelzi, hogy előző egy nap adatainak, 0 és a -1 jelzi adatok az aktuális és korábbi napra  |
| Hónap |Text |Havi az adatok szűrése a kiválasztott év, hónap első napján kezdődik, és 31 nappal ér véget |
| MonthDate | Dátum |Dátum a hónap, ha a hónap befejeződik, a kiválasztott adatok szűrése |
| MonthDiff |Tizedes tört szám |Például havi szűrési különbség, a 0 azt jelzi, hogy aktuális havi adatokat, a -1 azt jelzi, hogy az előző hónap adatok, a 0 és a -1 jelzi adat jelenlegi és előző hónapban |
| Hét |Text |Kínál az adatok, kijelölve hét hét vasárnap és szombat-ig |
| WeekDate |Dátum |Dátum, amikor hét ér véget, a hét kijelölt adatok szűrése |
| WeekDiff |Tizedes tört szám |Például a hét szűrési adatok különbség, 0 azt jelzi, hogy aktuális hét adatok, előző hét adatokat jelöl, -1, 0 és a -1 jelzi adatok az aktuális és az előző hét |
| Év |Text |Adatok szűrése a kiválasztott naptári év |
| YearDate |Dátum |Dátum az év, amikor év ér véget, a kiválasztott adatok szűrése |

### <a name="job"></a>Feladat
Ez a táblázat alapszintű mezők és az aggregációhoz feladatokkal kapcsolatos különböző mezők fölé.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #JobsCreatedInPeriod |Egész szám |A kijelölt időszakban létrehozott feladatok száma |
| %FailuresForJobsCreatedInPeriod |Százalék |Százalék a kiválasztott időszakban a teljes feladat-hibák |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Tizedes tört szám |MB-ban, az átvitt adatok 80 %-os érték **biztonsági mentési** a kiválasztott időszakban létrehozott feladatok |
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítésének ideje |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Tizedes tört szám |Átlagos időtartam percben **befejezett backup** a kiválasztott időszakban létrehozott feladatok |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Tizedes tört szám |Átlagos időtartam percben **visszaállítás befejeződött** a kiválasztott időszakban létrehozott feladatok |
| BackupStorageDestination |Text |A biztonsági mentési tár, például a felhőben, a lemez rendeltetési  |
| EntityState |Text |Feladat például aktív, a törölt objektum jelenlegi állapota |
| JobFailureCode |Text |Sikertelen hibakód karakterláncát miatt, amely feladat hiba történt |
| JobOperation |Text |A művelet, amelynek feladat futtatása például biztonsági mentés, visszaállítás, a Backup konfigurálása |
| JobStartDate |Dátum |Feladat elindításának dátuma |
| JobStartTime |Time |Ha a feladat elindításának |
| Feladat állapota |Text |A befejezett feladat például befejezve, sikertelen állapota |
| JobUniqueId |Text |A feladat azonosításához használatos egyedi azonosító |

### <a name="policy"></a>Szabályzat
Ez a táblázat alapszintű mezők és az aggregációhoz különböző házirendekhez kapcsolódó mezők fölé.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #Policies |Egész szám |A rendszer a biztonsági mentési szabályzatok száma |
| #PoliciesInUse |Egész szám |A biztonsági mentések konfigurálása jelenleg használt szabályzatok maximális számát |
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítésének ideje |
| BackupDaysOfTheWeek |Text |Ha biztonsági mentések ütemezett napjai |
| BackupFrequency |Text |Amellyel biztonsági mentések futnak, például, napi, heti gyakoriság |
| BackupTimes |Text |Dátum és idő, amikor a biztonsági mentés van ütemezve |
| DailyRetentionDuration |Egész szám |Teljes megőrzés időtartama napokban megadva a konfigurált biztonsági mentés |
| DailyRetentionTimes |Text |Dátum és idő, amikor napi megőrzés konfigurálása |
| EntityState |Text |Ha például aktív, a törölt csoportházirend-objektum aktuális állapotát |
| MonthlyRetentionDaysOfTheMonth |Text |A havi megőrzési kiválasztott hónap dátumok |
| MonthlyRetentionDaysOfTheWeek |Text |Havi megőrzési kijelölve a hét napjai |
| MonthlyRetentionDuration |Tizedes tört szám |Hónap konfigurált biztonsági mentés a teljes megőrzési időtartama |
| MonthlyRetentionFormat |Text |Írja be a havi adatmegőrzési-konfiguráció például naponta napi szintű, hetente a heti szintű |
| MonthlyRetentionTimes |Text |Dátum és idő, ha a havi megőrzési van konfigurálva |
| MonthlyRetentionWeeksOfTheMonth |Text |Havi megőrzési esetén a hónap, hét konfigurált például First, Last stb. |
| PolicyName |Text |A megadott házirend neve |
| PolicyUniqueId |Text |Azonosíthatja a szabályzat egyedi azonosítója |
| RetentionType |Text |Írja be a megőrzési házirend például, naponta, hetente, havonta, évente |
| WeeklyRetentionDaysOfTheWeek |Text |Heti megőrzési kijelölve a hét napjai |
| WeeklyRetentionDuration |Tizedes tört szám |Teljes hetes, a konfigurált biztonsági mentés heti megőrzési időtartama |
| WeeklyRetentionTimes |Text |Dátum és idő, ha a heti megőrzési van konfigurálva |
| YearlyRetentionDaysOfTheMonth |Text |A hónap, éves megőrzési kijelölt dátumok |
| YearlyRetentionDaysOfTheWeek |Text |Az éves megőrzési kijelölve a hét napjait |
| YearlyRetentionDuration |Tizedes tört szám |Teljes megőrzési időtartam a konfigurált biztonsági mentés éves |
| YearlyRetentionFormat |Text |Írja be az éves adatmegőrzési-konfiguráció például naponta alapú, hetente alapú hét nap |
| YearlyRetentionMonthsOfTheYear |Text |Az év hónapjai éves megőrzési kiválasztva |
| YearlyRetentionTimes |Text |Dátum és idő, amikor éves megőrzési van konfigurálva |
| YearlyRetentionWeeksOfTheMonth |Text |A hónap, éves megőrzési esetén hét konfigurált például First, Last stb. |

### <a name="protected-server"></a>Védett kiszolgáló
Ez a táblázat alapszintű mezők és az aggregációhoz-kiszolgálóval kapcsolatos különféle védett mezők fölé.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #ProtectedServers |Egész szám |Védett kiszolgálók száma |
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítésének ideje |
| AzureBackupAgentOSType |Text |Az Azure Backup-ügynök operációs rendszerének típusa |
| AzureBackupAgentOSVersion |Text |Az Azure Backup szolgáltatás ügynökének operációsrendszer-verziója |
| AzureBackupAgentUpdateDate |Text |Az ügynök Backup-ügynök frissítésének dátuma |
| AzureBackupAgentVersion |Text |Biztonsági másolat verzióját az ügynök verziószáma |
| BackupManagementType |Text |A biztonsági mentés például IaaSVM fájlmappa szolgáltató típusa |
| EntityState |Text |Aktív, a törölt például a védett kiszolgáló objektum aktuális állapotát |
| ProtectedServerFriendlyName |Text |Védett kiszolgáló rövid neve |
| ProtectedServerName |Text |Védett kiszolgáló neve |
| ProtectedServerType |Text |Például IaaSVMContainer készíteni a védett kiszolgáló típusa |
| ProtectedServerName |Text |Védett kiszolgáló melyik biztonsági másolati elem nevét tartozik |
| RegisteredContainerId |Text |Regisztrálva a biztonsági mentési tároló azonosítója |

### <a name="storage"></a>Storage
Ez a táblázat alapszintű mezők és az aggregációhoz különböző storage szolgáltatással kapcsolatos mezők fölé.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #ProtectedInstances |Tizedes tört szám |Az adott előtérbeli tár számlázási, számított alapján legújabb érték kiszámításához használt védett példányok száma |
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítésének ideje |
| CloudStorageInMB |Tizedes tört szám |A kijelölt legújabb érték alapján számított biztonsági mentések által használt felhőalapú biztonsági mentési tár |
| EntityState |Text |Ha például aktív, a törölt objektum jelenlegi állapota |
| LastUpdatedDate |Dátum |Ha a kijelölt sor utolsó módosításának dátuma |

### <a name="time"></a>Time
Ez a tábla mezők idővel kapcsolatos információt nyújt.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| Óra |Time |A nap, például 1:00:00 PM |
| HourNumber |Tizedes tört szám |A nap például 13,00 óra száma |
| Perc |Tizedes tört szám |Az óra azon perce |
| PeriodOfTheDay |Text |Időszak idősávja a nap, például a 12. és 3 óra |
| Time |Time |A következő napon: például 12:00:01-kor |
| TimeKey |Text |Az időt jelentik, a kulcs értéke |

### <a name="vault"></a>Tároló
Ez a táblázat alapszintű mezők és az aggregációhoz különböző tároló kapcsolódó mezők fölé.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #Vaults |Egész szám |Tárolók száma |
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítésének ideje |
| AzureDataCenter |Text |Adatközpont, ahol a tároló megtalálható |
| EntityState |Text |Például aktív, a törölt tároló objektum jelenlegi állapota |
| StorageReplicationType |Text |A tároló például GeoRedundant tárolóreplikáció típusa |
| SubscriptionId |Text |Az ügyfél-jelentések létrehozása a kijelölt előfizetés-azonosító |
| VaultName |Text |A tároló neve |
| VaultTags |Text |A tárolóhoz társított címkék |

## <a name="next-steps"></a>További lépések
Után tekintse át az adatmodell az Azure Backup-jelentések létrehozása, létrehozásával és -jelentések megtekintése a Power bi-ban kapcsolatos további részletekért tekintse meg a következő cikkekben talál.

- [Jelentések létrehozása a Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
- [A Power BI-jelentések szűrése](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)


<!-- Update_Description: update metedata properties -->