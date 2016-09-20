<properties 
    pageTitle="Frissítés a PhoneFactor ügynökről az Azure Multi-Factor Authentication-kiszolgálóra" 
    description="Ez a dokumentum ismerteti az Azure MFA-kiszolgálóval kapcsolatos első lépéseket, illetve azt, hogy hogyan frissíthet a régebbi phonefactor ügynökről." 
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

# Frissítés a PhoneFactor ügynökről az Azure Multi-Factor Authentication-kiszolgálóra

Ha a PhoneFactor ügynök 5.x vagy régebbi verziójáról frissít az Azure Multi-Factor Authentication-kiszolgálóra, a PhoneFactor ügynököt és a kapcsolódó összetevőit el kell távolítani a Multi-Factor Authentication-kiszolgáló és kapcsolódó összetevőinek telepítése előtt. 

## Frissítés a PhoneFactor ügynökről az Azure Multi-Factor Authentication-kiszolgálóra
<ol>
<li>Először készítsen biztonsági másolatot a PhoneFactor-adatfájlokról. Az alapértelmezett telepítési hely a C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata mappa.


<li>Ha a felhasználói portál telepítve van:</li>
<ol>
<li>Nyissa meg a telepítési mappát, és készítsen biztonsági másolatot a web.config fájlról. Az alapértelmezett telepítési hely a C:\inetpub\wwwroot\PhoneFactor mappa.</li>


<li>Ha hozzáadott egyéni témákat a portálhoz, készítsen biztonsági másolatot az egyéni mappáról a C:\inetpub\wwwroot\PhoneFactor\App_Themes könyvtárban.</li>


<li>Távolítsa el a felhasználói portált a PhoneFactor ügynök segítségével (csak akkor érhető el, ha ugyanazon a kiszolgálón telepítette, amelyen a PhoneFactor ügynököt) vagy a Windows Programok és szolgáltatások lapjáról.</li></ol>




<li>Ha a Mobile App Web Service telepítve van:
<ol>
<li>Nyissa meg a telepítési mappát, és készítsen biztonsági másolatot a web.config fájlról. Az alapértelmezett telepítési hely a C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService mappa.</li>
<li>Távolítsa el a Mobile App Web Service-t a Windows Programok és szolgáltatások lapja segítségével.</li></ol>

<li>Ha a Web Service SDK telepítve van, távolítsa el a PhoneFactor ügynök vagy a Windows Programok és szolgáltatások lapja segítségével.

<li>Távolítsa el a PhoneFactor ügynököt a Windows Programok és szolgáltatások lapja segítségével.

<li>Telepítse a Multi-Factor Authentication-kiszolgálót. Ne feledje, hogy a rendszer a telepítési útvonalat a korábbi PhoneFactor ügynök telepítése által létrehozott beállításjegyzékből olvassa be, ezért ugyanarra a helyre fogja telepíteni (pl. C:\Program Files\PhoneFactor). Az új telepítések eltérő alapértelmezett telepítési útvonallal rendelkeznek (pl. C:\Program Files\Multi-Factor Authentication Server). Az előző PhoneFactor ügynök által hátrahagyott adatfájlt a rendszer frissíti a telepítés során, ezért a felhasználók és beállítások megmaradnak az új Multi-Factor Authentication-kiszolgáló telepítése után.

<li>Ha a rendszer kéri, aktiválja a Multi-Factor Authentication-kiszolgálót, és győződjön meg arról, hogy a megfelelő replikációs csoporthoz van hozzárendelve.

<li>Ha a Web Service SDK-t korábban telepítette, telepítse az új Webszolgáltatás SDK-t a Multi-Factor Authentication-kiszolgáló felhasználói felületén keresztül. Vegye figyelembe, hogy az alapértelmezett virtuális címtár neve mostantól „PhoneFactorWebServiceSdk” helyett „MultiFactorAuthWebServiceSdk”. Ha a korábbi nevet szeretné használni, a virtuális címtár nevét a telepítés közben módosítania kell. Ellenkező esetben, ha engedélyezi a telepítés során az új alapértelmezett név használatát, módosítania kell az URL-címet minden alkalmazásban, amely a Web Service SDK-ra hivatkozik, például a felhasználói portál és a Mobile App Web Service, hogy a megfelelő helyre mutassanak.

<li>Ha a felhasználói portált korábban telepítette a PhoneFactor ügynök kiszolgálóján, telepítse az új Multi-Factor Authentication felhasználói portált a Multi-Factor Authentication-kiszolgáló felhasználói felületén keresztül. Vegye figyelembe, hogy az alapértelmezett virtuális címtár neve mostantól „PhoneFactor” helyett „MultiFactorAuth”. Ha a korábbi nevet szeretné használni, a virtuális címtár nevét a telepítés közben módosítania kell. Ellenkező esetben, ha a telepítés során engedélyezi az új alapértelmezett név használatát, kattintson a felhasználói portál ikonra a Multi-Factor Authentication-kiszolgálón és frissítse a felhasználói portál URL-címét a Beállítások lapon. 

<li>Ha a felhasználói portál és/vagy a Mobile App Web Service korábban a PhoneFactor ügynöktől eltérő kiszolgálón volt telepítve:
<ol>
<li>Nyissa meg a telepítési helyet (például C:\Program Files\PhoneFactor), és másolja a megfelelő telepítőket a másik kiszolgálóra. A felhasználói portál és a Mobile App Web Service is rendelkezik 32 bites és 64 bites telepítővel is. Ezeknek a neve MultiFactorAuthenticationUserPortalSetupXX.msi, illetve MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.</li>
<li>A webkiszolgálón a felhasználói portál telepítéséhez nyisson meg egy parancssort rendszergazdaként, és futtassa a MultiFactorAuthenticationUserPortalSetupXX.msi fájlt. Vegye figyelembe, hogy az alapértelmezett virtuális címtár neve mostantól „PhoneFactor” helyett „MultiFactorAuth”. Ha a korábbi nevet szeretné használni, a virtuális címtár nevét a telepítés közben módosítania kell. Ellenkező esetben, ha a telepítés során engedélyezi az új alapértelmezett név használatát, kattintson a felhasználói portál ikonra a Multi-Factor Authentication-kiszolgálón és frissítse a felhasználói portál URL-címét a Beállítások lapon. A meglévő felhasználókat tájékoztatni kell az új URL-címről.</li>
<li>Lépjen a felhasználói portál telepítési helyére (pl. C:\inetpub\wwwroot\MultiFactorAuth), és szerkessze a web.config fájlt. Másolja az appSettings és az applicationSettings szakaszban található értékeket az eredeti web.config fájlból, amelyről a frissítés előtt biztonsági másolatot készített, az új web.config fájlba. Ha a Web Service SDK telepítésekor megtartotta az új alapértelmezett virtuális címtár nevét, módosítsa az URL-címet az applicationSettings szakaszban, hogy a megfelelő helyre mutasson. Ha bármilyen egyéb alapértelmezett értéket módosított a korábbi web.config fájlban, alkalmazza ugyanezeket a módosításokat az új web.config fájlra.</li>
<li>A Mobile App Web Service a webkiszolgálón való telepítéséhez nyisson meg egy parancssort rendszergazdaként, és futtassa a MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi fájlt. Vegye figyelembe, hogy az alapértelmezett virtuális címtár neve mostantól „PhoneFactorPhoneAppWebService” helyett „MultiFactorAuthMobileAppWebService”. Ha a korábbi nevet szeretné használni, a virtuális címtár nevét a telepítés közben módosítania kell. Érdemes lehet egy rövidebb nevet választani, hogy a végfelhasználók egyszerűbben beírhassák a mobileszközeiken. Ellenkező esetben, ha a telepítés során engedélyezi az új alapértelmezett név használatát, kattintson a Mobile App ikonra a Multi-Factor Authentication-kiszolgálón és frissítse a Mobile App Web Service URL-címét.</li>
<li>Nyissa meg a Mobile App Web Service telepítési helyét (pl. C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService), és szerkessze a web.config fájlt. Másolja az appSettings és az applicationSettings szakaszban található értékeket az eredeti web.config fájlból, amelyről a frissítés előtt biztonsági másolatot készített, az új web.config fájlba. Ha a Web Service SDK telepítésekor megtartotta az új alapértelmezett virtuális címtár nevét, módosítsa az URL-címet az applicationSettings szakaszban, hogy a megfelelő helyre mutasson. Ha bármilyen egyéb alapértelmezett értéket módosított a korábbi web.config fájlban, alkalmazza ugyanezeket a módosításokat az új web.config fájlra.</li></ol>


 


 



<!--HONumber=sep16_HO1-->


