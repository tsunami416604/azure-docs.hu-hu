---
title: Windows Azure virtuális gépek gyakori kérdések |} Microsoft Docs
description: Windows virtuális gépek létrehozása a Resource Manager modellt kapcsolatos gyakori kérdésekre adott válaszok biztosít.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: e440385ca0f22dce8668fe73b91efe14e0c8a9b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Windows virtuális gépek kapcsolatos gyakran ismételt kérdések
Ez a cikk foglalkozik Windows virtuális gépek létrehozása az Azure-ban a Resource Manager üzembe helyezési modellel kapcsolatos gyakori kérdésekre. Ez a témakör Linux verziója: [gyakran feltett kérdés kapcsolatos Linux virtuális gépek](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Mit futtathatok egy Azure-beli virtuális gépen?
Minden előfizető kiszolgálószoftvereket futtathat az Azure-beli virtuális gépeken. A Microsoft server szoftver fut az Azure-ban kapcsolatos támogatási irányelveit kapcsolatos információkért lásd: [Microsoft server szoftver támogatása az Azure virtuális gépek](https://support.microsoft.com/kb/2721672)

A Windows 7, Windows 8.1 és Windows 10 egyes verziói MSDN Azure juttatás előfizetői és MSDN fejlesztői és teszt – használatalapú fizetés-előfizetők fejlesztési és tesztelési feladatok érhetők el. Részletekért, többek között az utasításokért és korlátozásokért tekintse meg az [MSDN-előfizetők számára elérhető Windows-rendszerképeket](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/) ismertető cikket. 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Mennyi tárhelyet használhatok egy virtuális gép esetén?
Minden egyes adatlemez lehet akár 4 TB (4095 GB). A használható adatlemezek száma a virtuális gép méretétől függ. Részletek: [Virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Az Azure Managed lemezek a javasolt lemez tárolási ajánlatok használható Azure virtuális gépekkel adatok állandó tárolására. Egy-egy virtuális géppel több felügyelt lemez is használható. A Managed Disks kétféle tartós tárolási lehetőséget kínál: Premium és Standard szintű Managed Disks. Díjszabási információkért lásd: [lemezek árképzési felügyelt](https://azure.microsoft.com/pricing/details/managed-disks).

Az Azure storage-fiókok tárolási is biztosít az operációsrendszer-lemez és adatlemezeket. Minden lemez egy lapblobként tárolt .vhd-fájl. A díjszabás részleteiért lásd [a Storage szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hogyan lehet fér hozzá a virtuális géphez?
Távoli asztali kapcsolat (RDP) használatával a Windows virtuális gépek távoli kapcsolatot létesíteni. Útmutatásért lásd: [csatlakoztatása, és jelentkezzen be a Windowst futtató Azure virtuális gép](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A két egyidejű kapcsolatok maximális támogatottak, kivéve, ha a kiszolgáló úgy van konfigurálva, a távoli asztali szolgáltatások munkamenet-gazdagépként.  

Ha a távoli asztal problémákat tapasztal, tekintse meg [távoli asztali kapcsolatainak hibaelhárítása a Windows-alapú Azure virtuális gép](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Ha járatos a Hyper-V használatában, akkor előfordulhat, hogy egy, a VMConnecthez hasonló eszközt szeretne használni. Az Azure nem biztosít hasonló eszközt, mivel nem támogatja a virtuális gépek konzolos elérését.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Az ideiglenes lemez (a d meghajtó alapértelmezés szerint) használatával tárolja az adatokat?
Ne használjon az ideiglenes lemez adatainak tárolásához. Értéke csak átmeneti tárolás és így lenne veszhetnek el, amelyek nem állíthatók vissza adatokat. Ha egy másik gazdagépre helyezi a virtuális gép előfordulhat adatvesztés. A virtuális gép átméretezése, a gazdagép frissítése vagy a gazdagépen bekövetkezett hardverhiba lehet az oka egy virtuális gép áthelyezésének.

Ha egy alkalmazás, amely a D: meghajtóbetűjelet használja, akkor is meghajtóbetűjeleket, hogy az ideiglenes lemez D: nem használja. Útmutatásért lásd a [Windows ideiglenes lemezének meghajtóbetűjel-módosítását](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) ismertető cikket.


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hogyan módosíthatom egy ideiglenes lemez meghajtóbetűjelét?
A meghajtó betűjelével módosíthatja a lapozófájlt áttelepítése, valamint a meghajtóbetűjelek újbóli, de győződjön meg arról, hogy a lépések meghatározott sorrendben kell. Útmutatásért lásd a [Windows ideiglenes lemezének meghajtóbetűjel-módosítását](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) ismertető cikket.

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Hozzáadható egy meglévő virtuális gép egy rendelkezésre állási csoportot?
Nem. Ha azt szeretné, hogy a virtuális gép rendelkezésre állási csoport részeként, kell belül a virtuális gép létrehozása. Jelenleg nem áll rendelkezésre egy módszerre, amellyel a virtuális gépek hozzáadása a rendelkezésre állási készlet létrehozása után.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Képes a virtuális gépek is feltöltése az Azure-bA?
Igen. Útmutatásért lásd: [áttelepítése a helyszíni virtuális gépek Azure-bA](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Átméretezhetek, az operációs rendszer lemezének?
Igen. Útmutatásért lásd: [hogyan bontsa ki az operációs rendszer meghajtóját a virtuális gépen az Azure-erőforráscsoport](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Másolja vagy egy meglévő Azure virtuális gép klónozása?
Igen. Felügyelt lemezképek használatával hozzon létre egy virtuális gép lemezképét, és majd használni a lemezkép több új virtuális gépek létrehozásához. Útmutatásért lásd: [hozzon létre egy egyéni lemezképet, a virtuális gépek](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Miért nem jelenik meg Kanada központi és Kanada keleti régiók Azure Resource Manageren keresztül?

A két új régiók Kanada központi és Kanada keleti nem automatikusan regisztrálva van a virtuális gép létrehozásához a meglévő Azure-előfizetések. Ez a regisztráció automatikusan történik, amikor egy virtuális gép telepítése Azure Resource Manager használatával bármely más régióban az Azure portálon keresztül. Bármely más Azure-régió, hogy a virtuális gép telepítése után az új régiók elérhetőknek kell lenniük a következő virtuális gépek.

## <a name="does-azure-support-linux-vms"></a>Támogatja az Azure Linux virtuális gépek?
Igen. Próbálja ki a Linux virtuális gépek gyors létrehozásához lásd: [Linux virtuális gép létrehozása az Azure portál használatával](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Hozzáadható egy hálózati Adaptert a virtuális gép létrehozása után?
Igen, ez a lehetőség most. A virtuális gép először meg kell állítani a felszabadított. Majd adja hozzá, vagy távolítsa el a hálózati adapter (kivéve, ha a virtuális gép utolsó hálózati adapter). 

## <a name="are-there-any-computer-name-requirements"></a>Vannak-e a számítógép neve követelmények?
Igen. A számítógépnév legfeljebb 15 karakter hosszúságú lehet. Lásd: [elnevezési konvenciókat szabályokat és korlátozásokat](/azure/architecture/best-practices/naming-conventions#compute) további információt az erőforrások elnevezési körül.

## <a name="are-there-any-resource-group-name-requirements"></a>Vannak-e valamely erőforrás csoport vonatkozó követelményeknek?
Igen. Az erőforráscsoport neve legfeljebb 90 karakter hosszúságú lehet. Lásd: [elnevezési konvenciókat szabályokat és korlátozásokat](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) erőforráscsoportok további információt.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Virtuális gép létrehozásakor Mik a username követelmények?

Felhasználónevek legfeljebb 20 karakter hosszúságú lehet, és nem végződhet pontra ("."). 


A következő felhasználónevek nem engedélyezettek:
<table>
    <tr>
        <td style="text-align:center">Rendszergazda </td><td style="text-align:center"> adminisztrátor </td><td style="text-align:center"> felhasználótól </td><td style="text-align:center"> Felhasználó1</td>
    </tr>
    <tr>
        <td style="text-align:center">teszt </td><td style="text-align:center"> Felhasználó2 </td><td style="text-align:center"> Test1 </td><td style="text-align:center"> user3</td>
    </tr>    <tr>
        <td style="text-align:center">rendszergazda1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> ADM </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">biztonsági mentés </td><td style="text-align:center"> Konzol </td><td style="text-align:center"> David </td><td style="text-align:center"> Vendég</td>
    </tr>
    <tr>
        <td style="text-align:center">John </td><td style="text-align:center"> tulajdonos </td><td style="text-align:center"> legfelső szintű </td><td style="text-align:center"> kiszolgáló</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> támogatás </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">Teszt2 </td><td style="text-align:center"> Teszt3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>A jelszavakra vonatkozó követelmények Mik azok a virtuális gép létrehozásakor?
Jelszavak 12 – 123 karakter hosszúságúnak kell és 3 kívül a következő 4 bonyolultsági megfeleljen:

* Alacsonyabb karaktereket
* Felső karaktereket
* Rendelkezik egy számjegy
* Rendelkezik egy speciális karaktert (reguláris kifejezéssel egyező [\W_])

A következő jelszavak használata nem megengedett:

<table>
    <tr>
        <td>abc@123 </td>
        <td>P@$$w0rd </td>
        <td>P@ssw0rd </td>
        <td>P@ssword123 </td>
        <td>Pa$ $word </td>
    </tr>
    <tr>
        <td>pass@word1 </td>
        <td>Jelszót! </td>
        <td>Jelszó1 </td>
        <td>Password22 </td>
        <td>ILOVEYOU! </td>
    </tr>
</table>
