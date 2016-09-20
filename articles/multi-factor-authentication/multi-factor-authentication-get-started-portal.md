<properties 
    pageTitle="A felhasználói portál üzembe helyezése az Azure Multi-Factor Authentication-kiszolgálóhoz" 
    description="Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és a felhasználói portál használatát." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="femila" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="billmath"/>

# A felhasználói portál üzembe helyezése az Azure Multi-Factor Authentication-kiszolgálóhoz

A felhasználói portál segítségével a rendszergazda telepítheti és konfigurálhatja az Azure Multi-Factor Authentication felhasználói portálját. A felhasználói portál egy IIS-webhely, ahol a felhasználók regisztrálhatnak az Azure Multi-Factor Authenticationre és kezelhetik a fiókjaikat. A felhasználók megváltoztathatják a telefonszámukat, a PIN-kódjukat, vagy mellőzhetik az Azure Multi-Factor Authenticationt a következő bejelentkezéskor. 

A felhasználók a normál felhasználónevükkel és jelszavukkal jelentkeznek be a felhasználói portálra és Azure Multi-Factor Authentication-hívást végeznek vagy biztonsági kérdésekre válaszolnak a hitelesítéshez. Ha a felhasználók regisztrációja engedélyezett, a felhasználók a felhasználói portálra való első bejelentkezéskor konfigurálják a telefonszámukat és PIN-kódjukat. 

A felhasználói portálhoz rendszergazdák állíthatók be, akik az új felhasználók hozzáadására és a meglévő felhasználók frissítésére vonatkozó engedélyt kaphatnak. 

<center>![Telepítés](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## A felhasználói portál üzembe helyezése az Azure Multi-Factor Authentication-kiszolgálóval megegyező kiszolgálón

A következő előfeltételek teljesülésére van szükség, ha a felhasználói portált az Azure Multi-Factor Authentication-kiszolgálóval megegyező kiszolgálóra szeretné telepíteni:

- Telepíteni kell az IIS-t, beleértve az asp.net-et és az IIS 6 metabázisával való kompatibilitási funkciót (IIS 7 vagy újabb verzió esetén) 
- A bejelentkezett felhasználóknak rendszergazdai jogosultságokkal kell rendelkezniük a számítógéphez és a tartományhoz, ha szükséges.  Ez azért van, mert a fióknak engedélyekre van szüksége Active Directory biztonsági csoportok létrehozásához.

### A felhasználói portál üzembe helyezése az Azure Multi-Factor Authentication-kiszolgálóhoz

1. Az Azure Multi-Factor Authentication-kiszolgálón: kattintson a bal oldali menüben lévő felhasználói portál ikonra, majd kattintson a Felhasználói portál telepítése gombra. 
1. Kattintson a Next (Tovább) gombra.
1. Kattintson a Next (Tovább) gombra.
1. Ha a számítógép tartományhoz csatlakozik, és a felhasználói portál és az Azure Multi-Factor Authentication szolgáltatás közötti kommunikáció védelmét biztosító Active Directory-konfiguráció hiányos, az Active Directory lépés jelenik meg. Kattintson a Tovább gombra a konfiguráció automatikus elvégzéséhez.
1. Kattintson a Next (Tovább) gombra.
1. Kattintson a Next (Tovább) gombra.
1. Kattintson a Bezárás gombra.
1. Nyisson meg egy webböngészőt valamelyik számítógépről és lépjen arra az URL-címre, ahol a felhasználói portál telepítve van (például: https://www.publicwebsite.com/MultiFactorAuth). Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.

<center>![Telepítés](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## Az Azure Multi-Factor Authentication-kiszolgáló felhasználói portáljának telepítése különálló kiszolgálón

Az Azure Multi-Factor Authentication alkalmazás használatához a következőkre van szükség, hogy az alkalmazás sikeresen kommunikáljon a felhasználói portállal: 

Tekintse meg a hardver- és szoftverkövetelményeket a Hardver- és szoftverkövetelmények részben:

- Az Azure Multi-Factor Authentication-kiszolgáló 6.0-s vagy újabb verzióját kell használnia.
- A felhasználói portált a Microsoft® Internet Information Services (IIS) 6.x, IIS 7.x vagy újabb verziót futtató internetes webkiszolgálóra kell telepíteni.
- Az IIS 6.x használatakor győződjön meg arról, hogy az ASP.NET 2.0.50727-es verziója telepítve van, regisztrálva van, és a beállítása Engedélyezve.
- Az IIS 7.x vagy újabb használatakor a szükséges szerepkör-szolgáltatásokba tartozik az ASP.NET és az IIS 6 metabázisával való kompatibilitás.
- A felhasználói portál védelméhez SSL-tanúsítvány használata szükséges.
- Az Azure Multi-Factor Authentication Web Service SDK-t telepíteni kell az IIS 6.x, IIS 7.x vagy újabb verziójában azon a kiszolgálón, ahol az Azure Multi-Factor Authentication-kiszolgáló telepítve van.
- Az Azure Multi-Factor Authentication Web Service SDK védelmét SSL-tanúsítvánnyal kell biztosítani.
- A felhasználói portálnak SSL-en keresztül kell tudnia csatlakozni az Azure Multi-Factor Authentication Web Service SDK-hoz.
- A felhasználói portálnak olyan szolgáltatásfiók hitelesítő adataival kell tudnia hitelesítést végeznie az Azure Multi-Factor Authentication Web Service SDK-ban, amely a PhoneFactor-adminisztrátorok nevű biztonsági csoport tagja. Ez a szolgáltatásfiók és csoport az Active Directoryban található meg, ha az Azure Multi-Factor Authentication-kiszolgáló tartományhoz csatlakoztatott kiszolgálón fut. Ez a szolgáltatásfiók és csoport helyben található meg az Azure Multi-Factor Authentication-kiszolgálón, ha az nincs tartományhoz csatlakoztatva.

A felhasználói portál az Azure Multi-Factor Authentication-kiszolgálótól eltérő kiszolgálón való telepítésének három lépése a következő:

1. A Web Service SDK telepítése
2. A felhasználói portál telepítése
3. A felhasználói portál beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón


### A Web Service SDK telepítése

Ha az Azure Multi-Factor Authentication Web Service SDK még nincs telepítve az Azure Multi-Factor Authentication-kiszolgálón, lépjen a kiszolgálóra és nyissa meg az Azure Multi-Factor Authentication-kiszolgálót. Kattintson a Web Service SDK ikonra, majd a Web Service SDK telepítése… gombra, és kövesse a megjelenő utasításokat. A Web Service SDK védelméhez SSL-tanúsítvány használata szükséges. Erre a célra megfelel egy önaláírt tanúsítvány, de azt a felhasználói portál webkiszolgálójának Helyi számítógép fiókja „Megbízható legfelső szintű hitelesítésszolgáltatók” tárolójába kell importálni, hogy megbízzon ebben a tanúsítványban az SSL-kapcsolat elindításakor. 

<center>![Telepítés](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### A felhasználói portál telepítése

Mielőtt a felhasználói portált különálló kiszolgálóra telepítené, vegye figyelembe a következőket:

- Hasznos lehet, ha megnyit egy webböngészőt az internetes webkiszolgálón és megkeresi a Web Service SDK a web.config fájlban megadott URL-címét. Ha a böngésző sikeresen eléri a webszolgáltatást, kérnie kell a hitelesítő adatok megadását. Írja be a web.config fájlban megadott felhasználónevet és jelszót pontosan úgy, ahogyan az a fájlban megjelenik. Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.
- Ha fordított proxy vagy tűzfal található a felhasználói portál webkiszolgálója előtt és SSL-tehermentesítést végez, a felhasználói portál web.config fájljának szerkesztésével a következő kulcsot adhatja a(z) <appSettings> szakaszhoz, hogy a felhasználói portál http-t használhasson https helyett. <add key="SSL_REQUIRED" value="false"/>

#### A felhasználói portál telepítése

1. Nyissa meg a Windows Intézőt az Azure Multi-Factor Authentication-kiszolgálón, és keresse meg azt a mappát, amelybe az Azure Multi-Factor Authentication-kiszolgáló telepítve van (például C:\Program Files\Multi-Factor Authentication Server). Válassza a MultiFactorAuthenticationUserPortalSetup telepítőfájl 32 bites vagy 64 bites verzióját annak a kiszolgálónak megfelelően, amelyre a felhasználói portált telepíti. Másolja a telepítőfájlt az internetes kiszolgálóra.
2. Az internetes webkiszolgálón a telepítőfájlt rendszergazdai jogosultságokkal kell futtatnia. Ennek legegyszerűbb módja, ha rendszergazdaként megnyit egy parancssort és arra a helyre lép, ahová a telepítőfájlt másolta.
3. Futtassa a MultiFactorAuthenticationUserPortalSetup64 telepítőfájlt, és ha szeretné, módosítsa a helyet és a virtuális könyvtár nevét.
4. A felhasználói portál telepítésének befejezése után lépjen a C:\inetpub\wwwroot\MultiFactorAuth könyvtárra (vagy a virtuális könyvtár neve alapján a megfelelő könyvtárra), és szerkessze a web.config fájlt.
5. Keresse meg a USE_WEB_SERVICE_SDK kulcsot és módosítsa false értékűről true értékűre. Keresse meg a WEB_SERVICE_SDK_AUTHENTICATION_USERNAME és a WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD kulcsot, és állítsa az értékeket azon szolgáltatásfiók felhasználónevére és jelszavára, amely a PhoneFactor-adminisztrátorok biztonsági csoport tagja (lásd a fenti Követelmények szakaszt). Ügyeljen rá, hogy a felhasználónév és a jelszó a sor végén lévő idézőjelek között legyen megadva (value=””/>). Ajánlott teljes felhasználónevet használni (például tartomány\felhasználónév vagy gép\felhasználónév)
6. Keresse meg a pfup_pfwssdk_PfWsSdk beállítást, és módosítsa az értéket „http://localhost:4898/PfWsSdk.asmx” értékről azon Web Service SDK URL-címére, amely az Azure Multi-Factor Authentication-kiszolgálón fut (például https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Mivel ehhez a kapcsolathoz a rendszer SSL-t használ, a Web Service SDK-ra kiszolgálónév alapján kell hivatkozni és nem IP-cím alapján, mivel az SSL-tanúsítvány a kiszolgálónévhez lesz kiadva, és a használt URL-nek meg kell felelnie a tanúsítványon lévő névnek. Ha a kiszolgáló neve nem az internetes kiszolgálóról származó IP-címmé oldódik fel, adjon egy bejegyzést a kiszolgálón lévő gazdafájlhoz az Azure Multi-Factor Authentication-kiszolgáló nevének az IP-címére való leképezéséhez. A módosítások elvégzése után mentse a web.config fájlt.
7. Ha azon webhely, amelyen a felhasználói portál telepítve lett (például alapértelmezett webhely) még nincs nyilvánosan aláírt tanúsítvánnyal kötve, telepítse a tanúsítványt a kiszolgálóra, ha még nincs telepítve, nyissa meg az IIS-kezelőt, és kösse a tanúsítványt a webhelyhez.
8. Nyisson meg egy webböngészőt valamelyik számítógépről és lépjen arra az URL-címre, ahol a felhasználói portál telepítve van (például: https://www.publicwebsite.com/MultiFactorAuth). Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.



## A felhasználói portál beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón
Most, hogy telepítette a portált, konfigurálnia kell az Azure Multi-Factor Authentication-kiszolgálót, hogy működjön a portállal.

Az Azure Multi-Factor Authentication-kiszolgáló több lehetőséget nyújt a felhasználói portálhoz.  A következő táblázat tartalmazza ezen lehetőségek listáját és annak magyarázatát, hogy mire szolgálnak.

Felhasználói portál beállításai|Leírás|
:------------- | :------------- | 
Felhasználói portál URL-címe| Lehetővé teszi, hogy beírja a portál üzemeltetési helyének URL-címét.
Elsődleges hitelesítés| Lehetővé teszi, hogy megadja a portálra való bejelentkezéskor használandó hitelesítési típust.  Ez Windows-, Radius- vagy LDAP-hitelesítés lehet.
Felhasználók bejelentkezésének engedélyezése|Lehetővé teszi, hogy a felhasználók beírjanak egy felhasználónevet és jelszót a bejelentkezési oldalon a felhasználói portálhoz.  Ha ez nincs bejelölve, a mezők szürkén jelennek meg.
Felhasználó beléptetésének engedélyezése|Lehetővé teszi, hogy a felhasználók regisztráljanak a Multi-Factor Authenticationben egy olyan beállítási képernyő megjelenítésével, amely további információk, például a telefonszám megadását kéri.  A másodlagos telefonszámra való rákérdezéskor a felhasználók megadhatnak egy másodlagos telefonszámot.  A külső OATH tokenre való rákérdezéskor a felhasználók megadhatnak egy külső OATH tokent.
Egyszeri megkerülés kezdeményezésének engedélyezése a felhasználóknak| Ez lehetővé teszi, hogy a felhasználók egyszeri mellőzést kezdeményezzenek.  Ha a felhasználó ezt beállítja, az a következő bejelentkezésekor lép életbe.  A mellőzés másodperceire való rákérdezéskor a felhasználók egy mezőben módosíthatják az alapértelmezett 300 másodpercet.  Egyéb esetben az egyszeri mellőzés csak 300 másodpercig érvényes.
Módszer kiválasztásának engedélyezése a felhasználóknak| Lehetővé teszi, hogy a felhasználók megadják az elsődleges kapcsolatfelvételi módot.  Ez lehet telefonhívás, szöveges üzenet, mobilalkalmazás vagy OATH token.
Nyelv kiválasztásának engedélyezése a felhasználóknak|  Lehetővé teszi, hogy a felhasználó módosítsa a telefonhíváshoz, szöveges üzenethez, mobilalkalmazáshoz vagy OATH tokenhez használt nyelvet.
Mobilalkalmazás aktiválásának engedélyezése a felhasználóknak| Lehetővé teszi, hogy a felhasználók aktiválási kódot hozzanak létre a kiszolgálóval használt mobilalkalmazás-aktiválási folyamat elvégzéséhez.  Azon eszközök számát is megadhatja, amelyeken ezt aktiválhatják.  1 és 10 között.
Biztonsági kérdések használata tartalék megoldásként|Lehetővé teszi, hogy biztonsági kérdéseket használjanak, ha a Multi-Factor Authentication meghiúsul.  Megadhatja a sikeresen megválaszolandó biztonsági kérdések számát.
Harmadik féltől származó OATH token hozzárendelésének engedélyezése a felhasználóknak| Lehetővé teszi, hogy a felhasználók külső OATH tokent társíthassanak.
OATH token használata tartalékként|Lehetővé teszi OATH token használatát, ha a Multi-Factor Authentication meghiúsul.  Ezenkívül a munkamenet időtúllépését is megadhatja percben.
Naplózás engedélyezése|Lehetővé teszi a naplózást a felhasználói portálon.  A naplófájlok a következő helyen találhatók: C:\Program Files\Multi-Factor Authentication Server\Logs.

Ezen beállítások többsége láthatóvá a felhasználók számára, ha engedélyezettek és a felhasználók bejelentkeznek a felhasználói portálra.

![Felhasználói portál beállításai](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### A felhasználói portál beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón




1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a felhasználói portál ikonra. A Beállítások lapon írja be a felhasználói portál URL-címét a Felhasználói portál URL-címe szövegmezőbe. Ez az URL a felhasználóknak küldött e-mailekben szerepel a felhasználók az Azure Multi-Factor Authentication-kiszolgálóra való importálásakor, ha az e-mail funkció engedélyezve van.
2. Válassza ki a felhasználói portálon használni kívánt beállításokat. Ha például a felhasználók vezérelhetik a hitelesítési módszereket, győződjön meg arról, hogy a Módszer kiválasztásának engedélyezése a felhasználóknak jelölőnégyzet be van jelölve azokkal a módszerekkel együtt, amelyek közül választhatnak.
3. Kattintson a jobb felső sarkokban található Súgó ikonra a megjelenő beállításokkal kapcsolatos magyarázatokért.

<center>![Telepítés](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## Rendszergazdák lap
Ez a lap rendszergazdai jogosultságokkal rendelkező felhasználók hozzáadását teszi lehetővé.  Rendszergazdák hozzáadásakor finomhangolhatja, hogy azok milyen engedélyeket kapjanak.  Így biztos lehet abban, hogy a szükséges engedélyeket biztosítja a rendszergazdáknak.  Egyszerűen kattintson a Hozzáadás gombra, majd válassza ki a felhasználót és az engedélyeit, és kattintson a Hozzáadás lehetőségre.

![A felhasználói portál rendszergazdái](./media/multi-factor-authentication-get-started-portal/admin.png)


## Biztonsági kérdések
Ezen a lapon megadhatja azokat a biztonsági kérdéseket, amelyeket a felhasználóknak meg kell válaszolniuk, ha a Biztonsági kérdések használata tartalék megoldásként jelölőnégyzet be van jelölve.  Az Azure Multi-Factor Authenticaton-kiszolgálón elérhető alapértelmezett kérdéseket is használhatja.  Emellett a sorrendet is módosíthatja, és saját kérdéseket adhat hozzá.  Saját kérdések hozzáadásakor megadhatja a kérdések nyelvét is.

![A felhasználói portál biztonsági kérdései](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## Elvégzett munkamenetek

## SAML
Lehetővé teszi a felhasználói portál beállítását arra, hogy jogcímeket fogadjon el egy identitásszolgáltatótól SAML használatával.  Megadhatja az időtúllépési munkamenetet, az ellenőrző tanúsítványt és a kijelentkezési átirányítási URL-címet.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## Megbízható IP-címek
Ez a lap lehetővé teszi, hogy egyetlen IP-címet vagy egy IP-címtartományt adjon meg, amelyek hozzáadhatók annak érdekében, hogy mellőzve legyen a Multi-Factor Authentication, ha egy felhasználó ezen IP-címek valamelyikéről jelentkezik be. 

![Felhasználó portál megbízható IP-címei](./media/multi-factor-authentication-get-started-portal/trusted.png)

## Önkiszolgáló felhasználói regisztráció
Ha azt szeretné, hogy a felhasználók bejelentkezzenek és regisztráljanak, be kell jelölnie a Felhasználók bejelentkezésének engedélyezése és a Felhasználó beléptetésének engedélyezése jelölőnégyzetet. Ne feledje, hogy a kiválasztott beállítások hatással vannak a felhasználó bejelentkezési élményére.

Amikor például egy felhasználó bejelentkezik a felhasználói portálra és a Bejelentkezés gombra kattint, az Azure Multi-Factor Authentication felhasználói beállítási lapjára kerül.  Az Azure Multi-Factor Authentication konfigurációjától függően lehet, hogy a felhasználó kiválaszthatja a hitelesítési módszert.  

Ha a felhasználók a Hanghívás hitelesítési módszert választják vagy ha ez a módszer van előre konfigurálva, a felhasználóknak a lapon meg kell adniuk az elsődleges telefonszámukat és melléküket, ha van ilyen.  Lehet, hogy másodlagos telefonszámot is megadhatnak.  

![Felhasználó portál megbízható IP-címei](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Ha a felhasználóknak PIN-kódot kell használniuk a hitelesítéskor, a lap PIN-kód megadását is kéri.  A telefonszám(ok) és a PIN-kód (ha van) megadása után a felhasználó a Hívjon fel a hitelesítéshez gombra kattint.  Az Azure Multi-Factor Authentication telefonhíváson alapuló hitelesítést kezdeményez a felhasználó elsődleges telefonszámának használatával.  A felhasználónak fogadnia kell a hívást, és meg kell adnia a PIN-kódját (ha van), majd meg kell nyomnia a # gombot az önregisztrációs folyamat következő lépésére való továbblépéshez.   

Ha a felhasználók az SMS szöveges hitelesítési módszert választják, vagy ha ez a módszer van előre konfigurálva, a felhasználóknak a lapon meg kell adniuk a mobiltelefonszámukat.  Ha a felhasználóknak PIN-kódot kell használniuk a hitelesítéskor, a lap PIN-kód megadását is kéri.  A telefonszám és a PIN-kód (ha van) beírása után a felhasználó a Küldjön SMS-t a hitelesítéshez gombra kattint.  Az Azure Multi-Factor Authentication SMS-hitelesítést kezdeményez a felhasználó mobiltelefonszáma használatával.  A felhasználónak egyszeri jelszót (OTP-t) tartalmazó SMS-t kell kapnia és válaszolnia kell az üzenetre az OTP-vel és a PIN-kódjával (ha van), hogy az önregisztrációs folyamat következő lépésére ugorjon. 

![Felhasználói portál SMS](./media/multi-factor-authentication-get-started-portal/text.png)   

Ha a felhasználó a mobilalkalmazásos hitelesítési módszert választja, vagy ha ez a módszer van előre konfigurálva, az oldal felkéri a felhasználót, hogy telepítse az Azure Multi-Factor Authentication alkalmazást az eszközére, és hozzon létre egy aktiválási kódot.  Az Azure Multi-Factor Authentication alkalmazás telepítése után a felhasználónak az Aktiváló kód előállítása gombra kell kattintania.    

>[AZURE.NOTE]Az Azure Multi-Factor Authentication alkalmazás használatához a felhasználónak engedélyeznie kell a leküldéses értesítéseket az eszközén. 

A lap ezután megjelenít egy aktiválási kódot és egy URL-címet egy vonalkód képével együtt.  Ha a felhasználóknak PIN-kódot kell használniuk a hitelesítéskor, a lap PIN-kód megadását is kéri.  A felhasználó beírja az aktiválási kódot és az URL-címet az Azure Multi-Factor Authentication alkalmazásba, vagy a vonalkódolvasóval beolvassa a vonalkód képét, majd az Aktiválás gombra kattint.    

Az aktiválás elvégzése után a felhasználó a Hitelesítsen most gombra kattint.  Az Azure Multi-Factor Authentication hitelesítést végez a felhasználó mobilalkalmazásában.  A felhasználónak meg kell adnia a PIN-kódját (ha van) és a mobilalkalmazásban a Hitelesítés gombra kell kattintania, hogy az önregisztrációs folyamat következő lépésére ugorjon.  


Ha a rendszergazdák úgy konfigurálták az Azure Multi-Factor Authentication-kiszolgálót, hogy biztonsági kérdéseket és válaszokat gyűjtsön, a felhasználó a Biztonsági kérdések lapra kerül.  A felhasználónak ki kell választania négy biztonsági kérdést, és meg kell válaszolnia azokat.    

![A felhasználói portál biztonsági kérdései](./media/multi-factor-authentication-get-started-portal/secq.png)  

A felhasználó önregisztrációja ezzel befejeződött, és a felhasználó be van jelentkezve a felhasználói portálra.  A felhasználók ezután bármikor újra bejelentkezhetnek a felhasználói portálra a telefonszámuk, PIN-kódjuk, hitelesítési módszerük és biztonsági kérdéseik módosításához, ha ezt a rendszergazdáik engedélyezték.

 



<!--HONumber=sep16_HO1-->


