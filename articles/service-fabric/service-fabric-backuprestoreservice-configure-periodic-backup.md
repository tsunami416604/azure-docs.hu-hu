---
title: Rendszeres biztonsági mentési konfiguráció ismertetése
description: Használja a Service Fabric rendszeres biztonsági mentési és visszaállítási szolgáltatását az alkalmazásadatok rendszeres adatbiztonsági mentésének engedélyezéséhez.
author: hrushib
ms.topic: article
ms.date: 2/01/2019
ms.author: hrushib
ms.openlocfilehash: 34c6495e094a1160f6ac75b9f098934d5cbce967
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610148"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Az Azure Service Fabric időszakos biztonsági mentési konfigurációjának ismertetése

A megbízható állapotalapú szolgáltatások vagy a Reliable Actors rendszeres biztonsági mentésének konfigurálása a következő lépésekből áll:

1. **Biztonsági mentési házirendek létrehozása:** Ebben a lépésben egy vagy több biztonsági mentési szabályzat jön létre a követelményektől függően.

2. **Biztonsági mentés engedélyezése**: Ebben a lépésben az _Partition_ **1.** _Application_ _Service_

## <a name="create-backup-policy"></a>Biztonsági másolat házirendjének létrehozása

A biztonsági mentési házirend a következő konfigurációkból áll:

* **Automatikus visszaállítás adatvesztés esetén**: Megadja, hogy a visszaállítás automatikusan elinduljon-e a legújabb rendelkezésre álló biztonsági mentés használatával, ha a partíció adatvesztési eseményt tapasztal.

* **Maximális növekményes biztonsági mentések:** Két teljes biztonsági mentés között a növekményes biztonsági mentések maximális számát határozza meg. A maximális növekményes biztonsági mentések határozzák meg a felső korlátot. Teljes biztonsági mentés készíthető, mielőtt a megadott számú növekményes biztonsági mentés tana van az alábbi feltételek egyikében

    1. A replika soha nem vett teljes biztonsági mentést, mivel elsődlegesvé vált.

    2. A legutóbbi biztonsági mentés óta a naplórekordok egy része csonkolva lett.

    3. A replika túllépte a MaxAccumulatedBackupLogSizeInMB korlátot.

* **Biztonsági mentés ütemezése**: Az az idő vagy gyakoriság, amelyen rendszeres biztonsági mentéseket kell készíteni. A biztonsági mentések ismétlődőként történő ütemezése meghatározott időközönként vagy egy meghatározott időpontban naponta / hetente.

    1. **Gyakoriságalapú biztonsági mentés ütemezése:** Ezt az ütemezéstípust kell használni, ha az adatok biztonsági mentését rögzített időközönként kell készíteni. A két egymást követő biztonsági mentés közötti kívánt időintervallumot ISO8601 formátumban definiálja. A gyakoriságalapú biztonsági mentésütemezés támogatja az időköz felbontását a percig.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Időalapú biztonsági mentés ütemezése**: Ezt az ütemezéstípust kell használni, ha az adatok biztonsági mentését a nap vagy a hét meghatározott időpontjaiban kell készíteni. Az ütemezés gyakoriságának típusa lehet napi vagy heti.
        1. ** _Napi_ időalapú biztonsági mentésütemezés:** Ezt az ütemezési típust kell használni, ha az id-nek a nap meghatározott időpontjaiban kell biztonsági másolatot készítenie. Ennek megadásához `ScheduleFrequencyType` állítsa _a Napi_; és `RunTimes` állítsa a kívánt időt a nap folyamán ISO8601 formátumban, megadott dátum mellett az idő figyelmen kívül hagyja. Például, `0001-01-01T18:00:00` képviseli _6:00 pm_ mindennapi, figyelmen kívül hagyva dátum rész _0001-01-01_. Az alábbi példa bemutatja a napi biztonsági mentés indítását _napi 9:00_ és _18:00 órakor_ minden nap.

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

        2. ** _Heti_ időalapú biztonsági mentés ütemezése:** Ezt az ütemezési típust kell használni, ha az id-nek a nap meghatározott időpontjaiban kell biztonsági másolatot készítenie. Ennek megadásához `ScheduleFrequencyType` állítsa _heti_; állítsa `RunDays` be a hét azon napjainak listájára, `RunTimes` amikor a biztonsági mentést el kell indítani, és a nap folyamán a kívánt idő listájára kell állítani Az ISO8601 formátumban, a megadott dátumot az idővel együtt figyelmen kívül hagyja. A hét azon napjainak listája, amikor a rendszeres biztonsági mentés t indítja el. Az alábbi példa a napi biztonsági mentés hétfőtől péntekig _9:00_ és _18:00 órakor_ való indításának konfigurációját mutatja be.

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

* **Biztonsági másolat tárolása**: Megadja a biztonsági másolatok feltöltésének helyét. A storage lehet Azure blob áruház vagy fájlmegosztás.
    1. **Azure blob store:** Ezt a tárolási típust kell kiválasztani, ha a létrehozott biztonsági mentések tárolása az Azure-ban. Mind _az önálló,_ mind _az Azure-alapú_ fürtök használhatják ezt a tárolási típust. Ennek a tárolótípusnak a leírása kapcsolati karakterláncot és annak a tárolónak a nevét igényli, ahol a biztonsági másolatokat fel kell tölteni. Ha a megadott nevű tároló nem érhető el, majd egy biztonsági másolat feltöltése során jön létre.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Fájlmegosztás**: Ezt a tárolási típust kell kiválasztani _az önálló_ fürtökhöz, ha az adatok biztonsági mentését a helyszínen kell tárolni. Ennek a tárolási típusnak a leírásához fájlmegosztási elérési út szükséges, ahol a biztonsági másolatokat fel kell tölteni. A fájlmegosztáshoz való hozzáférés az alábbi lehetőségek egyikével konfigurálható
        1. _Integrált Windows-hitelesítés_, ahol a fájlmegosztáshoz való hozzáférés a Service Fabric-fürthöz tartozó összes számítógép számára biztosított. Ebben az esetben állítsa be a következő mezőket a _fájlmegosztásalapú_ biztonsági mentési tároló konfigurálásához.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _A fájlmegosztás védelme felhasználónévvel és jelszóval,_ ahol a fájlmegosztáshoz való hozzáférés adott felhasználók számára biztosított. A fájlmegosztási tárolási specifikáció lehetővé teszi másodlagos felhasználónév és másodlagos jelszó megadását is, hogy tartalék hitelesítő adatokat adjon meg abban az esetben, ha a hitelesítés nem sikerül az elsődleges felhasználónévvel és az elsődleges jelszóval. Ebben az esetben állítsa be a következő mezőket a _fájlmegosztásalapú_ biztonsági mentési tároló konfigurálásához.

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
> Győződjön meg arról, hogy a tárolási megbízhatóság megfelel vagy meghaladja a biztonsági mentési adatok megbízhatósági követelményeit.
>

* **Adatmegőrzési szabály**: Megadja a biztonsági mentések megőrzésére szolgáló házirendet a konfigurált tárolóban. Csak az alapvető adatmegőrzési házirend támogatott.
    1. **Alapszintű adatmegőrzési szabály:** Ez az adatmegőrzési szabály lehetővé teszi az optimális tárolási kihasználtság biztosítását olyan biztonsági mentési fájlok eltávolításával, amelyekre már nincs szükség. `RetentionDuration`megadható, hogy beállítsa azt az időtartamot, amelyhez a biztonsági mentések megőrzése szükséges a tárolóban. `MinimumNumberOfBackups`egy választható paraméter, amely megadható annak érdekében, hogy a megadott számú biztonsági `RetentionDuration`másolat mindig megmaradjon, függetlenül atól. Az alábbi példa bemutatja a biztonsági mentések _10_ napig tartó megőrzésének konfigurációját, és nem teszi lehetővé, hogy a biztonsági mentések száma _20_alá csökkenjen .

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Rendszeres biztonsági mentés engedélyezése
Az adatmentési követelmények teljesítéséhez szükséges biztonsági mentési házirend meghatározása után a biztonsági mentési házirendet megfelelően társkell-e társviszonyba állítani egy _alkalmazáshoz_, _szolgáltatáshoz_vagy _egy partícióhoz._

### <a name="hierarchical-propagation-of-backup-policy"></a>A biztonsági mentési házirend hierarchikus terjesztése
A Service Fabric, az alkalmazás, a szolgáltatás és a partíciók közötti kapcsolat hierarchikus az [application modellben](./service-fabric-application-model.md)leírtak szerint. A biztonsági mentési házirend társítható egy _alkalmazáshoz_, _szolgáltatáshoz_vagy egy _partícióhoz_ a hierarchiában. A biztonsági mentési házirend hierarchikusan a következő szintre propagál. Feltételezve, hogy csak egy biztonsági mentési házirend jön létre, és egy _alkalmazáshoz_társított, az összes _megbízható állapotalapú szolgáltatások_ és az _alkalmazás_ _megbízható szereplői_ a biztonsági mentési szabályzat használatával lesz biztonsági mentés. Vagy ha a biztonsági mentési szabályzat egy _megbízható állapotalapú szolgáltatáshoz_van társítva, az összes partícióról a biztonsági mentési házirend használatával lesz biztonsági másolat.

### <a name="overriding-backup-policy"></a>A biztonsági mentési házirend felülbírálása
Előfordulhat, hogy az alkalmazás összes szolgáltatásához azonos biztonsági mentési ütemezéssel rendelkező adatbiztonsági mentésre van szükség, kivéve azokat az egyes szolgáltatásokat, ahol az adatok biztonsági mentésének magasabb gyakoriságú ütemezéssel történő biztonsági mentésére vagy egy másik tárfiókba történő biztonsági mentésre van szükség, vagy fájlmegosztás. Az ilyen esetek megoldásához a biztonsági mentési visszaállítási szolgáltatás lehetővé teszi a szolgáltatás és a partíció hatókörének propagált házirendjének felülbírálásához. Ha a biztonsági mentési házirend _szervizben_ vagy _partíción_van társítva, felülírja a propagált biztonsági mentési házirendet, ha van ilyen.

### <a name="example"></a>Példa

Ez a példa két alkalmazással , _MyApp_A_ és _MyApp_B_beállítást használ. Alkalmazás _MyApp_A_ két megbízható állapotalapú szolgáltatások, _SvcA1_ & _SvcA3_, és egy megbízható szereplő szolgáltatás, _ActorA2_. _Az SvcA1_ három partíciót tartalmaz, míg _az ActorA2_ és _az SvcA3_ két partíciót tartalmaz.  Az alkalmazás _MyApp_B_ három megbízható állapotalapú szolgáltatást tartalmaz, _az SvcB1,_ _az SvcB2_és az _SvcB3_szolgáltatást. _Az SvcB1_ és _az SvcB2_ két partíciót tartalmaz, míg _az SvcB3_ három partíciót.

Tegyük fel, hogy ezek az alkalmazások adatbiztonsági követelményei a következők:

1. MyApp_A
    1. Hozzon létre napi biztonsági másolatot az adatok az összes _megbízható állapotalapú szolgáltatások_ és az alkalmazáshoz tartozó _megbízható szereplők_ összes partícióját. Töltse fel a biztonsági mentési adatokat a _BackupStore1_helyre.

    2. Az egyik szolgáltatás, _az SvcA3_óránként adatmentést igényel.

    3. Az adatok mérete a _partíción SvcA1_P2_ a vártnál nagyobb, és a biztonsági mentési adatokat kell tárolni a különböző tárolási hely _BackupStore2_.

2. MyApp_B
    1. Készítsen biztonsági másolatot az adatokról minden vasárnap 8:00 órakor az _SvcB1_ szolgáltatás összes partíciójához. Töltse fel a biztonsági mentési adatokat a _BackupStore1_helyre.

    2. Minden nap 8:00 órakor készítsen biztonsági másolatot az adatokról a _partícióSvcB2_P1._ Töltse fel a biztonsági mentési adatokat a _BackupStore1_helyre.

Ezen adatbiztonsági követelmények teljesítése érdekében a BP_5 BP_1 biztonsági mentési házirendek jönnek létre, és a biztonsági mentés az alábbiak szerint engedélyezve van.
1. MyApp_A
    1. Hozzon létre biztonsági mentési házirendet, _BP_1,_ gyakoriságalapú biztonsági mentési ütemezéssel, ahol a gyakoriság 24 óra. és a _BackupStore1_tárolási hely használatára konfigurált biztonsági mentési tároló. Engedélyezze ezt a házirendet az _Alkalmazás-MyApp_A_ [alkalmazásbiztonsági mentés engedélyezése](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) API használatával. Ez a művelet lehetővé teszi az adatok biztonsági mentését biztonsági mentési _házirend BP_1_ használatával a _megbízható állapotalapú szolgáltatások_ és az _alkalmazáshoz_tartozó megbízható _szereplők_ összes MyApp_A.

    2. Hozzon létre biztonsági mentési házirendet, _BP_2,_ gyakoriságalapú biztonsági mentési ütemezéssel, ahol a gyakoriság 1 óra. és a _BackupStore1_tárolási hely használatára konfigurált biztonsági mentési tároló. Engedélyezze ezt a házirendet az _SvcA3_ szolgáltatáshoz [a Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) API engedélyezése használatával. Ez a művelet felülbírálja a propagált _házirendBP_1_ az _SvcA3_ szolgáltatás összes partíciójára vonatkozó, kifejezetten engedélyezett biztonsági mentési _házirend BP_2,_ amely adatbiztonsági mentéshez vezet ezen partíciók biztonsági mentési _házirendBP_2_ használatával.

    3. Hozzon létre biztonsági mentési szabályzatot, _BP_3,_ gyakoriságalapú biztonsági mentési ütemezéssel, ahol a gyakoriság 24 óra. és a _BackupStore2_tárolási hely használatára konfigurált biztonsági mentési tároló. Engedélyezze ezt a házirendet a _partícióSvcA1_P2_ a [Partícióbiztonsági mentés engedélyezése](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API használatával. Ez a művelet felülbírálja a _partícióSvcA1_P2_kifejezetten engedélyezett biztonsági mentési _házirend-BP_3_ által _BP_1_ propagált házirendet.

2. MyApp_B
    1. Hozzon létre biztonsági mentési házirendet, _BP_4_időalapú biztonsági mentési ütemezéssel, ahol az ütemezés gyakoriságának típusa heti, a futtatási napok vasárnapra, a futási idők pedig 8:00-ra vannak állítva. A BackupStore1 tárolóhely használatára konfigurált biztonsági másolat _tárolója._ Engedélyezze ezt a házirendet az _SvcB1_ szolgáltatáshoz [a Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) API engedélyezése használatával. Ez a művelet lehetővé teszi az adatok biztonsági mentését az _SvcB1_szolgáltatás összes partíciójának biztonsági mentési _BP_4_ használatával.

    2. Hozzon létre biztonsági mentési házirendet, _BP_5_időalapú biztonsági mentési ütemezéssel, ahol az ütemezés gyakoriságának típusa napi és futtatási idő 8:00-ra van állítva. A BackupStore1 tárolóhely használatára konfigurált biztonsági másolat _tárolója._ Engedélyezze ezt a házirendet a _partícióSvcB2_P1_ a [Partícióbiztonsági mentés engedélyezése](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API használatával. Ez a művelet lehetővé teszi az adatmentést a _partícióSvcB2_P1_biztonsági mentési _házirendjének BP_5_ használatával.

Az alábbi ábra explicit módon engedélyezett biztonsági mentési házirendeket és propagált biztonsági mentési házirendeket ábrázol.

![Szolgáltatásháló alkalmazáshierarchiája][0]

## <a name="disable-backup"></a>Biztonsági másolat letiltása
A biztonsági mentési házirendek letilthatók, ha nincs szükség adatok biztonsági mentéséhez. Egy _alkalmazásban_ engedélyezett biztonságimentési házirend csak ugyanabban az _alkalmazásban_ tiltható le az [Alkalmazásbiztonsági mentés letiltása](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) API-val, a _szolgáltatásban_ engedélyezett biztonsági mentési házirend ugyanazon a _szolgáltatáson_ tiltható le a Service Backup API [letiltása](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup) funkcióval, a _partíción_ engedélyezett biztonsági mentési házirend pedig ugyanazon a _partíción_ tiltható le a [Partícióbiztonsági mentési API letiltása](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup) funkcióval.

* Egy _alkalmazás_ biztonsági mentési házirendjének letiltása leállítja az összes időszakos adatbiztonsági mentést a biztonsági mentési házirend megbízható állapotalapú szolgáltatáspartíciókra vagy reliable actor partíciókra való propagálása miatt.

* A _szolgáltatás_ biztonsági mentési házirendjének letiltása leállítja az összes rendszeres adatbiztonsági mentést, amely a biztonsági mentési házirendnek a _szolgáltatás_partícióira történő propagálása miatt történik.

* A _partíció_ biztonsági mentési házirendjének letiltása leállítja az összes rendszeres adatbiztonsági mentést a partícióbiztonsági házirend miatt.

* Egy entitás (alkalmazás/szolgáltatás/partíció) `CleanBackup` biztonsági mentésének letiltása közben igaz értékre állítható be a konfigurált tárolóösszes biztonsági másolatának törléséhez. _true_
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>A & a biztonsági mentés folytatásának felfüggesztése
Bizonyos helyzetekben az adatok rendszeres biztonsági mentésének ideiglenes felfüggesztését követelhetik. Ebben az esetben a követelménytől függően a biztonsági mentési API felfüggesztése alkalmazáson _,_ _szolgáltatáson_vagy _partíción_is használható. Az időszakos biztonsági mentés felfüggesztése tranzitív az alkalmazás hierarchiájának részfája felett az alkalmazási ponttól. 

* Ha a felfüggesztés t alkalmaz egy _alkalmazás_ alkalmazás biztonsági [mentési API-t](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) használó, majd az alkalmazás alatt az összes szolgáltatás és partíciók felfüggesztették az adatok rendszeres biztonsági mentése.

* Ha a felfüggesztést egy _szolgáltatás_ a [Szolgáltatás biztonsági mentési](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) API felfüggesztésével alkalmazza, majd a szolgáltatás összes partícióját felfüggeszti az adatok rendszeres biztonsági mentése.

* Ha a felfüggesztés t alkalmaz egy _partíción_ a [Partíció biztonsági mentési](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) API felfüggesztése, majd felfüggeszti partíciók at ez a szolgáltatás felfüggesztették az adatok rendszeres biztonsági mentése.

Miután a felfüggesztés szükségessége véget ért, akkor az időszakos adatmentés visszaállítható a megfelelő önéletrajz-biztonsági mentési API-val. Az időszakos biztonsági mentést ugyanabban az _alkalmazásban_, _szolgáltatásban_vagy _partíción_ kell folytatni, ahol felfüggesztették.

* Ha egy _alkalmazásfelfüggesztéset_alkalmaztak, akkor a [Folytatás alkalmazásbiztonsági](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) API-val kell folytatni. 

* Ha egy _szolgáltatás_felfüggesztése lett alkalmazva, akkor a [szolgáltatásbiztonsági mentési](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup) API-t kell használnia.

* Ha a felfüggesztést egy _partíción_alkalmazták, akkor a [Folytatás partíció biztonsági mentési](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup) API-jával kell folytatni.

### <a name="difference-between-suspend-and-disable-backups"></a>Különbség a biztonsági mentések felfüggesztése és letiltása között
A biztonsági mentés letiltását akkor kell használni, ha egy adott alkalmazáshoz, szolgáltatáshoz vagy partícióhoz már nincs szükség biztonsági másolatokra. Lehet meghívni tiltsa biztonsági mentési kérelmet együtt tiszta biztonsági mentés paraméter igaz, ami azt jelentené, minden meglévő biztonsági mentések törlődnek is. Azonban a felfüggesztésolyan esetekben használatos, amikor az ember ideiglenesen ki akarja kapcsolni a biztonsági mentéseket, például amikor a helyi lemez megtelik, vagy a biztonsági mentés feltöltése ismert hálózati probléma miatt sikertelen stb. 

Bár a letiltás csak olyan szinten hívható meg, amely korábban kifejezetten engedélyezve volt a biztonsági mentéshez, azonban a felfüggesztés bármely olyan szinten alkalmazható, amely jelenleg engedélyezve van a biztonsági mentéshez közvetlenül vagy öröklési/ hierarchia útján. Ha például a biztonsági mentés alkalmazásszinten engedélyezve van, csak az alkalmazás szintjén lehet meghívni a letiltást, azonban a felfüggesztés meghívható az alkalmazás, az adott alkalmazás bármely szolgáltatása vagy partíciója. 

## <a name="auto-restore-on-data-loss"></a>Automatikus visszaállítás adatvesztés esetén
A szolgáltatáspartíció váratlan hibák miatt adatokat veszíthet. Például a partíció (beleértve az elsődleges replikát is) három replikájából kettő lemeze megsérül vagy törlődik.

Amikor a Service Fabric észleli, hogy a `OnDataLossAsync` partíció adatvesztés, meghívja a partíción a felület metódusát, és elvárja, hogy a partíció tegye meg a szükséges műveletet az adatvesztésből való kijövetelhez. Ebben a helyzetben, ha a partíció `AutoRestoreOnDataLoss` hatékony `true` biztonsági mentési házirendje jelzőt állít be, majd a visszaállítás automatikusan aktiválódik a partíció legújabb rendelkezésre álló biztonsági mentésének használatával.

## <a name="get-backup-configuration"></a>Biztonsági másolat konfigurálásának beszereznie
Külön API-k érhetők el, hogy biztonsági mentési konfigurációs információkat kapjon egy _alkalmazáson_, _szolgáltatáson_és _partícióhatókörön._ [Az alkalmazásbiztonsági mentés konfigurációs adatainak beszereznie](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [a szolgáltatás biztonsági másolatának konfigurációs adatainak beszereznie](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)és [a partícióbiztonsági másolat konfigurációs adatai](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) ezek az API-k. Ezek az API-k főként a vonatkozó biztonsági mentési szabályzatot, hatókört, amelynél a biztonsági mentési szabályzat alkalmazása és a biztonsági mentés felfüggesztésének részletei jelennek meg. Az alábbiakban röviden ismertetjük ezeket az API-kat.

- Alkalmazás biztonsági másolat konfigurációs információk: az alkalmazáson alkalmazott biztonsági mentési házirend részleteit, valamint az alkalmazáshoz tartozó szolgáltatások és partíciók túlterhelt házirendek részleteit tartalmazza. Azt is tartalmazza a felfüggesztési információkat az alkalmazás és az informatikai szolgáltatások, és a partíciók.

- Szolgáltatás biztonsági mentéskonfigurációjának adatai: a szolgáltatás hatékony biztonsági mentési házirendjének részleteit, valamint a házirend alkalmazásának hatókörét és a partíciók on-over-ridden házirendek részleteit tartalmazza. A szolgáltatás és partíciói felfüggesztési adatait is tartalmazza.

- Partíció biztonsági mentéskonfigurációjának adatai: a partíción érvényes biztonsági mentési házirend részleteit és a házirend alkalmazásának hatókörét tartalmazza. A partíciók felfüggesztési adatait is tartalmazza.

## <a name="list-available-backups"></a>Az elérhető biztonsági mentések listája

Az elérhető biztonsági mentések a Biztonsági másolat lista beszerzése API használatával sorolhatók fel. Az API-hívás eredménye tartalmazza a biztonsági mentési adatok at kapcsolatos biztonsági mentési tároló, amely a megfelelő biztonsági mentési szabályzatban konfigurált biztonsági mentési adatok kapcsolatos biztonsági mentési elemeket tartalmaz. Az API különböző változatai egy alkalmazáshoz, szolgáltatáshoz vagy partícióhoz tartozó rendelkezésre álló biztonsági mentések listázásához érhetők el. Ezek az API-k támogatják az összes vonatkozó partíció _legfrissebb_ rendelkezésre álló biztonsági mentésének beszerzését, illetve a biztonsági mentések _szűrését_ a kezdési és _befejezési dátum_alapján.

Ezek az API-k is támogatják az eredmények tördelését, ha a _MaxResults_ paraméter értéke nem nulla pozitív egész szám, majd az API maximális _MaxResults_ biztonsági mentési információs elemeket ad vissza. Abban az esetben, több biztonsági mentési információ elemek állnak rendelkezésre, mint a _MaxResults_ érték, majd a folytatási jogkivonatot adja vissza. Az érvényes folytatási token paraméter használható a következő eredményhalmaz lekéréséhez. Ha érvényes folytatási token értéket ad át az API következő hívása, az API a következő eredményhalmazt adja vissza. A válasz nem tartalmaz folytatási jogkivonatot, ha az összes rendelkezésre álló eredményt visszaadja.

Az alábbiakban rövid információt a támogatott változatok.

- [Alkalmazásbiztonsági lista beszerzése:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist)Az adott Service Fabric-alkalmazáshoz tartozó minden partícióhoz elérhető biztonsági mentések listáját adja vissza.

- [Szolgáltatás biztonsági másolatlistájának beszerzése:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist)Az adott Service Fabric-szolgáltatáshoz tartozó minden partícióhoz elérhető biztonsági mentések listáját adja vissza.
 
- [Partícióbiztonsági lista beszerzése](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): A megadott partícióhoz rendelkezésre álló biztonsági mentések listáját adja vissza.

## <a name="next-steps"></a>További lépések
- [Rest API-kézikönyv biztonsági mentésének visszaállítása](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
