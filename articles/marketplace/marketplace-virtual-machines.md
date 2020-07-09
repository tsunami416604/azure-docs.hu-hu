---
title: Az Azure Marketplace-en elérhető virtuálisgép-ajánlatok közzétételi útmutatója
description: Ez a cikk ismerteti a virtuális gépek közzétételének követelményeit, valamint az Azure Marketplace-en üzembe helyezett szoftveres ingyenes próbaverziót.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/15/2020
ms.openlocfilehash: 2d615e81891d68327b3225138495262d4eab9aa3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116726"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>A virtuális gépekkel kapcsolatos ajánlatok közzétételi útmutatója

A virtuálisgép-lemezképek közzététele az egyik fő módszer az Azure Marketplace-en való megoldás közzétételére. Ez az útmutató az ilyen típusú ajánlatokra vonatkozó követelmények megismerésére használható. 

A virtuális gépek ajánlatai az Azure Marketplace-en üzembe helyezett és számlázható tranzakciós ajánlatok. A felhasználó által megjelenő művelet meghívása *most*.

## <a name="free-trial"></a>Ingyenes próbaidőszak 

Ahhoz, hogy a felhasználók az ajánlat tesztelésére legyenek érvényesek, a saját licencű (BYOL) számlázási modell használata esetén a korlátozott idejű szoftverlicenc-licencet is elérheti. 

## <a name="test-drive"></a>Tesztelési meghajtó

Egy vagy több virtuális gépet üzembe helyezhet az infrastruktúra-szolgáltatás (IaaS) vagy a szolgáltatott szoftver (SaaS) használatával. A *tesztelési meghajtó* közzétételi lehetőségének előnye, hogy egy virtuális gép vagy egy teljes megoldás automatikus beállítása egy partner által üzemeltetett interaktív bemutató. A tesztvezetés lehetővé teszi, hogy az ügyfelek további díjak nélkül értékeljék a virtuális gépeket. Az ügyfélnek nem kell egy meglévő Azure-ügyfélnek lennie a próbaverzióval való részvételhez. 

Első lépésként vegye fel velünk a kapcsolatot e-mailben az [amp-testdrive](mailto:amp-testdrive@microsoft.com)címen. 

|Követelmények  |Részletek |
|---------|---------|
| Rendelkezik Azure Marketplace-alkalmazással   |  Egy vagy több virtuális gép IaaS vagy SaaS-n keresztül.      |

## <a name="interactive-demo"></a>Interaktív bemutató

Ezzel az ajánlattal interaktív bemutató használatával biztosíthatja ügyfelei számára a megoldás irányítását. Az interaktív bemutató közzétételi lehetőségének előnye, hogy az összetett megoldás bonyolult beállítása nélkül biztosíthatja a próbaverziót. 

## <a name="virtual-machine-offer"></a>Virtuálisgép-ajánlat

Ha virtuális berendezést telepít az ügyfélhez társított előfizetésre, használja a *virtuális gép* ajánlatának típusát. A virtuális gépek teljes mértékben elérhetők az utólagos elszámolású vagy a saját licencű (BYOL) licencelési modellek használatával. A Microsoft üzemelteti a kereskedelmi tranzakciót, és az Ön nevében számlázza az ügyfelet. Az ügyfél és a Microsoft közötti előnyben részesített fizetési kapcsolat előnyeit használhatja, beleértve a nagyvállalati szerződéseket is.

> [!NOTE]
> Jelenleg a Nagyvállalati Szerződéshez kapcsolódó pénzügyi kötelezettségvállalások a virtuális gép Azure-beli használatára, de a szoftverlicenc-díjakra nem alkalmazhatók.  
> 
> [!NOTE]
> A virtuális gép felderítését és üzembe helyezését egy adott ügyfélre korlátozhatja, ha közzéteszi a képet és a díjszabást privát ajánlatként. A privát ajánlatok lehetővé teszi, hogy exkluzív ajánlatokat hozzon létre a legközelebbi ügyfelei számára, és testreszabott szoftvereket és feltételeket nyújtson. A testreszabott feltételek lehetővé teszik különféle forgatókönyvek kiemelését, beleértve a speciális díjszabási és használati feltételeket, valamint a korlátozott kiadású szoftverek korai elérését. A privát ajánlatok lehetővé teszik, hogy meghatározott díjszabást vagy termékeket biztosítson egy korlátozott számú ügyfél számára egy új SKU létrehozásával ezekkel az adatokkal.  
>
> További információ: [privát ajánlatok az Azure Marketplace-en](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Követelmény | Részletek |  
|:--- |:--- | 
| Számlázás és mérés | A virtuális gépnek BYOL vagy utólagos elszámolású havi számlázást kell támogatnia. |  
| Azure-kompatibilis virtuális merevlemez (VHD) | A virtuális gépeket Windows vagy Linux rendszerre kell építeni. A virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: <ul> <li>Az Azure-ban [támogatott Linux-disztribúciók](../virtual-machines/linux/endorsed-distros.md) (linuxos virtuális merevlemezek esetén).</li> <li>[Azure-kompatibilis VHD létrehozása](./partner-center-portal/azure-vm-create-offer.md) (Windows rendszerű virtuális merevlemezekhez).</li> </ul> |  

>[!Note]
>Már elérhető a felhőalapú megoldás-szolgáltató (CSP) Partner Channel-beli aktiválása. Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](./cloud-solution-providers.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, Ismerje meg, hogyan [növelheti Felhőbeli üzletét az Azure Marketplace-szel](https://azuremarketplace.microsoft.com/sell).

A partner Centerben való regisztráció és a használat megkezdése:

- Az ajánlat létrehozásához vagy befejezéséhez [Jelentkezzen be a partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
- További információt a [virtuálisgép-ajánlat létrehozása](./partner-center-portal/azure-vm-create-offer.md) című témakörben talál.
