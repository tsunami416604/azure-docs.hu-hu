---
title: A Windows rendszerű virtuális asztali hibaelhárítás áttekintése – Azure
description: Áttekintés a Windows rendszerű virtuális asztali környezet beállítása során felmerülő problémák elhárításához.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 07857feb2e5552429c445b22c4c4b2f121f29a93
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976489"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Hibaelhárítási áttekintés, visszajelzés és támogatás

>[!IMPORTANT]
>Ez a tartalom a Spring 2020 frissítésre vonatkozik Azure Resource Manager Windows rendszerű virtuális asztali objektumokkal. Ha a Windows rendszerű virtuális 2019 asztalt Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).
>
> A Windows rendszerű virtuális asztali Spring 2020 frissítése jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk áttekintést nyújt a Windows rendszerű virtuális asztali környezet beállítása során felmerülő problémákról, és a problémák megoldásához nyújt lehetőségeket.

## <a name="report-issues-during-public-preview"></a>Problémák jelentése a nyilvános előzetes verzióban

Ha a Spring 2020 kiadásban nyilvános előzetes verzióban szeretne problémákat jelenteni vagy javaslatokat készíteni, látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). A technikai Közösség segítségével megismerheti az ajánlott eljárásokat, illetve az új funkciókra vonatkozó javaslatokat és szavazhat. Ha a nyilvános előzetes kiadással kapcsolatos problémát jelent, ügyeljen arra, hogy a probléma típusát a **Spring 2020 Update (előzetes verzió)** címkével adja meg.

Ha olyan bejegyzést tesz, amely segítséget kér vagy új szolgáltatást javasol, a lehető legrészletesebben írja le a témakört. A részletes információk segítséget nyújthatnak más felhasználóknak a kérdés megválaszolásához vagy az Ön által szavazásra javasolt funkció megismeréséhez.

## <a name="escalation-tracks"></a>Eszkalációs sávok

Mielőtt bármit végrehajtaná, győződjön meg róla, hogy az Azure-beli szolgáltatás megfelelően fut-e, és [Azure Service Health](https://azure.microsoft.com/features/service-health/) ellenőrizze az Azure-beli [állapot lapot](https://status.azure.com/status) .

A következő táblázat segítségével azonosíthatja és megoldhatja a környezetek Távoli asztal-ügyfél használatával történő beállításakor felmerülő problémákat. A környezet beállítása után az új [diagnosztikai szolgáltatás](diagnostics-role-service.md) segítségével azonosíthatja a gyakori forgatókönyvek problémáit.

| **Probléma**                                                            | **Javasolt megoldás**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Session Host Pool Azure Virtual Network (VNET) és Express Route-beállítások               | [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), majd válassza ki a megfelelő szolgáltatást (a hálózatkezelés kategóriában). |
| A munkamenet-gazdagép virtuális gép (VM) létrehozása, ha Azure Resource Manager a Windows rendszerű virtuális asztallal elérhető sablonok nincsenek használatban | [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), majd válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz. <br> <br> A Windows rendszerű virtuális asztal szolgáltatásban elérhető Azure Resource Manager sablonokkal kapcsolatos problémákért lásd: a gazdagép-készletek [létrehozásának](troubleshoot-set-up-issues.md)Azure Resource Manager sablon hibái szakasza. |
| Windows rendszerű virtuális asztali munkamenetgazda-környezet kezelése a Azure Portal    | [Nyisson meg egy Azure-támogatási kérelmet](https://azure.microsoft.com/support/create-ticket/). <br> <br> A Távoli asztali szolgáltatások/Windows rendszerű virtuális asztali PowerShell használatakor a felügyeleti problémákért lásd: a [Windows virtuális asztali PowerShell](troubleshoot-powershell.md) vagy [egy Azure-támogatási kérelem megnyitása](https://azure.microsoft.com/support/create-ticket/), válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, válassza a **konfiguráció és kezelés** lehetőséget a probléma típusa beállításnál, majd válassza a probléma altípushoz a **környezet konfigurálása a PowerShell használatával** című témakört. |
| A Windows rendszerű virtuális asztali konfiguráció kezelése a gazdagépek és az alkalmazások csoportjaihoz kötve (alkalmazás-csoportok)      | Tekintse meg a [Windows rendszerű virtuális asztali PowerShellt](troubleshoot-powershell.md), vagy [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), válassza a szolgáltatáshoz a **Windows virtuális asztal** lehetőséget, majd válassza ki a megfelelő problémát.|
| FSLogix-profilok tárolóinak üzembe helyezése és kezelése | Tekintse meg a [FSLogix termékekkel kapcsolatos hibaelhárítási útmutatót](/fslogix/fslogix-trouble-shooting-ht/) , és ha ez nem oldja meg a problémát, [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, válassza a **FSLogix** lehetőséget, majd válassza ki a megfelelő problémát altípust. |
| A távoli asztali ügyfelek működése a Start menüben                                                 | Lásd: [az távoli asztal-ügyfél hibaelhárítása](troubleshoot-client.md) , és ha ez nem oldja meg a problémát, [Nyisson meg egy Azure-támogatási kérelmet](https://azure.microsoft.com/support/create-ticket/), válassza a szolgáltatáshoz a **Windows virtuális asztal** lehetőséget, majd válassza a probléma típusa **Távoli asztal ügyfelek** lehetőséget.  <br> <br> Hálózati hiba esetén a felhasználóknak kapcsolatba kell lépniük a hálózati rendszergazdával. |
| Csatlakoztatva, de nincs hírcsatorna                                                                 | A felhasználó kapcsolódási hibáinak megoldása, de a [Windows rendszerű virtuális asztali szolgáltatás kapcsolatainak](troubleshoot-service-connection.md)egyike [sem jelenik meg (nincs hírcsatorna)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) szakasza. <br> <br> Ha a felhasználók egy alkalmazáshoz vannak rendelve, [Nyisson meg egy Azure-támogatási kérést](https://azure.microsoft.com/support/create-ticket/), válassza a **Windows virtuális asztal** lehetőséget a szolgáltatáshoz, majd válassza a probléma típusa **Távoli asztal ügyfelek** lehetőséget. |
| A hírcsatornák felderítésével kapcsolatos problémák a hálózat miatt                                            | A felhasználóknak kapcsolatba kell lépniük a hálózati rendszergazdával. |
| Ügyfelek csatlakoztatása                                                                    | Tekintse meg a [Windows rendszerű virtuális asztali szolgáltatások kapcsolatait](troubleshoot-service-connection.md) , és ha ez nem oldja meg a problémát, tekintse meg a [munkamenet-gazdagép virtuális gép konfigurációját](troubleshoot-vm-configuration.md) |
| A távoli alkalmazások vagy az asztal rugalmassága                                      | Ha a problémák egy adott alkalmazáshoz vagy termékhez vannak kötve, forduljon a termékért felelős csapathoz. |
| Licencelési üzenetek vagy hibák                                                          | Ha a problémák egy adott alkalmazáshoz vagy termékhez vannak kötve, forduljon a termékért felelős csapathoz. |
| Harmadik féltől származó hitelesítési módszerekkel kapcsolatos problémák | Győződjön meg arról, hogy a harmadik féltől származó szolgáltató támogatja a Windows rendszerű virtuális asztali környezeteket, és az ismert problémákkal kapcsolatban megközelíti őket. |
| Problémák a Windows rendszerű virtuális asztali Log Analytics használatával | A diagnosztikai sémával kapcsolatos problémák esetén [Nyisson meg egy Azure-támogatási kérelmet](https://azure.microsoft.com/support/create-ticket/).<br><br>A Log Analytics lekérdezések, vizualizációk vagy egyéb problémák esetén válassza ki a megfelelő problémát a Log Analytics alatt. |
| M365-alkalmazásokat használó problémák | Forduljon a M365 felügyeleti központhoz a [M365 felügyeleti központ súgójának](/microsoft-365/admin/contact-support-for-business-products/)egyikével. |

## <a name="next-steps"></a>További lépések

- A gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg az [alkalmazáskészlet létrehozása](troubleshoot-set-up-issues.md)című témakört.
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali szolgáltatások kapcsolatai](troubleshoot-service-connection.md)című témakört.
- Távoli asztal-ügyfelekkel kapcsolatos problémák elhárításához tekintse meg [a távoli asztal-ügyfél hibaelhárítása](troubleshoot-client.md) című témakört.
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerű virtuális asztali környezet](environment-setup.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](../azure-resource-manager/management/view-activity-logs.md).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](../azure-resource-manager/templates/deployment-history.md).
