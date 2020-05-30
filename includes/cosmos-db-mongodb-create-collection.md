---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 0f471c5aefdb39396ea189984d32a9e8f5419182
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84200498"
---
Most már használhatja a Azure Portal Adatkezelő eszközét egy Azure Cosmos DB API MongoDB-adatbázis és-tároló létrehozásához. 

1. Válassza ki **adatkezelő**  >  **új tárolót**. 
    
    A jobb szélen megjelenik a **tároló hozzáadása** felület, ezért a jobb oldali görgetéshez jobbra kell görgetni a megjelenítéshez.

    ![Az Azure Portal Adatkezelője a Tároló hozzáadása panellel](./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png)

2. A **tároló hozzáadása** lapon adja meg az új tároló beállításait.

    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis-azonosító**|db|Adja *meg az adatbázist az új* adatbázis neveként. Az adatbázis nevének 1 és 255 karakter közöttinek kell lennie, és nem tartalmazhat `/, \\, #, ?` szóközt. Tekintse meg az **adatbázis átviteli sebességének** kiosztása lehetőséget, amellyel megoszthatja az adatbázison belül kiosztott átviteli sebességet az adatbázis összes tárolóján. Ez a lehetőség a költségmegtakarítást is segíti. |
    |**Teljesítmény**|400|Az átviteli sebesség 400 adategység/másodperc (RU/s) esetén. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében. Kiválaszthatja az [autoskálázási módot](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale)is, amely az olyan ru/s-ket is megadja, amelyek szükség szerint dinamikusan növekednek és csökkennek.| 
    |**Gyűjtemény azonosítója**|Coll|Adja meg `coll` az új tároló nevét. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.|
    |**Tárolókapacitás**|Rögzített (10 GB)|Adja meg a *rögzített (10gb)* értéket ehhez az alkalmazáshoz. Ha a *korlátlan*lehetőséget választja, létre kell hoznia egy `Shard Key` elemet, amelyhez a beszúrt elemek szükségesek.|
    |**Szilánk kulcsa**| /_id| A cikkben ismertetett minta nem használ szegmens kulcsot, ezért a *_id* az automatikusan generált azonosító mezőt fogja használni a szegmens kulcsaként. További információ a particionálásról, más néven particionálásról [Azure Cosmos db](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)|
        
    Kattintson az **OK** gombra. Az Adatkezelő megjeleníti az új adatbázist és tárolót.
