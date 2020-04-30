---
title: Virtuálisgép-ajánlat – közzétételi útmutató az Azure Marketplace-hez
description: Ez a cikk a virtuális gép közzétételének követelményeit és a piactéren üzembe helyezett szoftveres ingyenes próbaverziót ismerteti.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687494"
---
# <a name="virtual-machine-offer-publishing-guide"></a>A virtuális gépek ajánlatának közzétételi útmutatója

A virtuálisgép-lemezképek egyike a megoldásnak az Azure Marketplace-en való közzétételének egyik fő módja. Az útmutató segítségével megismerheti az ajánlat követelményeit. 

Ezek olyan tranzakciós ajánlatok, amelyek üzembe helyezése és számlázása a piactéren történik. A felhasználó által megjelenő művelet hívása "Letöltés most".

## <a name="free-trial"></a>Ingyenes próbaverzió 

Ha a saját licenc használata (BYOL) számlázási modellt használja, a felhasználók az ajánlat tesztelésére korlátozott feltételekkel férhetnek hozzá. 

## <a name="test-drive"></a>Tesztverzió

Egy vagy több virtuális gépet üzembe kell helyeznie infrastruktúra-szolgáltatás (IaaS) vagy szolgáltatott (SaaS) alkalmazások használatával. A tesztelési meghajtó közzétételi lehetőségének előnye, hogy egy virtuális gép vagy egy teljes megoldás automatikusan kiépíthető, amelyet egy partner által üzemeltetett interaktív bemutató vezet. A tesztvezetés további díj nélkül nyújt értékelést az ügyfélnek. Az ügyfélnek nem kell egy meglévő Azure-ügyfélnek lennie a próbaidőszakos felhasználói élményben való részvételhez. 

Lépjen kapcsolatba velünk az [amp-testdrive](mailto:amp-testdrive@microsoft.com) az első lépésekhez. 

|Követelmények  |Részletek |
|---------|---------|
| Rendelkezik Piactéri alkalmazással   |    Egy vagy több virtuális gép IaaS vagy SaaS-n keresztül.      |

## <a name="interactive-demo"></a>Interaktív bemutató

Interaktív bemutató használatával biztosíthatja megoldását az ügyfeleknek. Az interaktív bemutató közzétételi lehetőségének előnye, hogy az összetett megoldás bonyolult kiépítés nélkül biztosít próbaverziót. 

## <a name="virtual-machine-offer"></a>Virtuálisgép-ajánlat

Ha virtuális berendezést telepít az ügyfélhez társított előfizetésre, használja a virtuális gép ajánlatának típusát. A virtuális gépek teljes mértékben elérhetők az utólagos elszámolású vagy a saját licencű (BYOL) licencelési modellek használatával. A Microsoft üzemelteti a kereskedelmi tranzakciót, és az Ön nevében számlázza az ügyfelet. Az ügyfél és a Microsoft közötti előnyben részesített fizetési kapcsolat előnyeit használhatja, beleértve a nagyvállalati szerződéseket is.

> [!NOTE]
> Jelenleg a Nagyvállalati Szerződéshoz kapcsolódó pénzügyi kötelezettségvállalások a virtuális gép Azure-használatával használhatók fel, de nem a szoftveres licencelési díjakra.  
> 
> [!NOTE]
> Lehetősége van arra, hogy a virtuális gép felderítését és üzembe helyezését egy adott ügyfélre korlátozza, ha közzéteszi a képet és a díjszabást privát ajánlatként. A privát ajánlatok lehetővé teszi, hogy exkluzív ajánlatokat hozzon létre a legközelebbi ügyfelei számára, és testreszabott szoftvereket és feltételeket nyújtson. A testreszabott feltételek lehetővé teszik különféle forgatókönyvek kiemelését, beleértve a speciális díjszabási és használati feltételeket, valamint a korlátozott kiadású szoftverek korai elérését. A privát ajánlatok lehetővé teszik, hogy meghatározott díjszabást vagy termékeket biztosítson egy korlátozott számú ügyfél számára egy új SKU létrehozásával ezekkel az adatokkal.  
> *   A privát ajánlatokról a [Azure.microsoft.com/blog/Private-offers-on-Azure-Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)címen elérhető Azure Marketplace webhelyen talál további információt.  

| Követelmény | Részletek |  
|:--- |:--- | 
| Számlázás és mérés | A virtuális gépnek BYOL vagy utólagos elszámolású havi számlázást kell támogatnia. |  
| Azure-kompatibilis virtuális merevlemez (VHD) | A virtuális gépeket Windows vagy Linux rendszerre kell építeni. <ul> <li>A linuxos virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Az Azure-ban támogatott Linux-disztribúciók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>A Windows virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Azure-kompatibilis virtuális merevlemez létrehozása](./partner-center-portal/azure-vm-create-offer.md).</li> </ul> |  

>[!Note]
>A Cloud Solution Providers (CSP) Partner Channel opt-in mostantól elérhető.  Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](./cloud-solution-providers.md) című témakört.

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, 

- [További](https://azuremarketplace.microsoft.com/sell) információ a Piactérről.

Ha regisztrálva van, és új ajánlatot hoz létre, vagy dolgozik egy meglévőn,

- Az ajánlat létrehozásához vagy befejezéséhez [Jelentkezzen be a partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
- További információt a [virtuálisgép-ajánlat létrehozása](./partner-center-portal/azure-vm-create-offer.md) című témakörben talál.
