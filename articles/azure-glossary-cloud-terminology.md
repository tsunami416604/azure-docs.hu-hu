---
title: Az Azure szószedet - Azure szótár |} Microsoft Docs
description: Az Azure szószedet használatával megérthetik, hogy a felhő terminológia az Azure platformon. A rövid Azure szótár biztosít jelentésdefiníciókat közös felhő feltételek az Azure-bA.
keywords: Azure szótár, felhőalapú terminológia, Azure szószedet, terminológiai definíciók, felhőalapú feltételek
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
ms.openlocfilehash: 84766ba4cf9e844184752bc44d2e0a471b97db27
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159128"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>A Microsoft Azure szószedet: az Azure platformon felhő terminológia dictionary

A Microsoft Azure szószedete felhő terminológia az Azure platformra rövid dictionary. Lásd még:

* [A Microsoft Azure és az Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) -definíciók az Azure-szolgáltatások és AWS másolataik.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [A felhő számítási feltételek](https://azure.microsoft.com/overview/cloud-computing-dictionary/) -általános iparági felhő feltételeket.

## <a name="account"></a>account
Eléréséhez és az Azure-előfizetés kezeléséhez használt fiókot. Ez gyakran nevezzük az Azure-fiók bár egy fiók is lehet ezek egyikét sem: egy meglévő munkahelyi, iskolai, vagy személyes Microsoft-fiók, vagy egy Office 365 felhasználói nevét és jelszavát. Azure-előfizetés kezeléséhez, ha regisztrál a fiókot is létrehozhat a [ingyenes próbaverzió](https://azure.microsoft.com).  
Lásd: [Azure-előfizetéssel az Office 365-fiókkal regisztrálhat](billing/billing-use-existing-office-365-account-azure-subscription.md) és [fiók használatával jelentkezzen be a](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>API-alkalmazás
Egy másik nevet [App Service alkalmazás](#app-service-app).

## <a name="app-service-app"></a>App Service-alkalmazás
A számítási erőforrásokat, amelyek [Azure App Service](app-service/app-service-web-overview.md) biztosít egy webhelyre vagy webalkalmazásra alkalmazást, a webes API-t vagy [mobil-háttéralkalmazás](app-service-mobile/app-service-mobile-value-prop.md). App Service-alkalmazásokhoz is nevezzük *alkalmazásszolgáltatások*, *webalkalmazások*, *API-alkalmazások*, és *mobilalkalmazások*.

## <a name="availability-set"></a>A rendelkezésre állási csoport
Virtuális gépek felügyelete együtt, hogy alkalmazás redundanciát és a megbízhatóság gyűjteménye. A rendelkezésre állási csoportok használata biztosítja, hogy tervezett vagy nem tervezett karbantartási esemény során legalább egy virtuális gép elérhető.  
Lásd: [Windows virtuális gépek rendelkezésre állásának kezelése](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [Linux virtuális gépek rendelkezésre állásának kezelése](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>Az Azure klasszikus telepítési modell
Egy két [üzembe helyezési modellel](resource-manager-deployment-model.md) erőforrások az Azure (az új modell az Azure Resource Manager) központi telepítéséhez. Egyes Azure-szolgáltatásokhoz csak a Resource Manager üzembe helyezési modellel támogatja, néhány csak a klasszikus üzembe helyezési modellt támogatja, és néhány támogatja. Minden Azure szolgáltatás dokumentációjában határozza meg, mely a modellek támogatják-e.

## <a name="cli"></a>Azure parancssori felület (CLI)
MacOS, Linux és a Windows Azure-szolgáltatások kezeléséhez használható parancssori felület.  Egyes szolgáltatásokat vagy a szolgáltatások csak a PowerShell vagy a parancssori felületen keresztül kezelheti. Lásd: [Azure CLI 2.0](/cli/azure)

## <a name="powershell"></a>Azure PowerShell
Egy parancssori felületet Azure szolgáltatást a parancssor használatával kezelheti a Windows rendszerű számítógépeken. Egyes szolgáltatásokat vagy a szolgáltatások csak a PowerShell vagy a parancssori felületen keresztül kezelheti.
Lásd: [hogyan Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)

## <a name="arm-model"></a>Az Azure Resource Manager telepítési modell
Egy két [üzembe helyezési modellel](resource-manager-deployment-model.md) központi telepítéséhez a Microsoft Azure (a másik pedig a klasszikus üzembe helyezési modellel) erőforrásait. Egyes Azure-szolgáltatásokhoz csak a Resource Manager üzembe helyezési modellel támogatja, néhány csak a klasszikus üzembe helyezési modellt támogatja, és néhány támogatja. Minden Azure szolgáltatás dokumentációjában határozza meg, mely a modellek támogatják-e.

## <a name="fault-domain"></a>Hibatartomány
A gyűjteményben található virtuális gépek rendelkezésre állási csoportok, amelyek valószínűleg egyszerre meghiúsulhat. Példa: gépcsoport szekrényben, amelyek egy közös power forrás- és a hálózati kapcsolóhoz. Az Azure a virtuális gépek rendelkezésre állási csoportba automatikusan egymástól több tartalék tartományokban.  
Lásd: [Windows virtuális gépek rendelkezésre állásának kezelése](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [Linux virtuális gépek rendelkezésre állásának kezelése](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>földrajzi
A két vagy több régió általában tartalmazó adatok rezidens meghatározott határ. A határok belül vagy kívül nemzeti szegélyek, és adó szabályozás befolyásolják. Minden földrajzi legalább egy régió tartozik. Geos példák Ázsia Csendes-óceáni és japán. Más néven *geográfiai*.  
Lásd: [Azure-régiók](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>a georeplikáció
A folyamat automatikusan replikálása tartalmának, például a blobot, táblát és üzenetsort, egy regionális pár belül.  
Lásd: [aktív Georeplikáció Azure SQL-adatbázis](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>Kép
A fájl, amely tartalmazza az operációs rendszer és az alkalmazás konfigurációját, tetszőleges számú virtuális gépek létrehozásához használható. Az Azure-ban lemezképek két típusa van: virtuális gép lemezképét és az operációsrendszer-lemezképek. Egy Virtuálisgép-lemezkép az operációs rendszer és a lemezkép létrehozásakor a virtuális géphez csatolt összes lemezt tartalmaz. Az operációsrendszer-lemezképek operációs rendszer nélküli adatok lemezkonfigurációkkal valóüzemeltetése csak egy általánosított tartalmazza.  
Lásd: [keresse meg és jelölje be a Windows virtuálisgép-rendszerképek a PowerShell vagy a CLI Azure-ban](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Korlátok
Erőforrások hozható létre vagy a teljesítmény teljesítményteszt elérhető száma. Korlátok általában társított előfizetéseket, szolgáltatások és ajánlatokat.  
Lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>Terheléselosztó
Ez az erőforrás osztja el a bejövő forgalmat a hálózaton lévő számítógépek között. Az Azure terheléselosztó osztja el a forgalmat a virtuális gépek definiált egy terheléselosztó készlet. A [terheléselosztó](load-balancer/load-balancer-overview.md) internetre is lehetnek, vagy belső lehet.  

## <a name="mobile-app"></a>mobilalkalmazás
Egy másik nevet [App Service-alkalmazást](#app-service-app).

## <a name="offer"></a>ajánlat
A díjszabás kreditek és az Azure-előfizetésre vonatkozó kapcsolódó fogalmak.  
Tekintse meg a [Azure-ajánlat a Részletek lap](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
A biztonságos webes portál telepítése és kezelése az Azure-szolgáltatásokhoz használt.

## <a name="region"></a>régió
Olyan, amely közötti nemzeti szegélyek, és egy vagy több adatközpontok tartalmaz egy földrajzi területen. Árképzési regionális szolgáltatások és típusú ki vannak téve a régió szinten. A régió általában párosítani egy másik régióban, amely lehet akár több száz miles számítógépnél. Regionális párok alkalmas mechanizmusaként vész-helyreállítási és magas rendelkezésre állás elérésére. Más néven *hely*.  
Lásd: [Azure-régiók](best-practices-availability-paired-regions.md)

## <a name="resource"></a>erőforrás
Az Azure-megoldás részét képező cikk. Minden Azure szolgáltatás lehetővé teszi különböző típusú erőforrások, például az adatbázisok és virtuális gépek telepítését.   
Lásd: [Azure Resource Manager áttekintése](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>erőforráscsoport
A tároló az erőforrás-kezelőben, amely az alkalmazáshoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatnak valamennyi az erőforrásokat az alkalmazáshoz, és csak azokat az erőforrásokat, amelyek logikailag egy csoportba tartoznak. Eldöntheti, hogyan kívánja kiosztani az erőforrásokat az erőforráscsoportokba a szervezetének legmegfelelőbb módon.  
Lásd: [Azure Resource Manager áttekintése](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>Resource Manager-sablon
A JSON-fájl, amely egy vagy több Azure-erőforrások deklaratív módon definiálja, és, amely meghatározza, hogy a telepített erőforrások közti függőségeket. A sablon erőforrások konzisztens és ismétlődő telepítésére használandó.  
Lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>erőforrás-szolgáltató
Az erőforrások, telepítheti és kezelheti a Resource Manageren keresztül biztosító szolgáltatás. Mindegyik erőforrás-szolgáltató műveleteket biztosít a telepített erőforrásokkal folytatott munkához. Erőforrás-szolgáltató az Azure portál, Azure PowerShell és több programozási SDK is elérhetőek.  
Lásd: [Azure Resource Manager áttekintése](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>szerepkör
Eszköz hozzárendelheti a felhasználók, csoportok és a szolgáltatások hozzáférés szabályozása. Szerepköröket használják, mint például létrehozása, kezelése, és az Azure-erőforrások olvasási műveleteket végezhet.  
Lásd: [RBAC: beépített szerepkörök](role-based-access-control/built-in-roles.md)

## <a name="sla"></a>Szolgáltatásiszint-szerződéssel (SLA)
A szerződést, amely leírja a Microsoft felé vállalt kötelezettségeinket hasznos üzemidő és kapcsolatot. Minden Azure szolgáltatásnak van egy adott SLA-t.  
Lásd: [szolgáltatási szintek](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>Közös hozzáférésű jogosultságkód (SAS)
Olyan aláírása, amely lehetővé teszi a hozzáférést csak egy erőforráshoz, anélkül, hogy a fiókkulcs. Például [Azure Storage használt SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) objektumok, például a blobok ügyfél hozzáférést. [Az IoT-központ által használt SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) engedélyt szeretne megadni eszközök telemetriai adatokat küldhet.

## <a name="storage-account"></a>tárfiók
Amely lehetővé teszi az Azure Storage Azure Blob, a várólista, a tábla és a fájl szolgáltatások elérését. A tárfiók nevét határozza meg az egyedi névteret Azure Storage-adatobjektumok.  
Lásd: [tudnivalók az Azure storage-fiókok](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>előfizetést
Az ügyfél Microsofttal, amely lehetővé teszi őket Azure-szolgáltatásokhoz. Az előfizetés árak és a kapcsolódó fogalmak az előfizetés a kiválasztott ajánlat szabályozzák.
Lásd: [Microsoft Online előfizetői szerződés](https://azure.microsoft.com/support/legal/subscription-agreement/) és [kapcsolódnak hogyan Azure-előfizetések az Azure Active Directoryval](active-directory/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>címke
Az indexelő kifejezés, amely lehetővé teszi, hogy a felügyeleti vagy számlázási követelményeinek megfelelően kategorizálhatja az erőforrásokat. Ha rendelkezik olyan összetett erőforrások gyűjteménye, a címkék segítségével vizuálisan megjeleníteni az eszközöket a legtöbb legjobb módja. Például elláthat címkével olyan erőforrásokat, amelyek hasonló szerepet töltenek be a szervezetben, vagy ugyanahhoz a részleghez tartoznak.  
Lásd: [az Azure-erőforrások rendszerezése címkék használatával](resource-group-using-tags.md)

## <a name="update-domain"></a>Tartomány frissítése
A gyűjteményben található virtuális gépek rendelkezésre állási csoport, amely egyszerre frissülnek. Az azonos frissítési tartományban lévő virtuális gépek tervezett karbantartás során együtt újraindul. Azure soha nem indít egynél több frissítési tartományt egyszerre. Más néven frissítési tartományokhoz.  
Lásd: [Windows virtuális gépek rendelkezésre állásának kezelése](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [Linux virtuális gépek rendelkezésre állásának kezelése](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>Virtuális gép
A szoftver végrehajtásának egy operációs rendszert futtató fizikai számítógépre. Több virtuális gép is futtatható egyidejűleg ugyanazon a hardveren. Az Azure virtuális gépek elérhetők a különböző méretű.  
Lásd: [Virtual Machines – dokumentáció](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>Virtuálisgép-bővítmény
Viselkedéshez vagy szolgáltatásokat vagy más programok működik, vagy biztosítson a működésre képes futó számítógép megvalósító erőforrás. A virtuális gép hozzáférési bővítmény segítségével például alaphelyzetbe állítása, vagy módosíthatja a távelérés értékek Azure virtuális géphez.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Lásd: [virtuálisgép-bővítmények és szolgáltatásokról (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [virtuálisgép-bővítmények és szolgáltatásokról (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>Virtuális hálózat
A hálózat, amely kapcsolatot biztosít az Azure-erőforrások, hogy el van különítve a többi Azure bérlők között. Egy [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) lehetővé teszi, hogy a virtuális hálózatok közötti kapcsolatokat létesíteni és [egy virtuális és egy a helyszíni hálózat között](vpn-gateway/vpn-gateway-plan-design.md). Az IP-címblokkok, a DNS-beállítások, a biztonsági házirendek és a útvonaltáblák a virtuális hálózaton belül teljes mértékben irányíthatja.  
Lásd: [Virtual Network áttekintése](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Webalkalmazás
Egy másik nevet [App Service-alkalmazást](#app-service-app).

## <a name="see-also"></a>Lásd még

* [Bevezetés az Azure használatába](https://azure.microsoft.com/get-started/)
* [Felhő erőforrás center](https://azure.microsoft.com/resources/)  
* [Az üzleti alkalmazás az Azure-ral](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Az Adatközpont Azure](https://azure.microsoft.com/overview/business-apps-on-azure/)

