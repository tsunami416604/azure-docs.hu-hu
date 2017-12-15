---
title: "Az Azure Event rács áttekintése"
description: "Azure Event rács és a fogalmakat ismerteti."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 12/14/2017
ms.author: babanisa
ms.openlocfilehash: e6665b3b0c6e92ed462f18dbd41d62ccd9304928
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="an-introduction-to-azure-event-grid"></a>Azure Event rács bemutatása

Az Azure Event rács lehetővé teszi az architektúrák esemény-alapú alkalmazások. Az Azure-erőforrás szeretne előfizetni, és adja meg a eseménykezelő vagy az esemény küldése a WebHook végpont választja. Esemény rács rendelkezik beépített támogatása az Azure-szolgáltatások, például a tárolási BLOB és -erőforráscsoportok származó események. Esemény rács is rendelkeznek, alkalmazás és a külső események egyéni támogatása egyéni témakörök és egyéni webhookokkal használatával. 

Szűrők segítségével meghatározott események különböző végpontokhoz, több végpontot, csoportos küldéssel történő továbbításához, és győződjön meg arról, hogy az események megbízhatóan érkeznek. Esemény rács is rendelkezik beépített egyéni és külső események támogatása.

Esemény rács jelenleg a következő régióban:

* USA középső régiója
*   USA keleti régiója
*   USA 2. keleti régiója
*   USA nyugati középső régiója
*   USA nyugati régiója
*   USA nyugati régiója, 2.

Más régiókban lesz hozzáadva.

Ez a cikk áttekintést Azure esemény rács. Ha azt szeretné, esemény rács használatába, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md). A következő kép bemutatja, hogyan csatlakozzon az esemény rács a közzétevők és kezelők, de nem biztosít a támogatott beállítások átfogó listáját.

![Esemény rács működési modell](./media/overview/event-grid-functional-model.png)

## <a name="event-publishers"></a>Esemény-közzétevők

Jelenleg az Azure-szolgáltatásokat kell beépített publisher az esemény rács:

* Azure-előfizetések (műveletek)
* Egyéni kapcsolatos témakörök
* Event Hubs
* Erőforráscsoportok (műveletek)
* Storage-Blobba

Más Azure-szolgáltatásokkal fog bővülni az év.

## <a name="event-handlers"></a>Az eseménykezelők

Jelenleg az Azure-szolgáltatásokat kell beépített kezelő az esemény rács: 

* Azure Automation
* Azure Functions
* Event Hubs
* Logic Apps
* Microsoft folyamata
* Webhookok

Más Azure-szolgáltatásokkal fog bővülni az év.

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

Azure esemény rács fizetési / esemény árképzési modellt használ, így csak a valóban használt funkciókért fizet.

Esemény rács költségek 0,60 $ millió műveleteket ($0,30 előzetes), és szabadon havonta az első 100 000 műveletet. Esemény érkező egyeznek, a kézbesítési kísérletek és a felügyeleti hívások speciális műveleteket is meg van adva.  További részletek találhatók a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Következő lépések

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