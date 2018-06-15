---
title: A Virtuálisgép-ajánlat a piactér tesztelése |} Microsoft Docs
description: A Virtuálisgép-lemezkép tesztelése az Azure piactéren ismertetése.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mbaldwin
ms.openlocfilehash: 7f225f113dd465f79d5d195b59a809fe3be6a1f8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29936939"
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>A tesztelési virtuális gép az Azure piactéren ajánlatát tesztelése
Átmeneti azt jelenti, hogy a SKU "védőfal", ahol tesztelése és érvényesítése funkciókat, mielőtt telepítené őket a piactér privát telepítése. A Termékváltozat volna egy felhasználói, akik már telepítették az átmeneti jelenik meg. A Virtuálisgép-lemezkép igazolja a kell előkészítésre továbbít.

## <a name="step-1-push-your-offer-to-staging"></a>1. lépés: Az átmeneti ajánlatot leküldéses
1. Az a **közzététel** lapra, majd **átmeneti leküldése**.
   
    ![rajz](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. Ha a közzétételi Portáljára értesíti az esetleges hibákat, javítsa ki őket.
3. Az a **ki férhet hozzá az előkészített ajánlatot?** párbeszédpanelen adja meg a használni kívánt tekintse meg az ajánlat az Azure-előfizetések listája a [Azure betekintő portál](https://portal.azure.com).
   
   > [!NOTE]
   > Virtuális gépek esetén és a megoldás sablonok adjon **nem** típusú kriptográfiai Szolgáltató, DreamSpark- vagy Azure in Open licencprogram engedélyezett előfizetések.
   > 
   > 

    > Virtuális gépek, a gombra kattintva esetén **LEKÜLDÉSES átmeneti**, az alábbi lépéseket kell végrehajtani a leképezni kívánt jelenetben mögött. A közzétételi tekintse meg a folyamatban van a közzététel lapon minden egyes lépést tudnak portálon. Ellenőrizze ezen a lapon rendszeres időközönként (mindaddig, amíg az állapot mutatja ELŐKÉSZÍTETT) javítása a átfogóan igénylő hiba információkat.

    > - Először ellenőrizze a virtuális merevlemez hitelesítő csoport átmeneti kérelmét lép. Azonban ha a kérést kapott rendelkezik módosítása csak marketing, majd a hitelesítésszolgáltató a lépés kimarad.
    > - A hitelesítésszolgáltató végrehajtása után ajánlat replikációs indítsa el az Azure adatközpontjaiban között. Általában 24-48hours a replikálás befejezéséhez szükséges, de a hét attól függően, hogy a virtuális merevlemez méretére is tarthat. Azonban ha a kérést kapott rendelkezik módosítása csak marketing, akkor a replikáció értéke gyorsabb.
    > - Ha a replikáció befejeződött, majd az ajánlat elérhető lesz a [Azure-portálon](http:/portal.azure.com). Ebben az időszakban az állapotkezelő ELŐKÉSZÍTENI válnak a közzétételi portálon. Egy előkészített ajánlatot csak látható a [Azure-portálon](http:/portal.azure.com) csak használatával az e-mailek azonosítója, amelyhez az ajánlat elő van készítve az előfizetéshez tartozó.

1. Jelentkezzen be a [Azure betekintő portál](https://portal.azure.com) az előző lépésben felsorolt Azure-előfizetések egyikének használatával.
2. Keresse meg az ajánlatot, és ellenőrizze a VM-lemezkép pontok:
   
   * Győződjön meg arról, hogy tartalom marketing jeleníti meg helyesen a piactéren.
   * A Virtuálisgép-lemezkép-végpontok közötti üzembe helyezése.
     
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> Az ajánlatot marad mindaddig, amíg Ön értesítést a közzétételi portálon Microsoft átmeneti [**közzététel** lapon > kattintson a gombra **"Kérelem jóváhagyásra való leküldéses az éles"**], hogy készen, amelyekkel a éles. Ez az az ideális idő szeretné, hogy a csoport ellenőrzése minden tagját keresztül minden felsorolt fog ajánlatát előkészítésekor.
> 
> Az átmeneti platform végzi a tesztelés ajánlatot egy előnézeti módban a közzétevő. Kifejezetten nem ajánlott a platofrm kereskedelmi célra használja.
> 
> 

## <a name="next-steps"></a>További lépések
Most, hogy az ajánlatot "előkészített" és funkciókat, tesztelése és tartalom marketing, folytathatja a végső közzétételi fázis **4. lépés**: [központi telepítése az ajánlatot a piactér](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Lásd még
* [Első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)

