---
title: Az Azure importálási/exportálási feladat állapotinformációinak lekérése |} A Microsoft Docs
description: Ismerje meg, hogy a Microsoft Azure Import/Export szolgáltatás feladatok állapotinformációinak beszerzése.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 1a878b5a9f0502ff9acd411359895d7431fb76f4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437242"
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Importálási/exportálási feladat állapotinformációinak lekérése
Hívása a [Get Job](/rest/api/storageimportexport/jobs) mindkettő adatainak beolvasásához művelet importálja és a feladatok exportálása. A visszaadott információ tartalmazza:

-   A feladat jelenlegi állapota.

-   A hozzávetőleges százalékos, hogy minden feladat befejeződött-e.

-   Minden olyan meghajtó aktuális állapotát.

-   URL-címének blobokat tartalmazó hibanaplókat és részletes naplózási információk (Ha engedélyezve van).

Az alábbi szakaszok ismertetik a által visszaadott adatokat a `Get Job` műveletet.

## <a name="job-states"></a>Feladatállapotok
A táblázat és az állapot az alábbi ábrán ismertetik az államok, amely egy feladat átmenetek keresztül életciklusa során. A feladat jelenlegi állapota meghívásával lehet meghatározni a `Get Job` műveletet.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

A következő táblázat ismerteti az egyes állapot esetében egy feladat mehet keresztül.

|Feladat állapota|Leírás|
|---------------|-----------------|
|`Creating`|Miután a feladat Put művelet meghívásával, létrejön egy feladat és az állapot értéke `Creating`. A feladat, amíg a `Creating` állapotba, az Import/Export szolgáltatás feltételezi, hogy rendelkezik a meghajtók nem elküldtük Önnek az adatközpontban. Egy feladat maradhatnak a `Creating` akár két hét, amely után automatikusan törli a szolgáltatás által állapotban.<br /><br /> Akkor, ha meghívja a frissítési feladat tulajdonságai műveletet a feladat, amíg a `Creating` állapotban marad a feladat a `Creating` állapotát és a időkorlátja visszaáll a két hét.|
|`Shipping`|Szállít a csomagot, akkor meg kell hívnia a feladat tulajdonságainak frissítése művelet frissítés a feladat állapotának `Shipping`. A szállítási állapotot állítható csak akkor, ha a `DeliveryPackage` (postai szállítmányozó és nyomkövetési azonosító szám) és a `ReturnAddress` tulajdonságok lettek beállítva a feladat.<br /><br /> A feladat két héttel a szállítási állapotban marad. Ha két hét teljesül, és a meghajtók nem érkeznek, az importálási/exportálási szolgáltatás operátorok kap értesítést.|
|`Received`|Miután az összes meghajtó megérkeztek az adatközpontban, a feladat állapotát állítja be a fogadott állapot.|
|`Transferring`|Miután a meghajtók megérkeztek az adatközpontban, és legalább egy meghajtó már megkezdődött a feldolgozás, a feladat állapotát állítja be a `Transferring` állapota. Tekintse meg a `Drive States` szakasz alább részletes információkat.|
|`Packaging`|Miután az összes meghajtó feldolgozása befejeződött, a feladat kerülnek a `Packaging` állapot, amíg vissza az ügyfélnek a meghajtókat tartalmazza a szükséges.|
|`Completed`|Miután az összes meghajtó térjen vissza az ügyfél rendelkezik elküldtük Önnek, ha a feladat hiba nélkül befejeződött, majd a feladat értékre lesz beállítva az `Completed` állapota. A feladat automatikusan törlődni fognak a 90 nap után a `Completed` állapota.|
|`Closed`|Miután az összes meghajtó térjen vissza az ügyfél rendelkezik elküldtük Önnek, ha sor került esetleges hibákat a feladat feldolgozása során, majd a feladat értékre lesz beállítva az `Closed` állapota. A feladat automatikusan törlődni fognak a 90 nap után a `Closed` állapota.|

Egy feladat csak egyes állapotok lemondhatja a szolgáltatásokat. Megszakított feladat kihagyja az adatok másolása lépés, de ellenkező esetben az azonos állapotváltásra egy feladatot, amely nem lett törölve, következik.

A következő táblázat ismerteti a hibákat, amelyek előfordulhatnak esetében minden egyes feladat állapotát, valamint a feladat milyen hatása, ha hiba lép fel.

|Feladat állapota|Esemény|Megoldás / a következő lépések|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|A feladat egy vagy több meghajtó megérkezett, de a feladat nem szerepel a következőben a `Shipping` állapotban vagy nem tartozik rekord a feladat a szolgáltatásban.|Az Import/Export szolgáltatás üzemeltetési csapat megpróbálja létrehozni vagy frissíteni a feladathoz szükséges adatokat a feladat előrelépés ügyfél.<br /><br /> Ha az üzemeltetési csapat tud kapcsolatba lépni az ügyfél két héten belül, az üzemeltetési csapat próbálja meg a meghajtók adja vissza.<br /><br /> Abban az esetben a meghajtó nem téríthetők vissza, és az ügyfél nem érhető el, a meghajtók biztonságosan megsemmisülnek 90 nap során.<br /><br /> Vegye figyelembe, hogy egy feladatot nem lehet feldolgozni, amíg az állapot frissül, és `Shipping`.|
|`Shipping`|A csomag egy feladat több mint két hétig nem érkezett.|Az üzemeltetési csapat értesíti az ügyfél-csomag hiányzik. Alapján az ügyfél részéről, az üzemeltetési csapat fog hosszabbítsa meg a csomag érkezik várakozási időt, vagy szakítsa meg a feladatot.<br /><br /> Abban az esetben az ügyfél nem érhető el, vagy 30 napon belül nem válaszol, az üzemeltetési csapat kezdeményez műveletet helyezze át a feladatot a `Shipping` állapot közvetlenül a `Closed` állapota.|
|`Completed/Closed`|Soha nem a meghajtók elérte a címet, vagy sérült a szállítmány (csak exportálási feladat vonatkozik).|A válaszcím nem jutnak a meghajtók, ha az ügyfél kell először a Get Job művelet meghívása, vagy ellenőrizze a feladat állapota a portálon győződjön meg arról, hogy a meghajtók kiszállítása lett-e. Ha a meghajtók kiszállítása lett, az ügyfél a kézbesítési szolgáltató kipróbálná, és keresse meg a meghajtók értesítse.<br /><br /> Ha a meghajtók szállításáról során egyaránt megsérül, az ügyfél kérheti egy másik exportálási feladatot, vagy töltse le a hiányzó érdemes.|
|`Transferring/Packaging`|Feladat rendelkezik egy hibás vagy hiányzó Válaszcím.|Az üzemeltetési csapat kapcsolatba fog lépni a kapcsolattartó, a feladat a helyes cím beszerzéséhez.<br /><br /> Abban az esetben, ha az ügyfél nem érhető el, a meghajtók biztonságosan megsemmisülnek 90 nap során.|
|`Creating / Shipping/ Transferring`|A szállítási címhez tartozó csomag olyan meghajtón, amely nem jelenik meg az importálni kívánt meghajtók listáját tartalmazza.|A további meghajtók nem dolgozható fel, és ad vissza az ügyfélnek a feladat elvégzésekor.|

## <a name="drive-states"></a>Meghajtó állapota
A táblázat és az alábbi ábrán ismertetik a meghajtók egyéni életciklusát, azt egy importálási vagy exportálási feladatot keresztül értékre vált. Meghívásával kérheti le az aktuális meghajtó állapotát a `Get Job` művelet és a vizsgálat a `State` eleme a `DriveList` tulajdonság.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

A következő táblázat ismerteti az egyes állapot esetében egy meghajtó mehet keresztül.

|Meghajtó állapota|Leírás|
|-----------------|-----------------|
|`Specified`|Importálási feladatokhoz, ha a feladat jön létre feladat Put művelet esetén a kezdeti meghajtó állapota a `Specified` állapota. Exportálási feladatokhoz, mivel nincs meghajtó van megadva, a feladat létrehozásakor a kezdeti meghajtó állapota a `Received` állapota.|
|`Received`|A meghajtó átkerül a `Received` állapot, amikor az Import/Export szolgáltatást üzemeltető feldolgozta a szállító cég importálási feladatokhoz érkezett meghajtókat. Exportálási feladatokhoz, a kezdeti meghajtó állapota a `Received` állapota.|
|`NeverReceived`|A meghajtó helyezi át a `NeverReceived` állapot, amikor egy feladat a csomag érkezik, de a csomag nem tartalmazza a meghajtó. A meghajtó is áthelyezheti ebbe az állapotba, ha két hét volt, mert a szolgáltatás a kézbesítési kapott, de a csomag még nem érkezett meg az adatközpontot.|
|`Transferring`|A meghajtó helyezi át a `Transferring` állapotba, ha a szolgáltatás elkezdi a meghajtó Windows Azure Storage-adatok átvitele.|
|`Completed`|A meghajtó helyezi át a `Completed` állapotba, ha a szolgáltatás sikeresen át lett adva hiba nélkül az adatok.|
|`CompletedMoreInfo`|A meghajtó helyezi át a `CompletedMoreInfo` állapotba, ha a szolgáltatás észlelt bizonyos problémák a meghajtóra vagy az adatok másolása közben. Az adatok között szerepelhetnek, hibák, figyelmeztetések és információs üzenetek blobok felülírására.|
|`ShippedBack`|A meghajtó helyezi át a `ShippedBack` állapota akkor azt elküldtük Önnek az data center biztonsági mentésekből, a feladó címe.|

A következő táblázat ismerteti a meghajtó hiba állapotait és az egyes állapotokhoz szinteknél végrehajtott műveleteket.

|Meghajtó állapota|Esemény|Megoldás / a következő lépés|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|Olyan meghajtón, amely van megjelölve `NeverReceived` (mert nem érkezett a feladat szállításáról részeként) egy másik szállítmány érkezik.|Az üzemeltetési csapat áthelyezi a meghajtót a `Received` állapota.|
|`N/A`|Olyan meghajtón, amely nem tartozik minden olyan feladat megérkezik az adatközpontban egy másik feladat részeként.|A meghajtó egy extra meghajtóként lesz megjelölve, és ad vissza az ügyfélnek a az eredeti csomaggal kapcsolatos feladat elvégzésekor.|

## <a name="faulted-states"></a>Hibás állapotok
Ha egy feladat vagy a meghajtó nem várható élettartama során általában halad, a feladat vagy a meghajtó lesz helyezve egy `Faulted` állapota. Ezen a ponton a műveleti csapata kapcsolatba lép az ügyfél e-mailben vagy telefonon. Ha a probléma nem oldódik meg, a hibás feladat vagy a meghajtó kívüli készül a `Faulted` állami és a megfelelő állapotba áthelyezett.

## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
