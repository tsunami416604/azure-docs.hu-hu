---
title: Ebből a cikkből elsajátíthatja, hogyan frissítse az Azure Stack ajánlatokat és csomagokat |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan lehet megtekinteni és módosítani a meglévő Azure Stack ajánlatokat és csomagokat.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 07/30/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a35ba993e6fd1162fa4a18bc0d6bc9351fe7dfa2
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358275"
---
# <a name="azure-stack-add-on-plans"></a>Az Azure Stack kiegészítő csomagok

Az Azure Stack operátorait szerint módosíthatja a kiegészítő csomagok létrehozása egy [ *ALAPCSOMAG* ](azure-stack-create-plan.md) Ha szeretne további szolgáltatásokat, vagy kibővítéséhez *számítógép*, *storage* , vagy *hálózati* kvóták alapcsomagok az ajánlat mellett. Kiegészítő csomagok az alapszintű csomag módosítása és opcionális bővítmények, amelyeket a felhasználók előfizetni. 

Vannak olyan helyzetek, amikor egyetlen csomagban kombinálásával mindent nem optimális. Más esetekben érdemes rendelkezik egy alap megtervezése és a majd a további szolgáltatásokat a kiegészítő csomagok. Például az IaaS-szolgáltatások ajánlat részeként egy alapcsomagot az összes PaaS-szolgáltatások kiegészítő csomagok számít sikerült dönt.

Felhasználók, erőforrások kihasználtságát, fordítson egy másik oka a használatára a kiegészítő csomagok azt. Ehhez egy alapcsomagot viszonylag kis kvóták (attól függően, a szükséges szolgáltatások) tartalmazó kezdhet. Ezt követően felhasználók kapacitás érni, azok lenne riasztást kap, akkor a lefoglalt erőforrásokat a hozzárendelt terv alapján felhasznált. Itt a felhasználók ezután válassza ki egy kiegészítő csomagot, amely a további forrásokat biztosít.

> [!NOTE]
> Ha nem szeretné kiterjeszteni a kvóta egy kiegészítő csomag használatával, azt is beállíthatja a [az eredeti konfigurációt kvóta szerkesztése](azure-stack-quota-types.md#to-edit-a-quota). 

Amikor egy felhasználó felvesz egy kiegészítő csomag egy meglévő ajánlat előfizetéshez, a további erőforrások is igénybe vehet egy órát jelenik meg. 

## <a name="create-an-add-on-plan"></a>Hozzon létre egy kiegészítő csomag
Létező ajánlat módosításával kiegészítő csomagok jönnek létre:

1. Jelentkezzen be az Azure Stack rendszergazdai portál a felhő rendszergazdájához.
2. Ugyanezekkel a lépésekkel használt [hozzon létre egy új alapszintű csomag](azure-stack-create-plan.md) , amely nem volt korábban kínált szolgáltatásokat kínáló új tervet létrehozni. Ebben a példában a Key Vault (Microsoft.KeyVault) szolgáltatások szerepelni fog az új csomag.
3. Kattintson a felügyeleti portál **kínál** , és válassza ki az ajánlat és a egy kiegészítő csomag frissíteni kell.

   ![](media/create-add-on-plan/1.PNG)

4.  Az ajánlat tulajdonságok alján görgessen és válassza ki **kiegészítő csomagok**. Kattintson a **Hozzáadás** parancsra.
   
    ![](media/create-add-on-plan/2.PNG)

5. Válassza ki a csomag hozzáadásához. Ebben a példában a csomagot nevezik **Key vault terv**. A csomag kiválasztása után kattintson az **kiválasztása** a csomag hozzáadása az ajánlatot. A terv hozzáadásának az ajánlatra sikeresen értesítést kell kapnia.
   
    ![](media/create-add-on-plan/3.PNG)

6. Tekintse át a bővítmény ellenőrzése, hogy az új kiegészítő csomagot az ajánlatban csomagok listáját.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>További lépések
[Ajánlat létrehozása](azure-stack-create-offer.md)