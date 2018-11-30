---
title: Az Azure Stream Analytics bemenetek ismertetése
description: Ez a cikk ismerteti a bemeneti adatokat az Azure Stream Analytics-feladat, referenciaadat-bemenetek bemeneti adatfolyam összehasonlításával fogalmát.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: b3b7b7374b02210f44fde408f28fd0378bf9bd12
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310854"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Az Azure Stream Analytics bemenetek ismertetése

Az Azure Stream Analytics-feladatok egy vagy több streamadat típusú bemenetek csatlakozni. Minden egyes bemenet egy kapcsolat egyik létező adatforrásához határozza meg. Stream Analytics eseményforrások, beleértve az Event Hubs, az IoT Hub és a Blob storage számos típusú adatok bejövő fogad el. A bemeneti adatok neve, amely az egyes feladatokhoz ír streamelési SQL-lekérdezés által hivatkozott. A lekérdezés kombinálhatja az adatokat, és összevetheti a referenciaadatok keresés streamelési adatok több bemenet csatlakozzon, és adja át az eredményeket a kimenetek. 

Stream Analytics bemenetként a három típusú erőforrásokhoz való kiváló integráció rendelkezik:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

Ezeket az erőforrásokat a bemeneti is élő Azure-előfizetéshez, a Stream Analytics-feladatot, vagy egy másik előfizetésben található.

Használhatja a [az Azure portal](stream-analytics-quick-create-portal.md#configure-job-input), [Azure PowerShell-lel](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [REST API-val](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input), és [Visual Studio](stream-analytics-tools-for-visual-studio-install.md)létrehozása, szerkesztése és tesztelése a Stream Analytics-feladat bemenetek.

## <a name="stream-and-reference-inputs"></a>Stream- és referenciainformációkat bemenetek
Az adatok leküldésekor egy adatforrásba, a Stream Analytics-feladat által használt, és valós idejű feldolgozásra. Bemenetei között meg vannak osztva a két típus: adatok streamelése bemenetei között, és streamadat típusú bemenetek hivatkoznak.

### <a name="data-stream-input"></a>Adatfolyam-bemenet
Egy adatfolyama korlátlan streameken működő eseménysorozatát idővel. Stream Analytics-feladatok tartalmaznia kell legalább egy adatfolyam-bemenetre. Az Event Hubs, az IoT Hub és a Blob storage adatforrások stream bemeneti támogatottak. Az Event Hubs segítségével több eszközökön és szolgáltatásokon szolgáltatásból. Ezekbe az adatfolyamokba tartalmazhatnak, közösségi hírcsatornákról tevékenység, tőzsdei kereskedelmi adatokat vagy érzékelők adatait. IoT-központok adatokat gyűjteni az eszközök internetes hálózata (IoT) csatlakozó eszközök vannak optimalizálva.  Adatok kötegelt feldolgozására adatfolyamként, például a naplófájlok a BLOB storage használható egy bemeneti forrásaként.  

Streamadat típusú bemenetek kapcsolatos további információkért lásd: [data Stream bemeneti Stream analyticsbe](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Referenciaadat-bemenetek
Stream Analytics is támogatja a bemeneti néven *referenciaadatok*. Referenciaadatok, vagy teljesen statikus vagy lassan módosításokat. Általában összefüggések keresésére és keresések végrehajtására szolgál. Például előfordulhat, hogy csatlakozik a szolgáltatáshoz data az adatok a referenciaadatok az adatfolyam-bemenetre akár egy SQL összekapcsolás statikus értékek keresse ki kell elvégeznie. Az Azure Blob storage szolgáltatás jelenleg az egyetlen támogatott bemeneti forrás a referenciaadatoknál. A referenciaadat blobok forrás mérete, a lekérdezés összetettségétől függően akár 300 MB legfeljebb és a folyamatos átviteli egységek.

Referenciaadat típusú bemenetek kapcsolatos további információkért lásd: [a referenciaadatok a Stream Analytics keresések](stream-analytics-use-reference-data.md)

Ez a cikk Ez a lépés a [Stream Analytics képzési](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/).

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Gyors útmutató: Stream Analytics-feladat létrehozása az Azure portal használatával](stream-analytics-quick-create-portal.md)
