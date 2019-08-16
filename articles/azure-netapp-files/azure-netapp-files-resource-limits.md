---
title: A Azure NetApp Files erőforrás-korlátai | Microsoft Docs
description: A Azure NetApp Files erőforrások korlátozásait ismerteti, beleértve a NetApp-fiókok, a kapacitás-készletek, a kötetek, a pillanatképek és a delegált alhálózat korlátait.
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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 4ce40fdf36f7d66e60e15955318e43f1f24f275f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515842"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Az Azure NetApp Files erőforráskorlátai

A Azure NetApp Files erőforrás-korlátainak megismerése segíti a kötetek kezelését.

## <a name="resource-limits"></a>Erőforráskorlátok

Az alábbi táblázat a Azure NetApp Files erőforrás-korlátozásait ismerteti:

|  Resource  |  Alapértelmezett korlát  |  A támogatási kérelem alapján állítható  |
|----------------|---------------------|--------------------------------------|
|  Egy Azure-előfizetéshez tartozó NetApp-fiókok száma   |  10    |  Igen   |
|  Kapacitási készletek száma NetApp-fiókban   |    25     |   Igen   |
|  Kötetek száma kapacitási készlet szerint     |    500   |    Igen     |
|  Pillanatképek másodpercenkénti száma       |    255     |    Nem        |
|  Azure NetApp Files (Microsoft. NetApp/kötetek) számára az Azure-ban delegált alhálózatok száma Virtual Network    |   1   |    Nem    |
|  Az egyidejű IP-címek száma VNet (beleértve a virtuális hálózatok is), amelyek hozzáférhetnek Azure NetApp Files   |    1000   |    Nem   |
|  Egyetlen kapacitású készlet minimális mérete   |  4 TiB     |    Nem  |
|  Egyetlen kapacitású készlet maximális mérete    |  500 TiB   |   Nem   |
|  Egyetlen kötet minimális mérete    |    100 GiB    |    Nem    |
|  Egyetlen kötet maximális mérete     |    100 TiB    |    Nem       |
|  Fájlok maximális száma (inode)/kötet     |    50 000 000    |    Nem    |    

## <a name="request-limit-increase"></a>Kérelmek korlátjának növekedése 

Létrehozhat egy Azure-támogatási kérelmet, amellyel növelheti az állítható korlátokat a fenti táblázatból. 

Azure Portal navigációs síkon: 

1. Kattintson a **Súgó és támogatás**elemre.
2. Kattintson az **+ új támogatási kérelem**elemre.
3. Az alapvető beállítások lapon adja meg a következő információkat: 
    1. Probléma típusa: Válassza **a szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.
    2. Előfizetések: Válassza ki az erőforráshoz tartozó előfizetést, amelyre szüksége van a kvóta növeléséhez.
    3. Kvóta típusa: Tároló **kiválasztása: Azure NetApp Files korlátok**.
    4. Kattintson **a Tovább gombra: Megoldások**.
4. A Részletek lapon:
    1. A Leírás mezőben adja meg a következő információkat a megfelelő erőforrástípus számára:

        |  Resource  |    Szülő erőforrások      |    Kért új korlátok     |    A kvóta növelésének oka       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Fiók |  *Előfizetés azonosítója*   |  *Kért új maximális **fiók** száma*    |  *Milyen forgatókönyv vagy használati eset kéri a kérést?*  |
        |  Készlet    |  *Előfizetés azonosítója, fiók URI azonosítója*  |  *Kért új **készlet** maximális száma*   |  *Milyen forgatókönyv vagy használati eset kéri a kérést?*  |
        |  Kötet  |  *Előfizetés azonosítója, fiók URI azonosítója, készlet URI azonosítója*   |  *Kért új maximális **kötet** száma*     |  *Milyen forgatókönyv vagy használati eset kéri a kérést?*  |

    2. Adja meg a megfelelő támogatási módszert, és adja meg a szerződésre vonatkozó információkat.

    3. Kattintson **a Tovább gombra: A kérelem létrehozásához tekintse át a + létrehozás** lehetőséget. 


## <a name="next-steps"></a>További lépések  

- [A Azure NetApp Files tárolási hierarchiájának megismerése](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Filesi Cost Model](azure-netapp-files-cost-model.md)
