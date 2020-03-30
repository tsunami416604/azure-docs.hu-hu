---
title: Az Azure Stream Analytics bemeneteinek megismerése
description: Ez a cikk ismerteti a bemenetek egy Azure Stream Analytics-feladat, összehasonlítása streamelési bemenet hivatkozási adatok bemeneti.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 6b841d6b47e009c3b01d9925e11d352c00ed5c19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426434"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Az Azure Stream Analytics bemeneteinek megismerése

Az Azure Stream Analytics-feladatok egy vagy több adatbemenethez kapcsolódnak. Minden bemenet egy meglévő adatforráshoz való kapcsolatot határoz meg. A Stream Analytics többféle eseményforrásból , például az Event Hubsból, az IoT Hubból és a Blob storage-ból érkező adatokat fogadja el. A bemenetek név szerint hivatkoznak az egyes feladathoz írt streamelési SQL-lekérdezésben. A lekérdezésben több bemenetet is csatlakoztathat az adatok keveréséhez, vagy összehasonlíthatja a streamelési adatokat a referenciaadatok keresésével, és továbbíthatja az eredményeket a kimenetek számára. 

A Stream Analytics első osztályú integrációval rendelkezik háromféle erőforrással bemenetként:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob-tárhely](https://azure.microsoft.com/services/storage/blobs/) 

Ezek a bemeneti erőforrások ugyanabban az Azure-előfizetésben élhetnek, mint a Stream Analytics-feladat, vagy egy másik előfizetésből.

Az Azure [Portalon,](stream-analytics-quick-create-portal.md#configure-job-input)az [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput) [a .NET API,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions)a [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)és a Visual [Studio](stream-analytics-tools-for-visual-studio-install.md) segítségével hozhat létre, szerkeszthet és tesztelhet Stream Analytics-feladatbemeneteket.

## <a name="stream-and-reference-inputs"></a>Stream- és referenciabemenetek
Az adatok adatforrásba küldése, a Stream Analytics feladat által felvan használva, és valós időben dolgozzák fel. A bemenetek két típusra oszthatók: adatstream-bemenetekre és referenciaadat-bemenetekre.

### <a name="data-stream-input"></a>Adatfolyam-bevitel
Az adatfolyam az események időbeli korlátozás nélküli sorozata. A Stream Analytics-feladatoknak tartalmazniuk kell legalább egy adatstream-bemenetet. A támogatott adatstream-bemeneti források az Event Hubs, az IoT Hub és a Blob Storage. Az Event Hubs több eszközről és szolgáltatásból származó eseményfolyamok gyűjtésére szolgál. Ezek a adatfolyamok tartalmazhatnak közösségi média tevékenységnaplókat, tőzsdei információkat vagy érzékelőktől származó adatokat. Az IoT Hubs úgy van optimalizálva, hogy adatokat gyűjtsön a csatlakoztatott eszközökről az eszközök internetes hálózatában (IoT) forgatókönyvekben.  A Blob storage bemeneti forrásként használható a tömeges adatok adatfolyamként, például naplófájlokként történő betöltéséhez.  

Az adatbevitelstreamelésről további információt az [Adatfolyam-adatfolyam-adatfolyamként című témakörben talál.](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Referenciaadatok bevitele
A Stream Analytics támogatja a *referenciaadatokként*ismert bevitelt is. A referenciaadatok teljesen statikusak vagy lassan változnak. Általában korreláció és keresmel végrehajtására használják. Például előfordulhat, hogy az adatfolyam-bemenetben lévő adatokat a referenciaadatokban lévő adatokhoz illeszti össze, ugyanúgy, mint egy SQL-illesztést a statikus értékek felkezéséhez. Az Azure Blob storage és az Azure SQL Database jelenleg támogatott a referenciaadatok bemeneti forrásaként. A referencia-adatforrásblobok mérete legfeljebb 300 MB, a lekérdezés összetettségétől és a lefoglalt streamelési egységektől függően (további részletekért lásd a referenciaadatok dokumentációjának [Méretkorlátozás](stream-analytics-use-reference-data.md#size-limitation) szakaszát).

A referenciaadatok bemeneteiről a [Stream Analytics-ben a keresett témakörben talál további](stream-analytics-use-reference-data.md) információt.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
