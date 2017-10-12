---
title: "Felhasználói portál az Azure MFA-kiszolgálóhoz | Microsoft Docs"
description: "Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és a felhasználói portál használatát."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: alexwe
ms.custom: it-pro
ms.openlocfilehash: a4eb403d3d21b7dbe63c2645b488a7bddb6d39fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="user-portal-for-the-azure-multi-factor-authentication-server"></a>Felhasználói portál az Azure Multi-Factor Authentication-kiszolgálóhoz

A felhasználói portál egy IIS-webhely, ahol a felhasználók regisztrálhatnak az Azure Multi-Factor Authenticationre (MFA), és kezelhetik a fiókjaikat. A felhasználók megváltoztathatják a telefonszámukat és a PIN-kódjukat, vagy választhatják a kétlépéses ellenőrzés mellőzését a következő bejelentkezéskor.

A felhasználók a szokványos felhasználónevükkel és jelszavukkal jelentkeznek be a felhasználói portálra, majd egy kétlépéses ellenőrző hívással vagy biztonsági kérdésekre adott válaszokkal végzik el a hitelesítést. Ha a felhasználók regisztrációja engedélyezett, a felhasználók a felhasználói portálra való első bejelentkezéskor konfigurálják a telefonszámukat és PIN-kódjukat.

A felhasználói portálhoz rendszergazdák állíthatók be, akik az új felhasználók hozzáadására és a meglévő felhasználók frissítésére vonatkozó engedélyt kaphatnak.

A felhasználói portált a környezettől függően érdemes lehet az Azure Multi-Factor Authentication-kiszolgálóval megegyező kiszolgálóra, illetve egy másik, internetkapcsolattal rendelkező kiszolgálóra telepíteni.

![MFA felhasználói portál](./media/multi-factor-authentication-get-started-portal/portal.png)

> [!NOTE]
> A felhasználói portál csak a Multi-Factor Authentication-kiszolgálóval érhető el. Ha a Multi-Factor Authenticationt a felhőben használja, hívja fel a felhasználói figyelmét a [fiók kétlépéses ellenőrzéshez történő beállítására](./end-user/multi-factor-authentication-end-user-first-time.md) vagy a [beállítások kétlépéses ellenőrzésnél történő kezelésére](./end-user/multi-factor-authentication-end-user-manage-settings.md).

## <a name="install-the-web-service-sdk"></a>A Web Service SDK telepítése

Mindkét forgatókönyvre érvényes, hogy ha az Azure Multi-Factor Authentication Web Service SDK még **nincs** telepítve az Azure Multi-Factor Authentication- (MFA-) kiszolgálón, akkor végre kell hajtania a következő lépéseket.

1. Nyissa meg a Multi-Factor Authentication-kiszolgáló konzolját.
2. Lépjen a **Web Service SDK** részre, majd válassza a **Web Service SDK telepítése** lehetőséget.
3. Végezze el a telepítést az alapértelmezett értékekkel, hacsak nem kell valamilyen okból módosítania őket.
4. Kössön egy SSL-tanúsítványt a helyhez az IIS-ben.

Ha kérdései vannak az SSL-tanúsítvány IIS-kiszolgálón történő konfigurálásáról, olvassa el [Az SSL beállítása az IIS-en](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) cikket.

A Web Service SDK védelméhez SSL-tanúsítvány használata szükséges. Erre a célra megfelel egy önaláírt tanúsítvány. Importálja a tanúsítványt a felhasználói portál webkiszolgálójának helyi számítógépfiókja „Megbízható legfelső szintű hitelesítésszolgáltatók” tárolójába, hogy az megbízhatónak tekintse ezt a tanúsítványt az SSL-kapcsolat elindításakor.

![MFA-kiszolgáló konfigurálása – Web Service SDK](./media/multi-factor-authentication-get-started-portal/sdk.png)

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>A felhasználói portál üzembe helyezése az Azure Multi-Factor Authentication-kiszolgálóval megegyező kiszolgálón

A következő előfeltételeknek kell teljesülniük, ha a felhasználói portált az Azure Multi-Factor Authentication-kiszolgálóval **megegyező kiszolgálóra** szeretné telepíteni:

* IIS, beleértve az ASP.NET-et és az IIS 6 metabázisával való kompatibilitási funkciót (IIS 7 vagy újabb verzió esetén)
* Egy fiók rendszergazdai jogosultságokkal a számítógéphez és a tartományhoz, ha szükséges. A fióknak engedéllyel kell rendelkeznie Active Directory-biztonságicsoportok létrehozásához.
* A felhasználói portál védelméhez SSL-tanúsítványt kell használni.
* Az Azure Multi-Factor Authentication Web Service SDK védelmét egy SSL-tanúsítvánnyal kell biztosítani.

A felhasználói portál üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Azure Multi-Factor Authentication-kiszolgáló konzolját, kattintson a bal oldali menüben lévő **Felhasználói portál** ikonra, majd kattintson a **Felhasználói portál telepítése** parancsra.
2. Végezze el a telepítést az alapértelmezett értékekkel, hacsak nem kell valamilyen okból módosítania őket.
3. Kössön egy SSL-tanúsítványt a webhelyhez az IIS-ben

   > [!NOTE]
   > Ez az SSL-tanúsítvány általában egy nyilvánosan aláírt SSL-tanúsítvány.

4. Nyisson meg egy webböngészőt valamelyik számítógépről, és lépjen arra az URL-címre, ahol a felhasználói portál telepítve van (például: https://mfa.contoso.com/MultiFactorAuth). Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.

![MFA-kiszolgáló felhasználói portáljának telepítése](./media/multi-factor-authentication-get-started-portal/install.png)

Ha kérdései vannak az SSL-tanúsítvány IIS-kiszolgálón történő konfigurálásáról, olvassa el [Az SSL beállítása az IIS-en](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) cikket.

## <a name="deploy-the-user-portal-on-a-separate-server"></a>A felhasználói portál telepítése különálló kiszolgálón

Ha az Azure Multi-Factor Authentication-kiszolgálót futtató kiszolgáló nem internetes elérésű, egy **külön, internetes elérésű kiszolgálón** kell telepítenie a felhasználói portált.

Ha a szervezete egyik ellenőrzési módszerként a Microsoft Authenticator appot használja, és telepíteni szeretné a felhasználói portált a saját kiszolgálóján, az alábbi feltételeknek kell teljesülniük:

* Az Azure Multi-Factor Authentication-kiszolgáló 6.0-s vagy újabb verzióját kell használnia.
* A felhasználói portált a Microsoft Internet Information Services (IIS) 6.x-es vagy újabb verzióját futtató internetes webkiszolgálóra kell telepítenie.
* Az IIS 6.x használatakor győződjön meg arról, hogy az ASP.NET 2.0.50727-es verziója telepítve van, regisztrálva van, és a beállítása **Engedélyezve**.
* Az IIS 7.x vagy újabb verzió használatakor IIS, beleértve az alapszintű hitelesítést, az ASP.NET-et és az IIS 6 metabázisával való kompatibilitási funkciót.
* A felhasználói portál védelméhez SSL-tanúsítványt kell használni.
* Az Azure Multi-Factor Authentication Web Service SDK védelmét egy SSL-tanúsítvánnyal kell biztosítani.
* A felhasználói portálnak képesnek kell lennie arra, hogy SSL-en keresztül csatlakozzon az Azure Multi-Factor Authentication Web Service SDK-hoz.
* A felhasználói portálnak képesnek kell lennie arra, hogy a „PhoneFactor-adminisztrátorok” nevű biztonsági csoportban található szolgáltatásfiók hitelesítő adataival végezzen hitelesítést az Azure Multi-Factor Authentication Web Service SDK-ban. Ez a szolgáltatásfiók és csoport az Active Directoryban található meg, ha az Azure Multi-Factor Authentication-kiszolgáló tartományhoz csatlakoztatott kiszolgálón fut. Ez a szolgáltatásfiók és csoport helyben található meg az Azure Multi-Factor Authentication-kiszolgálón, ha az nincs tartományhoz csatlakoztatva.

A felhasználói portál az Azure Multi-Factor Authentication-kiszolgálótól eltérő kiszolgálón való telepítésének lépései a következők:

1. **Az MFA-kiszolgálón** lépjen a telepítés helyére (például C:\Program Files\Multi-Factor Authentication Server), majd másolja a **MultiFactorAuthenticationUserPortalSetup64** fájlt egy olyan helyre, ahol hozzáférhet az interneteléréssel rendelkező kiszolgáló, amelyen telepíteni szeretné.
2. **Az interneteléréssel rendelkező webkiszolgálón** futtassa a Multi-Factor AuthenticationMobileAppWebServiceSetup telepítőfájlt rendszergazdaként, szükség esetén módosítsa a Hely beállítást, és tetszés szerint adjon meg egy rövid nevet a virtuális címtár számára.
3. Kössön egy SSL-tanúsítványt a helyhez az IIS-ben.

   > [!NOTE]
   > Ez az SSL-tanúsítvány általában egy nyilvánosan aláírt SSL-tanúsítvány.

4. Lépjen a **C:\inetpub\wwwroot\MultiFactorAuth** helyre.
5. Szerkessze a Web.config fájlt a Jegyzettömbben.

    * Keresse meg a **"USE_WEB_SERVICE_SDK"** kulcsot, és módosítsa **value="false"** értékről **value="true"** értékre.
    * Keresse meg a **„WEB_SERVICE_SDK_AUTHENTICATION_USERNAME”** kulcsot, és módosítsa a **value=""** értéket **value="DOMAIN\User"** értékre, ahol a DOMAIN\User egy olyan szolgáltatásfiók, amely a „PhoneFactor-adminisztrátorok” csoport tagja.
    * Keresse meg a **„WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD”** kulcsot, és módosítsa a **value=""** értéket **value="Password"** értékre, ahol a Password az előző sorban megadott szolgáltatásfiókhoz tartozó jelszó.
    * Keresse meg a **https://www.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx** értéket, és módosítsa ezt a helyőrző URL-címet arra a címre, amelyre a Web Service SDK-t telepítette a 2. lépésben.
    * Mentse a Web.Config fájlt, és zárja be a Jegyzettömböt.

6. Nyisson meg egy webböngészőt valamelyik számítógépről, és lépjen arra az URL-címre, ahol a felhasználói portál telepítve van (például: https://mfa.contoso.com/MultiFactorAuth). Győződjön meg arról, hogy nem látható tanúsítvánnyal kapcsolatos figyelmeztetés vagy hiba.

Ha kérdései vannak az SSL-tanúsítvány IIS-kiszolgálón történő konfigurálásáról, olvassa el [Az SSL beállítása az IIS-en](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) cikket.

## <a name="configure-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>A felhasználói portál beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón

Most, hogy telepítette a felhasználói portált, konfigurálnia kell az Azure Multi-Factor Authentication-kiszolgálót, hogy működjön a portállal.

1. Az Azure Multi-Factor Authentication-kiszolgáló konzoljában kattintson a **Felhasználói portál** ikonra. A Beállítások lapon írja be a felhasználói portál URL-címét a **Felhasználói portál URL-címe** szövegmezőbe. Ez az URL-cím a felhasználóknak küldött e-mailekben szerepel a felhasználók Azure Multi-Factor Authentication-kiszolgálóra való importálásakor, ha az e-mailes funkció engedélyezve van.
2. Válassza ki a felhasználói portálon használni kívánt beállításokat. Ha például a felhasználók választhatják ki a hitelesítési módszereket, győződjön meg arról, hogy a **Módszer kiválasztásának engedélyezése a felhasználóknak** jelölőnégyzet be van jelölve azokkal a módszerekkel együtt, amelyek közül választhatnak.
3. A **Rendszergazdák** lapon adja meg, kik legyenek a rendszergazdák. A részletes rendszergazdai engedélyek létrehozásához használja a Hozzáadás/Szerkesztés mezők jelölőnégyzeteit és legördülő listáit.

Választható konfiguráció:
- **Biztonsági kérdések** – Jóváhagyott biztonsági kérdéseket adhat meg a környezetéhez, továbbá a kérdések nyelvét is beállíthatja.
- **Elvégzett munkamenetek** – Konfigurálhatja a felhasználói portál integrációját egy űrlapalapú webhellyel az MFA használatával.
- **Megbízható IP-címek** – Engedélyezheti a felhasználóknak az MFA kihagyását, ha a hitelesítést egy olyan helyről végzik, amely szerepel a megbízható IP-címek vagy tartományok listáján.

![MFA-kiszolgáló felhasználói portáljának konfigurálása](./media/multi-factor-authentication-get-started-portal/config.png)

Az Azure Multi-Factor Authentication-kiszolgáló több lehetőséget nyújt a felhasználói portálhoz. A következő táblázat tartalmazza ezen lehetőségek listáját és annak magyarázatát, hogy mire szolgálnak.

| Felhasználói portál beállításai | Leírás |
|:--- |:--- |
| Felhasználói portál URL-címe | Adja meg a portál üzemeltetési helyének URL-címét. |
| Elsődleges hitelesítés | Adja meg a portálra való bejelentkezéskor használandó hitelesítési típust. Ez Windows-, Radius- vagy LDAP-hitelesítés lehet. |
| Felhasználók bejelentkezésének engedélyezése | Lehetővé teszi, hogy a felhasználók beírjanak egy felhasználónevet és jelszót a bejelentkezési oldalon a felhasználói portálhoz. Ha ez a lehetőség nincs bejelölve, a mezők szürkén jelennek meg. |
| Felhasználó beléptetésének engedélyezése | Lehetővé teszi, hogy a felhasználók regisztráljanak a Multi-Factor Authenticationben egy olyan beállítási képernyő megjelenítésével, amely további információk, például a telefonszám megadását kéri. A másodlagos telefonszámra való rákérdezéskor a felhasználók megadhatnak egy másodlagos telefonszámot. A külső OATH tokenre való rákérdezéskor a felhasználók megadhatnak egy külső OATH tokent. |
| Egyszeri megkerülés kezdeményezésének engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználók egyszeri mellőzést kezdeményezzenek. Ha a felhasználó megadja ezt a beállítást, a következő bejelentkezésekor lép életbe. A mellőzés másodperceire való rákérdezéskor a felhasználók egy mezőben módosíthatják az alapértelmezett 300 másodpercet. Egyéb esetben az egyszeri mellőzés csak 300 másodpercig érvényes. |
| Módszer kiválasztásának engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználók megadják az elsődleges kapcsolatfelvételi módot. Ez lehet telefonhívás, szöveges üzenet, mobilalkalmazás vagy OATH token. |
| Nyelv kiválasztásának engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználó módosítsa a telefonhíváshoz, szöveges üzenethez, mobilalkalmazáshoz vagy OATH tokenhez használt nyelvet. |
| Mobilalkalmazás aktiválásának engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználók aktiválási kódot hozzanak létre a kiszolgálóval használt mobilalkalmazás-aktiválási folyamat elvégzéséhez.  Megadhatja azon eszközök 1 és 10 közötti számát is, amelyeken ezt az alkalmazást aktiválhatják. |
| Biztonsági kérdések használata tartalék megoldásként | Engedélyezi a biztonsági kérdések használatát, ha a kétlépéses ellenőrzés sikertelennek bizonyul. Megadhatja a sikeresen megválaszolandó biztonsági kérdések számát. |
| Harmadik féltől származó OATH token hozzárendelésének engedélyezése a felhasználóknak | Lehetővé teszi, hogy a felhasználók megadhassanak egy külső OATH tokent. |
| OATH token használata tartalékként | Lehetővé teszi egy OATH token használatát, ha a kétlépéses ellenőrzés sikertelennek bizonyul. Ezenkívül a munkamenet időtúllépését is megadhatja percben. |
| Naplózás engedélyezése | Lehetővé teszi a naplózást a felhasználói portálon. A naplófájlok a következő helyen találhatók: C:\Program Files\Multi-Factor Authentication Server\Logs. |

Ezen beállítások láthatóvá válnak a portálon a felhasználók számára, ha engedélyezettek és be vannak jelentkezve a felhasználói portálra.

![Felhasználói portál beállításai](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="self-service-user-enrollment"></a>Önkiszolgáló felhasználói regisztráció

Ha azt szeretné, hogy a felhasználók bejelentkezzenek és regisztráljanak, a Beállítások lapon be kell jelölnie a **Felhasználók bejelentkezésének engedélyezése** és a **Felhasználó beléptetésének engedélyezése** jelölőnégyzetet. Ne feledje, hogy a kiválasztott beállítások hatással vannak a felhasználó bejelentkezési élményére.

Amikor például egy felhasználó először jelentkezik be a felhasználói portálra, az Azure Multi-Factor Authentication felhasználói beállítási lapjára kerül. Az Azure Multi-Factor Authentication konfigurációjától függően lehet, hogy a felhasználó kiválaszthatja a hitelesítési módszert.

Ha a felhasználók a Hanghívás ellenőrzési módszert választják vagy ha ez a módszer van előre konfigurálva, a felhasználóknak a lapon meg kell adniuk az elsődleges telefonszámukat és melléküket, ha van ilyen. Lehet, hogy másodlagos telefonszámot is megadhatnak.

![Elsődleges és másodlagos telefonszámok regisztrálása](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Ha a felhasználóknak PIN-kódot kell használniuk a hitelesítéskor, a lap PIN-kód létrehozását is kéri. A telefonszám(ok) és a PIN-kód (ha van) megadása után a felhasználó a **Hívjon fel a hitelesítéshez** gombra kattint. Az Azure Multi-Factor Authentication telefonhíváson alapuló ellenőrzést kezdeményez a felhasználó elsődleges telefonszámának használatával. A felhasználónak fogadnia kell a hívást, és meg kell adnia a PIN-kódját (ha van), majd meg kell nyomnia a # gombot az önregisztrációs folyamat következő lépésére való továbblépéshez.

Ha a felhasználók a szöveges üzenetes ellenőrzési módszert választják, vagy ha ez a módszer van előre konfigurálva, a lapon meg kell adniuk a mobiltelefonszámukat. Ha a felhasználóknak PIN-kódot kell használniuk a hitelesítéskor, a lap PIN-kód megadását is kéri.  A telefonszám és a PIN-kód (ha van) beírása után a felhasználó a **Küldjön SMS-t a hitelesítéshez** gombra kattint. Az Azure Multi-Factor Authentication SMS-ellenőrzést kezdeményez a felhasználó mobiltelefonszáma használatával. A felhasználó egyszeri jelszót (one-time-passcode, OTP) tartalmazó SMS-t kap, és válaszol az üzenetre az OTP-vel és a PIN-kódjával (ha van).

![Felhasználói portál SMS](./media/multi-factor-authentication-get-started-portal/text.png)

Ha a felhasználó a mobilalkalmazásos ellenőrzési módszert választja, az oldal felkéri, hogy telepítse a Microsoft Authenticator alkalmazást az eszközére, és hozzon létre egy aktiválási kódot. Az alkalmazás telepítése után a felhasználónak az Aktiváló kód előállítása gombra kell kattintania.

> [!NOTE]
> A Microsoft Authenticator alkalmazás használatához a felhasználónak engedélyeznie kell a leküldéses értesítéseket az eszközén.

A lap ezután megjelenít egy aktiválási kódot és egy URL-címet egy vonalkód képével együtt. Ha a felhasználóknak PIN-kódot kell használniuk a hitelesítéskor, a lap PIN-kód megadását is kéri. A felhasználó beírja az aktiválási kódot és az URL-címet a Microsoft Authenticator alkalmazásba, vagy a vonalkódolvasóval beolvassa a vonalkódot, majd az Aktiválás gombra kattint.

Az aktiválás elvégzése után a felhasználó a **Hitelesítsen most** gombra kattint. Az Azure Multi-Factor Authentication ellenőrzést végez a felhasználó mobilalkalmazásában. A felhasználónak meg kell adnia a PIN-kódját (ha van) és a mobilalkalmazásban a Hitelesítés gombra kell kattintania, hogy az önregisztrációs folyamat következő lépésére ugorjon.

Ha a rendszergazdák úgy konfigurálták az Azure Multi-Factor Authentication-kiszolgálót, hogy biztonsági kérdéseket és válaszokat gyűjtsön, a felhasználó a Biztonsági kérdések lapra kerül. A felhasználónak ki kell választania négy biztonsági kérdést, és meg kell válaszolnia azokat.

![A felhasználói portál biztonsági kérdései](./media/multi-factor-authentication-get-started-portal/secq.png)

A felhasználó önregisztrációja ezzel befejeződött, és a felhasználó be van jelentkezve a felhasználói portálra. Ezután bármikor újra bejelentkezhet a felhasználói portálra a telefonszáma, PIN-kódja, hitelesítési módszere és biztonsági kérdései módosításához, ha ezt a rendszergazdáik engedélyezték.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Multi-Factor Authentication-kiszolgáló Mobile App Web Service szolgáltatásának telepítése](multi-factor-authentication-get-started-server-webservice.md)