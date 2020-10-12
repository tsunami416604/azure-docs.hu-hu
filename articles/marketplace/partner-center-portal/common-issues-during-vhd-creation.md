---
title: Gyakori problémák a VHD létrehozásakor (GYIK)
description: Gyakori kérdések a virtuális merevlemezek (VHD-k) létrehozásakor előforduló gyakori problémákról.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: f4d30cdb931d6523eba3aac003caeee38a8c024d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653528"
---
# <a name="common-issues-during-vhd-creation"></a>Gyakori problémák a VHD létrehozása során

Ezek a gyakori kérdések (GYIK) a virtuális merevlemez (VHD) Azure-beli virtuálisgép-ajánlathoz való létrehozásakor előforduló gyakori problémákra vonatkoznak.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Hogyan hozzon létre egy virtuális gépet a Azure Portal egy Premium Storage-beli virtuális merevlemez használatával?

Az Azure Marketplace jelenleg nem támogatja virtuálisgép-ajánlatok létrehozását a felügyelt tárolón vagy az Azure Premium Storage-ból származó képekből. Részletekért lásd: az [Azure Managed Disks áttekintése](../../virtual-machines/managed-disks-overview.md).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Használhatok 2. generációs virtuális gépeket az ajánlatokhoz?

Nem, csak az 1. generációs virtuális merevlemezek támogatottak. Jelenleg azonban a Microsoft Azure platform csapatával dolgozunk, hogy kivizsgáljuk a 2. generációs virtuális gépek támogatását. Részletekért lásd: [1. vagy 2. generációs virtuális gépek létrehozása a Hyper-V-ben?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Hogyan módosítja a gazdagép nevét?

Ez nem kezelhető. A virtuális gép létrehozása után a felhasználók (beleértve a tulajdonosokat is) nem tudják frissíteni az állomásnevet.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Hogyan alaphelyzetbe állítani a Távoli asztal szolgáltatást vagy a bejelentkezési jelszavát?

Ezek a cikkek a Windows-és a Linux-alapú virtuális gépekhez készült RDS-visszaállításokat ismertetik:

* [A távoli asztali szolgáltatás, illetve a hozzá tartozó bejelentkezési jelszó visszaállítása Windows rendszerű virtuális gépen](/azure/virtual-machines/troubleshooting/reset-rdp)
* [Linux rendszerű virtuális gép jelszavának vagy SSH-kulcsának alaphelyzetbe állítása, az SSH-konfiguráció javítása és a lemez konzisztenciájának vizsgálata a VMAccess-bővítmény használatával](/azure/virtual-machines/extensions/vmaccess)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Hogyan új SSH-tanúsítványokat készíteni?

A tanúsítványok létrehozása az Azure-beli virtuálisgép- [rendszerkép minősítésének](https://aks.ms/CertifyVMimage)ismertetése.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hogyan a virtuális magánhálózat (VPN) konfigurálását a virtuális gépekkel való munkavégzéshez?

Ha a Azure Resource Manager üzembe helyezési modellt használja, három lehetőség közül választhat:

* [Route-alapú VPN-átjáró létrehozása a Azure Portal használatával](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [Route-alapú VPN-átjáró létrehozása Azure PowerShell használatával](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [Route-alapú VPN-átjáró létrehozása a parancssori felület használatával](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Mik a Microsoft-kiszolgálói szoftverek Azure-alapú virtuális gépeken való futtatására vonatkozó Microsoft-támogatási szabályzatok?

[Az Microsoft Azure Virtual Machines szolgáltatással kapcsolatos](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)további részletekért tekintse meg a Microsoft Server szoftver támogatását.

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>A virtuális gépekhez egyedi azonosítók vannak társítva?

Igen, ha az Azure-ban üzemelteti. Az Azure minden újonnan létrehozott virtuálisgép-erőforráshoz hozzárendel egy egyedi azonosítót, az Azure-beli [virtuális gép egyedi azonosítóját](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/). Részletekért lásd az Azure-beli virtuális gépek egyedi AZONOSÍTÓját. Ezt az azonosítót a [List API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)-n keresztül is lekérheti.

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Egy virtuális gépen Hogyan kezelhetem az egyéni szkriptek bővítményét az indítási feladatban?

További információ az egyéni szkriptek bővítmény használatáról a Azure PowerShell modullal, Azure Resource Manager sablonokkal és a Windows rendszerekkel kapcsolatos hibaelhárítási lépésekkel kapcsolatban: [egyéni parancsfájl-bővítmény a Windowshoz](/azure/virtual-machines/extensions/custom-script-windows).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Támogatottak-e a 32 bites alkalmazások vagy szolgáltatások az Azure Marketplace-en?

Általában nem. Az Azure-beli virtuális gépekhez támogatott operációs rendszerek és standard szolgáltatások mind 64 bitesek. Bár a legtöbb 64 bites operációs rendszer támogatja az alkalmazások 32 bites verzióit a visszamenőleges kompatibilitás érdekében, az 32 bites alkalmazások a virtuálisgép-megoldás részeként való használata nem támogatott és nem ajánlott. Hozza létre újra az alkalmazást 64 bites projektként.

További információért lásd a következő cikkeket:

* [32 bites alkalmazások futtatása](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [32 bites operációs rendszerek támogatása az Azure-beli virtuális gépeken](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft kiszolgálószoftveres támogatás a Microsoft Azure Virtual Machines szolgáltatáshoz](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Hiba: a virtuális merevlemez már regisztrálva van a rendszerkép-tárházban erőforrásként

Minden alkalommal, amikor megpróbálok létrehozni egy rendszerképet a virtuális merevlemezekről, a következő hibaüzenet jelenik meg: "a VHD már regisztrálva van a rendszerkép-tárházban erőforrásként" a Azure PowerShellban. Nem hoztam létre a képet, és nem találtam ilyen nevű képet az Azure-ban. Hogyan oldhatom meg ezt?

Ez a probléma általában akkor jelenik meg, ha olyan virtuális merevlemezről hozta létre a virtuális gépet, amelynek van zárolása. Győződjön meg arról, hogy nincs lefoglalva virtuális gép a virtuális merevlemezről, majd próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, nyisson meg egy támogatási jegyet. Lásd: [a partner Center támogatása](support.md).
