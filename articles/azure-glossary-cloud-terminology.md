---
title: Azure szószedet - Azure szótár | Microsoft dokumentumok
description: Az Azure szószedet segítségével megismerheti az Azure platform felhőbeli terminológiáját. Ez a rövid Azure-szótár definíciókat biztosít az Azure közös felhőfeltételeihez.
keywords: Azure szótár, felhőalapú terminológia, Azure szószedet, terminológiai definíciók, felhőalapú kifejezések
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
ms.openlocfilehash: 7c80ba6b15cec08da9bcefea243493a824ef66bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276477"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure szószedet: Felhőterminológia szótára az Azure platformon

A Microsoft Azure szószedet egy rövid szótár felhőterminológia az Azure platform. Lásd még:

* [Microsoft Azure és Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) – Az Azure-szolgáltatások és a ws-megfelelőik definíciói.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Felhőalapú számítástechnikai kifejezések](https://azure.microsoft.com/overview/cloud-computing-dictionary/) – Általános iparági felhőkifejezések.

## <a name="account"></a>account
Egy Azure-előfizetés eléréséhez és kezeléséhez használt fiók. Gyakran nevezik Azure-fióknak, bár egy fiók a következők bármelyike lehet: meglévő munkahelyi, iskolai vagy személyes Microsoft-fiók, vagy Office 365-felhasználónév és -jelszó. Az [ingyenes próbaverzióra](https://azure.microsoft.com)való feliratkozáskor azure-előfizetés kezeléséhez fiókot is létrehozhat.  
Lásd: [Feliratkozás Egy Azure-előfizetésre office 365-fiókjával](cost-management-billing/manage/office-365-account-for-azure-subscription.md) és [a bejelentkezéshez használható Fiókokkal.](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="api-app"></a>API-alkalmazás
Az [App Service-alkalmazás](#app-service-app)másik neve .

## <a name="app-service-app"></a>App Service-alkalmazás
Az [Azure App Service](app-service/overview.md) által egy webhely vagy webalkalmazás, webes API vagy [mobilalkalmazás-háttérszolgáltatás](app-service-mobile/app-service-mobile-value-prop.md)üzemeltetéséhez biztosított számítási erőforrások. Az App Service-alkalmazásokat *App Services,* *webalkalmazások,* *API-alkalmazások*és *mobilalkalmazások*is nevezik.

## <a name="availability-set"></a>rendelkezésre állási készlet
Virtuális gépek gyűjteménye, amelyek együttesen biztosítják az alkalmazások redundancia és megbízhatóság. Egy rendelkezésre állási csoport használata biztosítja, hogy egy tervezett vagy nem tervezett karbantartási esemény során legalább egy virtuális gép érhető el.  
Lásd: [A Windows virtuális gépek elérhetőségének kezelése](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és a [Linux-virtuális gépek elérhetőségének kezelése](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Klasszikus Azure-telepítési modell
Az Azure-beli erőforrások üzembe helyezéséhez használt két [üzembe helyezési modell](resource-manager-deployment-model.md) egyike (az új modell az Azure Resource Manager). Egyes Azure-szolgáltatások csak a Resource Manager telepítési modellt támogatják, néhány csak a klasszikus üzembe helyezési modellt, és néhány mindkettőt támogatja. Az egyes Azure-szolgáltatások dokumentációja határozza meg, hogy melyik modell(ek) támogatják.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Azure parancssori felület (CLI)
Az Azure-szolgáltatások Windows, macOS és Linux rendszerről történő kezelésére használható parancssori felület.  Egyes szolgáltatások vagy szolgáltatásfunkciók csak a PowerShellen vagy a CLI-n keresztül kezelhetők. Lásd: [Azure CLI](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Parancssori felület az Azure-szolgáltatások Windows-számítógépekről származó parancssorból történő kezeléséhez. Egyes szolgáltatások vagy szolgáltatásfunkciók csak a PowerShellen vagy a CLI-n keresztül kezelhetők.
Lásd: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Az Azure Resource Manager telepítési modellje
A Microsoft Azure-ban az erőforrások üzembe helyezéséhez használt két [üzembe helyezési modell](resource-manager-deployment-model.md) egyike (a másik a klasszikus üzembe helyezési modell). Egyes Azure-szolgáltatások csak a Resource Manager telepítési modellt támogatják, néhány csak a klasszikus üzembe helyezési modellt, és néhány mindkettőt támogatja. Az egyes Azure-szolgáltatások dokumentációja határozza meg, hogy melyik modell(ek) támogatják.

## <a name="fault-domain"></a>tartalék tartomány
A virtuális gépek gyűjteménye egy rendelkezésre állási csoportban, amely esetleg sikertelen ugyanabban az időben. Ilyen például egy állványon lévő gépek csoportja, amelyek közös áramforrással és hálózati kapcsolóval rendelkeznek. Az Azure-ban a rendelkezésre állási csoportban lévő virtuális gépek automatikusan elválnak több tartalék tartomány között.  
Lásd: [A Windows virtuális gépek elérhetőségének kezelése](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy a [Linux-virtuális gépek elérhetőségének kezelése](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>Geo
Az adatrezidensi terület meghatározott határa, amely általában két vagy több régiót tartalmaz. A határok a nemzeti határokon belül vagy azokon kívül is lehetnek, és azokat az adószabályozás befolyásolja. Minden geo rendelkezik legalább egy régióval. A geos például ázsia csendes-óceáni és japán. Más néven *földrajz*.  
Tekintse meg [az Azure-régiókat](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>georeplikáció
A folyamat automatikusan replikálja a tartalom, például a blobok, táblák és várólisták egy regionális páron belül.  
Lásd: [Aktív georeplikáció az Azure SQL-adatbázishoz](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Olyan fájl, amely tetszőleges számú virtuális gép létrehozására használható operációs rendszert és alkalmazáskonfigurációt tartalmazza. Az Azure-ban kétféle rendszerképek: Virtuálisgép-lemezkép és operációsrendszer-lemezkép. A virtuális gép lemezképe tartalmazza az operációs rendszert és a virtuális géphez csatlakoztatott összes lemezt a lemezkép létrehozásakor. Az operációsrendszer-lemezkép csak egy általános operációs rendszert tartalmaz, amelynek nincsenek adatlemez-konfigurációi.  
Lásd: [Navigálás és windowsos virtuálisgép-lemezképek kiválasztása az Azure-ban a PowerShell vagy a CLI segítségével](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Határok
A létrehozható erőforrások száma vagy az elérhető teljesítmény-teljesítmény-referenciaérték. A korlátok általában előfizetésekhez, szolgáltatásokhoz és ajánlatokhoz vannak társítva.  
Tekintse meg [az Azure előfizetési és szolgáltatáskorlátait, kvótáit és korlátozásait](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>terheléselosztó
Olyan erőforrás, amely elosztja a bejövő forgalmat a hálózat számítógépei között. Az Azure-ban egy terheléselosztó osztja el a forgalmat a terheléselosztó készletben definiált virtuális gépek között. A [terheléselosztó](load-balancer/load-balancer-overview.md) lehet internetre néző, vagy lehet belső.  

## <a name="mobile-app"></a>mobilalkalmazás
Az [App Service App alkalmazás](#app-service-app)másik neve.

## <a name="offer"></a>offer
Az Azure-előfizetésre vonatkozó díjszabás, kreditek és kapcsolódó feltételek.  
Tekintse meg az [Azure ajánlat részletei lapot](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portál
Az Azure-szolgáltatások üzembe helyezéséhez és kezeléséhez használt biztonságos webportál.

## <a name="region"></a>régió
Olyan terület, amely nem lépi át a nemzeti határokat, és egy vagy több adatközpontot tartalmaz. Az árak, a regionális szolgáltatások és az ajánlattípusok régiószinten vannak elérhetővé téve. A régió általában párosítva van egy másik régióval, amely akár több száz mérföldre is lehet. A regionális párok vész-helyreállítási és magas rendelkezésre állási forgatókönyvek mechanizmusaként használhatók. Más néven *hely*.  
Tekintse meg [az Azure-régiókat](best-practices-availability-paired-regions.md)

## <a name="resource"></a>Erőforrás
Egy elem, amely része az Azure-megoldás. Minden Egyes Azure-szolgáltatás lehetővé teszi, hogy különböző típusú erőforrásokat, például adatbázisokat vagy virtuális gépeket telepítsen.   
Tekintse meg az [Azure Resource Manager áttekintését](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>erőforráscsoport
Az Erőforrás-kezelő ben lévő olyan tároló, amely egy alkalmazás kapcsolódó erőforrásait tárolja. Az erőforráscsoport tartalmazhatja az alkalmazás összes erőforrását, vagy csak azokat az erőforrásokat, amelyek logikailag csoportosítva vannak. Eldöntheti, hogyan kívánja kiosztani az erőforrásokat az erőforráscsoportokba a szervezetének legmegfelelőbb módon.  
Tekintse meg az [Azure Resource Manager áttekintését](azure-resource-manager/management/overview.md)

## <a name="resource-manager-template"></a><a name="arm-template"></a>Erőforrás-kezelő sablon
Egy JSON-fájl, amely deklaratív módon definiál egy vagy több Azure-erőforrást, és amely meghatározza a telepített erőforrások közötti függőségeket. A sablon erőforrások konzisztens és ismétlődő telepítésére használandó.  
Lásd: [Az Azure Resource Manager-sablonok szerzői ről](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>erőforrás-szolgáltató
Olyan szolgáltatás, amely az Erőforrás-kezelőn keresztül telepíthető és kezelhető erőforrásokat biztosítja. Mindegyik erőforrás-szolgáltató műveleteket biztosít a telepített erőforrásokkal folytatott munkához. Az erőforrás-szolgáltatók az Azure Portalon, az Azure PowerShellen és számos programozási SDK-n keresztül érhetők el.  
Tekintse meg az [Azure Resource Manager áttekintését](azure-resource-manager/management/overview.md)

## <a name="role"></a>Szerepet
A felhasználókhoz, csoportokhoz és szolgáltatásokhoz rendelhető hozzáférés szabályozására szolgáló eszköz. A szerepkörök olyan műveleteket hajthatnak végre, mint például az Azure-erőforrások létrehozása, kezelése és olvasása.  
Lásd: [RBAC: Beépített szerepkörök](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>szolgáltatásiszint-szerződés (SLA)
A microsoftos uptime és connectivity kötelezettségvállalásait leíró szerződés. Minden Azure-szolgáltatás rendelkezik egy adott SLA.Each Azure service has a specific SLA.  
Lásd: [Szolgáltatásiszint-szerződések](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>megosztott hozzáférésű aláírás (SAS)
Olyan aláírás, amely lehetővé teszi, hogy korlátozott hozzáférést biztosítson egy erőforráshoz anélkül, hogy felfedi a fiókkulcsot. Az Azure Storage például [sas-t használ](storage/common/storage-dotnet-shared-access-signature-part-1.md) az olyan objektumokhoz való hozzáférés biztosításához, mint például a blobok. [Az IoT Hub a SAS segítségével](iot-hub/iot-hub-devguide-security.md#security-tokens) adja meg az eszközök nek a telemetriai adatok küldésére vonatkozó engedélyt.

## <a name="storage-account"></a>tárfiók
Egy fiók, amely hozzáférést biztosít az Azure Blob, Várólista, Tábla és fájl szolgáltatások az Azure Storage-ban. A tárfiók neve határozza meg az Azure Storage-adatobjektumok egyedi névterét.  
Lásd: [Azure storage-fiókok –](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>előfizetést
Az ügyfél szerződése a Microsofttal, amely lehetővé teszi számukra az Azure-szolgáltatások beszerzését. Az előfizetési díjszabást és a kapcsolódó feltételeket az előfizetéshez kiválasztott ajánlat szabályozza.
Lásd: [Microsoft online előfizetési szerződés](https://azure.microsoft.com/support/legal/subscription-agreement/) és hogyan [kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>címke
Indexelési kifejezés, amely lehetővé teszi az erőforrások kategorizálását a kezelés vagy a számlázás követelményeinek megfelelően. Ha az erőforrások összetett gyűjteménye van, címkék segítségével a legésszerűbb módon jelenítheti meg ezeket az eszközöket. Például elláthat címkével olyan erőforrásokat, amelyek hasonló szerepet töltenek be a szervezetben, vagy ugyanahhoz a részleghez tartoznak.  
Lásd: [Címkék használata az Azure-erőforrások rendszerezéséhez](resource-group-using-tags.md)

## <a name="update-domain"></a>tartomány frissítése
A virtuális gépek gyűjteménye egy rendelkezésre állási csoportban, amelyek egyidejűleg frissülnek. Az ugyanazon frissítési tartományban lévő virtuális gépek a tervezett karbantartás során együtt újraindulnak. Az Azure soha nem indít újra egyszerre egynél több frissítési tartományt. Frissítési tartománynak is nevezik.  
Lásd: [A Windows virtuális gépek elérhetőségének kezelése](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és a [Linux-virtuális gépek elérhetőségének kezelése](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-machine"></a><a name="vm"></a>virtuális gép
Az operációs rendszert futtató fizikai számítógép szoftverimplementációja. Egyszerre több virtuális gép is futtatható ugyanazon a hardveren. Az Azure-ban a virtuális gépek különböző méretekben érhetők el.  
Lásd: [Virtuális gépek dokumentációja](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>virtuális gép bővítménye
Olyan erőforrás, amely olyan viselkedéseket vagy szolgáltatásokat valósít meg, amelyek vagy segítik a többi program működését, vagy lehetővé teszik a futó számítógépekkel való interakciót. Például a Virtuálisgép Access-bővítmény segítségével alaphelyzetbe állíthatja vagy módosíthatja a távelérési értékeket egy Azure virtuális gépen.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Lásd: [Virtuálisgép-bővítmények és -szolgáltatások (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [A virtuálisgép-bővítmények és -szolgáltatások –(Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network"></a><a name="vnet"></a>virtuális hálózat
Olyan hálózat, amely kapcsolatot biztosít az Azure-erőforrások között, amely elkülönül az összes többi Azure-bérlőtől. Az [Azure VPN-átjáró](vpn-gateway/vpn-gateway-about-vpngateways.md) lehetővé teszi a virtuális hálózatok, valamint a virtuális hálózat és a helyszíni hálózat közötti kapcsolatok létesítését. Teljes mértékben szabályozhatja az IP-címblokkokat, a DNS-beállításokat, a biztonsági házirendeket és az útvonaltáblákat a virtuális hálózaton belül.  
Lásd: [Virtuális hálózat – áttekintés](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Webalkalmazás
Az [App Service App alkalmazás](#app-service-app)másik neve.

## <a name="see-also"></a>Lásd még

* [Bevezetés az Azure használatába](https://azure.microsoft.com/get-started/)
* [Felhőalapú erőforrásközpont](https://azure.microsoft.com/resources/)  
* [Azure vállalati alkalmazáshoz](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Az Azure az adatközpontban](https://azure.microsoft.com/overview/business-apps-on-azure/)

