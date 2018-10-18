---
title: Understanding rendszeres biztonsági mentési konfiguráció az Azure Service Fabricben |} A Microsoft Docs
description: A Service Fabric rendszeres biztonsági mentését, és a szolgáltatás engedélyezéséhez az alkalmazásadatok periodikus adatok biztonsági másolatának visszaállítása.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA45B4A-0258-4CB3-A825-7E8F70F28401
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: hrushib
ms.openlocfilehash: eeaa0e9a940f16c2416418959c98cd17e4816afc
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387633"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Az Azure Service Fabric rendszeres biztonsági mentési konfiguráció ismertetése

A Reliable stateful services és Reliable Actors rendszeres biztonsági mentés konfigurálása a következő lépésekből áll:

1. **A biztonsági mentési szabályzatok létrehozását**: Ebben a lépésben egy vagy több biztonsági mentési szabályzatok hozhatók létre követelményeitől függően.

2. **Lehetővé teszi a biztonsági mentés**: Ebben a lépésben létrehozott biztonsági mentési szabályzatok társítania **1. lépés** a szükséges entitások _alkalmazás_, _szolgáltatás_, vagy egy  _Partíció_.

## <a name="create-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

Biztonsági mentési szabályzat az alábbi konfigurációk áll:

* **Automatikus visszaállítási adatvesztési**: Megadja, hogy a legújabb elérhető biztonsági mentés automatikusan használ, abban az esetben a partíció adatvesztési esemény során lép a visszaállítás elindítása.

* **Maximális növekményes biztonsági mentések**: növekményes biztonsági másolat között két teljes biztonsági mentések maximális száma határozza meg. Maximális növekményes biztonsági mentések a felső határ megadása. Egy teljes biztonsági mentés előtt megadott számú növekményes biztonsági mentések végezhető el a következő feltételek valamelyike lehet venni

    1. A replika elsődleges vált, mert soha nem tartott egy teljes biztonsági mentés.

    2. A naplórekordok, mivel a rendszer csonkolta a legutóbbi biztonsági mentés része.

    3. Replika átadott MaxAccumulatedBackupLogSizeInMB korlátot.

* **Biztonsági mentés ütemezése**: az idő vagy a rendszeres biztonsági mentés időpontjának gyakorisága. Egy ismétlődő megadott időközönként vagy egy rögzített időpontban napi / heti biztonsági mentéseket ütemezhet.

    1. **Biztonsági mentési ütemezés gyakoriságának-alapú**: Ez az ütemezés típusa használandó, ha szükség az adatok biztonsági mentésének érvénybe rögzített időközönként. Két egymást követő biztonsági mentések közötti kívánt időintervallum van definiálva, használjon ISO8601 formátumot. Biztonsági mentési ütemezés gyakoriságának-alapú időköz felbontása legfeljebb perc támogatja.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Biztonsági mentés ütemezése időalapú**: Ez az ütemezés típusa Ha kell, hogy az adatok biztonsági mentésének igénybe, a nap vagy hét megadott időpontban kell használni. Az ütemezés gyakoriságának típusa lehet a napi vagy heti.
        1. **_Napi_ biztonsági mentési ütemezés időalapú**: Ez az ütemezés típusa használandó, ha az adatok biztonsági mentésének érvénybe a nap adott időpontokban kell azonosítója. Adja meg, állítsa `ScheduleFrequencyType` való _napi_; és `RunTimes` ISO8601 formátumot az a nap folyamán a kívánt időre listájához, dátum és idő figyelmen kívül hagyja. Ha például `0001-01-01T18:00:00` jelöli _6:00 és 18_ mindennap, figyelmen kívül hagyja a dátum részét _0001-01-01_. Alábbi példa szemlélteti a konfigurációt a napi biztonsági mentés elindítása, _9:00-kor_ és _6:00 és 18_ mindennapi.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **_Heti_ biztonsági mentési ütemezés időalapú**: Ez az ütemezés típusa használandó, ha az adatok biztonsági mentésének érvénybe a nap adott időpontokban kell azonosítója. Adja meg, állítsa `ScheduleFrequencyType` való _heti_; set `RunDays` , azokat a napokat, amikor aktiválódik, és állítsa be kell-e a biztonsági mentés heti `RunTimes` ISO8601 formátumot az a nap folyamán a kívánt időre listájához, dátum és idő megadva figyelmen kívül. A hét azon napjai, listája kiváltó ok a rendszeres biztonsági mentést. Alábbi példa szemlélteti a konfigurációt a napi biztonsági mentés elindítása, _9:00-kor_ és _6:00 és 18_ során hétfőtől péntekig.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Biztonsági mentési tár**: biztonsági mentés feltölteni a helyét adja meg. Storage vagy Azure blob-tároló vagy fájlmegosztás.
    1. **Azure-blobtárolóba**: A tárolási mód van kiválasztva, ha szükséges, hogy a biztonsági másolatok az Azure-ban létrehozott tárolásához. Mindkét _önálló_ és _Azure-alapú_ fürtök használhatja a tárolási típust. Tárolási típus leírása a kapcsolati karakterláncot, és ha a biztonsági mentéseket kell feltölteni a tároló nevére van szükség. Ha a tárolóhoz, az a megadott név nem érhető el, majd helybeállításokkal jön létre egy biztonsági mentési feltöltése közben.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Fájlmegosztás**: A tárolási mód van kiválasztva a _önálló_ fürtök adatok tárolásához szükség esetén a helyi biztonsági mentését. Leírás a tárolási típus szükséges fájl megosztás elérési útja, ahol a biztonsági mentéseket kell feltölteni. A fájlmegosztáshoz való hozzáférés konfigurálható az alábbi lehetőségek egyikének használatával
        1. _Integrált Windows-hitelesítés_, ahol a Service Fabric-fürthöz tartozó összes számítógép a hozzáférés a fájlmegosztáshoz megadott. Ebben az esetben állítsa a következő mezők konfigurálása _fájlmegosztás_ -alapú biztonsági mentési tár.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Felhasználónév és jelszó segítségével történő védelmét fájlmegosztás_, a hozzáférés a fájlmegosztáshoz amennyiben a megadott felhasználóknak. Megosztás tárolási fájlspecifikáció teszi, hogy adja meg a másodlagos felhasználó nevét és a másodlagos jelszó fall visszaírt hitelesítő adatok megadása, abban az esetben, ha a felhasználónév elsődleges hitelesítés sikertelen és az elsődleges jelszó is biztosít. Ebben az esetben állítsa a következő mezők konfigurálása _fájlmegosztás_ -alapú biztonsági mentési tár.
            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Győződjön meg arról, hogy a tárolómegbízhatóság megfelel-e vagy meghaladja a biztonsági mentési adatok megbízhatóságának követelményeket.
>

## <a name="enable-periodic-backup"></a>Rendszeres biztonsági mentés engedélyezése
Adatok biztonsági mentés követelményeinek teljesítéséhez biztonsági mentési házirend meghatározása, miután a biztonsági mentési szabályzat társítható megfelelően vagy egy _alkalmazás_, vagy _szolgáltatás_, vagy egy _partíció_.

### <a name="hierarchical-propagation-of-backup-policy"></a>Hierarchikus terjesztése a biztonsági mentési szabályzat
A Service Fabricben, alkalmazás, szolgáltatás és a partíciók közötti kapcsolat. a hierarchikus [alkalmazásmodell](./service-fabric-application-model.md). Biztonsági mentési házirend is társítható vagy egy _alkalmazás_, _szolgáltatás_, vagy egy _partíció_ a hierarchiában. A biztonsági mentési szabályzat hierarchikusan tölti ki a következő szintre. Feltéve, hogy csak egy biztonsági mentési szabályzathoz társított, és létrehozott egy _alkalmazás_, az összes tartozó összes állapot-nyilvántartó partíció _Reliable stateful services_ és _Reliable Actors_ , a _alkalmazás_ fog kell készíteni a biztonsági mentési házirend használatával. Vagy ha a biztonsági mentési házirend társítva van egy _Reliable stateful Services_, az összes partíció lesz kell készíteni a biztonsági mentési házirend használatával.

### <a name="overriding-backup-policy"></a>A biztonsági mentési szabályzat felülírása
Előfordulhat, hogy egy olyan forgatókönyvet, ahol azonos biztonsági mentési ütemezést az adatok biztonsági mentése szükség az alkalmazás egyes szolgáltatások kivételével az összes szolgáltatás, az kell, hogy az adatok biztonsági mentésének nagyobb gyakoriságot ütemezés vagy egy másik tárfiókba véve a biztonsági mentési vagy fájlmegosztás. Az ilyen-forgatókönyveket érintenek, mentéssel service szolgáltatás- és partíciószűrési hatókörben felülbírálás propagálja a házirend-létesítményben biztosít. Ha a biztonsági mentési szabályzat társítva, _szolgáltatás_ vagy _partíció_, ez a beállítás felülbírálja terjesztését a biztonsági mentési házirend, ha van ilyen.

### <a name="example"></a>Példa

Ebben a példában a telepítő a két alkalmazás _MyApp_A_ és _MyApp_B_. Alkalmazás _MyApp_A_ tartalmaz két Reliable Stateful services, _SvcA1_ & _SvcA3_, és a egy Reliable Actors-szolgáltatás _ActorA2_. _SvcA1_ közben három partíciókat tartalmaz _ActorA2_ és _SvcA3_ két partíció tartalmazza.  Alkalmazás _MyApp_B_ tartalmaz három Reliable Stateful services, _SvcB1_, _SvcB2_, és _SvcB3_. _SvcB1_ és _SvcB2_ két partíciókat tartalmaz minden közben _SvcB3_ három partíciókat tartalmaz.

Tegyük fel, hogy ezek az alkalmazások adatainak biztonsági mentés követelményeinek, a következők

1. MyApp_A
    1. Napi biztonsági mentést az összes partíciót az összes adat _Reliable Stateful services_ és _Reliable Actors_ az alkalmazáshoz tartozó. Biztonsági mentési adatok feltöltése a hely _BackupStore1_.

    2. Egy olyan szolgáltatást, _SvcA3_, szükséges adatok biztonsági mentését óránként.

    3. Adatok mérete a partíció _SvcA1_P2_ és a biztonsági mentési adatok különböző tárolási helyen kell tárolni a vártnál több _BackupStore2_.

2. MyApp_B
    1. Adatok biztonsági másolatának létrehozása minden vasárnap az összes partíciója 8:00 órakor _SvcB1_ szolgáltatás. Biztonsági mentési adatok feltöltése a hely _BackupStore1_.

    2. Adatok biztonsági másolatának létrehozása minden nap, 8:00-kor partíció _SvcB2_P1_. Biztonsági mentési adatok feltöltése a hely _BackupStore1_.

Ezen adatok biztonsági mentés követelményeinek teljesítésére, biztonsági mentési szabályzatok BP_1 BP_5 jönnek létre, és a következő biztonsági mentés engedélyezése.
1. MyApp_A
    1. Biztonsági mentési szabályzat létrehozása _BP_1_, a gyakoriság-alapú biztonsági mentés ütemezése ahol frequency értéke 24 órában. és a biztonsági másolati tárhely, tárolási hely használatára konfigurált _BackupStore1_. A szabályzat engedélyezéséhez alkalmazás _MyApp_A_ használatával [alkalmazás biztonsági mentés engedélyezése](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) API-t. Ez a művelet lehetővé teszi, hogy az adatok biztonsági mentése biztonsági mentési házirend használatával _BP_1_ az összes partíciója _Reliable Stateful services_ és _Reliable Actors_ alkalmazáshoztartozó _MyApp_A_.

    2. Biztonsági mentési szabályzat létrehozása _BP_2_, a gyakoriság-alapú biztonsági mentés ütemezése ahol gyakoriság értéke 1 óra. és a biztonsági másolati tárhely, tárolási hely használatára konfigurált _BackupStore1_. A szolgáltatás a szabályzat engedélyezéséhez _SvcA3_ használatával [szolgáltatás biztonsági mentés engedélyezése](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) API-t. Ez a művelet felülírja a terjesztését házirend _BP_1_ által kifejezetten engedélyezve a biztonsági mentési szabályzat _BP_2_ a szolgáltatás összes partíció _SvcA3_ és az adatok biztonsági mentése biztonsági mentéssel a házirend _BP_2_ az érintett partíciók.

    3. Biztonsági mentési szabályzat létrehozása _BP_3_, a gyakoriság-alapú biztonsági mentés ütemezése ahol frequency értéke 24 órában. és a biztonsági másolati tárhely, tárolási hely használatára konfigurált _BackupStore2_. A partíció szabályzat engedélyezéséhez _SvcA1_P2_ használatával [partíció biztonsági mentés engedélyezése](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API-t. Ez a művelet felülírja a terjesztését házirend _BP_1_ által kifejezetten engedélyezve a biztonsági mentési szabályzat _BP_3_ partíció _SvcA1_P2_.

2. MyApp_B
    1. Biztonsági mentési szabályzat létrehozása _BP_4_, az időalapú biztonsági mentési ütemezés ahol ütemezéstípus gyakoriságának beállítása heti, futtatási nap vasárnap értékre van állítva, és futási idő értéke 8:00-kor. Biztonsági másolati tárhely, tárolási hely használatára konfigurált _BackupStore1_. A szolgáltatás a szabályzat engedélyezéséhez _SvcB1_ használatával [szolgáltatás biztonsági mentés engedélyezése](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) API-t. Ez a művelet lehetővé teszi, hogy az adatok biztonsági mentése biztonsági mentési házirend használatával _BP_4_ a szolgáltatás összes partíció _SvcB1_.

    2. Biztonsági mentési szabályzat létrehozása _BP_5_, az időalapú biztonsági mentési ütemezés gyakoriságának ütemezéstípus esetén állítsa be napi és a futási idő értéke 8:00-kor. Biztonsági másolati tárhely, tárolási hely használatára konfigurált _BackupStore1_. A partíció szabályzat engedélyezéséhez _SvcB2_P1_ használatával [partíció biztonsági mentés engedélyezése](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API-t. Ez a művelet lehetővé teszi, hogy az adatok biztonsági mentése biztonsági mentési házirend használatával _BP_5_ partíció _SvcB2_P1_.

Következő diagram ábrázolja kifejezetten engedélyezett biztonsági szabályzatok és propagálja a biztonsági mentési szabályzatok.

![Service Fabric-alkalmazás hierarchia][0]

## <a name="disable-backup"></a>Biztonsági mentés letiltása
Biztonsági mentési szabályzatok letiltható, ha nem kell adatainak biztonsági mentését. Biztonsági mentési szabályzat engedélyezve egy _alkalmazás_ egyszerre csak letiltható _alkalmazás_ használatával [tiltsa le az alkalmazás biztonsági mentését](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) API-t, a biztonsági mentési szabályzat engedélyezve van, egy _szolgáltatás_ egyszerre letiltható _szolgáltatás_ használatával [tiltsa le a szolgáltatás biztonsági mentési](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup) API-t, és a biztonsági mentési szabályzat engedélyezve van egy _partíció_ Ezzel letiltható _partíció_ használatával [tiltsa le a partíció biztonsági mentés](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup) API-t.

* A biztonsági mentési szabályzat letiltása egy _alkalmazás_ leállítja az összes rendszeres biztonsági mentések megbízható állapotalapú szolgáltatás partícióinak vagy a Reliable Actor-partíciók miatt a biztonsági mentési szabályzat propagálás történik.

* A biztonsági mentési szabályzat letiltása egy _szolgáltatás_ leállítja az összes rendszeres biztonsági mentések szolgálatának eredményeként terjesztése a biztonsági mentési szabályzatot, a partíciók a _szolgáltatás_.

* A biztonsági mentési szabályzat letiltása egy _partíció_ összes rendszeres adatok biztonsági mentése történik a partíció, a biztonsági mentési szabályzat miatt leáll.

## <a name="suspend--resume-backup"></a>Felfüggeszteni és folytatni a biztonsági mentés
Bizonyos helyzet kérhetik rendszeres biztonsági mentési adatok ideiglenes felfüggesztése. Ilyen esetben a követelmény függően felfüggesztése API is használható, biztonsági mentés egy _alkalmazás_, _szolgáltatás_, vagy _partíció_. Rendszeres biztonsági mentési felfüggesztése tranzitív az alkalmazás-hierarchia a pontról alkalmazásának részfa keresztül. 

* Amikor felfüggesztés alkalmaz egy _alkalmazás_ használatával [felfüggesztése alkalmazás biztonsági mentési](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) API-t, majd a szolgáltatások és az alkalmazás partíciók fel vannak függesztve, az adatok rendszeres biztonsági mentés minden.

* Amikor felfüggesztés alkalmaz egy _szolgáltatás_ használatával [felfüggesztése szolgáltatás biztonsági mentési](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) API-t, majd az összes a partíciók alatt ez a szolgáltatás fel vannak függesztve, az adatok rendszeres biztonsági mentés.

* Amikor felfüggesztés alkalmaz egy _partíció_ használatával [felfüggesztése partíció biztonsági mentési](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) API-t, akkor azt felfüggeszti a partíciók alatt ez a szolgáltatás fel vannak függesztve, az adatok rendszeres biztonsági mentés.

Ha a felfüggesztés szükségességét, majd a rendszeres biztonsági mentését tudja állítani megfelelő folytatása biztonsági mentési API használatával. Rendszeres biztonsági mentés kell folytatni, azonos _alkalmazás_, _szolgáltatás_, vagy _partíció_ ahol felfüggesztették.

* Felfüggesztés lett alkalmazva Ha egy _alkalmazás_, akkor érdemes lehet folytatni, használatával [alkalmazás biztonsági mentés folytatása](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) API-t. 

* Felfüggesztés lett alkalmazva Ha egy _szolgáltatás_, akkor érdemes lehet folytatni, használatával [szolgáltatás biztonsági mentés folytatása](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup) API-t.

* Felfüggesztés lett alkalmazva Ha egy _partíció_, akkor érdemes lehet folytatni, használatával [partíció biztonsági mentés folytatása](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup) API-t.

## <a name="auto-restore-on-data-loss"></a>Adatvesztés automatikus visszaállítását
A szolgáltatás partíció adatvesztést okozhat váratlan hibák miatt. Például a lemez ki, a három replikák egy partícióhoz (többek között az elsődleges replika) lekérdezi sérült, vagy tartalmának végleges törléséig.

A Service Fabric azt észleli, hogy a partíció adatvesztést, ha meghívja `OnDataLossAsync` metódus a partíción felületet, és a partíció adatvesztés nem lesz szükség beavatkozásra vár. Ebben a helyzetben, ha a hatékony biztonsági mentési házirend, a partíció `AutoRestoreOnDataLoss` jelző értékre `true` és a visszaállítás aktivált lekérdezi a legújabb elérhető biztonsági másolat használatával a partíció automatikusan.

## <a name="get-backup-configuration"></a>Biztonsági mentési konfiguráció beolvasása
Különálló API-k elérhetővé válnak a biztonsági mentés konfigurációs adatokat beolvasni egy _alkalmazás_, _szolgáltatás_, és _partíció_ hatókör. [Alkalmazás biztonsági mentés konfigurációs adatainak beolvasása](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [első szolgáltatás biztonsági mentési konfiguráció adatainak](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo), és [partíció biztonsági mentés konfigurációs adatainak lekérése](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) is ezek rendre API-k. Ezen API-k elsősorban, a megfelelő biztonsági mentési szabályzat, hatókör, amikor a biztonsági mentési szabályzat nem alkalmazott és biztonsági mentési felfüggesztés részleteit adja vissza. Következő a következő API-k visszaadott eredmények rövid leírását.

- Biztonsági mentési konfiguráció informace aplikace: biztonsági mentési szabályzat alkalmazások és szolgáltatások és a partíciók az alkalmazáshoz tartozó összes továbbíthassanak házirend alkalmazva részleteit. Az alkalmazás a felfüggesztés információkat is tartalmaz, és a szolgáltatásokat, és partíciók.

- Szolgáltatás biztonsági mentési konfiguráció adatainak: leírja, hatékony biztonsági mentési szabályzat szolgáltatás és a hatókör, amely alkalmazta a házirend és a partíciókat továbbíthassanak házirendjeit. A szolgáltatás és a partíciók a felfüggesztés információkat is tartalmaz.

- Biztonsági mentési konfiguráció adatainak particionálása: hatékony biztonsági mentési szabályzat partíció és a hatókör, amelyen a házirend érvényben volt részleteit. A partíciók a felfüggesztés információkat is tartalmaz.

## <a name="list-available-backups"></a>Lista elérhető biztonsági másolatok

Elérhető biztonsági másolatok első biztonsági mentési lista API használatával is megadható. API-hívás eredménye a biztonsági mentési tár, a megfelelő biztonsági mentési szabályzathoz beállított elérhető összes biztonsági mentés kapcsolódó biztonsági mentési adatok elemet tartalmaz. Ez az API különböző változatai listában elérhető biztonsági másolatok egy alkalmazás, szolgáltatás vagy partíció tartozó vannak megadva. Ezen API-k támogatják az első a _legújabb_ elérhető biztonsági másolat az összes vonatkozó partícióra, vagy a biztonsági mentések szűrése alapján _kezdő dátum_ és _befejező dátum_.

Ezen API-k is támogatja az eredmények tördelés, amikor _MaxResults_ paraméter értéke nem nulla értékű pozitív egész szám, akkor az API-t adja vissza a maximális _MaxResults_ biztonsági mentési adatok elemek. Abban az esetben, érhetők el további biztonsági mentési adatok elemek, mint a _MaxResults_ érték, akkor egy folytatási tokent ad vissza. Érvénytelen folytatási token paraméter használható beolvasni a következő eredményeket készlete. Érvénytelen folytatási token értékét a következő API-hívás átadott, amikor az API-t eredmények következő készletét adja vissza. A rendszer az összes rendelkezésre álló eredményeket ad folytatási kód nem szerepel a válasz.

Alábbiakban a támogatott variantní hodnoty rövid ismertetését.

- [Alkalmazás biztonsági mentés listájának lekérése](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): Service Fabric-alkalmazás a megadott tartozó minden partíció esetében elérhető biztonsági másolatok listáját adja vissza.

- [Szolgáltatás biztonsági mentési listájának lekérése](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist): Service Fabric-szolgáltatást a megadott tartozó minden partíció esetében elérhető biztonsági másolatok listáját adja vissza.
 
- [Első biztonsági mentés Partíciólista](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): a megadott partíció számára elérhető biztonsági másolatok listáját adja vissza.

## <a name="next-steps"></a>További lépések
- [Biztonsági másolat visszaállítása – REST API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/BackupPolicyAssociationExample.png