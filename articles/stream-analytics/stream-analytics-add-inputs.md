---
title: Az Azure Stream Analytics bemenetek ismertetése
description: Ez a cikk bemenetében benne egy Azure Stream Analytics-feladat, referenciaadat-bemenetek adatfolyam bemenete összehasonlításával fogalma ismertetik.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 926821e2ba9912ae0140f11c9fe9a2d504609a1e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186062"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Az Azure Stream Analytics bemenetek ismertetése

Az Azure Stream Analytics-feladatok egy vagy több adat bemenetek csatlakozni. Minden bemeneti definiálja kapcsolat egy meglévő adatforráson. A Stream Analytics származó esemény móddal, többek között az Event Hubs, az IoT Hub és a Blob storage különböző típusú adatok bejövő fogad el. A bemenetek hivatkoznak az adatfolyam-továbbítási SQL-lekérdezésben írást minden feladat nevét. A lekérdezés több bemeneti adatokat tartalmazó vagy hasonlítsa össze a streamelési adatok egy értékét a referenciaadatok csatlakoztatja, és a kimenetek csomópontok visszaküldik az eredményeket. 

A Stream Analytics bemeneteként erőforrások három típusú első osztályú integrációját rendelkezik:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

A bemeneti erőforrást is megmarad a azonos Azure-előfizetés a Stream Analytics-feladat, vagy egy másik előfizetésből.

Használhatja a [Azure-portálon](stream-analytics-quick-create-portal.md#configure-input-to-the-job), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input), és [Visual Studio](stream-analytics-tools-for-visual-studio.md)létrehozása, szerkesztése és a Stream Analytics-feladatok bemeneteit tesztelése.

## <a name="stream-and-reference-inputs"></a>Adatfolyam- és referenciaadatok bemenetek
Mivel az adatokat a rendszer előkészítésre továbbít egy adatforrást, a Stream Analytics-feladat által használt, és valós időben feldolgozott. Bemeneti adatok meg vannak osztva kétféle: adatok adatfolyam-bemenet, és az adatok bemenetek hivatkozik.

### <a name="data-stream-input"></a>Adatfolyam-bemenet
Adatfolyam események unbounded sorozatát adott idő alatt. Stream Analytics-feladatok tartalmaznia kell legalább egy adatfolyam-bemenetre. Az Event Hubs, IoT Hub és a Blob storage adatforrások adatfolyam bemeneti is támogatottak. Az Event Hubs segítségével több eszközökön és szolgáltatásokon szolgáltatásból összegyűjtsék az eseményfolyamokat. Ezekbe az adatfolyamokba közé tartozik a közösségi média tevékenység hírcsatornák, a készlet kereskedelmi információ vagy az érzékelők. IoT-központok adatokat gyűjteni az eszközök internetes hálózatát (IoT) forgatókönyvekben csatlakoztatott eszközök vannak optimalizálva.  A BLOB storage tömeges eseményközpontokból adatfolyamként, például a naplófájlok bemeneti forrása is használható.  

Adatfolyam-adatok bemenetek kapcsolatos további információkért lásd: [Stream Analytics bevitt adatok folyamatos átviteléhez](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Referenciaadat-bemenetek
A Stream Analytics is támogatja a bemeneti néven *referenciaadatok*. Referenciaadatok esik, vagy teljesen statikus vagy lassan módosításait. Általában korrelációs és végrehajtott keresési műveletek végrehajtására szolgál. Például előfordulhat, hogy csatlakozik a adatok az adatok a referenciaadatok az adatfolyam-bemenetre mint statikus értékek kereshető SQL illesztés kell elvégeznie. Az Azure Blob storage jelenleg az egyetlen támogatott bemeneti forrás a referenciaadatoknál. Hivatkozási adatforrás a BLOB-adatobjektumok korlátozódnak 100 MB-nál.

Hivatkozás adatok bemenetek kapcsolatos további információkért lásd: [Using referenciaadatok a Stream Analytics keresések](stream-analytics-use-reference-data.md)

Ez a cikk Ez a lépés a [Stream Analytics képzési terv](/documentation/learning-paths/stream-analytics/).

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Gyors üzembe helyezés: A Stream Analytics-feladat létrehozása az Azure portál használatával](stream-analytics-quick-create-portal.md)