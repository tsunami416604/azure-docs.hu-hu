---
title: Azure Stream Analytics JobConfig. JSON mezők
description: Ez a cikk a Visual Studio Code-ban a feladatok létrehozásához használt Azure Stream Analytics JobConfig. JSON fájl támogatott mezőit sorolja fel.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617956"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig. JSON mezők

A következő mezők a [Visual Studio Code használatával Azure stream Analytics feladatok létrehozásához](quick-create-vs-code.md)használt *JobConfig. JSON* fájlban támogatottak.

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

|Name (Név)|Típus|Kötelező|Érték|
|----|----|--------|-----|
|DataLocale|sztring|Nem|A stream Analytics-feladatok adatterülete. Az értéknek egy támogatott névnek kell lennie. Ha nincs megadva, az alapértelmezett érték az "en-US".|
|OutputErrorPolicy|sztring|Nem|Azt a házirendet jelzi, amelyet a rendszer a kimenetre érkező eseményekre alkalmaz, és nem írható a külső tárolóba, mert helytelen formátumú (hiányzó oszlop-értékek, helytelen típusú oszlopok vagy méretek). – Leállítás vagy eldobás|
|EventsLateArrivalMaxDelayInSeconds|egész szám|Nem|Az a maximálisan megengedett késleltetés (másodpercben), ahol a késésben lévő események bekerülhetnek. A támogatott tartomány – 1 – 1814399 (20.23:59:59 nap) és-1 a várakozás határozatlan idejű megadására szolgál. Ha a tulajdonság hiányzik, a rendszer úgy értelmezi, hogy a-1 értékkel rendelkezik.|
|EventsOutOfOrderMaxDelayInSeconds|egész szám|Nem|Az a maximálisan megengedett késleltetés másodpercben, amelyben a megrendelési események nem állíthatók vissza rendelésre.|
|EventsOutOfOrderPolicy|sztring|Nem|Azt a házirendet jelzi, amely a bemeneti esemény adatfolyamában a sorrendtől megérkező eseményekre vonatkozik. – Módosítás vagy eldobás|
|StreamingUnits|egész szám|Igen|Meghatározza a folyamatos átviteli egység által használt folyamatos átviteli egységek számát.|
|CompatibilityLevel|sztring|Nem|A folyamatos átviteli feladatok bizonyos futásidejű viselkedéseit vezérli. -Elfogadható értékek: "1,0", "1,1", "1,2"|
|UseSystemAssignedIdentity|logikai|Nem|Az igaz érték megadásával engedélyezheti, hogy ez a feladattípus felügyelt Azure Active Directory identitás használatával kommunikáljon más Azure-szolgáltatásokkal.|
|GlobalStorage. AccountName|sztring|Nem|A globális Storage-fiók a stream Analytics-feladathoz kapcsolódó tartalmak tárolására használatos, például az SQL-hivatkozási adatok pillanatképeit.|
|GlobalStorage. AccountKey|sztring|Nem|A globális Storage-fiókhoz tartozó kulcs.|
|DataSourceCredentialDomain|sztring|Nem|A hitelesítő adatok helyi tárolójának fenntartott tulajdonsága.|
|ScriptType|sztring|Igen|Fenntartott tulajdonság a forrásfájl típusának jelzéséhez. Elfogadható érték: "JobConfig" a JobConfig. JSON fájlhoz.|
|Címkék|JSON-kulcs-érték párok|Nem|A címkék olyan név/érték párok, amelyek lehetővé teszik az erőforrások kategorizálását és az összevont számlázás megtekintését, ha ugyanazt a címkét több erőforrásra és erőforráscsoporthoz alkalmazza. A címkék nevei megkülönböztetik a kis-és nagybetűket, és a címke értéke kis-és nagybetűk megkülönböztetése.|

## <a name="next-steps"></a>Következő lépések

* [Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-vs-code.md)
* [A Visual Studio Code segítségével helyileg tesztelheti Stream Analytics lekérdezéseket a mintaadatok használatával](visual-studio-code-local-run.md)
* A [Visual Studio Code használatával helyileg tesztelheti stream Analytics lekérdezéseket az élő adatfolyam-bevitelsel](visual-studio-code-local-run-live-input.md)
*[üzembe helyezhet egy Azure stream Analytics feladatot a CI/CD NPM csomag használatával](setup-cicd-vs-code.md)