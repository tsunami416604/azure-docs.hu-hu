---
title: Virtuálisgép-ajánlat közzétételi útmutatója az Azure Piactérhez
description: Ez a cikk ismerteti a virtuális gép közzétételének követelményeit, és egy szoftver ingyenes próbaverziót kell telepíteni a Piactérről.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: dsindona
ms.openlocfilehash: 34de5f59e96a37282063741a1664f512697c167b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288716"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Virtuálisgép-ajánlat közzétételi útmutatója

A Virtuálisgép-lemezképek az egyik fő módja a megoldás közzétételének az Azure Marketplace-en. Ebben az útmutatóban megismerheti az ajánlat követelményeit. 

Ezek olyan tranzakciós ajánlatok, amelyek a Marketplace-en keresztül vannak telepítve és számlázva. A felhasználó által látott cselekvésre való felhívás a "Get It Now" (Get It Now) (Get It Now) (Get It Now) (Get It Now) (Get It Now) (Get It Now)

## <a name="free-trial"></a>Ingyenes próbaverzió 

A Bring Your Own License (BYOL) számlázási modell használata esetén elintézheti, hogy a felhasználók teszteljék az ajánlatot a korlátozott távú szoftverlicencek elérésével. 

## <a name="test-drive"></a>Tesztverzió

Egy vagy több virtuális gépet az infrastruktúra-szolgáltatásként (IaaS) vagy a szoftver-szolgáltatásként (SaaS) alkalmazásokon keresztül telepít. A tesztmeghajtó közzétételi lehetőség előnye a virtuális gép vagy a partner által irányított tárlatvezetés által vezetett teljes megoldás automatikus kiépítése. A tesztvezetés további költségek nélkül biztosít értékelést az ügyfél számára. Az ügyfélnek nem kell meglévő Azure-ügyfélnek lennie ahhoz, hogy részt vehessaa nek a próbaverzióban. 

Vegye fel velünk a kapcsolatot az [amp-testdrive-on](mailto:amp-testdrive@microsoft.com) a kezdéshez. 

|Követelmények  |Részletek |
|---------|---------|
| Piactéri alkalmazással rendelkezik   |    Egy vagy több virtuális gép az IaaS vagy a SaaS-en keresztül.      |

## <a name="interactive-demo"></a>Interaktív demó

A megoldás interaktív bemutatóval irányított élményt nyújt ügyfeleinek. Az interaktív bemutató közzétételi lehetőség előnye, hogy próbaverziót biztosít az összetett megoldás bonyolult kiépítése nélkül. 

## <a name="virtual-machine-offer"></a>Virtuális gép ajánlat

Használja a virtuális gép ajánlat típusát, amikor üzembe helyez egy virtuális berendezést az ügyfélhez társított előfizetéshez. A virtuális gépek teljes mértékben kereskedelmi engedélyezve vannak használatalapú fizetéses vagy bring-your-own-license (BYOL) licencelési modellek használatával. A Microsoft üzemelteti a kereskedelmi tranzakciót, és az Ön nevében számlázza az ügyfelet. Az ügyfél és a Microsoft közötti előnyben részesített fizetési kapcsolat előnyeit élvezheti, beleértve a nagyvállalati szerződéseket is.

> [!NOTE]
> Jelenleg a nagyvállalati szerződéssel kapcsolatos pénzügyi kötelezettségvállalások felhasználhatók a virtuális gép Azure-használatából, de a szoftverlicencelési díjak ellenében nem.  
> 
> [!NOTE]
> Korlátozhatja a virtuális gép felderítését és üzembe helyezését az ügyfelek egy adott készletére a lemezkép és az árképzés privát ajánlatként való közzétételével. Privát ajánlatok kinyit a képességét, hogy hozzon létre exkluzív ajánlatokat a legközelebbi ügyfelek és személyre szabott szoftver és feltételek. A testreszabott kifejezések lehetővé teszik, hogy kiemelje a különböző forgatókönyveket, beleértve a mező-vezérelt foglalkozik speciális árképzési és feltételek, valamint a korai hozzáférést a korlátozott kiadás szoftver. A privát ajánlatok lehetővé teszik, hogy meghatározott árképzést vagy termékeket adjon az ügyfelek korlátozott készletének azáltal, hogy új termékváltozatot hoz létre ezekkel az adatokkal.  
> *   A privát ajánlatokról a privát ajánlatok az [Azure](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)Marketplace-en azure.microsoft.com/blog/private-offers-on-azure-marketplace.  

| Követelmény | Részletek |  
|:--- |:--- | 
| Számlázás és mérés | A virtuális gépnek támogatnia kell a BYOL vagy a pay-as-you-go havi számlázást. |  
| Azure-kompatibilis virtuális merevlemez (VHD) | A virtuális gépeknek Windows vagy Linux rendszerre kell épülniük. <ul> <li>A Linux virtuális merevlemez ek létrehozásáról további információt az [Azure-ban jóváhagyott Linux-disztribúciók című](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)témakörben talál.</li> <li>A Windows virtuális merevlemez ek létrehozásáról további információt az [Azure-kompatibilis virtuális merevlemez létrehozása](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)című témakörben talál.</li> </ul> |  

>[!Note]
>A felhőszolgáltatók (CSP) partnercsatorna-opt-in már elérhető.  Az ajánlat microsoftos CSP-partnercsatornákon keresztül történő marketingről további információt a [felhőszolgáltatók](./cloud-solution-providers.md) ban talál.

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, 

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a piacon.

Ha regisztrált, és új ajánlatot hoz létre, vagy egy meglévőn dolgozik,

- [Jelentkezzen be a Cloud Partner Portalszolgáltatásba](https://cloudpartner.azure.com) az ajánlat létrehozásához vagy teljesítéséhez.
- További információt a [Virtuálisgép-ajánlat](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer) című témakörben talál.
