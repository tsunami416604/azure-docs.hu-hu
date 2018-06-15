---
title: Alkalmazások integrálása az Azure Active Directoryval
description: Hogyan lehet hozzáadni, frissítés, vagy távolítsa el a kérelmet az Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: luleon
ms.openlocfilehash: 76c6ef7d4cf53872dda308628790994b35d8431c
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157996"
---
# <a name="integrating-applications-with-azure-active-directory"></a>Alkalmazások integrálása az Azure Active Directoryban
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Vállalati fejlesztők és a szoftver-,-a-(SaaS) szolgáltatók alakulhat kereskedelmi felhőszolgáltatások vagy üzleti alkalmazások, amely integrálható az Azure Active Directory (Azure AD) a biztonságos bejelentkezés és engedélyezést biztosító ki a szolgáltatások. Egy alkalmazás vagy szolgáltatás integrálása az Azure AD, a fejlesztő először regisztrálnia kell az alkalmazást az Azure ad-val.

Ez a cikk bemutatja, hogyan hozzáadása, frissítése vagy távolítsa el az alkalmazás regisztrálása az Azure ad-ben. Megismerkedhet a különböző típusú, amely integrálható az Azure AD-alkalmazások konfigurálása az alkalmazások, például a webes API-k és még sok más egyéb erőforrásainak elérésére.

A két Azure AD-objektumok, amelyek megfelelnek a regisztrált alkalmazáshoz és a kapcsolat kapcsolatos további információkért lásd: [alkalmazás és szolgáltatás egyszerű objektumok](active-directory-application-objects.md); további információt a arculati útmutatóját, akkor az Azure Active Directoryval alkalmazások fejlesztése során használandó [Branding irányelveket az integrált alkalmazások](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Egy alkalmazás hozzáadása
Bármely alkalmazás, amely szeretné használni az Azure AD képességeit, előbb regisztrálni kell az Azure AD-bérlő. A regisztrációs folyamat során jogosultságot ad az Azure AD adatait az alkalmazás, például az URL-címet, akkor rendelkezik helyét, az URL-cím küldéséhez a válaszokat a felhasználó hitelesítése után az URI, amely azonosítja az alkalmazást, és így tovább.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Regisztrálni egy új alkalmazást, az Azure portál használatával
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók biztosítja az egynél több elérhető, kattintson a jobb felső sarokban a fiókba, és állítsa be a portál munkamenet a kívánt Azure ad bérlői.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatásba, kattintson a **App regisztrációk**, és kattintson a **új alkalmazás regisztrációja**.

   ![Egy új alkalmazás regisztrálása](./media/active-directory-integrating-applications/add-app-registration.png)

4. Ha a **létrehozása** lap jelenik meg, adja meg az alkalmazás termékregisztrációs adatokat: 

  - **Name:** adjon meg egy kifejező nevet
  - **Alkalmazás típusa:** 
    - Válassza ki a "Natív" a [ügyfélalkalmazások](active-directory-dev-glossary.md#client-application) , amelyek telepítése helyileg történik az eszközön. Ezzel a beállítással az OAuth nyilvános [natív ügyfelek](active-directory-dev-glossary.md#native-client).
    - Jelölje ki "webalkalmazás / API" a [ügyfélalkalmazások](active-directory-dev-glossary.md#client-application) és [erőforrás/API-alkalmazások](active-directory-dev-glossary.md#resource-server) a biztonságos kiszolgáló telepítéséhez. Ezzel a beállítással az OAuth bizalmas [webalkalmazás-ügyfelek](active-directory-dev-glossary.md#web-client) és nyilvános [felhasználói ügynök-alapú ügyfelek](active-directory-dev-glossary.md#user-agent-based-client). Ugyanazt az alkalmazást is is elérhetővé teheti a ügyfél és az erőforrás/API.
  - **Bejelentkezési URL-címe:** a "Web app / API" alkalmazások esetén adja meg az alkalmazás alap URL-CÍMÉT. Például `http://localhost:31544` lehet, hogy a helyi gépen futó webes alkalmazás URL-CÍMÉT. Felhasználók használna az URL-cím egy webes ügyfél alkalmazásba való bejelentkezéshez. 
  - **Átirányítási URI-ja:** "Natív" alkalmazások esetén adja meg a token válaszok vissza az Azure AD által használt URI-azonosító. Adja meg például egy adott értéket az alkalmazás `http://MyFirstAADApp`

   ![Egy új alkalmazás regisztrálása – létrehozása](./media/active-directory-integrating-applications/add-app-registration-create.png)

   Ha szeretné, hogy a webes alkalmazások és natív alkalmazások példák, tekintse meg a [quickstarts](active-directory-developers-guide.md#get-started).

5. Ha befejezte, kattintson a **létrehozása**. Az Azure AD alkalmazás azonosító egyedi Azonosítót rendel hozzá az alkalmazáshoz, és meg van-e az alkalmazás fő regisztrációs lapjához. Attól függően, hogy az alkalmazás egy webes vagy natív alkalmazás különböző beállítások állnak rendelkezésre a kiegészítő lehetőségekhez hozzá az alkalmazáshoz. A következő szakaszban áttekintést hozzájárulási és adatok tekintse meg az alkalmazás regisztrációja (hitelesítő adatok, engedélyek engedélyezése bejelentkezhet a felhasználók számára, a többi bérlőtől.) további konfigurációs szolgáltatásainak engedélyezése

  > [!NOTE]
  > Alapértelmezés szerint egy újonnan regisztrált webes alkalmazás van konfigurálva, hogy **csak** ugyanannak a bérlőnek bejelentkezni az alkalmazás felhasználóit.
  > 
  > 

## <a name="updating-an-application"></a>Egy alkalmazás frissítése
Miután az alkalmazás regisztrálva van az Azure ad-vel, kell frissíteni, hogy a webes API-k eléréséhez, elérhetik más szervezetek és még sok más. Ez a szakasz ismerteti a különböző módon, amelyben az alkalmazás további is konfigurálhatja. Először először a hozzájárulási keretrendszer, amely fontos tisztában lenni azzal, ha más felhasználók vagy alkalmazások által használt igénylő alkalmazások áttekintése.

### <a name="overview-of-the-consent-framework"></a>A hozzájárulási keretrendszer áttekintése

Az Azure AD hozzájárulási keretrendszer megkönnyíti a több-bérlős web- és natív ügyfél-alkalmazások fejlesztéséhez. Ezek az alkalmazások az Azure AD-bérlő, ahol az alkalmazás regisztrálva van egy eltérő engedélyező bejelentkezési felhasználói fiókokat. Előfordulhat, hogy is webes API-k, például a Microsoft Graph API (eléréséhez Azure Active Directory, az Intune és a szolgáltatások az Office 365-ben) és más Microsoft-szolgáltatásokban API-k, mellett a saját webes API-k eléréséhez szükséges. A keretrendszer egy felhasználó vagy a rendszergazda jogosultságot ad a beleegyezést kérő regisztrálni kell a könyvtárban, amelyek magukban foglalhatják directory adatokhoz hozzáférő alkalmazáshoz való alapul.

Például egy webes ügyfélalkalmazás rendszernek végig kell olvasnia a felhasználóval kapcsolatos adatok az Office 365 szolgáltatásból, ha a felhasználó hozzájárul az ügyfélalkalmazás először fel kell. Miután hozzájárulási kap, az ügyfélalkalmazás fog tudni hívja fel a Microsoft Graph API a felhasználó nevében, és a naptári adatok használatára, szükség szerint. A [Microsoft Graph API](https://graph.microsoft.io) hozzáférést biztosít az adatok az Office 365-ben (például a naptárak és az Exchange, helyek és a SharePoint, a dokumentumot a onedrive-ról a OneNote-ba, a tervező számára, Excel munkafüzetekhez feladatok notebookok listák üzenetek stb.), valamint felhasználók és csoportok az Azure AD és más adatok több Microsoft felhőszolgáltatás objektumokat. 

A hozzájárulási keretrendszer épül OAuth 2.0 és a különböző adatfolyamok, például engedélyezési code grant és az ügyfél hitelesítő adatai megadják, nyilvános vagy titkos ügyfél használatával. OAuth 2.0 használatával az Azure AD lehetővé teszi számos különböző típusú ügyfélalkalmazások, például a telefon, táblagép, kiszolgáló vagy egy webalkalmazás létrehozása és a szükséges erőforrások eléréséhez.

OAuth2.0 engedélyt biztosít a hozzájárulási keretrendszer használatával kapcsolatban további információkért lásd: [az OAuth 2.0 és az Azure AD használatával webes alkalmazásokhoz való hozzáférés engedélyezésére](active-directory-protocols-oauth-code.md) és [hitelesítési forgatókönyvek az Azure AD](active-directory-authentication-scenarios.md). További információ a hitelesített hozzáférést Microsoft Graph keresztül Office 365: [App hitelesítést a Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>A hozzájárulási élmény példája

Az alábbi lépések bemutatják, hogyan a hozzájárulási tapasztalhat az alkalmazás fejlesztőjének és a felhasználó is működik.

1. Során feltételezzük, hogy egy ügyfél webalkalmazás, amelyet a kérelem konkrét engedélyeket, hogy egy erőforrás vagy API-t. Megtudhatja, hogyan ehhez a következő szakasz ebben a konfigurációban, de alapvetően az Azure portál használatával engedélyekre vonatkozó kérései deklarálható konfigurációs időpontban. A konfigurációs beállításait, például az alkalmazás az Azure AD-regisztrációval részét képezik azok:
   
  ![Egyéb alkalmazások engedélyei](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. Vegye figyelembe, hogy az Alkalmazásengedélyek frissítve lett, az alkalmazás fut, és egy felhasználó először a használni kívánt. Először az alkalmazást kell egy engedélyezési kód kérése az Azure AD `/authorize` végpont. Az engedélyezési kód majd egy új hozzáférést szerezni a és a frissítési token használható.

3. Ha a felhasználó már nem hitelesített, az Azure AD `/authorize` végpont kérni fogja a bejelentkezéshez.
   
  ![Felhasználói vagy rendszergazdai bejelentkezés az Azure AD](./media/active-directory-integrating-applications/usersignin.png)

4. Miután a felhasználó bejelentkezett, az Azure AD határozza meg a felhasználónak kell jeleníthető meg a hozzájárulási lap. Ez a döntés alapján e a felhasználó (vagy annak szervezeti rendszergazdája) már megadta a kérelem jóváhagyását. Hozzájárulás már nincs megadva, ha az Azure AD kérni fogja a felhasználótól a, és megjeleníti a működéséhez szükséges szükséges engedélyekkel. A hozzájárulási párbeszédpanelen megjelenő engedélykészletüket megegyeznek a delegált engedélyek az Azure-portálon választott.
   
  ![Felhasználói hozzájárulás élmény](./media/active-directory-integrating-applications/consent.png)

5. Miután a felhasználó engedélyezi a hozzájárulási, az engedélyezési kód az alkalmazás, amely szerezzen be egy hozzáférési jogkivonatot, és a frissítési token váltják vissza küld vissza. Ez a folyamat kapcsolatos további információkért tekintse meg a [webes API szakasz hitelesítési forgatókönyvek az Azure AD-webalkalmazás](active-directory-authentication-scenarios.md#web-application-to-web-api).

6. A rendszergazdák is is beleegyezik a delegált engedélyek egy alkalmazás a felhasználók nevében az Ön bérelt szolgáltatásának. Felügyeleti hozzájárulási megakadályozza, hogy a hozzájárulási párbeszédpanel a bérlő minden felhasználó számára megjelenő, és teheti a [Azure-portálon](https://portal.azure.com) rendszergazdai szerepkörrel rendelkező felhasználók számára. Az a **beállítások** az alkalmazás lapján kattintson **szükséges engedélyek** , majd kattintson a a **engedélyt adjon** gombra. 

  ![Adja meg explicit admin szerezni a hozzájárulásukat engedélyeket](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > Explicit megadását hozzájárulás használatával a **engedélyt adjon** gomb akkor ADAL.js használó egylapos alkalmazások (SPA) szükséges. Ellenkező esetben az alkalmazás meghiúsul, ha a hozzáférési jogkivonat van szükség. 

### <a name="configure-a-client-application-to-access-web-apis"></a>Webes API-k elérésére ügyfélalkalmazás konfigurálása
Ahhoz, hogy részt vesz egy engedélyezési grant flow, amelyhez hitelesítés szükséges a (és egy hozzáférési jogkivonat beszerzése) webes bizalmas ügyfélalkalmazást azt kell létesítenie a biztonságos hitelesítő adatok. Az alapértelmezett hitelesítési módszer az Azure portál által támogatott ügyfél-azonosító + titkos kulcs. Ez a fejezet a konfigurációs lépéseket kell adnia a titkos kulcsot az ügyfél-hitelesítő adatokkal.

Emellett egy ügyfél egy webes API-t egy erőforrás-alkalmazáshoz (például a Microsoft Graph API) által elérhetővé tett hozzáférni, a hozzájárulási keretrendszer biztosítja az ügyfél beolvassa az engedély megadása szükséges, a kért engedélyek alapján. Alapértelmezés szerint minden alkalmazás engedélyek választhatja ki a "Windows Azure Active Directory" (Graph API-val) és a "Windows Azure szolgáltatásfelügyeleti API." A [Graph API "Bejelentkezés és olvasási felhasználói profil" engedély](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) is alapértelmezettként van beállítva. Ha az ügyfél, amely rendelkezik Office 365 előfizetett fiókok bérlőjében regisztrálva van folyamatban, webes API-k és a SharePoint és az Exchange Online zónaaliasok. Választhat [engedélyek két típusú](active-directory-dev-glossary.md#permissions) az egyes webes API szükséges:

- Alkalmazásengedélyek: Az ügyfélalkalmazást hozzá kell férniük a web API közvetlenül saját magát (felhasználói környezet). Ez a típus engedély rendszergazda jóváhagyását igényli, és nem is érhető el natív ügyfél-alkalmazások.

- Delegált engedélyek: Az ügyfélalkalmazást hozzá kell férniük a webes API-t a bejelentkezett felhasználó nevében, de az elérés korlátozója a kijelölt engedély. Az ilyen típusú engedélyt felhasználó által engedélyezhetők, kivéve, ha az engedély szükséges a rendszergazda jóváhagyását. 

  > [!NOTE]
  > Delegált engedélyek hozzáadása egy alkalmazáshoz nem automatikusan hozzájárulását belül a bérlő számára. Felhasználók hozzájárulás a hozzáadott delegált jogosultságokkal sikeresen telepítették futásidőben, az továbbra is futtathatja manuálisan kell, kivéve, ha a rendszergazda kattint a **engedélyt adjon** gombot a **szükséges engedélyek** szakasza a alkalmazások lap az Azure portálon. 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Adja hozzá az alkalmazás hitelesítő adatait, vagy a hozzáférési engedélye a webes API-khoz
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók biztosítja az egynél több elérhető, kattintson a jobb felső sarokban a fiókba, és állítsa be a portál munkamenet a kívánt Azure ad bérlői.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatásba, kattintson a **App regisztrációk**, majd keresés/kattintson a konfigurálni kívánt alkalmazás.

   ![Egy alkalmazás regisztráció frissítése](./media/active-directory-integrating-applications/update-app-registration.png)

4. A következő lépés az alkalmazás fő regisztrációs oldalra, így akár a **beállítások** az alkalmazás lapját. A webes alkalmazás hitelesítő adatait a titkos kulcs hozzáadása:
  - Kattintson a **kulcsok** a szakasz a **beállítások** lap. 
  - Adjon meg egy leírást a kulcshoz.
  - Válasszon ki egy vagy két év időtartama.
  - Kattintson a **Save** (Mentés) gombra. A jobb szélső oszlop lesz a kulcs értékét, a konfigurációs módosítások mentése után. **Ellenőrizze, hogy a kulcs** az Ügyfélkód használható, mert nem érhető el egyszer bezárja ezt az oldalt.

  ![Egy alkalmazás regisztráció - kulcsok frissítése](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. Az ügyfél erőforrás API-k elérésére engedélyt hozzáadása
  - Kattintson a **szükséges engedélyek** a szakasz a **beállítások** lap. 
  - Kattintson a **Hozzáadás** gombra.
  - Kattintson a **API kiválasztása** válassza ki a kívánt erőforrások típusának kiválasztása.
  - Tallózza végig elérhető API-kat a listáját, vagy használja a keresőmezőt alkalmazásokból az elérhető erőforrás a könyvtárban, amelyek teszi közzé a webes API-k kiválasztásához. Kattintson az erőforrás által szüksége, majd kattintson a **válasszon**.
  - Meg van-e a **hozzáférés engedélyezése** lap. Válassza ki az Alkalmazásengedélyek és/vagy az alkalmazás elérésekor az API-t kell delegált engedélyek.
   
  ![Egy alkalmazás regisztráció - engedélyek frissítése api](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![Egy alkalmazás regisztráció - engedélyek engedélyek frissítése](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. Ha elkészült, kattintson a **kiválasztása** gombra a **hozzáférés engedélyezése** lapon, majd a **végzett** gombra a **hozzáadása API-hozzáférés** lap. A rendszer visszairányítja a **szükséges engedélyek** lap, ahol az új erőforrás API-k listája kerül.

  > [!NOTE]
  > Kattintson a **végzett** gombra is automatikusan beállítja az engedélyeket az alkalmazáshoz beállított egyéb alkalmazások engedélyei alapján a könyvtárban. Ezek az Alkalmazásengedélyek tekintheti meg az alkalmazás megnézi **beállítások** lap.
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Egy erőforrás-alkalmazások – ezek általában a webes API-k konfigurálása

Webes API-k fejlesztése, és tegye elérhetővé számára jelentkezik, mintha a hozzáférési [hatókörök](active-directory-dev-glossary.md#scopes) és [szerepkörök](active-directory-dev-glossary.md#roles). Egy megfelelően konfigurált webes API-t szeretné elérhetővé tenni hasonlóan a más Microsoft webes API-k, beleértve a Graph API-t és az Office 365 API-kat. A hozzáférési hatókörök és szerepkörök keresztül közzétett a [alkalmazás jegyzékfájlja](active-directory-dev-glossary.md#application-manifest), vagyis a JSON-fájl, amely az alkalmazáskonfiguráció identitásának jelöli. 

A következő szakasz bemutatja, hogyan teszi közzé a hozzáférési hatókörök, az erőforrás alkalmazás jegyzékfájlja módosításával.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Hozzáférési hatókörök hozzáadása az erőforrás alkalmazás

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók biztosítja az egynél több elérhető, kattintson a jobb felső sarokban a fiókba, és állítsa be a portál munkamenet a kívánt Azure ad bérlői.

3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatásba, kattintson a **App regisztrációk**, majd keresés/kattintson a konfigurálni kívánt alkalmazás.

   ![Egy alkalmazás regisztráció frissítése](./media/active-directory-integrating-applications/update-app-registration.png)

4. A következő lépés az alkalmazás fő regisztrációs oldalra, így akár a **beállítások** az alkalmazás lapját. Váltás a **Szerkesztés jegyzékfájl** lap, kattintson **Manifest** az alkalmazás regisztrálása oldalról. Megnyílik egy webalapú jegyzék szerkesztő, lehetővé téve **szerkesztése** a jegyzék a portálon. Másik lehetőségként kattinthat **letöltése** helyileg szerkesztése, majd használja **feltöltése** léptetni az alkalmazás számára.

5. Ebben a példában a rendszer elérhetővé kell tenni az új hatókör neve `Employees.Read.All` az erőforrás/API, adja hozzá a következő JSON elem a `oauth2Permissions` gyűjtemény. A meglévő `user_impersonation` hatókör alapértelmezés szerint regisztrálás során. `user_impersonation` lehetővé teszi, hogy a bejelentkezett felhasználó személyazonossága alapján, az erőforrás elérésére engedélyt ügyfélalkalmazást. Ügyeljen arra, hogy a vesszővel hozzáadása után a meglévő `user_impersonation` elem hatókörét, és módosítsa az erőforrás igényeinek megfelelően tulajdonság értékét. 

  ```json
  {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
  }
  ```
  > [!NOTE]
  > Az "id" érték, mint egy GUID generációs eszköz segítségével hozható létre [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) vagy programon keresztül. A hatókör egyedi azonosítóját, a webes API-k által elérhetővé tett képviseli. Amennyiben az ügyfél konfigurálása megfelelő engedéllyel rendelkeznek a webes API-t, OAuth2.0 hozzáférési tokent az Azure AD által kiállított. Az ügyfél hívások a webes API-t, a hozzáférési jogkivonat, amelynek a hatóköre (scp) azt mutatja be az engedélyeket, az alkalmazás regisztrálása a kért állítja jogcímet.
  >
  > További hatókörökkel később szükség szerint is elérhetővé teheti. Vegye figyelembe, hogy a webes API számos különböző funkcióihoz tartozó több hatókör esetleg felfedi. Az erőforrás is való hozzáférést a webes API-t, futásidőben hatókörének (`scp`) jogcím(ek) a fogadott OAuth 2.0 hozzáférési jogkivonat.
  > 

6. Ha befejezte, kattintson a **mentése**. Most a webes API más alkalmazás a könyvtárban van beállítva. 

  ![Egy alkalmazás regisztráció frissítése](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Ellenőrizze a webes API van kitéve a bérlő lévő más alkalmazásokkal
1. Nyissa meg az Azure AD vissza a bérlői, kattintson a **App regisztrációk** újra, majd keresés/kattintson a konfigurálni kívánt ügyfélalkalmazást.

   ![Egy alkalmazás regisztráció frissítése](./media/active-directory-integrating-applications/update-app-registration.png)

2. 5. lépést megismételve a [webes API-k elérésére ügyfélalkalmazás konfigurálása](#configure-a-client-application-to-access-web-apis). Amikor elér a **API kiválasztása** . lépés:, keresse meg az erőforrás a keresési mezőbe írja be az alkalmazás nevét, majd kattintson **válasszon**. 

3. Az a **hozzáférés engedélyezése** lapon megtekintheti az új hatókör, az ügyfélkérelmek engedély érhető el.

  ![Új engedélyek láthatók.](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>További információk az az alkalmazás jegyzékében

Az alkalmazás jegyzékében ténylegesen a frissítés az alkalmazás entitás, amely meghatározza egy Azure AD-alkalmazást identitás konfiguráció – beleértve az API a hozzáférési hatókörök tárgyalt összes attribútumát egy mechanizmusként funkcionál. Az alkalmazás entitás és a séma további információkért lásd: a [Graph API-alkalmazás entitás dokumentáció](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Az alkalmazás entitástagok használatával adja meg az API-engedélyeit a teljes hivatkozási információt tartalmaz többek között:  

- A appRoles számára, amely gyűjtemény a [birtokolhat](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) meghatározásához használt entitások [Alkalmazásengedélyek](active-directory-dev-glossary.md#permissions) webes API-k esetében. 
- A oauth2Permissions számára, amely gyűjtemény a [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) meghatározásához használt entitások [delegált engedélyekkel](active-directory-dev-glossary.md#permissions) webes API-k esetében.

További információ az alkalmazás általában manifest fogalmak című [az Azure Active Directory alkalmazásjegyzékének megismerése](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Az Azure AD Graph és az Office 365 keresztül Microsoft Graph API-k eléréséhez  

A korábbiak mellett az ilyen/elérése API-k a saját alkalmazásai regisztrálhatja az ügyfélalkalmazást API-k által Microsoft erőforrások elérésére. A Microsoft Graph API néven érhető el a portál erőforrás/API listában "Microsoft Graph" az Azure ad-vel regisztrált összes alkalmazásra. Ha az ügyfélalkalmazást, amely az Office 365-előfizetésre feliratkozott fiókokat tartalmazó bérlőjében regisztrál, a hatókörök által a különböző Office 365-erőforrások is elérhető.

A Microsoft Graph API által elérhetővé tett hatóköre teljes leírása, tekintse meg a [Microsoft Graph engedélyek hivatkozás](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference) cikk.

> [!NOTE]
> A jelenlegi korlátozás miatt natív ügyfélalkalmazások csak hívhatják meg azokat az Azure AD Graph API ha azok a "Hozzáférés a munkahely címtárában" engedéllyel. Ez a korlátozás vonatkozik webes alkalmazásokhoz.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Több-bérlős alkalmazások konfigurálása

Ha egy alkalmazás regisztrálása az Azure ad-ben, érdemes lehet az alkalmazás csak a felhasználók elérni a szervezet számára. Másik lehetőségként is érdemes lehet az alkalmazás a felhasználók a külső szervezetek elérhetőnek kell lennie. Ezek a típusok két alkalmazás egyetlen-bérlő és a több-bérlős alkalmazásoknak nevezzük. Ez a szakasz tárgyalja abba, hogy egy több-bérlős alkalmazás egyetlen-bérlő alkalmazás konfigurációjának módosítása.

Fontos megjegyzés: single-bérlő és a több-bérlős alkalmazás közötti különbségek:  

- A single-bérlői alkalmazások szól az egyik szervezet. Általában le egy olyan vállalati fejlesztők által írt-üzletági (LoB) alkalmazás. A single-bérlői alkalmazások ugyanannak a bérlőnek, mint az alkalmazás regisztrációja fiókkal rendelkező felhasználók csak érhető el. Ennek eredményeképpen csak kell építhető ki egy könyvtárat.
- Egy több-bérlős alkalmazás számos szervezet használatra készült. Néven szoftver,--szolgáltatás (SaaS) webalkalmazás, akkor általában írja független szoftverszállító (ISV). Több-bérlős alkalmazásokhoz ki kell építenie az egyes bérlők, ahol felhasználóknak hozzá kell férniük. A bérlők számára, ahol az alkalmazás regisztrálva van más a felhasználó vagy a rendszergazda jóváhagyását szükség ahhoz, hogy regisztrálja őket. Ne feledje, hogy natív ügyfélalkalmazások több-bérlős alapértelmezés szerint az erőforrás tulajdonosa eszközön telepítettként. Lásd az előző [hozzájárulási keretében áttekintése](#overview-of-the-consent-framework) című szakaszban talál információt a hozzájárulási keretrendszerre.

Így egy alkalmazás több-bérlős mindkét alkalmazás regisztrációs módosítását igényli, valamint a webes alkalmazás maga vált. A következő szakaszok is foglalkozik.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>A több-bérlős támogatásához regisztrációja módosítása

Ha egy alkalmazás, ha az ügyfelekre és partnerekre a szervezeten kívül szeretné elérhetővé tenni kívánt, módosítania a definíciót az Azure portálon.

> [!IMPORTANT]
> Az Azure AD App ID URI-azonosítója globálisan egyedi több-bérlős alkalmazásokhoz szükséges. Az App ID URI az alkalmazás azonosítható protokoll üzeneteinek eljárások valamelyikével. Egyetlen bérlői alkalmazások a rendszer megfelelő a App ID URI bérlőre belül egyedinek kell lennie. Egy több-bérlős alkalmazáshoz kell legyen globálisan egyedi, az Azure AD megtalálja az alkalmazás összes bérlők között. Globális egyediségi azzal, hogy rendelkezik egy állomásnevet, amely megfelel az Azure AD-bérlő ellenőrzött tartományt App ID URI érvényesül. Például, ha a bérlő neve contoso.onmicrosoft.com, majd egy érvényes App ID URI lenne https://contoso.onmicrosoft.com/myapp. Ha a bérlő rendelkezik egy ellenőrzött tartomány, a contoso.com, akkor egy érvényes App ID URI is használhatók lesznek https://contoso.com/myapp. Ha az App ID URI nem követi a ebben a mintában beállítását egy alkalmazás több-bérlős sikertelen lesz.
> 

Ahhoz, hogy megkapja a külső felhasználók számára az alkalmazás elérését: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók biztosítja az egynél több elérhető, kattintson a jobb felső sarokban a fiókba, és állítsa be a portál munkamenet a kívánt Azure ad bérlői.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatásba, kattintson a **App regisztrációk**, majd keresés/kattintson a konfigurálni kívánt alkalmazás. A következő lépés az alkalmazás fő regisztrációs oldalra, így akár a **beállítások** az alkalmazás lapját.
4. Az a **beállítások** kattintson **tulajdonságok** , és módosítsa a **Multi-központjaként** váltani **Igen**.

Miután kiválasztotta a módosításokat, felhasználók és más vállalatok rendszergazdái képesek megadja a felhasználóknak jelentkezzen be az alkalmazás képes így az alkalmazás a bérlő által védett erőforrások eléréséhez.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Az alkalmazás támogassa a több-bérlős módosítása

Támogatja a több-bérlős alkalmazásokhoz az Azure AD hozzájárulási keretrendszer fokozottan támaszkodik. Hozzájárulás, akkor az eszköz, amely lehetővé teszi a felhasználóknak az alkalmazás-hozzáférést biztosít a felhasználó-bérlője által biztosított erőforrásokhoz egy másik bérlőhöz. Ez a felület nevezzük "felhasználói hozzájárulás."

A webes alkalmazás is lehet, hogy kínál.

- A lehetőségét, hogy a rendszergazdák számára, hogy "a vállalati regisztráció." Ez a felület "rendszergazda jóváhagyását," néven segítségével a rendszergazdák a funkció a hozzájárulás nevében *minden felhasználó* a szervezetek. Csak egy felhasználó egy olyan fiókkal, amely a globális rendszergazdai szerepkör tartozik hitelesítő biztosíthat a rendszergazda jóváhagyását; mások hibaüzenetet kap.

- A bejelentkezési élmény a felhasználók számára. Várható, hogy a felhasználó biztosítja-e a "előfizetés" gombra kattintva a böngésző átirányítja az Azure AD OAuth2.0 `/authorize` endpoint vagy az OpenID Connect `/userinfo` végpont. Itt engedélyezheti az alkalmazásnak az új felhasználó információt lekérdezni a id_token vizsgálatával ezeket a végpontokat. A regisztrációs fázisban, számára jelenik meg a felhasználó a jóváhagyási kérése, hasonló a képen látható a következő a [hozzájárulási keretében áttekintése](#overview-of-the-consent-framework) szakasz.

Az alkalmazás módosítások multi-központjaként hozzáférési és bejelentkezési-a/regisztrációs élmény támogatásához szükséges további információkért lásd:

- [Az Azure Active Directory (AD) felhasználóinak bejelentkeztetése több-bérlős alkalmazásminta használatával](active-directory-devhowto-multi-tenant-overview.md)
- A listája [több-bérlős mintakódok](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Gyors üzembe helyezés: Adja hozzá a vállalati arculat megjelenítése a bejelentkezési oldal az Azure ad-ben](../customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Engedélyezése OAuth 2.0 implicit adja meg egy lap alkalmazások

Egyetlen lap alkalmazás (gyógyfürdők) általában felépítése a JavaScript-gyakori előtér, amely a böngészőben, amely meghívja az alkalmazás webes API-t az üzleti logika végrehajtásához háttér futtatja. Az Azure ad-ben üzemeltetett gyógyfürdők, az OAuth 2.0 Implicit Grant hitelesíteni a felhasználót az Azure AD és használhat, amelyekkel az alkalmazás JavaScript ügyfél hívást a háttér-webes API biztonságossá jogkivonat beszerzése. 

Miután a felhasználó számára engedélyezett a hozzájárulási, a hitelesítési protokollnak használható az alkalmazáshoz konfigurált API erőforrások biztonságba helyezése az ügyfél és a más webkiszolgáló közötti hívások-jogkivonat beszerzése. További tudnivalók az implicit hitelesítésengedélyezési, és segít eldönteni, hogy az alkalmazás forgatókönyv szerint jobb [OAuth2 implicit ismertetése adja meg az Azure Active Directoryban folyamat](active-directory-dev-understanding-oauth2-implicit-grant.md).

OAuth 2.0 típusú implicit Grant alkalmazások alapértelmezés szerint le van tiltva. Úgy, hogy az alkalmazás OAuth 2.0 Implicit Grant ahol engedélyezheti a `oauth2AllowImplicitFlow` értéket a [alkalmazásjegyzék](active-directory-application-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>OAuth 2.0 típusú implicit támogatás engedélyezése

> [!NOTE]
> A részletes információt az alkalmazás jegyzékében szerkesztése lehet arra, hogy először tekintse át az előző szakaszban [teszi közzé egy erőforrás-alkalmazások konfigurálása webes API-khoz](#configuring-a-resource-application-to-expose-web-apis).
>

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók biztosítja az egynél több elérhető, kattintson a jobb felső sarokban a fiókba, és állítsa be a portál munkamenet a kívánt Azure ad bérlői.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatásba, kattintson a **App regisztrációk**, majd keresés/kattintson a konfigurálni kívánt alkalmazás. A következő lépés az alkalmazás fő regisztrációs oldalra, így akár a **beállítások** az alkalmazás lapját.
4. Váltás a **Szerkesztés jegyzékfájl** lap, kattintson **Manifest** az alkalmazás regisztrálása oldalról. Megnyílik egy webalapú jegyzék szerkesztő, lehetővé téve **szerkesztése** a jegyzék a portálon. Keresse meg és állítsa be a "oauth2AllowImplicitFlow" értéke "true"értékre. Alapértelmezés szerint van állítva, akkor a "false".
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. A frissített jegyzékfájl mentése. Miután menti, a webes API konfigurálva van az OAuth 2.0 Implicit Grant segítségével hitelesíti a felhasználókat.

## <a name="removing-an-application"></a>Az alkalmazás eltávolítása
Ez a szakasz ismerteti a távolítsa el az alkalmazás regisztrálása az Azure AD-bérlő.

### <a name="removing-an-application-authored-by-your-organization"></a>A szervezet által létrehozott alkalmazás eltávolítása
A szervezet által regisztrált alkalmazások csoportban jelennek meg a "Saját alkalmazások" szűrő a bérlő App regisztrációk"fő" lapon. Ezek az alkalmazások azok manuálisan regisztrált az Azure-portálon vagy programozottan PowerShell vagy a Graph API segítségével. Pontosabban, azok vannak objektum által jelképezett mindkét egy alkalmazás és egyszerű szolgáltatás az Ön bérelt szolgáltatásának. További információkért lásd: [alkalmazás és szolgáltatás egyszerű objektumok](active-directory-application-objects.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>A címtárban lévő egyetlen-bérlő alkalmazás eltávolítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók biztosítja az egynél több elérhető, kattintson a jobb felső sarokban a fiókba, és állítsa be a portál munkamenet a kívánt Azure ad bérlői.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatásba, kattintson a **App regisztrációk**, majd keresés/kattintson a konfigurálni kívánt alkalmazás. A következő lépés az alkalmazás fő regisztrációs oldalra, így akár a **beállítások** az alkalmazás lapját.
4. Az alkalmazás fő regisztrációs lapon kattintson **törlése**.
5. Kattintson a **Igen** a megerősítő üzeneten.

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>A saját címtárukkal több-bérlős alkalmazás eltávolítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók biztosítja az egynél több elérhető, kattintson a jobb felső sarokban a fiókba, és állítsa be a portál munkamenet a kívánt Azure ad bérlői.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatásba, kattintson a **App regisztrációk**, majd keresés/kattintson a konfigurálni kívánt alkalmazás. A következő lépés az alkalmazás fő regisztrációs oldalra, így akár a **beállítások** az alkalmazás lapját.
4. Az a **beállítások** lapon, válassza ki **tulajdonságok** , és módosítsa a **Multi-központjaként** váltani **nem**, először módosíthatja az alkalmazás számára Single-bérlőt, majd kattintson a **mentése**. Az alkalmazás szolgáltatás egyszerű objektumok az összes olyan szervezeteknek, amelyek már hozzájárult, hogy azt a bérlői maradnak.
5. Kattintson a **törlése** gombra az alkalmazás fő regisztrációs oldalról.
6. Kattintson a **Igen** a megerősítő üzeneten.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Egy másik szervezet által felhatalmazott több-bérlős alkalmazás eltávolítása
Az alkalmazásokat, amely alatt az "Összes alkalmazások" szűrő megjelenik egy részét (kivéve a "Saját alkalmazások" regisztrációk) a bérlő "App regisztrációk" főoldalon, amelyek több-bérlős alkalmazásokhoz. Technikai szempontból a több-bérlős alkalmazásokhoz egy másik bérlőhöz, és volt a hozzájárulási folyamat során a bérlő regisztrálni. Pontosabban, azok vannak objektum által jelképezett csak a szolgáltatás egyszerű az Ön bérlőjében, nem megfelelő alkalmazás-objektum. Az alkalmazás és szolgáltatás egyszerű objektumok közötti különbségekkel kapcsolatos további információkért lásd: [alkalmazás és szolgáltatás egyszerű objektumok az Azure AD](active-directory-application-objects.md).

El szeretné távolítani egy több-bérlős alkalmazás hozzáférés a címtárhoz (után hozzájárulási megadó), a vállalati rendszergazda el kell távolítania a szolgáltatásnevet. A rendszergazda kell globális rendszergazdai hozzáféréssel rendelkeznek, és távolítsa el az Azure portálon keresztül vagy használja a [Azure AD PowerShell-parancsmagok](http://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>További lépések
- Az Azure AD hitelesítési működéséről további információkért lásd: [hitelesítési forgatókönyvek az Azure AD](active-directory-authentication-scenarios.md).
- Tekintse meg a [Branding irányelveket az integrált alkalmazások](active-directory-branding-guidelines.md) kapcsolatos tippek az alkalmazások visual útmutatást.
- Az alkalmazás alkalmazás- és szolgáltatásnevet objektumok közötti kapcsolatot további információkért lásd: [alkalmazás és szolgáltatás egyszerű objektumok](active-directory-application-objects.md).
- További információt a szerepkör az alkalmazás jegyzékének lejátszása közben, lásd: [az Azure Active Directory alkalmazásjegyzékének megismerése](active-directory-application-manifest.md)
- Tekintse meg a [az Azure AD fejlesztői szószedet](active-directory-dev-glossary.md) néhányat az Azure AD alapvető fejlesztői fogalmak definícióját.
- Látogasson el a [Active Directory fejlesztői útmutatója](active-directory-developers-guide.md) minden fejlesztéssel kapcsolatos tartalom áttekintését.

