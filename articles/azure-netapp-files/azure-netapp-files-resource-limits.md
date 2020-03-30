---
title: Az Azure NetApp-fájlok erőforráskorlátai | Microsoft dokumentumok
description: Az Azure NetApp Files-erőforrások ra vonatkozó korlátozások at és az erőforrás-korlát növelésének igénylését ismerteti.
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
ms.date: 3/13/2020
ms.author: b-juche
ms.openlocfilehash: 36b2d50722a1840e461d6907f440d859c7c82117
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408827"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Az Azure NetApp Files erőforráskorlátai

Az Azure NetApp Files erőforrás-korlátainak ismertetése segít a kötetek kezelésében.

## <a name="resource-limits"></a>Erőforráskorlátok

Az alábbi táblázat az Azure NetApp-fájlok erőforráskorlátait ismerteti:

|  Erőforrás  |  Alapértelmezett korlát  |  Állítható támogatási kérelemmel  |
|----------------|---------------------|--------------------------------------|
|  NetApp-fiókok száma Azure-régiónként   |  10    |  Igen   |
|  Kapacitáskészletek száma NetApp-fiókonként   |    25     |   Igen   |
|  Kötetek száma kapacitáskészletenként     |    500   |    Igen     |
|  Pillanatképek száma kötetenként       |    255     |    Nem        |
|  Az Azure NetApp-fájlokba delegált alhálózatok (Microsoft.NetApp/kötetek) által Azure virtuális hálózatonként delegált alhálózatok száma    |   1   |    Nem    |
|  A virtuális hálózatban használt IP-k száma (beleértve az azonnal társviszonyba adott virtuális hálózatokat) az Azure NetApp-fájlokkal   |    1000   |    Igen   |
|  Egyetlen kapacitáskészlet legkisebb mérete   |  4 TiB     |    Nem  |
|  Egyetlen kapacitáskészlet maximális mérete    |  500 TiB   |   Nem   |
|  Egyetlen kötet legkisebb mérete    |    100 GiB    |    Nem    |
|  Egyetlen kötet maximális mérete     |    100 TiB    |    Nem    |
|  Egyetlen fájl maximális mérete     |    16 TiB    |    Nem    |    
|  Egyetlen könyvtár maximális mérete      |    320 MB    |    Nem    |    
|  Fájlok maximális száma ([maxfiles](#maxfiles)) kötetenként     |    100 millió    |    Igen    |    

További információt a Kapacitáskezelési gyakori kérdések című [témakörben talál.](azure-netapp-files-faqs.md#capacity-management-faqs)

## <a name="maxfiles-limits"></a>Maxfiles korlátok<a name="maxfiles"></a> 

Az Azure NetApp Files kötetek maxfiles nevű korláttal *rendelkeznek.* A maxfiles korlát a kötetben található fájlok száma. Az Azure NetApp Files kötet maxfiles-korlátja a kötet mérete (kvótája) alapján kerül indexelésre. A kötet maxfiles korlátja a kiosztott kötetméret TiB-re vetítve 20 millió fájlra nő vagy csökken. 

A szolgáltatás dinamikusan állítja be a maxfiles korlátot a kötethez a kiosztott méret alapján. Egy eredetileg 1 TiB-méretű kötet például 20 millió maxfiles korláttal rendelkezik. A kötet méretének későbbi módosítása a maxfiles korlát automatikus újrabeállítását eredményezné a következő szabályok alapján: 

|    Kötet mérete (kvóta)     |  A maxfiles határérték automatikus újrabeállítása    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 millió     |
|    >= 1 TiB, de 2 TiB <    |    40 millió     |
|    >= 2 TiB, de < 3 TiB    |    60 millió     |
|    >= 3 TiB, de < 4 TiB    |    80 millió     |
|    >= 4 TiB                |    100 millió    |

Bármilyen kötetméret esetén támogatási [kérelmet](#limit_increase) kezdeményezhet a maxfiles korlát 100 milliónál nagyobb növelésére.

## <a name="request-limit-increase"></a>Kérelemkorlát-növelés<a name="limit_increase"></a> 

Létrehozhat egy Azure-támogatási kérelmet a fenti táblázatban a módosítható korlátok növeléséhez. 

Az Azure Portal navigációs síkjáról: 

1. Kattintson **a Súgó + támogatás gombra.**
2. Kattintson **a + Új támogatási kérelem gombra.**
3. Az Alapok lapon adja meg a következő információkat: 
    1. Probléma típusa: Válassza a **Szolgáltatás- és előfizetési korlátokat (kvótákat).**
    2. Előfizetések: Válassza ki az előfizetést az erőforrás, amely a kvóta növelve szükséges.
    3. Kvótatípusa: **Tárhely kiválasztása: Az Azure NetApp-fájlok korlátozva.**
    4. Kattintson a **Tovább gombra: Megoldások**.
4. A Részletek lapon:
    1. A Leírás mezőben adja meg a megfelelő erőforrástípusra vonatkozó következő információkat:

        |  Erőforrás  |    Fölérendelt erőforrások      |    Kért új korlátok     |    A kvótaemelés oka       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Fiók |  *Előfizetés azonosítója*   |  *Kért új maximális **számlaszám***    |  *Milyen forgatókönyv vagy használati eset kérte a kérést?*  |
        |  Készlet    |  *Előfizetés azonosítója, fiók URI-ja*  |  *Kért új maximális **készletszám***   |  *Milyen forgatókönyv vagy használati eset kérte a kérést?*  |
        |  Kötet  |  *Előfizetés azonosítója, fiók URI-ja, készletURI-ja*   |  *Kért új maximális **kötetszám***     |  *Milyen forgatókönyv vagy használati eset kérte a kérést?*  |
        |  Maxfiles fájlok  |  *Előfizetés azonosítója, fiók URI-ja, készletURI-ja, kötetURI-ja*   |  *Kért új maximális **maxfiles** szám*     |  *Milyen forgatókönyv vagy használati eset kérte a kérést?*  |    

    2. Adja meg a megfelelő támogatási módszert, és adja meg a szerződés adatait.

    3. Kattintson a **Tovább gombra: A** kérelem létrehozásához kattintson a Véleményezés + létrehozás gombra. 


## <a name="next-steps"></a>További lépések  

- [Az Azure NetApp Files tárhely-hierarchiájának ismertetése](azure-netapp-files-understand-storage-hierarchy.md)
- [Az Azure NetApp Files költségmodellje](azure-netapp-files-cost-model.md)
