---
title: Windows 7 virtuális gép telepítése Windows Virtual Desktop – Azure
description: A Windows 7 virtuális gép konfigurálása és telepítése a Windows Virtuális asztalon.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158dd9d6a38cc1aaf7667c0b16518e23b3f7cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366683"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Windows 7 rendszerű virtuális gép üzembe helyezése a Windows Virtual Desktopban

A Windows 7 virtuális gép (VM) Windows virtuális asztalon történő központi telepítésének folyamata némileg eltér a Windows későbbi verzióit futtató virtuális gépektől. Ez az útmutató bemutatja, hogyan telepítheti a Windows 7 rendszert.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, kövesse a [PowerShell-készlettel rendelkező gazdakészlet létrehozása](create-host-pools-powershell.md) című útmutató utasításait egy gazdakészlet létrehozásához. Ezt követően kövesse az [Azure Marketplace-en a gazdagépkészletek létrehozása](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) című útmutató utasításait, és rendeljen hozzá egy vagy több felhasználót az asztali alkalmazáscsoporthoz.

## <a name="configure-a-windows-7-virtual-machine"></a>Windows 7 rendszerű virtuális gép konfigurálása

Miután elvégezte az előfeltételeket, készen áll a Windows 7 virtuális gép konfigurálására a Windows virtuális asztalon való telepítéshez.

Windows 7 virtuális gép beállítása Windows Virtual Desktop rendszeren:

1. Jelentkezzen be az Azure Portalra, és keresse meg a Windows 7 Enterprise lemezképet, vagy töltse fel saját testreszabott Windows 7 Enterprise (x64) lemezképét.  
2. Telepítsen egy vagy több virtuális gépet a Windows 7 Enterprise operációs rendszerrel. Győződjön meg arról, hogy a virtuális gépek engedélyezik a Távoli asztali protokoll (RDP) protokollt (a TCP/3389 portot).
3. Csatlakozzon a Windows 7 Enterprise állomáshoz az RDP használatával, és hitelesítse magát a központi telepítés konfigurálása során megadott hitelesítő adatokkal. 
4. Adja hozzá a "Távoli asztali felhasználó" csoporthoz az RDP-vel rendelkező állomáshoz való csatlakozáskor használt fiókot. Ha ezt nem teszi meg, előfordulhat, hogy nem tud csatlakozni a virtuális géphez, miután csatlakozott az Active Directory-tartományhoz.
5. Nyissa meg a Windows Update webhelyet a virtuális gépen.
6. Telepítse az összes Windows-frissítést a Fontos kategóriába.
7. Telepítse az összes Windows-frissítést a Választható kategóriába (kivéve a nyelvi csomagokat). Ez telepíti a Távoli asztali protokoll 8.0-s frissítését ([KB2592687),](https://www.microsoft.com/download/details.aspx?id=35387)amelyet el kell végeznie.
8. Nyissa meg a Helyi csoportházirend-szerkesztőt, és keresse meg a **Számítógép konfigurációs** > **felügyeleti sablonjait** > **a Windows-összetevők** > távoli asztali**szolgáltatások** > **távoli asztali munkamenetgazda** > távoli**munkamenetkörnyezetében.**
9. Engedélyezze a Távoli asztali protokoll 8.0 házirendet.
10. Csatlakozzon ehhez a virtuális géphez az Active Directory-tartományhoz.
11. Indítsa újra a virtuális gépet a következő parancs futtatásával:
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. Kövesse az [itt](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) található utasításokat a regisztrációs jogkivonat beszerzéséhez.
13. [Töltse le a Windows Virtual Desktop Agent for Windows 7 rendszert.](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)
14. [Töltse le a Windows Virtual Desktop Agent Manager for Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)rendszert.
15. Nyissa meg a Windows virtuális asztali ügynök telepítőt, és kövesse az utasításokat. Amikor a rendszer kéri, adja meg a 12.
16. Nyissa meg a Windows virtuális asztal telepítőt, és kövesse az utasításokat.
17. Szükség esetén blokkolja a TCP/3389 portot a virtuális gép közvetlen távoli asztali protokoll-hozzáférésének eltávolításához.

## <a name="next-steps"></a>További lépések

A Windows virtuális asztal központi telepítése készen áll a használatra. A kezdéshez [töltse le a Windows virtuális asztali ügyfél legújabb verzióját.](https://aka.ms/wvd/clients/windows)

A Windows 7 windows os virtual desktop rendszeren futó windows 7-ével kapcsolatos ismert problémák és hibaelhárítási utasítások listáját a [Windows 7 virtuális gépek hibáinak elhárítása a Windows Virtual Desktop rendszerben című témakörben találja.](troubleshoot-windows-7-vm.md)
