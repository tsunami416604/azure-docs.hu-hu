---
title: "Felhasználói portál az Azure MFA-kiszolgálóhoz | Microsoft Docs"
description: "Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és a felhasználói portál használatát."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1222223f8c45249402bfdd04c8754074f877e132
ms.openlocfilehash: 1236489212b2a9c421972599a12511d5bc42efdf


---
# <a name="deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>A felhasználói portál üzembe helyezése az Azure Multi-Factor Authentication-kiszolgálóhoz
A felhasználói portál segítségével a rendszergazda telepítheti és konfigurálhatja az Azure Multi-Factor Authentication felhasználói portálját. A felhasználói portál egy IIS-webhely, ahol a felhasználók regisztrálhatnak az Azure Multi-Factor Authenticationre, és kezelhetik a fiókjaikat. A felhasználók megváltoztathatják a telefonszámukat és a PIN-kódjukat, vagy választhatják a kétlépéses ellenőrzés mellőzését a következő bejelentkezéskor.

A felhasználók a normál felhasználónevükkel és jelszavukkal jelentkeznek be a felhasználói portálra, majd egy kétlépéses ellenőrző hívással vagy biztonsági kérdésekre adott válaszokkal végzik el a hitelestést. Ha a felhasználók regisztrációja engedélyezett, a felhasználók a felhasználói portálra való első bejelentkezéskor konfigurálják a telefonszámukat és PIN-kódjukat.

A felhasználói portálhoz rendszergazdák állíthatók be, akik az új felhasználók hozzáadására és a meglévő felhasználók frissítésére vonatkozó engedélyt kaphatnak.

<center>![Telepítés](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>A felhasználói portál üzembe helyezése az Azure Multi-Factor Authentication-kiszolgálóval megegyező kiszolgálón
A következő előfeltételek teljesülésére van szükség, ha a felhasználói portált az Azure Multi-Factor Authentication-kiszolgálóval megegyező kiszolgálóra szeretné telepíteni:

* IIS, beleértve az asp.net-et és az IIS 6 metabázisával való kompatibilitási funkciót (IIS 7 vagy újabb verzió esetén).
* A bejelentkezett felhasználóknak rendszergazdai jogosultságokkal kell rendelkezniük a számítógéphez és a tartományhoz, ha szükséges.  Ez azért van, mert a fióknak engedélyekre van szüksége Active Directory biztonsági csoportok létrehozásához.

### <a name="to-deploy-the-user-portal"></a>A felhasználói portál üzembe helyezése
1. Az Azure Multi-Factor Authentication-kiszolgálón: kattintson a bal oldali menüben lévő **Felhasználói portál** ikonra, majd kattintson a **Felhasználói portál telepítése** gombra.
2. Kattintson a **Tovább** gombra.
3. Kattintson a **Tovább** gombra.
4. Ha a számítógép tartományhoz csatlakozik, és az Active Directory nincs konfigurálva a felhasználói portál és az Azure Multi-Factor Authentication szolgáltatás közötti kommunikáció védelmére, az Active Directory lépés jelenik meg. Kattintson a **Tovább** gombra a konfiguráció automatikus elvégzéséhez.
5. Kattintson a **Tovább** gombra.
6. Kattintson a **Tovább** gombra.
7. Kattintson a **Bezárás** gombra.
8. Nyisson meg egy webböngészőt valamelyik számítógépről, és lépjen arra az URL-címre, ahol a felhasználói portál telepítve van (például: https://www.publicwebsite.com/MultiFactorAuth). Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.

<center>![Telepítés](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploy-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Az Azure Multi-Factor Authentication-kiszolgáló felhasználói portáljának telepítése különálló kiszolgálón
A Microsoft Authenticator alkalmazás és a felhasználói portál kommunikációjának engedélyezéséhez a következő követelményeknek kell teljesülnie: 

* Az Azure Multi-Factor Authentication-kiszolgáló 6.0-s vagy újabb verzióját kell használnia.
* A felhasználói portált a Microsoft® Internet Information Services (IIS) 6.x, IIS 7.x vagy újabb verziót futtató internetes webkiszolgálóra kell telepíteni.
* Az IIS 6.x használatakor győződjön meg arról, hogy az ASP.NET 2.0.50727-es verziója telepítve van, regisztrálva van, és a beállítása **Engedélyezve**.
* Az IIS 7.x vagy újabb használatakor a szükséges szerepkör-szolgáltatásokba tartozik az ASP.NET és az IIS 6 metabázisával való kompatibilitás.
* A felhasználói portál védelméhez SSL-tanúsítványt kell használni.
* Az Azure Multi-Factor Authentication Web Service SDK-t telepíteni kell az IIS 6.x, IIS 7.x vagy újabb verziójában azon a kiszolgálón, ahol az Azure Multi-Factor Authentication-kiszolgáló telepítve van.
* Az Azure Multi-Factor Authentication Web Service SDK védelmét SSL-tanúsítvánnyal kell biztosítani.
* A felhasználói portálnak képesnek kell lennie arra, hogy SSL-en keresztül csatlakozzon az Azure Multi-Factor Authentication Web Service SDK-hoz.
* A felhasználói portálnak képesnek kell lennie arra, hogy a „PhoneFactor-adminisztrátorok” nevű biztonsági csoportban található szolgáltatásfiók hitelesítő adataival végezzen hitelesítést az Azure Multi-Factor Authentication Web Service SDK-ban. Ez a szolgáltatásfiók és csoport az Active Directoryban található meg, ha az Azure Multi-Factor Authentication-kiszolgáló tartományhoz csatlakoztatott kiszolgálón fut. Ez a szolgáltatásfiók és csoport helyben található meg az Azure Multi-Factor Authentication-kiszolgálón, ha az nincs tartományhoz csatlakoztatva.

A felhasználói portál az Azure Multi-Factor Authentication-kiszolgálótól eltérő kiszolgálón való telepítésének három lépése a következő:

1. A Web Service SDK telepítése
2. A felhasználói portál telepítése
3. A felhasználói portál beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón

### <a name="step-1-install-the-web-service-sdk"></a>1. lépés: A Web Service SDK telepítése
Ha az Azure Multi-Factor Authentication Web Service SDK még nincs telepítve az Azure Multi-Factor Authentication-kiszolgálón, lépjen a kiszolgálóra és nyissa meg az Azure Multi-Factor Authentication-kiszolgálót. Kattintson a **Web Service SDK** ikonra, majd a **Web Service SDK telepítése** gombra. Kövesse a megjelenő utasításokat. 

A Web Service SDK védelméhez SSL-tanúsítvány használata szükséges. Erre a célra megfelel egy önaláírt tanúsítvány, amelyet a kiszolgáló Helyi számítógép fiókjának „Megbízható legfelső szintű hitelesítésszolgáltatók” tárolójába kell importálni. Így a Web Service SDK megbízhat a tanúsítványban az SSL-kapcsolat kezdeményezésekor.

<center>![Telepítés](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="step-2-install-the-user-portal"></a>2. lépés: A felhasználói portál telepítése
Mielőtt a felhasználói portált különálló kiszolgálóra telepítené, vegye figyelembe a következő ajánlott eljárásokat:

* Hasznos lehet, ha megnyit egy webböngészőt az internetes webkiszolgálón és megkeresi a Web Service SDK a web.config fájlban megadott URL-címét. Ha a böngésző sikeresen eléri a webszolgáltatást, kérnie kell a hitelesítő adatok megadását. Írja be a web.config fájlban megadott felhasználónevet és jelszót pontosan úgy, ahogyan az a fájlban megjelenik. Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.
* Ha fordított proxy vagy tűzfal található a felhasználói portál webkiszolgálója előtt, és SSL-tehermentesítést végez, a felhasználói portál web.config fájljának szerkesztésével a következő kulcsot adhatja az `<appSettings>` szakaszhoz, hogy a felhasználói portál http-t használhasson https helyett:

    `<add key="SSL_REQUIRED" value="false"/>`

#### <a name="to-install-the-user-portal"></a>A felhasználói portál telepítése
1. Nyissa meg a Windows Intézőt az Azure Multi-Factor Authentication-kiszolgálón, és keresse meg azt a mappát, amelybe az Azure Multi-Factor Authentication-kiszolgáló telepítve van (például C:\Program Files\Multi-Factor Authentication Server). Válassza a MultiFactorAuthenticationUserPortalSetup telepítőfájl 32 bites vagy 64 bites verzióját annak a kiszolgálónak megfelelően, amelyre a felhasználói portált telepíti. Másolja a telepítőfájlt az internetes kiszolgálóra.
2. Az internetes webkiszolgálón a telepítőfájlt rendszergazdai jogosultságokkal kell futtatnia. Ennek legegyszerűbb módja, ha rendszergazdaként megnyit egy parancssort és arra a helyre lép, ahová a telepítőfájlt másolta.
3. Futtassa a MultiFactorAuthenticationUserPortalSetup64 telepítőfájlt, és ha szeretné, módosítsa a helyet és a virtuális könyvtár nevét.
4. A felhasználói portál telepítésének befejezése után lépjen a C:\inetpub\wwwroot\MultiFactorAuth könyvtárra (vagy a virtuális könyvtár neve alapján a megfelelő könyvtárra), és szerkessze a web.config fájlt.
5. Keresse meg a USE_WEB_SERVICE_SDK kulcsot és módosítsa false értékűről true értékűre. Keresse meg a WEB_SERVICE_SDK_AUTHENTICATION_USERNAME és a WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD kulcsot, és állítsa az értékeket a PhoneFactor-adminisztrátorok biztonsági csoportban található szolgáltatásfiók felhasználónevére és jelszavára (lásd a Követelmények szakaszt). Ügyeljen rá, hogy a felhasználónév és a jelszó a sor végén lévő idézőjelek között legyen megadva (value=””/>). Használjon teljes felhasználónevet (például tartomány\felhasználónév vagy gép\felhasználónév).
6. Keresse meg a pfup_pfwssdk_PfWsSdk beállítást, és módosítsa az értéket „http://localhost:4898/PfWsSdk.asmx” értékről azon Web Service SDK URL-címére, amely az Azure Multi-Factor Authentication-kiszolgálón fut (például https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Mivel ehhez a kapcsolathoz a rendszer SSL-t használ, a Web Service SDK-ra kiszolgálónév alapján hivatkozzon és ne IP-cím alapján, mivel az SSL-tanúsítvány a kiszolgálónévhez van kiadva. Ha a kiszolgáló neve nem az internetes kiszolgálóról származó IP-címmé oldódik fel, adjon egy bejegyzést a kiszolgálón lévő gazdafájlhoz az Azure Multi-Factor Authentication-kiszolgáló nevének az IP-címére való leképezéséhez. A módosítások elvégzése után mentse a web.config fájlt.

    A konfigurációs fájl szerkesztésével kapcsolatos további részletekért menjen [Az erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS használatával](multi-factor-authentication-get-started-adfs-w2k12.md#edit-the-multifactorauthenticationadfsadapterconfig-file) című részhez.

7. Ha az a webhely, amelyen a felhasználói portál telepítve lett (például az alapértelmezett webhely) még nincs nyilvánosan aláírt tanúsítvánnyal kötve, telepítse a tanúsítványt a kiszolgálóra, nyissa meg az IIS-kezelőt, és kösse a tanúsítványt a webhelyhez.
8. Nyisson meg egy webböngészőt valamelyik számítógépről, és lépjen arra az URL-címre, ahol a felhasználói portál telepítve van (például: https://www.publicwebsite.com/MultiFactorAuth). Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.

### <a name="step-3-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>3. lépés: A felhasználói portál beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón
Most, hogy telepítette a felhasználói portált, konfigurálnia kell az Azure Multi-Factor Authentication-kiszolgálót, hogy működjön a portállal.

Az Azure Multi-Factor Authentication-kiszolgáló több lehetőséget nyújt a felhasználói portálhoz.  A következő táblázat tartalmazza ezen lehetőségek listáját és annak magyarázatát, hogy mire szolgálnak.

| Felhasználói portál beállításai | Leírás |
|:--- |:--- |
| Felhasználói portál URL-címe | Adja meg a portál üzemeltetési helyének URL-címét. |
| Elsődleges hitelesítés | Adja meg a portálra való bejelentkezéskor használandó hitelesítési típust.  Ez Windows-, Radius- vagy LDAP-hitelesítés lehet. |
| Felhasználók bejelentkezésének engedélyezése | Lehetővé teszi, hogy a felhasználók beírjanak egy felhasználónevet és jelszót a bejelentkezési oldalon a felhasználói portálhoz.  Ha ez nincs bejelölve, a mezők szürkén jelennek meg. |
| Felhasználó beléptetésének engedélyezése | Lehetővé teszi, hogy a felhasználók regisztráljanak a Multi-Factor Authenticationben egy olyan beállítási képernyő megjelenítésével, amely további információk, például a telefonszám megadását kéri. A másodlagos telefonszámra való rákérdezéskor a felhasználók megadhatnak egy másodlagos telefonszámot. A külső OATH tokenre való rákérdezéskor a felhasználók megadhatnak egy külső OATH tokent. |
| Egyszeri megkerülés kezdeményezésének engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználók egyszeri mellőzést kezdeményezzenek.  Ha a felhasználó megadja ezt a beállítást, a következő bejelentkezésekor lép életbe. A mellőzés másodperceire való rákérdezéskor a felhasználók egy mezőben módosíthatják az alapértelmezett 300 másodpercet. Egyéb esetben az egyszeri mellőzés csak 300 másodpercig érvényes. |
| Módszer kiválasztásának engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználók megadják az elsődleges kapcsolatfelvételi módot.  Ez lehet telefonhívás, szöveges üzenet, mobilalkalmazás vagy OATH token. |
| Nyelv kiválasztásának engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználó módosítsa a telefonhíváshoz, szöveges üzenethez, mobilalkalmazáshoz vagy OATH tokenhez használt nyelvet. |
| Mobilalkalmazás aktiválásának engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználók aktiválási kódot hozzanak létre a kiszolgálóval használt mobilalkalmazás-aktiválási folyamat elvégzéséhez.  Megadhatja azon eszközök 1 és 10 közötti számát is, amelyeken ezt az alkalmazást aktiválhatják. |
| Biztonsági kérdések használata tartalék megoldásként | Engedélyezi a biztonsági kérdések használatát, ha a kétlépéses ellenőrzés sikertelennek bizonyul.  Megadhatja a sikeresen megválaszolandó biztonsági kérdések számát. |
| Harmadik féltől származó OATH token hozzárendelésének engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználók megadhassanak egy külső OATH tokent. |
| OATH token használata tartalékként | Lehetővé teszi egy OATH token használatát, ha a kétlépéses ellenőrzés sikertelennek bizonyul. Ezenkívül a munkamenet időtúllépését is megadhatja percben. |
| Naplózás engedélyezése | Lehetővé teszi a naplózást a felhasználói portálon. A naplófájlok a következő helyen találhatók: C:\Program Files\Multi-Factor Authentication Server\Logs. |

Ezen beállítások többsége látható a felhasználók számára, ha engedélyezettek és be vannak jelentkezve a felhasználói portálra.

![Felhasználói portál beállításai](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>A felhasználói portál beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón
1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a **Felhasználói portál** ikonra. A Beállítások lapon írja be a felhasználói portál URL-címét a **Felhasználói portál URL-címe** szövegmezőbe. Ez az URL-cím a felhasználóknak küldött e-mailekben szerepel a felhasználók Azure Multi-Factor Authentication-kiszolgálóra való importálásakor, ha az e-mail funkció engedélyezve van.
2. Válassza ki a felhasználói portálon használni kívánt beállításokat. Ha például a felhasználók vezérelhetik a hitelesítési módszereket, győződjön meg arról, hogy a **Módszer kiválasztásának engedélyezése a felhasználóknak** jelölőnégyzet be van jelölve azokkal a módszerekkel együtt, amelyek közül választhatnak.
3. Kattintson a jobb felső sarkokban található **Súgó** ikonra a megjelenő beállításokkal kapcsolatos magyarázatokért.

<center>![Telepítés](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Rendszergazdák lap
Ez a lap rendszergazdai jogosultságokkal rendelkező felhasználók hozzáadását teszi lehetővé.  Rendszergazdák hozzáadásakor finomhangolhatja, hogy milyen engedélyeket kapjanak. Kattintson a **Hozzáadás** gombra, majd válassza ki a felhasználót és az engedélyeit, és kattintson a **Hozzáadás** elemre.

![A felhasználói portál rendszergazdái](./media/multi-factor-authentication-get-started-portal/admin.png)

## <a name="security-questions"></a>Biztonsági kérdések
Ezen a lapon megadhatja azokat a biztonsági kérdéseket, amelyeket a felhasználóknak meg kell válaszolniuk, ha a **Biztonsági kérdések használata tartalék megoldásként** jelölőnégyzet be van jelölve.  Az Azure Multi-Factor Authenticaton-kiszolgálón elérhető alapértelmezett kérdéseket is használhatja. A sorrendet is módosíthatja, és saját kérdéseket is megadhat.  Saját kérdések hozzáadásakor megadhatja a kérdések nyelvét is.

![A felhasználói portál biztonsági kérdései](./media/multi-factor-authentication-get-started-portal/secquestion.png)

## <a name="saml"></a>SAML
Ezen a lapon beállíthatja a felhasználói portált arra, hogy jogcímeket fogadjon el egy identitásszolgáltatótól az SAML használatával.  Megadhatja az időtúllépési munkamenetet, az ellenőrző tanúsítványt és a kijelentkezési átirányítási URL-címet.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>Megbízható IP-címek
Ez a lap lehetővé teszi, hogy egyetlen IP-címet vagy egy IP-címtartományt adjon meg, amely hozzáadható annak érdekében, hogy az ezen címek valamelyikéről bejelentkező felhasználóknak ne kelljen elvégezniük a kétlépéses ellenőrzést.

![Felhasználó portál megbízható IP-címei](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Önkiszolgáló felhasználói regisztráció
Ha azt szeretné, hogy a felhasználók bejelentkezzenek és regisztráljanak, a Beállítások lapon be kell jelölnie a **Felhasználók bejelentkezésének engedélyezése** és a **Felhasználó beléptetésének engedélyezése** jelölőnégyzetet. Ne feledje, hogy a kiválasztott beállítások hatással vannak a felhasználó bejelentkezési élményére.

Amikor például egy felhasználó először jelentkezik be a felhasználói portálra, az Azure Multi-Factor Authentication felhasználói beállítási lapjára kerül.  Az Azure Multi-Factor Authentication konfigurációjától függően lehet, hogy a felhasználó kiválaszthatja a hitelesítési módszert.  

Ha a felhasználók a Hanghívás ellenőrzési módszert választják vagy ha ez a módszer van előre konfigurálva, a felhasználóknak a lapon meg kell adniuk az elsődleges telefonszámukat és melléküket, ha van ilyen.  Lehet, hogy másodlagos telefonszámot is megadhatnak.  

![Felhasználó portál megbízható IP-címei](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Ha a felhasználóknak PIN-kódot kell használniuk a hitelesítéskor, a lap PIN-kód létrehozását is kéri.  A telefonszám(ok) és a PIN-kód (ha van) megadása után a felhasználó a **Hívjon fel a hitelesítéshez** gombra kattint.  Az Azure Multi-Factor Authentication telefonhíváson alapuló ellenőrzést kezdeményez a felhasználó elsődleges telefonszámának használatával.  A felhasználónak fogadnia kell a hívást, és meg kell adnia a PIN-kódját (ha van), majd meg kell nyomnia a # gombot az önregisztrációs folyamat következő lépésére való továbblépéshez.   

Ha a felhasználók a szöveges üzenetes ellenőrzési módszert választják, vagy ha ez a módszer van előre konfigurálva, a lapon meg kell adniuk a mobiltelefonszámukat.  Ha a felhasználóknak PIN-kódot kell használniuk a hitelesítéskor, a lap PIN-kód megadását is kéri.  A telefonszám és a PIN-kód (ha van) beírása után a felhasználó a **Küldjön SMS-t a hitelesítéshez** gombra kattint.  Az Azure Multi-Factor Authentication SMS-ellenőrzést kezdeményez a felhasználó mobiltelefonszáma használatával.  A felhasználó egyszeri jelszót (one-time-passcode, OTP) tartalmazó SMS-t kap, és válaszol az üzenetre az OTP-vel és a PIN-kódjával (ha van).

![Felhasználói portál SMS](./media/multi-factor-authentication-get-started-portal/text.png)   

Ha a felhasználó a mobilalkalmazásos ellenőrzési módszert választja, az oldal felkéri, hogy telepítse a Microsoft Authenticator alkalmazást az eszközére, és hozzon létre egy aktiválási kódot.  Az alkalmazás telepítése után a felhasználónak az Aktiváló kód előállítása gombra kell kattintania.    

> [!NOTE]
> A Microsoft Authenticator alkalmazás használatához a felhasználónak engedélyeznie kell a leküldéses értesítéseket az eszközén.

A lap ezután megjelenít egy aktiválási kódot és egy URL-címet egy vonalkód képével együtt.  Ha a felhasználóknak PIN-kódot kell használniuk a hitelesítéskor, a lap PIN-kód megadását is kéri.  A felhasználó beírja az aktiválási kódot és az URL-címet a Microsoft Authenticator alkalmazásba, vagy a vonalkódolvasóval beolvassa a vonalkódot, majd az Aktiválás gombra kattint.    

Az aktiválás elvégzése után a felhasználó a **Hitelesítsen most** gombra kattint.  Az Azure Multi-Factor Authentication ellenőrzést végez a felhasználó mobilalkalmazásában.  A felhasználónak meg kell adnia a PIN-kódját (ha van) és a mobilalkalmazásban a Hitelesítés gombra kell kattintania, hogy az önregisztrációs folyamat következő lépésére ugorjon.  

Ha a rendszergazdák úgy konfigurálták az Azure Multi-Factor Authentication-kiszolgálót, hogy biztonsági kérdéseket és válaszokat gyűjtsön, a felhasználó a Biztonsági kérdések lapra kerül.  A felhasználónak ki kell választania négy biztonsági kérdést, és meg kell válaszolnia azokat.    

![A felhasználói portál biztonsági kérdései](./media/multi-factor-authentication-get-started-portal/secq.png)  

A felhasználó önregisztrációja ezzel befejeződött, és a felhasználó be van jelentkezve a felhasználói portálra.  Ezután bármikor újra bejelentkezhet a felhasználói portálra a telefonszáma, PIN-kódja, hitelesítési módszere és biztonsági kérdései módosításához, ha ezt a rendszergazdáik engedélyezték.




<!--HONumber=Feb17_HO3-->


