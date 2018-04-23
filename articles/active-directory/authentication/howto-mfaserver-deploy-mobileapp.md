---
title: Az Azure MFA-kiszolgáló Mobile App Web Service szolgáltatása | Microsoft Docs
description: A Microsoft Authenticator alkalmazás egy további sávon kívüli hitelesítési lehetőséget kínál.  Ez lehetővé teszi, hogy az MFA-kiszolgáló leküldéses értesítéseket küldjön a felhasználóknak.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 4a6d4f903ff47970cb1fc6b3964ea78c735d5113
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>A mobilalkalmazásos hitelesítés engedélyezése az Azure Multi-Factor Authentication-kiszolgálóval

A Microsoft Authenticator alkalmazás egy további sávon kívüli ellenőrzési lehetőséget kínál. Automatikus telefonhívás vagy SMS küldése helyett a felhasználó bejelentkezésekor az Azure Multi-Factor Authentication értesítést küld le a Microsoft Authenticator alkalmazásba a felhasználó okostelefonján vagy táblagépén. A felhasználó egyszerűen az **Ellenőrzés** (vagy a PIN-kód megadása után a „Hitelesítés”) elemre koppint a bejelentkezés befejezéséhez.

Ha a telefonon a vétel nem megbízható, kétlépéses ellenőrzést biztosító mobilalkalmazás használata ajánlott. Ha az alkalmazást OATH token előállítására használja, nincs szükség hálózatra vagy internetkapcsolatra.

A Mobile App Web Service-t a környezettől függően érdemes lehet az Azure Multi-Factor Authentication-kiszolgálóval megegyező kiszolgálóra, illetve egy másik, internetkapcsolattal rendelkező kiszolgálóra telepíteni.

> [!IMPORTANT]
> Ha telepítette az MFA Server 8.0-s vagy újabb verzióját, a következő lépések legtöbbjére nincs szükség. A mobilalkalmazások hitelesítését [a mobilalkalmazás konfigurálását](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server) ismertető szakasz lépéseit követve állíthatja be.

## <a name="requirements"></a>Követelmények

A Microsoft Authenticator alkalmazás használatához a következőkre van szükség, hogy az alkalmazás sikeresen kommunikáljon a Mobile App Web Service-szel:

* Azure Multi-Factor Authentication-kiszolgáló 6.x. vagy újabb verziója
* A Mobile App Web Service telepítése Microsoft® [Internet Information Services (IIS) 7.x vagy újabb](http://www.iis.net/) verziót futtató internetes webkiszolgálóra
* Az ASP.NET 4.0.30319-es verziója telepítve és regisztrálva van, és engedélyezett állapotú
* A szükséges szerepkör-szolgáltatások közé tartozik az ASP.NET és az IIS 6 metabázisával való kompatibilitás.
* A Mobile App Web Service elérhető egy nyilvános URL-címről
* A Mobile App Web Service védelmét egy SSL-tanúsítvány biztosítja.
* Az Azure Multi-Factor Authentication Web Service SDK telepítése az IIS 7.x vagy újabb verziójában **az Azure Multi-Factor Authentication-kiszolgálóval megegyező kiszolgálón**
* Az Azure Multi-Factor Authentication Web Service SDK védelmét egy SSL-tanúsítvány biztosítja.
* A Mobile App Web Service SSL-en keresztül tud csatlakozni az Azure Multi-Factor Authentication Web Service SDK-hoz
* A Mobile App Web Service a „PhoneFactor-adminisztrátorok” nevű biztonsági csoportban található szolgáltatásfiók hitelesítő adataival tud hitelesítést végezni az Azure MFA Web Service SDK-ban. Ez a szolgáltatásfiók és csoport az Active Directoryban található meg, ha az Azure Multi-Factor Authentication-kiszolgáló tartományhoz csatlakoztatott kiszolgálón fut. Ez a szolgáltatásfiók és csoport helyben található meg az Azure Multi-Factor Authentication-kiszolgálón, ha az nincs tartományhoz csatlakoztatva.

## <a name="install-the-mobile-app-web-service"></a>A Mobile App Web Service telepítése

A Mobile App Web Service telepítése előtt vegye figyelembe a következő részleteket:

* A Mobile App Web Service telepítése 8.0-s vagy újabb verzió esetén nem szükséges. Csak [a mobilalkalmazás konfigurálásáról](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server) szóló szakasz lépéseit végezze el.
* Olyan szolgáltatásfiók szükséges, amely a „PhoneFactor-adminisztrátorok” csoport tagja. Ez lehet ugyanaz a fiók, amelyet a felhasználói portál telepítéséhez használt.
* Hasznos lehet, ha megnyit egy webböngészőt az internetes webkiszolgálón és megkeresi a Web Service SDK a web.config fájlban megadott URL-címét. Ha a böngésző sikeresen eléri a webszolgáltatást, kérnie kell a hitelesítő adatok megadását. Írja be a web.config fájlban megadott felhasználónevet és jelszót pontosan úgy, ahogyan az a fájlban megjelenik. Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.
* Ha fordított proxy vagy tűzfal található a Mobile App Web Service webkiszolgálója előtt és SSL-tehermentesítést végez, a Mobile App Web Service web.config fájljának szerkesztésével a Mobile App Web Service http-t használhat https helyett. Az SSL továbbra is szükséges a Mobile App és a tűzfal/fordított proxy között. Adja hozzá a következő kulcsot az \<appSettings\> szakaszhoz:

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>A Web Service SDK telepítése

Mindkét forgatókönyvre érvényes, hogy ha az Azure Multi-Factor Authentication Web Service SDK még **nincs** telepítve az Azure Multi-Factor Authentication- (MFA-) kiszolgálón, akkor végre kell hajtania a következő lépéseket.

1. Nyissa meg a Multi-Factor Authentication-kiszolgáló konzolját.
2. Lépjen a **Web Service SDK** részre, majd válassza a **Web Service SDK telepítése** lehetőséget.
3. Végezze el a telepítést az alapértelmezett értékekkel, hacsak nem kell valamilyen okból módosítania őket.
4. Kössön egy SSL-tanúsítványt a helyhez az IIS-ben.

Ha kérdései vannak az SSL-tanúsítvány IIS-kiszolgálón történő konfigurálásáról, olvassa el [Az SSL beállítása az IIS-en](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) cikket.

A Web Service SDK védelméhez SSL-tanúsítvány használata szükséges. Erre a célra megfelel egy önaláírt tanúsítvány. Importálja a tanúsítványt a felhasználói portál webkiszolgálójának helyi számítógépfiókja „Megbízható legfelső szintű hitelesítésszolgáltatók” tárolójába, hogy az megbízhatónak tekintse ezt a tanúsítványt az SSL-kapcsolat elindításakor.

![MFA-kiszolgáló konfigurálása – Web Service SDK](./media/howto-mfaserver-deploy-mobileapp/sdk.png)

### <a name="install-the-service"></a>A szolgáltatás telepítése

1. **Az MFA-kiszolgálón** keresse meg a telepítés helyét.
2. Nyissa meg azt a mappát, amelybe az Azure MFA-kiszolgáló telepítve van (alapértelmezés szerint **C:\Program Files\Azure Multi-Factor Authentication**).
3. Keresse meg a **MultiFactorAuthenticationMobileAppWebServiceSetup64** nevű telepítőfájlt. Ha a kiszolgáló **nem** rendelkezik internetkapcsolattal, másolja át a telepítőfájlt az internetkapcsolattal rendelkező kiszolgálóra.
4. Ha az MFA-kiszolgáló **nem** rendelkezik internetkapcsolattal, váltson át az **internetkapcsolattal rendelkező kiszolgálóra**.
5. Futtassa rendszergazdaként a **MultiFactorAuthenticationMobileAppWebServiceSetup64** telepítőfájlt, szükség esetén módosítsa a Hely beállítást, és tetszés szerint adjon meg egy rövid nevet a virtuális címtár számára.
6. A telepítés befejezése után lépjen a **C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService** könyvtárra (vagy a virtuális könyvtár neve alapján a megfelelő könyvtárra), és szerkessze a Web.Config fájlt.

   * Keresse meg a **„WEB_SERVICE_SDK_AUTHENTICATION_USERNAME”** kulcsot, és módosítsa a **value=""** értéket **value="DOMAIN\User"** értékre, ahol a DOMAIN\User egy olyan szolgáltatásfiók, amely a „PhoneFactor-adminisztrátorok” csoport tagja.
   * Keresse meg a **„WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD”** kulcsot, és módosítsa a **value=""** értéket **value="Password"** értékre, ahol a Password az előző sorban megadott szolgáltatásfiókhoz tartozó jelszó.
   * Keresse meg a **pfMobile App Web Service_pfwssdk_PfWsSdk** beállítást, és módosítsa az értéket **http://localhost:4898/PfWsSdk.asmx** értékről a Web Service SDK URL-címére (például: https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx)).
   * Mentse a Web.Config fájlt, és zárja be a Jegyzettömböt.

   > [!NOTE]
   > Mivel ehhez a kapcsolathoz a rendszer SSL-t használ, a Web Service SDK-ra **nem IP-cím**, hanem **teljes tartománynév (FQDN)** alapján kell hivatkozni. Az SSL-tanúsítvány a teljes tartománynévhez lesz kiadva, és a használt URL-címnek meg kell egyeznie a tanúsítványon szereplő névvel.

7. Ha az a webhely, amelyen a Mobile App Web Service telepítve lett, még nincs nyilvánosan aláírt tanúsítványhoz kötve, telepítse a tanúsítványt a kiszolgálóra, nyissa meg az IIS-kezelőt, és kösse a tanúsítványt a webhelyhez.
8. Nyisson meg egy webböngészőt bármilyen számítógépről, és lépjen arra az URL-címre, ahol a Mobile App Web Service telepítve van (például: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService)). Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.
9. A webes szolgáltatások SDK-jában elérhető módszerekkel kapcsolatos további információkért tekintse meg az MFA-kiszolgáló súgófájlját.
10. Most, hogy telepítette a Mobile App Web Service-t, konfigurálnia kell az Azure Multi-Factor Authentication-kiszolgálót, hogy működjön a portállal.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>A mobilalkalmazás beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón

1. A Multi-Factor Authentication-kiszolgáló konzoljában kattintson a Felhasználói portál ikonra. Ha a felhasználók vezérelhetik a hitelesítési módszereiket, a Beállítások lapon a **Módszer kiválasztásának engedélyezése a felhasználóknak** lehetőség alatt jelölje be a **Mobile App** jelölőnégyzetét. Ha a szolgáltatás nincs engedélyezve, a végfelhasználóknak kapcsolatba kell lépniük a támogatási szolgálattal a Mobile App aktiválásának befejezéséhez.
2. Jelölje be a **Mobilalkalmazás aktiválásának engedélyezése a felhasználóknak** jelölőnégyzetet.
3. Jelölje be a **Felhasználó beléptetésének engedélyezése** jelölőnégyzetet.
4. Kattintson a **Mobile App** ikonra.
5. Ha 8.0-s vagy újabb verziót használ, ugorja át a következő lépést: Adja meg az URL-címet, amelyet a MultiFactorAuthenticationMobileAppWebServiceSetup64 telepítésekor létrehozott virtuális könyvtárral használ: (Példa: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/) a **Mobile App Web Service URL:** mezőben).
6. A **Fiók neve** mezőben adja meg a fiókhoz tartozó mobilalkalmazásban megjeleníteni kívánt cég- vagy szervezetnevet.
   ![MFA-kiszolgáló konfigurálása – A Mobile App beállításai](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>További lépések

- [Speciális, az Azure Multi-Factor Authenticationre és külső VPN-ekre vonatkozó forgatókönyvek](howto-mfaserver-nps-vpn.md).
