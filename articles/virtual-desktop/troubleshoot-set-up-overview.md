---
title: Windows virtuális asztal áttekintése, visszajelzések és az Azure-támogatás – hibaelhárítás
description: A virtuális asztali Windows-bérlős környezet beállítása közben hibák elhárításának áttekintése.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 8e344d6908ba19f8e2294c7777b9c1016eafaf52
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927642"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Hibaelhárítás – áttekintés, visszajelzés és támogatás

Ez a cikk a problémák jelentkezhetnek, ha egy Windows virtuális asztal bérlői környezet beállítására, és a problémák megoldásához módot nyújt áttekintést.

## <a name="provide-feedback"></a>Visszajelzés küldése

A Microsoft jelenleg nem tart támogatási esetek Windows virtuális asztal pedig előzetes verzióban érhető el. Látogasson el a [Windows virtuális asztal technikai Közösség](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) vitatni a virtuális asztali Windows-szolgáltatás a termékért felelős csoport és az aktív Közösség tagjai.

## <a name="escalation-tracks"></a>Eszkalációs nyomon követi

Az alábbi táblázat segítségével azonosíthatja, és a egy távoli asztali ügyfél bérlői-környezet beállítása során felmerülő problémák megoldásához.

>[!NOTE]
>A Microsoft jelenleg nem tart támogatási esetek Windows virtuális asztal pedig előzetes verzióban érhető el. Minden alkalommal, amikor Windows virtuális asztal támogatási nevezzük, nyissa meg a szakmai közösségi fórum most. Látogasson el a [Windows virtuális asztal technikai Közösség](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) való vitatni a kérdéseket a termékért felelős csoport és az aktív Közösség tagjai. El kell hárítania egy támogatási problémát, ha a tevékenység azonosítója és tartalmazniuk hozzávetőleges időkeretet esetén a probléma lépett fel.

| **A probléma**                                                            | **Javasolt megoldás**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Bérlő létrehozása                                                    | Ha egy Azure-szolgáltatáskiesés, lépjen kapcsolatba a [Azure-támogatási](https://azure.microsoft.com/support/options/); ellenkező esetben forduljon **virtuális asztal távoli asztali szolgáltatások/Windows támogatási**.|
| Elérése a piactér-sablonok az Azure Portalon       | Ha egy Azure-szolgáltatáskiesés, lépjen kapcsolatba a [Azure-támogatási](https://azure.microsoft.com/support/options/). <br> <br> Az Azure Marketplace-en Windows virtuális asztal sablonok szabadon elérhetők.|
| A GitHub elérése az Azure Resource Manager-sablonok                                  | A "Creating a Windows virtuális asztali munkamenetgazda virtuális gépek" című szakaszában talál [bérlő és a gazdagép-készlet létrehozása](troubleshoot-set-up-issues.md). Ha a probléma továbbra is feloldatlan, forduljon a [GitHub támogatási csapatának](https://github.com/contact). <br> <br> Ha a hiba után elérése a GitHub-sablon jelenik meg, forduljon a [Azure-támogatási](https://azure.microsoft.com/support/options/).|
| Munkamenetgazda készlet az Azure Virtual Network (VNET) és az Express Route beállításai               | Kapcsolattartó **(hálózat) az Azure-támogatás**. |
| Munkamenet gazdagép készlet virtuális gép (VM) létrehozása, amikor nem használja a Windows virtuális asztal biztosított Azure Resource Manager-sablonok | Kapcsolattartó **az Azure-támogatás (számítás)**. <br> <br> A problémák az Azure Resource Manager-sablonokkal a virtuális asztali Windows, Windows virtuális asztal létrehozása bérlő című szakaszában talál [bérlő és a gazdagép-készlet létrehozása](troubleshoot-set-up-issues.md). |
| Windows virtuális asztali állomás környezete kezelése az Azure Portalról    | Kapcsolattartó **az Azure-támogatás**. <br> <br> Felügyeleti problémák esetén a távoli asztali szolgáltatások/Windows virtuális asztal PowerShell használatával, lásd: [Windows virtuális asztal PowerShell](troubleshoot-powershell.md) , vagy forduljon a **virtuális asztal távoli asztali szolgáltatások/Windows támogatási csoport** . |
| Windows virtuális asztal konfigurációjának kezelésével gazdagép készletek és application (alkalmazás) csoportok kötve      | Lásd: [Windows virtuális asztal PowerShell](troubleshoot-powershell.md), vagy lépjen kapcsolatba a **virtuális asztal távoli asztali szolgáltatások/Windows támogatási csapatának**. <br> <br> Ha a minta grafikus felhasználói felülettel kapcsolatos problémák vannak társítva, fel a kapcsolatot a Yammer-Közösség.|
| Távoli asztali ügyfelek hibásan működik, a Start menüben                                                 | Lásd: [távoli asztali kapcsolatok](troubleshoot-client-connection.md) , ha nem oldja meg a probléma, forduljon a **virtuális asztal távoli asztali szolgáltatások/Windows támogatási csapatának**.  <br> <br> Ha hálózati probléma, a felhasználóknak kell lépjen kapcsolatba a rendszergazdával. |
| De nem hírcsatorna csatlakoztatva                                                                 | Hibaelhárítás a használatával a "felhasználó kapcsolódik, de semmi nem jelenik meg (nincs hírcsatorna)" szakaszában [távoli asztali kapcsolatok](troubleshoot-client-connection.md). <br> <br> Ha a felhasználók egy alkalmazás csoporthoz vannak rendelve, eszkaláció a **virtuális asztal távoli asztali szolgáltatások/Windows támogatási csapatának**. |
| Felderítési problémák miatt a hálózati csatorna                                            | A felhasználóknak kell lépjen kapcsolatba a rendszergazdával. |
| Csatlakozó ügyfelek                                                                    | Lásd: [távoli asztali kapcsolatok](troubleshoot-client-connection.md) , és ha ez nem oldja meg a problémát, tekintse meg [munkamenetgazda virtuális gép konfigurálása](troubleshoot-vm-configuration.md). |
| Távoli alkalmazások és asztali válaszideje                                      | Ha egy adott alkalmazás vagy termék problémák vannak társítva, lépjen kapcsolatba a termék felelős csapat. |
| Licencelési üzenetek vagy a hibák                                                          | Ha egy adott alkalmazás vagy termék problémák vannak társítva, lépjen kapcsolatba a termék felelős csapat. |

## <a name="next-steps"></a>További lépések

- Windows virtuális asztali környezetben egy bérlő és a gazdagép-készlet létrehozása során problémák hibaelhárítása: [bérlő és a gazdagép-készlet létrehozása](troubleshoot-set-up-issues.md).
- Egy virtuális gépet (VM) konfigurálása a Windows virtuális asztal során problémák hibaelhárítása: [munkamenetgazda virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- Windows virtuális asztali kapcsolatok problémáinak hibaelhárítása: [távoli asztali kapcsolatok](troubleshoot-client-connection.md).
- A virtuális asztal Windows PowerShell-lel kapcsolatos problémák elhárításához lásd: [Windows virtuális asztal PowerShell](troubleshoot-powershell.md).
- Az előzetes verziójú szolgáltatások kapcsolatos további információkért lásd: [Windows Desktop előzetes verziójú környezet](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Nyissa meg a hibaelhárítás az oktatóanyagot, tekintse meg [oktatóanyag: Resource Manager-sablon üzemelő példányok hibaelhárítása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Naplózási műveletek kapcsolatos további információkért lásd: [auditálási műveletek a Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Üzembe helyezés során a hibák megállapításához műveleteivel kapcsolatos tudnivalókért lásd: [üzembehelyezési műveletek megtekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).