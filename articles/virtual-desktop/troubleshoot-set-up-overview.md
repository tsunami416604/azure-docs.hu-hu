---
title: A Windows virtuális asztal hibaelhárításának áttekintése – Azure
description: A Windows virtuális asztal bérlői környezetének beállítása során felmerülő hibák elhárításának áttekintése.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127404"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Hibaelhárítási áttekintés, visszajelzés és támogatás

Ez a cikk áttekintést nyújt a Windows virtuális asztali bérlői környezet beállításakor felmerülő problémákról, és a problémák megoldásának módjait tartalmazza.

## <a name="provide-feedback"></a>Visszajelzés küldése

Látogasson el a [Windows Virtual Desktop Tech Közösség,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) hogy megvitassák a Windows Virtual Desktop szolgáltatás a termék csapat és az aktív közösség tagjai.

## <a name="escalation-tracks"></a>Eszkalációs pályák

Az alábbi táblázat segítségével azonosíthatja és megoldhatja azokat a problémákat, amelyekkel a távoli asztali ügyfél használatával egy bérlői környezet beállításakor találkozhat. Miután a bérlő beállítása, használhatja az új diagnosztikai szolgáltatás problémák [azonosítására](diagnostics-role-service.md) a gyakori forgatókönyvek.

>[!NOTE]
> Van egy Tech Közösségi fórum, amely akkor látogasson el, hogy megvitassák a kérdéseket a termék csapat és az aktív közösség tagjai. A vita megkezdéséhez látogasson el a [Windows Virtual Desktop Tech Közösségbe.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

| **Probléma**                                                            | **Javasolt megoldás**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Windows virtuális asztal bérlő létrehozása                                                    | Ha van egy Azure-kimaradás, [nyisson meg egy Azure-támogatási kérelmet;](https://azure.microsoft.com/support/create-ticket/) ellenkező esetben [nyisson meg egy Azure-támogatási kérelmet,](https://azure.microsoft.com/support/create-ticket/)válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, válassza a Telepítés **lehetőséget** a probléma típusához, majd válassza a Windows virtuális **asztal bérlőjének létrehozása** lehetőséget a probléma altípushoz.|
| Marketplace-sablonok elérése az Azure Portalon       | Ha van egy Azure-kimaradás, [nyisson meg egy Azure-támogatási kérelmet.](https://azure.microsoft.com/support/create-ticket/) <br> <br> Az Azure Marketplace Windows virtuális asztal sablonjai szabadon elérhetők.|
| Azure Resource Manager-sablonok elérése a GitHubról                                  | Lásd a Bérlő és a gazdakészlet létrehozásának [Windows virtuális asztal munkamenetgazda virtuális gépeinek létrehozása](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) című [szakaszát.](troubleshoot-set-up-issues.md) Ha a probléma továbbra is fennáll, lépjen kapcsolatba a [GitHub támogatási csapatával.](https://github.com/contact) <br> <br> Ha a hiba a sablon GitHubon való elérése után jelentkezik, forduljon az [Azure-támogatáshoz.](https://azure.microsoft.com/support/create-ticket/)|
| Az Azure Virtual Network (VNET) és az Express Route beállításai               | [Nyisson meg egy Azure-támogatási kérelmet,](https://azure.microsoft.com/support/create-ticket/)majd válassza ki a megfelelő szolgáltatást (a Hálózatkategória alatt). |
| Munkamenet-gazdagép virtuális gép (VM) létrehozása, ha a Windows virtuális asztalhoz mellékelt Azure Resource Manager-sablonok nincsenek használatban | [Nyisson meg egy Azure-támogatási kérelmet,](https://azure.microsoft.com/support/create-ticket/)majd válassza a szolgáltatás **Windows-alapú virtuális gép** lehetőséget. <br> <br> A Windows virtuális asztalhoz mellékelt Azure Resource Manager-sablonokkal kapcsolatos problémákról a [Bérlő és a gazdagépkészlet létrehozásának](troubleshoot-set-up-issues.md)Windows virtuális asztal bérlői részének létrehozása című témakörben olvashat. |
| A Windows Virtual Desktop munkamenetgazda-környezet kezelése az Azure Portalról    | [Nyisson meg egy Azure-támogatási kérelmet.](https://azure.microsoft.com/support/create-ticket/) <br> <br> A Távoli asztali szolgáltatások/Windows virtuális asztali PowerShell használata kori felügyeleti problémákról a [Windows virtuális asztali PowerShell](troubleshoot-powershell.md) című témakörben vagy [az Azure támogatási kérelmében,](https://azure.microsoft.com/support/create-ticket/)a **Windows virtuális asztal** a szolgáltatáshoz lehetőséget kap, válassza a problématípus **konfigurációja és kezelése** lehetőséget, majd válassza a bérlő konfigurálása a probléma altípushoz a **PowerShell használatával beállításával lehetőséget.** |
| A Gazdagépkészletekhez és alkalmazáscsoportokhoz (alkalmazáscsoportokhoz) kötött Windows virtuális asztal konfigurációjának kezelése      | Lásd: [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md), vagy [nyisson meg egy Azure támogatási kérelmet,](https://azure.microsoft.com/support/create-ticket/)válassza a **Windows virtuális asztal** a szolgáltatás, majd válassza ki a megfelelő probléma típusát.|
| FSLogix profiltárolók telepítése és kezelése | Lásd: [Hibaelhárítási útmutató az FSLogix-termékekhez,](/fslogix/fslogix-trouble-shooting-ht/) és ha ez nem oldja meg a problémát, [nyisson meg egy Azure támogatási kérelmet,](https://azure.microsoft.com/support/create-ticket/)válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, válassza az **FSLogix** lehetőséget a probléma típusához, majd válassza ki a megfelelő probléma altípust. |
| A távoli asztali ügyfelek hibásan működnek az indításkor                                                 | Lásd: [A Távoli asztali ügyfél hibaelhárítása,](troubleshoot-client.md) és ha ez nem oldja meg a problémát, [Nyisson meg egy Azure támogatási kérelmet,](https://azure.microsoft.com/support/create-ticket/)válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, majd válassza a Távoli asztali ügyfelek **lehetőséget** a probléma típusához.  <br> <br> Ha hálózati probléma rólvan szó, a felhasználóknak kapcsolatba kell lépniük a hálózati rendszergazdával. |
| Csatlakoztatva, de nincs hírcsatorna                                                                 | Hibaelhárítás a [Felhasználó csatlakozik, de semmi sem jelenik meg (nincs hírcsatorna)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) szakasza [Windows Virtual Desktop szolgáltatás kapcsolatok](troubleshoot-service-connection.md). <br> <br> Ha a felhasználók egy alkalmazáscsoporthoz vannak rendelve, [nyisson meg egy Azure-támogatási kérelmet,](https://azure.microsoft.com/support/create-ticket/)válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, majd válassza a Távoli asztali ügyfelek **lehetőséget** a probléma típusához. |
| A hírcsatorna-felderítési problémák a hálózat miatt                                            | A felhasználóknak kapcsolatba kell lépniük a hálózati rendszergazdával. |
| Ügyfelek összekapcsolása                                                                    | Olvassa el a [Windows virtuális asztali szolgáltatás kapcsolatait,](troubleshoot-service-connection.md) és ha ez nem oldja meg a problémát, olvassa el a [Munkamenetgazda virtuálisgép-konfigurációját.](troubleshoot-vm-configuration.md) |
| Távoli alkalmazások vagy asztali számítógépek válaszképessége                                      | Ha a problémák egy adott alkalmazáshoz vagy termékhez vannak kötve, forduljon az adott termékért felelős csapathoz. |
| Üzenetek vagy hibák licencelése                                                          | Ha a problémák egy adott alkalmazáshoz vagy termékhez vannak kötve, forduljon az adott termékért felelős csapathoz. |
| Problémák a Windows Virtuális asztali eszközök használatakor a GitHubon (Azure Resource Manager-sablonok, diagnosztikai eszköz, felügyeleti eszköz) | A problémák jelentéséhez tekintse meg [az Azure Resource Manager-sablonok at a Távoli asztali szolgáltatásokhoz.](https://github.com/Azure/RDS-Templates/blob/master/README.md) |

## <a name="next-steps"></a>További lépések

- A bérlői és gazdagépkészlet Windows virtuális asztali környezetben való létrehozása során felmerülő problémák elhárításához olvassa el a [Bérlő és a gazdakészlet létrehozása című témakört.](troubleshoot-set-up-issues.md)
- A Windows virtuális asztal virtuális gépének konfigurálása során felmerülő problémák elhárításához olvassa el a [Munkamenetgazda virtuálisgép-konfiguráció című témakört.](troubleshoot-vm-configuration.md)
- A Windows Virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításáról a Windows Virtuális asztal szolgáltatás kapcsolatai című [témakörben található.](troubleshoot-service-connection.md)
- A Távoli asztali ügyfelekkel kapcsolatos problémák elhárításáról [a Távoli asztali ügyfél – Problémaeltetés – problémamegoldás](troubleshoot-client.md)
- A PowerShell Windows virtuális asztallal való használatakor felmerülő problémák elhárításáról a [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)című témakörben található.
- A szolgáltatásról a [Windows virtuális asztali környezet című témakörben](environment-setup.md)olvashat bővebben.
- Ha hibaelhárítási oktatóanyagon szeretne átmenni, olvassa el [az Oktatóanyag: Erőforrás-kezelő sablontelepítésekkel kapcsolatos hibaelhárítása című témakört.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
- A naplózási műveletekről a [Naplózási műveletek az Erőforrás-kezelővel (Audit operations with Resource Manager) témakörben](../azure-resource-manager/management/view-activity-logs.md)olvashat.
- A központi telepítés során előforduló hibák meghatározására irányuló műveletekről a [Telepítési műveletek megtekintése című témakörben](../azure-resource-manager/templates/deployment-history.md)olvashat.
