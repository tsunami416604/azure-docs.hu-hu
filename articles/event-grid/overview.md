---
title: "Az Azure Event rács áttekintése"
description: "Azure Event rács és a fogalmakat ismerteti."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: babanisa
ms.openlocfilehash: 5e700e3e9d17e790083facf00c7f4b8decf9037a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="an-introduction-to-azure-event-grid"></a>Azure Event rács bemutatása

Az Azure Event rács lehetővé teszi az architektúrák esemény-alapú alkalmazások. Az Azure-erőforrás szeretne előfizetni, és adja meg a eseménykezelő vagy az esemény küldése a WebHook végpont választja. Esemény rács rendelkezik beépített támogatása az Azure-szolgáltatások, például a tárolási BLOB és -erőforráscsoportok származó események. Esemény rács is rendelkeznek, alkalmazás és a külső események egyéni támogatása egyéni témakörök és egyéni webhookokkal használatával. 

Szűrők segítségével meghatározott események különböző végpontokhoz, több végpontot, csoportos küldéssel történő továbbításához, és győződjön meg arról, hogy az események megbízhatóan érkeznek. Esemény rács is rendelkezik beépített egyéni és külső események támogatása.

Esemény rács jelenleg a következő régióban:

* Ázsia óceáni térség délkeleti régiója
* Ázsiai keleti régiója
* USA középső régiója
*   USA keleti régiója
*   USA 2. keleti régiója
* Nyugat-Európa
* Észak-Európa
*   USA nyugati középső régiója
*   USA nyugati régiója
*   USA nyugati régiója, 2.

Ez a cikk áttekintést Azure esemény rács. Ha azt szeretné, esemény rács használatába, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md). A következő kép bemutatja, hogyan csatlakozzon az esemény rács a közzétevők és kezelők, de nem biztosít a támogatott beállítások átfogó listáját.

![Esemény rács működési modell](./media/overview/functional-model.png)

## <a name="event-publishers"></a>Esemény-közzétevők

Jelenleg az Azure-szolgáltatásokat kell beépített publisher az esemény rács:

* Azure-előfizetések (műveletek)
* Egyéni kapcsolatos témakörök
* Event Hubs
* IoT Hub
* Erőforráscsoportok (műveletek)
* Storage Blob
* Tárolási célú v2 (GPv2)

## <a name="event-handlers"></a>Az eseménykezelők

Jelenleg az Azure-szolgáltatásokat kell beépített kezelő az esemény rács: 

* Azure Automation
* Azure Functions
* Event Hubs
* Logic Apps
* Microsoft Flow
* Webhookok

Az Azure Functions kezelőként használjon az esemény rács eseményindító általános HTTP-eseményindítók helyett. Esemény rács automatikusan ellenőrzi az esemény rácsos függvényt eseményindítók. Az általános HTTP-eseményindítók, meg kell valósítani a [érvényesítési válasz](security-authentication.md#webhook-event-delivery).

## <a name="concepts"></a>Alapelvek

Nincsenek Azure esemény rács, amelyek lehetővé teszik az induláshoz öt fogalmak:

* **Események** – Mi történt.
* **Esemény források-közzétevők** – Ha az esemény történt.
* **Témakörök** -a végpont, ahol közzétevők küldi az eseményeket.
* **Esemény-előfizetések** -útvonal események, egyes esetekben több kezelők végpont vagy beépített mechanizmusa. Előfizetések is használják kezelők ezután a bejövő események szűréséhez.
* **Az eseménykezelők** – az alkalmazás vagy szolgáltatás reagálnak az esemény.

Ezek a fogalmak kapcsolatos további információkért lásd: [Azure esemény rácsban fogalmak](concepts.md).

## <a name="capabilities"></a>Funkciók

Az alábbiakban néhány fő funkciója Azure esemény rács:

* **Egyszerűség** -ponton, majd kattintson az e eseménykezelő vagy a végpont az Azure-erőforrás származó események célja.
* **Speciális szűrési** -szűrőt a esemény típusa vagy esemény közzététele elérési út annak biztosítása érdekében az eseménykezelők csak megkapják a kapcsolódó eseményeket.
* **Szétterítési** -előfizetés az összes olyan helyre, szükség esetén az esemény példánya küldendő ugyanarra az eseményre több végpontot.
* **Megbízhatóság** -24 órás újra exponenciális leállítási biztosításához kézbesíti az eseményeket egy használják.
* **Fizetési / esemény** - kell fizetnie, amennyit csak az összeg esemény rács használja.
* **Magas teljesítmény** -esemény rács nagy munkaterhelések létrehozásához, támogatja a több millió esemény / másodperc.
* **Beépített események** - létrehozásához, és gyorsan futtató beépített események erőforrás által definiált.
* **Egyéni események** -esemény rács útvonal, szűrő és megbízhatóan kézbesítése egyéni események használja az alkalmazásban.

Egy esemény rács, az Event Hubs és a Service Bus, lásd: [válasszon az Azure-szolgáltatásokat, hogy a üzenetek](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Mi a teendő esemény rácshoz?

Azure Event rács több képességeket kínál, amelyek jelentősen javítja a kiszolgáló nélküli, ops automatizálási és integrációs munkahelyi biztosít: 

### <a name="serverless-application-architectures"></a>Kiszolgáló nélküli alkalmazás-architektúrák

![Kiszolgáló nélküli alkalmazást](./media/overview/serverless_web_app.png)

Az Event Grid összeköti az adatforrásokat az eseménykezelőkkel. Az Event Grid használatával például azonnal aktiválható egy képelemzést futtató, kiszolgáló nélküli funkció, valahányszor új kép kerül egy Blob Storage-tárolóba. 

### <a name="ops-automation"></a>OPS automatizálás

![Műveletek automatizálása](./media/overview/Ops_automation.png)

Az Event Grid lehetővé teszi az automatizálás felgyorsítását és a szabályzatok érvényesítésének egyszerűsítését. Az Event Grid értesítheti például az Azure Automation szolgáltatást, amikor egy virtuális gép létrejön vagy egy SQL Database működésbe lép. Ezek az események felhasználhatók a szolgáltatáskonfigurációk megfelelőségének automatikus ellenőrzésére, metaadatok műveleti eszközöknek való átadására, és virtuális gépek vagy munkatétel-fájlok címkézésére.

### <a name="application-integration"></a>Alkalmazásintegrálás

![Alkalmazásintegrálás](./media/overview/app_integration.png)

Az Event Grids más szolgáltatásokkal kapcsolja össze alkalmazását. Például hozzon létre egy egyéni témakör az alkalmazás esemény adatokat küldeni a rács esemény, és kihasználhatják a megbízható szállítási, speciális útválasztási, és közvetlen integráció az Azure-ral. Úgy is dönthet, hogy az Event Gridet a Logic Apps-szel együtt használ adatfeldolgozásra tetszőleges helyen, kód írása nélkül. 

## <a name="how-much-does-event-grid-cost"></a>Milyen mértékű nem költség esemény rács?

Azure esemény rács fizetési / esemény árképzési modellt használ, így csak a valóban használt funkciókért fizet. Havonta az első 100 000 műveletek szabadon. Esemény érkező egyeznek, a kézbesítési kísérletek és a felügyeleti hívások speciális műveleteket is meg van adva. További információkért lásd: a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>További lépések

* [Útvonal tárolási Blob események](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  A blob storage-események válaszolni esemény rács használatával.
* [Hozzon létre, és feliratkozhat a egyéni események](custom-event-quickstart.md)  
  A jobb és a saját egyéni események küldése az Azure Event rács gyors üzembe helyezés bármely végpont start ugorhat.
* [A Logic Apps használatával eseménykezelő](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Logic Apps segítségével esemény rács által leküldött események reagálni az alkalmazás elkészítése az oktatóanyag.
* [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)  
  Ez az oktatóanyag az Azure Functions segítségével adatfolyam adatokat az Event Hubs az SQL Data Warehouse.
* [Esemény rács REST API-referencia](/rest/api/eventgrid)  
  Esemény-előfizetések kezelése az Azure Event rács, valamint egy hivatkozást kapcsolatos további műszaki információkat biztosít az Útválasztás és a szűrés.