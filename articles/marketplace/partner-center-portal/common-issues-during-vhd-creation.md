---
title: Gyakori problémák a virtuális merevlemez ek létrehozása során (GYAKORI KÉRDÉSEK)
description: Gyakori kérdések a virtuális merevlemez (VHD) létrehozásakor felmerülő gyakori problémákról.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266265"
---
# <a name="common-issues-during-vhd-creation"></a>Gyakori problémák a virtuális merevlemez ek létrehozása során

> [!NOTE]
> Az Azure virtuális gép ajánlatainak kezelését áthelyezzük a Cloud Partner Portalról a Partnerközpontba. Az ajánlatok áttelepítéséig kövesse a Virtuális merevlemez létrehozása során a [gyakori problémák (GYIK)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) utasításokat az ajánlatok kezeléséhez.

Ezek a gyakran ismételt kérdések (GYIK) olyan gyakori problémákat fednek le, amelyekkel az Azure virtuálisgép-ajánlathoz virtuális merevlemez (VHD) létrehozásakor találkozhat.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Hogyan hozhatok létre virtuális gép az Azure Portalon egy virtuális merevlemez prémium szintű storage-ban?

Az Azure Marketplace jelenleg nem támogatja a felügyelt tárhelyen lévő lemezképekből vagy az Azure Premium Storage-ból származó virtuálisgép-ajánlatok létrehozását. További információt az [Azure felügyelt lemezek – áttekintés című témakörben talál.](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)

## <a name="can-i-use-generation-2-vms-for-offers"></a>Használhatom a 2.

Nem, csak az 1. Jelenleg azonban együttműködünk a Microsoft Azure Platform Team szolgáltatással a 2. A részleteket lásd: [1. generációs vagy 2-es virtuális gép létrehozása a Hyper-V-ben?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Hogyan változtathatom meg a gazdagép nevét?

Nem, nem lehet. A virtuális gép létrehozása után a felhasználók (beleértve a tulajdonosokat is) nem frissíthetik az állomásnevet.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Hogyan állíthatom alaphelyzetbe a Távoli asztal szolgáltatást vagy annak bejelentkezési jelszavát?

Az alábbi cikkek ismertetik, hogyan hajthatja végre a Távoli asztali szolgáltatások visszaállítása Windows- és Linux-alapú virtuális gépeken:

* [A távoli asztali szolgáltatás, illetve a hozzá tartozó bejelentkezési jelszó visszaállítása Windows rendszerű virtuális gépen](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Linux virtuális gép jelszava vagy SSH-kulcs alaphelyzetbe állítása, az SSH-konfiguráció javítása és a lemezkonzisztencia ellenőrzése a VMAccess-bővítmény használatával](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Hogyan hozhatok létre új SSH tanúsítványokat?

A tanúsítványok létrehozását az [Azure VM-lemezkép-minősítés](https://aks.ms/CertifyVMimage)ismerteti.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hogyan konfigurálhatok virtuális magánhálózatot (VPN) a virtuális gépekkel való együttműködésre?

Ha az Azure Resource Manager telepítési modelljét használja, három lehetősége van:

* [Útvonalalapú VPN-átjáró létrehozása az Azure Portal használatával](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Útvonalalapú VPN-átjáró létrehozása az Azure PowerShell használatával](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [Útvonalalapú VPN-átjáró létrehozása a CLI használatával](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Mik a Microsoft támogatási szabályzatai a Microsoft kiszolgálószoftverének Azure-alapú virtuális gépeken való futtatásához?

A részleteket a [Microsoft Server szoftvertámogatási szolgálatában találja a Microsoft Azure virtuális gépekhez.](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>A virtuális gépekhez egyedi azonosítók vannak társítva?

Igen, ha az Azure-ban üzemelteti. Az Azure minden új virtuálisgép-erőforráshoz hozzárendel egy egyedi azonosítót, az [Azure virtuálisgép-egyedi azonosítót.](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/) További részletek: Azure Virtual Machine Unique ID. Ezt az azonosítót a [List API-n](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)keresztül is beszerezheti.

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>A virtuális gép, hogyan kezelheti az egyéni parancsfájl-bővítmény az indítási feladat?

Az Egyéni parancsfájl-bővítmény Azure PowerShell-modullal, az Azure Resource Manager-sablonokkal és a Windows-rendszerek hibaelhárítási lépéseinek használatával kapcsolatos részletekért olvassa el [a Windows egyéni parancsfájl-bővítménye](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)című témakört.

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>A 32 bites alkalmazások vagy szolgáltatások támogatottak az Azure Marketplace-en?

Általában nem. A támogatott operációs rendszerek és az Azure virtuális gépek szabványos szolgáltatásai mind 64 bitesek. Bár a legtöbb 64 bites operációs rendszer támogatja az alkalmazások 32 bites verzióit a visszamenőleges kompatibilitás érdekében, a 32 bites alkalmazások virtuálisgép-megoldás részeként való használata nem támogatott és erősen ajánlott. Hozza létre újra az alkalmazást 64 bites projektként.

További információval a következő cikkek szolgálnak:

* [32 bites alkalmazások futtatása](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [32 bites operációs rendszerek támogatása az Azure-beli virtuális gépeken](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft kiszolgálószoftveres támogatás a Microsoft Azure Virtual Machines szolgáltatáshoz](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Hiba: A virtuális merevlemez már regisztrálva van a képtártárban erőforrásként

Minden alkalommal, amikor megpróbálok létrehozni egy lemezképet a Virtuális merevlemezek, kapok a "VHD már regisztrálva van a rendszerképtár, mint az erőforrás" az Azure PowerShellben. Korábban nem hoztam létre semmilyen képet, és nem találtam ilyen nevű képet az Azure-ban. Hogyan oldhatom meg ezt?

Ez a probléma általában akkor jelenik meg, ha virtuális gép egy virtuális merevlemez, amely a zár rajta. Győződjön meg arról, hogy nincs virtuális gép lefoglalva a virtuális merevlemez, majd próbálja meg újra a műveletet. Ha a probléma továbbra is fennáll, nyisson meg egy támogatási jegyet. Lásd: [Partnerközpont támogatása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).
