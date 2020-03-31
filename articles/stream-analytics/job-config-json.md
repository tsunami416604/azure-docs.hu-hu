---
title: Azure Stream Analytics JobConfig.json mezők
description: Ez a cikk az Azure Stream Analytics JobConfig.json-fájl támogatott mezőit sorolja fel, amely a Visual Studio-kódban lévő feladatok létrehozásához szolgál.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617956"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig.json mezők

A következő mezőket támogatja a *JobConfig.json* fájl, amely [egy Azure Stream Analytics-feladat visual studio-kód használatával történő létrehozásához](quick-create-vs-code.md)használható.

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Név|Típus|Kötelező|Érték|
|----|----|--------|-----|
|DataLocale között|sztring|Nem|Az adatfolyam-elemzési feladat adatterületi beállítása. Az értéknek egy támogatott neve kell, hogy legyen. Alapértelmezés szerint "en-US", ha nincs megadva.|
|OutputErrorPolicy (Kimenetihiba-házirend)|sztring|Nem|Azt a házirendet jelzi, amelyet a kimenetre érkező eseményekre kell alkalmazni, és nem lehet írni a külső tárolóba a hibás anesztezinodott (hiányzó oszlopértékek, helytelen típusú vagy méretű oszlopértékek) miatt. - Megállás vagy ledobás|
|EventslateArrivalMaxDelayInseconds|egész szám|Nem|A maximális tolerálható késleltetés másodpercben, ahol a későn érkező események is szerepelhetnek. A támogatott tartomány -1 és 1814399 (20.23:59:59 nap) és -1 a várakozás határozatlan ideig történő megadására szolgál. Ha a tulajdonság hiányzik, a függvény -1 értéket jelent.|
|Eseményekrendelésen kívülimaxdelaymásodperc|egész szám|Nem|A maximális toleráló késleltetés másodpercek alatt, ahol a sorrenden kívüli események beállíthatók, hogy visszaálljanak a sorrendbe.|
|Eseményekrendelésen kívüli házirend|sztring|Nem|A bemeneti eseményfolyamban nem sorrendben érkező eseményekre alkalmazandó házirendet jelzi. - Állítsa be vagy csepp|
|Streamingegységek|egész szám|Igen|A streamelési feladat által használt streamelési egységek számát adja meg.|
|Kompatibilitási szint|sztring|Nem|A streamelési feladat bizonyos futásidejű viselkedéseit szabályozza. - Elfogadható értékek: "1.0", "1.1", "1.2"|
|UseSystemAssignedIdentity|logikai|Nem|Állítsa be a true beállítást, hogy ez a feladat kommunikáljon más Azure-szolgáltatásokkal, mint maga egy felügyelt Azure Active Directory-identitás használatával.|
|GlobalStorage.AccountName|sztring|Nem|A globális tárfiók a streamelemzési feladathoz kapcsolódó tartalmak, például az SQL referenciaadatok pillanatképei tárolására szolgál.|
|GlobalStorage.AccountKey|sztring|Nem|A globális tárfiók megfelelő kulcsa.|
|DataSourceCredentialtartomány|sztring|Nem|Fenntartott tulajdonság hitelesítő adatok helyi tárolójának.|
|ScriptType típusú|sztring|Igen|A forrásfájl típusának jelzésére fenntartott tulajdonság. Az elfogadható érték a JobConfig.json "JobConfig" értéke.|
|Címkék|JSON kulcs-érték párok|Nem|A címkék olyan név-/értékpárok, amelyek lehetővé teszik az erőforrások kategorizálását és az összevont számlázás megtekintését, ha ugyanazt a címkét több erőforrásra és erőforráscsoportra alkalmazza. A címkenevek ben nincsenek kis- és nagybetűk, a címkeértékek pedig a kis- és nagybetűket.|

## <a name="next-steps"></a>További lépések

* [Azure Stream Analytics-feladat létrehozása a Visual Studio-kódban](quick-create-vs-code.md)
* [A Test Stream Analytics helyi lekérdezéseket lekérdezése imént a Visual Studio-kód használatával](visual-studio-code-local-run.md)
* [A Test Stream Analytics helyi legkülönfélöző lekérdezéseket lekérdezi az élő közvetítés bemenetével szemben a Visual Studio-kód](visual-studio-code-local-run-live-input.md)
*[használatával Az Azure Stream Analytics-feladat telepítése CI/CD npm csomag használatával](setup-cicd-vs-code.md)