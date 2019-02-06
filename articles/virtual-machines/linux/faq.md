---
title: Gyakori kérdések az Azure-ban Linux rendszerű virtuális gépekhez |} A Microsoft Docs
description: Linux rendszerű virtuális gépek a Resource Manager modellel létrehozott kapcsolatos gyakori kérdésekre adott válaszokat biztosít.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: cynthn
ms.openlocfilehash: 8d421adfae335a976485ed463a69484a74be5b44
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753929"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Linux rendszerű virtuális gépek kapcsolatos gyakori kérdések
Ez a cikk foglalkozik az Azure Resource Manager-alapú üzemi modellel létrehozott Linux virtuális gépek kapcsolatos gyakori kérdésekre. Ez a témakör Windows verziója: [– gyakori kérdések a Windows Virtual Machines szolgáltatásról](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Mit futtathatok egy Azure-beli virtuális gépen?
Minden előfizető kiszolgálószoftvereket futtathat az Azure-beli virtuális gépeken. További információkért lásd: [Linux-Disztribúciók Azure-Endorsed](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Mennyi tárhelyet használhatok egy virtuális gép esetén?
Minden adatlemez akár 4 TB-ig (4095 GB) lehet. A használható adatlemezek száma a virtuális gép méretétől függ. Részletek: [Virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure Managed Disks szolgáltatást ajánljuk lemezes tárolás ajánlatokra használatra az Azure Virtual Machines hosszú távú adattárolásra az adatok. Egy-egy virtuális géppel több felügyelt lemez is használható. A felügyelt lemezek ajánlat kétféle tartós tárolási lehetőséget kínál: Premium and Standard Managed Disks. Díjszabási információkért tekintse meg a [Managed Disks díjszabását ismertető](https://azure.microsoft.com/pricing/details/managed-disks).

Az Azure storage-fiókok storage is lehetővé teszi az operációsrendszer-lemez és bármely adatlemez számára. Minden lemez egy lapblobként tárolt .vhd-fájl. A díjszabás részleteiért lásd [a Storage szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hogyan érhetem el a virtuális gépem?
Jelentkezzen be a virtuális gépet, a Secure Shell (SSH) használatával távoli kapcsolatot létesíteni. A csatlakozás tudnivalókat [a Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [a Linux és Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Alapértelmezés szerint az SSH legfeljebb 10 párhuzamos kapcsolatot tesz lehetővé. Ezt a számot a konfigurációs fájl szerkesztésével növelheti.

Ha problémákat tapasztal, tekintse meg az [hibaelhárítása Secure Shell (SSH) kapcsolatok](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Használhatom az ideiglenes lemezt (/ dev/sdb1) adatok tárolására?
Ne használja az ideiglenes lemezt (/ dev/sdb1) adatok tárolására. Csak ott van az ideiglenes tárhely. Veszhetnek el, amelyek nem állíthatók vissza adatokat.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Másolása vagy egy meglévő Azure virtuális gép klónozása?
Igen. Útmutatásért lásd: [Linux rendszerű virtuális gép másolatának létrehozása a Resource Manager-alapú üzemi modellben](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Miért nem látok közép-Kanada és kelet-Kanada régióban az Azure Resource Manager?
Közép-Kanada és kelet-Kanada két új régió automatikusan nincs regisztrálva a virtuális gép létrehozása meglévő Azure-előfizetés számára. Ez a regisztráció automatikusan történik, ha egy virtuális gép telepítve van az Azure Resource Manager bármelyik régióban az Azure Portalon keresztül. Egy virtuális gépet bármely más Azure-régióban való üzembe helyezését követően az új régiókban érhető el a következő virtuális gépek kell lennie.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Is hozzáadhatok egy hálózati Adaptert a virtuális gép létrehozása után?
Igen, ez a lehetőség most. A virtuális Gépet először meg kell állítani a felszabadítása sikeresen megtörtént. Ezután adja hozzá, vagy távolítsa el a hálózati adapter (kivéve a legutóbbi hálózati Adaptert a virtuális gépen). 

## <a name="are-there-any-computer-name-requirements"></a>Vannak-e minden olyan számítógépre vonatkozó követelményeknek?
Igen. A számítógép neve legfeljebb 64 karakter hosszúságú lehet. Lásd: [elnevezési konvenciók szabályokat és korlátozásokat](/azure/architecture/best-practices/naming-conventions) további információt az erőforrások elnevezési körül.

## <a name="are-there-any-resource-group-name-requirements"></a>Vannak-e valamelyik erőforrás csoport vonatkozó követelményeknek?
Igen. Az erőforráscsoport neve legfeljebb 90 karakter hosszúságú lehet. Lásd: [elnevezési konvenciók szabályokat és korlátozásokat](/azure/architecture/best-practices/naming-conventions) erőforráscsoportokkal kapcsolatos további információt.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Mik a felhasználónév egy virtuális gép létrehozásakor?

Felhasználónevek 1 – 32 karakter hosszúságúnak kell lennie.

A következő felhasználónevek nem engedélyezettek:

<table>
    <tr>
        <td style="text-align:center">Rendszergazda </td><td style="text-align:center"> admin </td><td style="text-align:center"> Felhasználó </td><td style="text-align:center"> Felhasználó1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> mind a Felhasználó2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> Felhasználó3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">biztonsági mentés </td><td style="text-align:center"> konzol </td><td style="text-align:center"> David </td><td style="text-align:center"> Vendég</td>
    </tr>
    <tr>
        <td style="text-align:center">János </td><td style="text-align:center"> tulajdonos </td><td style="text-align:center"> legfelső szintű </td><td style="text-align:center"> kiszolgáló</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> támogatás </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys.</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> Teszt3 </td><td style="text-align:center"> Felhasználó4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Mik a jelszót a virtuális gép létrehozásakor?
Jelszavak kell 6 – 72 karakter hosszúságú lehet, és felel meg a 3 tartományon kívül az alábbi 4 összetettségi követelményeknek:

* Alacsonyabb karakter hosszúságú lehet
* Felső karakter hosszúságú lehet
* Rendelkezik egy számjegy
* Egy speciális karaktert (reguláris kifejezéssel egyező [\W_])

A következő jelszavak használata nem megengedett:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Jelszót!</td>
        <td style="text-align:center">Jelszó1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
