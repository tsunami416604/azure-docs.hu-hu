---
title: Azure szószedet - Azure szótár |} A Microsoft Docs
description: Az Azure szószedet használatával az Azure platform felhőalapú terminológia ismertetése. A rövid Azure szótár biztosít jelentésdefiníciókat közös felhőalapú feltételek az Azure-hoz.
keywords: Azure szótár, felhő-terminológia, Azure szószedetét, terminológiai definíciók, felhőalapú feltételek
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 9a93786759941def4cf8677509b1b2565cac5090
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823858"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>A Microsoft Azure szószedet: Az Azure platform felhőalapú terminológia szótár

A Microsoft Azure szószedet egy rövid szótárban az Azure platform felhőalapú terminológia. Lásd még:

* [A Microsoft Azure és az Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) -definíciók az Azure-szolgáltatások és AWS-megfelelőik.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Felhő-számítástechnikai kifejezések](https://azure.microsoft.com/overview/cloud-computing-dictionary/) – általános szakági felhőalapú kifejezéseket.

## <a name="account"></a>account
Eléréséhez és egy Azure-előfizetés kezeléséhez használt fiók. Azt gyakran nevezik az Azure-fiók bár egy fiók is lehet ezek egyikét sem: egy meglévő munkahelyi, iskolai vagy személyes Microsoft-fiók, vagy egy Office 365 felhasználónevet és jelszót. Azure-előfizetés kezelésére, ha regisztrál a fiókot is létrehozhat a [az ingyenes próbaidőszak](https://azure.microsoft.com).  
Lásd: [egy Azure-előfizetést, az Office 365-fiókjával regisztráljon](billing/billing-use-existing-office-365-account-azure-subscription.md) és [segítségével jelentkezzen be a fiókok](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>API-alkalmazás
Egy másik nevet [App Service-alkalmazás](#app-service-app).

## <a name="app-service-app"></a>App Service-alkalmazás
A számítási erőforrásokat, amelyek [Azure App Service](app-service/overview.md) biztosít egy webhelyre vagy webalkalmazásra alkalmazást, a webes API-t, a üzemeltetési vagy [mobil-háttéralkalmazás](app-service-mobile/app-service-mobile-value-prop.md). App Service-alkalmazások is nevezzük *App Services*, *webes alkalmazások*, *az API apps*, és *mobilalkalmazások*.

## <a name="availability-set"></a>rendelkezésre állási csoport
Virtuális gépek, amelyek felügyelete együtt, adja meg az alkalmazás a redundancia és megbízhatóság gyűjteménye. Rendelkezésre állási csoport használata biztosítja, hogy mindkét tervezett vagy nem tervezett karbantartási események legalább egy virtuális gép elérhető.  
Lásd: [Windows virtuális gépek rendelkezésre állásának kezelése](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [Linux rendszerű virtuális gépek rendelkezésre állásának kezelése](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>Klasszikus Azure üzemi modell
Egy két [üzembe helyezési modellek](resource-manager-deployment-model.md) segítségével helyezheti üzembe az erőforrásokat az Azure-ban (az új modell az Azure Resource Manager). Bizonyos Azure-szolgáltatások csak a Resource Manager üzemi modell támogatja, néhány csak a klasszikus üzemi modell támogatja, és néhány egyaránt támogatja. Az egyes Azure-szolgáltatások dokumentációját Itt adhatja meg, mely támogatja a modellek.

## <a name="cli"></a>Az Azure parancssori felület (CLI)
Parancssori felület, amely Windows, macOS és Linux rendszerű Azure-szolgáltatások kezelésére is használható.  Néhány szolgáltatás és a szolgáltatások csak a PowerShell vagy a parancssori felület használatával kezelhetők. Lásd: [az Azure CLI](/cli/azure)

## <a name="powershell"></a>Azure PowerShell
Azure-szolgáltatások egy parancssor segítségével kezelheti a Windows rendszerű számítógépek a parancssori felület. Néhány szolgáltatás és a szolgáltatások csak a PowerShell vagy a parancssori felület használatával kezelhetők.
Lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview)

## <a name="arm-model"></a>Az Azure Resource Manager üzemi modell
Egy két [üzembe helyezési modellek](resource-manager-deployment-model.md) az (a másik pedig a klasszikus üzemi modellhez) a Microsoft Azure-erőforrások üzembe helyezéséhez használt. Bizonyos Azure-szolgáltatások csak a Resource Manager üzemi modell támogatja, néhány csak a klasszikus üzemi modell támogatja, és néhány egyaránt támogatja. Az egyes Azure-szolgáltatások dokumentációját Itt adhatja meg, mely támogatja a modellek.

## <a name="fault-domain"></a>Tartalék tartomány
A gyűjtemény virtuális gépek rendelkezésre állási csoportban, amely egyszerre esetleg sikertelen lehet. Példa olyan rendelkező gépek állványra közös áramforrással és hálózati kapcsolóval. Az Azure-ban egy rendelkezésre állási csoportban lévő virtuális gépek automatikusan egymástól több tartalék tartomány között.  
Lásd: [Windows virtuális gépek rendelkezésre állásának kezelése](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [Linux rendszerű virtuális gépek rendelkezésre állásának kezelése](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>Georedundáns
Adattárolás általában két vagy több régióban tartalmazó megadott határértékkel. A határok belül vagy országhatáron túl is lehet, és adó rendelet vannak hatással. Minden egyes földrajzi legalább egy régió tartozik. Helyeken példák Ázsia Csendes-óceáni és japán. Más néven *földrajzi*.  
Lásd: [Azure-régiók](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>Georeplikáció
Automatikusan replikálja a tartalmat, például blobok, táblák és üzenetsorok belül egy regionális párokból érdemes folyamata.  
Lásd: [aktív Georeplikáció az Azure SQL Database-hez](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Egy fájl, amely tartalmazza az operációs rendszer és alkalmazás-konfigurációs tetszőleges számú virtuális gépek létrehozásához használható. Az Azure-ban két típusa van lemezképek: Virtuálisgép-rendszerképet és operációsrendszer-lemezkép. Virtuálisgép-rendszerkép tartalmazza az operációs rendszer és a rendszerkép létrehozásakor egy virtuális géphez csatlakoztatott összes lemez. Operációsrendszer-lemezkép csak egy általánosított operációs rendszer, amely nincs adatok lemezkonfigurációk tartalmazza.  
Lásd: [navigálása és kiválasztása Windows virtuálisgép-lemezképek az Azure-ban a PowerShell vagy a parancssori felület](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Korlátok
Erőforrások hozhatók létre, vagy a teljesítmény a teljesítményteszt elérhető száma. Korlátok általában tartoznak előfizetések, szolgáltatások és a is.  
Lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>terheléselosztó
Egy erőforrás, amely elosztja a bejövő forgalmat a hálózaton lévő számítógépek között. Az Azure-ban a terheléselosztó elosztja a forgalmat egy terheléselosztó készlet meghatározott virtuális gépek között. A [terheléselosztó](load-balancer/load-balancer-overview.md) lehet internetkapcsolattal rendelkező vagy belső is lehet.  

## <a name="mobile-app"></a>mobilalkalmazás
Egy másik nevet [App Service-alkalmazás](#app-service-app).

## <a name="offer"></a>az ajánlat
A díjszabás, kreditek és érvényes Azure-előfizetéshez kapcsolódó feltételeket.  
Tekintse meg a [Azure-ajánlat részletei lap](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
A biztonságos webes portál üzembe helyezése és kezelése az Azure-szolgáltatások segítségével.

## <a name="region"></a>régió
Egy adott területre, amely nem közötti nemzeti határok, és a egy vagy több adatközpont tartalmaz egy-egy térségen belül. Díjszabás, regionális szolgáltatások és ajánlattípusokról régió szinten érhetők el. A régiót általában párban áll egy másik régióban, amely lehet akár több száz mérföld távolságban. Regionális párokról a magas rendelkezésre állású és vész-helyreállítási mechanizmusként használható. Más néven *hely*.  
Lásd: [Azure-régiók](best-practices-availability-paired-regions.md)

## <a name="resource"></a>erőforrás
Egy elem, amely az Azure-megoldás része. Minden egyes Azure-szolgáltatás lehetővé teszi a különböző típusú erőforrások, például adatbázisok és virtuális gépek üzembe helyezését.   
Lásd: [Azure Resource Manager áttekintése](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>erőforráscsoport
A tároló a Resource Managerben, amely alkalmazásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja az alkalmazás erőforrását, vagy csak azokat az erőforrásokat, amelyek logikailag mindegyikét. Eldöntheti, hogyan kívánja kiosztani az erőforrásokat az erőforráscsoportokba a szervezetének legmegfelelőbb módon.  
Lásd: [Azure Resource Manager áttekintése](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>Resource Manager-sablon
Egy JSON-fájlt, amely deklaratív meghatározza egy vagy több Azure-erőforrások és, amely meghatározza, hogy a telepített erőforrások közti függőségeket. A sablon erőforrások konzisztens és ismétlődő telepítésére használandó.  
Lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>Erőforrás-szolgáltató
Egy szolgáltatás, amely az erőforrásokat, akkor telepítheti és kezelheti a Resource Manageren keresztül. Mindegyik erőforrás-szolgáltató műveleteket biztosít a telepített erőforrásokkal folytatott munkához. Erőforrás-szolgáltatók az Azure Portalon, az Azure PowerShell és a számos programozási SDK-k érhetők el.  
Lásd: [Azure Resource Manager áttekintése](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>szerepkör
Hanem a hozzáférés-felhasználók, csoportok és szolgáltatásokat kell hozzárendelni. Ezek tudni elvégezni a műveleteket, mint például létrehozása, kezelése, és olvassa el az Azure-erőforrás.  
Lásd: [RBAC: Beépített szerepkörök](role-based-access-control/built-in-roles.md)

## <a name="sla"></a>szolgáltatói szerződés (SLA)
A szerződést, amely a Microsoft elkötelezettségét a üzemidejével és elérhetőségével. Minden Azure szolgáltatásnak van egy adott SLA-t.  
Lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>közös hozzáférésű jogosultságkód (SAS)
Aláírás, amely lehetővé teszi, meg kell adnia egy erőforrást, korlátozott hozzáférésű anélkül, hogy a fiókkulcs. Ha például [Azure Storage használ az SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) ügyfél hozzáférést, mint a blobok objektumok. [IoT Hub ezekkel az SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) eszközök telemetriát küldjön engedélyt adni.

## <a name="storage-account"></a>tárfiók
Egy fiókot, amely lehetővé teszi az Azure Blob, üzenetsor, tábla és fájl az Azure Storage szolgáltatásaihoz való hozzáférést. A tárfiók nevét határozza meg az egyedi névteret Azure Storage-adatobjektumaihoz.  
Lásd: [tudnivalók az Azure storage-fiókok](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>előfizetést
Az ügyfél szerződést a Microsofttal, amely lehetővé teszi, hogy szerezze be az Azure-szolgáltatásokat. Az ajánlatot az előfizetéshez kiválasztott előfizetési díjak és kapcsolódó feltételek vonatkoznak rájuk.
Lásd: [Microsoft Online előfizetői szerződés](https://azure.microsoft.com/support/legal/subscription-agreement/) és [kapcsolódnak az Azure-előfizetések az Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>címke
Az indexelő kifejezés, amely lehetővé teszi, hogy a felügyeleti vagy számlázási követelményeinek megfelelően kategorizálhatja az erőforrásokat. Összetett erőforrások gyűjteménye, ha a címkék használatával vizuálisan megjeleníteni az eszközöket, amelyek a legésszerűbb módon. Például elláthat címkével olyan erőforrásokat, amelyek hasonló szerepet töltenek be a szervezetben, vagy ugyanahhoz a részleghez tartoznak.  
Lásd: [az Azure-erőforrások rendszerezése címkék használatával](resource-group-using-tags.md)

## <a name="update-domain"></a>Frissítési tartomány
A virtuális gépek rendelkezésre állási csoportban egyszerre frissítendő gyűjtemény. Az azonos frissítési tartományban található virtuális gépek rendszer egyszerre indítja újra a tervezett karbantartás során. Azure egyszerre nem indul újra egynél több frissítési tartományt. Más néven a frissítési tartomány.  
Lásd: [Windows virtuális gépek rendelkezésre állásának kezelése](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [Linux rendszerű virtuális gépek rendelkezésre állásának kezelése](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>Virtuális gép
A szoftver megvalósítását egy operációs rendszert futtató fizikai számítógépre. Több virtuális gép futtatható egyszerre ugyanazt a hardvert. Az Azure-ban a virtuális gépek különböző méretű érhető el.  
Lásd: [Virtual Machines – dokumentáció](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>Virtuálisgép-bővítmény
Egy erőforrás, amely megvalósítja a viselkedés vagy szolgáltatásokat vagy más programok működik, vagy arra, hogy az, hogy a futó számítógép biztosítson. Használhatja például a Virtuálisgép-hozzáférési bővítmény alaphelyzetbe állítása, vagy módosítsa a távelérési értékeket egy Azure virtuális gépen.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Lásd: [virtuális gépi bővítmények és szolgáltatások (Windows) kapcsolatos](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [virtuális gépi bővítmények és szolgáltatások (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>Virtuális hálózat
Az Azure-erőforrások, amelyek el van különítve a többi Azure bérlő közötti kapcsolatot biztosít a hálózat. Egy [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) lehetővé teszi a virtuális hálózat és a egy helyszíni hálózat és a virtuális hálózatok közötti kapcsolatokat hozhat létre. Teljes körűen szabályozhatja az IP-címblokkok, a DNS-beállítások, a biztonsági szabályzatok és a virtuális hálózaton belüli útválasztási táblázatokat.  
Lásd: [Virtual Network áttekintése](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Webalkalmazás
Egy másik nevet [App Service-alkalmazás](#app-service-app).

## <a name="see-also"></a>Lásd még

* [Bevezetés az Azure használatába](https://azure.microsoft.com/get-started/)
* [Felhőerőforrás-központ](https://azure.microsoft.com/resources/)  
* [Az Azure az üzleti alkalmazás](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [A helyi adatközpontban Azure](https://azure.microsoft.com/overview/business-apps-on-azure/)

