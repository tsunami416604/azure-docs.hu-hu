---
title: A Windows rendszerű virtuális asztali hibaelhárítás áttekintése – Azure
description: Áttekintés a Windows rendszerű virtuális asztali bérlői környezet beállítása során felmerülő problémák elhárításához.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: aa4254673d426579a5e5f0e90e389db7c6cadff0
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607354"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Hibaelhárítási áttekintés, visszajelzés és támogatás

Ez a cikk áttekintést nyújt a Windows rendszerű virtuális asztali bérlői környezet beállításakor felmerülő problémákról, és a problémák megoldásához nyújt lehetőségeket.

## <a name="provide-feedback"></a>Visszajelzés küldése

Látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , és beszéljen a Windows Virtual Desktop szolgáltatásról a termék csapatával és az aktív közösség tagjaival.

## <a name="escalation-tracks"></a>Eszkalációs sávok

A következő táblázat segítségével azonosíthatja és megoldhatja a bérlői környezet Távoli asztal-ügyféllel való beállításakor felmerülő problémákat. A bérlő beállítása után az új [diagnosztikai szolgáltatás](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) segítségével azonosíthatja a gyakori forgatókönyvekkel kapcsolatos problémákat.

>[!NOTE]
> Van egy technikai közösségi fórum, amelyről megtudhatja, hogy milyen problémák léptek fel a termék csapatával és az aktív közösség tagjaival kapcsolatban. Látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 

| **Kérdés**                                                            | **Javasolt megoldás**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Bérlő létrehozása                                                    | Ha van Azure-leállás, forduljon az [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/options/). egyéb esetben **Nyisson meg egy támogatási kérést a Windows rendszerű virtuális asztal (számítás) számára**.|
| A Marketplace-sablonok elérése Azure Portal       | Ha van Azure-leállás, forduljon az [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/options/). <br> <br> Az Azure piactéren elérhető Windows rendszerű virtuális asztali sablonok szabadon hozzáférhetők.|
| Azure Resource Manager sablonok elérése a GitHubról                                  | Tekintse meg a [bérlői és a gazdagép-készlet létrehozása](troubleshoot-set-up-issues.md)című témakör "a Windows rendszerű virtuális asztali munkamenetgazda virtuális gépek létrehozása" című szakaszát. Ha a probléma továbbra is megoldatlan, lépjen kapcsolatba a [GitHub támogatási csapatával](https://github.com/contact). <br> <br> Ha a hiba a GitHub-sablonhoz való hozzáférés után fordul elő, forduljon az [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/options/).|
| Session Host Pool Azure Virtual Network (VNET) és Express Route-beállítások               | Forduljon az **Azure támogatási szolgálatához (Hálózatkezelés)** . |
| A munkamenet-gazdagép virtuális gép (VM) létrehozása, ha Azure Resource Manager a Windows rendszerű virtuális asztallal elérhető sablonok nincsenek használatban | Forduljon az **Azure támogatási szolgálatához (számítás)** . <br> <br> A Windows virtuális asztal szolgáltatásban elérhető Azure Resource Manager sablonokkal kapcsolatos problémákért lásd: a Windows virtuális asztali bérlő létrehozása szakasz a [bérlői és a gazdagép-készlet létrehozásához](troubleshoot-set-up-issues.md). |
| Windows rendszerű virtuális asztali munkamenetgazda-környezet kezelése a Azure Portal    | Forduljon az **Azure ügyfélszolgálatához**. <br> <br> A Távoli asztali szolgáltatások/Windows rendszerű virtuális asztali PowerShell használatával kapcsolatos felügyeleti problémákért lásd: [Windows virtuális asztali PowerShell](troubleshoot-powershell.md) vagy **támogatási kérelem megnyitása a Windows rendszerű virtuális asztali környezethez (számítás)** . |
| A Windows rendszerű virtuális asztali konfiguráció kezelése a gazdagépek és az alkalmazások csoportjaihoz kötve (alkalmazás-csoportok)      | Tekintse meg a [Windows rendszerű virtuális asztali PowerShellt](troubleshoot-powershell.md), vagy **Nyisson meg egy támogatási kérést a Windows rendszerű virtuális asztal (számítás) számára**. <br> <br> Ha a probléma a minta grafikus felhasználói felülettel (GUI) van társítva, akkor a Yammer-Közösségnek kell megérkeznie.|
| A távoli asztali ügyfelek működése a Start menüben                                                 | Tekintse meg [Távoli asztal ügyfélkapcsolatokat](troubleshoot-client-connection.md) , és ha ez nem oldja meg a problémát, **Nyisson meg egy támogatási kérést a Windows rendszerű virtuális asztal (számítás) számára**.  <br> <br> Hálózati hiba esetén a felhasználóknak kapcsolatba kell lépniük a hálózati rendszergazdával. |
| Csatlakoztatva, de nincs hírcsatorna                                                                 | A "felhasználó összekapcsolása, de a semmi nem jelenik meg (hírcsatorna nélkül)" szakasz használata a [Távoli asztal ügyfélkapcsolatok](troubleshoot-client-connection.md)esetén – problémamegoldás. <br> <br> Ha a felhasználók egy alkalmazáshoz vannak rendelve, **Nyisson meg egy támogatási kérést a Windows rendszerű virtuális asztal (számítás) számára**. |
| A hírcsatornák felderítésével kapcsolatos problémák a hálózat miatt                                            | A felhasználóknak kapcsolatba kell lépniük a hálózati rendszergazdával. |
| Ügyfelek csatlakoztatása                                                                    | Lásd: [Távoli asztal ügyfélkapcsolatok](troubleshoot-client-connection.md) , és ha ez nem oldja meg a problémát, tekintse meg a [munkamenet-gazda virtuális gép konfigurációját](troubleshoot-vm-configuration.md). |
| A távoli alkalmazások vagy az asztal rugalmassága                                      | Ha a problémák egy adott alkalmazáshoz vagy termékhez vannak kötve, forduljon a termékért felelős csapathoz. |
| Licencelési üzenetek vagy hibák                                                          | Ha a problémák egy adott alkalmazáshoz vagy termékhez vannak kötve, forduljon a termékért felelős csapathoz. |

## <a name="next-steps"></a>További lépések

- A bérlők és a gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [bérlői és az alkalmazáskészletek létrehozását](troubleshoot-set-up-issues.md)ismertető részt.
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításához lásd: [Távoli asztal ügyfélkapcsolatok](troubleshoot-client-connection.md).
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerű virtuális asztali környezet](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
