---
title: Virtuálisgép-ajánlat – közzétételi útmutató az Azure Marketplace-hez
description: Ez a cikk a virtuális gép közzétételének követelményeit és a piactéren üzembe helyezett szoftveres ingyenes próbaverziót ismerteti.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 0f2047501b6c57b2bb98ff7e3c56498417644324
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818982"
---
# <a name="virtual-machine-offer-publishing-guide"></a>A virtuális gépek ajánlatának közzétételi útmutatója

A virtuálisgép-lemezképek egyike a megoldásnak az Azure Marketplace-en való közzétételének egyik fő módja. Az útmutató segítségével megismerheti az ajánlat követelményeit. 

Ezek olyan tranzakciós ajánlatok, amelyek üzembe helyezése és számlázása a piactéren történik. A felhasználó által megjelenő művelet hívása "Letöltés most".

## <a name="free-trial"></a>Free Trial (Ingyenes próba) 

Ha a saját licenc használata (BYOL) számlázási modellt használja, a felhasználók az ajánlat tesztelésére korlátozott feltételekkel férhetnek hozzá. Az alábbi követelmények vonatkoznak az ajánlat üzembe helyezésére. 

|Követelmények  |Részletek  |
|---------|---------|
|Ingyenes próbaidőszak és próbaidőszak     |   Az ügyfelek korlátozott ideig ingyenesen kipróbálhatják az alkalmazást. Az ügyfeleknek megjegyzésekre van szükségük az ajánlatra vonatkozó licencek vagy előfizetési díjak megfizetéséhez. Ügyfeleinek nem kell fizetniük az alapul szolgáló Microsoft-termék vagy-szolgáltatásért. Az Azure-előfizetéshez minden próbaverziós beállítás telepítve van. A Cost Optimization és a felügyelet kizárólagos felügyelettel rendelkezik. Dönthet úgy, hogy ingyenes próbaverziót vagy interaktív bemutatót választ. Függetlenül attól, hogy mit választ, az ingyenes próbaverzióban előre meghatározott időtartamot kell biztosítania az ügyfeleknek az ajánlat kipróbálásához.|
|Könnyen konfigurálható, használatra kész megoldás    |  Az alkalmazásnak egyszerűen és gyorsan kell konfigurálnia és beállítania.       |
|Rendelkezésre állás/üzemidő    |    Az SaaS-alkalmazásnak vagy-platformnak legalább 99,9%-os üzemidővel kell rendelkeznie.     |
|Azure Active Directory     |    Az ajánlatnak engedélyeznie kell a Azure Active Directory (Azure AD) összevont egyszeri bejelentkezést (SSO) (Azure AD összevont SSO) a beleegyezett engedéllyel.     |

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
| Azure-kompatibilis virtuális merevlemez (VHD) | A virtuális gépeket Windows vagy Linux rendszerre kell építeni. <ul> <li>A linuxos virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Az Azure-ban támogatott Linux-disztribúciók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>A Windows virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Azure-kompatibilis virtuális merevlemez létrehozása](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

>[!Note]
>A Cloud Solution Providers (CSP) Partner Channel opt-in mostantól elérhető.  Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](./cloud-solution-providers.md) című témakört.

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, 

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a piactéren.

Ha regisztrálva van, és új ajánlatot hoz létre, vagy dolgozik egy meglévőn,

- Az ajánlat létrehozásához vagy befejezéséhez [Jelentkezzen be a Cloud Partner Portalba](https://cloudpartner.azure.com) .
- További információért lásd a [virtuális gép ajánlatát](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer) .
