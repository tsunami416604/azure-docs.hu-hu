---
title: A Windows rendszerű virtuális gépek áttekintése – Azure | Microsoft Docs
description: Tudnivalók Windows rendszerű virtuális gépek létrehozásáról és kezeléséről az Azure-ban.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 23d7fbc07e900612d4e5274d60aae31600e59a54
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657127"
---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Windows rendszerű virtuális gépek áttekintése az Azure-ban

Az Azure Virtual Machines (VM) az Azure által kínált számos különböző típusú, [igény szerinti, méretezhető számítási erőforrás](../../app-service/overview-compare.md) közé tartozik. Virtuális gépet általában akkor érdemes választani, ha a számítási környezet átfogóbb vezérlésére van szüksége annál, amelyet az egyéb lehetőségek kínálnak. Ez a cikk bemutatja, hogy mit kell szem előtt tartania egy virtuális gép létrehozása előtt, valamint hogy hogyan hozhatja létre és kezelheti azt.

Az Azure VM a virtualizálás rugalmasságát biztosítja anélkül, hogy a virtuális gépet futtató fizikai hardvereket kellene vásárolnia és karbantartania. A virtuális gép karbantartásához azonban így is szükséges elvégeznie néhány feladatot, például a virtuális gépen futó szoftver konfigurálását, javítását és telepítését.

Az Azure virtuális gépek különféle módon használhatóak. Néhány példa:

* **Fejlesztés és tesztelés** – Az Azure virtuális gépek gyors és egyszerű módot biztosítanak az alkalmazások programozásához és teszteléséhez szükséges adott konfigurációjú számítógépek létrehozásához.
* **Felhőbeli alkalmazások** – Mivel az alkalmazások iránti igény ingadozhaz, gazgaságosabb lehet őket egy virtuális gépen futtatni az Azure szolgáltatásban. A további virtuális gépekért csak akkor kell fizetnie, amikor szüksége van rájuk, amikor pedig nincs, akkor leállíthatja őket.
* **Bővített adatközpont** – Az Azure virtuális hálózatokon futó virtuális gépek könnyedén összekapcsolhatók a szervezet hálózatához.

Az alkalmazás által használt virtuális gépek száma az Ön igényei szerint skálázható.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Mit kell átgondolnom egy virtuális gép létrehozása előtt?
Az Azure-ban futó alkalmazás-infrastruktúrák kiépítésekor mindig számos [kialakítási szempontot](/azure/architecture/reference-architectures/virtual-machines-windows?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kell figyelembe venni. A kezdés előtt a virtuális gépek következő tulajdonságait fontos átgondolni:

* Az alkalmazás erőforrásainak nevei
* Az erőforrások tárolásának helye
* A virtuális gép mérete
* A létrehozható virtuális gépek maximális száma
* A virtuális gépen futó operációs rendszer
* A virtuális gép indítás utáni konfigurációja
* A virtuális gép által igényelt kapcsolódó erőforrások

### <a name="naming"></a>Elnevezés
A virtuális gépeknek van egy hozzárendelt [nevük](/azure/architecture/best-practices/naming-conventions), valamint egy, az operációs rendszer részeként konfigurálható számítógépnevük. A virtuális gépek neve legfeljebb 15 karakter hosszú lehet.

Ha az operációs rendszert tartalmazó lemezt az Azure használatával hozza létre, akkor a számítógépnév és a virtuális gép neve ugyanaz. Ha egy korábban már konfigurált operációs rendszert tartalmazó [saját rendszerképet tölt fel és használ](upload-generalized-managed.md) egy virtuális gép létrehozásához, akkor a nevek különbözőek lehetnek. Javasoljuk, hogy a saját rendszerkép feltöltésekor állítsa be, hogy az operációs rendszerben megadott számítógépnév és a virtuális gép neve ugyanaz legyen.

### <a name="locations"></a>Helyek
Az Azure-ban létrehozott minden erőforrás világszerte több [földrajzi régió](https://azure.microsoft.com/regions/) között oszlik meg. A virtuális gépek létrehozásakor a régiót általában **helynek** nevezik. A virtuális gépek esetében a hely adja meg, hogy a virtuális merevlemezek hol tárolódnak.

Az alábbi táblázatban az elérhető helyek listájának megismeréséhez olvasható néhány módszer.

| Módszer | Leírás |
| --- | --- |
| Azure Portal |Egy virtuális gép létrehozásakor válasszon egy helyet a listából. |
| Azure PowerShell |Használja a [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) parancsot. |
| REST API |Használja a [Helyek listázása](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_ListLocations) műveletet. |
| Azure CLI |Használja az [az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest) műveletet. |

### <a name="vm-size"></a>Virtuális gép mérete
A használt virtuális gép [mérete](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a futtatni kívánt számítási feladatok mennyiségétől függ. A választott méret határoz meg olyan tényezőket, mint a feldolgozókapacitás, a memória és a tárolókapacitás. Az Azure számos különböző méretet kínál különféle felhasználási módokhoz.

Az Azure [óradíjas rendszerben](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) számláz, a virtuális gép mérete és az operációs rendszer alapján. Nem egész órák esetében az Azure csak a használt perceket számlázza. A tárhely árazása és felszámítása külön történik.

### <a name="vm-limits"></a>A virtuális gépekre korlátai
Minden előfizetésre alapértelmezett [kvótakorlátozások](../../azure-subscription-service-limits.md) vonatkoznak, amelyek akadályt jelenthetnek, ha a projektjéhez nagy számú virtuális gépet szeretne üzembe helyezni. A jelenlegi határérték minden előfizetés esetében régiónként 20 virtuális gép. A határértékek megemelhetők [egy emelést kérvényező támogatási jegy benyújtásával](../../azure-supportability/resource-manager-core-quotas-request.md)

### <a name="operating-system-disks-and-images"></a>Operációsrendszer-lemezek és -rendszerképek
A virtuális gépek [virtuális merevlemezeken (VHD-k)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) tárolják az operációs rendszereket és az adatokat. A VHD-ken találhatók az operációs rendszer telepítéséhez kiválasztható rendszerképek is. 

Az Azure számos, a [piactéren elérhető rendszerképet](https://azure.microsoft.com/marketplace/virtual-machines/) kínál használatra, amelyek a Windows Server operációs rendszer különböző verzióit és típusait tartalmazzák. A piactérről származó rendszerképek azonosítása a rendszerkép közzétevője, ajánlat, termékváltozat és verzió alapján lehetséges (a verzió általában mint „legfrissebb” van megadva). Kizárólag a 64 bites operációs rendszerek támogatottak. A támogatott vendég operációs rendszerekkel, szerepkörökkel és funkciókkal kapcsolatos további információkért tekintse meg [a Microsoft Azure-beli virtuális gépek Microsoft kiszolgálószoftveres támogatását](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) ismertető cikket.

Az alábbi tábla bemutat néhány módszert, amelyekkel egy rendszerképről több információ is szerezhető.

| Módszer | Leírás |
| --- | --- |
| Azure Portal |Az értékek a rendszerképek kiválasztásakor automatikusan megjelennek. |
| Azure PowerShell |[Get-AzureRMVMImagePublisher](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmimagepublisher) -Location *location*<BR>[Get-AzureRMVMImageOffer](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmimageoffer) -Location *location* -Publisher *publisherName*<BR>[Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) -Location *location* -Publisher *publisherName* -Offer *offerName* |
| REST API-k |[Rendszerkép-közzétevők listázása](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Rendszerkép-ajánlatok listázása](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Rendszerkép-termékváltozatok listázása](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[az vm image list-publishers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az_vm_image_list_publishers) --location *location*<BR>[az vm image list-offers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --location *location* --publisher *publisherName*<BR>[az vm image list-skus](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_skus) --location *location* --publisher *publisherName* --offer *offerName*|

Lehetősége van egy [saját rendszerképek feltöltésére és használatára](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account) is, ebben az esetben a közzétevő neve, az ajánlat és a termékváltozat adatok nem használatosak.

### <a name="extensions"></a>Bővítmények
A virtuális gépek [bővítményei](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) további hozzáadott képességekkel ruházzák fel a virtuális gépeket az üzembe helyezést követő konfigurálás és automatizált feladatok útján.

A bővítményekkel a következő gyakori feladatok végezhetők el:

* **Egyéni szkriptek futtatása** – A [Custom Script bővítmény](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) segít a virtuális gép számítási feladatainak konfigurálásában azáltal, hogy a virtuális gép üzembe helyezésekor lefuttatja az Ön szkriptjét.
* **Konfigurációk üzembe helyezése és kezelése** – A [PowerShell Célállapot konfigurálása (DSC) bővítmény](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) segítségével beállíthatja egy virtuális gépen a DSC-t a konfigurációk és környezetek kezeléséhez.
* **Diagnosztikai adatok gyűjtése** – Az [Azure Diagnostics bővítményével](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a virtuális gép diagnosztikai adatok gyűjtésére konfigurálható, amelyek felhasználhatók az alkalmazás állapotának figyelésére.

### <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)
Az alábbi táblázat a virtuális gép által használt erőforrásokat tartalmazza, amelyeknek létezniük kell a virtuális gép létrehozásakor, vagy akkor kell őket létrehozni.

| Erőforrás | Szükséges | Leírás |
| --- | --- | --- |
| [Erőforráscsoport](../../azure-resource-manager/resource-group-overview.md) |Igen |A virtuális gépnek egy erőforráscsoport tagjának kell lennie. |
| [Storage-fiók](../../storage/common/storage-create-storage-account.md) |Igen |A virtuális gépnek szüksége van egy tárfiókra a virtuális merevlemezek tárolásához. |
| [Virtuális hálózat](../../virtual-network/virtual-networks-overview.md) |Igen |A virtuális gépnek egy virtuális hálózat tagjának kell lennie. |
| [Nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Nem |A virtuális gép rendelkezhet hozzárendelt nyilvános IP-címmel, hogy távolról is el lehessen érni. |
| [Hálózati illesztő](../../virtual-network/virtual-network-network-interface.md) |Igen |A virtuális gépnek szüksége van a hálózati illesztőre a hálózattal való kommunikációhoz. |
| [Adatlemezek](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Nem |A virtuális gépek a tárolókapacitást bővítő adatlemezeket is tartalmazhatnak. |

## <a name="how-do-i-create-my-first-vm"></a>Hogyan hozhatom létre az első virtuális gépemet?
A virtuális gépek létrehozásakor számos választási lehetőség áll rendelkezésre. A megfelelő választás az Ön környezetétől függ. 

Az alábbi táblázat információkat nyújt a virtuális gépek létrehozásának első lépéseivel kapcsolatban.

| Módszer | Cikk |
| --- | --- |
| Azure Portal |[Windows rendszerű virtuális gép létrehozása a portál használatával](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Sablonok |[Windows rendszerű virtuális gép létrehozása egy Resource Manager-sablonnal](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[Windows rendszerű virtuális gép létrehozása a PowerShell használatával](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Ügyfél-SDK-k |[Azure erőforrások üzembe helyezés a C# használatával](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| REST API-k |[Virtuális gépek létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |
| Azure CLI |[Virtuális gép létrehozása az Azure CLI-vel](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-cli-sample-create-vm) |

Reménykedhet, hogy sosem következik be, de várhatóan időnként elromlik valami. Ha ilyen helyzet áll elő, tekintse át [A Resource Manager üzembe helyezési hibáinak elhárítása egy Windows rendszerű virtuális gép létrehozásakor az Azure-ban](../troubleshooting/troubleshoot-deployment-new-vm-windows.md) témakört.

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Hogyan kezelhetem a létrehozott virtuális gépet?
A virtuális gépek felügyelhetők egy böngészőalapú portállal, parancsfájlkezelést támogató parancssori eszközökkel, vagy közvetlenül az API-kon keresztül. A tipikus kezelési feladatok közé tartozik a virtuális gépek adatainak lekérése, a virtuális gépekre való bejelentkezés, a rendelkezésre állás kezelése és a biztonsági mentések készítése.

### <a name="get-information-about-a-vm"></a>Virtuális gép adatainak lekérése
Az alábbi táblázatban az elérhető helyek listájának megismeréséhez olvasható néhány módszer.

| Módszer | Leírás |
| --- | --- |
| Azure Portal |A központi menüben kattintson a **Virtual Machines** elemre, majd válassza ki a virtuális gépet a listából. A virtuális gép panelén érheti el az információk áttekintését, az értékek beállításait és a mérőszámok figyelését. |
| Azure PowerShell |A virtuális gépek PowerShell-lel való kezelésével kapcsolatos tudnivalókért tekintse meg a [Windows rendszerű virtuális gépek Azure PowerShell modullal való létrehozásával és felügyeletével kapcsolatos](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) cikket. |
| REST API |Használja a [Virtuális gép adatainak lekérése](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) műveletet egy virtuális gép adatainak lekéréséhez. |
| Ügyfél-SDK-k |Információk a virtuális gépek C# használatával való kezeléséről: [Microsoft Azure virtuális gépek kezelése az Azure Resource Manager és a C# használatával](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Azure CLI |A virtuális gépek Azure CLI-vel való kezelésével kapcsolatban tekintse meg [az Azure CLI referenciaanyagát](https://docs.microsoft.com/cli/azure/vm). |

### <a name="log-on-to-the-vm"></a>Bejelentkezés a virtuális gépre
Használja az Azure Portal Csatlakozás gombját [egy távoli asztali (RDP) munkamenet elindításához](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A távoli kapcsolatok használatának megkísérlésekor időnként hibák történhetnek. Ha ez a helyzet áll elő, tekintse át a súgó információit a [Távoli asztali kapcsolatok hibaelhárítása Windows rendszert futtató Azure virtuális gépeken](../troubleshooting/troubleshoot-rdp-connection.md) szakaszban.

### <a name="manage-availability"></a>Rendelkezésre állás kezelése
Fontos, hogy tisztában legyen azzal, hogyan [biztosíthatja alkalmazása magas rendelkezésre állását](manage-availability.md). Ebben a konfigurációban több virtuális gépet hozhat létre, hogy közülük legalább egy mindig, mindenképp fusson.

Ahhoz, hogy az üzembe helyezett példány megfeleljen a 99,95-ös virtuálisgép-szolgáltatói szerződésünknek, legalább két virtuális gépet kell üzembe helyeznie a számítási feladatok futtatásához egy [rendelkezésre állási csoporton](tutorial-availability-sets.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) belül. Ez a konfiguráció biztosítja, hogy a virtuális gépek több tartalék tartomány között oszoljanak meg, és az őket futtató gazdagépeknek különböző karbantartási időszakaik legyenek. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

### <a name="back-up-the-vm"></a>Virtuális gépek biztonsági mentése
A [Recovery Services tároló](../../backup/backup-introduction-to-azure-backup.md) megvédi az Azure Backup és az Azure Site Recovery szolgáltatásokban található adatokat és adategységeket. Igénybe vehet egy Recovery Services tárolót a [Resource Managerrel üzembe helyezett virtuális gépek biztonsági mentéseinek létrehozásához és kezeléséhez a PowerShell használatával.](../../backup/backup-azure-vms-automation.md) 

## <a name="next-steps"></a>További lépések
* Ha Linux rendszerű virtuális gépeket tervez használni, nézze át [Az Azure és a Linux](../linux/overview.md) témakört.
* Infrastruktúra-beállítási útmutatóért lásd [Az Azure-infrastruktúra bemutatása példákkal](infrastructure-example.md) témakört.
