---
title: Gyakori kérdések linuxos virtuális gépekről az Azure-ban
description: Választ ad az Erőforrás-kezelő modellel létrehozott Linux virtuális gépekkel kapcsolatos gyakori kérdésekre.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 3c6a5e011a536cc9c34565d4f72a9bee6c6a5254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945168"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Gyakran feltett kérdés a Linux virtuális gépekről
Ez a cikk az Azure-ban a Resource Manager üzembe helyezési modell használatával létrehozott Linux virtuális gépekkel kapcsolatos néhány gyakori kérdést ismerteti. A témakör Windows-verzióját a [Gyakran ismételt kérdések a Windows virtuális gépekkel kapcsolatban című témakörben talál.](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Mit futtathatok egy Azure-beli virtuális gépen?
Minden előfizető kiszolgálószoftvereket futtathat az Azure-beli virtuális gépeken. További információ: [Linux on Azure által támogatott disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Mennyi tárhelyet használhatok egy virtuális gép esetén?
Minden adatlemez legfeljebb 32 767 GiB lehet. A használható adatlemezek száma a virtuális gép méretétől függ. Részletek: [Virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Az Azure felügyelt lemezek az azure-beli virtuális gépekkel való használatra ajánlott lemeztárolási ajánlatok az adatok állandó tárolásához. Minden virtuális géphez több felügyelt lemezt is használhat. A felügyelt lemezek kétféle tartós tárolási lehetőséget kínálnak: prémium és standard felügyelt lemezeket. Az árképzésről a [Felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks)című témakörben talál.

Az Azure storage-fiókok az operációs rendszer lemezei és az adatlemezek számára is biztosíthatnak tárhelyet. Minden lemez egy lapblobként tárolt .vhd-fájl. A díjszabás részleteiért lásd [a Storage szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hogyan érhetem el a virtuális gépemet?
Hozzon létre egy távoli kapcsolatot, hogy jelentkezzen be a virtuális gép, secure shell (SSH). Tekintse meg a Windows vagy [a Linux és a Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) [rendszerről](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) történő csatlakozásra vonatkozó utasításokat. Alapértelmezés szerint az SSH legfeljebb 10 párhuzamos kapcsolatot tesz lehetővé. Ezt a számot a konfigurációs fájl szerkesztésével növelheti.

Ha problémája van, olvassa el [a Biztonságos rendszerhéj (SSH) kapcsolatok hibaelhárítása című témakört.](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Használhatom az ideiglenes lemezt (/dev/sdb1) az adatok tárolására?
Ne használja az ideiglenes lemezt (/dev/sdb1) adatok tárolására. Csak ideiglenes tárolásra van. Azt kockáztatja, hogy olyan adatokat veszít, amelyeket nem lehet helyrehajtani.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Másolhatok vagy klónozhatok egy meglévő Azure-virtuális gép?
Igen. További információt a [Linux-virtuális gépek másolatának létrehozása az Erőforrás-kezelő telepítési modelljében című témakörben talál.](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Miért nem látom Kanada középső és keleti régióit az Azure Resource Manageren keresztül?
Kanada Közép- és Kelet-Kanada két új régiója nincs automatikusan regisztrálva a meglévő Azure-előfizetések virtuálisgép-létrehozásához. Ez a regisztráció automatikusan történik, ha egy virtuális gép üzembe helyezése az Azure Portalon keresztül bármely más régióban az Azure Resource Manager használatával. Miután egy virtuális gép telepítve van bármely más Azure-régióban, az új régiók nak elérhetőnek kell lenniük a későbbi virtuális gépek.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Hozzáadhatok egy hálózati adaptert a virtuális géphez a létrehozása után?
Igen, ez most már lehetséges. A virtuális gép először le kell állítani a felszabadított. Ezután hozzáadhat vagy eltávolíthat egy hálózati adaptert (kivéve, ha ez az utolsó hálózati adapter a virtuális gépen). 

## <a name="are-there-any-computer-name-requirements"></a>Vannak-e számítógépnév-követelmények?
Igen. A számítógépnév hossza legfeljebb 64 karakter lehet. Az erőforrások elnevezésével kapcsolatos további információkért tekintse meg az [elnevezési konvenciókra és korlátozásokra](/azure/architecture/best-practices/resource-naming) vonatkozó információkat.

## <a name="are-there-any-resource-group-name-requirements"></a>Vannak erőforráscsoport-névkövetelmények?
Igen. Az erőforráscsoport neve legfeljebb 90 karakter hosszúságú lehet. [Az erőforráscsoportokról az Elnevezési konvenciók szabályai és korlátozásai](/azure/architecture/best-practices/resource-naming) című témakörben talál további információt.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Mik a felhasználónévi követelmények a virtuális gép létrehozásakor?

A felhasználónevek nek 1-32 karakter hosszúságúnak kell lenniük.

A következő felhasználónevek nem engedélyezettek:

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |
| `video`         |

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Mik a jelszókövetelmények a virtuális gép létrehozásakor?

A használt eszköztől függően a jelszó hosszára különböző követelmények vonatkoznak:
 - Portál - 12 -72 karakter között
 - PowerShell - 8 és 123 karakter között
 - CLI - 12 - 123 között
 

A jelszavaknak az alábbi 4 összetettségi követelmény közül 3-nak is meg kell felelniük:

* Tartalmaz kisbetűs karaktereket
* Tartalmaz nagybetűs karaktereket
* Tartalmaz számjegyet
* Tartalmaz speciális karaktert (reguláris kifejezés [\W_])

A következő jelszavak nem engedélyezettek:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0.</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Jelszó!</td>
        <td style="text-align:center">Jelszó1</td>
        <td style="text-align:center">Jelszó22</td>
        <td style="text-align:center">ilove you!</td>
    </tr>
</table>