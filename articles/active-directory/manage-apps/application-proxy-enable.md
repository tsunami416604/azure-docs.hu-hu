---
title: Ismerkedés az Azure AD alkalmazás Proxy - összekötő telepítése |} Microsoft Docs
description: Alkalmazásproxy bekapcsolása az Azure portálon, és a fordított proxyhoz tartozó az összekötők telepítése.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 58bcb3cbdf389393beb58a9f50b3e77aa2b3aae8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Az alkalmazásproxy első lépései, és az összekötő telepítése
Ez a cikk útmutatást nyújt a felhőcímtárhoz tartozó Microsoft Azure AD alkalmazásproxy engedélyezéséhez szükséges lépésekről az Azure AD-ben.

Ha Ön még nem ismeri a biztonsági és a termelékenység előnyök alkalmazásproxy számos lehetőséget kínál a szervezet számára, további információ [hogyan biztosíthat biztonságos távoli hozzáférést a helyszíni alkalmazások](application-proxy.md).

## <a name="application-proxy-prerequisites"></a>Az alkalmazásproxy használatának előfeltételei
Az alkalmazásproxy szolgáltatásainak engedélyezése és használata előtt a következőkkel kell rendelkeznie:

* [Microsoft Azure AD Prémium vagy Alapszintű előfizetés](../active-directory-whatis.md) és egy globális rendszergazdaként használt Azure AD-címtár.
* Egy Windows Server 2012 R2 vagy 2016, amelyre telepítheti az alkalmazásproxy-összekötő futtató kiszolgáló. A kiszolgálónak kell lennie csatlakozni tudjanak a felhőben, és a helyszíni alkalmazások közzétételekor az alkalmazásproxy-szolgáltatásokat.
  * Az egyszeri bejelentkezéshez a Kerberos által korlátozott delegálást használó közzétett alkalmazások a gép kell a tartományhoz ugyanabban a tartományban AD mint az alkalmazások, amelyek tesznek közzé. További információ: [Kerberos által korlátozott Delegálás az egyszeri bejelentkezés az alkalmazásproxy](application-proxy-configure-single-sign-on-with-kcd.md).

Ha a szervezet proxykiszolgálót használ az internetkapcsolat működését, olvassa el [együttműködnek a meglévő helyszíni proxykiszolgálókat](application-proxy-configure-connectors-with-proxy-servers.md) talál részletes információt az alkalmazásproxy megkezdése előtt konfigurálásuk módját.

## <a name="open-your-ports"></a>A portok megnyitása

A környezet előkészítése az Azure AD-alkalmazásproxy, először az Azure-adatközpont kommunikáció. Ha az útvonalon tűzfal található, győződjön meg arról, hogy az nyitva van, így az összekötő el tudja küldeni a HTTPS- (TCP-) kéréseket az alkalmazásproxynak.

1. Nyissa meg a következő portokat a **kimenő** forgalmat:

   | Portszám | Hogyan használja fel azokat |
   | --- | --- |
   | 80 | Az SSL-tanúsítvány érvényesítése közben letöltése a tanúsítvány-visszavonási listákat (CRL) |
   | 443 | Az alkalmazásproxy minden kimenő kommunikáció |

   Ha a tűzfal szerint szabályozza az adatforgalmat származó felhasználók, nyissa meg ezeket a portokat, a forgalom hálózati szolgáltatásként futó Windows-szolgáltatások.

   > [!IMPORTANT]
   > A táblázat által adott jelentéseket tükrözik a connector verziók 1.5.132.0 portokra vonatkozó követelmények és újabb. Ha továbbra is régebbi összekötő verziója van, akkor is engedélyeznie kell mellett 80 és a 443-as: 5671, 8080-as, a következő portokat 9090-9091, 9350 9352, 10100 – 10120.
   >
   >Az összekötők a legújabb verzióra frissítésével kapcsolatos információkért lásd: [megértéséhez Azure AD-alkalmazásproxy összekötők](application-proxy-connectors.md#automatic-updates).

2. Ha a tűzfal vagy a proxy lehetővé teszi a DNS engedélyezett, akkor az engedélyezett kapcsolatok msappproxy.net és servicebus.windows.net. Ha nem szeretné a hozzáférést a [Azure DataCenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653), amely minden héten frissítése.

3. Microsoft négy címet használ a tanúsítványok ellenőrzése. A következő URL-hozzáférés engedélyezése, ha ezt még nem tette meg egyéb termékek esetében:
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. Az összekötő számára a regisztrációs folyamat során hozzá kell férnie login.windows.net és login.microsoftonline.com.

5. Használja a [az Azure AD Application Proxy Connector portok teszt eszközét](https://aadap-portcheck.connectorporttest.msappproxy.net/) ellenőrzése, hogy az összekötő el lehet-e érni az alkalmazásproxy-szolgáltatás. Minimális győződjön meg arról, hogy a központi US régió és az Önhöz legközelebbi régiót összes zöld jelölők. Túl további zöld jelölők azt jelenti, hogy nagyobb rugalmasság.

## <a name="install-and-register-a-connector"></a>Telepítse és regisztrálja az összekötőhöz
1. Jelentkezzen be rendszergazdaként a a [Azure-portálon](https://portal.azure.com/).
2. A jelenlegi címtárral jobb felső sarokban a felhasználónevére alatt jelenik meg. Ha szeretne módosítsa a könyvtárat, válassza ki a erre az ikonra.
3. Ugrás a **az Azure Active Directory** > **alkalmazásproxy**.

   ![Navigáljon az alkalmazásproxy](./media/application-proxy-enable/app_proxy_navigate.png)

4. Válassza ki **összekötő letöltése**.

   ![Összekötő letöltése](./media/application-proxy-enable/download_connector.png)

5. Az előfeltételeknek megfelelően előkészített kiszolgálón futtassa az **AADApplicationProxyConnectorInstaller.exe** programot.
6. A telepítés végrehajtásához kövesse a varázsló utasításait. A telepítés során kéri az összekötő regisztrálására az Azure AD-bérlő Alkalmazásproxyjával.

   * Adja meg Azure AD globális rendszergazdai hitelesítő adatait. A globális rendszergazdai bérlő adatai eltérhetnek a Microsoft Azure rendszerre vonatkozó hitelesítő adatoktól.
   * Győződjön meg arról, hogy az összekötőt regisztráló rendszergazda ugyanabban a címtárban található, ahol az alkalmazásproxy-szolgáltatás engedélyezve van. Ha például a bérlő tartománya a contoso.com, a rendszergazda admin@contoso.com vagy más, adott tartománybeli alias lehet.
   * Ha **Internet Explorer fokozott biztonsági beállítások** értéke **a** a kiszolgálón, ahol az összekötőt telepíti, nem láthatók a regisztrációs képernyő. Is elérheti, kövesse a hibaüzenetben. Győződjön meg arról, hogy az Internet Explorer fokozott biztonsági beállításai ki vannak kapcsolva.

A magas szintű rendelkezésre állás érdekében legalább kettő összekötőt üzembe kell helyeznie. Minden egyes összekötőt külön kell regisztrálni.

## <a name="test-that-the-connector-installed-correctly"></a>Tesztelje, hogy az összekötő megfelelően telepítve

Ellenőrizheti, hogy egy új összekötő megfelelően telepítve ellenőrzésével, akár az Azure-portálon vagy a kiszolgálón. 

A bérlő bejelentkezés az Azure portálon, és navigáljon a **Azure Active Directory** > **alkalmazásproxy**. Az összes összekötőt és összekötő csoportok jelennek meg ezen a lapon. Jelöljön ki egy összekötőt, a részletek megtekintéséhez, vagy helyezze át egy másik összekötő csoportot. 

A kiszolgálón ellenőrizze az összekötő, és megtekinti a connector aktív szolgáltatások listáját. A két szolgáltatást kell azonnal futtatni, de ha nem, kapcsolhatja be azokat: 

   * a **Microsoft AAD alkalmazásproxy-összekötő** a kapcsolódást engedélyezi;

   * **Microsoft AAD Alkalmazásproxyösszekötő** az automatikus frissítési szolgáltatása. Megtekinti a ellenőrzi a connector új verziója, és szükség szerint frissíti.

   ![Az alkalmazásproxy összekötőjének szolgáltatásai – képernyőfelvétel](./media/application-proxy-enable/app_proxy_services.png)

Összekötők és hogyan azok friss kapcsolatos információkért lásd: [megértéséhez Azure AD-alkalmazásproxy összekötők](application-proxy-connectors.md).


## <a name="next-steps"></a>További lépések
Most már készen áll az [alkalmazások közzétételére az alkalmazásproxy használatával](application-proxy-publish-azure-portal.md).

Ha olyan alkalmazásokkal rendelkezik, különálló hálózatokból vagy különböző helyeken lévő, összekötő csoportok használata a különböző összekötők rendezze a logikai egységeket. További információ: [Az alkalmazásproxy-összekötők használata](application-proxy-connector-groups.md).
