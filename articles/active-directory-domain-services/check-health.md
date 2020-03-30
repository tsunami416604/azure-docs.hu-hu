---
title: Az Azure Active Directory tartományi szolgáltatások állapotának ellenőrzése | Microsoft dokumentumok
description: Megtudhatja, hogyan ellenőrizheti az Azure Active Directory tartományi szolgáltatások (Azure AD DS) felügyelt tartományának állapotát, és hogyan ismerheti meg az állapotüzeneteket az Azure Portalhasználatával.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: fa1cba2d791cd40a46f8ad182c123a726143faec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614248"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Az Azure Active Directory tartományi szolgáltatások által kezelt tartomány állapotának ellenőrzése

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) néhány háttérfeladatot futtat, hogy a felügyelt tartomány kifogástalan állapotban és naprakész maradjon. Ezek a feladatok közé tartozik a biztonsági mentések, a biztonsági frissítések alkalmazása és az Azure AD-ből származó adatok szinkronizálása. Ha problémák merülnek fel az Azure AD DS felügyelt tartománnyal kapcsolatban, előfordulhat, hogy ezek a feladatok nem fejeződnek be. A problémák áttekintéséhez és megoldásához ellenőrizheti az Azure AD DS által felügyelt tartomány állapotát az Azure Portalon keresztül.

Ez a cikk bemutatja, hogyan tekintheti meg az Azure AD DS állapotának állapotát, és ismerje meg a megjelenített információkat vagy riasztásokat.

## <a name="view-the-health-status"></a>Az állapot megtekintése

Az Azure AD DS felügyelt tartomány állapotának állapota az Azure Portalon keresztül tekinthető meg. Az Azure AD-vel való utolsó biztonsági mentési idővel és szinkronizálással kapcsolatos információk, valamint a felügyelt tartomány állapotával kapcsolatos problémára utaló riasztások láthatók. Az Azure AD DS felügyelt tartomány állapotának megtekintéséhez hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon keresse meg és válassza ki az **Azure AD tartományi szolgáltatásokat.**
1. Válassza ki az Azure AD DS felügyelt tartományát, például *a aaddscontoso.com.*
1. Az Azure AD DS erőforrásablak bal oldalán válassza az **Állapot**lehetőséget. A következő példa képernyőfelvételen látható egy kifogástalan Azure AD DS felügyelt tartomány és az utolsó biztonsági mentés és az Azure AD szinkronizálás állapota:

    ![Az Azure Active Directory tartományi szolgáltatások állapotát megjelenítő Azure-portál állapotának áttekintése az Azure-portálon](./media/check-health/health-page.png)

Az *állapotlap utolsó kiértékelt* időbélyege azt mutatja, hogy az Azure AD DS felügyelt tartományának utolsó ellenőrzése mikor történt. Az Azure AD DS felügyelt tartomány ának állapotát óránként értékeli ki a rendszer. Ha bármilyen módosítást hajt végre egy Azure AD DS felügyelt tartományban, várjon, amíg a következő értékelési ciklus a frissített állapot megtekintéséhez.

Az állapot a jobb felső sarokban az Azure AD DS felügyelt tartomány általános állapotát jelzi. Az állapot a tartomány összes meglévő riasztását figyelembe veszi. Az alábbi táblázat részletezi a rendelkezésre álló állapotmutatókat:

| status | Ikon | Magyarázat |
| --- | :----: | --- |
| Fut | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Az Azure AD DS felügyelt tartomány megfelelően fut, és nem rendelkezik kritikus vagy figyelmeztető riasztást. A tartomány nak lehetnek tájékoztató riasztásai. |
| Figyelmet igényel (figyelmeztetés) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Nincsenek kritikus riasztások az Azure AD DS felügyelt tartományban, de van egy vagy több figyelmeztető riasztások, amelyeket meg kell oldani. |
| Figyelmet igényel (kritikus) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Egy vagy több kritikus riasztások az Azure AD DS felügyelt tartományban, amely et meg kell címezni. Ön is figyelmeztető és / vagy információs riasztások. |
| Telepítése | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Az Azure AD DS-tartomány telepítése folyamatban van. |

## <a name="understand-monitors-and-alerts"></a>A monitorok és riasztások ismertetése

Az Azure AD DS által felügyelt tartomány állapotának állapota kétféle információt jelenít meg : *a figyelőket*és *a riasztásokat.* A figyelők az alapvető háttérfeladatok befejezésének idejét mutatják. A riasztások a felügyelt tartomány stabilitásának javítása érdekében nyújtanak tájékoztatást vagy javaslatokat.

### <a name="monitors"></a>Monitorozások

A figyelők az Azure AD DS által felügyelt tartomány olyan területei, amelyek rendszeresen ellenőrzöttek. Ha az Azure AD DS felügyelt tartományhoz aktív riasztások vannak, előfordulhat, hogy az egyik figyelő jelenti a problémát. Az Azure AD tartományi szolgáltatások jelenleg a következő területekfigyelőivel rendelkeznek:

* Backup
* Szinkronizálás az Azure AD-vel

#### <a name="backup-monitor"></a>Biztonsági másolat figyelője

A biztonsági mentési figyelő ellenőrzi, hogy az Automatikus rendszeres biztonsági mentések az Azure AD DS felügyelt tartomány sikeresen fut. Az alábbi táblázat részletezi a biztonsági másolat figyelőjének állapotát:

| Részlet érték | Magyarázat |
| --- | --- |
| Soha nem biztonsági másolatot | Ez az állapot az új Azure AD DS felügyelt tartományok esetén normális. Az első biztonsági mentést az Azure AD DS felügyelt tartomány telepítése után 24 órával kell létrehozni. Ha ez az állapot továbbra is fennáll, [nyisson meg egy Azure-támogatási kérelmet.][azure-support] |
| Az utolsó biztonsági mentés 1-14 nappal ezelőtt készült | Ez az időtartomány a biztonsági másolat figyelőjének várt állapota. Ebben az időszakban automatikus rendszeres biztonsági mentésnek kell történnie. |
| Az utolsó biztonsági mentés több mint 14 napja készült. | A két hétnél hosszabb időtartam azt jelzi, hogy probléma van az automatikus rendszeres biztonsági mentésekkel. Az aktív kritikus riasztások megakadályozhatják az Azure AD DS felügyelt tartomány biztonsági másolatot. Az Azure AD DS felügyelt tartományának aktív riasztásait oldhatja fel. Ha a biztonsági másolat figyelője nem frissíti az állapotot egy legutóbbi biztonsági mentés jelentéséhez, [nyisson meg egy Azure-támogatási kérelmet.][azure-support] |

#### <a name="synchronization-with-azure-ad-monitor"></a>Szinkronizálás az Azure AD-figyelővel

Az Azure AD DS által felügyelt tartomány rendszeresen szinkronizálja az Azure Active Directoryval. A felhasználók és a csoportobjektumok száma, valamint az Azure AD-címtárban az utolsó szinkronizálás óta végrehajtott módosítások száma befolyásolja a szinkronizálás iményét. Ha az Azure AD DS felügyelt tartománya utoljára három nappal ezelőtt lett szinkronizálva, ellenőrizze és oldja fel az aktív riasztásokat. Ha a szinkronizálásfigyelő nem frissíti az állapotot, hogy az aktív riasztások kezelése után a legutóbbi szinkronizálás jelenjen meg, [nyisson meg egy Azure-támogatási kérelmet.][azure-support]

### <a name="alerts"></a>Riasztások

Riasztások jönnek létre az Azure AD DS felügyelt tartományban, amelyeket a szolgáltatás megfelelő futtatásához meg kell oldani. Minden riasztás ismerteti a problémát, és ad egy URL-t, amely felvázolja a probléma megoldásához szükséges konkrét lépéseket. A lehetséges riasztásokról és azok megoldásairól a [Hibaelhárítási riasztások](troubleshoot-alerts.md)című témakörben talál további információt.

Az állapotriasztások a következő súlyossági szintekbe vannak kategorizálva:

 * **A kritikus riasztások** olyan problémák, amelyek súlyosan befolyásolják az Azure AD DS felügyelt tartományát. Ezeket a riasztásokat azonnal foglalkozni kell. Az Azure platform nem figyelheti, kezelheti, nem tudja kijavítani és szinkronizálni a felügyelt tartományt, amíg a problémák meg nem oldódnak.
 * **A figyelmeztetési riasztások** értesítést küldenek azokról a problémákról, amelyek hatással lehetnek az Azure AD DS felügyelt tartományi műveleteire, ha a probléma továbbra is fennáll. Ezek a riasztások is javaslatokat a felügyelt tartomány biztonságossá tétele.
 * **A tájékoztató riasztások** olyan értesítések, amelyek nem befolyásolják hátrányosan az Azure AD DS felügyelt tartományát. A tájékoztató riasztások némi betekintést nyújtanak abba, hogy mi történik a felügyelt tartományban.

## <a name="next-steps"></a>További lépések

Az állapotállapot lapon megjelenő riasztásokról a Felügyelt [tartományriasztásmegoldása című][troubleshoot-alerts] témakörben talál további információt.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
