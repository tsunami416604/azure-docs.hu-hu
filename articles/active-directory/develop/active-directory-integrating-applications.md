---
title: Alkalmazások integrálása az Azure Active Directoryval
description: Hogyan hozzáadása, módosítása és alkalmazás eltávolítása az Azure Active Directoryban (Azure AD).
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
ms.openlocfilehash: 7ff8df2d3d0ef90607e670cd0754228e0f1d02c3
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493894"
---
# <a name="integrating-applications-with-azure-active-directory"></a>Alkalmazások integrálása az Azure Active Directoryval
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Vállalati fejlesztők és a szoftver--szolgáltatásként (SaaS) szolgáltatók alakulhat kereskedelmi felhőszolgáltatásokat és üzleti alkalmazások esetén, amelyek integrálhatók az Azure Active Directory (Azure AD) a biztonságos bejelentkezéshez és engedélyezést biztosító azok szolgáltatások. Egy alkalmazás vagy szolgáltatás integrálása az Azure ad-ben, egy fejlesztői először regisztrálnia kell az alkalmazás az Azure ad-ben.

Ez a cikk bemutatja, hogyan hozzáadása, frissítése vagy távolítsa el az alkalmazás regisztrálása az Azure ad-ben. Megismerkedhet a különböző típusú alkalmazásokat, amelyek integrálhatók az Azure AD-ben az alkalmazások, például a webes API-kat és más egyéb erőforrásainak elérésére konfigurálása.

A két Azure AD-objektum regisztrált alkalmazásra és a közöttük kapcsolat képviselő kapcsolatos további információkért lásd: [alkalmazásobjektumok és egyszerű szolgáltatási objektumok](active-directory-application-objects.md); tudhat meg többet, érdemes arculati irányelveinek használhatja az Azure Active Directory-alkalmazások fejlesztése során, [védjegyzési irányelvek integrált alkalmazások](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Egy alkalmazás hozzáadása
Minden olyan alkalmazás, amely az Azure ad-ben funkcióinak használatát szeretne először regisztrálni kell az Azure AD-bérlővel. A regisztrálási folyamat magában foglalja a jogosultságot ad az alkalmazások, például az URL-címet, hogy hol található, a válaszok elküldésére, a felhasználó hitelesítése után az URL-cím az Azure AD részleteit az URI-t, amely azonosítja az alkalmazást, és így tovább.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Az Azure portal használatával egy új alkalmazás regisztrálása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók lehetőséget biztosít több mint egy eléréséhez, kattintson a jobb felső sarokban a fiókjába, és állítsa be a portál munkamenet a kívánt Azure ad-bérlőben.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatást, kattintson a **alkalmazásregisztrációk**, és kattintson a **új alkalmazásregisztráció**.

   ![Egy új alkalmazás regisztrálása](./media/active-directory-integrating-applications/add-app-registration.png)

4. Ha a **létrehozás** lap, adja meg az alkalmazás regisztrációs adatait: 

  - **Name:** adjon meg egy kifejező alkalmazásnevet
  - **Alkalmazás típusa:** 
    - Válassza ki a "Natív" a [ügyfélalkalmazások](active-directory-dev-glossary.md#client-application) telepített helyileg az eszközön. Ezzel a beállítással OAuth nyilvános [natív ügyfelek](active-directory-dev-glossary.md#native-client).
    - Jelölje be "webalkalmazás / API" a [ügyfélalkalmazások](active-directory-dev-glossary.md#client-application) és [erőforrás és az API-alkalmazások](active-directory-dev-glossary.md#resource-server) biztonságos kiszolgálón telepített. Ezzel a beállítással az OAuth bizalmas [webes ügyfél](active-directory-dev-glossary.md#web-client) és nyilvános [felhasználói ügynök-alapú ügyfelek](active-directory-dev-glossary.md#user-agent-based-client). Egy ügyfél- és erőforrás és az API, ugyanazt az alkalmazást is közzéteheti.
  - **Bejelentkezési URL-címe:** a "webalkalmazás / API" alkalmazások esetén adja meg az alkalmazás alap URL-CÍMÉT. Ha például `http://localhost:31544` lehet, hogy a helyi gépen futó webalkalmazás URL-CÍMÉT. Felhasználók használna az URL-címet a webes ügyfélalkalmazás bejelentkezni. 
  - **Átirányítási URI-ja:** "Natív" alkalmazások, adja meg az Azure AD a jogkivonatválaszok visszaadására használt URI-ja. Adja meg például egy adott értéket az alkalmazás `http://MyFirstAADApp`

   ![Egy új alkalmazás regisztrálása – létrehozása](./media/active-directory-integrating-applications/add-app-registration-create.png)

   Ha szeretné, hogy adott példák a webalkalmazásokra vagy natív alkalmazásokat, tekintse meg a [útmutatóink](azure-ad-developers-guide.md#get-started).

5. Ha végzett, kattintson a **Létrehozás** gombra. Azure ad-ben a egyedi Azonosítót rendel az alkalmazás, és ekkor átkerül az alkalmazás fő való regisztrációhoz. Attól függően, hogy az alkalmazás webes vagy natív alkalmazás más lehetőségek állnak rendelkezésre további funkciók hozzáadása az alkalmazáshoz. Beleegyezése és az adatok áttekintését a következő szakaszban tekintse meg az alkalmazás regisztrálása (hitelesítő adatok, engedélyek engedélyezése jelentkezzen be a felhasználók számára, a többi bérlőtől.) további konfigurációs szolgáltatásainak engedélyezése

  > [!NOTE]
  > Alapértelmezés szerint egy újonnan regisztrált webalkalmazás van konfigurálva, hogy **csak** ugyanahhoz a bérlőhöz való bejelentkezéshez az alkalmazás felhasználóit.
  > 
  > 

## <a name="updating-an-application"></a>Egy alkalmazás frissítése
Miután az alkalmazás regisztrálva lett az Azure ad-vel, frissíteni kell a webes API-k hozzáférést biztosítanak, elérhetővé válik az más szervezetek és más szükségessé. Ez a szakasz ismerteti a különféle módon, amelyben az alkalmazás további is konfigurálhatja. Először kezdődik a hozzájárulási keretrendszer, amely azért fontos, hogy más felhasználók vagy alkalmazások által használt igénylő alkalmazások készítése során áttekintést.

### <a name="overview-of-the-consent-framework"></a>A hozzájárulási keretrendszer áttekintése

Az Azure ad-ben hozzájárulási keretrendszer megkönnyíti a több-bérlős webes és natív ügyfélalkalmazások fejlesztéséhez. Ezek az alkalmazások engedélyezheti a bejelentkezési felhasználói fiókokat az Azure AD-bérlővel, ahol az alkalmazás regisztrálva van-e eltérő. Webes API-k, például a Microsoft Graph API (az Azure Active Directory, az Intune és az Office 365-ben szolgáltatások eléréséhez) és más Microsoft-szolgáltatások API-k, a saját webes API-k mellett eléréséhez is szükséges lehet. A keretrendszer egy felhasználó vagy rendszergazda engedélyezi hogy egy alkalmazás, amely rákérdez, regisztrálni kell a könyvtárban, amelyek magukban foglalhatják a directory-adatok elérése alapul.

Például ha webes ügyfélalkalmazásnak szüksége van a felhasználó naptár adatainak olvasásához az Office 365-ből, a felhasználó szükséges, hogy engedélyt adjanak az ügyfélalkalmazásnak. Miután jóváhagyás van megadva, az ügyfélalkalmazás lesz a Microsoft Graph API meghívása a felhasználó nevében, és használhatják a naptári adatok, igény szerint. A [Microsoft Graph API](https://graph.microsoft.io) hozzáférést biztosít az adatok az Office 365-ben (például a naptárak és az Exchange, helyek és a SharePoint, a dokumentumot a onedrive-ról, a OneNote-ban, a Planner, a munkafüzetet az Excelben a feladatokat a notebookok listák üzenetek stb.), valamint a felhasználók és csoportok Azure AD-ből és más objektumok további Microsoft cloud servicesből. 

A hozzájárulási keretrendszer az OAuth 2.0 és a különböző folyamatok épül, mint például engedélyezési kód engedélyezési és az ügyfél hitelesítő adatainak megadása, nyilvános vagy bizalmas ügyfelek használatával. OAuth 2.0 használatával az Azure AD lehetővé teszi számos különböző típusú ügyfélalkalmazások, például telefonon, táblagépen, kiszolgáló vagy egy webalkalmazásban, illetve a szükséges erőforrásokhoz való hozzáférés elnyeréséhez.

A hozzájárulási keretrendszer OAuth2.0-engedélyezések való használatával kapcsolatos további információkért lásd: [engedélyezése webalkalmazásoknak OAuth 2.0 és az Azure AD használatával hozzáférést](active-directory-protocols-oauth-code.md) és [hitelesítési forgatókönyvek az Azure AD](authentication-scenarios.md). További információ a jogosult hozzáférni az Office 365 a Microsoft Graph-n keresztül: [alkalmazáshitelesítést a Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>A jóváhagyási felületen – példa

A következő lépések bemutatják, hogyan a a jóváhagyási működik az alkalmazás fejlesztői és a felhasználói élmény.

1. Fel, hogy egy ügyfél webalkalmazást, amely egy erőforrás és az API eléréséhez adott engedélyek kéréséhez szükséges. Megtudhatja, hogyan ehhez a következő szakaszban Ez a konfiguráció, de alapvetően az Azure portal segítségével alkalmazásengedély-kérelmeket deklarálja a konfiguráláskor. Egyéb olyan konfigurációs beállításoknak, például az alkalmazás Azure AD-regisztrációs részét képezik azok:
   
  ![Egyéb alkalmazások engedélyei](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. Vegye figyelembe, hogy az Alkalmazásengedélyek frissítve lett-e, az alkalmazás fut, és egy felhasználó arra készül, hogy első alkalommal használja. Először az alkalmazás-engedélyezési kód beszerzése az Azure AD-ből kell `/authorize` végpont. Az engedélyezési kód majd egy új hozzáférési beszerezni, és a jogkivonat frissítésére használható.

3. Ha a felhasználó még nem hitelesített, Azure AD `/authorize` végpont kérni fogja a bejelentkezéshez.
   
  ![Felhasználó vagy rendszergazda bejelentkezhet az Azure ad-ben](./media/active-directory-integrating-applications/usersignin.png)

4. Miután a felhasználó jelentkezett be, az Azure AD határozza meg, ha a felhasználónak megjelenítendő egy hozzájárulást kérő lap. Ez a döntés e a felhasználó (vagy a szervezet rendszergazdája) már megadta az alkalmazás jóváhagyásának alapul. Jóváhagyás nem már rendelkezik, ha az Azure AD felkéri a felhasználót a hozzájárulásra, és megjeleníti a működéséhez szükséges engedélyekkel. A beleegyezés párbeszédpanelen megjelenő engedélykészletet felel meg a delegált engedélyek az Azure Portalon a kijelölt azokat.
   
  ![Felhasználói jóváhagyás élmény](./media/active-directory-integrating-applications/consent.png)

5. Miután a felhasználó engedélyezi a jóváhagyás, az engedélyezési kódot az alkalmazás, amely váltják a hozzáférési jogkivonat beszerzése és a frissítési token vissza küld vissza. Ezzel a folyamattal kapcsolatos további információkért lásd: a [webes API szakasz hitelesítési forgatókönyvek az Azure ad-webalkalmazás](authentication-scenarios.md#web-application-to-web-api).

6. A rendszergazdák is is beleegyezik az összes felhasználó nevében egy alkalmazás delegált engedélyeit a bérlőben. Rendszergazdai jóváhagyás megakadályozza, hogy a beleegyezés párbeszédpanelen jelenik meg a bérlő összes felhasználója számára, és elvégezhető a [az Azure portal](https://portal.azure.com) a rendszergazdai szerepkörrel rendelkező felhasználók által. Az a **beállítások** oldalon az alkalmazás **szükséges engedélyek** , majd kattintson a a **engedélyek megadása** gombra. 

  ![Engedélyek megadása az explicit rendszergazdai jóváhagyás](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > Hozzájárulás megadása az explicit használatával a **engedélyek megadása** gomb ADAL.js használó egyoldalas alkalmazások (SPA) jelenleg szükség. Ellenkező esetben a kérelem sikertelen lesz, amikor a hozzáférési jogkivonatot kér. 

### <a name="configure-a-client-application-to-access-web-apis"></a>Webes API-k eléréséhez ügyfélalkalmazás konfigurálása
Ahhoz, hogy egy webes/bizalmas ügyfélalkalmazás egy engedélyezési megadási folyamatában, amelyhez hitelesítés szükséges részt (és a hozzáférési jogkivonat beszerzése), az azt kell létesítenie a biztonságos hitelesítő adatok. Az Azure portál által támogatott alapértelmezett hitelesítési módszere, ügyfél-Azonosítót és a titkos kulcsot. Ebben a részben adja meg a titkos kulcsot az ügyfél-hitelesítő adatok a szükséges konfigurációs lépéseket ismertetjük.

Ezenkívül egy ügyfél hozzáfér a webes API-k egy erőforrás-alkalmazás (például a Microsoft Graph API) által elérhetővé tett, mielőtt a hozzájárulási keretrendszer biztosítja, hogy az ügyfél lekéri az engedély megadására, szükség esetén a kért engedélyek alapján. Alapértelmezés szerint minden alkalmazás engedélyek közül választhat "Windows Azure Active Directory" (Graph API-val) és a "Windows Azure Service Management API-t." A [Graph API "be- és olvasási felhasználói profil" engedély](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) is alapértelmezés szerint van kiválasztva. Ha az ügyfél, amely rendelkezik az Office 365-höz előfizetett fiókok bérlőben regisztrálva van folyamatban, webes API-k és a SharePoint és Exchange online-hoz is kiválasztható. Közül választhat [engedélyek két típusú](active-directory-dev-glossary.md#permissions) minden szükséges a webes API számára:

- Alkalmazásengedélyek: Az ügyfélalkalmazás hozzáféréssel kell rendelkeznie a webes API-t közvetlenül magaként (felhasználói környezet). Az ilyen típusú engedély rendszergazdai jóváhagyást igényel, és sem érhető el natív ügyfélalkalmazások esetében.

- Delegált engedélyek: Az ügyfélalkalmazásnak szüksége van a webes API-k elérése a bejelentkezett felhasználó, de az elérés korlátozója a kijelölt engedéllyel. Engedély az ilyen típusú adható felhasználó által, kivéve, ha az engedély szükséges a rendszergazdai jóváhagyást. 

  > [!NOTE]
  > Delegált engedély hozzáadása egy alkalmazáshoz nem automatikusan biztosít hozzájárulása a felhasználók számára a bérlőn belül. Felhasználók kell manuálisan még mindig vonatkozó beleegyezés a hozzáadott delegált engedélyeket futásidőben, kivéve, ha a rendszergazda kattint a **engedélyek megadása** gombra a **szükséges engedélyek** szakaszában a alkalmazások lap az Azure Portalon. 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Alkalmazás hitelesítő adatait, vagy a webes API-k elérésére jogosult hozzáadása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók lehetőséget biztosít több mint egy eléréséhez, kattintson a jobb felső sarokban a fiókjába, és állítsa be a portál munkamenet a kívánt Azure ad-bérlőben.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatást, kattintson a **alkalmazásregisztrációk**, majd keresse meg és kattintson a konfigurálni kívánt alkalmazás.

   ![Frissítse az alkalmazás regisztrálása](./media/active-directory-integrating-applications/update-app-registration.png)

4. Ekkor megnyílik az alkalmazás fő regisztrációs oldal, amely megnyílik a **beállítások** az alkalmazás lapját. A webalkalmazás hitelesítő adatokat a titkos kulcs hozzáadása:
  - Kattintson a **kulcsok** szakaszában a **beállítások** lapot. 
  - Adjon hozzá egy leírást a kulcshoz.
  - Válasszon ki egy vagy két év időtartama.
  - Kattintson a **Save** (Mentés) gombra. A jobb szélső oszlop pedig tartalmazza a kulcs értékét, a konfigurációs módosítások mentése után. **Ügyeljen arra, hogy a kulcs másolása** az Ügyfélkód használatra, mert nem érhető el egyszer elhagyja az oldalt.

  ![Egy alkalmazás regisztrálása - kulcsok frissítése](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. Erőforrás API-k elérésére a ügyfélről eke hozzáadása
  - Kattintson a **szükséges engedélyek** szakaszában a **beállítások** lapot. 
  - Kattintson a **Hozzáadás** gombra.
  - Kattintson a **API kiválasztása** , adja meg, válassza ki a kívánt erőforrásokat.
  - Böngésszen az elérhető API-k listáját, vagy használja a keresőmezőt az alkalmazásokból kiválaszthassák az elérhető erőforrás a könyvtárban, amelyek teszik közzé a webes API-t. Kattintson az iránt, majd kattintson az erőforrás **kiválasztása**.
  - Ekkor átkerül a **hozzáférés engedélyezése** lapot. Válassza ki az Alkalmazásengedélyek és/vagy delegált engedélyeket az alkalmazásnak szüksége van, az API-hoz való hozzáféréskor.
   
  ![Egy alkalmazás regisztrálása - engedélyek frissítése api](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![Egy alkalmazás regisztrálása - engedélyek engedélyek frissítése](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. Ha elkészült, kattintson a **válassza** gombot **hozzáférés engedélyezése** lapon, majd a **kész** gombot a **API-hozzáférés hozzáadása** lap. A rendszer visszairányítja az **szükséges engedélyek** oldal, ahol az új erőforrás hozzáadódik az API-k listája.

  > [!NOTE]
  > Kattintson a **kész** gomb is automatikusan beállítja az engedélyeket az alkalmazáshoz a címtárban konfigurált egyéb alkalmazások engedélyei alapján. Ezek az Alkalmazásengedélyek tekintheti meg az alkalmazás Hibaoldal **beállítások** lapot.
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Webes API-k elérhetővé egy erőforrás-alkalmazás konfigurálása

Webes API-k fejlesztéséhez és elérhetővé az ügyfélalkalmazások számára teszi elérhetővé a hozzáférés [hatókörök](active-directory-dev-glossary.md#scopes) és [szerepkörök](active-directory-dev-glossary.md#roles). Egy megfelelően konfigurált webes API-hoz hasonlóan az egyéb Microsoft webes API-kat, beleértve a Graph API és az Office 365 API-k elérhetővé. A hozzáférési hatókörök és szerepkörök keresztül érhetők el a [alkalmazás jegyzékfájlját](active-directory-dev-glossary.md#application-manifest), amely egy JSON-fájlt, amely az alkalmazás identitását konfigurációs. 

A következő szakasz bemutatja, hogyan hozzáférési hatókörök, elérhetővé az erőforrás-alkalmazás jegyzékfájlja módosításával.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Az erőforrás-alkalmazás hozzáadása a hozzáférési hatókörök

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók lehetőséget biztosít több mint egy eléréséhez, kattintson a jobb felső sarokban a fiókjába, és állítsa be a portál munkamenet a kívánt Azure ad-bérlőben.

3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatást, kattintson a **alkalmazásregisztrációk**, majd keresse meg és kattintson a konfigurálni kívánt alkalmazás.

   ![Frissítse az alkalmazás regisztrálása](./media/active-directory-integrating-applications/update-app-registration.png)

4. Ekkor megnyílik az alkalmazás fő regisztrációs oldal, amely megnyílik a **beállítások** az alkalmazás lapját. Váltson a **Edit manifest** lapra kattintva **Manifest** az alkalmazás regisztrációs lapján. Megnyílik egy webalapú alkalmazásjegyzék-szerkesztőben, így **szerkesztése** a jegyzékfájlt a portálon. Másik lehetőségként kattinthat **letöltése** és helyi szerkesztéséhez, majd a **feltöltése** szeretne újból alkalmazni, az alkalmazásnak.

5. Ebben a példában fogja elérhetővé tesszük az új hatókör nevű `Employees.Read.All` az erőforrás/API, a következő JSON-elem való hozzáadásával a `oauth2Permissions` gyűjtemény. A meglévő `user_impersonation` hatókör alapértelmezés szerint biztosított regisztrációja során. `user_impersonation` lehetővé teszi, hogy egy ügyfélalkalmazás elérni az erőforrást, a bejelentkezett felhasználó identitása alatt engedélyt. Ügyeljen arra, hogy a vessző hozzáadása után a meglévő `user_impersonation` eleme hatókörét, és módosítani a tulajdonság értékét az erőforrás igényeinek megfelelően. 

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
  > Az "id" értéke kell létrejönnie, például a GUID Azonosítót. generációs eszköz használatával [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) vagy programozott módon. Egy egyedi azonosítót a hatókör azt jelöli, ahogy a webes API-k által elérhetővé tett. Miután egy ügyfél megfelelően van konfigurálva, a webes API elérésére jogosult, azt egy OAuth2.0-hozzáférési jogkivonatot az Azure AD által kiadott. Amikor az ügyfél hívásai a webes API bemutatja a hozzáférési jogkivonatot a hatókör (scp) rendelkező jogcímek készletéhez, az alkalmazás regisztrálása a kért engedélyeket.
  >
  > További hatókörökkel később szükség szerint tehetők közzé. Vegye figyelembe, hogy a webes API teheti ki a különböző funkciók különböző társított több hatókörhöz. Az erőforrás által hatókörének szabályozhatja a hozzáférést az futásidőben, a webes API-t (`scp`) a fogadott OAuth 2.0 hozzáférési jogkivonatban a jogcím.
  > 

6. Ha befejezte, kattintson a **mentése**. Most a webes API a címtár más alkalmazások használatra van konfigurálva. 

  ![Frissítse az alkalmazás regisztrálása](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Ellenőrizze a webes API-t közvetlenül csatlakozik a bérlőn belüli más alkalmazások
1. Lépjen vissza a az Azure AD-bérlőhöz, kattintson a **alkalmazásregisztrációk** újra, majd keresse meg és kattintson a konfigurálni kívánt ügyfélalkalmazás.

   ![Frissítse az alkalmazás regisztrálása](./media/active-directory-integrating-applications/update-app-registration.png)

2. 5. lépést megismételve a [ügyfélalkalmazás webes API-k eléréséhez konfigurálása](#configure-a-client-application-to-access-web-apis). Amikor juthat el a **API kiválasztása** lépés, keresse meg az erőforrás a keresőmezőbe az alkalmazás név beírásával, majd kattintson **kiválasztása**. 

3. Az a **hozzáférés engedélyezése** lapon kell megjelennie az új hatókör, elérhető-e engedéllyel az ügyféli kérelmek részére.

  ![Új engedélyek láthatók](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>További információ az manifestu aplikace

Az alkalmazásjegyzék ténylegesen az alkalmazás entitás, amely meghatározza egy Azure AD-alkalmazást identitás konfigurálására, beleértve az API a hozzáférési hatókörök már volt szó az összes attribútum frissítésére szolgáló mechanizmusként szolgál majd. Az alkalmazás entitást és a séma további információkért lásd: a [Graph API-alkalmazás entitás dokumentáció](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). A cikk tartalmazza az alkalmazás rendszerentitás-tagok az API-hoz, az engedélyek megadásához használja a teljes referenciája többek között:  

- A appRoles tagja, amely gyűjtemény, [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) entitások definiálásához használt [Alkalmazásengedélyek](active-directory-dev-glossary.md#permissions) webes API-hoz. 
- Az oauth2Permissions tag, amely gyűjtemény, [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) entitások definiálásához használt [delegált engedélyek](active-directory-dev-glossary.md#permissions) webes API-hoz.

További információ az application manifest általában a fogalmak, lásd: [az Azure Active Directory alkalmazásjegyzékének megismerése](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Az Azure AD Graph és az Office 365-höz a Microsoft Graph API-kon keresztül eléréséhez  

Ahogy korábban említettük, kívül is közzéteheti/API-kat az Ön saját alkalmazásait, az ügyfélalkalmazás által a Microsoft-erőforrások API-k elérése is regisztrálhat. A Microsoft Graph API nevén a "Microsoft Graph" erőforrás és az API listában a portálon, az Azure ad-vel regisztrált alkalmazások érhető el. Ha az ügyfélalkalmazás egy bérlő feliratkozott egy Office 365-előfizetéshez tartozó fiókokat tartalmazó regisztrál, a hatókörök által a különböző Office 365-erőforrások is elérheti.

Befejeződött a Microsoft Graph API által közzétett hatókörök, lásd: a [Microsoft Graph-engedélyek referencia](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference) cikk.

> [!NOTE]
> A jelenlegi korlátozás miatt natív ügyfélalkalmazások csak meghívhatja az Azure AD Graph API, ha azok a "Eléréséhez a munkahely címtárában" engedéllyel. Ez a korlátozás nem vonatkozik a webes alkalmazásokhoz.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Több-bérlős alkalmazások konfigurálása

Ha egy alkalmazás regisztrálása az Azure ad-ben, érdemes lehet az alkalmazás kizárólag a szervezet felhasználói számára legyen elérhető. Másik megoldásként is érdemes lehet az alkalmazás külső szervezetek által lesznek elérhetők. Ezek a típusok két alkalmazás egybérlős és több-bérlős alkalmazásokat nevezzük. Ez a szakasz bemutatja, hogyan lehet módosítani a konfigurációt, győződjön meg arról, hogy egy több-bérlős alkalmazás egy egybérlős alkalmazást.

Fontos megjegyezni, egy egybérlős és több-bérlős alkalmazás közötti különbségeket:  

- Egy egybérlős alkalmazást egy szervezeten belüli használatra szolgál. Fontos általában egy – üzletági (LoB) alkalmazás-nagyvállalati fejlesztő által írt. Egy egybérlős alkalmazást csak ugyanabban a bérlőben, az alkalmazás regisztrációs-fiókkal rendelkező felhasználók által érhető el. Ennek eredményeképpen csak kell egy címtárban ki kell építeni.
- Több-bérlős alkalmazás számos szervezet használatra szánt. Szoftver--szolgáltatásként (SaaS) webalkalmazásként említett, általában írás független szoftverszállító (ISV) szerint. Több-bérlős alkalmazások minden bérlő, ahol a felhasználóknak hozzá kell ki kell építeni. A bérlők számára, mint ahol az alkalmazás regisztrálva van a felhasználó vagy rendszergazda hozzájárulását szükség ahhoz, hogy regisztrálja őket. Vegye figyelembe, hogy natív ügyfélalkalmazások áll a több-bérlős alapértelmezés szerint az erőforrás tulajdonosa eszköz telepítik őket. Lásd az előző [a hozzájárulási keretrendszer áttekintése](#overview-of-the-consent-framework) a hozzájárulási keretrendszer a részleteket a következő szakaszban.

Így egy alkalmazás több-bérlős mindkét alkalmazás regisztrációs módosítását igényli az is, mint maga a webalkalmazás módosításait. A következő szakaszok is foglalkozik.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Az alkalmazás regisztrációját, hogy támogatja a több-bérlős módosítása

Ha egy alkalmazás, amelyet szeretne elérhetővé tenni az ügyfelek vagy partnerek a cégen kívüli kell írnia, frissítenie a felügyeltalkalmazás-definíció az Azure Portalon.

> [!IMPORTANT]
> Az Azure AD szükséges több-bérlős alkalmazások globálisan egyedinek kell lennie az Alkalmazásazonosító URI. Az Alkalmazásazonosító URI az az alkalmazás a protokollüzenetekről azonosítja módszerekkel egyik. Egybérlős alkalmazás esetén a rendszer megfelelő az Alkalmazásazonosító URI a bérlőn belül egyedinek kell lennie. Több-bérlős alkalmazás esetében, globálisan egyedinek kell lennie, az Azure ad-ben található az alkalmazás az összes bérlőre kiterjedő. Globális egyedi-e kényszerítve van azzal, hogy az Alkalmazásazonosító URI-t szeretné, hogy egy állomásnevet, amely megfelel az Azure AD-bérlő ellenőrzött tartományt. Például, ha a bérlő nevével contoso.onmicrosoft.com, akkor egy érvényes App ID URI lenne https://contoso.onmicrosoft.com/myapp. A bérlőnek rendelkeznie kell egy ellenőrzött tartomány contoso.com, akkor egy érvényes Alkalmazásazonosító URI-t is lenne https://contoso.com/myapp. Ha az Alkalmazásazonosító URI nem követi a ebben a mintában beállítása egy alkalmazást, több-bérlős sikertelen lesz.
> 

Biztosíthat a külső felhasználók elérhetik az alkalmazást: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók lehetőséget biztosít több mint egy eléréséhez, kattintson a jobb felső sarokban a fiókjába, és állítsa be a portál munkamenet a kívánt Azure ad-bérlőben.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatást, kattintson a **alkalmazásregisztrációk**, majd keresse meg és kattintson a konfigurálni kívánt alkalmazás. Ekkor megnyílik az alkalmazás fő regisztrációs oldal, amely megnyílik a **beállítások** az alkalmazás lapját.
4. Az a **beállítások** kattintson **tulajdonságok** , és módosítsa a **több-bérlős** váltson **Igen**.

Miután elvégezte a módosításokat, a felhasználók és más szervezetek a rendszergazdák képesek jelentkezzen be az alkalmazás lehetővé teszi a felhasználók megadását lehetővé teszi az alkalmazás a bérlő által védett erőforrások eléréséhez.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Az alkalmazás támogatja a több-bérlős módosítása

Több-bérlős alkalmazások támogatása az Azure ad-ben hozzájárulási keretrendszer erősen támaszkodik. A beleegyezés kinyilvánítása annak a mechanizmus, amely lehetővé teszi a felhasználóknak az alkalmazás a felhasználó bérlője által védett erőforrásokhoz való hozzáférést egy másik bérlőben. Ez a tapasztalat nevezik "felhasználói beleegyezés."

Előfordulhat, hogy a zabalení webové aplikace is kínál.

- Lehetővé teszi a rendszergazdák "vállalatom regisztráció." Ez a tapasztalat, "rendszergazdai jóváhagyás", a továbbiakban teszi a rendszergazda a nevében a hozzájárulás *minden felhasználó* a szervezetben. Csak olyan végzi a hitelesítést egy olyan fiókkal, a globális rendszergazdai szerepkörhöz tartozó felhasználó rendszergazdai jóváhagyás; tud biztosítani. mások hibaüzenetet kap.

- A bejelentkezési élmény a felhasználók számára. Valószínű, hogy a felhasználó a "regisztráció" gombra kattintva a böngésző átirányítja a az Azure AD OAuth2.0 adott `/authorize` végpontot, illetve az OpenID Connect `/userinfo` végpont. Itt engedélyezheti az alkalmazásnak, információt szeretne kapni az új felhasználót a id_token vizsgálatával szerezheti be ezeket a végpontokat. Az előfizetési fázis, a felhasználó jóváhagyásukat kéri, ahogyan hasonló egyike a következő a [a hozzájárulási keretrendszer áttekintése](#overview-of-the-consent-framework) szakaszban.

A több-bérlős hozzáférési és bejelentkezési-a vagy a regisztráláshoz élmény támogatásához szükséges alkalmazások módosítására vonatkozó további információkért lásd:

- [Az Azure Active Directory (AD) felhasználóinak bejelentkeztetése több-bérlős alkalmazásminta használatával](active-directory-devhowto-multi-tenant-overview.md)
- Listájának [több-bérlős Kódminták](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Gyors útmutató: Adja hozzá a vállalati arculat megjelenítése a bejelentkezési oldal az Azure ad-ben](../fundamentals/customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Engedélyezés az OAuth 2.0 implicit adja meg egyetlen oldal alkalmazásokhoz

Egyetlen lap alkalmazás (gyógyfürdők) általában struktúrája JavaScript műveltekből előtér, amely a böngészőben, amely meghívja az alkalmazás webes API-t az üzleti logika végrehajtására háttérbeli futtatja. Az Azure AD-ben üzemeltetett gyógyfürdők, OAuth 2.0 típusú Implicit engedélyezés hitelesíteni a felhasználót az Azure ad-vel és használhat, amely segítségével biztonságos hívása a háttér-webes API-t az alkalmazás JavaScript ügyfélről jogkivonat beszerzése. 

Miután a felhasználó számára engedélyezett a jóváhagyás, ez ugyanazt a hitelesítési protokollt használható API-erőforrásokhoz az alkalmazás konfigurálva az ügyfél és a más webkiszolgáló közötti hívások biztonságossá tételéhez tokenek beszerzése érdekében. Tudjon meg többet az implicit engedélyezés megadásáról, és segít eldönteni, hogy ez az alkalmazás forgatókönyvhöz jobb: [OAuth2 implicit ismertetése adja meg az Azure Active Directoryban folyamat](active-directory-dev-understanding-oauth2-implicit-grant.md).

OAuth 2.0 típusú implicit engedélyezés alkalmazások alapértelmezés szerint le van tiltva. Állítsa az alkalmazás OAuth 2.0 típusú Implicit engedélyezés is engedélyeznie a `oauth2AllowImplicitFlow` értéket annak [alkalmazásjegyzék](active-directory-application-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>OAuth 2.0 típusú implicit engedélyezés engedélyezése

> [!NOTE]
> Megtudhatja, hogyan szerkesztheti az alkalmazásjegyzékben, akkor először tekintse át az előző szakaszban [elérhetővé egy erőforrás-alkalmazás konfigurálása a webes API-kat](#configuring-a-resource-application-to-expose-web-apis).
>

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók lehetőséget biztosít több mint egy eléréséhez, kattintson a jobb felső sarokban a fiókjába, és állítsa be a portál munkamenet a kívánt Azure ad-bérlőben.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatást, kattintson a **alkalmazásregisztrációk**, majd keresse meg és kattintson a konfigurálni kívánt alkalmazás. Ekkor megnyílik az alkalmazás fő regisztrációs oldal, amely megnyílik a **beállítások** az alkalmazás lapját.
4. Váltson a **Edit manifest** lapra kattintva **Manifest** az alkalmazás regisztrációs lapján. Megnyílik egy webalapú alkalmazásjegyzék-szerkesztőben, így **szerkesztése** a jegyzékfájlt a portálon. Keresse meg és állítsa a "oauth2AllowImplicitFlow" érték "true". Alapértelmezés szerint "false"értékre. van beállítva
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Mentse a frissített jegyzékfájl. A mentés után a webes API konfigurálva van az OAuth 2.0 típusú Implicit engedélyezés segítségével hitelesítheti a felhasználókat.

## <a name="removing-an-application"></a>Alkalmazás eltávolítása
Ez a szakasz ismerteti, hogyan távolíthat el egy alkalmazás regisztrálása az az Azure AD-bérlő.

### <a name="removing-an-application-authored-by-your-organization"></a>A szervezet által létrehozott alkalmazás eltávolítása
A szervezet által regisztrált alkalmazások jelennek meg a "Saját alkalmazások" szűrő a bérlő fő "alkalmazásregisztrációk" oldalon. Ezek az alkalmazások azok meg manuálisan regisztrálni az Azure Portalon vagy programozott módon PowerShell vagy a Graph API-n keresztül. Pontosabban azok szerepelnek mindkét alkalmazás- és szolgáltatásnév objektumok által a bérlőben. További információkért lásd: [alkalmazásobjektumok és egyszerű szolgáltatási objektumok](active-directory-application-objects.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Egybérlős alkalmazás eltávolítása a címtárból
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók lehetőséget biztosít több mint egy eléréséhez, kattintson a jobb felső sarokban a fiókjába, és állítsa be a portál munkamenet a kívánt Azure ad-bérlőben.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatást, kattintson a **alkalmazásregisztrációk**, majd keresse meg és kattintson a konfigurálni kívánt alkalmazás. Ekkor megnyílik az alkalmazás fő regisztrációs oldal, amely megnyílik a **beállítások** az alkalmazás lapját.
4. Az alkalmazás fő regisztrációs oldalon kattintson **törlése**.
5. Kattintson a **Igen** a megerősítő üzenetben.

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Több-bérlős alkalmazás eltávolítása a kezdőkönyvtár
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha a fiók lehetőséget biztosít több mint egy eléréséhez, kattintson a jobb felső sarokban a fiókjába, és állítsa be a portál munkamenet a kívánt Azure ad-bérlőben.
3. A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directory** szolgáltatást, kattintson a **alkalmazásregisztrációk**, majd keresse meg és kattintson a konfigurálni kívánt alkalmazás. Ekkor megnyílik az alkalmazás fő regisztrációs oldal, amely megnyílik a **beállítások** az alkalmazás lapját.
4. Az a **beállítások** lapon a **tulajdonságok** , és módosítsa a **több-bérlős** váltson **nem**, először a az alkalmazás módosítása egybérlős, majd kattintson a **mentése**. Az alkalmazás egyszerű szolgáltatási objektumok minden olyan szervezetnek, amely már hozzájárult, hogy ez az a bérlői maradnak.
5. Kattintson a **törlése** gombot az alkalmazás fő való regisztrációhoz.
6. Kattintson a **Igen** a megerősítő üzenetben.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Egy másik szervezet által felhatalmazott több-bérlős alkalmazás eltávolítása
Amely a "Minden alkalmazás" szűrő meg fog jelenni az alkalmazások egy része (kivéve a "Saját alkalmazások" regisztrációk) oldalon a bérlő fő "Alkalmazásregisztrációk", több-bérlős alkalmazások vannak. Technikai szempontból a több-bérlős alkalmazások más bérlők, és a jóváhagyási folyamat során regisztráltak a bérlőbe. Pontosabban azok képviseli csak egy szolgáltatásnév-objektumot a bérlőben a nem megfelelő alkalmazás-objektum. Az alkalmazás és egyszerű szolgáltatási objektumok közötti különbségekkel kapcsolatos további információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure AD-ben](active-directory-application-objects.md).

Annak érdekében, hogy távolítsa el egy több-bérlős alkalmazás-hozzáférés a címtárhoz (után megadó hozzájárulás), a vállalati rendszergazda el kell távolítania az egyszerű szolgáltatás. A rendszergazda kell globális rendszergazdai hozzáféréssel rendelkezik, és távolítsa el az Azure Portalon keresztül vagy használja a [Azure AD PowerShell-parancsmagok](http://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>További lépések
- Az Azure AD-hitelesítés működéséről további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-ben](authentication-scenarios.md).
- Tekintse meg a [védjegyzési irányelvek integrált alkalmazások](active-directory-branding-guidelines.md) amely tippekkel szolgál az alkalmazás visual útmutatást.
- Egy alkalmazás alkalmazás- és szolgáltatásnév objektumok közötti kapcsolatot további információkért lásd: [alkalmazásobjektumok és egyszerű szolgáltatási objektumok](active-directory-application-objects.md).
- További információ a szerepkört az alkalmazás jegyzékfájl fontos szerepet játszik, lásd: [az Azure Active Directory alkalmazásjegyzékének megismerése](active-directory-application-manifest.md)
- Tekintse meg a [Azure AD fejlesztői szószedetét](active-directory-dev-glossary.md) néhány, az Azure AD fejlesztői alapfogalmakat definíciói.
- Látogasson el a [Active Directory fejlesztői útmutatója](azure-ad-developers-guide.md) áttekintheti az összes fejlesztői kapcsolódó tartalmat.

