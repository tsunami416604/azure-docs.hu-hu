---
title: Azure Backup adatmodellje
description: Ez a cikk a Azure Backup-jelentésekhez Power BI adatmodell részleteiről beszél.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: dacurwin
ms.openlocfilehash: f3db0e6a78eada52f2b5936f5194138d97ebcca6
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074897"
---
# <a name="data-model-for-azure-backup-reports"></a>Adatmodell az Azure Backup-jelentésekhez

Ez a cikk a Azure Backup-jelentések létrehozásához használt Power BI adatmodellt ismerteti. Az adatmodell használatával a meglévő jelentéseket a megfelelő mezők alapján szűrheti, ami még fontosabb, a modellben található táblák és mezők használatával saját jelentéseket hozhat létre.

## <a name="creating-new-reports-in-power-bi"></a>Új jelentések létrehozása a Power BIban

A Power BI testreszabási funkciókat biztosít, amelyekkel [jelentéseket hozhat létre az adatmodell használatával](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Azure Backup adatmodell használata

A következő mezőket használhatja az adatmodell részeként jelentések létrehozásához és a meglévő jelentések testreszabásához.

### <a name="alert"></a>Riasztás

Ez a tábla alapvető mezőket és összesítéseket biztosít különböző riasztásokkal kapcsolatos mezőkhöz.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Egész szám |A kiválasztott időszakban létrehozott riasztások száma |
| %ActiveAlertsCreatedInPeriod |Százalék |Aktív riasztások százalékos aránya a kiválasztott időszakban |
| %CriticalAlertsCreatedInPeriod |Százalék |Kritikus riasztások százalékos aránya a kiválasztott időszakban |
| AlertOccurrenceDate |Dátum |A riasztás létrehozásának dátuma |
| AlertSeverity |Szöveg |A riasztás súlyossága. Például kritikus |
| AlertStatus |Szöveg |A riasztás állapota. Például: aktív |
| AlertType |Szöveg |A generált riasztás típusa. Például: biztonsági mentés |
| AlertUniqueId |Szöveg |A generált riasztás egyedi azonosítója |
| AsOnDateTime |Dátum és idő |A kijelölt sor legutóbbi frissítésének ideje |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Tizedes tört szám |A kijelölt időszakra vonatkozó riasztás feloldásának átlagos időtartama (percben) |
| EntityState |Szöveg |A riasztási objektum aktuális állapota. Például: aktív, törölve |

### <a name="backup-item"></a>Biztonsági másolati tétel

Ez a tábla alapszintű mezőket és összesítéseket biztosít a különböző biztonsági mentési elemek kapcsolódó mezőihez.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #BackupItems |Egész szám |Biztonsági másolati elemek száma |
| #UnprotectedBackupItems |Egész szám |A védelemhez leállított biztonságimásolat-elemek száma, de biztonsági mentések vannak konfigurálva, de a biztonsági másolatok nem indult el|
| AsOnDateTime |Dátum és idő |A kijelölt sor legutóbbi frissítésének ideje |
| BackupItemFriendlyName |Szöveg |A biztonságimásolat-tétel rövid neve |
| BackupItemId |Szöveg |A biztonsági másolati tétel azonosítója |
| BackupItemName |Szöveg |A biztonságimásolat-tétel neve |
| BackupItemType |Szöveg |A biztonságimásolat-tétel típusa. Például: VM, fájlmappa |
| EntityState |Szöveg |A biztonságimásolat-elem objektumának aktuális állapota. Például: aktív, törölve |
| LastBackupDateTime |Dátum és idő |A kijelölt biztonsági másolati elemek legutóbbi biztonsági mentésének időpontja |
| LastBackupState |Szöveg |A kijelölt biztonsági másolati elem utolsó biztonsági mentésének állapota. Például sikeres, sikertelen |
| LastSuccessfulBackupDateTime |Dátum és idő |A kijelölt biztonsági másolati tétel utolsó sikeres biztonsági mentésének időpontja |
| ProtectionState |Szöveg |A biztonsági másolati elem jelenlegi védelmi állapota. Például: védett, ProtectionStopped |

### <a name="calendar"></a>Naptár

Ez a táblázat a naptárral kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| Dátum |Dátum |Az adatok szűréséhez kiválasztott dátum |
| DateKey |Szöveg |Minden dátumérték egyedi kulcsa |
| DayDiff |Tizedes tört szám |Az adat szűrésének napja. Például a 0 az aktuális nap adataira utal, a-1 az előző egy nap adatát jelzi, 0 és-1 az aktuális és az előző nap adataira utal.  |
| Month |Szöveg |Az év hónapja, amely az adat szűrésére lett kiválasztva, a hónap első napján kezdődik, és a 31. napon ér véget |
| MonthDate | Dátum |A hónap végének dátuma, amely az adatok szűrésére van kiválasztva |
| MonthDiff |Tizedes tört szám |Az Adatszűrés hónapjának különbsége. Például a 0 a jelenlegi hónap adataira utal, a-1 az előző hónap adataira utal, 0 és-1 az aktuális és az előző hónapra vonatkozó adatjelzést. |
| Hét |Szöveg |Az adatszűrésre kijelölt hét, a hét vasárnap kezdődik, és szombaton ér véget |
| WeekDate |Dátum |Az adatok szűrésére kijelölt hét napja, a hét végén |
| WeekDiff |Tizedes tört szám |Az adatszűréshez használt hét különbsége. Például a 0 az aktuális hét adataira utal, a-1 az előző heti adatértékeket jelzi, 0 és-1 az aktuális és az előző hét adataira utal. |
| Year |Szöveg |Az adatszűréshez kiválasztott naptári év |
| YearDate |Dátum |Az év végének dátuma, amely az adatok szűrésére van kiválasztva |

### <a name="job"></a>Feladat

Ez a tábla alapszintű mezőket és összesítéseket biztosít a különböző feladatokhoz kapcsolódó mezőkben.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #JobsCreatedInPeriod |Egész szám |A kiválasztott időszakban létrehozott feladatok száma |
| %FailuresForJobsCreatedInPeriod |Százalék |A feladatok általános feladatainak százalékos aránya a kiválasztott időszakban |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Tizedes tört szám |a kiválasztott időszakban létrehozott **biztonsági mentési** feladatok MB-ban továbbított adatok 80th százalékos értéke |
| AsOnDateTime |Dátum és idő |A kijelölt sor legutóbbi frissítésének ideje |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Tizedes tört szám |A kiválasztott időszakban létrehozott **befejezett biztonsági mentési** feladatok átlagos ideje (percben) |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Tizedes tört szám |A kiválasztott időszakban létrehozott **befejezett visszaállítási** feladatok átlagos ideje (percben) |
| BackupStorageDestination |Szöveg |A biztonsági mentési tár célja. Például: felhő, lemez  |
| EntityState |Szöveg |A feladatütemezés aktuális állapota. Például: aktív, törölve |
| JobFailureCode |Szöveg |Hiba történt a hibakód karakterlánca miatt, mert a művelet sikertelen volt |
| JobOperation |Szöveg |A művelet, amelynek a feladata fut. Például biztonsági mentés, visszaállítás, biztonsági mentés konfigurálása |
| JobStartDate |Dátum |Az a dátum, amikor a feladatot elindították |
| JobStartTime |Time |A feladatok futásának ideje |
| Feladat állapota |Szöveg |A Befejezett feladatok állapota. Például befejezve, sikertelen |
| JobUniqueId |Szöveg |A feladatot azonosító egyedi azonosító |

### <a name="policy"></a>Szabályzat

Ez a táblázat alapvető mezőket és összesítéseket biztosít különböző házirendekkel kapcsolatos mezőkhöz.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #Policies |Egész szám |A rendszeren létező biztonsági mentési házirendek száma |
| #PoliciesInUse |Egész szám |A biztonsági másolatok konfigurálásához jelenleg használt házirendek száma |
| AsOnDateTime |Dátum és idő |A kijelölt sor legutóbbi frissítésének ideje |
| BackupDaysOfTheWeek |Szöveg |A hét azon napjai, amikor a biztonsági mentések ütemezése megtörtént |
| BackupFrequency |Szöveg |A biztonsági másolatok futtatásának gyakorisága. Például: napi, heti |
| BackupTimes |Szöveg |A biztonsági másolatok ütemezésének dátuma és időpontja |
| DailyRetentionDuration |Egész szám |A beállított biztonsági másolatok teljes megőrzési időtartama (nap) |
| DailyRetentionTimes |Szöveg |A napi megőrzés konfigurálásának dátuma és időpontja |
| EntityState |Szöveg |A házirend-objektum aktuális állapota. Például: aktív, törölve |
| MonthlyRetentionDaysOfTheMonth |Szöveg |A havi megőrzésre kijelölt hónap dátuma |
| MonthlyRetentionDaysOfTheWeek |Szöveg |A havi megőrzésre kiválasztott hét napjai |
| MonthlyRetentionDuration |Tizedes tört szám |A beállított biztonsági másolatok teljes megőrzési időtartama (hónap) |
| MonthlyRetentionFormat |Szöveg |A havi megőrzés konfigurációjának típusa Például naponta, hetente, hetente |
| MonthlyRetentionTimes |Szöveg |A havi megőrzés konfigurálásának dátuma és időpontja |
| MonthlyRetentionWeeksOfTheMonth |Szöveg |A hónap hete, amikor a havi megőrzés konfigurálva van. Például: első, utolsó stb. |
| PolicyName |Szöveg |A megadott házirend neve |
| PolicyUniqueId |Szöveg |A szabályzat azonosítására szolgáló egyedi azonosító |
| RetentionType |Szöveg |Adatmegőrzési szabály típusa Például: napi, heti, havi, éves |
| WeeklyRetentionDaysOfTheWeek |Szöveg |A heti megőrzéshez kiválasztott hét napjai |
| WeeklyRetentionDuration |Tizedes tört szám |Heti megőrzési időtartam összesen hetekben a konfigurált biztonsági másolatok esetében |
| WeeklyRetentionTimes |Szöveg |A heti megőrzés konfigurálásának dátuma és időpontja |
| YearlyRetentionDaysOfTheMonth |Szöveg |Az éves megőrzéshez kiválasztott hónap dátuma |
| YearlyRetentionDaysOfTheWeek |Szöveg |Az éves megőrzéshez kiválasztott hét napjai |
| YearlyRetentionDuration |Tizedes tört szám |Teljes megőrzési időtartam években a konfigurált biztonsági másolatok esetében |
| YearlyRetentionFormat |Szöveg |A konfiguráció típusa az éves megőrzéshez. Például naponta, hetente, hetente |
| YearlyRetentionMonthsOfTheYear |Szöveg |Az év hónapos megőrzésre kiválasztott hónapja |
| YearlyRetentionTimes |Szöveg |Az éves adatmegőrzés konfigurálásának dátuma és időpontja |
| YearlyRetentionWeeksOfTheMonth |Szöveg |A hónap hete, amikor az éves megőrzés konfigurálva van. Például: első, utolsó stb. |

### <a name="protected-server"></a>Védett kiszolgáló

Ez a táblázat alapvető mezőket és összesítéseket biztosít a különböző védett kiszolgálókkal kapcsolatos mezőkhöz képest.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #ProtectedServers |Egész szám |Védett kiszolgálók száma |
| AsOnDateTime |Dátum és idő |A kijelölt sor legutóbbi frissítésének ideje |
| AzureBackupAgentOSType |Szöveg |Azure Backup ügynök operációs rendszerének típusa |
| AzureBackupAgentOSVersion |Szöveg |Azure Backup ügynök operációs rendszerének verziója |
| AzureBackupAgentUpdateDate |Szöveg |Az ügynök biztonsági mentési ügynökének frissítésének dátuma |
| AzureBackupAgentVersion |Szöveg |Az ügynök biztonsági mentési verziójának verziószáma |
| BackupManagementType |Szöveg |A biztonsági mentést végző szolgáltató típusa. Például: IaaSVM, fájlmappa |
| EntityState |Szöveg |A védett kiszolgáló objektum jelenlegi állapota. Például: aktív, törölve |
| ProtectedServerFriendlyName |Szöveg |A védett kiszolgáló rövid neve |
| ProtectedServerName |Szöveg |A védett kiszolgáló neve |
| ProtectedServerType |Szöveg |A védett kiszolgáló biztonsági másolatának típusa. Például: IaaSVMContainer |
| ProtectedServerName |Szöveg |Annak a védett kiszolgálónak a neve, amelyhez a biztonsági másolati elemek tartoznak |
| RegisteredContainerId |Szöveg |A biztonsági mentéshez regisztrált tároló azonosítója |

### <a name="storage"></a>Tárolás

Ez a táblázat alapvető mezőket és összesítéseket biztosít a különböző tárterületekkel kapcsolatos mezőkhöz képest.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #ProtectedInstances |Tizedes tört szám |A előtér-tároló kiszámításához használt védett példányok száma a számlázásban, a kijelölt idő legutóbbi értékének alapján számítva |
| AsOnDateTime |Dátum és idő |A kijelölt sor legutóbbi frissítésének ideje |
| CloudStorageInMB |Tizedes tört szám |A biztonsági mentések által használt Felhőbeli biztonsági mentési tár, amely a kijelölt idő legutóbbi értéke alapján lett kiszámítva |
| EntityState |Szöveg |Az objektum aktuális állapota. Például: aktív, törölve |
| LastUpdatedDate |Dátum |A kijelölt sor legutóbbi frissítésének dátuma |

### <a name="time"></a>Time

Ez a táblázat az időponthoz kapcsolódó mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| Óra |Time |A nap órája. Például 1:00:00 PM |
| HourNumber |Tizedes tört szám |Az óra száma a nap folyamán. Például 13,00 |
| Perc |Tizedes tört szám |Az óra perce |
| PeriodOfTheDay |Szöveg |A nap időpontjának tárolóhelye. Például: 12-3 |
| Time |Time |A nap időpontja. Például: 12:00:01 |
| TimeKey |Szöveg |Az időt jelölő kulcs értéke |

### <a name="vault"></a>Tároló

Ez a tábla alapszintű mezőket és összesítéseket biztosít különböző tárolókkal kapcsolatos mezőkben.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #Vaults |Egész szám |Tárolók száma |
| AsOnDateTime |Dátum és idő |A kijelölt sor legutóbbi frissítésének ideje |
| AzureDataCenter |Szöveg |Az adatközpont, ahol a tár található |
| EntityState |Szöveg |A tár objektum jelenlegi állapota. Például: aktív, törölve |
| StorageReplicationType |Szöveg |A tár tárolási replikálásának típusa. Például: GeoRedundant |
| SubscriptionId |Szöveg |A jelentések létrehozásához kiválasztott ügyfél előfizetés-azonosítója |
| vaultName |Szöveg |A tároló neve |
| VaultTags |Szöveg |A tárolóhoz társított Címkék |

## <a name="next-steps"></a>További lépések

Miután áttekintette a Azure Backup-jelentések létrehozásához szükséges adatmodellt, további részleteket a következő cikkekben talál a jelentések létrehozásával és megtekintésével kapcsolatban Power BIban.

* [Jelentések létrehozása a Power BIban](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Jelentések szűrése Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
