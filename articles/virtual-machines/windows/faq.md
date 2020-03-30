---
title: Gyakori kérdések az Azure-beli Windows-virtuális gépekről
description: Válaszok az Erőforrás-kezelő modellel létrehozott Windows virtuális gépekkel kapcsolatos gyakori kérdésekre.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: e3d2673ba52ba0cca36e2a999558313b64716ade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299614"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Gyakori kérdések a Windows virtuális gépekkel kapcsolatban
Ez a cikk az Azure-ban a Resource Manager telepítési modell használatával létrehozott Windows virtuális gépekkel kapcsolatos néhány gyakori kérdést ismerteti. A linuxos verzió ebben a témában lásd [a Gyakran ismételt kérdés linuxos virtuális gépek](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="what-can-i-run-on-an-azure-vm"></a>Mit futtathatok egy Azure-beli virtuális gépen?
Minden előfizető kiszolgálószoftvereket futtathat az Azure-beli virtuális gépeken. A Microsoft kiszolgálószoftverek Azure-beli futtatására vonatkozó támogatási szabályzatról a [Microsoft kiszolgálói szoftvertámogatási szolgáltatása az Azure virtuális gépekhez című témakörben talál.](https://support.microsoft.com/kb/2721672)

A Windows 7, a Windows 8.1 és a Windows 10 bizonyos verziói elérhetők az MSDN Azure előnyökkel járó előfizetői és az MSDN dev és a test for-you-go-előfizetők számára fejlesztési és tesztelési feladatokhoz. Részletekért, többek között az utasításokért és korlátozásokért tekintse meg az [MSDN-előfizetők számára elérhető Windows-rendszerképeket](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/) ismertető cikket. 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Mennyi tárhelyet használhatok egy virtuális gép esetén?
Minden adatlemez legfeljebb 32 767 GiB lehet. A használható adatlemezek száma a virtuális gép méretétől függ. Részletek: [Virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Az Azure felügyelt lemezek az azure-beli virtuális gépekkel való használatra ajánlott lemeztárolási ajánlatok az adatok állandó tárolásához. Minden virtuális géphez több felügyelt lemezt is használhat. A felügyelt lemezek kétféle tartós tárolási lehetőséget kínálnak: prémium és standard felügyelt lemezeket. Az árképzésről a [Felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks)című témakörben talál.

Az Azure storage-fiókok az operációs rendszer lemezei és az adatlemezek számára is biztosíthatnak tárhelyet. Minden lemez egy lapblobként tárolt .vhd-fájl. A díjszabás részleteiért lásd [a Storage szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hogyan érhetem el a virtuális gépemet?
Távoli kapcsolat létrehozása távoli asztali kapcsolat (RDP) használatával Windows virtuális géphez. További információt a [Windows rendszerű Azure-alapú virtuális gépekhez való csatlakozás és bejelentkezés.](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Legfeljebb két egyidejű kapcsolat támogatott, kivéve, ha a kiszolgáló távoli asztali szolgáltatások munkamenet-gazdagépként van konfigurálva.  

Ha problémái vannak a Távoli asztallal, olvassa el a [Távoli asztali kapcsolatok hibaelhárítása Windows-alapú Azure virtuális géppel című témakört.](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 

Ha ismeri a Hyper-V-t, előfordulhat, hogy a VMConnect-hez hasonló eszközt keres. Az Azure nem kínál hasonló eszközt, mert a konzol-hozzáférés nem támogatott.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Használhatom az ideiglenes lemezt (alapértelmezés szerint a D: meghajtót) az adatok tárolására?
Ne használja az ideiglenes lemezt adatok tárolására. Ez csak ideiglenes tárolás, így azt kockáztatja, hogy elveszíti az adatokat, amelyek nem lehet vissza. Adatvesztés akkor fordulhat elő, ha a virtuális gép egy másik állomásra költözik. A virtuális gép átméretezése, a gazdagép frissítése vagy a gazdagépen bekövetkezett hardverhiba lehet az oka egy virtuális gép áthelyezésének.

Ha olyan alkalmazással rendelkezik, amelynek a D: meghajtóbetűjelet kell használnia, újra hozzárendelheti a meghajtóbetűjeleket, hogy az ideiglenes lemez a D:-tól eltérő t. Útmutatásért lásd a [Windows ideiglenes lemezének meghajtóbetűjel-módosítását](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) ismertető cikket.


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hogyan módosíthatom egy ideiglenes lemez meghajtóbetűjelét?
A meghajtóbetűjelet a lapozófájl áthelyezésével és a meghajtóbetűjelek újbóli hozzárendelésével módosíthatja, de meg kell győződnie arról, hogy a lépéseket adott sorrendben hajtja végre. Útmutatásért lásd a [Windows ideiglenes lemezének meghajtóbetűjel-módosítását](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) ismertető cikket.

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Hozzáadhatok egy meglévő virtuális gép egy rendelkezésre állási csoport?
Nem. Ha azt szeretné, hogy a virtuális gép egy rendelkezésre állási csoport része legyen, létre kell hoznia a virtuális gép a készleten belül. Jelenleg nincs módja annak, hogy egy virtuális gép hozzáadása egy rendelkezésre állási csoport létrehozása után.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Feltölthetek egy virtuális gépet az Azure-ba?
Igen. További információt a [helyszíni virtuális gépek Azure-ba való áttelepítése.](on-prem-to-azure.md)

## <a name="can-i-resize-the-os-disk"></a>Átméretezhetem az operációs rendszer lemezét?
Igen. További információt [a Virtuális gép operációsrendszer-meghajtójának kibontása Azure-erőforráscsoportban című témakörben talál.](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Másolhatok vagy klónozhatok egy meglévő Azure-virtuális gép?
Igen. Felügyelt lemezképek használatával létrehozhat egy virtuális gép egy lemezképet, majd a lemezkép segítségével több új virtuális gépet hozhat létre. További információt a [Virtuálisgép egyéni lemezképének létrehozása című témakörben talál.](tutorial-custom-images.md)

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Miért nem látom Kanada középső és keleti régióit az Azure Resource Manageren keresztül?

Kanada Közép- és Kelet-Kanada két új régiója nincs automatikusan regisztrálva a meglévő Azure-előfizetések virtuálisgép-létrehozásához. Ez a regisztráció automatikusan történik, ha egy virtuális gép üzembe helyezése az Azure Portalon keresztül bármely más régióban az Azure Resource Manager használatával. Miután egy virtuális gép telepítve van bármely más Azure-régióban, az új régiók nak elérhetőnek kell lenniük a későbbi virtuális gépek.

## <a name="does-azure-support-linux-vms"></a>Támogatja az Azure a Linux virtuális gépeket?
Igen. Ha gyorsan szeretne linuxos virtuális gép kipróbálására, olvassa el a [Linux virtuális gép létrehozása az Azure-ban a portál használatával című témakört.](../linux/quick-create-portal.md)

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Hozzáadhatok egy hálózati adaptert a virtuális géphez a létrehozása után?
Igen, ez most már lehetséges. A virtuális gép először le kell állítani a felszabadított. Ezután hozzáadhat vagy eltávolíthat egy hálózati adaptert (kivéve, ha ez az utolsó hálózati adapter a virtuális gépen). 

## <a name="are-there-any-computer-name-requirements"></a>Vannak-e számítógépnév-követelmények?
Igen. A számítógépnév hossza legfeljebb 15 karakter lehet. Az erőforrások elnevezésével kapcsolatos további információkért tekintse meg az [elnevezési konvenciókra és korlátozásokra](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging) vonatkozó információkat.

## <a name="are-there-any-resource-group-name-requirements"></a>Vannak erőforráscsoport-névkövetelmények?
Igen. Az erőforráscsoport neve legfeljebb 90 karakter hosszúságú lehet. [Az erőforráscsoportokról az Elnevezési konvenciók szabályai és korlátozásai](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) című témakörben talál további információt.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Mik a felhasználónévi követelmények a virtuális gép létrehozásakor?

A felhasználónevek hossza legfeljebb 20 karakter lehet, és nem végződhetnek egy pontban ("."). 

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


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Mik a jelszókövetelmények a virtuális gép létrehozásakor?

A használt eszköztől függően a jelszó hosszára különböző követelmények vonatkoznak:
 - Portál - 12 -72 karakter között
 - PowerShell - 8 és 123 karakter között
 - CLI - 12 - 123 között

* Tartalmaz kisbetűs karaktereket
* Tartalmaz nagybetűs karaktereket
* Tartalmaz számjegyet
* Tartalmaz speciális karaktert (reguláris kifejezés [\W_])

A következő jelszavak nem engedélyezettek:

<table>
    <tr>
        <td>abc@123</td>
        <td>ilove you!</td>
        <td>P@$$w0.</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>Jelszó!</td>
        <td>Jelszó1</td>
        <td>Jelszó22</td>
    </tr>
</table>