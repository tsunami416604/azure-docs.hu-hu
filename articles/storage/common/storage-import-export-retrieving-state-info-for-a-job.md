---
title: Azure importálási/exportálási feladatok állapot adatainak lekérése során |} Microsoft Docs
description: Ismerje meg az beszerzése a Microsoft Azure Import/Export szolgáltatás feladatok állapotadatait.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 22d7e5f0-94da-49b4-a1ac-dd4c14a423c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.openlocfilehash: 13169716c47cf9389c8f2651393ac744441bdd6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874009"
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Importálási/exportálási feladatok állapot adatainak lekérése során
Hívása a [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) műveletet is adatainak lekérésére importálni és exportálni a feladatokat. A visszaadott adatok tartalmazzák:

-   A feladat jelenlegi állapota.

-   Hozzávetőleges százalékos meg, hogy minden feladat befejeződött-e.

-   Minden olyan meghajtó aktuális állapotát.

-   URI-azonosítók a blobok tartalmazó hibanaplókat és részletes naplózási információk (Ha engedélyezve van).

Az alábbi szakaszok ismertetik által visszaadott adatok a `Get Job` műveletet.

## <a name="job-states"></a>Feladatállapotok
A táblázat és az állapot az alábbi ábrán az állapotok életciklusa során keresztül átkerül egy feladat leírása A feladat aktuális állapotának meghívásával lehet meghatározni a `Get Job` műveletet.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

A következő táblázat ismerteti az egyes állapot esetében egy feladat lehet, hogy továbbítása.

|Feladat állapota|Leírás|
|---------------|-----------------|
|`Creating`|A feladat Put művelet hívása, egy feladat jön létre, és az állapot értéke után `Creating`. Amíg a feladat a `Creating` állapotba kerül, az Import/Export szolgáltatás azt feltételezi, hogy rendelkezik a meghajtók nem lett kiadva az adatközpontban. Egy feladat maradhatnak a `Creating` két héttel, amely után a rendszer automatikusan törli a szolgáltatás állapotának.<br /><br /> Ha a feladat tulajdonságainak művelet meghívja a helyreállítás alatt a feladat a `Creating` állapotba kerül, a feladat marad a `Creating` állapotát, és a időkorlátja visszaáll két héten.|
|`Shipping`|Miután a csomag küldje el, célszerű hívni a feladatok tulajdonságainak frissítése művelet frissítés a feladat állapotának `Shipping`. A szállítási állapota beállítható, ha csak a `DeliveryPackage` (postai szolgáltatója és követési szám) és a `ReturnAddress` tulajdonságait úgy állították be a feladathoz.<br /><br /> A feladat két héttel a szállítási állapotban marad. Ha két héten teljesül, és a meghajtók nem fogadott, az Import/Export szolgáltatás operátorok értesítést fog kapni.|
|`Received`|Után az összes meghajtó az adatközpontban a fogadott, a feladat állapota a fogadott állapotba állítja.|
|`Transferring`|Miután a meghajtók az adatközpontban a fogadott, és legalább egy meghajtó már megkezdődött a feldolgozás, a feladat állapotát állítja be a `Transferring` állapotát. Tekintse meg a `Drive States` szakasz alatt részletes információkat.|
|`Packaging`|Miután az összes meghajtó feldolgozása befejeződött, a feladat kerülnek a `Packaging` mindaddig, amíg a meghajtók vissza az ügyfél szállított állapot.|
|`Completed`|Miután az összes meghajtó még szállított vissza az ügyfél a, ha a feladat hiba nélkül befejeződött, majd a feladatot úgy lesz beállítva, a `Completed` állapotát. A feladat automatikusan törli a 90 nap után a `Completed` állapotát.|
|`Closed`|Miután az összes meghajtó még szállított vissza az ügyfél a, ha történt hiba a feladat feldolgozása során, majd a feladatot úgy lesz beállítva, a `Closed` állapotát. A feladat automatikusan törli a 90 nap után a `Closed` állapotát.|

Megszakíthatja a feladatot csak egyes állapotok. A megszakított feladatok kihagyja az adatok másolása lépést, de egyébként ez azt jelenti, hogy az azonos Állapotváltások nem megszakították feladatként.

A következő táblázat ismerteti, hogy minden feladat állapotát, valamint a feladat hatással a hiba akkor fordul elő hibák.

|Feladat állapota|Esemény|Megoldási / további lépések|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|Egy vagy több meghajtó feladat érkezett, de nincs a feladat a `Shipping` állapota vagy nincs rekord. a feladat a szolgáltatásban.|Az Import/Export szolgáltatás műveleti csapata megkísérli az ügyfél lehet létrehozni vagy frissíteni a feladat szükséges információt továbbítható helyezhető át a feladat.<br /><br /> A műveleti csapata nem tud kapcsolatba lépni az ügyfél két héten belül, ha a műveleti csapata megkísérli a meghajtóit adja vissza.<br /><br /> Abban az esetben a meghajtók nem adható vissza, és az ügyfél nem érhető el, a meghajtók biztonságosan megsemmisülnek 90 napon belül.<br /><br /> Vegye figyelembe, hogy egy feladatot nem lehet feldolgozni, amíg az állapot frissül `Shipping`.|
|`Shipping`|A feladat a csomag nem érkezett meg több mint két hétig.|A műveleti csapata értesíti az ügyfél a hiányzó csomag. Az ügyfél válasz alapján, a műveleti csapata fog hosszabbítsa intervallum Várjon, amíg a csomag az ügyfélszámítógépekre érkeznek, vagy szakítsa meg a feladatot.<br /><br /> Abban az esetben az ügyfél nem érhető el, vagy nem válaszol számított 30 napon belül, a műveleti csapata fogja elindítani a műveletet úgy, hogy helyezze át a feladatot a `Shipping` állapot közvetlenül a `Closed` állapotát.|
|`Completed/Closed`|A meghajtók soha nem érhető el a címet, vagy a szállítási (csak exportálási feladat vonatkozik) sérült.|Ha a meghajtó nem eléri a címet, az ügyfél kell először hívható meg a Get Job művelet, vagy a portálhoz, és győződjön meg arról, hogy a meghajtók megtörtént-e a feladat állapotának. Ha a meghajtók rendelkeznek megtörtént, az ügyfél a szállítási szolgáltatót próbálja, és keresse meg a meghajtók lépjen kapcsolatba.<br /><br /> Ha a meghajtó sérült szállítás során, az ügyfél érdemes kérheti másik exportálási feladat, vagy töltse le a hiányzó blobokat.|
|`Transferring/Packaging`|Feladat rendelkezik egy hibás vagy hiányzik a címet.|A műveleti csapata a helyes cím beszerzése a feladat a kapcsolattartó személy lesznek elérhetők.<br /><br /> Abban az esetben, ha az ügyfél nem érhető el, a meghajtók biztonságosan megsemmisülnek 90 napon belül.|
|`Creating / Shipping/ Transferring`|A meghajtó, amelyek nem szerepelnek a listán, a meghajtók, importálandók a szállítási csomagban található.|A további meghajtók nem dolgozható fel, és visszatér az ügyfél a feladat végrehajtását.|

## <a name="drive-states"></a>A meghajtó állapota
A táblázat és az alábbi ábrán ismertetik a meghajtók egyéni életciklusának, akkor átkerül egy importálási vagy exportálási feladat keresztül. Az aktuális meghajtó állapotát meghívásával kérheti le a `Get Job` műveletet, és tanulmányozza a `State` eleme a `DriveList` tulajdonság.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

A következő táblázat ismerteti az egyes állapot esetében egy meghajtó továbbítása is.

|Meghajtó állapotát|Leírás|
|-----------------|-----------------|
|`Specified`|Az importálási feladat, amikor a feladat jön létre feladat Put művelet esetén a kezdeti egy meghajtó állapota a `Specified` állapotát. Exportálási feladat, a meghajtó nem adható meg a feladat jön létre, mert a kezdeti meghajtó van a `Received` állapotát.|
|`Received`|A meghajtó közeledik, hogy a `Received` állapot, amikor az Import/Export szolgáltatás operátor feldolgozta-e a meghajtókat, hogy az importálás a szállítási vállalati érkezett. Az exportálási feladat, a kezdeti meghajtó van a `Received` állapotát.|
|`NeverReceived`|A meghajtó helyezi át a `NeverReceived` feladat csomag megérkeznek, de a csomag nem tartalmaz a meghajtó állapotát. A meghajtó is áthelyezheti a állapotba, ha két héten lett, mert a szolgáltatás a szállítási adatokat kapott, de a csomag még nem érkezett meg az Adatközpont.|
|`Transferring`|A meghajtó helyezi át a `Transferring` állapot, ha a szolgáltatás megkezdi az adatok átvitele a meghajtó Windows Azure Storage.|
|`Completed`|A meghajtó helyezi át a `Completed` állapot, ha a szolgáltatás sikeresen átadta minden az adatok hibásak.|
|`CompletedMoreInfo`|A meghajtó helyezi át a `CompletedMoreInfo` állapot, ha a szolgáltatás észlelt problémákat adatok másolása az vagy a meghajtóra. Az információk között szerepelhet hibák, figyelmeztetések és információs üzenetek blobok felülírására.|
|`ShippedBack`|A meghajtó helyezi át a `ShippedBack` állapota akkor azt teljesítették data center hátsó visszatérési címre.|

A következő táblázat ismerteti a meghajtó hiba állapotok és az egyes állapotokhoz végrehajtott műveleteket.

|Meghajtó állapotát|Esemény|Megoldás / a következő lépés|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|A jelölés szerint meghajtót `NeverReceived` (mert nem érkezett a feladat szállítási részeként) egy másik szállítási érkezik.|A műveleti csapata elmozdul-e a meghajtót a `Received` állapotát.|
|`N/A`|A meghajtó, amely nem része semmilyen feladatot megérkezik az Adatközpont egy másik feladat részeként.|A meghajtó egy extra meghajtóként lesz megjelölve, és visszatér az ügyfél az eredeti csomagot kapcsolódó feladat végrehajtását.|

## <a name="faulted-states"></a>Hibás állapotok
Ha egy feladat vagy a meghajtó nem várt élettartama során általában előrehaladás, a feladat vagy a meghajtó kerül egy `Faulted` állapotát. Ezen a ponton a műveleti csapata kapcsolatba fog lépni az ügyfél e-mailek vagy a telefon. A probléma megoldása után, a hibás feladat vagy a meghajtó megnyílik kívüli a `Faulted` állapot és a megfelelő állapotba áthelyezett.

## <a name="next-steps"></a>Következő lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
