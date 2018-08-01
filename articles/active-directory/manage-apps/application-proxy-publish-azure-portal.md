---
title: Alkalmazások közzététele az Azure AD-alkalmazásproxyval | Microsoft Docs
description: A helyszíni alkalmazások a felhőbe az Azure AD-alkalmazásproxy közzététele az Azure Portalon.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1224642bb7e6fc0c51b3f839a78449132db5b4bb
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364257"
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Alkalmazások közzététele az Azure AD-alkalmazásproxyval

Az Azure Active Directory (AD) alkalmazásproxy segítségével támogatja a távoli dolgozók tegye közzé a helyszíni alkalmazások számára az interneten keresztül érhető el. Ezeket az alkalmazásokat az Azure portal segítségével biztonságos távoli hozzáférést a hálózaton kívüli teheti közzé.

Ez a cikk végigvezeti a lépéseken az alkalmazásproxy használatával a helyszíni alkalmazás közzététele. Miután elvégezte a cikkben, a felhasználók tudják az alkalmazás távoli eléréséhez. És készen áll további funkciókkal, például egyszeri bejelentkezés, a személyre szabott információkat és a biztonsági követelményeket az alkalmazás konfigurálásához.

Ha most ismerkedik az alkalmazásproxyt, tudjon meg többet a Ez a cikk a szolgáltatás [biztonságos távoli elérést biztosíthat a helyszíni alkalmazások](application-proxy.md).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már telepítve és regisztrálva egy összekötőt. Ha továbbra is szeretné hajtsa végre ezeket a lépéseket, tekintse meg [alkalmazásproxy – első lépések, és telepítse az összekötőt](application-proxy-enable.md).

## <a name="publish-an-on-premises-app-for-remote-access"></a>Távoli hozzáférés a helyszíni alkalmazások közzététele

Kövesse az alábbi lépéseket az alkalmazások közzététele az alkalmazásproxy. Ha még nem már letöltött és konfigurált egy összekötőt a szervezet számára, lépjen a [alkalmazásproxy – első lépések, és telepítse az összekötőt](application-proxy-enable.md) első, és tegye közzé az alkalmazást.

> [!TIP]
> Ha első alkalommal meg alkalmazásproxy tesztel, válassza ki az olyan alkalmazás, amely jelszóalapú hitelesítés be van állítva. Alkalmazásproxy más hitelesítési típust támogat, de a jelszó alapú alkalmazások használatának legegyszerűbb és egyszerű munkakezdés. 

1. Jelentkezzen be rendszergazdaként a a [az Azure portal](https://portal.azure.com/).
2. Válassza ki **Azure Active Directory** > **vállalati alkalmazások** > **új alkalmazás**.

  ![Adja hozzá a vállalati alkalmazás](./media/application-proxy-publish-azure-portal/add-app.png)

3. Válassza ki **összes**, majd **helyszíni alkalmazás**.  

  ![Saját alkalmazás hozzáadása](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Adja meg a következő információkat az alkalmazásról:

   - **Név**: az alkalmazás a hozzáférési panelen és az Azure Portalon megjelenő nevére. 

   - **Belső URL-cím**: az URL-cím, amellyel érhető el az alkalmazás a magánhálózaton belülről. Megadhat egyedi elérési utat a háttérkiszolgálón a közzétételhez, míg a kiszolgáló további része nem lesz közzétéve. Ily módon a közzététele ugyanarra a kiszolgálóra, mint a különböző alkalmazások különböző helyeken, és mindegyikhez adjon a saját nevet és hozzáférési szabályokat.

     > [!TIP]
     > Ha közzétesz egy útvonalat, győződjön meg róla, hogy az tartalmaz minden szükséges lemezképet, szkriptet és stíluslapot az alkalmazásához. Például, ha az alkalmazás https://yourapp/app és helyen található képeket használ https://yourapp/media, majd tegyen közzé https://yourapp/ mert az elérési út. A belső URL-cím nem kell lennie a kezdőlapját, megjelennek a felhasználók számára. További információkért lásd: [beállítása egy egyéni kezdőlapja közzétett alkalmazások](application-proxy-configure-custom-home-page.md).

   - **Külső URL-cím**: A cím a felhasználóknak halad át a hálózaton kívülről az alkalmazás eléréséhez. Ha nem szeretné az alapértelmezett alkalmazásproxy tartományát szeretné használni, olvassa el [egyéni tartományok az Azure AD-alkalmazásproxy](application-proxy-configure-custom-domain.md).
   - **Előhitelesítés**: alkalmazásproxy az alkalmazáshoz hozzáférés engedélyezése előtt ellenőrzi a felhasználót. 

     - Azure Active Directory: Az alkalmazásproxy átirányítja a felhasználókat az Azure AD bejelentkezési oldalára, ahol megtörténik a címtár és az alkalmazás használatára vonatkozó engedélyeik hitelesítése. Javasoljuk, hogy a beállítás az alapértelmezett, így az Azure AD biztonsági funkciókkal, például a feltételes hozzáférés és a multi-factor Authentication szolgáltatás előnyeit élvezheti.
     - Átengedés: Felhasználók hitelesítése az Azure Active Directory hozzáférni az alkalmazáshoz nem tartozik. Továbbra is beállíthatja a háttérkiszolgálón hitelesítési követelmények.
   - **Összekötőcsoport**: összekötők feldolgozni a távoli hozzáférést az alkalmazáshoz, és összekötő azokat, ahogy az összekötők és a régiót, hálózati vagy célú alkalmazások segítséget. Ha nincs még létrehozva összekötő csoportnak sem, az alkalmazás hozzá van rendelve **alapértelmezett**.

>[!NOTE]
>Ha az alkalmazás való csatlakozáshoz használja a websockets protokoll, győződjön meg, hogy connector ezen verziója 1.5.612.0 rendelkezik-e vagy magasabb a websocket-támogatás és a hozzárendelt Összekötőcsoport csak akkor használja ezeket az összekötőket.

   ![Az alkalmazás konfigurálása](./media/application-proxy-publish-azure-portal/configure-app.png)
5. Szükség esetén további beállítások konfigurálására. A legtöbb alkalmazás esetén ezek a beállítások érdemes megtartani az alapértelmezett állapotra. 
   - **Háttéralkalmazás túllépte az időkorlátot**: Ez az érték **hosszú** csak akkor, ha az alkalmazás lassú hitelesítéshez és csatlakozáshoz. 
   - **A fejlécek URL-címek lefordítása**: tartsa ezt az értéket **Igen** , kivéve, ha az alkalmazás által igényelt az eredeti állomásfejlécet a hitelesítési kérelmet.
   - **A kérelem törzsében URL-címek lefordítása**: tartsa ezt az értéket **nem** , ha rendelkezik szoftveresen kötött HTML-hivatkozások más helyszíni alkalmazásokhoz, és ne használja az egyéni tartományok. További információkért lásd: [hivatkozásra a proxyval fordítási](application-proxy-configure-hard-coded-link-translation.md).
   
   ![Az alkalmazás konfigurálása](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. Válassza a **Hozzáadás** lehetőséget.


## <a name="add-a-test-user"></a>Adja hozzá a tesztfelhasználó számára 

Ha tesztelni szeretné, hogy az alkalmazás megfelelően lett közzétéve, adjon hozzá egy tesztfelhasználói fiókja. Ellenőrizze, hogy ez a fiók rendelkezik-e férhessen hozzá az alkalmazást a vállalati hálózaton belül.

1. Vissza a gyors üzembe helyezési panelen válassza ki a **felhasználó hozzárendelése teszteléshez**.

  ![Felhasználó hozzárendelése teszteléshez](./media/application-proxy-publish-azure-portal/assign-user.png)

2. A felhasználók és csoportok panelt, válassza ki a **Hozzáadás**.

  ![Egy felhasználó vagy csoport hozzáadása](./media/application-proxy-publish-azure-portal/add-user.png)

3. A hozzárendelés hozzáadása panelen válassza ki a **felhasználók és csoportok** majd válassza ki a hozzáadni kívánt fiókot. 
4. Válassza a **Hozzárendelés** elemet.

## <a name="test-your-published-app"></a>A közzétett alkalmazás tesztelése

A böngészőben lépjen a közzétételi lépés során konfigurált külső URL-CÍMÉT. Tekintse meg a kezdőképernyőn kell, és állítsa be a teszt fiókkal jelentkezhet.

![A közzétett alkalmazás tesztelése](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>További lépések
- [Letöltési összekötők](application-proxy-enable.md) és [összekötőcsoportok létrehozása](application-proxy-connector-groups.md) közzététel külön hálózatokon és helyek.

- [Egyszeri bejelentkezés beállítása](application-proxy-configure-single-sign-on-password-vaulting.md) az újonnan közzétett alkalmazás
