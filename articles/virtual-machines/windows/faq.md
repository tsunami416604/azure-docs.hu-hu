---
title: Windows virtuális gépek az Azure-ban – gyakori kérdések |} A Microsoft Docs
description: A Resource Manager modellel létrehozott Windows virtuális gépek kapcsolatos gyakori kérdésekre adott válaszokat biztosít.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
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
ms.openlocfilehash: 65d4326763ef9754159e94c9426f3aee69f80ffd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253363"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Gyakori kérdések a Windows Virtual Machines szolgáltatásról
Ez a cikk foglalkozik az Azure Resource Manager-alapú üzemi modellel létrehozott Windows virtuális gépek kapcsolatos gyakori kérdésekre. Ez a témakör Linux-verzióját, lásd: [gyakori kérdést Linux Virtual Machines szolgáltatásról](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Mit futtathatok egy Azure-beli virtuális gépen?
Minden előfizető kiszolgálószoftvereket futtathat az Azure-beli virtuális gépeken. Az Azure-ban futó Microsoft kiszolgálói szoftverek a támogatási házirenddel kapcsolatos további információkért lásd: [Microsoft kiszolgálószoftveres támogatás az Azure Virtual Machines szolgáltatáshoz](https://support.microsoft.com/kb/2721672)

Windows 7, Windows 8.1 és Windows 10 egyes verzióit MSDN Azure előfizetői előnyöket és az MSDN-es fejlesztési és tesztelés – használatalapú fizetést előfizetők fejlesztési és tesztelési feladatok érhetők el. Részletekért, többek között az utasításokért és korlátozásokért tekintse meg az [MSDN-előfizetők számára elérhető Windows-rendszerképeket](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/) ismertető cikket. 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Mennyi tárhelyet használhatok egy virtuális gép esetén?
Minden adatlemez akár 4 TB-ig (4095 GB) lehet. A használható adatlemezek száma a virtuális gép méretétől függ. Részletek: [Virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Azure Managed Disks szolgáltatást ajánljuk lemezes tárolás ajánlatokra használatra az Azure Virtual Machines hosszú távú adattárolásra az adatok. Egy-egy virtuális géppel több felügyelt lemez is használható. A Managed Disks kétféle tartós tárolási lehetőséget kínál: Premium és Standard szintű Managed Disks. Díjszabási információkért tekintse meg a [Managed Disks díjszabását ismertető](https://azure.microsoft.com/pricing/details/managed-disks).

Az Azure storage-fiókok storage is lehetővé teszi az operációsrendszer-lemez és bármely adatlemez számára. Minden lemez egy lapblobként tárolt .vhd-fájl. A díjszabás részleteiért lásd [a Storage szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hogyan érhetem el a virtuális gépem?
Windows virtuális gép távoli asztali kapcsolat (RDP) használatával távoli kapcsolatot létesíteni. Útmutatásért lásd: [csatlakoztatása és bejelentkezés Windows rendszert futtató Azure virtuális gép](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Legfeljebb két egyidejű kapcsolatok támogatottak, kivéve, ha a kiszolgáló úgy van konfigurálva, a távoli asztali szolgáltatások munkamenet-gazdagépként.  

Ha a távoli asztal problémákat tapasztal, tekintse meg [távoli asztali kapcsolatok hibaelhárítása a Windows-alapú Azure virtuális gép](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Ha járatos a Hyper-V használatában, akkor előfordulhat, hogy egy, a VMConnecthez hasonló eszközt szeretne használni. Az Azure nem biztosít hasonló eszközt, mivel nem támogatja a virtuális gépek konzolos elérését.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Használhatom az ideiglenes lemezt (a D: meghajtó alapértelmezés szerint) adatok tárolására?
Ne használja az ideiglenes lemez a adatok tárolásához. Csak ideiglenes tároló, így azt kockáztatja adatok nem állíthatók vissza. Adatvesztés fordulhat elő, ha a virtuális gép egy másik gazdagépre helyezi. A virtuális gép átméretezése, a gazdagép frissítése vagy a gazdagépen bekövetkezett hardverhiba lehet az oka egy virtuális gép áthelyezésének.

Ha egy alkalmazás, amely kell használnia a D: meghajtó betűjelét, akkor is meghajtóbetűjeleket, hogy az ideiglenes lemez D: nem használja. Útmutatásért lásd a [Windows ideiglenes lemezének meghajtóbetűjel-módosítását](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) ismertető cikket.


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hogyan módosíthatom egy ideiglenes lemez meghajtóbetűjelét?
A meghajtó betűjelét a lapozófájl áthelyezésével és a meghajtóbetűjel módosíthatja, de győződjön meg arról, hogy a lépések a meghatározott sorrendben kell. Útmutatásért lásd a [Windows ideiglenes lemezének meghajtóbetűjel-módosítását](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) ismertető cikket.

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Adhatok hozzá egy meglévő virtuális Gépet egy rendelkezésre állási csoportot?
Nem. Ha azt szeretné, hogy a virtuális gép egy rendelkezésre állási csoport részeként, a csoporton belül a virtuális gép létrehozásához szüksége. Jelenleg nem lehet megoldást a virtuális gép hozzáadása egy rendelkezésre állási csoport létrehozása után.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Képes a virtuális gépek is feltölthetők az Azure-bA?
Igen. Útmutatásért lásd: [való Migrálás a helyszíni virtuális gépek az Azure-bA](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Átméretezheti az operációsrendszer-lemez?
Igen. Útmutatásért lásd: [annak az operációs rendszer meghajtóját, a virtuális gép az Azure-erőforráscsoport](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Másolása vagy egy meglévő Azure virtuális gép klónozása?
Igen. Felügyelt rendszerképekből, hozzon létre egy rendszerképet a virtuális gép és a lemezkép használatával több új virtuális gépeket hozhat létre. Útmutatásért lásd: [hozzon létre egy egyéni rendszerképet a virtuális gépek](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Miért nem látok közép-Kanada és kelet-Kanada régióban az Azure Resource Manager?

Közép-Kanada és kelet-Kanada két új régió automatikusan nincs regisztrálva a virtuális gép létrehozása meglévő Azure-előfizetés számára. Ez a regisztráció automatikusan történik, ha egy virtuális gép telepítve van az Azure Resource Manager bármelyik régióban az Azure Portalon keresztül. Egy virtuális gépet bármely más Azure-régióban való üzembe helyezését követően az új régiókban érhető el a következő virtuális gépek kell lennie.

## <a name="does-azure-support-linux-vms"></a>Támogatja az Azure Linux rendszerű virtuális gépek?
Igen. Próbálja ki a Linuxos virtuális gép gyors létrehozásához lásd: [Linux rendszerű virtuális gép létrehozása az Azure-ban a portál használatával](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Is hozzáadhatok egy hálózati Adaptert a virtuális gép létrehozása után?
Igen, ez a lehetőség most. A virtuális Gépet először meg kell állítani a felszabadítása sikeresen megtörtént. Ezután adja hozzá, vagy távolítsa el a hálózati adapter (kivéve a legutóbbi hálózati Adaptert a virtuális gépen). 

## <a name="are-there-any-computer-name-requirements"></a>Vannak-e minden olyan számítógépre vonatkozó követelményeknek?
Igen. A számítógépnév legfeljebb 15 karakter hosszú lehet. Lásd: [elnevezési konvenciók szabályokat és korlátozásokat](/azure/architecture/best-practices/naming-conventions#compute) további információt az erőforrások elnevezési körül.

## <a name="are-there-any-resource-group-name-requirements"></a>Vannak-e valamelyik erőforrás csoport vonatkozó követelményeknek?
Igen. Az erőforráscsoport neve legfeljebb 90 karakter hosszúságú lehet. Lásd: [elnevezési konvenciók szabályokat és korlátozásokat](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) erőforráscsoportokkal kapcsolatos további információt.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Mik a felhasználónév egy virtuális gép létrehozásakor?

Felhasználónevek legfeljebb 20 karakter hosszúságú lehet, és nem végződhet ponttal ("."). 


A következő felhasználónevek nem engedélyezettek:
<table>
    <tr>
        <td style="text-align:center">1</td><td style="text-align:center">123</td><td style="text-align:center">a</td><td style="text-align:center">actuser</td>
    </tr>
    <tr>
        <td style="text-align:center">ADM –</td><td style="text-align:center">rendszergazda</td><td style="text-align:center">rendszergazda1</td><td style="text-align:center">admin2</td>
    </tr>   <tr>
        <td style="text-align:center">Rendszergazda</td><td style="text-align:center">aspnet</td><td style="text-align:center">biztonsági mentés</td><td style="text-align:center">konzol</td>
    </tr>
    <tr>
        <td style="text-align:center">David </td><td style="text-align:center">Vendég</td><td style="text-align:center">János</td><td style="text-align:center">tulajdonos</td>
    </tr>
    <tr>
        <td style="text-align:center">legfelső szintű</td><td style="text-align:center">kiszolgáló</td><td style="text-align:center">sql</td><td style="text-align:center">támogatás</td>
    </tr>
    <tr>
        <td style="text-align:center">support_388945a0</td><td style="text-align:center">sys.</td><td style="text-align:center">test</td><td style="text-align:center">test1</td>
    </tr>
    <tr>
        <td style="text-align:center">test2</td><td style="text-align:center">Teszt3</td><td style="text-align:center">Felhasználó</td><td style="text-align:center">Felhasználó1</td>
    </tr>
    <tr>
        <td style="text-align:center">mind a Felhasználó2</td><td style="text-align:center">Felhasználó3</td><td style="text-align:center">Felhasználó4</td><td style="text-align:center">user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Mik a jelszót a virtuális gép létrehozásakor?
Jelszavak kell 12 – 123 karakter hosszúságú lehet, és felel meg a 3 tartományon kívül az alábbi 4 összetettségi követelményeknek:

* Alacsonyabb karakter hosszúságú lehet
* Felső karakter hosszúságú lehet
* Rendelkezik egy számjegy
* Egy speciális karaktert (reguláris kifejezéssel egyező [\W_])

A következő jelszavak használata nem megengedett:

<table>
    <tr>
        <td>abc@123</td>
        <td>ILOVEYOU!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$ $word</td>
        <td>pass@word1</td>
        <td>Jelszót!</td>
        <td>Jelszó1</td>
        <td>Password22</td>
    </tr>
</table>
