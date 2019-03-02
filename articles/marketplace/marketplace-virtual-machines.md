---
title: Útmutató az Azure Marketplace közzétételi virtuálisgép-ajánlat
description: Ez a cikk ismerteti a követelmények közzététele egy virtuális gép és a szoftver ingyenes próbaverziót a Marketplace-ről üzembe helyezhető.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: b9403855949e87a287f543c2ac41738e92238303
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217206"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Virtuálisgép-ajánlat közzétételi útmutató

Virtuálisgép-lemezképek tartoznak a fő módon lehet közzéteszi a megoldást az Azure piactéren. Ez az útmutató segítségével áttekinteni a követelményeket, ezt az ajánlatot. 

Ezek a tranzakció ajánlatok, amelyek üzembe helyezve, és a Marketplace-en keresztül számlázott. Művelet, amely a felhasználó kap, a hívást a "Letöltés most."

## <a name="free-trial"></a>Free Trial (Ingyenes próba) 

Beállíthatja, hogy a felhasználók számára, hogy tesztelje az ajánlat korlátozott kifejezés szoftverlicencek elérésével, a Bring Your saját licences (BYOL) számlázási modell használata esetén. Az alábbiakban a követelményeknek, ez az ajánlat üzembe helyezéséhez. 

|Követelmények  |Részletek  |
|---------|---------|
|Ingyenes próbaidőszak és próbaverziója     |   Az ügyfelek ingyenes korlátozott ideig előfordulhat, hogy kipróbálják az alkalmazását. A felhasználók számára szükséges licencek vagy előfizetések díjakat fizetni az ajánlat megjegyzés. Az ügyfelek nem szükségesek az alapul szolgáló Microsoft belső termék vagy szolgáltatás díjat fizetni. Minden próbaverzió lehetőségeket vannak telepítve az Azure-előfizetéshez. A költségek optimalizálása és a felügyeleti kizárólagos hozzáférése van. Dönthet úgy, egy ingyenes próba- vagy interaktív bemutatót. Függetlenül attól, hogy milyen választja az ingyenes próbaverzió kell biztosítania az ügyfeleknek egy előre megadott időtartamra szeretné kipróbálni az ajánlat, további költségek nélkül.|
|Könnyen konfigurálható, használatra kész megoldás    |  Az alkalmazás könnyen és gyorsan konfigurálását, és állítsa be kell lennie.       |
|Rendelkezésre állás / üzemidő    |    Az SaaS-alkalmazás vagy a platform legalább 99,9 %-os hasznos üzemidővel kell rendelkeznie.     |
|Azure Active Directory     |    Az ajánlat engedélyeznie kell az Azure Active Directory (Azure AD) összevont egyszeri bejelentkezés (SSO) (Azure AD összevont egyszeri bejelentkezés) hozzájárult engedélyezve van.     |

## <a name="test-drive"></a>Tesztverzió

Egy vagy több virtuális gép infrastruktúra--szolgáltatásként (IaaS) vagy a szoftver--szolgáltatásként (SaaS) alkalmazások központi telepítése. Egyik előnye a test drive közzétételi lehetőség az automatikus üzembe helyezése egy virtuális gép vagy a teljes megoldás egy partner által szolgáltatott által vezetett, interaktív bemutatót. Próbálja ki az az ügyfél-értékelési további költségek nélkül biztosít. Az ügyfél nem kell lennie, ahol kapcsolatba léphet az próbaverziója meglévő Azure-ügyfeleket. 

Írjon nekünk az [amp – testdrive](mailto:amp-testdrive@microsoft.com) a kezdéshez. 

|Követelmények  |Részletek |
|---------|---------|
| A Marketplace-alkalmazás   |    Egy vagy több virtuális gép IaaS vagy SaaS-keresztül.      |

## <a name="interactive-demo"></a>Interaktív bemutató

Az ügyfelek, hogy a megoldás az interaktív felhasználói élményt nyújtson az interaktív bemutató használatával. A közzétételi lehetőség interaktív bemutatót előnye, hogy biztosítson egy kipróbálására nyújt lehetőséget a összetett megoldás bonyolult üzembe helyezése nélkül. 

## <a name="virtual-machine-offer"></a>Virtuálisgép-ajánlat

Virtuális berendezés az előfizetéshez társított az ügyfelek központi telepítéséhez használja a virtuális gép ajánlat típusát. Virtuális gépek teljes körűen engedélyezve a használatalapú fizetéses vagy bring-your-saját licenc (használata BYOL) licencelési modell használatával kereskedelmi. A Microsoft kereskedelmi-tranzakciót futtat, és az Ön nevében az ügyfél bankkártyáján. Az ügyfél- és Microsoft, beleértve a nagyvállalati szerződéseket közötti előnyben részesített fizetési kapcsolat használatának előnye kap.

>[!NOTE]
>Jelenleg a társított nagyvállalati szerződés program pénzügyi kötelezettségvállalásainak keretében tudnak a virtuális gép az Azure-használat alapján, de a szoftverlicenc-díjak ellenében nem használható.  

>[!NOTE]
>A felderítési és a virtuális gép üzembe helyezési ügyfelek adott halmazának való korlátozásához a kép közzétételével, és a egy privát ajánlatunk díjszabás érvényesülnek. Saját ajánlatok arra, hogy az exkluzív ajánlatokat létrehozását a legközelebbi ügyfelei számára, és testre szabott szoftver és használati zárolásának feloldásához. A testre szabott használati lehetővé teszi számos forgatókönyv esetében, beleértve a speciális díjszabás és feltételeket, valamint korlátozott kiadású szoftver korai hozzáférést a mező által vezetett üzletek kiemeléséhez. Szóló ajánlatok tenni meghatározott díjszabás vagy termékek korlátozott számú ügyfél engedélyezéséhez hoz létre egy új Termékváltozatban ezeket az adatokat.  
*   Saját ajánlatok kapcsolatos további információkért látogasson el a privát ajánlatok lapon található Azure Marketplace-en [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Követelmény | Részletek |  
|:--- |:--- | 
| Számlázással és méréssel | A virtuális gép vagy BYOL, vagy használatalapú havi számlázási támogatniuk kell. |  
| Azure-kompatibilis virtuális merevlemez (VHD) | Windows vagy Linux rendszerű virtuális gépek kell épül. <ul> <li>Linux rendszerű virtuális Merevlemezek létrehozásával kapcsolatos további információkért lásd: [az Azure által támogatott Linux-disztribúciók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Windows virtuális Merevlemezek létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy Azure-kompatibilis virtuális Merevlemezt](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

>[!Note]
>Cloud Solution Providers (CSP) partner csatorna vehetnek részt már elérhető.  Lásd: [Cloud Solution Providers](./cloud-solution-providers.md) további tájékoztatást a marketing, az ajánlat keretében a Microsoft CSP partner-csatornákon.

## <a name="next-steps"></a>További lépések

Ha ezt még nem tette meg, 

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a Marketplace-en.

Ha regisztrálva van és a egy új ajánlat létrehozása vagy egy meglévő, dolgozik

- [Jelentkezzen be a Cloud Partner portálra](https://cloudpartner.azure.com) létrehozni, vagy végezze el az ajánlatot.
- Lásd: [virtuálisgép-ajánlat](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer) további információt.
