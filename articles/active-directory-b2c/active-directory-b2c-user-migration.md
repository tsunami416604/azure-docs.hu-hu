---
title: Felhasználói áttelepítési megközelítések Azure Active Directory B2C
description: Az Azure AD Graph API használatával ismerteti a felhasználók áttelepítésének alapvető és speciális fogalmait, és igény szerint Azure AD B2C egyéni házirendeket is.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c8e4027bd8892ff3bf5c598573b7736aea42953f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73602579"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: felhasználói áttelepítés

Ha Azure Active Directory B2C (Azure AD B2C) rendszerre telepíti át az identitás-szolgáltatót, akkor előfordulhat, hogy a felhasználói fiókokat is át kell telepítenie. Ez a cikk azt ismerteti, hogyan telepítheti át a meglévő felhasználói fiókokat bármely identitás-szolgáltatótól a Azure AD B2Cba. A cikknek nem kell előírásnak lennie, hanem néhány forgatókönyvet ismertet. A fejlesztő feladata az egyes megközelítések megfelelősége.

## <a name="user-migration-flows"></a>Felhasználói áttelepítési folyamatok

A Azure AD B2C segítségével áttelepítheti a felhasználókat az [Azure ad-Graph API][B2C-GraphQuickStart]használatával. A felhasználó áttelepítési folyamata két folyamatba esik:

- **Áttelepítés előtti**: Ez a folyamat akkor érvényes, ha a felhasználó hitelesítő adatai (Felhasználónév és jelszó) vagy a hitelesítő adatok titkosítottak, de visszafejti őket. Az áttelepítés előtti folyamat magában foglalja a régi identitás-szolgáltató felhasználóinak olvasását és új fiókok létrehozását a Azure AD B2C könyvtárban.

- **Áttelepítés előtti és jelszó alaphelyzetbe állítása**: Ez a folyamat akkor érvényes, ha a felhasználó jelszava nem érhető el. Például:
  - A jelszót a rendszer KIVONATOLÓ formátumban tárolja.
  - A jelszót egy olyan identitás-szolgáltató tárolja, amely nem érhető el. A régi identitás-szolgáltató a webszolgáltatások meghívásával érvényesíti a felhasználói hitelesítő adatokat.

Mindkét folyamat során először futtassa az áttelepítés előtti folyamatot, olvassa be a felhasználókat a régi identitás-szolgáltatótól, és hozzon létre új fiókokat a Azure AD B2C könyvtárban. Ha nem rendelkezik a jelszóval, a fiókot véletlenszerűen generált jelszó használatával hozza létre. Ezután megkéri a felhasználót, hogy változtassa meg a jelszót, vagy ha a felhasználó első alkalommal jelentkezik be, Azure AD B2C megkéri a felhasználót, hogy állítsa alaphelyzetbe.

## <a name="password-policy"></a>Jelszóházirend

A Azure AD B2C jelszóházirend (helyi fiókok esetében) az Azure AD-szabályzaton alapul. A Azure AD B2C a regisztrálási vagy bejelentkezési és a jelszó-visszaállítási szabályzatok az "erős" jelszó erősségét használják, és nem jár le semmilyen jelszó. További információ: [Azure ad Password Policy][AD-PasswordPolicies].

Ha az áttelepíteni kívánt fiókok gyengébb jelszóval rendelkeznek, mint az [Azure ad B2C által kényszerített erős jelszó][AD-PasswordPolicies]erőssége, letilthatja a jelszó erős követelményét. Az alapértelmezett jelszóházirend módosításához állítsa a `passwordPolicies` tulajdonságot `DisableStrongPassword`re. A felhasználói kérelem létrehozása például a következőképpen módosítható:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>1\. lépés: a felhasználók áttelepíthetők az Azure AD Graph API

A Azure AD B2C felhasználói fiókot Graph API használatával (a jelszóval vagy véletlenszerű jelszóval) hozza létre. Ez a szakasz azt ismerteti, hogyan hozhat létre felhasználói fiókokat a Azure AD B2C könyvtárban Graph API használatával.

### <a name="step-11-register-your-application-in-your-tenant"></a>1,1. lépés: az alkalmazás regisztrálása a bérlőben

A Graph APIával folytatott kommunikációhoz először rendszergazdai jogosultságokkal rendelkező szolgáltatásfiók szükséges. Az Azure AD-ben regisztrálni kell egy alkalmazást, és engedélyeznie kell az írási hozzáférést a címtárhoz. Az alkalmazás hitelesítő adatai az **alkalmazás azonosítója** és az **alkalmazás titkos kulcsa**. Az alkalmazás önmagát, nem pedig felhasználóként működik a Graph API meghívásához.

Először regisztráljon egy olyan alkalmazást, amelyet olyan felügyeleti feladatokhoz használhat, mint például a felhasználói áttelepítés.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>1,2. lépés: rendszergazdai engedély megadása az alkalmazás számára

Ezután adja meg az alkalmazásnak, hogy az Azure AD Graph API a címtárba való íráshoz szükséges engedélyeket.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>1,3. lépés: az alkalmazás titkos kulcsának létrehozása

Hozzon létre egy ügyfél titkos kulcsot (kulcs) a felhasználói áttelepítési alkalmazás számára, amelyet egy későbbi lépésben konfigurál.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Most már rendelkezik egy olyan alkalmazással, amely jogosult a Azure AD B2C-bérlőben lévő felhasználók létrehozására, olvasására és frissítésére.

### <a name="step-14-optional-environment-cleanup"></a>1,4. lépés: (nem kötelező) a környezet tisztítása

Az *olvasási és írási könyvtári* engedély nem *tartalmazza a* felhasználók törlésére vonatkozó jogot. Ahhoz, hogy az alkalmazás képes legyen törölni a felhasználókat (a környezet tisztításához), egy további lépést kell elvégeznie, amely magában foglalja a PowerShell futtatását a felhasználói fiók rendszergazdai engedélyeinek beállításához. Ellenkező esetben ugorjon a következő szakaszra.

> [!IMPORTANT]
> A B2C-bérlőhöz *helyi* B2C-bérlői rendszergazdai fiókot kell használnia. A fiók neve szintaxis a *rendszergazda\@contosob2c.onmicrosoft.com*.

Ebben a PowerShell-parancsfájlban, amelyhez az [Azure ad PowerShell V2-modul][AD-Powershell]szükséges, tegye a következőket:

1. Kapcsolódjon az online szolgáltatáshoz. Ehhez futtassa a `Connect-AzureAD` parancsmagot a Windows PowerShell parancssorába, és adja meg a hitelesítő adatait.

1. Az alkalmazás- **azonosító** használatával rendelje hozzá az alkalmazást a felhasználói fiók rendszergazdai szerepköréhez. Ezek a szerepkörök jól ismert azonosítókkal rendelkeznek, ezért mindössze annyit kell tennie, hogy megadja az **alkalmazás azonosítóját** a szkriptben.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

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

Módosítsa a `$AppId` értéket az Azure AD- **alkalmazás azonosítójával**.

## <a name="step-2-pre-migration-application-sample"></a>2\. lépés: az áttelepítés előtti alkalmazás mintája

Az áttelepítés előtti kódrészletet a Közösség által karbantartott `azure-ad-b2c/user-migration` GitHub-tárházban találja:

[Azure-ad-B2C/felhasználó-áttelepítés/áttelepítés előtti][UserMigrationSample-code] (GitHub)

### <a name="step-21-edit-the-migration-data-file"></a>2,1. lépés: az áttelepítési adatfájl szerkesztése

A minta alkalmazás egy olyan JSON-fájlt használ, amely tartalmazza a dummy felhasználói adatkészleteket. A minta sikeres futtatása után módosíthatja a kódot az adatok saját adatbázisból való felhasználásához. Vagy exportálhatja a felhasználói profilt egy JSON-fájlba, majd beállíthatja, hogy az alkalmazás használja ezt a fájlt.

A JSON-fájl szerkesztéséhez nyissa meg a `AADB2C.UserMigration.sln` Visual Studio megoldást. A `AADB2C.UserMigration` projektben nyissa meg a `UsersData.json` fájlt.

![A UsersData. JSON fájl egy része, amely két felhasználó JSON-blokkját mutatja](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Amint láthatja, a fájl tartalmazza a felhasználói entitások listáját. Minden felhasználói entitás a következő tulajdonságokkal rendelkezik:

- e-mail
- displayName
- firstName
- lastName
- jelszó (üres is lehet)

> [!NOTE]
> A fordítás ideje alatt a Visual Studio átmásolja a fájlt a `bin` könyvtárba.

### <a name="step-22-configure-the-application-settings"></a>2,2. lépés: az Alkalmazásbeállítások konfigurálása

Nyissa meg az *app. config* fájlt a `AADB2C.UserMigration` projekt alatt. Cserélje le a következő Alkalmazásbeállítások értékét a saját értékeire:

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
> - Az Azure Table-kapcsolatok karakterláncának használatát a következő szakaszokban ismertetjük.
> - A B2C-bérlő neve az a tartomány, amelyet a bérlő létrehozásakor megadott, és megjelenik a Azure Portal. A bérlő neve általában a következő utótaggal végződik: *. onmicrosoft.com* (például *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>2,3. lépés: az áttelepítés előtti folyamat futtatása

Kattintson a jobb gombbal a `AADB2C.UserMigration` megoldásra, majd hozza létre újra a mintát. Ha a művelet sikeres, a `AADB2C.UserMigration\bin\Debug\net461``UserMigration.exe` végrehajtható fájljának kell lennie. Az áttelepítési folyamat futtatásához használja a következő parancssori paraméterek egyikét:

- A **jelszóval rendelkező felhasználók áttelepíthetők**a `UserMigration.exe 1` parancs használatával.

- Ha **véletlenszerű jelszóval szeretné áttelepíteni a felhasználókat**, használja a `UserMigration.exe 2` parancsot. Ez a művelet létrehoz egy Azure Table entitást is. Később konfigurálja a házirendet a REST API szolgáltatás meghívásához. A szolgáltatás egy Azure-táblázatot használ az áttelepítési folyamat nyomon követéséhez és kezeléséhez.

![A UserMigration. exe parancs kimenetét megjelenítő parancssori ablak](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>2,4. lépés: az áttelepítés előtti folyamat megtekintése

Az áttelepítés ellenőrzéséhez használja az alábbi két módszer egyikét:

- Ha megjelenítendő név alapján szeretne megkeresni egy felhasználót, használja a Azure Portal:

   1. Nyissa meg **Azure ad B2C**, majd válassza a **felhasználók**lehetőséget.
   1. A keresőmezőbe írja be a felhasználó megjelenítendő nevét, majd tekintse meg a felhasználó profilját.

- A felhasználó bejelentkezési e-mail-címével való lekéréséhez használja a minta alkalmazást:

   1. Futtassa az alábbi parancsot:

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > A felhasználót a megjelenítendő név alapján is lekérheti a következő parancs használatával: `UserMigration.exe 4 "<Display name>"`.

   1. A felhasználó adatainak megtekintéséhez nyissa meg a profile. JSON fájlt egy JSON-szerkesztőben.

      ![A felhasználói profil. JSON fájl megnyitása a Visual Studio Code Editorban](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>2,5. lépés: (nem kötelező) a környezet tisztítása

Ha törölni szeretné az Azure AD-bérlőt, és el kívánja távolítani a felhasználókat az Azure AD-címtárból, futtassa a `UserMigration.exe 5` parancsot.

> [!NOTE]
> * A bérlő tisztításához konfigurálja a felhasználói fiók rendszergazdai engedélyeit az alkalmazáshoz.
> * A minta áttelepítési alkalmazás megtisztítja a JSON-fájlban felsorolt összes felhasználót.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>2,6. lépés: bejelentkezés áttelepített felhasználókkal (jelszóval)

Az áttelepítés előtti folyamat felhasználói jelszavakkal való futtatása után a fiókok készen állnak a használatra, és a felhasználók Azure AD B2C használatával bejelentkezhetnek az alkalmazásba. Ha nincs hozzáférése a felhasználói jelszavakhoz, folytassa a következő szakasszal.

## <a name="step-3-help-users-reset-their-password"></a>3\. lépés: a felhasználók jelszavának alaphelyzetbe állításának segítése

Ha véletlenszerű jelszóval telepíti át a felhasználókat, alaphelyzetbe kell állítania a jelszavukat. A jelszó alaphelyzetbe állításához küldjön egy üdvözlő e-mailt a jelszó alaphelyzetbe állítására szolgáló hivatkozással.

A jelszó-visszaállítási házirendre mutató hivatkozás beszerzéséhez kövesse az alábbi lépéseket. Ez az eljárás azt feltételezi, hogy korábban létrehozott egy új jelszó kérése [Egyéni szabályzatot](active-directory-b2c-get-started-custom.md).

1. Válassza ki a Azure AD B2C bérlőt tartalmazó könyvtárat a [Azure Portal](https://portal.azure.com)jobb felső részén található **Directory + előfizetés** szűrő használatával.
1. Válassza a **Azure ad B2C** lehetőséget a bal oldali menüben (vagy az **összes szolgáltatásból**).
1. A **szabályzatok**területen válassza az **identitási élmény keretrendszere**elemet.
1. Válassza ki a jelszó-visszaállítási szabályzatot. Például: *B2C_1A_PasswordReset*.
1. Válassza ki az alkalmazást az **alkalmazás kiválasztása** legördülő menüből.

    > [!NOTE]
    > A **futtatáshoz** szükség van legalább egy alkalmazás regisztrálására a bérlőben. Az alkalmazások regisztrálásával kapcsolatos további információkért lásd [: oktatóanyag: alkalmazás regisztrálása Azure Active Directory B2Cban][B2C-AppRegister].

1. Másolja a **Futtatás most végpont** szövegmezőben látható URL-címet, majd küldje el a felhasználóknak.

    ![Jelszó-visszaállítási házirend lap a Futtatás most végponttal kiemelve](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>4\. lépés: (nem kötelező) módosítsa a szabályzatot a felhasználó áttelepítési állapotának vizsgálatához és beállításához

> [!NOTE]
> A felhasználó áttelepítési állapotának megtekintéséhez és módosításához egyéni szabályzatot kell használnia. Be kell fejezni az [Egyéni szabályzatok első lépéseinek][B2C-GetStartedCustom] telepítési utasításait.

Amikor a felhasználók először próbálnak bejelentkezni a jelszó alaphelyzetbe állítása nélkül, a szabályzatnak barátságos hibaüzenetet kell visszaadnia. Például:

> *A jelszó lejárt. Az alaphelyzetbe állításhoz válassza a jelszó alaphelyzetbe állítása hivatkozást.*

Ez a választható lépés az egyéni [szabályzatok használatának első lépései][B2C-GetStartedCustom] című cikkben leírtak szerint Azure ad B2C egyéni szabályzatok használatát igényli.

Ebben a szakaszban módosítania kell a szabályzatot a felhasználó áttelepítési állapotának a bejelentkezéskor való vizsgálatához. Ha a felhasználó nem módosította a jelszót, adja vissza a HTTP 409 hibaüzenetet, amely arra kéri a felhasználót, hogy jelölje ki az **elfelejtette a jelszavát?** hivatkozást.

A jelszó módosításának nyomon követéséhez Azure-táblázatot kell használnia. Ha az áttelepítés előtti folyamatot az `2`parancssori paraméterrel futtatja, akkor egy Azure-tábla felhasználói entitását kell létrehoznia. A szolgáltatás a következő műveleteket végzi el:

- Bejelentkezéskor a Azure AD B2C szabályzat meghívja az áttelepítési REST-szolgáltatást, amely egy e-mail-üzenetet küld bemeneti jogcímként. A szolgáltatás megkeresi az e-mail-címet az Azure-táblában. Ha a címe létezik, a szolgáltatás hibaüzenetet jelenít meg: *meg kell változtatnia a jelszót*.

- Miután a felhasználó sikeresen módosította a jelszót, távolítsa el az entitást az Azure-táblából.

> [!NOTE]
> Az Azure-táblázat segítségével egyszerűsítheti a mintát. Az áttelepítési állapot bármely adatbázisban vagy egyéni tulajdonságként tárolható a Azure AD B2C fiókban.

### <a name="41-update-your-application-setting"></a>4,1: az alkalmazás beállításainak frissítése

1. A REST API-bemutató teszteléséhez nyissa meg `AADB2C.UserMigration.sln` a Visual Studióban.
1. A `AADB2C.UserMigration.API` projektben nyissa meg a *web. config* fájlt. Cserélje le a beállítást az 2,2-es [lépésben](#step-22-configure-the-application-settings)konfiguráltra:

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>4,2. lépés: a webalkalmazás üzembe helyezése Azure App Service

Megoldáskezelő kattintson a jobb gombbal a `AADB2C.UserMigration.API`ra, majd válassza a "közzététel..." lehetőséget. A Azure App Service közzétételéhez kövesse az utasításokat. További információ: [az alkalmazás üzembe helyezése Azure app Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>4,3. lépés: adjon hozzá egy technikai profilt és egy technikai profilt a Szabályzathoz

1. A Megoldáskezelő bontsa ki a "megoldás elemei" elemet, és nyissa meg a *TrustFrameworkExtensions. XML* fájlt.
1. Módosítsa `TenantId`, `PublicPolicyUri` és `<TenantId>` mezőket a bérlő nevére `yourtenant.onmicrosoft.com`.
1. A `<TechnicalProfile Id="login-NonInteractive">` elem alatt cserélje le a `ProxyIdentityExperienceFrameworkAppId` és a `IdentityExperienceFrameworkAppId` összes példányát az [Egyéni házirendek első lépéseiben][B2C-GetStartedCustom]konfigurált alkalmazás-azonosítókkal.
1. A `<ClaimsProviders>` csomópont alatt keresse meg a következő XML-kódrészletet. Módosítsa `ServiceUrl` értékét úgy, hogy az a Azure App Service URL-címére mutasson.

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

Az előző technikai profil egy bemeneti jogcímet definiál: `signInName` (Küldés e-mailben). Bejelentkezéskor a rendszer elküldi a jogcímet a REST-végpontnak.

Miután meghatározta a REST API technikai profilját, adja meg a Azure AD B2C szabályzatot a technikai profil meghívásához. Az XML-kódrészlet felülbírálja `SelfAsserted-LocalAccountSignin-Email`, amely az alapházirendben van meghatározva. Az XML-kódrészlet emellett `ValidationTechnicalProfile`is hozzáadja, a ReferenceId pedig a technikai profil `LocalAccountUserMigration`ra mutat.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>4,4. lépés: töltse fel a szabályzatot a bérlőbe

1. A [Azure Portal][Portal]váltson a [Azure ad B2C bérlő kontextusára][B2C-NavContext], majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza az **identitási élmény keretrendszert**.
1. Válassza **az összes szabályzat**lehetőséget.
1. Válassza a **szabályzat feltöltése**lehetőséget.
1. Jelölje be a **házirend felülírása, ha létezik** jelölőnégyzetet.
1. Töltse fel a *TrustFrameworkExtensions. XML* fájlt, és ellenőrizze, hogy átadja-e az érvényesítést.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>4,5. lépés: az egyéni házirend tesztelése a Futtatás most használatával

1. Válassza a **Azure ad B2C**lehetőséget, majd az **identitási élmény keretrendszert**.
1. Nyissa meg a *B2C_1A_signup_signin*, a függő entitás (RP) egyéni házirendjét, majd válassza a **Futtatás most**lehetőséget.
1. Adja meg az áttelepített felhasználók egyikének hitelesítő adatait, majd válassza a **Bejelentkezés**lehetőséget. A REST API a következő hibaüzenetet kell eldobnia:

    ![Bejelentkezési oldalon a jelszó módosítása hibaüzenet jelenik meg](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>4,6. lépés: (nem kötelező) a REST API hibáinak megoldása

A naplózási információkat közel valós időben tekintheti meg és figyelheti.

1. A REST-alkalmazás beállítások menüjének **figyelés**területén válassza a **diagnosztikai naplók**lehetőséget.
1. Az **alkalmazás naplózásának (filesystem)** beállítása **a**következőre:.
1. Adja meg a **részletességi** **szintet** .
1. Kattintson a **Mentés** gombra

    ![Diagnosztikai naplók konfigurációs lapja Azure Portal](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. A **Beállítások** menüben válassza a **napló stream**elemet.
1. Vizsgálja meg a REST API kimenetét.

> [!IMPORTANT]
> Csak a fejlesztés és a tesztelés során használja a diagnosztikai naplókat. A REST API-kimenet olyan bizalmas adatokat is tartalmazhat, amelyeket nem szabad éles környezetben feltenni.

## <a name="optional-download-the-complete-policy-files"></a>Választható Töltse le a teljes házirend-fájlokat

Miután elvégezte az [Egyéni szabályzatok használatának első lépéseit][B2C-GetStartedCustom] , javasoljuk, hogy a saját egyéni házirend-fájljaival hozza létre a forgatókönyvet. Az Ön referenciája alapján megadta a [minta házirend-fájlokat][UserMigrationSample-policy].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
