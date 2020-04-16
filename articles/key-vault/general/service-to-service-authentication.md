---
title: Szolgáltatás-szolgáltatás hitelesítés az Azure Key Vault számára a .NET használatával
description: A Microsoft.Azure.Services.AppAuthentication könyvtár segítségével hitelesítheti magát az Azure Key Vault ban a .NET használatával.
keywords: az azure key vault helyi hitelesítő adatok hitelesítése
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: d6ac5961cbecf4e81c0b6bcc25c39aad42b18416
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429836"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Szolgáltatás-szolgáltatás hitelesítés az Azure Key Vault számára a .NET használatával

Az Azure Key Vault hitelesítéséhez egy Azure Active Directory (Azure AD) hitelesítő adatokra van szükség, akár egy közös titokban, akár egy tanúsítványban.

Az ilyen hitelesítő adatok kezelése nehéz lehet. Csábító, hogy köteg hitelesítő adatokat egy alkalmazásba, beleértve azokat a forrás- vagy konfigurációs fájlokat. A `Microsoft.Azure.Services.AppAuthentication` .NET könyvtár leegyszerűsíti ezt a problémát. A fejlesztő hitelesítő adatait használja a helyi fejlesztés során történő hitelesítéshez. Amikor a megoldás később üzembe kerül az Azure-ban, a könyvtár automatikusan átvált az alkalmazás hitelesítő adataira. A fejlesztői hitelesítő adatok használata a helyi fejlesztés során biztonságosabb, mert nem kell azure AD hitelesítő adatokat létrehoznia, vagy a fejlesztők között megosztania a hitelesítő adatokat.

A `Microsoft.Azure.Services.AppAuthentication` tár automatikusan kezeli a hitelesítést, ami viszont lehetővé teszi, hogy a megoldásra összpontosítson, ne pedig a hitelesítő adataira. Támogatja a helyi fejlesztést a Microsoft Visual Studio, az Azure CLI vagy az Azure AD integrált hitelesítéssel. Ha felügyelt identitást támogató Azure-erőforrásra helyezi üzembe, a könyvtár automatikusan felügyelt identitásokat használ [az Azure-erőforrásokhoz.](../../active-directory/msi-overview.md) Nincs szükség kód- vagy konfigurációmódosításra. A könyvtár is támogatja az Azure [AD-ügyfél hitelesítő adatok](../../azure-resource-manager/resource-group-authenticate-service-principal.md) közvetlen használatát, ha egy felügyelt identitás nem érhető el, vagy ha a fejlesztő biztonsági környezetben nem határozható meg a helyi fejlesztés során.

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) vagy [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- A Visual Studio alkalmazáshitelesítési bővítménye, amely a Visual Studio 2017 5-ös frissítésének külön bővítményeként érhető el, és a 6. A 6- as vagy újabb frissítéssel ellenőrizheti az alkalmazáshitelesítési bővítmény telepítését, ha a Visual Studio telepítőjéből kiválasztja az Azure Development eszközöket.

## <a name="using-the-library"></a>A könyvtár használata

A .NET-alkalmazások esetében a felügyelt identitással való munka `Microsoft.Azure.Services.AppAuthentication` legegyszerűbb módja a csomagon keresztül történik. Az első lépések:

1. Válassza **az Eszközök** > **NuGet csomagkezelő** > **kezelése A NuGet csomagok kezelése a megoldáshoz** lehetőséget, ha hivatkozásokat szeretne hozzáadni a [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és a [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet csomagokhoz a projekthez.

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

Az `AzureServiceTokenProvider` osztály gyorsítótárazza a jogkivonatot a memóriában, és lekéri azt az Azure AD-ből közvetlenül a lejárat előtt. Tehát már nem kell ellenőriznie a `GetAccessTokenAsync` lejárati időt a metódus hívása előtt. Csak hívja meg a metódust, ha használni szeretné a jogkivonatot.

A `GetAccessTokenAsync` metódushoz erőforrás-azonosító szükséges. Ha többet szeretne megtudni a Microsoft Azure-szolgáltatásokról, olvassa el [a Mi az Azure-erőforrások felügyelt identitásai.](../../active-directory/msi-overview.md)

## <a name="local-development-authentication"></a>Helyi fejlesztési hitelesítés

A helyi fejlesztéshez két elsődleges hitelesítési forgatókönyv létezik: [az Azure-szolgáltatásokhitelesítés](#authenticating-to-azure-services)és [az egyéni szolgáltatások hitelesítése](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Hitelesítés az Azure-szolgáltatásokba

A helyi gépek nem támogatják az Azure-erőforrások felügyelt identitásait. Ennek eredményeképpen `Microsoft.Azure.Services.AppAuthentication` a tár a fejlesztői hitelesítő adatokat használja a helyi fejlesztési környezetben való futtatáshoz. Amikor a megoldás telepítve van az Azure-ban, a tár egy felügyelt identitás takar egy OAuth 2.0 ügyfél hitelesítő adatok megadása flow-t. Ez a megközelítés azt jelenti, hogy tesztelje ugyanazt a kódot helyileg és távolról gond nélkül.

Helyi fejlesztés `AzureServiceTokenProvider` esetén a tokeneket a **Visual Studio**, az **Azure parancssori felület** (CLI) vagy az Azure AD integrált hitelesítés használatával olvassa **be.** Minden beállítás egymás után próbálkozik, és a tár az első sikeres beállítást használja. Ha egyetlen lehetőség `AzureServiceTokenProviderException` sem működik, a program kivételt tartalmaz a részletes információkkal.

#### <a name="authenticating-with-visual-studio"></a>Hitelesítés a Visual Studio segítségével

Hitelesítés a Visual Studio használatával:

1. Jelentkezzen be a Visual Studióba, és az **Eszközök**&nbsp;>&nbsp;**beállításai segítségével** nyissa meg a Beállítások **lehetőséget.**

1. Válassza az **Azure Service Authentication**lehetőséget, válasszon egy fiókot a helyi fejlesztéshez, és kattintson az OK **gombra.**

Ha problémákba ütközik a Visual Studio használatával, például a jogkivonat-szolgáltató fájlját tartalmazó hibákba, alaposan tekintse át az előző lépéseket.

Előfordulhat, hogy újra kell hitelesítenie a fejlesztői jogkivonatot. Ehhez válassza az **Eszközök**&nbsp;>&nbsp;**beállításai**lehetőséget, majd az **Azure&nbsp;&nbsp;Service Authentication**lehetőséget. Keressen egy **újrahitelesítési** hivatkozást a kiválasztott fiók alatt. Jelölje ki a hitelesítéshez.

#### <a name="authenticating-with-azure-cli"></a>Hitelesítés az Azure CLI-vel

Az Azure CLI helyi fejlesztéshez való használatához győződjön meg arról, hogy [az Azure CLI 2.0.12-es](/cli/azure/install-azure-cli) vagy újabb verzióját használja.

Az Azure CLI használata:

1. Keresse meg az Azure CLI-t a Windows tálcán a **Microsoft Azure parancssor**megnyitásához.

1. Jelentkezzen be az Azure Portalon: *az jelentkezzen be* az Azure-ba való bejelentkezéshez.

1. A hozzáférés ellenőrzése *az-fiók https://vault.azure.netget-access-token --resource *. Ha hibaüzenetet kap, ellenőrizze, hogy az Azure CLI megfelelő verziója megfelelően van-e telepítve.

   Ha az Azure CLI nincs telepítve az alapértelmezett könyvtárba, `AzureServiceTokenProvider` előfordulhat, hogy hibaüzenetet kap, amely nem találja az Azure CLI elérési útját. Az **AzureCLIPath** környezeti változó használatával definiálja az Azure CLI telepítési mappát. `AzureServiceTokenProvider`szükség esetén hozzáadja az **AzureCLIPath** környezeti változóban megadott könyvtárat a **Path** környezeti változóhoz.

1. Ha több fiókkal jelentkezett be az Azure CLI-be, vagy ha a fiók több előfizetéshez is hozzáfér, meg kell adnia a használni kívánt előfizetést. Adja meg a *parancsot az fiók készlet --előfizetés <előfizetés-id>*.

Ez a parancs csak meghibásodás esetén hoz létre kimenetet. Az aktuális fiók beállításainak ellenőrzéséhez írja be a parancsot. `az account list`

#### <a name="authenticating-with-azure-ad-authentication"></a>Hitelesítés Azure AD-hitelesítéssel

Az Azure AD-hitelesítés használatához ellenőrizze, hogy:

- A helyszíni Active Directory szinkronizálja az Azure AD-t. További információ: [Mi a hibrid identitás az Azure Active Directoryval?](../../active-directory/connect/active-directory-aadconnect.md).

- A kód tartományhoz csatlakozó számítógépen fut.

### <a name="authenticating-to-custom-services"></a>Hitelesítés egyéni szolgáltatásokhoz

Amikor egy szolgáltatás meghívja az Azure-szolgáltatásokat, az előző lépések működnek, mert az Azure-szolgáltatások lehetővé teszik a hozzáférést a felhasználók és az alkalmazások számára.

Amikor egy szolgáltatást, amely meghívja az egyéni szolgáltatás, használja az Azure AD-ügyfél hitelesítő adatait a helyi fejlesztési hitelesítés. Két lehetőség érhető el:

- Egyszerű szolgáltatás használata az Azure-ba való bejelentkezéshez:

    1. Hozzon létre egy egyszerű szolgáltatás. További információ: [Create an Azure service principal with Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Az Azure CLI használatával jelentkezzen be a következő paranccsal:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Mivel előfordulhat, hogy az egyszerű szolgáltatás nem `--allow-no-subscriptions` fér hozzá egy előfizetéshez, használja az argumentumot.

- Környezeti változók használatával adja meg az egyszerű szolgáltatás részleteit. További információ: [Az alkalmazás futtatása egyszerű szolgáltatás használatával című témakörben.](#running-the-application-using-a-service-principal)

Miután bejelentkezett az Azure-ba, `AzureServiceTokenProvider` használja az egyszerű szolgáltatás tanusítja a helyi fejlesztés jogkivonatát.

Ez a megközelítés csak a helyi fejlesztésre vonatkozik. Amikor a megoldás telepítve van az Azure-ban, a könyvtár átvált egy felügyelt identitás hitelesítéshez.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Az alkalmazás futtatása felügyelt identitással vagy felhasználó által hozzárendelt identitással

Amikor a kódot egy Azure App Service-en vagy egy Azure-beli virtuális gépen futtatja, amelynek engedélyezett a felügyelt identitása, a kódtár automatikusan a felügyelt identitást használja. Nincs szükség kódmódosításra, de a felügyelt identitásnak be kell *szereznie* a key vault engedélyeit. A felügyelt identitás a kulcstartó *hozzáférési*szabályzatain keresztül *kaphat* engedélyeket.

Azt is megteheti, hogy hitelesíti a felhasználó által hozzárendelt identitást. A felhasználó által hozzárendelt identitásokról az [Azure-erőforrások felügyelt identitások – további információt](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)talál. A felhasználó által hozzárendelt identitással történő hitelesítéshez meg kell adnia a kapcsolati karakterláncban a felhasználó által hozzárendelt identitás ügyfélazonosítóját. A kapcsolati karakterlánc a [Kapcsolati karakterlánc támogatása](#connection-string-support)című részben van megadva.

## <a name="running-the-application-using-a-service-principal"></a>Az alkalmazás futtatása egyszerű szolgáltatás használatával

Előfordulhat, hogy létre kell hoznia egy Azure AD-ügyfél hitelesítő adatait a hitelesítéshez. Ez a helyzet a következő példákban fordulhat elő:

- A kód helyi fejlesztői környezetben fut, de nem a fejlesztő identitása alatt. A Service Fabric például a [NetworkService-fiókot](../../service-fabric/service-fabric-application-secret-management.md) használja a helyi fejlesztéshez.

- A kód helyi fejlesztői környezetben fut, és egy egyéni szolgáltatásban hitelesíti magát, így nem használhatja a fejlesztői identitást.

- A kód fut egy Azure-számítási erőforrás, amely még nem támogatja az Azure-erőforrások, például az Azure Batch felügyelt identitások.

Három elsődleges módszer az egyszerű szolgáltatás használatával az alkalmazás futtatásához. Ezek bármelyikének használatához először létre kell hoznia egy egyszerű szolgáltatást. További információ: [Create an Azure service principal with Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Tanúsítvány használata a helyi kulcstárolóban az Azure AD-be való bejelentkezéshez

1. Hozzon létre egy egyszerű szolgáltatástanúsítványt az Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) paranccsal.

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Ez a parancs a kezdőkönyvtárban tárolt .pem fájlt (személyes kulcsot) hoz létre. Telepítse ezt a tanúsítványt a *LocalMachine* vagy a *CurrentUser* tárolóba.

    > [!Important]
    > A CLI parancs .pem fájlt hoz létre, de a Windows csak natív támogatást nyújt a PFX-tanúsítványokhoz. PFX-tanúsítvány létrehozásához használja az itt látható [PowerShell-parancsokat: Egyszerű szolgáltatás létrehozása önaláírt tanúsítvánnyal.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) Ezek a parancsok automatikusan telepítik a tanúsítványt is.

1. Állítson be egy **AzureServicesAuthConnectionString** nevű környezeti változót a következő értékre:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Cserélje le *a(z) {AppId}*, *{TenantId}* és *{Thumbprint}* értékeket az 1. Cserélje le *a(z) {CertificateStore}* programot *a LocalMachine*' vagy a *CurrentUser*szolgáltatásra a telepítési terv alapján.

1. Futtassa az alkalmazást.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Az Azure AD-be való bejelentkezéshez használjon megosztott titkos hitelesítő adatokat

1. Hozzon létre egy egyszerű szolgáltatástanúsítványt jelszóval az Azure CLI [az ad create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) paranccsal a --sdk-auth paraméterrel.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Állítson be egy **AzureServicesAuthConnectionString** nevű környezeti változót a következő értékre:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Cserélje le _a(z) {AppId}_, _{TenantId}_ és _{ClientSecret}_ értékeket az 1.

1. Futtassa az alkalmazást.

Ha minden helyesen van beállítva, nincs szükség további kódmódosításra. `AzureServiceTokenProvider`a környezeti változót és a tanúsítványt használja az Azure AD hitelesítéséhez.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Tanúsítvány használata a Key Vaultban az Azure AD-be való bejelentkezéshez

Ez a beállítás lehetővé teszi, hogy egy egyszerű szolgáltatás ügyféltanúsítványát tárolja a Key Vaultban, és használja azt az egyszerű szolgáltatás hitelesítéséhez. Ezt a lehetőséget a következő esetekben használhatja:

- Helyi hitelesítés, ahol explicit egyszerű szolgáltatáshasználatával szeretné hitelesíteni, és szeretné a szolgáltatásegyszerű hitelesítő adatokbiztonságosan egy key vaultban. A fejlesztői fióknak hozzáféréssel kell rendelkeznie a kulcstartóhoz.

- Hitelesítés az Azure-ból, ahol explicit hitelesítő adatokat szeretne használni, és szeretné, hogy a szolgáltatásegyszerű hitelesítő adatok biztonságosan egy key vaultban. Ezt a beállítást egy több-bérlős forgatókönyvhöz is használhatja. A felügyelt identitásnak hozzáféréssel kell rendelkeznie a kulcstartóhoz.

A felügyelt identitás vagy a fejlesztői identitás nak engedéllyel kell rendelkeznie az ügyféltanúsítvány lekéréséhez a Key Vaultból. Az AppAuthentication könyvtár a beolvasott tanúsítványt használja az egyszerű szolgáltatás ügyfélhitelesítő adataiként.

Ügyféltanúsítvány használata egyszerű szolgáltatáshitelesítéshez:

1. Hozzon létre egy egyszerű szolgáltatástanúsítványt, és automatikusan tárolja a Key Vaultban. Használja az Azure CLI [az ad sp create-rbac \<--keyvault keyvaultname> --cert \<tanúsítványnév> --create-cert --skip-assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancs:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    A tanúsítványazonosító egy URL lesz a formátumban`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Cserélje `{KeyVaultCertificateSecretIdentifier}` le ezt a kapcsolati karakterláncot a tanúsítványazonosítóra:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Ha például a kulcstartóját *myKeyVault-nak* hívták, és létrehozott egy *myCert nevű tanúsítványt,* a tanúsítványazonosító a következő lesz:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Kapcsolati karakterlánc támogatása

Alapértelmezés szerint `AzureServiceTokenProvider` több módszert használ a token beolvasásához.

A folyamat vezérléséhez használjon a `AzureServiceTokenProvider` konstruktornak átadott vagy az *AzureServicesAuthConnectionString* környezeti változóban megadott kapcsolati karakterláncot.

A következő beállítások támogatottak:

| Kapcsolati karakterlánc beállítás | Forgatókönyv | Megjegyzések|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Helyi fejlesztés | `AzureServiceTokenProvider`az AzureCli segítségével kap token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Helyi fejlesztés | `AzureServiceTokenProvider`a Visual Studio segítségével kap tokeneket. |
| `RunAs=CurrentUser` | Helyi fejlesztés | `AzureServiceTokenProvider`az Azure AD integrált hitelesítést használja a token bekéréséhez. |
| `RunAs=App` | [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`felügyelt identitást használ a jogkivonat lekéréséhez. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Felhasználó által hozzárendelt identitás az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider`a felhasználó által hozzárendelt identitást használja a jogkivonat lekéréséhez. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Egyéni szolgáltatások hitelesítése | `KeyVaultCertificateSecretIdentifier`a tanúsítvány titkos azonosítója. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Szolgáltatásnév | `AzureServiceTokenProvider`tanúsítvány tetszetős get-t az Azure AD-től. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Szolgáltatásnév | `AzureServiceTokenProvider`tanúsítvány használatával kap token az Azure AD-től|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Szolgáltatásnév |`AzureServiceTokenProvider`titkos jogkivonatot használ az Azure AD-ből. |

## <a name="samples"></a>Példák

A `Microsoft.Azure.Services.AppAuthentication` tár működés közbeni megtekintéséhez tekintse meg az alábbi kódmintákat.

- [Felügyelt identitás használata titkos kulcs lekéréséhez az Azure Key Vaultból futásidőben](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Programozott módon üzembe helyez egy Azure Resource Manager-sablont egy felügyelt identitású Azure virtuális gépről.](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)

- [A .NET Core minta és a felügyelt identitás segítségével hívja meg az Azure-szolgáltatásokat egy Azure Linux virtuális gépről.](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)

## <a name="appauthentication-troubleshooting"></a>AppAuthentication – hibaelhárítás

### <a name="common-issues-during-local-development"></a>Közös problémák a helyi fejlesztés során

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Az Azure CLI nincs telepítve, nincs bejelentkezve, vagy nem rendelkezik a legújabb verzióval

*Futtassa az-fiók get-access-token,* hogy ha az Azure CLI mutat egy jogkivonatot az Ön számára. Ha azt mondja, **hogy nem található ilyen program,** telepítse az Azure [CLI legújabb verzióját.](/cli/azure/install-azure-cli?view=azure-cli-latest) Előfordulhat, hogy a rendszer arra kéri, hogy jelentkezzen be.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>Az AzureServiceTokenProvider nem találja az Azure CLI elérési útját

Az AzureServiceTokenProvider az Azure CLI-t az alapértelmezett telepítési helyeken keresi. Ha nem találja az Azure CLI-t, állítsa be az **AzureCLIPath** környezeti változót az Azure CLI telepítési mappájába. Az AzureServiceTokenProvider hozzáadja a környezeti változót a Path környezeti változóhoz.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Több fiók használatával van bejelentkezve az Azure CLI-be, ugyanaz a fiók több bérlőelőfizetéséhez is hozzáfér, vagy hozzáférés-megtagadási hibaüzenetet kap, amikor a helyi fejlesztés során próbál hívásokat kezdeményezni

Az Azure CLI használatával állítsa be az alapértelmezett előfizetést olyanra, amely rendelkezik a használni kívánt fiókkal. Az előfizetésnek ugyanabban a bérlőben kell lennie, mint az elérni kívánt erőforrásnak: **az fiókkészlet --subscription [subscription-id]**. Ha nem látható kimenet, akkor sikerült. Ellenőrizze, hogy a megfelelő fiók az alapértelmezett az **az fióklista**használatával.

### <a name="common-issues-across-environments"></a>Gyakori problémák a környezetekben

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Jogosulatlan hozzáférés, hozzáférés megtagadva, tiltott vagy hasonló hiba

A résztvevőnek nincs hozzáférése ahhoz az erőforráshoz, amelyet megpróbál elérni. Adjon hozzáférést a felhasználói fiókjához vagy az App Service MSI "Közreműködő" szolgáltatásához egy erőforráshoz. Melyik függ attól, hogy a mintát a helyi számítógépen futtatja-e, vagy az Azure-ban telepíti-e az App Service-be. Egyes erőforrások, például a kulcstartók, saját [hozzáférési szabályzatokkal](https://docs.microsoft.com/azure/key-vault/secure-your-key-vault#data-plane-and-access-policies) is rendelkeznek, amelyeket a rendszerbiztonsági tagoknak, például a felhasználóknak, az alkalmazásoknak és a csoportoknak a hozzáférési engedélyek használatával is használhat.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Gyakori problémák az Azure App Service-ben való üzembe helyezéskor

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>A felügyelt identitás nincs beállítva az App Service-ben

Ellenőrizze a környezeti változók at MSI_ENDPOINT és MSI_SECRET létezik [kudu debug konzol](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Ha ezek a környezeti változók nem léteznek, felügyelt identitás nem engedélyezett az App Service.If these environment variables don't exist, Managed Identity isn't enabled on the App Service.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Gyakori problémák az IIS-szel helyileg történő telepítésesetén

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Nem lehet lekérni a tokeneket az alkalmazás hibakeresésekénél az IIS-ben

Alapértelmezés szerint az AppAuth az IIS-ben más felhasználói környezetben fut. Ezért nem rendelkezik hozzáféréssel a fejlesztői identitás használatához a hozzáférési jogkivonatok lekéréséhez. Az IIS a következő két lépéssel konfigurálható a felhasználói környezettel való futtatásra:
- Konfigurálja úgy az alkalmazáskészletet, hogy a webalkalmazás az aktuális felhasználói fiókként fusson. További információk [itt](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Állítsa a "setProfileEnvironment" beállítást "True" értékre. További információ [itt](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Nyissa meg a %windir%\System32\inetsrv\config\applicationHost.config webhelyet.
    - Keressen rá a "setProfileEnvironment" kifejezésre. Ha "Hamis" értékre van állítva, módosítsa "True" értékre. Ha nincs jelen, adja hozzá attribútumként a processModel/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironmentelemhez ( ), és állítsa "Igaz" értékre.

- További információ [az Azure-erőforrások felügyelt identitásairól.](../../active-directory/managed-identities-azure-resources/index.yml)
- További információ az [Azure AD hitelesítési forgatókönyveiről.](../../active-directory/develop/active-directory-authentication-scenarios.md)
