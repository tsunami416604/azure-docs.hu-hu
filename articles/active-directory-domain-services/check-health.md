---
title: Azure AD Domain Services – a felügyelt tartomány állapotának ellenőrzését | Microsoft Docs
description: A felügyelt tartomány állapotának ellenőrzését a Azure Portal állapot lapján hajthatja meg.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 6b808126fe4366d3ca3cc19c674b489ec3055665
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234157"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services felügyelt tartomány állapotának ellenőrzését

## <a name="overview-of-the-health-page"></a>Az állapot lap áttekintése
A Azure Portal állapot lapján naprakészen tarthatja, hogy mi történik a felügyelt tartományon. Ez a cikk végigvezeti az állapot lap elemein.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>A felügyelt tartomány állapotának megtekintése
1. Navigáljon a Azure Portal [Azure ad Domain Services lapjára](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) .
2. Kattintson arra a tartományra, amelynek állapotát meg szeretné tekinteni.
3. A bal oldali navigációs ablaktáblán kattintson az **állapot**elemre.

A következő kép egy minta állapotot ábrázoló oldalt mutat be: ![Példa állapot lap](./media/active-directory-domain-services-alerts/health-page.png)

>[!NOTE]
> A felügyelt tartomány állapota óránként lesz kiértékelve. Miután módosította a felügyelt tartományt, várjon, amíg a következő kiértékelési ciklus megtekinti a felügyelt tartomány frissített állapotát. A jobb felső sarokban lévő "utolsó kiértékelt" időbélyegző azt mutatja, hogy a felügyelt tartomány állapota mikor lett utoljára kiértékelve.
>

### <a name="status-of-your-managed-domain"></a>A felügyelt tartomány állapota
Az állapot oldal jobb felső sarkában található állapot a felügyelt tartomány általános állapotát jelzi. A tartomány összes meglévő riasztásának állapotára vonatkozó tényezők. A tartomány állapotát a Azure AD Domain Services áttekintés lapján is megtekintheti.

| Állapot | Ikon | Magyarázat |
| --- | :----: | --- |
| Fut | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | A felügyelt tartomány zökkenőmentesen fut, és nem rendelkezik kritikus vagy figyelmeztető riasztásokkal. Ez a tartomány tájékoztató riasztásokkal rendelkezhet. |
| Figyelmet igényel (figyelmeztetés) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Nincsenek kritikus riasztások a felügyelt tartományon, de egy vagy több figyelmeztető riasztást kell kezelni. |
| Figyelmet igényel (kritikus) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Egy vagy több kritikus riasztás található a felügyelt tartományon. Figyelmeztetési és/vagy tájékoztató riasztásokat is tartalmazhat. |
| Telepítése | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | A tartomány üzembe helyezése folyamatban van. |

## <a name="monitors"></a>Monitorozások
A figyelők a felügyelt tartomány azon szempontjai, amelyek rendszeres időközönként Azure AD Domain Services figyelik. A figyelők kifogástalan állapotban tartásához a legjobb módszer a felügyelt tartományhoz tartozó aktív riasztások feloldása.

Azure AD Domain Services jelenleg a következőket figyeli:
 - Backup
 - Szinkronizálás az Azure AD-vel

### <a name="the-backup-monitor"></a>A "Backup" figyelő
Ez figyeli, hogy a felügyelt tartomány rendszeres biztonsági mentése zajlik-e. A következő táblázat ismerteti, mire számíthat a Backup figyelő részletek oszlopában:

| Részletességi érték | Magyarázat |
| --- | --- |
|"Soha nem biztonsági másolat készítése" | Ez az állapot egy újonnan létrehozott felügyelt tartomány esetében normális. Általában az első biztonsági mentés a felügyelt tartomány kiépítés utáni 24 órában jön létre. Ha a felügyelt tartomány nem újonnan lett létrehozva, vagy rendellenes ideig látja ezt az állapotot, [forduljon](contact-us.md)az ügyfélszolgálathoz. |
| A legutóbbi biztonsági mentés 1 – 14 nappal ezelőtt történt | Általánosságban elmondható, hogy ez az érték a biztonsági mentési figyelő esetében várható. |
| A legutóbbi biztonsági mentés több mint 14 napja is megtörtént. | A legutóbbi biztonsági mentés óta a két hétnél hosszabb idő szokatlanul hosszú. Az aktív kritikus riasztások akadályozhatják a felügyelt tartomány rendszeres biztonsági mentését. Először oldja meg a felügyelt tartomány összes aktív riasztását, és ha a probléma továbbra is fennáll, [forduljon](contact-us.md)az ügyfélszolgálathoz. |


### <a name="the-synchronization-with-azure-ad-monitor"></a>A "szinkronizálás az Azure AD-vel" figyelő
A Microsoft figyeli, hogy a felügyelt tartomány milyen gyakran szinkronizálva van Azure Active Directoryával. Az objektumok (felhasználók & csoportok) száma és az Azure AD-címtárban végrehajtott módosítások száma, mivel a legutóbbi szinkronizálás is befolyásolhatja, hogy mennyi ideig tarthat a szinkronizálási időszak. Ha a felügyelt tartománya három nappal ezelőtt volt utoljára szinkronizálva, [forduljon](contact-us.md)az ügyfélszolgálathoz.

## <a name="alerts"></a>Riasztások
A rendszer riasztásokat hoz létre a felügyelt tartományhoz tartozó olyan problémák esetén, amelyeket a Azure AD Domain Services futtatásához kell kezelnie. Az egyes riasztások ismertetik a problémát, és egy olyan feloldási URL-címet biztosítanak, amely a probléma megoldásának konkrét lépéseit ismerteti. Az összes riasztás és a hozzájuk tartozó [megoldás](troubleshoot-alerts.md) megtekintéséhez tekintse meg a riasztások elhárítása című cikket.

### <a name="alert-severity"></a>Riasztás súlyossága
A riasztások három különböző súlyossági szintre vannak kategorizálva: kritikus, figyelmeztetés és tájékoztató.

 * A **kritikus riasztások** olyan problémák, amelyek súlyosan érintik a felügyelt tartományt. Ezeket a riasztásokat azonnal meg kell oldani, mivel a Microsoft nem tudja figyelni, felügyelni, javítani és szinkronizálni a felügyelt tartományt. 
 * A **figyelmeztető riasztások** értesítést küldenek arról, hogy a későbbiekben milyen problémák léphetnek fel a felügyelt tartományra. Ezek a riasztások a felügyelt tartomány biztonságossá tételére vonatkozó javaslatokat nyújtanak.
 * Az **tájékoztató riasztások** olyan értesítések, amelyek nem negatív hatással vannak a tartományra. Az tájékoztató riasztások úgy lettek kialakítva, hogy megismerje a tartományon és a Azure AD Domain Serviceson zajló teendőket.

## <a name="next-steps"></a>További lépések
- [Riasztások feloldása a felügyelt tartományon](troubleshoot-alerts.md)
- [További információ a Azure AD Domain Servicesról](overview.md)
- [Kapcsolatfelvétel a termék csapatával](contact-us.md)
