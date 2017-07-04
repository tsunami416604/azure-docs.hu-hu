---
title: "Az Azure MFA-kiszolgáló Mobile App Web Service szolgáltatása | Microsoft Docs"
description: "A Microsoft Authenticator alkalmazás egy további sávon kívüli hitelesítési lehetőséget kínál.  Ez lehetővé teszi, hogy az MFA-kiszolgáló leküldéses értesítéseket küldjön a felhasználóknak."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/15/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017,it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: 4014bf0217e25ea9bc8473ef2383279e5eb79b87
ms.contentlocale: hu-hu
ms.lasthandoff: 02/28/2017

---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>A mobilalkalmazásos hitelesítés engedélyezése az Azure Multi-Factor Authentication-kiszolgálóval

A Microsoft Authenticator alkalmazás egy további sávon kívüli ellenőrzési lehetőséget kínál. Automatikus telefonhívás vagy SMS küldése helyett a felhasználó bejelentkezésekor az Azure Multi-Factor Authentication értesítést küld le a Microsoft Authenticator alkalmazásba a felhasználó okostelefonján vagy táblagépén. A felhasználó egyszerűen az **Ellenőrzés** (vagy a PIN-kód megadása után a „Hitelesítés”) elemre koppint a bejelentkezés befejezéséhez.

Ha a telefonon a vétel nem megbízható, kétlépéses ellenőrzést biztosító mobilalkalmazás használata ajánlott. Ha az alkalmazást OATH token előállítására használja, nincs szükség hálózatra vagy internetkapcsolatra.

A felhasználói portál az Azure Multi-Factor Authentication-kiszolgálótól eltérő kiszolgálón való telepítésének lépései a következők:

1. A Web Service SDK telepítése
2. A Mobile App Web Service telepítése
3. A mobilalkalmazás beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón
4. A Microsoft Authenticator alkalmazás aktiválása a végfelhasználók számára

## <a name="requirements"></a>Követelmények

A Microsoft Authenticator alkalmazás használatához a következőkre van szükség, hogy az alkalmazás sikeresen kommunikáljon a Mobile App Web Service-szel:

* Az Azure Multi-Factor Authentication-kiszolgáló 6.0-s vagy újabb verziója
* A Mobile App Web Service telepítése Microsoft® [Internet Information Services (IIS) 7.x vagy újabb](http://www.iis.net/) verziót futtató internetes webkiszolgálóra
* Az ASP.NET 4.0.30319-es verziója telepítve és regisztrálva van, és engedélyezett állapotú
* A szükséges szerepkör-szolgáltatások közé tartozik az ASP.NET és az IIS 6 metabázisával való kompatibilitás.
* A Mobile App Web Service elérhető egy nyilvános URL-címről
* A Mobile App Web Service védelmét egy SSL-tanúsítvány biztosítja.
* Az Azure Multi-Factor Authentication Web Service SDK telepítése az IIS 7.x vagy újabb verziójában az Azure Multi-Factor Authentication-kiszolgálóval megegyező kiszolgálón
* Az Azure Multi-Factor Authentication Web Service SDK védelmét egy SSL-tanúsítvány biztosítja.
* A Mobile App Web Service SSL-en keresztül tud csatlakozni az Azure Multi-Factor Authentication Web Service SDK-hoz
* A Mobile App Web Service a „PhoneFactor-adminisztrátorok” nevű biztonsági csoportban található szolgáltatásfiók hitelesítő adataival tud hitelesítést végezni az Azure MFA Web Service SDK-ban. Ez a szolgáltatásfiók és csoport az Active Directoryban található meg, ha az Azure Multi-Factor Authentication-kiszolgáló tartományhoz csatlakoztatott kiszolgálón fut. Ez a szolgáltatásfiók és csoport helyben található meg az Azure Multi-Factor Authentication-kiszolgálón, ha az nincs tartományhoz csatlakoztatva.


## <a name="install-the-web-service-sdk"></a>A Web Service SDK telepítése
Ha az Azure Multi-Factor Authentication Web Service SDK még nincs telepítve az Azure Multi-Factor Authentication- (MFA-) kiszolgálón, lépjen a kiszolgálóra, és nyissa meg az Azure MFA-kiszolgálót.

1. Kattintson a Web Service SDK ikonra.
2. Kattintson a **Web Service SDK telepítése** gombra, és kövesse a megjelenő utasításokat.

A Web Service SDK védelméhez SSL-tanúsítvány használata szükséges. Erre a célra megfelel egy önaláírt tanúsítvány. Importálja a tanúsítványt a felhasználói portál webkiszolgálójának helyi számítógépfiókja „Megbízható legfelső szintű hitelesítésszolgáltatók” tárolójába, hogy az megbízhatónak tekintse ezt a tanúsítványt az SSL-kapcsolat elindításakor.

![Beállítás](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

## <a name="install-the-mobile-app-web-service"></a>A Mobile App Web Service telepítése
A Mobile App Web Service telepítése előtt vegye figyelembe a következő részleteket:

* Ha az Azure MFA felhasználói portál már telepítve van az internetes kiszolgálón, a felhasználónév, a jelszó és a Web Service SDK URL-címe másolható a felhasználói portál web.config fájljából.
* Hasznos lehet, ha megnyit egy webböngészőt az internetes webkiszolgálón és megkeresi a Web Service SDK a web.config fájlban megadott URL-címét. Ha a böngésző sikeresen eléri a webszolgáltatást, kérnie kell a hitelesítő adatok megadását. Írja be a web.config fájlban megadott felhasználónevet és jelszót pontosan úgy, ahogyan az a fájlban megjelenik. Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.
* Ha fordított proxy vagy tűzfal található a Mobile App Web Service webkiszolgálója előtt és SSL-tehermentesítést végez, a Mobile App Web Service web.config fájljának szerkesztésével a Mobile App Web Service http-t használhat https helyett. Az SSL továbbra is szükséges a Mobile App és a tűzfal/fordított proxy között. Adja hozzá a következő kulcsot az \<appSettings\> szakaszhoz:

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-service"></a>A szolgáltatás telepítése

1. Nyissa meg a Windows Explorert az Azure Multi-Factor Authentication-kiszolgálón, és keresse meg azt a mappát, amelybe az Azure MFA-kiszolgáló telepítve van (általában C:\Program Files\Azure Multi-Factor Authentication). Válassza az Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup telepítőfájljának 32 bites vagy 64 bites verzióját. Másolja a telepítőfájlt az internetes kiszolgálóra.

2. Az internetes webkiszolgálón futtassa a telepítőfájlt rendszergazdai jogosultságokkal. Nyisson meg egy parancssort rendszergazdaként, és lépjen arra a helyre, ahová a telepítőfájlt másolta.

3. Futtassa a Multi-Factor AuthenticationMobileAppWebServiceSetup telepítőfájlt, szükség esetén módosítsa a Hely beállítást, és a virtuális címtár számára adjon meg egy rövid nevet, például: „PA”.

  Rövid virtuális-címtárnév használata javasolt, mivel a felhasználóknak a Mobile App Web Service URL-címét meg kell adniuk a mobileszközeiken aktiváláskor.

4. Az Azure Multi-Factor AuthenticationMobileAppWebServiceSetup telepítésének befejezése után lépjen a C:\inetpub\wwwroot\PA könyvtárra (vagy a virtuális könyvtár neve alapján a megfelelő könyvtárra), és szerkessze a web.config fájlt.

5. Keresse meg a WEB_SERVICE_SDK_AUTHENTICATION_USERNAME és a WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD kulcsot. Állítsa az értékeket azon szolgáltatásfiók felhasználónevére és jelszavára, amely a PhoneFactor-adminisztrátorok biztonsági csoport tagja. Ez lehet ugyanaz a fiók, amelyet a Azure Multi-Factor Authentication felhasználói portál identitásaként használ, ha az már korábban telepítve volt. Ügyeljen rá, hogy a felhasználónév és a jelszó a sor végén lévő idézőjelek között legyen megadva (value=””/>). Teljes felhasználónevet adjon meg, mint tartomány\felhasználónév vagy gép\felhasználónév.  

6. Keresse meg a pfMobile App Web Service_pfwssdk_PfWsSdk beállítást. Módosítsa az értéket *http://localhost:4898/PfWsSdk.asmx* értékről azon Web Service SDK URL-címére, amely az Azure Multi-Factor Authentication-kiszolgálón fut (például https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx).

  Mivel ehhez a kapcsolathoz a rendszer SSL-t használ, a Web Service SDK-ra kiszolgálónév alapján kell hivatkozni és nem IP-cím alapján. Az SSL-tanúsítvány a kiszolgálónévhez lesz kiadva, és a használt URL-címnek meg kell egyeznie a tanúsítványon szereplő névvel. Előfordulhat, hogy a kiszolgáló neve nem az internetes kiszolgálóról származó IP-címmé lesz feloldva. Ebben az esetben adjon egy bejegyzést a kiszolgálón lévő gazdafájlhoz az Azure Multi-Factor Authentication-kiszolgáló nevének az IP-címére való leképezéséhez. A módosítások elvégzése után mentse a web.config fájlt.

7. Ha az a webhely, amelyen a Mobile App Web Service telepítve lett, még nincs nyilvánosan aláírt tanúsítvánnyal kötve, telepítse a tanúsítványt a kiszolgálóra, nyissa meg az IIS-kezelőt, és kösse a tanúsítványt a webhelyhez.

8. Nyisson meg egy webböngészőt valamelyik számítógépről, és lépjen arra az URL-címre, ahol a Mobile App Web Service telepítve van (például: https://www.publicwebsite.com/PA ). Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>A mobilalkalmazás beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón
Most, hogy telepítette a Mobile App Web Service-t, konfigurálnia kell az Azure Multi-Factor Authentication-kiszolgálót, hogy működjön a portállal.

1. Az Azure MFA-kiszolgálón kattintson a Felhasználói portál ikonra. Ha a felhasználók vezérelhetik a hitelesítési módszereiket, a Beállítások lapon a **Módszer kiválasztásának engedélyezése a felhasználóknak** lehetőség alatt jelölje be a **Mobile App** jelölőnégyzetét. Ha a szolgáltatás nincs engedélyezve, a végfelhasználóknak kapcsolatba kell lépniük a támogatási szolgálattal a Mobile App aktiválásának befejezéséhez.
2. Jelölje be a **Mobilalkalmazás aktiválásának engedélyezése a felhasználóknak** jelölőnégyzetet.
3. Jelölje be a **Felhasználó beléptetésének engedélyezése** jelölőnégyzetet.
4. Kattintson a Mobile App ikonra.
5. Adja meg az Azure Multi-Factor AuthenticationMobileAppWebServiceSetup telepítésekor létrehozott virtuális címtárhoz használt URL-címet. A rendelkezésre álló helyen megadhat egy fióknevet. Ez a vállalati név jelenik meg a mobilalkalmazásban. Ha a mezőt üresen hagyja, a klasszikus Azure portálon létrehozott Multi-Factor Authentication-szolgáltató neve jelenik meg.

<center>![Telepítés](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

