---
title: Gyakori problémák a VHD-k létrehozásakor (GYIK) az Azure Marketplace-en
description: A VHD-létrehozással és a kapcsolódó problémákkal kapcsolatos gyakori kérdések.
services: Azure Marketplace
author: HannibalSII
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/02/2018
ms.author: hascipio
ms.reviewer: v-divte; v-miclar
ms.openlocfilehash: 1d4224d8f1f735ecef3d4551e3032b916e81dc3c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826619"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Gyakori problémák a VHD létrehozásakor (GYIK)

A következő gyakori kérdések (GYIK) kiterjednek a virtuális merevlemez (VHD) és a virtuális gépek (VM) létrehozásakor előforduló gyakori problémákra. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Hogyan hozhat létre virtuális gépet a Azure Portal a Premium Storage-ba feltöltött VHD használatával?

Az Azure Marketplace jelenleg nem támogatja a virtuális gépeknek a felügyelt tárolón vagy az Azure Premium Storageban található rendszerképekből történő létrehozását.  További információ ezekről a tárolási lehetőségekről: az [Azure Managed Disks áttekintése](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Használhatja a 2. generációs virtuális gépeket az ajánlatokhoz?

Nem, csak az 1. generációs virtuális merevlemezek támogatottak.  Jelenleg azonban a Microsoft Azure platform csapatával dolgozunk, hogy kivizsgáljuk a 2. generációs virtuális gépek támogatását.  A különbségekről további információt az 1. [vagy 2. generációs virtuális gépek létrehozása a Hyper-V-ben](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) című témakörben talál.


## <a name="how-do-you-change-the-name-of-the-host"></a>Hogyan változtathatja meg a gazdagép nevét?

Nem.  A virtuális gép létrehozása után a felhasználók (beleértve a tulajdonosokat is) nem tudják frissíteni a gazdagép nevét.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Hogyan állíthatja alaphelyzetbe a Távoli asztal szolgáltatást vagy a bejelentkezési jelszavát?

A következő cikkek elmagyarázzák, hogyan végezhető el a Windows-és Linux-alapú virtuális gépek RDS-alaphelyzetbe állítása:   

- [A távoli asztal szolgáltatás vagy annak bejelentkezési jelszavának visszaállítása Windows rendszerű virtuális gépen](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Linux rendszerű virtuális gép jelszavának vagy SSH-kulcsának alaphelyzetbe állítása, az SSH-konfiguráció javítása és a lemez konzisztenciájának vizsgálata a VMAccess-bővítmény használatával](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Hogyan hozhatók új SSH-tanúsítványok?

A tanúsítványok előállítását a cikk a virtuálisgép- [rendszerkép megosztott hozzáférés-aláírási URI-ja beszerzése](./cpp-get-sas-uri.md) című szakaszában ismertetett szakasza [technikai eszközöket hoz létre](./cpp-create-technical-assets.md)a virtuálisgép-ajánlathoz.


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hogyan konfigurálhat virtuális magánhálózati (VPN) hálózatot a virtuális gépekkel való együttműködéshez?

Ha a Azure Resource Manager üzembe helyezési modellt használja, a VPN beállításának három gyakori lehetősége van:
- [Route-alapú VPN-átjáró létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Route-alapú VPN-átjáró létrehozása a PowerShell használatával](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Route-alapú VPN-átjáró létrehozása a parancssori felület használatával](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Mik a Microsoft-kiszolgálói szoftverek Azure-alapú virtuális gépeken való futtatására vonatkozó Microsoft-támogatási szabályzatok?

Ezek a támogatási szabályzatok részletesen ismertetik a [Microsoft Server szoftver Microsoft Azure virtuális gépekkel kapcsolatos támogatását](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)ismertető cikket.


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>A virtuális gépekhez egyedi azonosítók vannak társítva?

Igen, ha az Azure-ban üzemelteti.  Az Azure egy egyedi azonosítót rendel hozzá, amelynek neve az Azure virtuális gép egyedi azonosítója minden létrehozott új virtuálisgép-erőforráshoz.  További információért olvassa el az Azure-beli [virtuális gép egyedi azonosítójának](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)közzététele című blogbejegyzést.  Ezt az azonosítót programozott módon is beszerezheti a [List API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)-n keresztül.


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Egy virtuális gépen hogyan kezelheti az egyéni szkriptek bővítményét az indítási feladatban?

A következő cikk részletesen ismerteti, hogyan használhatók az egyéni szkriptek bővítménye a Azure PowerShell modullal, Azure Resource Manager sablonokkal és a Windows rendszereken végzett hibaelhárítási lépésekkel: [egyéni parancsfájl-bővítmény Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/) rendszeren


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Támogatottak-e a 32 bites alkalmazások vagy szolgáltatások az Azure piactéren?

Általánosságban nem.  Az Azure-beli virtuális gépekhez támogatott operációs rendszerek és standard szolgáltatások mind 64 bitesek.  Technikai szempontból azonban a legtöbb 64 bites operációs rendszer támogatja a visszamenőleges kompatibilitás érdekében az alkalmazások 32 bites verzióinak futtatását.  A virtuálisgép-megoldás részeként azonban a 32 bites alkalmazások használata nem támogatott, *ezért nem ajánlott.*  Ehelyett fordítsa újra az alkalmazást 64 bites projektként.

További információkért tekintse át a következő cikkeket:
- [32 bites alkalmazások futtatása](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [32 bites operációs rendszerek támogatása az Azure Virtual Machines szolgáltatásban](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft kiszolgálószoftveres támogatás a Microsoft Azure Virtual Machines szolgáltatáshoz](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Minden alkalommal, amikor megpróbálok létrehozni egy rendszerképet a virtuális merevlemezekről, a következő hibaüzenet jelenik meg: `.VHD is already registered with image repository as the resource` a PowerShellben. Még nem hozott létre képeket, és nem találtam ilyen nevű képet az Azure-ban. Hogyan a probléma megoldásához?

Ez a probléma általában akkor fordul elő, ha a felhasználó olyan virtuális merevlemezről telepített egy virtuális gépet, amely zárolva van.  Győződjön meg arról, hogy nincs lefoglalva virtuális gép a virtuális merevlemezről, majd próbálja megismételni a műveletet.  Ha a probléma továbbra is fennáll, nyisson meg egy támogatási jegyet, ahogy azt a [Cloud Partner Portal támogatása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal)című témakör ismerteti. 

