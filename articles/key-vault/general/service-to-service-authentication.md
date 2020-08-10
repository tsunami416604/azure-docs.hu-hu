---
title: Szolgáltatások közötti hitelesítés Azure Key Vault .NET használatával
description: A Microsoft. Azure. Services. AppAuthentication függvénytár használatával hitelesítheti Azure Key Vault a .NET használatával.
keywords: Azure Key-Vault hitelesítés – helyi hitelesítő adatok
author: msmbaldwin
services: key-vault
ms.author: mbaldwin
ms.date: 08/08/2020
ms.topic: conceptual
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: d48e9ac71ba12ecd2eaadb8ba333f5440c68af4b
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88034787"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Szolgáltatások közötti hitelesítés Azure Key Vault .NET használatával

> [!NOTE]
> A **Microsoft. Azure. Services. AppAuthentication** már nem ajánlott az új Key Vault SDK-val való használatra. A rendszer felváltotta az új Azure Identity Library **DefaultAzureCredentials** .net-, Java-, írógéppel-és Python-szolgáltatásokhoz, és minden új fejlesztéshez használható. További információt itt talál: [hitelesítés és az Azure SDK](https://devblogs.microsoft.com/azure-sdk/authentication-and-the-azure-sdk/).

A Azure Key Vault való hitelesítéshez szüksége van egy Azure Active Directory (Azure AD) hitelesítő adatra, vagy egy közös titkos kulcsra vagy egy tanúsítványra.

Az ilyen hitelesítő adatok kezelése nehéz lehet. A hitelesítő adatoknak a forrás-vagy konfigurációs fájlokba való belefoglalásával csábító. A `Microsoft.Azure.Services.AppAuthentication` for .net Library leegyszerűsíti ezt a problémát. A fejlesztő hitelesítő adatait használja a helyi fejlesztés során végzett hitelesítéshez. Ha a megoldást később üzembe helyezi az Azure-ban, a könyvtár automatikusan az alkalmazás hitelesítő adataira vált. A fejlesztői hitelesítő adatok használata a helyi fejlesztés során biztonságosabb, mert nincs szükség Azure AD-beli hitelesítő adatok létrehozására vagy a hitelesítő adatok megosztására a fejlesztők között.

A `Microsoft.Azure.Services.AppAuthentication` könyvtár automatikusan kezeli a hitelesítést, ami lehetővé teszi, hogy a hitelesítő adatai helyett a megoldásra koncentráljon. Támogatja a helyi fejlesztést a Microsoft Visual Studióval, az Azure CLI-vel vagy az Azure AD integrált hitelesítéssel. Felügyelt identitást támogató Azure-erőforrás telepítésekor a tár automatikusan [felügyelt identitásokat használ az Azure-erőforrásokhoz](../../active-directory/msi-overview.md). Nincs szükség kód-vagy konfigurációs módosításra. A könyvtár az Azure AD- [ügyfél hitelesítő adatainak](../../azure-resource-manager/resource-group-authenticate-service-principal.md) közvetlen használatát is támogatja, ha egy felügyelt identitás nem érhető el, vagy ha a fejlesztő biztonsági környezete nem határozható meg a helyi fejlesztés során.

## <a name="prerequisites"></a>Előfeltételek

- [Visual studio 2019](https://www.visualstudio.com/downloads/) vagy [Visual Studio 2017 v 15,5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- A Visual studióhoz készült alkalmazás-hitelesítési bővítmény, amely külön bővítményként érhető el a Visual Studio 2017 Update 5-ös verziójához, és a 6. és újabb verziókban a termékkel együtt van csomagolva. A 6-os vagy újabb frissítéssel ellenőrizheti az alkalmazás-hitelesítési bővítmény telepítését az Azure fejlesztői eszközök kiválasztásával a Visual Studio telepítőből.

## <a name="using-the-library"></a>A könyvtár használata

A .NET-alkalmazások esetében a felügyelt identitással való munka legegyszerűbb módja a `Microsoft.Azure.Services.AppAuthentication` csomagban található. Az első lépések:

1. Válassza az **eszközök**  >  **NuGet csomagkezelő**  >  **NuGet-csomagok kezelése megoldást** a [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és a [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) kulcstartó NuGet-csomagok hivatkozásainak a projekthez való hozzáadásához.

1. Adja hozzá a következő kódot:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

Az `AzureServiceTokenProvider` osztály gyorsítótárazza a memóriában lévő jogkivonatot, és lekéri azt az Azure ad-ből a lejárat előtt. Így a metódus meghívása előtt már nem kell ellenőriznie a lejáratot `GetAccessTokenAsync` . Csak akkor hívja meg a metódust, ha a tokent szeretné használni.

A `GetAccessTokenAsync` metódushoz erőforrás-azonosító szükséges. A Microsoft Azure-szolgáltatásokkal kapcsolatos további tudnivalókért tekintse meg a [Mi az Azure-erőforrások felügyelt identitása](../../active-directory/msi-overview.md)című témakört.

## <a name="local-development-authentication"></a>Helyi fejlesztési hitelesítés

Helyi fejlesztés esetén két elsődleges hitelesítési forgatókönyv létezik: [Az Azure-szolgáltatásokhoz való hitelesítés](#authenticating-to-azure-services)és [az egyéni szolgáltatásokhoz való hitelesítés](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Hitelesítés az Azure-szolgáltatásokban

A helyi gépek nem támogatják az Azure-erőforrások felügyelt identitásait. Ennek eredményeképpen a könyvtár a `Microsoft.Azure.Services.AppAuthentication` fejlesztői hitelesítő adatait használja a helyi fejlesztési környezetben való futtatáshoz. Ha a megoldást üzembe helyezi az Azure-ban, a könyvtár felügyelt identitást használ a OAuth 2,0 ügyfél-hitelesítő adatok engedélyezési folyamatára való váltáshoz. Ez a módszer azt jelenti, hogy a kódot helyileg és távolról is tesztelni kell, anélkül, hogy aggódnia kellene.

Helyi fejlesztés esetén a `AzureServiceTokenProvider` tokeneket a **Visual Studio**, az **Azure parancssori felület** (CLI) vagy az **Azure ad integrált hitelesítés**használatával kérdezi le. Az egyes lehetőségek végrehajtása szekvenciálisan történik, és a könyvtár az első sikeres beállítást használja. Ha egyetlen beállítás sem működik, a `AzureServiceTokenProviderException` kivételt részletes információkkal kell eldobni.

#### <a name="authenticating-with-visual-studio"></a>Hitelesítés a Visual Studióval

Hitelesítés a Visual Studio használatával:

1. Jelentkezzen be a Visual studióba, és válassza az **eszközök** &nbsp; > &nbsp; **lehetőséget** a **Beállítások**megnyitásához.

1. Válassza az **Azure-szolgáltatás hitelesítése**lehetőséget, válasszon egy fiókot a helyi fejlesztéshez, majd kattintson **az OK gombra**.

Ha problémákba ütközik a Visual Studióval, például a jogkivonat-szolgáltatói fájlt érintő hibákkal, gondosan tekintse át az előző lépéseket.

Előfordulhat, hogy újra kell hitelesítenie a fejlesztői jogkivonatot. Ehhez válassza az **eszközök** &nbsp; > &nbsp; **beállításai lehetőséget**, majd válassza az **Azure &nbsp; szolgáltatás- &nbsp; hitelesítés**lehetőséget. Keresse meg az **újbóli hitelesítést** mutató hivatkozást a kiválasztott fiókban. Válassza ki a hitelesítést.

#### <a name="authenticating-with-azure-cli"></a>Hitelesítés az Azure CLI-vel

Az Azure CLI helyi fejlesztéshez való használatához győződjön meg róla, hogy az [Azure CLI v 2.0.12](/cli/azure/install-azure-cli) vagy újabb verzióval rendelkezik.

Az Azure CLI használata:

1. Keresse meg az Azure CLI-t a Windows tálcán a **Microsoft Azure parancssor**megnyitásához.

1. Jelentkezzen be a Azure Portalba: az *login* (bejelentkezés) gombra az Azure-ba való bejelentkezéshez.

1. A hozzáférés ellenőrzéséhez írja be *az az Account Get-Access-Token--Resource https: \/ /Vault.Azure.net*értéket. Ha hibaüzenetet kap, ellenőrizze, hogy megfelelően van-e telepítve az Azure CLI megfelelő verziója.

   Ha az Azure CLI nem az alapértelmezett könyvtárba van telepítve, akkor előfordulhat, hogy a hibajelentés `AzureServiceTokenProvider` nem találja az Azure CLI elérési útját. Az Azure CLI telepítési mappájának definiálásához használja a **AzureCLIPath** környezeti változót. `AzureServiceTokenProvider`szükség esetén hozzáadja a **AzureCLIPath** környezeti változóban megadott könyvtárat a **path** környezeti változóhoz.

1. Ha több fiókkal jelentkezett be az Azure CLI-be, vagy a fiókja több előfizetéshez is hozzáfér, meg kell adnia a használni kívánt előfizetést. Írja be az az *Account set--előfizetés <előfizetés-azonosító>* parancsot.

Ez a parancs csak meghibásodás esetén generál kimenetet. A fiók aktuális beállításainak ellenőrzéséhez adja meg a parancsot `az account list` .

#### <a name="authenticating-with-azure-ad-authentication"></a>Hitelesítés az Azure AD-hitelesítéssel

Az Azure AD-hitelesítés használatához ellenőrizze a következőket:

- A helyszíni Active Directory szinkronizál az Azure AD-be. További információ: [Mi az a hibrid identitás a Azure Active Directory?](../../active-directory/connect/active-directory-aadconnect.md).

- A kód egy tartományhoz csatlakoztatott számítógépen fut.

### <a name="authenticating-to-custom-services"></a>Hitelesítés egyéni szolgáltatásokban

Amikor egy szolgáltatás meghívja az Azure-szolgáltatásokat, az előző lépések működnek, mivel az Azure-szolgáltatások lehetővé teszik a hozzáférést a felhasználókhoz és az alkalmazásokhoz.

Egyéni szolgáltatást meghívó szolgáltatás létrehozásakor használja az Azure AD-ügyfél hitelesítő adatait a helyi fejlesztési hitelesítéshez. Két lehetőség érhető el:

- Egyszerű szolgáltatás használata az Azure-ba való bejelentkezéshez:

    1. Egyszerű szolgáltatásnév létrehozása. További információ: Azure-beli [szolgáltatásnév létrehozása az Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Az Azure CLI használatával jelentkezzen be a következő paranccsal:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Mivel előfordulhat, hogy az egyszerű szolgáltatás nem fér hozzá egy előfizetéshez, használja az `--allow-no-subscriptions` argumentumot.

- Környezeti változók használatával megadhatja az egyszerű szolgáltatásnév részleteit. További információ: [az alkalmazás futtatása egyszerű szolgáltatásnév használatával](#running-the-application-using-a-service-principal).

Miután bejelentkezett az Azure-ba, `AzureServiceTokenProvider` az egyszerű szolgáltatásnév használatával lekéri a helyi fejlesztéshez tartozó jogkivonatot.

Ez a megközelítés csak a helyi fejlesztésre vonatkozik. Ha a megoldást üzembe helyezi az Azure-ban, a könyvtár felügyelt identitásra vált a hitelesítéshez.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Az alkalmazás futtatása felügyelt identitás vagy felhasználó által hozzárendelt identitás használatával

Ha a kódot egy Azure App Service vagy egy Azure-beli virtuális gépen futtatja, amelyen engedélyezve van a felügyelt identitás, a függvénytár automatikusan a felügyelt identitást használja. Nincs szükség kód módosítására, de a felügyelt identitásnak rendelkeznie kell *Get* engedélyekkel a kulcstartóhoz. A felügyelt *identitás a* Key Vault *hozzáférési házirendjein*keresztül adható meg.

Azt is megteheti, hogy egy felhasználó által hozzárendelt identitással hitelesíti magát. A felhasználó által hozzárendelt identitásokkal kapcsolatos további információkért tekintse meg az [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)című témakört. A felhasználó által hozzárendelt identitással történő hitelesítéshez meg kell adnia a felhasználó által hozzárendelt identitás ügyfél-AZONOSÍTÓját a kapcsolódási karakterláncban. A kapcsolatok karakterlánca a [kapcsolatok karakterláncának támogatásában](#connection-string-support)van megadva.

## <a name="running-the-application-using-a-service-principal"></a>Az alkalmazás futtatása egyszerű szolgáltatásnév használatával

Előfordulhat, hogy létre kell hoznia egy Azure AD-ügyfél hitelesítő adatait a hitelesítéshez. Ez a helyzet az alábbi példákban fordulhat elő:

- A kód helyi fejlesztési környezetben fut, de a fejlesztői identitása nem. Service Fabric például a [NetworkService fiókot](../../service-fabric/service-fabric-application-secret-management.md) használja a helyi fejlesztéshez.

- A kód egy helyi fejlesztési környezetben fut, és Ön hitelesíti magát egy egyéni szolgáltatásban, így nem használhatja a fejlesztői identitását.

- A kód olyan Azure számítási erőforráson fut, amely még nem támogatja az Azure-erőforrások felügyelt identitásait, például a Azure Batch.

Az alkalmazás futtatásához három elsődleges módszer használható egyszerű szolgáltatásnév használatával. Ezek bármelyikének használatához először létre kell hoznia egy egyszerű szolgáltatásnevet. További információ: Azure-beli [szolgáltatásnév létrehozása az Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Helyi tárolóban lévő tanúsítvány használata az Azure AD-ba való bejelentkezéshez

1. Hozzon létre egy egyszerű szolgáltatásnév-tanúsítványt az Azure CLI az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancs használatával.

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Ez a parancs létrehoz egy. PEM-fájlt (titkos kulcsot), amely a saját könyvtárában van tárolva. Telepítse a tanúsítványt a *LocalMachine* vagy a *CurrentUser* tárolóba.

    > [!Important]
    > A CLI-parancs létrehoz egy. PEM-fájlt, de a Windows csak natív támogatást biztosít a PFX-tanúsítványokhoz. PFX-tanúsítvány létrehozásához használja az itt látható PowerShell-parancsokat: [egyszerű szolgáltatásnév létrehozása önaláírt tanúsítvánnyal](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Ezek a parancsok automatikusan telepítik a tanúsítványt is.

1. Állítson be egy **AzureServicesAuthConnectionString** nevű környezeti változót a következő értékre:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Cserélje le a *{AppID}*, *{TenantId}* és *{ujjlenyomat}* értéket az 1. lépésben generált értékekre. Cserélje le a (z) *{CertificateStore}* *LocalMachine*vagy *CurrentUser*a telepítési terv alapján.

1. Futtassa az alkalmazást.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Közös titkos kulcs hitelesítő adatainak használata az Azure AD-ba való bejelentkezéshez

1. Hozzon létre egy egyszerű szolgáltatásnév-tanúsítványt az Azure CLI az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancs használatával az--SDK-Auth paraméterrel.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Állítson be egy **AzureServicesAuthConnectionString** nevű környezeti változót a következő értékre:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Cserélje le a _{AppID}_, _{TenantId}_ és _{ClientSecret}_ értéket az 1. lépésben generált értékekre.

1. Futtassa az alkalmazást.

Ha minden megfelelően be van állítva, nincs szükség további kód módosítására. `AzureServiceTokenProvider`a környezeti változót és a tanúsítványt használja az Azure AD-ben való hitelesítéshez.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Key Vault-tanúsítvány használata az Azure AD-ba való bejelentkezéshez

Ez a beállítás lehetővé teszi egy egyszerű szolgáltatásnév tanúsítványának tárolását Key Vaultban, és az egyszerű szolgáltatás hitelesítéséhez használja azt. Ezt a lehetőséget a következő esetekben használhatja:

- Helyi hitelesítés, ha egy explicit egyszerű szolgáltatásnév használatával szeretne hitelesítést végezni, és a szolgáltatás egyszerű hitelesítő adatait szeretné biztonságosan tárolni egy kulcstartóban. A fejlesztői fióknak hozzáféréssel kell rendelkeznie a kulcstartóhoz.

- Hitelesítés az Azure-ból, ahol explicit hitelesítő adatokat szeretne használni, és a szolgáltatás egyszerű hitelesítő adatait szeretné biztonságosan tárolni egy kulcstartóban. Ezt a lehetőséget egy több-bérlős forgatókönyv esetén is használhatja. A felügyelt identitásnak hozzáféréssel kell rendelkeznie a Key vaulthoz.

A felügyelt identitásnak vagy a fejlesztői identitásnak engedéllyel kell rendelkeznie az ügyféltanúsítványt a Key Vaultból való lekéréséhez. A AppAuthentication-könyvtár a beolvasott tanúsítványt használja az egyszerű szolgáltatásnév ügyfél-hitelesítő adataiként.

Ügyféltanúsítvány használata az egyszerű szolgáltatás hitelesítéséhez:

1. Hozzon létre egy egyszerű szolgáltatásnév-tanúsítványt, és tárolja automatikusan a Key Vault. Az Azure CLI az [ad SP Create-for-RBAC--Key Vault \<keyvaultname> --CERT \<certificatename> --create-CERT--skip-hozzárendelés](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancs használata:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    A tanúsítvány azonosítója egy URL-cím lesz a formátumban`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Cserélje le a `{KeyVaultCertificateSecretIdentifier}` karakterláncot a következő azonosítójú tanúsítványra:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Ha például a Key Vault neve *myKeyVault* volt, és létrehozott egy *myCert*nevű tanúsítványt, a tanúsítvány azonosítója a következő lesz:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>A kapcsolatok karakterláncának támogatása

Alapértelmezés szerint `AzureServiceTokenProvider` a a következő hitelesítési módszereket kísérli meg a jogkivonat lekéréséhez:

- [Felügyelt identitás az Azure-erőforrásokhoz](../..//active-directory/managed-identities-azure-resources/overview.md)
- Visual Studio-hitelesítés
- [Azure CLI-hitelesítés](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)
- [Integrált Windows-hitelesítés](/aspnet/web-api/overview/security/integrated-windows-authentication)

A folyamat szabályozásához használjon a konstruktornak átadott, `AzureServiceTokenProvider` vagy a *AzureServicesAuthConnectionString* környezeti változóban megadott kapcsolatot megadó karakterláncot.  A következő lehetőségek támogatottak:

| A kapcsolatok karakterláncának beállítása | Eset | Megjegyzések|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Helyi fejlesztés | `AzureServiceTokenProvider`a AzureCli használatával kérdezi le a tokent. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Helyi fejlesztés | `AzureServiceTokenProvider`a Visual studiót használja a jogkivonat lekéréséhez. |
| `RunAs=CurrentUser` | Helyi fejlesztés | A .NET Core-ban nem támogatott. `AzureServiceTokenProvider`Az Azure AD integrált hitelesítést használ a token beszerzéséhez. |
| `RunAs=App` | [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`felügyelt identitást használ a jogkivonat lekéréséhez. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Felhasználó által hozzárendelt identitás az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) | `AzureServiceTokenProvider`felhasználó által hozzárendelt identitást használ a jogkivonat lekéréséhez. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Egyéni szolgáltatások hitelesítése | `KeyVaultCertificateSecretIdentifier`a tanúsítvány titkos azonosítója. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Szolgáltatásnév | `AzureServiceTokenProvider`tanúsítvány használatával szerez tokent az Azure AD-ből. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Szolgáltatásnév | `AzureServiceTokenProvider`tanúsítvány használatával szerez tokent az Azure AD-ből|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Szolgáltatásnév |`AzureServiceTokenProvider`a Secret használatával szerez tokent az Azure AD-ből. |

## <a name="samples"></a>Példák

A `Microsoft.Azure.Services.AppAuthentication` függvénytár működés közbeni megtekintéséhez tekintse meg a következő kódrészleteket.

- [Felügyelt identitás használata Azure Key Vault-beli titkos kód lekéréséhez futásidőben](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Felügyelt identitással programozott módon telepíthet egy Azure Resource Manager sablont egy Azure-beli virtuális](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)gépről.

- A [.net Core minta és egy felügyelt identitás használatával hívhat Azure-szolgáltatásokat Azure-beli LINUXOS virtuális](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)gépről.

## <a name="appauthentication-troubleshooting"></a>AppAuthentication-hibaelhárítás

### <a name="common-issues-during-local-development"></a>Gyakori problémák a helyi fejlesztés során

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Az Azure CLI nincs telepítve, nincs bejelentkezve, vagy nem rendelkezik a legújabb verzióval

Futtassa az *az Account Get-Access-Token* parancsot, és ellenőrizze, hogy az Azure CLI megjelenít-e tokent. Ha **nem talál ilyen programot**, telepítse az [Azure CLI legújabb verzióját](/cli/azure/install-azure-cli?view=azure-cli-latest). Előfordulhat, hogy a rendszer arra kéri, hogy jelentkezzen be.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>A AzureServiceTokenProvider nem találja az Azure CLI elérési útját

A AzureServiceTokenProvider az Azure CLI-t az alapértelmezett telepítési helyein keresi. Ha nem találja az Azure CLI-t, állítsa be az **AzureCLIPath** környezeti változót az Azure CLI telepítési mappájába. A AzureServiceTokenProvider hozzáadja a környezeti változót a PATH környezeti változóhoz.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Több fiókkal jelentkezett be az Azure CLI-be, ugyanaz a fiók több bérlőn is hozzáférhet az előfizetésekhez, vagy hozzáférés-megtagadási hibaüzenetet kap, amikor a helyi fejlesztés során próbál meg hívásokat kezdeményezni

Az Azure CLI használatával állítsa be az alapértelmezett előfizetést, amely a használni kívánt fiókot használja. Az előfizetésnek ugyanabban a bérlőben kell lennie, mint az elérni kívánt erőforrásnak: az **Account set--előfizetés [előfizetés-azonosító]**. Ha nem látható kimenet, a művelet sikeres volt. Ellenőrizze, hogy a megfelelő fiók most az alapértelmezett az **az Account List**paranccsal.

### <a name="common-issues-across-environments"></a>Gyakori problémák a környezetek között

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Jogosulatlan hozzáférés, hozzáférés megtagadva, tiltott vagy hasonló hiba

A résztvevőnek nincs hozzáférése ahhoz az erőforráshoz, amelyet megpróbál elérni. Adja meg a felhasználói fiókját vagy a App Service MSI "közreműködő" hozzáférését egy erőforráshoz. Az egyik attól függ, hogy a mintát a helyi számítógépen futtatja, vagy az Azure-ban telepítette a App Serviceba. Bizonyos erőforrások, például a kulcstartók is rendelkeznek saját [hozzáférési szabályzatokkal](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) , amelyeket a rendszerbiztonsági tag, például a felhasználók, az alkalmazások és a csoportok hozzáférésének biztosítására használ.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Gyakori problémák a Azure App Service való üzembe helyezéskor

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>A felügyelt identitás nincs beállítva a App Service

Győződjön meg arról, hogy a környezeti változók MSI_ENDPOINT és MSI_SECRET léteznek a [kudu hibakeresési konzol](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/)használatával. Ha a környezeti változók nem léteznek, a felügyelt identitás nincs engedélyezve a App Serviceon.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Gyakori problémák az IIS-sel való helyi üzembe helyezéskor

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Nem lehet beolvasni a jogkivonatokat az IIS-alkalmazás hibakeresése során

Alapértelmezés szerint a AppAuth az IIS egy másik felhasználói környezetében fut. Ezért nem fér hozzá a fejlesztői identitás használatához hozzáférési jogkivonatok lekéréséhez. Az IIS-t beállíthatja úgy, hogy a felhasználói környezettel fusson a következő két lépéssel:
- Konfigurálja a webalkalmazás alkalmazáskészletét úgy, hogy az aktuális felhasználói fiókként fusson. További információk [itt](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities) találhatók
- Konfigurálja a "setProfileEnvironment" értéket a "true" értékre. További információ [itt](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)található. 

    - Ugrás a (z)% windir% \System32\inetsrv\config\applicationHost.config
    - Keressen rá a "setProfileEnvironment" kifejezésre. Ha "false" (hamis) értékre van állítva, módosítsa a "true" (igaz) értékre. Ha nincs jelen, adja hozzá attribútumként a processModel elemhez ( /configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment ), és állítsa "true" értékre.

- További információ az [Azure-erőforrások felügyelt identitásáról](../../active-directory/managed-identities-azure-resources/index.yml).
- További információ az [Azure ad-hitelesítési forgatókönyvekről](../../active-directory/develop/active-directory-authentication-scenarios.md).
