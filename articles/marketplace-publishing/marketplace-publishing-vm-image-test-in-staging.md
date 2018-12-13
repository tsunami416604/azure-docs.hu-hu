---
title: Tesztelje a Virtuálisgép-ajánlat a Marketplace-en |} A Microsoft Docs
description: Megtudhatja, hogyan tesztelheti a Virtuálisgép-lemezkép az Azure Marketplace-en.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: hascipio
ms.openlocfilehash: bf41953300c3042853118c815dbf64e4474106fa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53194113"
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>A Virtuálisgép-ajánlat az Azure Marketplace-en tesztelése az átmeneti állapotában
Átmeneti azt jelenti, hogy központi telepítése egy saját "védőfal" mögött, ahol tesztelheti, és ellenőrizze a működését, a piactéren való telepítés előtt a Termékváltozat. A Termékváltozat jelenik meg, ahogy bármilyen olyan ügyfelünk, akit helyezte üzembe, hogy átmeneti. A Virtuálisgép-lemezkép kell leküldeni, átmeneti minősítéssel kell rendelkezniük.

## <a name="step-1-push-your-offer-to-staging"></a>1. lépés: Az ajánlat átmeneti környezetbe
1. Az a **közzététel** lapra, majd **az átmeneti üzem**.
   
    ![rajz](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. Ha a közzétételi portál értesíti a hibákat, javítsa ki őket.
3. Az a **férhet hozzá az előkészített ajánlat?** párbeszédpanelen adja meg a az ajánlat kipróbálásához használni kívánt Azure-előfizetések listáját az [Azure betekintő portál](https://portal.azure.com).
   
   > [!NOTE]
   > Virtuális gépek esetén megoldássablonokkal, és adjon **nem** engedélyezett előfizetések CSP, DreamSpark- vagy Azure in Open licencprogram típusú.
   > 
   > 
   >
   > Virtuális gépek, a gombra kattintva esetén **átmeneti üzembe**, az alábbi lépéseket kell végrehajtani a jelenet mögött. Lesz a közzététel lapon az egyes lépések állapotának megtekintése a közzétételi portálon. Ellenőrizze ezen a lapon rendszeres időközönként (mindaddig, amíg az állapot mutatja, ELŐKÉSZÍTETT) javítás a végétől igénylő hibák információkért.

   > - Először az előkészítési kérelemben kerül a minősítési csapatot, amely a virtuális merevlemez ellenőrzése. Azonban ha a kérelem csak marketing-módosítás van itt, majd a minősítési lépésben a rendszer kihagyta.
   > - A minősítés befejezése után a replikáció az ajánlat indítsa el az összes Azure-adatközpontok között. Általában 24-48hours a replikálás befejezéséhez szükséges, de is igénybe vehet egy héttel a vhd méretétől függően. Ha viszont a kérelem csak marketing-módosítás van itt, majd a replikálást, gyorsabb.
   > - Amikor a replikálás is befejeződik, majd az ajánlat elérhető lesz a [az Azure portal](http:/portal.azure.com). Ebben az időszakban az állapot ELŐKÉSZÍTENI válnak a közzétételi portálon. Egy előkészített ajánlat jelenik meg a a [az Azure portal](http:/portal.azure.com) csak használatával a, amellyel elő van készítve az ajánlatot az előfizetéshez tartozó e-mail-azonosítót.

1. Jelentkezzen be a [Azure betekintő portál](https://portal.azure.com) az előző lépésben szereplő Azure-előfizetések egyikének használatával.
2. Keresse meg az ajánlat, és ellenőrizze a VM-lemezkép pontok:
   
   * Győződjön meg arról, hogy marketing-tartalom megjelenik-e megfelelően a Marketplace-en.
   * A Virtuálisgép-lemezkép teljes körű üzembe helyezése.
     
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> Az ajánlat átmeneti, amíg nem értesíti a Microsoftot a közzétételi portálon keresztül arról marad [**közzététel** lap > kattintson a gombra **"Kérelem jóváhagyása a leküldéses, éles"**], hogy készen áll az való üzembe helyezésre éles környezetben. Ez ideális alkalom arra, hogy a csapat ellenőrzés mindent fogja a listán az ajánlatot.
> 
> A fejlesztői platform a kiadó által a jelenleg előzetes módban az ajánlat tesztelés tervezték. Kifejezetten nem ajánlott a platofrm használatával kereskedelmi célokra.
> 
> 

## <a name="next-steps"></a>További lépések
Most, hogy az ajánlat "előkészített" és tesztelése a működését, és marketing-tartalmat, folytassa a végső közzétételi fázis **4. lépés**: [Üzembe helyezése az ajánlatot a piactéren](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Lásd még
* [Első lépések: Ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)

