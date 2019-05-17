---
title: Erőforráskorlátok NetApp Azure-fájlok |} A Microsoft Docs
description: A NetApp fájlokat az Azure-erőforrások, többek között a NetApp fiókok, kapacitás készletek, kötetek, pillanatképeket és a delegált alhálózatra vonatkozó korlátok korlátozásait ismerteti.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: d55e11ace4ca306c3d3ec8c0094a751966289db6
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523050"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Az Azure NetApp Files erőforráskorlátai

Erőforráskorlátok ismertetése a NetApp Azure-fájlok segítségével kezelheti a köteteket.

## <a name="resource-limits"></a>Erőforráskorlátok

A következő táblázat ismerteti a erőforráskorlátok NetApp Azure-fájlok:

|  Resource  |  Alapértelmezett korlát  |  Támogatási kérelem útján állítható  |
|----------------|---------------------|--------------------------------------|
|  Azure-előfizetésenként NetApp fiókok száma   |  10    |  Igen   |
|  NetApp fiókonként kapacitás készletek száma   |    25     |   Igen   |
|  Kapacitás készletenként kötetek száma     |    500   |    Igen     |
|  Kötetenként pillanatképek számát       |    255     |    Nem        |
|  Delegálva az Azure NetApp fájlok (Microsoft.NetApp/volumes) az Azure virtuális hálózat / alhálózatok száma    |   1.   |    Nem    |
|  Minimális méretének egyetlen kapacitás   |  4 TiB     |    Nem  |
|  Egy egyetlen kapacitás a készlet maximális mérete    |  500 TiB   |   Nem   |
|  Egyetlen kötet minimális mérete    |    100 GiB    |    Nem    |
|  Maximális hozzárendelt kvóta egyetlen köteten *   |   92 TiB   |    Nem   |
|  Egyetlen köteten * maximális mérete     |    100 TiB    |    Nem       |

* A kötet manuálisan létrehozott vagy átméretezett standardként 92 Tib-ra. Azonban a kötet legfeljebb 100 TiB-keretet túllépő forgatókönyvben növelhető. Lásd: [Azure NetApp fájlok költségmodell](azure-netapp-files-cost-model.md) kapacitás kerettúllépés részleteiért. 

## <a name="request-limit-increase"></a>Kérje a korlát növelését. 

Létrehozhat egy Azure-támogatási kérést a fenti táblázatból állítható határértékek növelését. 

Az Azure portal navigációs felületben rejlő előnyöket: 

1. Kattintson a **súgó + támogatás**.
2. Kattintson a **+ új támogatási kérelem**.
3. Az alapismeretek lapon adja meg a következő információkat: 
    1. Probléma típusa: Válassza ki **szolgáltatás és az előfizetések korlátai (kvóták)**.
    2. Előfizetések: Válassza ki az előfizetést, az erőforrás kvótáját szükséges.
    3. Kvóta típusa: Válassza ki **tároló: Korlátozza a NetApp Azure Files**.
    4. Kattintson a **tovább: Megoldások**.
4. A részletek lapon:
    1. A Leírás mezőben adja meg a megfelelő erőforrás-típus a következő információkat:

        |  Resource  |    Szülő erőforrások      |    A kért új korlátok     |    Kvótanövelést oka       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Fiók |  *előfizetés-azonosító*   |  *Kért új maximális **fiók** száma*    |  *Milyen forgatókönyvet vagy használati eset kéri a kérelmet?*  |
        |  Készlet    |  *Előfizetés-azonosító, a fiók URI*  |  *Kért új maximális **készlet** száma*   |  *Milyen forgatókönyvet vagy használati eset kéri a kérelmet?*  |
        |  Kötet  |  *Előfizetés-azonosító, a fiók URI-t, készlet URI*   |  *Kért új maximális **kötet** száma*     |  *Milyen forgatókönyvet vagy használati eset kéri a kérelmet?*  |

    2. Adja meg a megfelelő módszert támogatja, és adja meg a szerződés adatait.

    3. Kattintson a **tovább: Felülvizsgálat + létrehozás** a kérelem létrehozásához. 


## <a name="next-steps"></a>További lépések  

- [Megismerheti a NetApp Azure-fájlok hierarchiája](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp fájlok költségű modell](azure-netapp-files-cost-model.md)
