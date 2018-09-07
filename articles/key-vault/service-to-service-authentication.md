---
title: Szolgáltatások közötti hitelesítés az Azure Key Vaultba .NET használatával
description: A Microsoft.Azure.Services.AppAuthentication könyvtár segítségével hitelesíti az Azure Key Vaultba .NET használatával.
keywords: az Azure key-vault helyi hitelesítő adatok
author: bryanla
manager: mbaldwin
services: key-vault
ms.author: bryanla
ms.date: 09/05/2018
ms.topic: article
ms.prod: ''
ms.service: key-vault
ms.technology: ''
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: bbab141a7d0515ac1bd0726f861089befb1f4cf0
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022523"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Szolgáltatások közötti hitelesítés az Azure Key Vaultba .NET használatával

Hitelesítéséhez az Azure Key Vaultba, szüksége van egy Azure Active Directory (AD) hitelesítő adatok, vagy egy közös titkos kulcsot, vagy egy tanúsítványt. Az ilyen hitelesítő adatok kezelése nehézkes lehet és csábító szeretné a hitelesítő adatok egy alkalmazásba fel őket a forrás- vagy konfigurációs fájlok.

A `Microsoft.Azure.Services.AppAuthentication` .NET-kódtár leegyszerűsíti a probléma. Helyi fejlesztés során hitelesítsék magukat a fejlesztői hitelesítő adatokat használ. A megoldás ezt követően telepíti az Azure-ba, amikor a tár automatikusan vált, amennyiben az alkalmazás hitelesítő adatait.  

Helyi fejlesztés során fejlesztői hitelesítő adatok használatával használata biztonságosabb, mert nem kell létrehozni az Azure AD hitelesítő adatait, vagy fájlmegosztási hitelesítő adatokat a fejlesztők közötti.

A `Microsoft.Azure.Services.AppAuthentication` könyvtár kezeli a hitelesítés automatikusan, amely viszont lehetővé teszi, hogy a megoldás ahelyett, hogy a hitelesítő adatok összpontosíthat.

A `Microsoft.Azure.Services.AppAuthentication` kódtár támogatja a helyi fejlesztés a Microsoft Visual Studio, az Azure parancssori felület vagy az Azure AD integrált hitelesítést. Azure App Services vagy egy Azure virtuális gép (VM) telepítésekor automatikusan használja-e a szalagtár [felügyelt Azure-szolgáltatások identitásainak](/azure/active-directory/msi-overview). Nincs kódírásra vagy konfigurálásra módosításokra szükség. A kódtár emellett támogatja az Azure AD közvetlenül is felhasználható [ügyfél-hitelesítő adatok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) Ha egy felügyelt identitás nem érhető el, vagy ha a fejlesztői biztonsági környezetben nem lehet megállapítani a helyi fejlesztés során.

<a name="asal"></a>
## <a name="using-the-library"></a>A kódtár használatával

A .NET-alkalmazásokban, a legegyszerűbb módszer egy felügyelt identitás használata keresztül, a `Microsoft.Azure.Services.AppAuthentication` csomagot. Első lépések a következő:

1. Vegyen fel egy hivatkozást a [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) az alkalmazás NuGet-csomagot.

2. Adja hozzá a következő kódot:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // ...

    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
    azureServiceTokenProvider.KeyVaultTokenCallback));

    // or

    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync(
       "https://management.azure.com/").ConfigureAwait(false);
    ```

A `AzureServiceTokenProvider` osztály gyorsítótárazza a memóriában, és lekéri az Azure ad-ből lejárata előtt. Ennek következtében, már nem rendelkezik a lejárati hívása előtt ellenőrizze a `GetAccessTokenAsync` metódust. Ha meg szeretné használni a jogkivonat csak a metódust hívja. 

A `GetAccessTokenAsync` módszerhez egy erőforrás-azonosítója. További tudnivalókért lásd: [mely Azure-szolgáltatások felügyelt identitások támogatása az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity).


<a name="samples"></a>
## <a name="samples"></a>Példák

A következő minták megjelenítése a `Microsoft.Azure.Services.AppAuthentication` könyvtár működés közben:

1. [Titkos kulcs lekérése az Azure Key Vault futásidőben egy felügyelt identitás használata](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Programozott módon telepítheti az Azure Resource Manager-sablon, egy felügyelt identitás az Azure virtuális gépből](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Minta .NET Core és a egy felügyelt identitás használata Azure-szolgáltatások meghívása az Azure Linux virtuális](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Helyi fejlesztés hitelesítés

A helyi fejlesztési két forgatókönyv közül választhat az elsődleges hitelesítés:

- [Hitelesítés az Azure-szolgáltatások](#authenticating-to-azure-services)
- [Egyéni szolgáltatások való hitelesítése](#authenticating-to-custom-services)

Itt megismerheti, minden egyes forgatókönyv és a támogatott eszközök a követelményeknek.


### <a name="authenticating-to-azure-services"></a>Hitelesítés az Azure-szolgáltatások

Helyi gépek nem támogatják a felügyelt identitások az Azure-erőforrásokhoz.  Ennek eredményeképpen a `Microsoft.Azure.Services.AppAuthentication` könyvtár a fejlesztői hitelesítő adatait használja a helyi fejlesztési környezet futtatásához. Telepítésekor a megoldás az Azure-ba, a tár használja egy felügyelt identitás váltson át egy OAuth 2.0 ügyfél-hitelesítő adatok megadási folyamatában.  Ez azt jelenti, hogy nélkül lehet tesztelni ugyanazt a kódot helyileg és távolról aggódjon.

A helyi fejlesztési `AzureServiceTokenProvider` jogkivonatok segítségével beolvassa **Visual Studio**, **Azure parancssori felület** (CLI), vagy **az Azure AD integrált hitelesítés**. Minden beállítást a rendszer megpróbálkozik egymás után, és a tár használja az első lehetőség, hogy sikeres volt. Ha nincs lehetőség a működik, egy `AzureServiceTokenProviderException` részletes információkkal együtt, kivételhiba fordul elő.

### <a name="authenticating-with-visual-studio"></a>A Visual Studióval hitelesítése

Visual Studio használata esetén győződjön meg:

1. Telepítette [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) vagy újabb.

2. A [for Visual Studio App hitelesítési kiterjesztés](https://go.microsoft.com/fwlink/?linkid=862354) telepítve van.
 
3. A Visual Studio bejelentkezett, és kiválasztott egy fiókot a helyi fejlesztési célra. Használat **eszközök**&nbsp;>&nbsp;**beállítások**&nbsp;>&nbsp;**Azure-szolgáltatás hitelesítési**, válasszon ki egy helyi fejlesztői fiókot. 

Ha hibákba ütközne a Visual studióval, például a jogkivonat-szolgáltató fájl kapcsolatos hibákat el figyelmesen ezeket a lépéseket. 

Azt is szükség lehet hitelesítse magát újra a fejlesztői jogkivonatot.  Ehhez nyissa meg **eszközök**&nbsp;>&nbsp;**beállítások**>**Azure&nbsp;szolgáltatás&nbsp;hitelesítés**  , és keressen egy **újra hitelesíteni kell** a kiválasztott fiók alatti hivatkozásra.  Válassza ki azt a hitelesítéshez. 

### <a name="authenticating-with-azure-cli"></a>Hitelesítés az Azure CLI-vel

Helyi fejlesztés az Azure CLI használata:

1. Telepítés [Azure CLI-vel v2.0.12](/cli/azure/install-azure-cli) vagy újabb. Korábbi verziók frissítése. 

2. Használat **az bejelentkezési** bejelentkezni az Azure-bA.

Használat `az account get-access-token` hozzáférés ellenőrzéséhez.  Ha hibaüzenetet kap, ellenőrizze, hogy 1. lépés sikeresen befejeződött-e. 

Ha az alapértelmezett címtár az Azure CLI nincs telepítve, akkor előfordulhat, hogy megjelenik egy hibaüzenet, jelentéskészítési `AzureServiceTokenProvider` Azure CLI-hez az elérési út nem található.  Használja a **AzureCLIPath**környezeti változót, az Azure CLI telepítési mappa megadása. `AzureServiceTokenProvider` hozzáadja a megadott könyvtárban a **AzureCLIPath** környezeti változót, a **elérési út** környezeti változót, ha szükséges.

Ha bejelentkezett az Azure CLI-t több fiókot, vagy a fiók több előfizetést is hozzáféréssel rendelkezik, adja meg az adott előfizetés használni szeretne.  Ehhez használja:

```
az account set --subscription <subscription-id>
```

Ez a parancs létrehoz kimeneti csak a hibával kapcsolatban.  A jelenlegi beállítások ellenőrzéséhez használja:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Az Azure AD-integráció a hitelesítésről

Az Azure AD-hitelesítés használatához ellenőrizze, hogy:

- A helyszíni active Directoryban [szinkronizálja az Azure AD](/azure/active-directory/connect/active-directory-aadconnect).

- A kód egy tartományhoz csatlakoztatott számítógépen fut.


### <a name="authenticating-to-custom-services"></a>Egyéni szolgáltatások való hitelesítése

Ha egy szolgáltatás hívások Azure-szolgáltatások, az előző lépések működik, mert a Azure-szolgáltatások lehetővé teszik a felhasználók és az alkalmazások elérését.  

Egy szolgáltatás, amely meghív egy egyéni szolgáltatás létrehozásakor használja az Azure AD ügyfél-hitelesítő adatok a helyi fejlesztési hitelesítéshez.  Két lehetőség van: 

1.  Használjon szolgáltatásnevet az Azure-ba való bejelentkezéshez:

    1.  [Egyszerű szolgáltatás létrehozása](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Azure CLI használatával jelentkezzen be:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Mivel az egyszerű szolgáltatás nem jogosult az elérésére, egy előfizetésre, használja a `--allow-no-subscriptions` argumentum.

2.  A környezeti változók használatával adja meg a szolgáltatás egyszerű adatait.  További információkért lásd: [használatával egy egyszerű szolgáltatást az alkalmazás futtatása](#running-the-application-using-a-service-principal).

Az Azure-ba, bejelentkezés után `AzureServiceTokenProvider` használja az egyszerű szolgáltatás a helyi fejlesztési jogkivonat beszerzésére.

Ez csak helyi fejlesztési vonatkozik. Telepítésekor a megoldás az Azure-ba, a tár átvált egy felügyelt identitás a hitelesítéshez.

<a name="msi"></a>
## <a name="running-the-application-using-managed-identity"></a>Az alkalmazás felügyelt identitás használatával fut 

Amikor futtatja a kódot egy Azure App Service-ben vagy egy Azure virtuális Gépen engedélyezve van egy felügyelt identitás, a tár automatikusan használja a felügyelt identitás. Kódmódosítás nélkül szükség. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Egyszerű szolgáltatás használatával az alkalmazás futtatása 

Elképzelhető, hogy hitelesítéséhez az Azure AD-ügyfél hitelesítő adatok létrehozásához szükséges. Gyakori példák:

1. A kód a helyi fejlesztési környezetet, de nem a fejlesztői identitás alatt fut.  A Service Fabric, például használja a [NetworkService fiók](/azure/service-fabric/service-fabric-application-secret-management) a helyi fejlesztési.
 
2. A kód fut a helyi fejlesztési környezetet, és a egy egyéni szolgáltatás, ezért nem használható a fejlesztői személyazonosság hitelesítéséhez. 
 
3. A kódja fut. a még nem támogatja a felügyelt identitások Azure-erőforrások, például az Azure Batch számítási Azure-erőforrások.

A tanúsítvány használata az Azure AD-ba való bejelentkezéshez:

1. Hozzon létre egy [szolgáltatásnév](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. A tanúsítvány központi telepítése vagy a _LocalMachine_ vagy _CurrentUser_ tárolásához. 

3. Nevű környezeti változó értéke **AzureServicesAuthConnectionString** való:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={LocalMachine or CurrentUser}
    ```
 
    Cserélje le _{AppId}_, _{Bérlőazonosító}_, és _{Thumbprint}_ az 1. lépésben létrehozott értékekkel.

    **CertificateStoreLocation** kell lennie, vagy _CurrentUser_ vagy _LocalMachine_a telepítési terv alapján.

4. Futtassa az alkalmazást. 

Jelentkezzen be az Azure AD közös titkos hitelesítő adat:

1. Hozzon létre egy [egy jelszót a szolgáltatásnév](/azure/azure-resource-manager/resource-group-authenticate-service-principal) , és adja meg azt a Key Vaulthoz való hozzáférése. 

2. Nevű környezeti változó értéke **AzureServicesAuthConnectionString** való:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Cserélje le _{AppId}_, _{Bérlőazonosító}_, és _{ClientSecret}_ az 1. lépésben létrehozott értékekkel.

3. Futtassa az alkalmazást. 

Miután minden van beállítva, további kódmódosítás nélkül szükség.  `AzureServiceTokenProvider` a környezeti változót, és a tanúsítvány használatával az Azure AD-hitelesítést. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Kapcsolati karakterlánc-támogatás

Alapértelmezés szerint `AzureServiceTokenProvider` több módszert használ a jogkivonat beszerzésére. 

A folyamat szabályozásához átadott kapcsolati karakterlánc használata a `AzureServiceTokenProvider` konstruktor vagy a megadott a *AzureServicesAuthConnectionString* környezeti változót. 

A következő beállítások támogatottak:

| Kapcsolat&nbsp;karakterlánc&nbsp;lehetőség | Forgatókönyv | Megjegyzések|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Helyi fejlesztés | AzureServiceTokenProvider AzureCli használatával szerezze be a tokent. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Helyi fejlesztés | AzureServiceTokenProvider token beszerzése a Visual Studióval. |
| `RunAs=CurrentUser;` | Helyi fejlesztés | AzureServiceTokenProvider beszerezni a jogkivonatot az Azure AD integrált hitelesítést használ. |
| `RunAs=App;` | Felügyelt identitások az Azure-erőforrásokhoz | AzureServiceTokenProvider egy felügyelt identitás használatával szerezze be a tokent. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Szolgáltatásnév | `AzureServiceTokenProvider` a tanúsítványt használja, szerezze be a tokent az Azure ad-ből. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Szolgáltatásnév | `AzureServiceTokenProvider` tanúsítványt használ, szerezze be a tokent az Azure ad-ből|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Szolgáltatásnév |`AzureServiceTokenProvider` titkos kulcs használatával szerezze be a tokent az Azure ad-ből. |


## <a name="next-steps"></a>További lépések

- Tudjon meg többet [felügyelt identitások az Azure-erőforrások](/azure/app-service/app-service-managed-service-identity).

- Ismerje meg, különböző módjait [hitelesítése és engedélyezése az alkalmazások](/azure/app-service/app-service-authentication-overview).

- További információ az Azure AD [hitelesítési forgatókönyvek](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application).
