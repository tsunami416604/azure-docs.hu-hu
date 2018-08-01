---
title: Ismerkedés az Azure AD-alkalmazásproxy - összekötő telepítése |} A Microsoft Docs
description: Kapcsolja be az alkalmazásproxyt, hogy az Azure Portalon, és az összekötők telepítése a fordított proxy.
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
ms.date: 06/26/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 5227f756e807a30573733bd408144d869caac9ec
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366475"
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Ismerkedés az Application Proxy és az összekötő telepítése
Ez a cikk útmutatást nyújt a felhőcímtárhoz tartozó Microsoft Azure AD alkalmazásproxy engedélyezéséhez szükséges lépésekről az Azure AD-ben.

Ha még nem, de a biztonság és hatékonyság előnyöket tudomást az alkalmazásproxy utolsósorban a szervezet számára, tudjon meg többet [biztonságos távoli elérést biztosíthat a helyszíni alkalmazások](application-proxy.md).

## <a name="application-proxy-prerequisites"></a>Az alkalmazásproxy használatának előfeltételei
Az alkalmazásproxy szolgáltatásainak engedélyezése és használata előtt a következőkkel kell rendelkeznie:

* [Microsoft Azure AD Prémium vagy Alapszintű előfizetés](../fundamentals/active-directory-whatis.md) és egy globális rendszergazdaként használt Azure AD-címtár.
* Windows Server 2012 R2 és 2016-ban, amelyre telepítheti az alkalmazásproxy-összekötő futtató kiszolgálókra. A kiszolgáló képesnek kell lennie kapcsolódni a felhőben és a helyszíni alkalmazások közzéteszi az alkalmazásproxy-szolgáltatásokat.
  * Az egyszeri bejelentkezéshez a Kerberos által korlátozott delegálás használatával közzétett alkalmazásokban a gép kell a tartományhoz a közzétett alkalmazások azonos AD tartományhoz. További információ: [KCD az egyszeri bejelentkezés alkalmazásproxyval való](application-proxy-configure-single-sign-on-with-kcd.md).

Ha a szervezete proxykiszolgálóval csatlakozik az internethez, olvassa el [együttműködnek a meglévő helyszíni proxykiszolgálók](application-proxy-configure-connectors-with-proxy-servers.md) konfigurálni őket, mielőtt elkezdené a proxyval kapcsolatos részletekért.

## <a name="open-your-ports"></a>A portok megnyitása

A környezet előkészítése az Azure AD-alkalmazásproxy, először az Azure-beli adatközpontok kommunikáció engedélyezésére. Ha az útvonalon tűzfal található, győződjön meg arról, hogy az nyitva van, így az összekötő el tudja küldeni a HTTPS- (TCP-) kéréseket az alkalmazásproxynak.

1. Nyissa meg a következő portokat **kimenő** forgalmat:

   | Portszám | Hogyan használja fel azokat |
   | --- | --- |
   | 80 | Letölti a tanúsítvány-visszavonási listákat (CRL) az SSL-tanúsítvány érvényesítése közben |
   | 443 | Minden kimenő kommunikációnak az alkalmazásproxy-szolgáltatással |

   Ha a tűzfal szabályozza az adatforgalmat a felhasználók helye szerint, nyissa meg ezeket a portokat, a forgalom hálózati szolgáltatásként futó Windows-szolgáltatások.

   > [!IMPORTANT]
   > A tábla tartalmazza az összekötő verziók 1.5.132.0 port követelményei és újabb. Ha egy régebbi összekötő továbbra is rendelkezik, akkor is engedélyeznie kell mellett 80 és 443-as: 5671-es, 8080-as, a következő portokat 9090-9091, 9350, 9352-es, 10100 – 10120.
   >
   >Az összekötők a legújabb verzióra való frissítésével kapcsolatos információkért lásd: [megismerheti az Azure AD-alkalmazásproxy összekötőit](application-proxy-connectors.md#automatic-updates).

2. Ha a tűzfal vagy proxy lehetővé teszi a DNS-engedélyezési, msappproxy.net és servicebus.windows.net engedélyezett kapcsolatokat is. Ha nem, engedélyeznie kell a hozzáférést a [Azure DataCenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653), amely hetente frissül.

3. A Microsoft négy címet használ a tanúsítványok ellenőrzése. A következő URL-címek hozzáférés engedélyezése, ha más termékek esetében tette:
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. Az összekötő a regisztrációs folyamat login.windows.net és login.microsoftonline.com hozzá kell férnie.


## <a name="install-and-register-a-connector"></a>Telepítés és a egy összekötő regisztrálása
1. Jelentkezzen be rendszergazdaként a a [az Azure portal](https://portal.azure.com/).
2. Az aktuális könyvtár megjelenik a jobb felső sarokban a felhasználónevére. Módosítsa a könyvtárakat kell, ha az ikon kiválasztásával.
3. Lépjen a **az Azure Active Directory** > **alkalmazásproxy**.

   ![Keresse meg az alkalmazásproxy](./media/application-proxy-enable/app_proxy_navigate.png)

4. Válassza ki **-összekötő letöltése**.

   ![Összekötő letöltése](./media/application-proxy-enable/download_connector.png)

5. Az előfeltételeknek megfelelően előkészített kiszolgálón futtassa az **AADApplicationProxyConnectorInstaller.exe** programot.
6. A telepítés végrehajtásához kövesse a varázsló utasításait. A telepítés során kell megadnia az összekötő regisztrálására az Azure AD-bérlő alkalmazásproxyjával.

   * Adja meg Azure AD globális rendszergazdai hitelesítő adatait. A globális rendszergazdai bérlő adatai eltérhetnek a Microsoft Azure rendszerre vonatkozó hitelesítő adatoktól.
   * Győződjön meg arról, hogy az összekötőt regisztráló rendszergazda ugyanabban a címtárban található, ahol az alkalmazásproxy-szolgáltatás engedélyezve van. Ha például a bérlő tartománya a contoso.com, a rendszergazda admin@contoso.com vagy más, adott tartománybeli alias lehet.
   * Ha **Internet Explorer fokozott biztonsági beállításai** értékre van állítva **a** a kiszolgálón, amelyre az összekötőt telepíti, nem láthatók a regisztrációs képernyő. Érhet el, kövesse a hibaüzenetben. Győződjön meg arról, hogy az Internet Explorer fokozott biztonsági beállításai ki vannak kapcsolva.

A magas szintű rendelkezésre állás érdekében legalább kettő összekötőt üzembe kell helyeznie. Minden egyes összekötőt külön kell regisztrálni.

## <a name="test-that-the-connector-installed-correctly"></a>Tesztelje, hogy az összekötő helyesen telepítve

Ellenőrizheti, hogy egy új összekötő megfelelően telepítve ellenőrzésével, akár az Azure Portalon vagy a kiszolgálón. 

Az Azure Portalon, jelentkezzen be a bérlő, és navigáljon a **Azure Active Directory** > **alkalmazásproxy**. Az összes összekötőcsoportok és összekötők ezen az oldalon jelennek meg. Válasszon egy összekötőt, a részletek megtekintéséhez, vagy helyezze át egy másik összekötőcsoporthoz. 

A kiszolgálón ellenőrizze az összekötő és az összekötő updater aktív szolgáltatások listáját. A két szolgáltatás kell azonnal elindultak, de ha nem, akkor kapcsolja őket: 

   * a **Microsoft AAD alkalmazásproxy-összekötő** a kapcsolódást engedélyezi;

   * **A Microsoft AAD Alkalmazásproxyösszekötő** egy automatikus frissítési szolgáltatás. A frissítési ellenőrzi, hogy az összekötő újabb verziója, és szükség szerint frissíti.

   ![Az alkalmazásproxy összekötőjének szolgáltatásai – képernyőfelvétel](./media/application-proxy-enable/app_proxy_services.png)

Az összekötők, és hogyan maradjon naprakész kapcsolatos információkért lásd: [megismerheti az Azure AD-alkalmazásproxy összekötőit](application-proxy-connectors.md).


## <a name="next-steps"></a>További lépések
Most már készen áll az [alkalmazások közzétételére az alkalmazásproxy használatával](application-proxy-publish-azure-portal.md).

Ha az alkalmazásokat, amelyek egy különálló hálózaton vagy különböző helyeken vannak, az összekötő csoportok használatával rendszerezheti a különböző összekötők logikai egységekbe. További információ: [Az alkalmazásproxy-összekötők használata](application-proxy-connector-groups.md).
