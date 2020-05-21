---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 55236e3083ded0e8e07afd406e87f61f53451e9d
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701710"
---
Most már használhatja a Azure Portal Adatkezelő eszközét egy Azure Cosmos DB API MongoDB-adatbázis és-tároló létrehozásához. 

1. Válassza ki **adatkezelő**  >  **új tárolót**. 
    
    A jobb szélen megjelenik a **tároló hozzáadása** felület, ezért a jobb oldali görgetéshez jobbra kell görgetni a megjelenítéshez.

    ![Az Azure Portal Adatkezelője a Tároló hozzáadása panellel](./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png)

2. A **tároló hozzáadása** lapon adja meg az új tároló beállításait.

    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis-azonosító**|db|Adja *meg az adatbázist az új* adatbázis neveként. Az adatbázis nevének 1 és 255 karakter közöttinek kell lennie, és nem tartalmazhat `/, \\, #, ?` szóközt. Tekintse meg az **adatbázis átviteli sebességének** kiosztása lehetőséget, amellyel megoszthatja az adatbázison belül kiosztott átviteli sebességet az adatbázis összes tárolóján. Ez a lehetőség a költségmegtakarítást is segíti. |
    |**Teljesítmény**|400|Az átviteli sebesség 400 adategység/másodperc (RU/s) esetén. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében. Kiválaszthatja az [Autopilot üzemmódot](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autopilot)is, amely olyan ru/s-ket biztosít, amelyek szükség szerint dinamikusan növekednek és csökkennek.| 
    |**Gyűjtemény azonosítója**|Coll|Adja meg a *Coll* nevet az új tároló neveként. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.|
    |**Tárolókapacitás**|Rögzített (10 GB)|Adja meg a *rögzített (10gb)* értéket ehhez az alkalmazáshoz. Ha a *korlátlan*lehetőséget választja, létre kell hoznia egy `Shard Key` elemet, amelyhez a beszúrt elemek szükségesek.|
    |**Szilánk kulcsa**| /_id| A cikkben ismertetett minta nem használ szegmens kulcsot, ezért a *_id* az automatikusan generált azonosító mezőt fogja használni a szegmens kulcsaként. További információ a particionálásról, más néven particionálásról [Azure Cosmos db](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)|
        
    Válassza az **OK** lehetőséget. Az Adatkezelő megjeleníti az új adatbázist és tárolót.