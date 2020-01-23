---
title: A Azure Active Directory Domain Services állapotának ellenõrzése | Microsoft Docs
description: Megtudhatja, hogyan ellenőrizhető egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartomány állapota, és hogyan értelmezhető az állapotüzenetek a Azure Portal használatával.
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
ms.openlocfilehash: f0719542eb693e52f9a7996e28699b7425b0e0fe
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509138"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services felügyelt tartomány állapotának ellenőrzését

Azure Active Directory Domain Services (Azure AD DS) néhány háttérben futó feladatot futtat a felügyelt tartomány kifogástalan állapotának és naprakész állapotának megőrzése érdekében. Ezek a feladatok magukban foglalják a biztonsági másolatok készítését, a biztonsági frissítések alkalmazását és az adatok szinkronizálását az Azure AD-ból. Ha problémák merülnek fel az Azure AD DS felügyelt tartományával kapcsolatban, előfordulhat, hogy ezek a feladatok nem hajthatók végre sikeresen. A problémák áttekintéséhez és megoldásához a Azure Portal használatával ellenőrizheti egy Azure AD DS felügyelt tartomány állapotát.

Ez a cikk bemutatja, hogyan tekintheti meg az Azure AD DS állapotát, és megismerheti a megjelenített információkat vagy riasztásokat.

## <a name="view-the-health-status"></a>Állapot megtekintése

Egy Azure AD DS felügyelt tartomány állapotának megtekintése a Azure Portal használatával történik. A legutóbbi biztonsági mentési időpontra és az Azure AD-vel való szinkronizálásra vonatkozó információk láthatók a felügyelt tartomány állapotával kapcsolatos problémákat jelző riasztásokkal együtt. Egy Azure AD DS felügyelt tartomány állapotának megtekintéséhez hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services**lehetőséget.
1. Válassza ki az Azure AD DS felügyelt tartományát, például *aadds.contoso.com*.
1. Az Azure AD DS erőforrás ablak bal oldalán válassza az **állapot**lehetőséget. Az alábbi képernyőképen egy kifogástalan Azure AD DS felügyelt tartomány és az utolsó biztonsági mentés és az Azure AD szinkronizálás állapota látható:

    ![A Azure Active Directory Domain Services állapotot megjelenítő Azure Portal állapot oldalának áttekintése](./media/check-health/health-page.png)

Az állapot lap *utolsó kiértékelt* időbélyegzője azt mutatja, hogy az Azure AD DS felügyelt tartomány utolsó ellenőrzése megtörténik. Az Azure AD DS felügyelt tartomány állapotát óránként értékeli a rendszer. Ha módosít egy Azure AD DS felügyelt tartományt, várjon, amíg a következő kiértékelési ciklus megtekinti a frissített állapotot.

A jobb felső sarokban az Azure AD DS felügyelt tartomány általános állapota látható. Az állapot a tartomány összes meglévő riasztását befolyásolja. Az alábbi táblázat az elérhető állapotjelzőket ismerteti:

| Állapot | Ikon | Magyarázat |
| --- | :----: | --- |
| Fut | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Az Azure AD DS felügyelt tartománya megfelelően fut, és nem rendelkezik kritikus vagy figyelmeztető riasztásokkal. Előfordulhat, hogy a tartomány tájékoztató riasztásokkal rendelkezik. |
| Figyelmet igényel (figyelmeztetés) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Nincsenek kritikus riasztások az Azure AD DS felügyelt tartományon, de egy vagy több figyelmeztető riasztást kell kezelni. |
| Figyelmet igényel (kritikus) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Egy vagy több kritikus riasztás található az Azure AD DS felügyelt tartományon, amelyet meg kell oldani. Figyelmeztetési és/vagy tájékoztató riasztásokat is tartalmazhat. |
| Üzembe helyezés | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Az Azure AD DS-tartomány üzembe helyezése folyamatban van. |

## <a name="understand-monitors-and-alerts"></a>A figyelők és a riasztások ismertetése

Egy Azure AD DS felügyelt tartomány állapota két típusú információt mutat be – *figyelők*és *riasztások*. A figyelők megjelenítik az alapvető háttér-feladatok befejezésének időpontját. A riasztások a felügyelt tartomány stabilitásának javításához nyújtanak információt vagy javaslatokat.

### <a name="monitors"></a>Monitorozások

A figyelők egy Azure AD DS felügyelt tartomány azon területei, amelyek ellenőrzése rendszeresen megtörténik. Ha van aktív riasztás az Azure AD DS felügyelt tartományhoz, akkor előfordulhat, hogy az egyik figyelő egy problémát jelent. Azure AD Domain Services jelenleg a következő területekhez tartozó figyelőket tartalmaz:

* Biztonsági mentés
* Szinkronizálás az Azure AD-vel

#### <a name="backup-monitor"></a>Biztonsági mentési figyelő

A biztonsági mentési figyelő ellenőrzi, hogy az Azure AD DS felügyelt tartományának automatizált rendszeres biztonsági mentése sikeresen megtörtént-e. A következő táblázat az elérhető biztonsági mentési figyelő állapotát részletezi:

| Részletességi érték | Magyarázat |
| --- | --- |
| Soha nem készített biztonsági mentést | Ez az állapot normális az új Azure AD DS felügyelt tartományok esetében. Az első biztonsági mentést az Azure AD DS felügyelt tartomány üzembe helyezése után 24 órával kell létrehozni. Ha ez az állapot tartósan fennáll, [Nyisson meg egy Azure-támogatási kérelmet][azure-support]. |
| A legutóbbi biztonsági mentés 1 – 14 nappal ezelőtt történt | Ez az időtartomány a biztonsági mentési figyelő várt állapota. Ebben az időszakban automatikusan rendszeres biztonsági mentést kell készíteni. |
| A legutóbbi biztonsági mentés több mint 14 napja is megtörtént. | A két hétnél hosszabb TimeSpan azt jelzi, hogy probléma van az automatizált rendszeres biztonsági mentésekkel. Az aktív kritikus riasztások akadályozhatják az Azure AD DS felügyelt tartomány biztonsági mentését. Javítsa ki az Azure AD DS felügyelt tartományhoz tartozó összes aktív riasztást. Ha a biztonsági mentési figyelő nem frissíti az állapotot a legutóbbi biztonsági másolat jelentésére, [Nyisson meg egy Azure-támogatási kérelmet][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Szinkronizálás az Azure AD-figyelővel

Egy Azure AD DS felügyelt tartomány rendszeresen szinkronizál a Azure Active Directoryokkal. A felhasználók és a csoport objektumainak száma, valamint az Azure AD-címtárban az utolsó szinkronizálás óta végrehajtott módosítások száma befolyásolja, hogy mennyi ideig tart a szinkronizálás. Ha az Azure AD DS felügyelt tartomány utolsó szinkronizálása három nappal ezelőtt történt, keresse meg és oldja fel az aktív riasztásokat. Ha a szinkronizációs figyelő nem frissíti az állapotot, hogy az aktív riasztások kezelése után egy legutóbbi szinkronizálás jelenjen [meg, nyisson meg egy Azure-támogatási kérelmet][azure-support].

### <a name="alerts"></a>Értesítések

Riasztások jönnek létre olyan Azure AD DS felügyelt tartománybeli problémák esetén, amelyeket a szolgáltatás megfelelő futtatásához kell kezelnie. Minden riasztás ismerteti a problémát, és olyan URL-címet ad meg, amely a probléma megoldásának konkrét lépéseit ismerteti. A lehetséges riasztásokról és azok megoldásáról további információt a [riasztások hibaelhárítása](troubleshoot-alerts.md)című témakörben talál.

Az állapot riasztásai a következő súlyossági szintekre vannak kategorizálva:

 * A **kritikus riasztások** olyan problémák, amelyek súlyosan érintik az Azure AD DS felügyelt tartományát. Ezeket a riasztásokat azonnal meg kell oldani. Az Azure platform a problémák megoldása előtt nem tudja figyelni, kezelni, javítani és szinkronizálni a felügyelt tartományt.
 * A **figyelmeztető riasztások** értesítik az Azure AD DS felügyelt tartományi műveletekkel kapcsolatos esetleges problémákról, ha a probléma továbbra is fennáll. Ezek a riasztások a felügyelt tartomány biztonságossá tételére vonatkozó javaslatokat is kínálnak.
 * Az **tájékoztató riasztások** olyan értesítések, amelyek negatív hatással vannak az Azure AD DS felügyelt tartományára. Az tájékoztató riasztások betekintést nyújtanak a felügyelt tartományba tartozó adatokra.

## <a name="next-steps"></a>Következő lépések

Az állapot lapon megjelenő riasztásokkal kapcsolatos további információkért lásd: [riasztások feloldása a felügyelt tartományon][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
