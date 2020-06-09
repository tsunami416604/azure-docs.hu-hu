---
title: A Azure NetApp Files erőforrás-korlátai | Microsoft Docs
description: Leírja a Azure NetApp Files erőforrásainak korlátait, valamint az erőforrás-korlát növelését.
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
ms.date: 06/08/2020
ms.author: b-juche
ms.openlocfilehash: 8b417559a17dc05a07467a28d37fec9b9a7c12cb
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84553468"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Az Azure NetApp Files erőforráskorlátai

A Azure NetApp Files erőforrás-korlátainak megismerése segíti a kötetek kezelését.

## <a name="resource-limits"></a>Erőforráskorlátok

Az alábbi táblázat a Azure NetApp Files erőforrás-korlátozásait ismerteti:

|  Erőforrás  |  Alapértelmezett korlát  |  A támogatási kérelem alapján állítható  |
|----------------|---------------------|--------------------------------------|
|  A NetApp-fiókok száma Azure-régiónként   |  10    |  Igen   |
|  Kapacitási készletek száma NetApp-fiókban   |    25     |   Igen   |
|  Kötetek száma kapacitási készlet szerint     |    500   |    Igen     |
|  Pillanatképek másodpercenkénti száma       |    255     |    Nem        |
|  Azure NetApp Files (Microsoft. NetApp/kötetek) számára az Azure-ban delegált alhálózatok száma Virtual Network    |   1   |    Nem    |
|  A VNet lévő használt IP-címek száma (beleértve az azonnal összetartozó virtuális hálózatok is) Azure NetApp Files   |    1000   |    Nem   |
|  Egyetlen kapacitású készlet minimális mérete   |  4 TiB     |    Nem  |
|  Egyetlen kapacitású készlet maximális mérete    |  500 TiB   |   Nem   |
|  Egyetlen kötet minimális mérete    |    100 GiB    |    Nem    |
|  Egyetlen kötet maximális mérete     |    100 TiB    |    Nem    |
|  Egyetlen fájl maximális mérete     |    16 TiB    |    Nem    |    
|  A címtár metaadatainak maximális mérete egyetlen címtárban      |    320 MB    |    Nem    |    
|  Fájlok maximális száma ([maxfiles](#maxfiles))/kötet     |    100 000 000    |    Igen    |    

További információ: a [kapacitások kezelésével kapcsolatos gyakori kérdések](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="maxfiles-limits"></a>Maxfiles korlátok<a name="maxfiles"></a> 

Azure NetApp Files kötetek *maxfiles*nevű korláttal rendelkeznek. A maxfiles korlátja a kötet által tartalmazott fájlok száma. Egy Azure NetApp Files kötethez tartozó maxfiles-korlát indexelése a kötet mérete (kvóta) alapján történik. A kötetek maxfiles korlátja növekszik vagy csökken a kiosztott kötet méretének 20 000 000-os fájlja alapján. 

A szolgáltatás dinamikusan módosítja a kötet maxfiles korlátját a kiosztott méret alapján. Például egy 1 TiB-os mérettel konfigurált kötethez maxfiles korlát 20 000 000. A kötet méretének későbbi módosításai a következő szabályok alapján automatikusan újramódosíthatják a maxfiles-korlátot: 

|    Kötet mérete (kvóta)     |  A maxfiles-korlát automatikus módosítása    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 000 000     |
|    >= 1 TiB, de < 2 TiB    |    40 000 000     |
|    >= 2 TiB, de < 3 TiB    |    60 000 000     |
|    >= 3 TiB, de < 4 TiB    |    80 000 000     |
|    >= 4 TiB                |    100 000 000    |

A kötetek méretének növeléséhez [támogatási kérést](#limit_increase) indíthat, amely a maxfiles-korlátot 100 000 000-nál nagyobb mértékben növelheti.

## <a name="request-limit-increase"></a>Kérelmek korlátjának növekedése<a name="limit_increase"></a> 

Létrehozhat egy Azure-támogatási kérelmet, amellyel növelheti az állítható korlátokat a fenti táblázatból. 

Azure Portal navigációs síkon: 

1. Kattintson a **Súgó és támogatás**elemre.
2. Kattintson az **+ új támogatási kérelem**elemre.
3. Az alapvető beállítások lapon adja meg a következő információkat: 
    1. Probléma típusa: válassza **a szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.
    2. Előfizetések: válassza ki az erőforráshoz tartozó előfizetést, amelyre szüksége van a kvóta növeléséhez.
    3. Kvóta típusa: válassza a **Storage: Azure NetApp Files korlátok**elemet.
    4. Kattintson a **Tovább gombra: megoldások**.
4. A Részletek lapon:
    1. A Leírás mezőben adja meg a következő információkat a megfelelő erőforrástípus számára:

        |  Erőforrás  |    Szülő erőforrások      |    Kért új korlátok     |    A kvóta növelésének oka       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Fiók |  *Előfizetés azonosítója*   |  *Kért új maximális **fiók** száma*    |  *Milyen forgatókönyv vagy használati eset kéri a kérést?*  |
        |  Készlet    |  *Előfizetés azonosítója, fiók URI azonosítója*  |  *Kért új **készlet** maximális száma*   |  *Milyen forgatókönyv vagy használati eset kéri a kérést?*  |
        |  Kötet  |  *Előfizetés azonosítója, fiók URI azonosítója, készlet URI azonosítója*   |  *Kért új maximális **kötet** száma*     |  *Milyen forgatókönyv vagy használati eset kéri a kérést?*  |
        |  Maxfiles  |  *Előfizetés azonosítója, fiók URI azonosítója, készlet URI azonosítója, kötet URI*   |  *Kért új maximális **maxfiles** -szám*     |  *Milyen forgatókönyv vagy használati eset kéri a kérést?*  |    

    2. Adja meg a megfelelő támogatási módszert, és adja meg a szerződésre vonatkozó információkat.

    3. Kattintson a **Tovább gombra: felülvizsgálat + létrehozás** elemre a kérelem létrehozásához. 


## <a name="next-steps"></a>Következő lépések  

- [Az Azure NetApp Files tárhely-hierarchiájának ismertetése](azure-netapp-files-understand-storage-hierarchy.md)
- [Az Azure NetApp Files költségmodellje](azure-netapp-files-cost-model.md)
