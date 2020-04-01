---
title: Azure AD-hitelesítés konfigurálása
description: Ismerje meg, hogyan konfigurálhatja az Azure Active Directory-hitelesítést identitásszolgáltatóként az App Service vagy az Azure Functions alkalmazáshoz.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 4b42f0966288e4ee72b689ddce6313a41e91f13e
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438034"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Az App Service vagy az Azure Functions alkalmazás konfigurálása az Azure AD bejelentkezési adatainak használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure App Service vagy az Azure Functions az Azure Active Directory (Azure AD) hitelesítésszolgáltatóként való használatát.

> [!NOTE]
> Jelenleg az [Azure Active Directory v2.0-s (beleértve](../active-directory/develop/v2-overview.md) az [MSAL-t](../active-directory/develop/msal-overview.md)is) nem támogatott az Azure App Service és az Azure Functions. Kérjük, látogasson vissza a frissítéseket.
>

Kövesse az alábbi gyakorlati tanácsokat az alkalmazás és a hitelesítés beállításakor:

- Adjon meg minden Egyes App Service-alkalmazásnak saját engedélyeket és beleegyezést.
- Konfigurálja az egyes App Service-alkalmazásoksaját regisztrációval.
- Kerülje az engedélyek megosztását a környezetek között, ha külön alkalmazásregisztrációkat használ a különálló központi telepítési helyekhez. Az új kód tesztelése során ez a gyakorlat segíthet megakadályozni, hogy az éles alkalmazást érintő problémák.

## <a name="configure-with-express-settings"></a><a name="express"> </a>Konfigurálás expressz beállításokkal

> [!NOTE]
> Az **Expressz** beállítás kormányzati felhők esetén nem érhető el. 

1. Az [Azure Portalon]keresse meg és válassza az **App Services**lehetőséget, majd válassza ki az alkalmazást.
2. A bal oldali navigációs sávon válassza a **Hitelesítés / Engedélyezés** > **bekapcsolva**lehetőséget.
3. Válassza az **Azure Active Directory** > **Express**lehetőséget.

   Ha egy meglévő alkalmazásregisztrációt szeretne választani:

   1. Válassza **a Meglévő AD-alkalmazás kijelölése**lehetőséget, majd kattintson **az Azure AD-alkalmazás**elemre.
   2. Válasszon egy meglévő alkalmazásregisztrációt, és kattintson **az OK gombra.**

3. Válassza **az OK lehetőséget** az App Service-alkalmazás regisztrálásához az Azure Active Directoryban. Új alkalmazásregisztráció jön létre.
   
    ![Expressz beállítások az Azure Active Directoryban](./media/configure-authentication-provider-aad/express-settings.png)
   
4. (Nem kötelező) Alapértelmezés szerint az App Service hitelesítést biztosít, de nem korlátozza az engedélyezett hozzáférést a webhely tartalmához és API-khoz. Az alkalmazáskódban engedélyeznie kell a felhasználókat. Ha csak az Azure Active Directory által hitelesített felhasználókra szeretné korlátozni az alkalmazáshoz való hozzáférést, állítsa be a **Művelet et, ha a kérés nincs hitelesítve az** **Azure Active Directoryval való bejelentkezéshez.** Ha ezt a funkciót állítja be, az alkalmazás minden kérelem hitelesítését igényli. Azt is átirányítja az összes nem hitelesített az Azure Active Directory hitelesítéshez.

    > [!CAUTION]
    > A hozzáférés ily módon történő korlátozása az alkalmazás minden hívására vonatkozik, ami nem feltétlenül kívánatos a nyilvánosan elérhető kezdőlappal rendelkező alkalmazások esetében, mint sok egyoldalas alkalmazásban. Az ilyen alkalmazások esetében előfordulhat, **hogy a névtelen kérelmek engedélyezése (nincs művelet)** előnyben részesítésen, az alkalmazás manuálisan indítja el magát a bejelentkezést. További információt a [Hitelesítési folyamat című témakörben talál.](overview-authentication-authorization.md#authentication-flow)
5. Kattintson a **Mentés** gombra.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Konfigurálás speciális beállításokkal

Manuálisan konfigurálhatja az alkalmazásbeállításokat, ha egy másik Azure AD-bérlőalkalmazás-regisztrációt szeretne használni. Az egyéni konfiguráció befejezéséhez:

1. Hozzon létre egy regisztrációt az Azure AD-ben.
2. Adja meg a regisztrációs adatokat az App Service-nek.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Alkalmazásregisztráció létrehozása az Azure AD-ben az App Service-alkalmazáshoz

Az App Service-alkalmazás konfigurálásakor a következő adatokra lesz szüksége:

- Ügyfél-azonosító
- Bérlőazonosító
- Ügyféltitok (nem kötelező)
- Alkalmazásazonosító URI

Hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalon,]keresse meg és válassza az **App Services**lehetőséget, majd válassza ki az alkalmazást. Jegyezze fel az alkalmazás **URL-címét.** Az Azure Active Directory alkalmazás regisztrációjának konfigurálásához fogja használni.
1. Válassza az **Azure Active Directory** > **alkalmazás regisztrációit** > **Új regisztráció.**
1. A **Jelentkezés regisztrálása** lapon adja meg az alkalmazás **regisztrációjának nevét.**
1. Az **Átirányítás URI-ban** `<app-url>/.auth/login/aad/callback`válassza a **Web** és a Type lehetőséget. Például: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Kattintson a **Létrehozás** gombra.
1. Az alkalmazás regisztrációja létrehozása után másolja az **alkalmazás (ügyfél) azonosítóját** és a **címtár (bérlői) azonosítóját** későbbi használatra.
1. Válassza a **Hitelesítés** lehetőséget. Az **Implicit támogatás**csoportban engedélyezze az **azonosítójogkivonatokat,** hogy engedélyezze az OpenID Connect felhasználói bejelentkezéseket az App Service-ből.
1. (Nem kötelező) Válassza a **Márkajelzés lehetőséget.** A **kezdőlap URL-címében**adja meg az App Service-alkalmazás URL-címét, és válassza a **Mentés**lehetőséget.
1. Válassza **az API-készlet** > felfedése**lehetőséget.** Illessze be az App Service-alkalmazás URL-címét, és válassza a **Mentés gombot.**

   > [!NOTE]
   > Ez az érték az alkalmazásregisztráció **alkalmazásazonosító-URI-ja.** Ha a webalkalmazás hozzáférést igényel egy API-hoz a felhőben, a felhőbeli App Service-erőforrás konfigurálásakor szüksége van a webalkalmazás **alkalmazásazonosító-URI-jára.** Ezt például akkor használhatja, ha azt szeretné, hogy a felhőszolgáltatás explicit módon biztosítson hozzáférést a webalkalmazáshoz.

1. Válassza a **Hatókör hozzáadása** lehetőséget.
   1. A **Hatókör neve**mezőbe írja be *user_impersonation.*
   1. A szövegmezőkben adja meg azt a jóváhagyási hatókör nevét és leírását, amelyet a felhasználóknak látniuk kell a jóváhagyási lapon. Írja be például *az Access az alkalmazásomat.* 
   1. Válassza **a Hatókör hozzáadása**lehetőséget.
1. (Nem kötelező) Ügyféltitok létrehozásához jelölje be **a Tanúsítványok & titkos kulcsok** > **Új ügyféltitok** > **hozzáadása lehetőséget.** Másolja a lapon megjelenő titkos ügyfélértéket. Nem jelenik meg újra.
1. (Nem kötelező) Ha több **válasz URL-t**szeretne hozzáadni, válassza a **Hitelesítés**lehetőséget.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Az Azure Active Directory engedélyezése az App Service-alkalmazásban

1. Az [Azure Portalon]keresse meg és válassza az **App Services**lehetőséget, majd válassza ki az alkalmazást. 
1. A bal oldali ablaktáblában a **Beállítások**csoportban válassza a **Hitelesítés / Engedélyezés** > **bekapcsolva**lehetőséget.
1. (Nem kötelező) Alapértelmezés szerint az App Service-hitelesítés nem hitelesített hozzáférést biztosít az alkalmazáshoz. A felhasználói hitelesítés kényszerítéséhez állítsa be **a műveletet, ha a kérelem nincs hitelesítve az** **Azure Active Directoryval való bejelentkezéshez.**
1. A **Hitelesítésszolgáltatók csoportban**válassza az **Azure Active Directory**lehetőséget.
1. **Felügyeleti módban**válassza **a Speciális** lehetőséget, és konfigurálja az App Service-hitelesítést az alábbi táblázat szerint:

    |Mező|Leírás|
    |-|-|
    |Ügyfél-azonosító| Használja az **alkalmazásregisztráció alkalmazásazonosítóját (ügyfélazonosítóját).** |
    |Kiállító url-címe| Használja `https://login.microsoftonline.com/<tenant-id>`a , és cserélje le * \<a bérlői azonosító>* az alkalmazásregisztráció **címtár (bérlői) azonosítójára.** Ez az érték a felhasználók átirányítására a megfelelő Azure AD-bérlő, valamint a megfelelő metaadatok letöltéséhez a megfelelő jogkivonat-aláíró kulcsok és jogkivonat-kibocsátó jogcímértéke például. |
    |Ügyféltitok (nem kötelező)| Használja az alkalmazásregisztrációsorán létrehozott ügyféltitkot.|
    |Engedélyezett tokenközönségek| Ha felhőalapú vagy kiszolgálóalkalmazásról van szó, és egy webalkalmazásból szeretné engedélyezni a hitelesítési jogkivonatokat, adja hozzá a webalkalmazás **alkalmazásazonosító-URI-ját.** A konfigurált **ügyfélazonosító** *t mindig* hallgatólagosan engedélyezett célközönségnek tekinti. |

2. Válassza **az OK**gombot, majd a Mentés **gombot.**

Most már készen áll az Azure Active Directory használatára az App Service-alkalmazásban történő hitelesítéshez.

## <a name="configure-a-native-client-application"></a>Natív ügyfélalkalmazás konfigurálása

A natív ügyfelek regisztrálhatják a hitelesítést az alkalmazásban tárolt webes API-k számára egy ügyféltár, például az **Active Directory hitelesítési könyvtár**használatával.

1. Az [Azure Portalon]válassza az **Active Directory** > **alkalmazás regisztrációk** > **Új regisztráció**lehetőséget.
1. A **Jelentkezés regisztrálása** lapon adja meg az alkalmazás **regisztrációjának nevét.**
1. Az **Átirányítás URI-ban**válassza a Nyilvános ügyfél `<app-url>/.auth/login/aad/callback` **(mobil & asztali)** lehetőséget, és írja be az URL-címet . Például: `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Microsoft Store-alkalmazások esetén használja a [csomag SID-jét](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) URI-ként.
1. Kattintson a **Létrehozás** gombra.
1. Az alkalmazás regisztrációja létrehozása után másolja az **alkalmazás (ügyfél) azonosítóértékét.**
1. Válassza az **API-engedélyek** > **kiválasztása Engedély hozzáadása** > **SAJÁT API-k**hozzáadása .
1. Válassza ki az App Service-alkalmazáshoz korábban létrehozott alkalmazásregisztrációt. Ha nem látja az alkalmazás regisztrációját, győződjön meg arról, hogy hozzáadta a **user_impersonation** hatókört az [Alkalmazásregisztráció létrehozása az Azure AD-ben az App Service-alkalmazáshoz](#register)című témakörben.
1. Jelölje be **user_impersonation**, majd az **Engedélyek hozzáadása**lehetőséget.

Most konfigurált egy natív ügyfélalkalmazást, amely egy felhasználó nevében hozzáférhet az App Service-alkalmazáshoz.

## <a name="next-steps"></a><a name="related-content"> </a>További lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure-portál]: https://portal.azure.com/
