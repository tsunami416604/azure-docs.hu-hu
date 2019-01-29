---
title: Felhasználói áttelepítése megközelítést alkalmaz az Azure Active Directory B2C |} A Microsoft Docs
description: Bemutatjuk a core és a speciális fogalmakat a Graph API-val, és igény szerint az Azure AD B2C-vel egyéni szabályzatok használatával felhasználói áttelepítése.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5a168ca3aafc171e4ed9b9f7572ee60b2ac7c350
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182268"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Felhasználói áttelepítése
Az Azure Active Directory B2C az identitásszolgáltató áttelepítéskor (Azure AD B2C-vel), szükség lehet a felhasználói fiók áttelepítéséhez. Ez a cikk bemutatja, hogyan telepítheti át meglévő felhasználói fiókok bármely identitásszolgáltatótól az Azure AD B2C-vel. A cikk nem szinkronban kell lennie az előírásoknak megfelelő, de néhány forgatókönyv bemutatja. A fejlesztő feladata mindkét megközelítés megfelelőségét.

## <a name="user-migration-flows"></a>Áttelepítési felhasználókövetési adatai
Az Azure AD B2C keresztül a felhasználó telepíthet át [Azure AD Graph API][B2C-GraphQuickStart]. A felhasználó áttelepítési folyamat két folyamatok jogszerűnek:

- **Az áttelepítés előtti**: Ez a folyamat vonatkozik, vagy törölje hozzáférés egy felhasználó hitelesítő adatait (felhasználónév és jelszó) vagy a hitelesítő adatok titkosítottak, de képes visszafejteni azokat. Az áttelepítést megelőző folyamatba beletartozik az olvasó a felhasználók a régi identitásszolgáltatótól, és új fiókok létrehozása az Azure AD B2C-címtárát a.

- **Az áttelepítés előtti és a jelszó-visszaállítás**: Ez a folyamat vonatkozik, amikor egy felhasználó jelszavát nem érhető el. Példa:
   - A jelszót a JELSZÓKIVONAT-formátumban tárolódik.
   - A jelszó, amelyek nem érhetők el egy identitásszolgáltatótól tárolja. A régi identitásszolgáltató webszolgáltatások hívásával a felhasználói hitelesítő adatok ellenőrzése.

Mindkét folyamatokban először futtatja az áttelepítést megelőző folyamatba, olvassa el a felhasználók a régi identitásszolgáltatótól származó, és új fiók létrehozása az Azure AD B2C-címtárát a. Ha nem rendelkezik a jelszót, a rendszer véletlenszerűen generált jelszó használatával létrehozni a fiókot. Majd kérje meg a felhasználó módosíthatja a jelszavát, vagy amikor a felhasználó első alkalommal bejelentkezik, az Azure AD B2C-vel kéri a felhasználót, hogy állítsa alaphelyzetbe.

## <a name="password-policy"></a>Jelszószabályzat
Az Azure AD B2C-vel jelszóházirend (helyi fiókok esetében) az Azure AD-házirend alapján történik. Az Azure AD B2C regisztrálási vagy bejelentkezési és a jelszó alaphelyzetbe állítása a házirendek használja "erős" jelszó erőssége, és nem jár le a jelszavakat. További információkért lásd: [az Azure AD-jelszóházirendet][AD-PasswordPolicies].

Ha a fiókok, amelyeket szeretné áttelepíteni használja, mint egy gyengébb jelszó erőssége a [kényszeríti ki az Azure AD B2C-vel erős jelszó erőssége][AD-PasswordPolicies], letilthatja az erős jelszó követelmény. Az alapértelmezett jelszó szabályzat módosításához állítsa a `passwordPolicies` tulajdonságot `DisableStrongPassword`. Ha például a következő módosíthatja a felhasználói kérés:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>1. lépés: Felhasználók migrálása az Azure AD Graph API használatával
Graph API-n keresztül az Azure AD B2C felhasználói fiókot hoz létre, (jelszóval vagy véletlenszerű jelszót). Ez a szakasz ismerteti a folyamatot a Graph API-val az Azure AD B2C-címtár felhasználói fiókokat hozhat létre.

### <a name="step-11-register-your-application-in-your-tenant"></a>1.1. lépés: A bérlő az alkalmazás regisztrálása
A Graph API-val folytatott kommunikációhoz, először kell rendelkeznie a szolgáltatás-rendszergazdai jogosultsággal rendelkező fiókot. Az Azure AD-ben regisztrál egy alkalmazás és az Azure AD-hitelesítés. Az alkalmazás hitelesítő adatok **Alkalmazásazonosító** és **Application Secret**. Az alkalmazás maga nem felhasználóként, a Graph API meghívása funkcionál.

Először regisztrálja az áttelepítés alkalmazás Azure AD-ben. Ezután hozzon létre egy alkalmazáskulcsot (Alkalmazáskulcs), és állítsa be az alkalmazás az írási jogosultsággal rendelkező.

1. Jelentkezzen be az [Azure Portalra][Portal].
   
1. Válassza ki az Azure AD- **B2C** bérlő, a fiókra az oldal tetején, az ablak jobb.
   
1. A bal oldali panelen válassza ki a **Azure Active Directory** (nem Azure AD B2C-vel). Találja, akkor előfordulhat, hogy kell választania **további szolgáltatások**.
   
1. Válassza az **Alkalmazásregisztrációk** elemet.
   
1. Válassza az **Új alkalmazás regisztrálása** elemet.
   
   ![Új alkalmazásregisztráció](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
   
1. Új alkalmazás létrehozásához az alábbiak szerint:
   - A **neve**, használjon **B2CUserMigration** vagy bármilyen más nevet.
   - A **alkalmazástípus**, használjon **Web app és az API**.
   - A **bejelentkezési URL-**, használjon **https://localhost** (mert nem megfelelő ehhez az alkalmazáshoz).
   - Kattintson a **Létrehozás** gombra.
   
1. Az alkalmazás létrehozása után a a **alkalmazások** listájához, válassza ki az újonnan létrehozott **B2CUserMigration** alkalmazás.
   
1. Válassza ki **tulajdonságok**, másolása a **Alkalmazásazonosító**, és mentse későbbi használatra.

### <a name="step-12-create-the-application-secret"></a>1.2. lépés: Az alkalmazás titkos kód létrehozása
1. Az Azure Portalon **regisztrált alkalmazás** ablakban válassza **kulcsok**.
   
1. Adjon hozzá egy új kulcsot (más néven ügyfélkódot), és másolja a kulcsot a későbbi használatra.
   
   ![Alkalmazás-azonosító és -kulcsok](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)
   
### <a name="step-13-grant-administrative-permission-to-your-application"></a>1.3. lépés: Az alkalmazás rendszergazdai engedély megadása
1. Az Azure Portalon **regisztrált alkalmazás** ablakban válassza **szükséges engedélyek**.

1. Válassza ki **Windows Azure Active Directory**.
   
1. Az a **hozzáférés engedélyezése** panel alatt **Alkalmazásengedélyek**, jelölje be **címtáradatok olvasása és írása**, majd válassza ki **mentése**.
   
1. Az a **szükséges engedélyek** ablaktáblán válassza előbb **engedélyek megadása**.
   
   ![Alkalmazásengedélyek](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)
   
Most már van egy alkalmazása, az engedélyek létrehozása, olvasása és frissítése az Azure AD B2C-bérlő felhasználóit.

### <a name="step-14-optional-environment-cleanup"></a>1.4 lépést: (Nem kötelező) Környezet törlése
Olvasási és írási könyvtárengedélyek data do *nem* a jogot arra, hogy törli a felhasználókat tartalmazzák. Biztosíthat az alkalmazás a felhasználók (a környezet tisztítása) törli, egy extra lépés, amely magában foglalja a PowerShell beállítása a felhasználói fiók rendszergazdai jogosultságokkal fut kell végrehajtania. Ellenkező esetben kihagyhatja a következő szakaszra.

> [!IMPORTANT]
> A B2C bérlő rendszergazdai fiók, amely kell használnia *helyi* a B2C-bérlőre. A fiók nevének szintaxisa *admin@contosob2c.onmicrosoft.com*.

>[!NOTE]
> A következő PowerShell-parancsfájl igényel [Azure Active Directory PowerShell 2-es verzió][AD-Powershell].

A PowerShell-parancsprogram tegye a következőket:
1. Az online szolgáltatáshoz csatlakozhat. Ehhez futtassa a `Connect-AzureAD` parancsmag a Windows PowerShell parancssort, és adja meg a hitelesítő adatokat.
   
1. Használja a **Alkalmazásazonosító** az alkalmazás a felhasználói fiók rendszergazdai szerepkör hozzárendelése. Ezek a szerepkörök rendelkeznek a jól ismert azonosítók, így ehhez szüksége, adja meg a **Alkalmazásazonosító** a szkriptben.
   
```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Módosítsa a `$AppId` érték az Azure AD-vel **Alkalmazásazonosító**.

## <a name="step-2-pre-migration-application-sample"></a>2. lépés: Az áttelepítés előtti alkalmazás minta
[Töltse le és futtassa a mintakód][UserMigrationSample]. Letöltheti a .zip-fájlként.

### <a name="step-21-edit-the-migration-data-file"></a>2.1. lépés: Az áttelepítési adatfájl szerkesztése
A mintaalkalmazás próbafelhasználó adatokat tartalmazó JSON-fájlt használ. A minta sikeres futtatása után módosíthatja a kódot, és a saját adatbázis felhasználását. Vagy a felhasználói profil exportálása egy JSON-fájlt, és állítsa az alkalmazásnak, hogy ezt a fájlt használja.

A JSON-fájl szerkesztéséhez nyissa meg a `AADB2C.UserMigration.sln` Visual Studio-megoldás. Az a `AADB2C.UserMigration` projektben nyissa meg a `UsersData.json` fájlt.

![Felhasználói adatok fájl](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Amint láthatja, a fájl felhasználói entitások listáját tartalmazza. Minden egyes felhasználó típusú entitás a következő tulajdonságokkal rendelkezik:
- e-mail
- displayName
- Keresztnév
- Vezetéknév
- jelszó (üres is lehet)

> [!NOTE]
> A fordítás során, a Visual Studio másolja át a fájlt a `bin` könyvtár.

### <a name="step-22-configure-the-application-settings"></a>2.2. lépés: Az Alkalmazásbeállítások konfigurálása
Alatt a `AADB2C.UserMigration` projektben nyissa meg a *App.config* fájlt. Cserélje le a saját értékeit a következő beállításokkal:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - Egy Azure-tábla kapcsolati karakterlánc használata a következő szakaszban ismertetjük.
> - A B2C bérlő neve a tartományhoz, amely során a bérlő létrehozásakor megadott, és megjelenik az Azure Portalon. A bérlő neve általában utótaggal végződik *. onmicrosoft.com* (például *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>2.3. lépés: Az áttelepítés előtti folyamat futtatása
Kattintson a jobb gombbal a `AADB2C.UserMigration` megoldást, és ezután készítse el a mintát. Ha sikeres, most már egy `UserMigration.exe` található végrehajtható fájl `AADB2C.UserMigration\bin\Debug\net461`. Az áttelepítési folyamat futtatásához használja a következő parancssori paraméterek egyikét:

- A **jelszóval rendelkező felhasználók migrálása**, használja a `UserMigration.exe 1` parancsot.

- A **véletlenszerű jelszóval rendelkező felhasználók migrálása**, használja a `UserMigration.exe 2` parancsot. Ez a művelet létrehoz egy Azure-tábla entitás is. Később a REST API-szolgáltatás meghívására a házirend konfigurálása. A szolgáltatás egy Azure-tábla nyomon követheti és kezelheti az áttelepítési folyamat használja.

![Áttelepítési folyamat bemutató](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>2.4. lépés: Az áttelepítés előtti folyamat ellenőrzése
Az áttelepítés ellenőrzése, használja az alábbi két módszer egyikét:

- Keresse meg a felhasználó megjelenített név alapján, használja az Azure Portalon:
   
   1. Nyissa meg **Azure AD B2C-vel**, majd válassza ki **felhasználók és csoportok**.
   
   1. A Keresés mezőbe írja be a felhasználó megjelenített neve, és tekintse meg a felhasználó profilját.
   
- Egy felhasználó lekérdezheti a bejelentkezési e-mail-címe, ez a mintaalkalmazás használja:
   
   1. Futtassa az alábbi parancsot:
   
      ```Console
          UserMigration.exe 3 {email address}
      ```
      
      > [!TIP]
      > Is lekérhet egy felhasználó által megjelenített neve a következő paranccsal: `UserMigration.exe 4 "<Display name>"`.
      
   1. Nyissa meg a UserProfile.json fájlt egy JSON-szerkesztő felhasználó adatainak megtekintéséhez.
   
      ![The UserProfile.json file](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)
      
### <a name="step-25-optional-environment-cleanup"></a>2.5. lépés: (Nem kötelező) Környezet törlése
Ha törölni szeretne beállítása az Azure AD-bérlőhöz, és távolítsa el a felhasználókat az Azure AD-címtárban, futtassa a `UserMigration.exe 5` parancsot.

> [!NOTE]
> * A bérlő törlése, konfigurálja az alkalmazás felhasználói fiók rendszergazdai jogosultságokkal.
> * A mintaalkalmazás áttelepítési megtisztítja a JSON-fájlban szereplő összes felhasználó.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>2.6. lépés: Jelentkezzen be az áttelepített felhasználók (jelszó)
A felhasználói jelszavakat az áttelepítés előtti folyamat futtatása után a fiókok készen áll a használatra, és felhasználók is bejelentkeznek az alkalmazás Azure AD B2C használatával. Ha nem rendelkezik hozzáféréssel a felhasználói jelszavakat, folytassa a következő szakaszban.

## <a name="step-3-help-users-reset-their-password"></a>3. lépés: Segítség a felhasználóknak a jelszavuk
Ha telepít át, hogy a felhasználók egy véletlenszerű jelszó, akkor a jelszavát alaphelyzetbe kell állítania. Könnyebben alaphelyzetbe állíthatja a jelszót, a jelszó alaphelyzetbe állítása mutató hivatkozást tartalmazó üdvözlő e-mail küldése.

A jelszó-visszaállítási házirend hivatkozásának beszerzése, tegye a következőket:

1. Válassza ki **Azure AD B2C-beállítások**, majd válassza ki **jelszó alaphelyzetbe állítása** házirend tulajdonságai.

1. Válassza ki az alkalmazását.

    > [!NOTE]
    > Futtatás most kell előzetesen regisztrálva, a bérlő legalább egy alkalmazás szükséges. Megtudhatja, hogyan regisztrálja az alkalmazást, tekintse meg az Azure AD B2C [Ismerkedés] [ B2C-GetStarted] cikk vagy a [alkalmazásregisztráció] [ B2C-AppRegister] cikk.

1. Válassza ki **Futtatás most**, majd ellenőrizze a szabályzatot.

1. Az a **Futtatás most végponton** mezőbe, majd küldje el a felhasználók számára és másolja az URL-címet.

    ![Set-diagnosztikai naplók](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>4. lépés: (Nem kötelező) A szabályzat ellenőrzi, és állítsa be a felhasználó migrálási állapot módosítása

> [!NOTE]
> Ellenőrizze, és módosítsa a felhasználó migrálás állapotát, egyéni házirendet kell használnia. A telepítési utasításokat [egyéni szabályzatok – első lépések] [ B2C-GetStartedCustom] kell elvégezni.
>

Amikor a felhasználó próbál jelentkezzenek be anélkül, hogy először a jelszó alaphelyzetbe állítása, a szabályzat egy felhasználóbarát hibaüzenetet adja vissza. Példa:
>*Lejárt a jelszava. Új, a jelszó alaphelyzetbe állítása hivatkozásra.*

Ez a lépés nem kötelező az Azure AD B2C-vel egyéni szabályzatok használatát igényli, leírtak szerint a [Ismerkedés az egyéni szabályzatok] [ B2C-GetStartedCustom] cikk.

Ebben a szakaszban Ön módosítja a házirendet, a bejelentkezési felhasználói áttelepítés állapotát. Ha a felhasználó módosította a jelszavát, adja vissza egy HTTP 409 hibaüzenet, amely megkérdezi a felhasználót, válassza ki a **elfelejtette a jelszavát?** hivatkozásra.

A jelszó módosítása nyomon követéséhez használhatja az Azure-táblát. Ha az áttelepítés előtti folyamat futtatja a parancssori paraméterrel `2`, az Azure-táblát hoz létre egy felhasználó típusú entitás. A szolgáltatás a következőket teszi:

- A bejelentkezéshez az Azure AD B2C-szabályzathoz a migrálás RESTful szolgáltatás egy e-mailt küld egy bemeneti jogcímet, hívja meg. A szolgáltatás megkeresi a megadott e-mail-címre az Azure-tábla. Ha a cím létezik, a szolgáltatás a hibaüzenetet jelez: *Meg kell változtatni a jelszót*.

- Miután a felhasználó sikeresen megváltoztatja a jelszót, távolítsa el az entitás az Azure table.

>[!NOTE]
>Az Azure-táblát a mintát egyszerűbb használjuk. A migrálás állapota minden adatbázisban vagy egy egyéni tulajdonság az Azure AD B2C-fiókban tárolhat.

### <a name="41-update-your-application-setting"></a>4.1: Az alkalmazás-beállítás frissítése
1. A RESTful API – demó verzió ellenőrzéséhez nyissa meg a `AADB2C.UserMigration.sln` a Visual Studióban.

1. Az a `AADB2C.UserMigration.API` projektben nyissa meg a *appsettings.json* fájlt. Cserélje le a beállítás-ben konfigurálttal [2.2. lépés](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>4.2. lépés: Az Azure App Service webalkalmazás üzembe helyezése
A Megoldáskezelőben kattintson a jobb gombbal a a `AADB2C.UserMigration.API`, válassza ki a "Közzététele...". Kövesse az utasításokat az Azure App Service-ben való közzététele. További információkért lásd: [alkalmazás üzembe helyezése az Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>4.3. lépés: Egy technikai profil és a technikai profil érvényesítése a szabályzat hozzáadása
1. A Megoldáskezelőben bontsa ki a "Megoldás elem", és nyissa meg a *TrustFrameworkExtensions.xml* szabályzatot tartalmazó fájlt.
1. Változás `TenantId`, `PublicPolicyUri` és `<TenantId>` mezőit a `yourtenant.onmicrosoft.com` a bérlő nevével.
1. Alatt a `<TechnicalProfile Id="login-NonInteractive">` elemben cserélje le az összes példányát `ProxyIdentityExperienceFrameworkAppId` és `IdentityExperienceFrameworkAppId` a konfigurált alkalmazás azonosítóval rendelkező [Ismerkedés az egyéni szabályzatok][B2C-GetStartedCustom].
1. Alatt a `<ClaimsProviders>` csomópontot, keresse meg a következő XML-részletet. Módosítsa az értéket a `ServiceUrl` átirányítása az Azure App Service URL-CÍMÉT.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

A korábbi technikai profil határozza meg egy bemeneti jogcímet: `signInName` (e-mailek küldése). A bejelentkezés a jogcímet küld a RESTful-végpont.

A technikai profil meghatározása a RESTful API-hoz, után ossza meg az Azure AD B2C-szabályzat a technikai profil meghívásához. Az XML-kódrészlet felülbírálások `SelfAsserted-LocalAccountSignin-Email`, amely az alapszintű szabályzat van definiálva. Az XML-kódrészlet emellett hozzáadja `ValidationTechnicalProfile`, a technikai profil mutató hivatkozás azonosítója a `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>4.4. lépés: A szabályzat feltöltése a bérlőhöz
1. Az a [az Azure portal][Portal], váltson át a [az Azure AD B2C-bérlője kontextusában][B2C-NavContext], majd válassza ki **Azure AD B2C-vel**.

1. Válassza ki **identitás-kezelőfelületi keretrendszer**.

1. Válassza ki **összes szabályzat**.

1. Válassza ki **szabályzat feltöltése**.

1. Válassza ki a **szabályzat felülírása, ha létezik** jelölőnégyzetet.

1. Töltse fel a *TrustFrameworkExtensions.xml* fájlt, és győződjön meg arról, hogy érvényesítési továbbítja.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>4.5. lépés: Az egyéni házirend tesztelése a Futtatás most
1. Válassza ki **Azure AD B2C-beállítások**, majd lépjen **identitás-kezelőfelületi keretrendszer**.

1. Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni-szabályzattal, feltöltött, és válassza ki **Futtatás most**.

1. Jelentkezzen be az egyik az áttelepített felhasználók hitelesítő adatait, és válassza ki, próbálja meg **bejelentkezés**. A REST API-t kell throw a következő hibaüzenetet kapja:

    ![Set-diagnosztikai naplók](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>4.6. lépés: (Nem kötelező) A REST API hibaelhárítása
Megtekintheti és naplóinformációi csaknem valós időben figyelheti.

1. A REST-alapú alkalmazás Beállítások menü alatt **figyelés**válassza **diagnosztikai naplók**.

1. Állítsa be **alkalmazásnaplózás (fájlrendszer)** való **a**.

1. Állítsa be a **szint** való **részletes**.

1. Válassza ki **mentése**

    ![Set-diagnosztikai naplók](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. Az a **beállítások** menüjében válassza **naplófolyam**.

1. A RESTful API-kimenet ellenőrzése.

> [!IMPORTANT]
> A diagnosztikai naplók használata során csak a fejlesztési és tesztelési célra. A RESTful API-kimenet tartalmazhatnak bizalmas adatokat, amelyek előtt nem szerencsés felfedni éles környezetben.
>

## <a name="optional-download-the-complete-policy-files"></a>(Nem kötelező) A teljes-fájlok letöltése
Miután elvégezte a [egyéni szabályzatok – első lépések] [ B2C-GetStartedCustom] forgatókönyv, azt javasoljuk, hogy a forgatókönyv a saját egyéni házirend-fájlok használatával hozhat létre. Referenciaként adtunk meg [házirendfájljait minta][UserMigrationSample].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration
