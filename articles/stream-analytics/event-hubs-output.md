---
title: Azure Stream Analytics Event Hubs kimenete
description: Ez a cikk azt ismerteti, hogyan lehet adatokat kiadni Azure Stream Analyticsról az Azure Event Hubsba.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 50d2d974815e0921d99154bce67f604b7314970d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892031"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>Azure Stream Analytics Event Hubs kimenete

Az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatás egy nagymértékben méretezhető közzétételi és előfizetési esemény betöltése. Másodpercenként akár több millió eseményt is gyűjthet. Az Event hub egyik használata kimenetként, amikor egy Stream Analytics-feladatok kimenete lesz egy másik folyamatos átviteli feladathoz tartozó bemenet. További információ az üzenetek maximális méretéről és a Batch méretének optimalizálásáról: [kimeneti köteg mérete](#output-batch-size) szakasz.

## <a name="output-configuration"></a>Kimeneti konfiguráció

A következő táblázat az Event hubokból kimenetként konfigurált adatstreamek konfigurálásához szükséges paramétereket tartalmazza.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | Egy rövid név, amelyet a lekérdezések a lekérdezés kimenetének az Event hub-ba történő irányításához használnak. |
| Eseményközpont-névtér | Az üzenetküldési entitások készletének tárolója. Új Event hub létrehozásakor létrehozott egy Event hub-névteret is. |
| Event Hubs neve | Az Event hub kimenetének neve. |
| Event hub-házirend neve | A megosztott elérési házirend, amelyet az Event hub **configure (Konfigurálás** ) lapján lehet létrehozni. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. |
| Event hub-házirend kulcsa | Az Event hub-névtérhez való hozzáférés hitelesítéséhez használt megosztott elérési kulcs. |
| Partíciós kulcs oszlopa | Választható. Az Event hub kimenetének partíciós kulcsát tartalmazó oszlop. |
| Eseményszerializációs formátum | A kimeneti adatmennyiség szerializálási formátuma. A JSON, a CSV és a Avro támogatottak. |
| Encoding | A CSV és a JSON esetében jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó | Csak a CSV-szerializáláshoz alkalmazható. Stream Analytics számos gyakori elhatárolót támogat a CSV-formátumú adatszerializáláshoz. A támogatott értékek a következők: vessző, pontosvessző, szóköz, TAB és függőleges sáv. |
| Formátum | Csak a JSON-szerializáláshoz alkalmazható. A **sor elválasztva** érték azt jelenti, hogy a kimenet formázása úgy történik, hogy minden JSON-objektum új sorral van elválasztva. Ha a **sor elválasztva**lehetőséget választja, a JSON egyszerre csak egy objektumot olvas be. A teljes tartalom önmagában nem érvényes JSON. A **Array** beállítás azt adja meg, hogy a kimenet a JSON-objektumok tömbje legyen formázva.  |
| Tulajdonságok oszlopai | Választható. Vesszővel tagolt oszlopokat kell csatolni a kimenő üzenet felhasználói tulajdonságaihoz a hasznos adatok helyett. A szolgáltatással kapcsolatos további információkért lásd: [Egyéni metaadatok tulajdonságai a kimenethez](#custom-metadata-properties-for-output). |

## <a name="partitioning"></a>Particionálás

A particionálás a partíciók igazítása függvényében változhat. Ha az Event hub kimenetéhez tartozó partíciós kulcs egyenlően van igazítva a felsőbb rétegbeli (előző) lekérdezési lépéssel, az írók száma megegyezik az Event hub kimenetében található partíciók számával. Az egyes írók a [EventHubSender osztály](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet&preserve-view=true) használatával küldik el az eseményeket az adott partícióra. Ha az Event hub kimenetének partíciós kulcsa nincs igazítva a felsőbb rétegbeli (előző) lekérdezési lépéssel, az írók száma megegyezik az előző lépésben lévő partíciók számával. Mindegyik író a [SendBatchAsync osztályt](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet&preserve-view=true) használja a **EventHubClient** -ben, hogy eseményeket küldjön az összes kimeneti partícióra. 

## <a name="output-batch-size"></a>Kimeneti köteg mérete

Az üzenetek maximális mérete 256 KB vagy 1 MB/üzenet. További információ: [Event Hubs korlátok](../event-hubs/event-hubs-quotas.md). Ha a bemeneti/kimeneti particionálás nincs igazítva, az egyes események külön vannak csomagolva, `EventData` és egy kötegben lesznek elküldve az üzenetek maximális méretére. Ez akkor is előfordul, ha [Egyéni metaadatokat](#custom-metadata-properties-for-output) használunk. Ha a bemeneti/kimeneti particionálás be van igazítva, több esemény is egyetlen példányba van csomagolva `EventData` , az üzenetek maximális méretével és a küldéssel.

## <a name="custom-metadata-properties-for-output"></a>A kimenet egyéni metaadat-tulajdonságai

A lekérdezési oszlopokat felhasználói tulajdonságokként csatolhatja a kimenő üzenetekhez. Ezek az oszlopok nem kerülnek bele a hasznos adatokba. A tulajdonságok a kimeneti üzenet szótárának formájában jelennek meg. A *kulcs* az oszlopnév és az *érték* az oszlop értéke a tulajdonságok szótárban. A rekord és a tömb kivételével minden Stream Analytics adattípus támogatott.  

## <a name="next-steps"></a>Következő lépések

* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása az Azure CLI használatával](quick-create-azure-cli.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása ARM-sablon használatával](quick-create-azure-resource-manager.md)
* [Gyors útmutató: Stream Analytics-feladatok létrehozása Azure PowerShell használatával](stream-analytics-quick-create-powershell.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-visual-studio-code.md)
