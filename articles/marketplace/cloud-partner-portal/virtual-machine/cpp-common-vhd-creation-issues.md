---
title: Gyakori problémák a virtuális merevlemezek létrehozása során (GYAKORI KÉRDÉSEK) az Azure Piactéren
description: Gyakori kérdések a virtuális merevlemez ek létrehozásáról és a kapcsolódó problémákról.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: dsindona
ms.openlocfilehash: 8ae1dcbeff6e14e6f1f7cd44ae3a0a68bd379b56
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387342"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Gyakori problémák a virtuális merevlemez ek létrehozása során (GYAKORI KÉRDÉSEK)

A következő gyakori kérdések (GYIK) a virtuális merevlemez (VHD) és a virtuális gép (VM) létrehozása során felmerülő gyakori problémákat fedik le. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Hogyan hozhat létre virtuális gép az Azure Portalon a prémium szintű storage-ba feltöltött virtuális merevlemez használatával?

Az Azure Marketplace jelenleg nem támogatja a felügyelt tárhelyen vagy az Azure Premium Storage-ban tárolt lemezképek virtuálisgép-ajánlatai létrehozását.  Ezekről a tárolási lehetőségekről az Azure felügyelt lemezek – áttekintés című témakörben olvashat [bővebben.](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)


## <a name="can-you-use-generation-2-vms-for-offers"></a>Használhatja a 2.

Nem, csak az 1.generációs Virtuális d-k támogatottak.  Jelenleg azonban együttműködünk a Microsoft Azure Platform Team szolgáltatással a 2.  A különbségekről további információt az [1- es vagy 2-es generációs virtuális gép létrehozása a Hyper-V-ben című témakörben talál.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Hogyan lehet megváltoztatni a nevét a fogadó?

Nem lehet.  A virtuális gép létrehozása után a felhasználók (beleértve a tulajdonosokat is) nem frissíthetik az állomás nevét.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Hogyan állíthatja alaphelyzetbe a Távoli asztal szolgáltatást vagy annak bejelentkezési jelszavát?

Az alábbi cikkek ismertetik, hogyan hajthatja végre a Távoli asztali szolgáltatások visszaállítása Windows- és Linux-alapú virtuális gépeken:   

- [A távoli asztali szolgáltatás, illetve a hozzá tartozó bejelentkezési jelszó visszaállítása Windows rendszerű virtuális gépen](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Linux virtuális gép jelszava vagy SSH-kulcs alaphelyzetbe állítása, az SSH-konfiguráció javítása és a lemezkonzisztencia ellenőrzése a VMAccess-bővítmény használatával](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Hogyan hozhat létre új SSH tanúsítványokat?

A tanúsítványok létrehozását a virtuális [gép lemezképének megosztott hozzáférésű aláírásának URI-jának beszerzése](./cpp-get-sas-uri.md) című cikk ismerteti a [virtuális gép ajánlatának technikai eszközök létrehozása](./cpp-create-technical-assets.md)című szakaszában.


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hogyan konfigurálható egy virtuális magánhálózat (VPN) a virtuális gépekkel való együttműködésre?

Ha az Azure Resource Manager telepítési modelljét használja, akkor három gyakori lehetősége van a VPN beállítására:
- [Útvonalalapú VPN-átjáró létrehozása az Azure Portal használatával](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Útvonalalapú VPN-átjáró létrehozása a PowerShell használatával](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Útvonalalapú VPN-átjáró létrehozása a CLI használatával](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Mik a Microsoft támogatási szabályzatai a Microsoft kiszolgálószoftverének Azure-alapú virtuális gépeken való futtatásához?

Ezeket a támogatási irányelveket a Microsoft Server szoftvertámogatása a [Microsoft Azure virtuális gépekhez](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)című cikkismerteti.


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>A virtuális gépekhez egyedi azonosítók vannak társítva?

Igen, ha az Azure-ban üzemelteti.  Az Azure egy egyedi azonosítót rendel hozzá, amelyet az Azure virtuálisgép-egyedi azonosítónak neveznek minden új virtuálisgép-erőforráshoz, amely létre jön.  További információért olvassa el az [Azure virtuálisgép-azonosító blogbejegyzést.](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)  Ezt az azonosítót programozott módon is beszerezheti a [List API-n](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)keresztül.


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>A virtuális gép, hogyan kezelheti az egyéni parancsfájl-bővítmény az indítási feladat?

A következő cikk ismerteti, hogyan használhatja az egyéni parancsfájlbővítményt az Azure PowerShell-modul, az Azure Resource Manager-sablonok és a Windows-rendszerek hibaelhárítási lépései használatával: [Egyéni parancsfájlbővítmény Windows-hoz](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>A 32 bites alkalmazások vagy szolgáltatások támogatottak az Azure Marketplace-en?

Általában nem.  A támogatott operációs rendszerek és az Azure virtuális gépek szabványos szolgáltatásai mind 64 bitesek.  Technikai szempontból azonban a legtöbb 64 bites operációs rendszer támogatja az alkalmazások 32 bites verzióinak futtatását a visszamenőleges kompatibilitás érdekében.  A 32 bites alkalmazások virtuálisgép-megoldás részeként történő használata azonban nem támogatott, ezért *nagy mértékben elriasztja a használatát.*  Ehelyett fordítsa újra az alkalmazást 64 bites projektként.

További információkért tekintse át a következő cikkeket:
- [32 bites alkalmazások futtatása](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [32 bites operációs rendszerek támogatása az Azure-beli virtuális gépeken](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft kiszolgálószoftveres támogatás a Microsoft Azure Virtual Machines szolgáltatáshoz](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Minden alkalommal, amikor megpróbálok létrehozni egy képet a Virtuálisgép-azonosítók, kapok a hiba `.VHD is already registered with image repository as the resource` a PowerShell. Korábban nem hoztam létre semmilyen képet, és nem találtam ilyen nevű képet az Azure-ban. Hogyan oldhatom meg a problémát?

Ez a probléma általában akkor fordul elő, ha a felhasználó kiépített egy virtuális gép egy virtuális merevlemez, amely rendelkezik a zárolás.  Ellenőrizze, hogy nincs-e virtuális gép lefoglalva a virtuális merevlemez, majd próbálja meg újra a műveletet.  Ha a probléma továbbra is fennáll, nyisson meg egy támogatási jegyet a [Felhőpartner-portál támogatása című](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal)részben leírtak szerint. 

