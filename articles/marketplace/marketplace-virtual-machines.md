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
ms.openlocfilehash: 5508b5943e116545297d91e85621d2a11a635299
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059766"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Virtuálisgép-ajánlat közzétételi útmutató

Virtuálisgép-lemezképek tartoznak a fő módon lehet közzéteszi a megoldást az Azure piactéren. Ez az útmutató segítségével áttekinteni a követelményeket, ezt az ajánlatot. 

Ezek a tranzakció ajánlatok, amelyek üzembe helyezve, és a Marketplace-en keresztül számlázott. Művelet, amely a felhasználó kap, a hívást a "Letöltés most."

## <a name="free-trial"></a>Free Trial (Ingyenes próba) 

Beállíthatja, hogy a felhasználók számára, hogy tesztelje az ajánlat korlátozott kifejezés szoftverlicencek elérésével, amikor a Bring Your saját licences (BYOL) blling modellel. Az alábbiakban a követelményeknek, ez az ajánlat üzembe helyezéséhez. 

|Követelmények  |Részletek  |
|---------|---------|
|Ingyenes próbaidőszak és próbaverziója     |   Az ügyfelek ingyenes korlátozott ideig előfordulhat, hogy kipróbálják az alkalmazását. Az ügyfél nem szükséges licencek vagy előfizetések díjakat fizetni az ajánlatot. Az ügyfelek nem szükségesek az alapul szolgáló Microsoft belső termék vagy szolgáltatás díjat fizetni. Minden próbaverzió lehetőségeket vannak telepítve az Azure-előfizetéshez. A költségek optimalizálása és a felügyeleti kizárólagos hozzáférése van. Dönthet úgy, egy ingyenes próba- vagy interaktív bemutatót. Függetlenül attól, hogy milyen választja az ingyenes próbaverzió kell megadnia az ügyfél előre megadott, és próbálja meg az ajánlat, további költségek nélkül.|
|Könnyen konfigurálható, használatra kész megoldás    |  Az alkalmazás könnyen és gyorsan konfigurálását, és állítsa be kell lennie.       |
|Rendelkezésre állás / üzemidő    |    Az SaaS-alkalmazás vagy a platform legalább 99,9 %-os hasznos üzemidővel kell rendelkeznie.     |
|Azure Active Directory     |    Az ajánlat engedélyeznie kell az Azure Active Directory (Azure AD) összevont egyszeri bejelentkezés (SSO) (Azure AD összevont egyszeri bejelentkezés) hozzájárult engedélyezve van.     |

## <a name="test-drive"></a>Kipróbálás

Egy vagy több virtuális gép infrastructure-as-a-service(IaaS) vagy SaaS-alkalmazások központi telepítése. Egyik előnye a test drive közzétételi lehetőség az automatikus üzembe helyezése egy virtuális gép vagy a teljes megoldás egy partner által szolgáltatott által vezetett, interaktív bemutatót. Próbálja ki az az ügyfél-értékelési további költségek nélkül biztosít. Az ügyfél nem kell lennie, ahol kapcsolatba léphet az próbaverziója meglévő Azure-ügyfeleket. 

E-mailek amp-testdrive@microsoft.com a kezdéshez. 

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
| Azure-kompatibilis virtuális merevlemez (VHD) | Windows vagy Linux rendszerű virtuális gépek kell épül.<ul> <li>Linux rendszerű virtuális Merevlemezek létrehozásával kapcsolatos további információkért látogasson el az Azure-kompatibilis VHD-t (Linux-alapú) szakaszban található létrehozása [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Windows virtuális Merevlemezek létrehozásával kapcsolatos további információkért látogasson el az Azure-kompatibilis VHD-t (Windows-alapú) szakaszban található létrehozása [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-Based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>További lépések

Ha ezt még nem tette meg, 

- [Regisztráljon](https://azuremarketplace.microsoft.com/sell) a Marketplace-en

Ha regisztrálva van és a egy új ajánlat létrehozása vagy egy meglévő, dolgozik

- [Jelentkezzen be a Cloud Partner portálra](https://cloudpartner.azure.com) létrehozni, vagy végezze el az ajánlatot
