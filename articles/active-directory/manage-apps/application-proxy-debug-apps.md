---
title: Az alkalmazásproxy-alkalmazások – Azure Active Directory |} A Microsoft Docs
description: Hibakeresés az Azure Active Directory (Azure AD) Application Proxy alkalmazásai.
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
ms.openlocfilehash: d0a12bde119e9dae3f950603fac4bce060bb5f91
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66172265"
---
# <a name="debug-application-proxy-application-issues"></a>Az alkalmazásproxy-alkalmazás kapcsolatos problémák megoldásában 

Ez a cikk segít az Azure Active Directory (Azure AD) Application Proxy alkalmazásai kapcsolatos hibaelhárítás. Ha egy helyszíni webes alkalmazásba való távoli hozzáférés az Application Proxy szolgáltatást használja, de problémába ütközik az alkalmazás csatlakozik, ez a folyamatábra használatával alkalmazás kapcsolatos problémák megoldásában. 

## <a name="before-you-begin"></a>Előkészületek

Az alkalmazásproxy-problémák hibaelhárításához, javasoljuk, hogy először a az összekötők. Először hajtsa végre a hibaelhárítási folyamat [problémák hibakeresése Application Proxy Connector](application-proxy-debug-connectors.md) , hogy az alkalmazásproxy-összekötők megfelelően vannak konfigurálva. Ha továbbra is problémákat tapasztal, térjen vissza a cikk az alkalmazás hibaelhárításához.  

Alkalmazás-proxyval kapcsolatos további információkért lásd:

- [Alkalmazásproxyn keresztül a helyszíni alkalmazások távoli elérését](application-proxy.md)
- [Alkalmazásproxy-összekötők](application-proxy-connectors.md)
- [Telepítés és a egy összekötő regisztrálása](application-proxy-add-on-premises-application.md)
- [Az alkalmazásproxy-problémák és hibaüzenetek hibaelhárítása](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Az alkalmazások esetleges hibáit folyamatábrája

Ez a folyamatábra végigvezeti a gyakori problémák a csatlakozást az alkalmazást a hibakereséshez. Tekintse meg az a folyamatábra szemlélteti a következő táblázat az egyes lépések kapcsolatos további információk.

![Az alkalmazás hibakeresésének lépéseit bemutató folyamatábra](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Műveletek | Leírás | 
|---------|---------|---------|
|1 | Nyisson meg egy böngészőt, az alkalmazás eléréséhez, és adja meg hitelesítő adatait | Jelentkezzen be az alkalmazást, és ellenőrizze a felhasználóval kapcsolatos hibákat, például a hitelesítő adatok használatával próbálja [vállalati alkalmazás nem érhető el](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Ellenőrizze az alkalmazás a felhasználó-hozzárendelés | Győződjön meg arról, hogy a felhasználói fiók rendelkezik hozzáféréssel az alkalmazást a vállalati hálózaton belül, és tesztelje a lépéseket követve jelentkezik be az alkalmazás [az alkalmazás tesztelése](application-proxy-add-on-premises-application.md#test-the-application). Bejelentkezési problémák továbbra is fennállnak, ha [bejelentkezési kapcsolatos hibák elhárítása](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-troubleshoot-sign-in-errors).  |
|3 | Nyisson meg egy böngészőt, és próbálja meg elérni az alkalmazást | Ha hibaüzenet jelenik meg azonnal, ellenőrizze, hogy az alkalmazásproxy megfelelően van konfigurálva. További információk konkrét hibaüzeneteket: [problémák elhárítása az Application Proxy és hibaüzenetek](application-proxy-troubleshoot.md).  |
|4 | Ellenőrizze az egyéni tartomány beállítása, vagy a hiba elhárítása | Ha az oldal összes már nem jeleníti meg, ellenőrizze, hogy az egyéni tartomány megfelelően van-e konfigurálva áttekintésével [egyéni tartományok használata](application-proxy-configure-custom-domain.md).<br></br>Ha az oldal nem tölt be, és a egy hibaüzenet jelenik meg, a lépésként tekintse át a hiba elhárítása [problémák elhárítása az Application Proxy és hibaüzenetek](application-proxy-troubleshoot.md). <br></br>Ha egy hibaüzenet jelenik meg, a 20 másodpercnél tovább tart, kapcsolódási probléma okozhatja. Nyissa meg a [hibakeresése az alkalmazásproxy-összekötők](application-proxy-debug-connectors.md) hibaelhárításról szóló cikket.  |
|5 | Ha a problémák továbbra is fennállnak, lépjen a összekötő hibakeresés | Kapcsolódási probléma a proxy- és az összekötő vagy az összekötő és a háttérrendszer között lehet. Nyissa meg a [hibakeresése az alkalmazásproxy-összekötők](application-proxy-debug-connectors.md) hibaelhárításról szóló cikket. |
|6 | Az összes erőforrások közzétételét, ellenőrizze a böngésző fejlesztői eszközök és hivatkozások javítása | Győződjön meg arról, hogy a közzétételi elérési út tartalmazza a szükséges lemezképek, parancsprogramok és stíluslapot az alkalmazásához. További információkért lásd: [helyszíni alkalmazás hozzáadása az Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>A böngésző fejlesztői eszközök (az Internet Explorer és Microsoft Edge-eszközök F12), és jelölje használata a problémák közzétételéhez leírtak szerint [alkalmazás lapján nem jelennek meg megfelelően](application-proxy-page-appearance-broken-problem.md). <br></br>Tekintse át a hivatkozások feloldására vonatkozó beállításokat [a lapon lévő hivatkozások nem működnek](application-proxy-page-links-broken-problem.md). |
|7 | Ellenőrizze a hálózati késés | Ha az oldal betöltött lassan, ismerje meg a hálózati késés minimalizálása érdekében módon [csökkenti a késéseket szempontjai](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | További hibaelhárítási információkat lásd: | Ha a problémák továbbra is fennállnak, keresse meg a további hibaelhárítási cikkek a [Application Proxy hibaelhárítási dokumentáció](application-proxy-page-appearance-broken-problem.md). |

## <a name="next-steps"></a>További lépések


* [Külön hálózatok és helyek összekötőcsoportok használatával az alkalmazások közzététele](application-proxy-connector-groups.md)
* [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
* [Proxy és az összekötő kapcsolatos hibák elhárítása](application-proxy-troubleshoot.md)
* [Az Azure AD alkalmazásproxy-összekötő csendes telepítése](application-proxy-register-connector-powershell.md)
