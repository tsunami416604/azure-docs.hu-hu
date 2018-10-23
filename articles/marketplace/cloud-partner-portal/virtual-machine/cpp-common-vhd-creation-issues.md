---
title: Az Azure Marketplace-en (GYIK) virtuális merevlemez létrehozása során felmerülő gyakori hibák |} A Microsoft Docs
description: Gyakori kérdések a virtuális merevlemez létrehozása és a kapcsolódó problémákkal kapcsolatban.
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: Patrick.Butler
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 10/02/2018
ms.author: hascipio; v-divte; v-miclar
ms.openlocfilehash: 535a947f7a4b9c750d585ce854a14be80c4a135c
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639783"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Virtuális merevlemez létrehozása (GYIK) során felmerülő gyakori hibák

A következő gyakori kérdések (GYIK) virtuális merevlemezt (VHD) és a virtuális gép során észlelt lefedik a gyakori problémák, amelyek virtuális gép (VM) létrehozása. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Hogyan virtuális gép létrehozása a prémium szintű tárolóba feltöltött virtuális merevlemez használata az Azure Portalról

Az Azure Marketplace jelenleg nem támogatja a létrehozása VM-ajánlatok rendszerképeket a felügyelt tárfiókok elhelyezkedhet, vagy az Azure Premium Storage-ból.  További információ a tárolási lehetőségek: [Azure Managed Disks – áttekintés](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) és [nagy teljesítményű Premium Storage és a felügyelt lemezek virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Használhatja a 2. generációs virtuális gépek, amelyek?

Nem, csak generation 1 virtuális merevlemezek használata támogatott.  Azonban jelenleg folyamatban van a Microsoft Azure Platform csapatával vizsgálhatja a 2. generációs virtuális gépek számára.  Különbségekkel kapcsolatos további információkért lásd: [kell hozhatok létre 1 vagy 2. generációs virtuális gépek a Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Hogyan változnak a gazdagép neve?

Nem.  Virtuális gép létrehozása után a felhasználók (ideértve a tulajdonosok) a gazdagép neve nem lehet frissíteni.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Hogyan hajtsa végre a távoli asztali szolgáltatás vagy alaphelyzetbe a bejelentkezési jelszavát?

A következő cikkek ismertetik a Windows - és Linux-alapú virtuális gépek távoli asztali szolgáltatások alaphelyzetbe állítást:   

- [A távoli asztal szolgáltatás vagy annak bejelentkezési jelszavának visszaállítása Windows rendszerű virtuális gépen](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Linux rendszerű virtuális gép jelszó vagy SSH-kulcs visszaállítása, javítsa ki az SSH-konfigurációt, és a VMAccess bővítmény használatával lemez-konzisztencia ellenőrzése](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Hogyan hoz létre új SSH-tanúsítványok?

A cikk ezen tanúsítványok generációja [Get közös hozzáférésű jogosultságkód URI-t a Virtuálisgép-lemezkép](./cpp-get-sas-uri.md) az ezt követő szakaszban [hozzon létre egy Virtuálisgép-ajánlat technikai eszközök](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hogy hogyan konfigurálhat egy virtuális magánhálózati (VPN) virtuális gépek használata?

Az Azure Resource Manager üzemi modell használatakor, majd három gyakori lehetősége van a virtuális Magánhálózatok beállításáról:
- [Az Azure portal használatával útvonalalapú VPN-átjáró létrehozása](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [PowerShell-lel az útvonalalapú VPN-átjáró létrehozása](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [CLI-vel az útvonalalapú VPN-átjáró létrehozása](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Mik a Microsoft támogatási házirendjeit a Microsoft server szoftvert futtató Azure-alapú virtuális gépeken?

E házirendek részletes leírást talál a cikk támogató [Microsoft kiszolgálószoftveres támogatás a Microsoft Azure virtuális gépek](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Rendelkezik a virtuális gépek a hozzájuk rendelt egyedi azonosítóként?

Igen, ha az Azure-ban üzemeltetett.  Az Azure hozzárendeli egy egyedi azonosítója, az Azure virtuális gép egyedi azonosítója, minden egyes létrehozott új virtuális gép erőforrás neve.  További információkért olvassa el a blogbejegyzést [Azure virtuális gép egyedi Azonosítóját](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Ez az azonosító programozott módon keresztül is beszerezhető a [lista API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>A virtuális gép hogyan kezelhetők az egyéni szkriptek bővítményét az indítási feladat?

A következő cikk részletesen bemutatja az egyéni Szkriptbővítmény használatával, az Azure PowerShell-modul, az Azure Resource Manager-sablonok és hibaelhárítási lépéseket a Windows rendszerek adatainak használata: [Custom Script bővítmény a Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Azok a 32 bites alkalmazások és szolgáltatások támogatott az Azure piactéren?

Általában nem.  A támogatott operációs rendszerek és a standard szintű szolgáltatások az Azure virtuális gépek minden 64 bites.  Technikai szempontból, azonban leginkább a 64 bites operációs rendszerek támogatják a visszamenőleges kompatibilitás érdekében az alkalmazások 32 bites verzióját futtató.  Azonban a 32 bites alkalmazások használják, a VM-megoldás részét képező nem támogatott, és ezért *erősen javasolt*.  Ehelyett újra az alkalmazást olyan 64 bites projekt.

További információkért tekintse át a következő cikkeket:
- [32 bites alkalmazások futtatása](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Az Azure-beli virtuális gépek 32 bites operációs rendszerek támogatása](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft kiszolgálószoftveres támogatás a Microsoft Azure virtuális gépekhez](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Minden alkalommal, amikor megpróbálom-lemezkép készítése saját virtuális merevlemezek, a hibaüzenetet kapom `.VHD is already registered with image repository as the resource` a PowerShellben. Nem tudok hozott létre bármilyen kép előtt sem volt található ilyen nevű képet az Azure-ban. Hogyan oldhatom fel probléma?

A probléma általában akkor fordul elő, ha a felhasználó egy olyan virtuális Merevlemezt a zárolás, a virtuális Gépet.  Győződjön meg arról, hogy nincs virtuális gép foglalja le a VHD-t, és próbálkozzon újra a művelettel.  Ha a probléma nem szűnik meg, nyisson egy támogatási jegyet, amint azt [Cloud Partner Portalon támogatása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

