<properties 
    pageTitle="Első lépések az MFA-kiszolgáló Mobile App Web Service szolgáltatásával" 
    description="Az Azure Multi-Factor Authentication alkalmazás egy további sávon kívüli hitelesítési lehetőséget kínál.  Ez lehetővé teszi, hogy az MFA-kiszolgáló leküldéses értesítéseket küldjön a felhasználóknak." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/04/2016" 
    ms.author="billmath"/>

# Első lépések az MFA-kiszolgáló Mobile App Web Service szolgáltatásával

Az Azure Multi-Factor Authentication alkalmazás egy további sávon kívüli hitelesítési lehetőséget kínál. Automatikus telefonhívás vagy SMS küldése helyett a felhasználó bejelentkezésekor az Azure Multi-Factor Authentication értesítést küld le az Azure Multi-Factor Authentication alkalmazásba a felhasználó okostelefonjára vagy táblagépére. A felhasználó egyszerűen a „Hitelesítés” lehetőségre koppint (vagy a PIN-kód megadása után a „Hitelesítés” lehetőségre koppint) a bejelentkezéshez az alkalmazásban. 

Az Azure Multi-Factor Authentication alkalmazás használatához a következőkre van szükség, hogy az alkalmazás sikeresen kommunikáljon a Mobile App Web Service-szel: 

- Tekintse meg a hardver- és szoftverkövetelményeket a Hardver- és szoftverkövetelmények című részben
- Az Azure Multi-Factor Authentication-kiszolgáló 6.0-s vagy újabb verzióját kell használnia
- A Mobile App Web Service-t Microsoft® Internet Information Services (IIS) IIS 7.x vagy újabb verziót futtató internetes webkiszolgálóra kell telepíteni.  További információ az IIS-ről: [IIS.NET](http://www.iis.net/).
- Ellenőrizze, hogy az ASP.NET v4.0.30319 telepítve és regisztrálva van-e, valamint az állapota Engedélyezett-e
- A szükséges szerepkör-szolgáltatások közé tartozik az ASP.NET és az IIS 6 metabázisával való kompatibilitás.
- A Mobile App Web Service-nek elérhetőnek kell lennie egy nyilvános URL-címről
- A Mobile App Web Service védelmét SSL-tanúsítvánnyal kell biztosítani.
- Az Azure Multi-Factor Authentication Web Service SDK-t az IIS 7.x vagy újabb verziójában kell telepíteni arra a kiszolgálóra, ahol az Azure Multi-Factor Authentication-kiszolgáló telepítve van
- Az Azure Multi-Factor Authentication Web Service SDK védelmét SSL-tanúsítvánnyal kell biztosítani.
- A Mobile App Web Service-nek SSL-en keresztül kell tudnia csatlakozni az Azure Multi-Factor Authentication Web Service SDK-hoz.
- A Mobile App Web Service-nek olyan szolgáltatásfiók hitelesítő adataival kell tudnia hitelesítést végeznie az Azure Multi-Factor Authentication Web Service SDK-ban, amely a PhoneFactor-adminisztrátorok nevű biztonsági csoport tagja. Ez a szolgáltatásfiók és csoport az Active Directoryban található meg, ha az Azure Multi-Factor Authentication-kiszolgáló tartományhoz csatlakoztatott kiszolgálón fut. Ez a szolgáltatásfiók és csoport helyben található meg az Azure Multi-Factor Authentication-kiszolgálón, ha az nincs tartományhoz csatlakoztatva.


A felhasználói portál az Azure Multi-Factor Authentication-kiszolgálótól eltérő kiszolgálón való telepítésének három lépése a következő:

1. A Web Service SDK telepítése
2. A Mobile App Web Service telepítése
3. A mobilalkalmazás beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón
4. Az Azure Multi-Factor Authentication alkalmazás aktiválása a végfelhasználók számára

## A Web Service SDK telepítése

Ha az Azure Multi-Factor Authentication Web Service SDK még nincs telepítve az Azure Multi-Factor Authentication-kiszolgálón, lépjen a kiszolgálóra és nyissa meg az Azure Multi-Factor Authentication-kiszolgálót. Kattintson a Web Service SDK ikonra, majd a Web Service SDK telepítése… gombra, és kövesse a megjelenő utasításokat. A Web Service SDK védelméhez SSL-tanúsítvány használata szükséges. Erre a célra megfelel egy önaláírt tanúsítvány, de azt a felhasználói portál webkiszolgálójának Helyi számítógép fiókja „Megbízható legfelső szintű hitelesítésszolgáltatók” tárolójába kell importálni, hogy megbízzon ebben a tanúsítványban az SSL-kapcsolat elindításakor. 

<center>![Telepítés](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## A Mobile App Web Service telepítése
A Mobile App Web Service telepítése előtt vegye figyelembe a következőket:

- Ha az Azure Multi-Factor Authentication felhasználói portál már telepítve van az internetes kiszolgálón, a felhasználónév, a jelszó és a Web Service SDK-ra mutató URL-cím átmásolható a felhasználói portál web.config fájljából. 
- Hasznos lehet, ha megnyit egy webböngészőt az internetes webkiszolgálón és megkeresi a Web Service SDK a web.config fájlban megadott URL-címét. Ha a böngésző sikeresen eléri a webszolgáltatást, kérnie kell a hitelesítő adatok megadását. Írja be a web.config fájlban megadott felhasználónevet és jelszót pontosan úgy, ahogyan az a fájlban megjelenik. Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.
- Ha fordított proxy vagy tűzfal található a Mobile App Web Service webkiszolgálója előtt és SSL-tehermentesítést végez, a Mobile App Web Service web.config fájljának szerkesztésével a következő kulcsot adhatja a(z) <appSettings> szakaszhoz, hogy a Mobile App Web Service http-t használhasson https helyett. Az SSL azonban továbbra is szükséges a Mobile App és a tűzfal/fordított proxy között. <add key="SSL_REQUIRED" value="false"/> 

### A Mobile App Web Service telepítése

<ol>
<li>Nyissa meg a Windows Intézőt az Azure Multi-Factor Authentication-kiszolgálón, és keresse meg azt a mappát, amelybe az Azure Multi-Factor Authentication-kiszolgáló telepítve van (például C:\Program Files\Azure Multi-Factor Authentication). Válassza az Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup telepítőfájl 32 bites vagy 64 bites verzióját annak a kiszolgálónak megfelelően, amelyre a Mobile App Web Service-t telepíti. Másolja a telepítőfájlt az internetes kiszolgálóra.</li> 

<li>Az internetes webkiszolgálón a telepítőfájlt rendszergazdai jogosultságokkal kell futtatnia. Ennek legegyszerűbb módja, ha rendszergazdaként megnyit egy parancssort és arra a helyre lép, ahová a telepítőfájlt másolta.</li>  

<li>Futtassa a Multi-Factor AuthenticationMobileAppWebServiceSetup telepítőfájlt, szükség esetén módosítsa a Hely beállítást, és a virtuális címtár számára adjon meg egy rövid nevet, például: „PA”. Rövid virtuális-címtárnév használata javasolt, mivel a felhasználóknak a Mobile App Web Service URL-címét meg kell adniuk a mobileszközeiken aktiváláskor.</li> 

<li>Az Azure Multi-Factor AuthenticationMobileAppWebServiceSetup telepítésének befejezése után lépjen a C:\inetpub\wwwroot\PA könyvtárra (vagy a virtuális könyvtár neve alapján a megfelelő könyvtárra), és szerkessze a web.config fájlt.</li>  

<li>Keresse meg a WEB_SERVICE_SDK_AUTHENTICATION_USERNAME és a WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD kulcsot, és állítsa az értékeket azon szolgáltatásfiók felhasználónevére és jelszavára, amely a PhoneFactor-adminisztrátorok biztonsági csoport tagja (lásd a fenti Követelmények szakaszt). Ez lehet ugyanaz a fiók, amelyet a Azure Multi-Factor Authentication felhasználói portál identitásaként használ, ha az már korábban telepítve volt. Ügyeljen rá, hogy a felhasználónév és a jelszó a sor végén lévő idézőjelek között legyen megadva (value=””/>). Ajánlott teljes felhasználónevet használni (például tartomány\felhasználónév vagy gép\felhasználónév).</li>  

<li>Keresse meg a pfMobile App Web Service_pfwssdk_PfWsSdk beállítást, és módosítsa az értéket „http://localhost:4898/PfWsSdk.asmx” értékről azon Web Service SDK URL-címére, amely az Azure Multi-Factor Authentication-kiszolgálón fut (például https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Mivel ehhez a kapcsolathoz a rendszer SSL-t használ, a Web Service SDK-ra kiszolgálónév alapján kell hivatkozni és nem IP-cím alapján, mivel az SSL-tanúsítvány a kiszolgálónévhez lesz kiadva, és a használt URL-nek meg kell felelnie a tanúsítványon lévő névnek. Ha a kiszolgáló neve nem az internetes kiszolgálóról származó IP-címmé oldódik fel, adjon egy bejegyzést a kiszolgálón lévő gazdafájlhoz az Azure Multi-Factor Authentication-kiszolgáló nevének az IP-címére való leképezéséhez. A módosítások elvégzése után mentse a web.config fájlt.</li>  

<li>Ha azon webhely, amelyen a Mobile App Web Service telepítve lett (például alapértelmezett webhely) még nincs nyilvánosan aláírt tanúsítvánnyal kötve, telepítse a tanúsítványt a kiszolgálóra, ha még nincs telepítve, nyissa meg az IIS-kezelőt, és kösse a tanúsítványt a webhelyhez.</li>  

<li>Nyisson meg egy webböngészőt valamelyik számítógépről és lépjen arra az URL-címre, ahol a Mobile App Web Service telepítve van (például: https://www.publicwebsite.com/PA ). Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.</li> 

### A mobilalkalmazás beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón
Most, hogy telepítette a Mobile App Web Service-t, konfigurálnia kell az Azure Multi-Factor Authentication-kiszolgálót, hogy működjön a portállal.

#### A mobilalkalmazás beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a felhasználói portál ikonra. Ha a felhasználók vezérelhetik a hitelesítési módszereiket, a Beállítások lapon a Módszer kiválasztásának engedélyezése a felhasználóknak lehetőség alatt jelölje be a Mobile App jelölőnégyzetét. Ha a szolgáltatás nincs engedélyezve, a végfelhasználóknak kapcsolatba kell lépniük a támogatási szolgálattal a Mobile App aktiválásának befejezéséhez.
2. Jelölje be a Mobilalkalmazás aktiválásának engedélyezése a felhasználóknak jelölőnégyzetet.
3. Jelölje be a Felhasználó beléptetésének engedélyezése jelölőnégyzetet.
4. Kattintson a Mobile App ikonra.
5. Adja meg az Azure Multi-Factor AuthenticationMobileAppWebServiceSetup telepítésekor létrehozott virtuális címtárhoz használt URL-címet. A rendelkezésre álló helyen megadhat egy fióknevet. Ez a vállalati név jelenik meg a mobilalkalmazásban. Ha a mezőt üresen hagyja, az Azure felügyeleti portálon létrehozott Multi-Factor Auth-szolgáltató neve jelenik meg. 



<center>![Telepítés](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>
 



<!--HONumber=sep16_HO1-->


