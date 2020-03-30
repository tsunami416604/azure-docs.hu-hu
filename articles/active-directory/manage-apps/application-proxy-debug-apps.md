---
title: Hibakeresési alkalmazásproxy-alkalmazások – Azure Active Directory | Microsoft dokumentumok
description: Hibakeresési problémák az Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazásokkal.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 575891d99c077299f5e7abf008c1ebb2b158373f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382077"
---
# <a name="debug-application-proxy-application-issues"></a>Alkalmazásproxy alkalmazáshibáinak hibakeresése 

Ez a cikk segít az Azure Active Directory (Azure AD) alkalmazásproxy-alkalmazásokkal kapcsolatos problémák elhárításában. Ha az alkalmazásproxy-szolgáltatást használja egy helyszíni webalkalmazás távoli eléréséhez, de nem sikerül csatlakoznia az alkalmazáshoz, használja ezt a folyamatábrát az alkalmazásproblémák hibakereséséhez. 

## <a name="before-you-begin"></a>Előkészületek

Alkalmazásproxyval kapcsolatos problémák elhárításakor azt javasoljuk, hogy kezdje az összekötőkkel. Először kövesse a [hibakeresési alkalmazásproxy-összekötő vel](application-proxy-debug-connectors.md) kapcsolatos hibaelhárítási folyamatot, és győződjön meg arról, hogy az alkalmazásproxy-összekötők megfelelően vannak konfigurálva. Ha továbbra is problémákat, térjen vissza ezt a cikket az alkalmazás hibaelhárításához.  

Az alkalmazásproxyról további információt a következő témakörben talál:

- [Távoli hozzáférés a helyszíni alkalmazásokhoz alkalmazásproxyn keresztül](application-proxy.md)
- [Alkalmazásproxy-összekötők](application-proxy-connectors.md)
- [Összekötő telepítése és regisztrálása](application-proxy-add-on-premises-application.md)
- [Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Folyamatábra alkalmazásokkal kapcsolatos problémákhoz

Ez a folyamatábra végigvezeti a lépéseket az alkalmazáshoz való csatlakozással kapcsolatos leggyakoribb problémák hibakereséséhez. Az egyes lépésekről a folyamatábrát követő táblázatban talál részleteket.

![Folyamatábra az alkalmazások hibakeresésének lépéseit mutatja be](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Műveletek | Leírás | 
|---------|---------|---------|
|1 | Nyisson meg egy böngészőt, nyissa meg az alkalmazást, és adja meg hitelesítő adatait | Próbáljon meg a hitelesítő adataival bejelentkezni az alkalmazásba, és ellenőrizze, hogy vannak-e a felhasználókkal kapcsolatos hibák, például [Ez a vállalati alkalmazás nem érhető el.](application-proxy-sign-in-bad-gateway-timeout-error.md) |
|2 | Az alkalmazáshoz való felhasználói hozzárendelés ellenőrzése | Győződjön meg arról, hogy a felhasználói fiók rendelkezik engedéllyel az alkalmazás eléréséhez a vállalati hálózaton belülről, majd tesztelje az alkalmazásba való bejelentkezést [az alkalmazás tesztelése](application-proxy-add-on-premises-application.md#test-the-application)című részben leírt lépéseket követve. Ha a bejelentkezési problémák továbbra is fennállnak, olvassa el [a Bejelentkezési hibák elhárítása című témakört.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)  |
|3 | Nyisson meg egy böngészőt, és próbálja meg elérni az alkalmazást | Ha hiba jelenik meg azonnal, ellenőrizze, hogy az alkalmazásproxy megfelelően van-e konfigurálva. Az egyes hibaüzenetekkel kapcsolatos további részleteket az [Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása](application-proxy-troubleshoot.md)című témakörben talál.  |
|4 | Ellenőrizze az egyéni tartomány beállítását, vagy hárítsa el a hibát | Ha a lap egyáltalán nem jelenik meg, ellenőrizze, hogy az egyéni tartomány megfelelően van-e konfigurálva az [Egyéni tartományok kal való együttműködés](application-proxy-configure-custom-domain.md)című lapban.<br></br>Ha a lap nem töltődik be, és hibaüzenet jelenik meg, a hiba elhárításához hivatkozva [az Alkalmazásproxy-problémák és -hibaüzenetek elhárítása című témakörben található.](application-proxy-troubleshoot.md) <br></br>Ha egy hibaüzenet megjelenése 20 másodpercnél tovább tart, akkor kapcsolódási probléma merülhet fel. Nyissa meg a [Debug Application Proxy connectors](application-proxy-debug-connectors.md) hibaelhárítási cikket.  |
|5 | Ha a problémák továbbra is fennállnak, nyissa meg az összekötő hibakeresését | Lehet, hogy a proxy és az összekötő, vagy az összekötő és a háttérrendszer közötti kapcsolati probléma merül fel. Nyissa meg a [Debug Application Proxy connectors](application-proxy-debug-connectors.md) hibaelhárítási cikket. |
|6 | Az összes erőforrás közzététele, a böngészőfejlesztői eszközök ellenőrzése és hivatkozások javítása | Győződjön meg arról, hogy a közzétételi útvonal tartalmazza az alkalmazáshoz szükséges összes képet, parancsfájlt és stíluslapot. További információt a [Helyszíni alkalmazás hozzáadása az Azure AD-hez.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) <br></br>Használja a böngésző fejlesztői eszközeit (Az Internet Explorer vagy a Microsoft Edge F12 eszközeit), és ellenőrizze, hogy a közzétételi problémák az Alkalmazás lapon leírt módon [nem jelennek-e meg megfelelően.](application-proxy-page-appearance-broken-problem.md) <br></br>A lap Hivatkozáshivatkozásaiban található hibás hivatkozások feloldásának áttekintési lehetőségei [nem működnek.](application-proxy-page-links-broken-problem.md) |
|7 | Hálózati késés ellenőrzése | Ha a lap lassan töltődik be, ismerje meg, hogyan minimalizálhatja a hálózati késést [a Késés csökkentésére szolgáló szempontok](application-proxy-network-topology.md#considerations-for-reducing-latency)című részben. | 
|8 | További hibaelhárítási súgó | Ha a problémák továbbra is fennállnak, további hibaelhárítási cikkeket találhat az [alkalmazásproxy hibaelhárítási dokumentációjában.](application-proxy-troubleshoot.md) |

## <a name="next-steps"></a>További lépések


* [Alkalmazások közzététele különböző hálózatokon és helyeken összekötőcsoportok használatával](application-proxy-connector-groups.md)
* [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
* [Alkalmazásproxyval és csatlakozóval kapcsolatos hibák elhárítása](application-proxy-troubleshoot.md)
* [Az Azure AD alkalmazásproxy-összekötő csendes telepítése](application-proxy-register-connector-powershell.md)
