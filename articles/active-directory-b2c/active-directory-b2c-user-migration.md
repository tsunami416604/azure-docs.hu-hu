---
title: Az Azure Active Directory B2C megközelíti felhasználóáttelepítés |} Microsoft Docs
description: Vitassa meg alapvető és speciális fogalmak a Graph API-val, és külön kérésre mintázatot használ az Azure AD B2C egyéni házirendek felhasználói áttelepítése.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7c78cb13f9028b2be527794751d5f8ced9bff171
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711341"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Az Azure Active Directory B2C: Felhasználói áttelepítése
Ha végez Identitásszolgáltatóként az Azure Active Directory B2C (az Azure AD B2C), szükség lehet áttelepíteni a felhasználói fiók. Ez a cikk azt ismerteti, hogyan telepíthetők át a meglévő felhasználói fiókokat bármely identitásszolgáltató az Azure AD B2C. A cikk nem célja, hogy lehet előírásoknak megfelelő, de ehelyett néhány olyan forgatókönyvet ismerteti. A fejlesztői felelős az egyes frissítési megfelelőségét.

## <a name="user-migration-flows"></a>Felhasználó áttelepítési forgalom
Azure AD B2C, felhasználókon telepíthet át [Azure AD Graph API][B2C-GraphQuickStart]. A felhasználó áttelepítési folyamat két adatfolyamok beleesik:

* **Áttelepítés előtti**: Ez a folyamat vonatkozik, vagy törölje a jelet a hozzáférést egy felhasználó hitelesítő adatait (felhasználónév és jelszó) vagy a azonosító adatok titkosítása, de azokat visszafejtéséhez. Az áttelepítés előtti folyamat a következőkből áll: a felhasználók a régi identitásszolgáltató olvasását, és új fiókot hoz létre az Azure AD B2C-címtárat a.

* **Áttelepítés előtti és a jelszó alaphelyzetbe állítása**: folyamatot alkalmazza, ha a jelszó nem érhető el. Példa:
    * A jelszó KIVONATÁT van tárolja.
    * A jelszó nem férhet hozzá az identitásszolgáltató tárolódik. A régi identitásszolgáltató ellenőrzi a felhasználó hitelesítő adatai egy webszolgáltatás-bővítmény meghívásával.

Mindkét adatfolyamok, a először futtatja az áttelepítést megelőző folyamatba, olvassa el a felhasználók a régi identitásszolgáltatótól származó, és új fiók létrehozása az Azure AD B2C-címtárat a. Ha még nem rendelkezik a jelszót, akkor hozzon létre a fiók véletlenszerűen létrehozott jelszót. Majd kérje meg a felhasználó módosíthatja a jelszót, vagy a felhasználó először jelentkezik be, az Azure AD B2C megkérdezi a felhasználót, hogy állítsa alaphelyzetbe.

## <a name="password-policy"></a>Jelszóházirend
Az Azure AD B2C jelszóházirend (helyi fiókok esetében) az Azure AD-házirend alapul. Az Azure AD B2C regisztráció vagy bejelentkezés és a jelszó alaphelyzetbe állítása házirendek használata a "erős" jelszó erőssége, és nem jár le a jelszavakat. További információkért lásd: [az Azure AD-jelszóházirendet][AD-PasswordPolicies].

Ha a fiókok, amelyet át szeretne használni egy gyengébb jelszó erőssége, mint a [kényszeríti ki az Azure AD B2C erős jelszó erőssége][AD-PasswordPolicies], letilthatja a erős jelszóval kapcsolatos követelmények. Az alapértelmezett jelszó-házirend módosításához állítsa a `passwordPolicies` tulajdonságot `DisableStrongPassword`. Például az alábbiak szerint módosíthatja a felhasználó kérés:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>1. lépés: Használata Azure AD Graph API használatával áttelepítheti a felhasználókat
A Graph API-n keresztül az Azure AD B2C-felhasználói fiókot hoz létre, (jelszóval vagy egy véletlenszerű jelszót). Ez a szakasz ismerteti a felhasználói fiókok létrehozása az Azure AD B2C-címtárat a Graph API használatával.

### <a name="step-11-register-your-application-in-your-tenant"></a>1.1. lépés: Az alkalmazás regisztrálása-bérlőben
A Graph API-val folytatott kommunikációhoz, először rendelkeznie kell egy szolgáltatás-rendszergazdai jogosultságokkal rendelkező fiók. Az Azure Active Directoryban regisztrálja a az alkalmazás és az Azure AD hitelesítési. Az alkalmazás hitelesítő adatok **Alkalmazásazonosító** és **Alkalmazáskulcsot**. Az alkalmazás maga nem felhasználóként, a Graph API hívása funkcionál.

Először az Azure AD áttelepítési alkalmazás regisztrációja. Ezután (alkalmazás titkos kulcs) alkalmazás-kulcs létrehozása, és állítsa be az alkalmazás írási jogosultsággal.

1. Jelentkezzen be az [Azure Portalra][Portal].

2. Válassza ki az Azure AD **B2C** bérlői; ehhez válassza a fiókját a felső sarkában az ablak.

3. A bal oldali panelen válassza ki a **Azure Active Directory** (nem az Azure AD B2C). Találja, szükség van kiválasztásához **több szolgáltatások**.

4. Válassza az **Alkalmazásregisztrációk** elemet.

5. Válassza az **Új alkalmazás regisztrálása** elemet.

    ![Új alkalmazásregisztráció](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)

6. Hozzon létre egy új alkalmazást a következő módon:
    * A **neve**, használjon **B2CUserMigration** vagy bármilyen más nevet.
    * A **alkalmazástípus**, használjon **Web app/API**.
    * A **bejelentkezési URL-cím**, használjon **https://localhost** (mivel az nincs ehhez az alkalmazáshoz kapcsolódó).
    * Kattintson a **Létrehozás** gombra.

7. Az alkalmazás létrehozása után a a **alkalmazások** listára, válassza ki az újonnan létrehozott **B2CUserMigration** alkalmazás.

8. Válassza ki **tulajdonságok**, másolása a **Alkalmazásazonosító**, és elmenti későbbi használatra.

### <a name="step-12-create-the-application-secret"></a>1.2. lépés: Az alkalmazás titkos kulcs létrehozása
1. Az Azure portálon **regisztrált alkalmazás** ablakban válassza ki **kulcsok**.

2. Adja hozzá egy új kulcsot (más néven ügyfélkulcs), és másolja a kulcs későbbi használatra.

    ![Alkalmazásazonosító és kulcsok](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>1.3. lépés: Felügyeleti engedélyt az alkalmazás
1. Az Azure portálon **regisztrált alkalmazás** ablakban válassza ki **szükséges engedélyek**.

2. Válassza ki **Windows Azure Active Directory**.

3. Az a **hozzáférés engedélyezése** ablaktáblán, a **Alkalmazásengedélyek**, jelölje be **címtáradatok olvasása és írása**, majd válassza ki **mentése**.

4. Az a **szükséges engedélyek** ablaktáblán válassza előbb **engedélyt adjon**.

    ![Alkalmazásengedélyek](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

Most már rendelkezik egy alkalmazás létrehozása, olvasása, és frissítheti az Azure AD B2C-bérlő felhasználóit engedélyekkel.

### <a name="step-14-optional-environment-cleanup"></a>1.4. lépés: (Nem kötelező) környezetben karbantartása
Olvasási és írási directory adatok engedélyt *nem* tartalmazzák a felhasználók törlése. Ahhoz, hogy megkapja az alkalmazás törlésének felhasználók (a környezet törlése) lehetőségét, el kell végeznie egy további lépést, amely magában foglalja a futtató felhasználói fiók rendszergazdai engedélyek beállítása PowerShell. Egyéb esetben ugorjon a következő szakaszban.

> [!IMPORTANT]
> A B2C bérlői rendszergazdai fiókot, amely segítségével kell *helyi* a B2C bérlő számára. A fiók nevének szintaxisa *admin@contosob2c.onmicrosoft.com*.

>[!NOTE]
> A következő PowerShell-parancsfájl szükséges [Azure Active Directory PowerShell 2-es verzió][AD-Powershell].

A PowerShell-parancsfájlt tegye a következőket:
1. Az online szolgáltatáshoz kapcsolódni. Ehhez futtassa a `Connect-AzureAD` karakterláncot a Windows PowerShell parancsmag parancssort, és adja meg a hitelesítő adatokat.

2. Használja a **Alkalmazásazonosító** rendelhető hozzá az alkalmazás a felhasználói fiók rendszergazdai szerepkör. Ezek a szerepkörök jól ismert azonosítók rendelkezik, így kell tennie, csak adja meg a **Alkalmazásazonosító** a parancsfájlban.

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

Módosítsa a `$AppId` érték és az Azure AD **Alkalmazásazonosító**.

## <a name="step-2-pre-migration-application-sample"></a>2. lépés: Áttelepítés előtti alkalmazás minta
[Töltse le és futtassa a mintakódot][UserMigrationSample]. Letöltheti a .zip-fájlként.

### <a name="step-21-edit-the-migration-data-file"></a>2.1. lépés: Az áttelepítési adatfájl szerkesztése
A mintaalkalmazás dummy felhasználói adatokat tartalmazó JSON-fájlt használ. A minta sikeres futtatása után módosíthatja a kódot az adatokat a saját adatbázis. Vagy a felhasználói profil exportálása egy JSON-fájl, és utána állítsa be az alkalmazást, hogy a fájl használatára.

A JSON-fájl szerkesztéséhez nyissa meg a `AADB2C.UserMigration.sln` Visual Studio megoldás. Az a `AADB2C.UserMigration` projektben nyissa meg a `UsersData.json` fájlt.

![Felhasználói adatok fájl](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Ahogy látja, akkor a fájl felhasználói entitások listáját tartalmazza. Minden felhasználó entitás tulajdonságai a következők:
* e-mailben
* displayName
* Utónév
* Vezetéknév
* jelszó (üres is lehet)

> [!NOTE]
> Fordítási időben, a Visual Studio másolja át a fájlt a `bin` könyvtár.

### <a name="step-22-configure-the-application-settings"></a>2.2. lépés: Az alkalmazás beállításainak konfigurálása
Az a `AADB2C.UserMigration` projektben nyissa meg a *App.config* fájlt. Cserélje le a saját értékeit a következő beállításokkal:

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
> * Egy Azure-tábla kapcsolati karakterlánc használata a következő szakaszokban ismertetett.
> * A B2C bérlő neve a tartományhoz, amely a bérlő létrehozásakor megadott, és az Azure-portálon jelenik meg. A bérlő neve általában utótaggal végződik *. onmicrosoft.com* (például *contosob2c.onmicrosoft.com*).
>

### <a name="step-23-run-the-pre-migration-process"></a>2.3. lépés: Az áttelepítés előtti folyamat fut
Kattintson a jobb gombbal a `AADB2C.UserMigration` megoldás, és ezután összeállítása a minta. Ha sikeres, most rendelkeznie kell egy `UserMigration.exe` található végrehajtható fájl `AADB2C.UserMigration\bin\Debug\net461`. Az áttelepítési folyamat futtatásához használja a következő parancssori paraméterek egyikét:

* A **áttelepítheti a felhasználókat jelszó**, használja a `UserMigration.exe 1` parancsot.

* A **véletlenszerű jelszót a felhasználók áttelepítése**, használja a `UserMigration.exe 2` parancsot. Ez a művelet létrehoz egy Azure-táblaentitássá is. Később a REST API-t hívó házirend konfigurálása. A szolgáltatás egy Azure-tábla nyomon követheti és figyelheti az áttelepítési folyamat használja.

![Áttelepítési folyamat bemutató](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>2.4. lépés: Az áttelepítés előtti folyamat ellenőrzése
Az áttelepítés ellenőrzéséhez használja a következő két módszer egyikét:

* Keresse meg a felhasználó által megjelenített név, használja az Azure-portálon:

    a. Nyissa meg **az Azure AD B2C**, majd válassza ki **felhasználók és csoportok**.

    b. A keresési mezőbe írja be a felhasználó megjelenített neve, és nézze meg a profil.

* A felhasználó bejelentkezési e-mail cím lekéréséhez használja a mintaalkalmazást:

    a. Futtassa az alábbi parancsot:

    ```Console
        UserMigration.exe 3 {email address}
    ```

    > [!TIP]
    > Is olvashatók be a felhasználó megjelenített neve a következő paranccsal: `UserMigration.exe 4 "<Display name>"`.

    b. Nyissa meg a UserProfile.json fájlt egy JSON-szerkesztőt, és tekintse meg a felhasználó adatait.

    ![A UserProfile.json fájl](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>2.5. lépés: (Nem kötelező) környezetben karbantartása
Ha törölni szeretné az Azure AD-bérlő össze, és távolítsa el a felhasználók az Azure AD-címtárral, futtassa a a `UserMigration.exe 5` parancsot.

> [!NOTE]
> * A bérlő karbantartása, konfigurálja a felhasználói fiók rendszergazdai jogosultságokkal az alkalmazáshoz.
> * A mintaalkalmazás áttelepítési a szükségtelenné vált a felhasználók, akik a JSON-fájlban találhatók.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>2.6. lépés: Be kell jelentkezniük az áttelepített felhasználók (jelszó)
Futtatása után az áttelepítés előtti folyamat a felhasználói jelszavakat a fiókok készen áll a használatra, és felhasználók is jelentkezzen be az alkalmazás az Azure AD B2C segítségével. Ha nem fér a felhasználói jelszavakat, folytassa a következő szakaszban.

## <a name="step-3-help-users-reset-their-password"></a>3. lépés: Segítség a felhasználóknak a jelszó alaphelyzetbe állítása
Ha telepít át, hogy a felhasználók egy véletlenszerű jelszót, akkor a jelszavát kell visszaállítania. Segítséget nyújthatnak a jelszó alaphelyzetbe állítása, egy hivatkozás, a jelszó alaphelyzetbe állítása üdvözlő e-mailt küldeni.

A jelszó-visszaállítási házirend hivatkozásának beszerzése, tegye a következőket:

1. Válassza ki **az Azure AD B2C beállítások**, majd válassza ki **jelszó-átállítási** házirend tulajdonságai.

2. Válassza ki az alkalmazást.

    > [!NOTE]
    > Futtatás most a tenant preregistered kell legalább egy alkalmazás szükséges. Megtudhatja, hogyan kell regisztrálni az alkalmazások, tekintse meg az Azure AD B2C [Ismerkedés] [ B2C-GetStarted] cikk vagy a [regisztrációja] [ B2C-AppRegister] cikk.

3. Válassza ki **futtatása most**, és ellenőrizze a házirend.

4. Az a **futtatása most végpont** mezőbe, majd küldje el a felhasználók számára és másolja az URL-címet.

    ![Diagnosztikai naplók beállítása](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>4. lépés: (Nem kötelező) módosítása a házirend ellenőrzése és a felhasználó áttelepítési állapot beállítása

> [!NOTE]
> Ellenőrizze és módosítsa a felhasználói áttelepítés állapotot, egyéni házirendet kell használnia. A telepítés utasításainak [Ismerkedés az egyéni házirendek] [ B2C-GetStartedCustom] el kell végezni.
>

Amikor a felhasználók megpróbálják először jelszavának visszaállítása nélkül bejelentkezni, a házirend a felhasználóbarát hibaüzenet kell visszaadnia. Példa:
>*A jelszava lejárt. Új, jelölje ki a jelszó alaphelyzetbe állítása kapcsolatot.*

Ez az opcionális lépés az Azure AD B2C egyéni házirendek használatát igényli, lásd: a [Ismerkedés az egyéni házirendek] [ B2C-GetStartedCustom] cikk.

Ebben a szakaszban módosítsa a házirendet, a bejelentkezési felhasználói áttelepítési állapotának ellenőrzéséhez. Ha a felhasználó nem módosíthatja a jelszót, térjen vissza a HTTP 409 hibaüzenet jelenik meg, megkérdezi a felhasználót, jelölje be a **elfelejtette a jelszavát?** hivatkozásra.

A jelszó módosítása követheti nyomon, az Azure-tábla használja. Futtatásakor az áttelepítés előtti folyamat a parancssori paraméterrel `2`, az Azure tábla felhasználói entitást hoz létre. A szolgáltatás a következőket teszi:

* Bejelentkezés az Azure AD B2C házirend meghívja az áttelepítés RESTful szolgáltatás, e-mailt küld, bemeneti jogcímet. A szolgáltatás megkeresi az e-mail cím, az Azure tábla. Ha a cím létezik, a szolgáltatás okoz-e a hibaüzenet: *kell változtatni a jelszót*.

* Miután a felhasználó sikeresen megváltoztatta a jelszavát, távolítsa el az entitás az Azure-tábla.

>[!NOTE]
>Egy Azure-tábla használatával leegyszerűsítheti a minta. A tármigrálás állapotának tárolhatja bármilyen adatbázis vagy egy egyéni tulajdonság az Azure AD B2C-fiókban.

### <a name="41-update-your-application-setting"></a>4.1: az alkalmazás beállításainak frissítése
1. Nyissa meg a RESTful API bemutató teszteléséhez `AADB2C.UserMigration.sln` a Visual Studióban.

2. Az a `AADB2C.UserMigration.API` projektben nyissa meg a *appsettings.json* fájlt. Cserélje le a beállítás a beállított egy [lépés 2.2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>4.2. lépés: Az Azure App Service a webes alkalmazás központi telepítése
A Megoldáskezelőben kattintson a jobb gombbal a a `AADB2C.UserMigration.API`, válassza ki a "Közzététele...". Kövesse az utasításokat az Azure App Service közzététele. További információkért lásd: [telepítse az alkalmazást az Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>4.3. lépés: A műszaki profil és a műszaki profil érvényesítési hozzáadása a házirend
1. A Megoldáskezelőben bontsa ki a "Megoldás elem", és nyissa meg a *TrustFrameworkExtensions.xml* házirendfájl.
2. Változás `TenantId`, `PublicPolicyUri` és `<TenantId>` mezőit a `yourtenant.onmicrosoft.com` a bérlő nevét.
3. Az a `<TechnicalProfile Id="login-NonInteractive">` elem, cserélje le az összes példányát `ProxyIdentityExperienceFrameworkAppId` és `IdentityExperienceFrameworkAppId` konfigurált alkalmazás azonosítókkal [Ismerkedés az egyéni házirendek][B2C-GetStartedCustom].
4. Az a `<ClaimsProviders>` csomópont, a következő XML-részletet található. Módosítsa az értéket a `ServiceUrl` úgy, hogy az Azure App Service URL-cím mutasson.

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

Az előző műszaki profil határozza meg egy bemeneti jogcímek: `signInName` (e-mailek küldése). A bejelentkezés a jogcímet küld a RESTful-végpontot.

Után a műszaki profilt a RESTful API határozza meg, kérje meg az Azure AD B2C házirend a műszaki profil hívni. Az XML-részletet felülbírálások `SelfAsserted-LocalAccountSignin-Email`, az alap házirendben definiált. Az XML-részletet is hozzáadja `ValidationTechnicalProfile`, az a műszaki profil mutató hivatkozás azonosítója `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>4.4. lépés: A házirend a bérlő feltöltése.
1. A a [Azure-portálon][Portal], váltson a [az Azure AD B2C-bérlő kontextusában][B2C-NavContext], majd válassza ki **az Azure AD B2C**.

2. Válassza ki **identitás élmény keretrendszer**.

3. Válassza ki **házirend**.

4. Válassza ki **házirend feltöltése**.

5. Válassza ki a **felülírja a házirendet, ha létezik** jelölőnégyzetet.

6. Töltse fel a *TrustFrameworkExtensions.xml* fájlt, és győződjön meg arról, hogy ellenőrzése sikeres.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>4.5. lépés: Az egyéni házirend tesztelése Futtatás most használatával
1. Válassza ki **az Azure AD B2C beállítások**, majd lépjen **identitás élmény keretrendszer**.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni házirend feltöltött, és válassza **futtatása most**.

3. Jelentkezzen be az áttelepített felhasználói hitelesítő adatok közül, majd válassza ki a próbálja **bejelentkezés**. A REST API-t kell előidéznie a következő hibaüzenet:

    ![Diagnosztikai naplók beállítása](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>4.6. lépés: (Nem kötelező) a REST API hibaelhárítása
Megtekintheti és naplózási adatok közel valós idejű figyelését.

1. A RESTful alkalmazás Beállítások menü alatti **figyelés**, jelölje be **diagnosztikai naplók**.

2. Állítsa be **alkalmazásnaplózás (fájlrendszer)** való **a**.

3. Állítsa be a **szint** való **részletes**.

4. Válassza ki **mentése**

    ![Diagnosztikai naplók beállítása](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. Az a **beállítások** menü **naplófolyamot**.

6. Ellenőrizze a RESTful API kimenetét.

További információkért lásd: [folyamatos átviteli naplók és a konzol][AppService-Log].

> [!IMPORTANT]
> A diagnosztikai naplók használata csak fejlesztése és tesztelése során. A RESTful API kimeneti előfordulhat, hogy éles környezetben nem szabad felfedni bizalmas információkat tartalmaznak.
>

## <a name="optional-download-the-complete-policy-files"></a>(Választható) A teljes házirend-fájlok letöltésére
Miután elvégezte a [Ismerkedés az egyéni házirendek] [ B2C-GetStartedCustom] forgatókönyv, azt javasoljuk, hogy a saját egyéni házirend-fájlok használatával történő létrehozása adott esetben. Referenciaként a adtunk [házirendfájljait minta][UserMigrationSample].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[AppService-Log]: https://docs.microsoft.com/azure/active-directory-b2c/app-service-web/web-sites-streaming-logs-and-console
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration