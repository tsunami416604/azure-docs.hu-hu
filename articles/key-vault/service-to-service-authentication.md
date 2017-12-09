---
title: "Szolgáltatások közötti hitelesítés az Azure Key Vaultba .NET használatával"
description: "Az Azure Key Vault használatával a .NET hitelesítés használható a Microsoft.Azure.Services.AppAuthentication könyvtár."
keywords: "az Azure-kulcstároló helyi hitelesítő adatok"
author: lleonard-msft
manager: mbaldwin
services: key-vault
ms.author: alleonar
ms.date: 11/15/2017
ms.topic: article
ms.prod: 
ms.service: microsoft-keyvault
ms.technology: 
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: bff4b15ca2f1c985c4b4e27d159adaa5fd039553
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Szolgáltatások közötti hitelesítés az Azure Key Vaultba .NET használatával

Hitelesítést végeznek az Azure Key Vault, az Azure Active Directory (AD) hitelesítő adatát, vagy egy közös titkot, vagy egy tanúsítvány szükséges. Az ilyen hitelesítő adatok kezelése nehéz lehet, és szeretné a hitelesítő adatokat az alkalmazásba vagy a konfigurációs fájlokban ot tempting.

A `Microsoft.Azure.Services.AppAuthentication` a .NET könyvtár egyszerűbbé teszi a probléma. A fejlesztői hitelesítő adatok helyi fejlesztési során hitelesítésére használ. A megoldás ezt követően telepíti az Azure-ba, amikor a szalagtár automatikusan vált alkalmazás hitelesítő adatait.  

Helyi fejlesztési fejlesztői hitelesítő adatokkal nem biztonságosabb, mert nem kell létrehozni az Azure AD hitelesítő adatait, vagy fájlmegosztási hitelesítő adatokat a fejlesztők közötti.

A `Microsoft.Azure.Services.AppAuthentication` könyvtár kezeli a hitelesítési automatikusan, viszont lehetővé teszi a megoldás ahelyett, hogy a hitelesítő adatok összpontosítanak.

A `Microsoft.Azure.Services.AppAuthentication` könyvtár támogatja a helyi fejlesztése a Microsoft Visual Studio, az Azure parancssori felület vagy az Azure AD integrált hitelesítést. Azure App Services vagy egy Azure virtuális gép (VM) telepítésekor automatikusan használja-e a szalagtár [Szolgáltatásidentitás felügyelt](/azure/active-directory/msi-overview) (MSI). Kód vagy a konfigurációs módosítások nélküli szükség. A kódtár emellett támogatja az Azure AD közvetlenül is felhasználható [ügyfél hitelesítő adatait](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) MSI esetén nem érhető el, vagy ha a fejlesztői biztonsági környezet nem határozható meg helyi fejlesztése során.

<a name="asal"></a>
## <a name="using-the-library"></a>A könyvtár használata

.NET-alkalmazások, a legegyszerűbben úgy működik, a felügyelt szolgáltatás Identity (MSI) keresztül történik a `Microsoft.Azure.Services.AppAuthentication` csomag. Itt található első lépések:

1. Adjon hozzá egy hivatkozást, a [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) NuGet-csomagot, hogy az alkalmazást.

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

A `AzureServiceTokenProvider` osztály gyorsítótárazza a jogkivonatot a memóriában, és lekéri az Azure AD lejárata előtt. Ennek következtében már nem rendelkezik a lejárati hívása előtt ellenőrizze a `GetAccessTokenAsync` metódust. A metódus csak akkor hívja, amikor a jogkivonat használni kívánt. 

A `GetAccessTokenAsync` metódus használatához egy erőforrás-azonosítót. További tudnivalókért lásd: [amely Azure services támogatja a felügyelt Szolgáltatásidentitás](https://docs.microsoft.com/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity).


<a name="samples"></a>
## <a name="samples"></a>Példák

A következő minták megjelenítése a `Microsoft.Azure.Services.AppAuthentication` könyvtár működés közben:

1. [Egy felügyelt szolgáltatás Identity (MSI) segítségével az Azure Key Vault futásidőben titkos kulcs lekérése](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Programozott módon telepítheti az Azure virtuális gép egy olyan MSI Csomaghoz, az Azure Resource Manager sablon](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [A .NET Core a minta és az MSI-fájl használatával felelnek meg Azure-szolgáltatásokat, és az Azure Linux virtuális gép](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Helyi fejlesztési hitelesítés

Helyi fejlesztési van két elsődleges hitelesítési forgatókönyvei:

- [Azure-szolgáltatásokhoz való hitelesítése](#authenticating-to-azure-services)
- [Egyéni szolgáltatások való hitelesítése](#authenticating-to-custom-services)

Itt ismerje meg minden egyes forgatókönyv és a támogatott eszközök a követelményeknek.


### <a name="authenticating-to-azure-services"></a>Azure-szolgáltatásokhoz való hitelesítése

Helyi gépek nem támogatják a kezelt Service Identity (MSI).  Ennek eredményeképpen a `Microsoft.Azure.Services.AppAuthentication` könyvtár a fejlesztői hitelesítő adatokat használja a helyi fejlesztési környezetben történő futását. A megoldást már telepítették az Azure-ba, ha a szalagtár MSI váltson át az OAuth 2.0 ügyfél hitelesítő adat grant folyamat használja.  Ez azt jelenti, hogy nélkül lehet tesztelni ugyanazt a kódot helyileg és távolról aggódjon.

Helyi fejlesztési `AzureServiceTokenProvider` jogkivonatok használatával beolvassa **Visual Studio**, **Azure parancssori felület** (CLI), vagy **az Azure AD integrált hitelesítés**. Minden egyes beállítás rendszer sorban egymás után, és a könyvtár módszerrel az első sikeres. Ha nincs lehetőség akkor működik, egy `AzureServiceTokenProviderException` kivétel részletes információkkal.

### <a name="authenticating-with-visual-studio"></a>A Visual Studio hitelesítése

Visual Studio használata esetén győződjön meg:

1. Telepített [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) vagy újabb.

2. A [a Visual Studio alkalmazás hitelesítési kiterjesztés](https://go.microsoft.com/fwlink/?linkid=862354) telepítve van.
 
3. Bejelentkezett a Visual Studio és a kiválasztott egy fiókot a helyi fejlesztési célra. Használjon **eszközök**&nbsp;>&nbsp;**beállítások**&nbsp;>&nbsp;**Azure szolgáltatás hitelesítési**egy helyi fejlesztési fiók kiválasztása. 

Ha az Visual Studio, például a jogkivonat-szolgáltató fájl kapcsolatos hibákat problémát tapasztal gondosan tekintse át az alábbi lépéseket. 

Is szükségessé válhat a fejlesztői token újból hitelesítésre.  Ehhez nyissa meg a **eszközök**&nbsp;>&nbsp;**beállítások**>**Azure&nbsp;szolgáltatás&nbsp;hitelesítés**  , és keressen egy **újra hitelesíteni** hivatkozásra a kiválasztott fiók alatt.  Válassza ki azt a hitelesítéshez. 

### <a name="authenticating-with-azure-cli"></a>Az Azure CLI hitelesítése

Helyi fejlesztési Azure CLI használata:

1. Telepítés [Azure CLI v2.0.12](/cli/azure/install-azure-cli) vagy újabb. Korábbi verziók frissítése. 

2. Használjon **az bejelentkezési** bejelentkezni az Azure-bA.

Használjon `az account get-access-token` való hozzáférés ellenőrzése.  Ha hibaüzenetet kap, győződjön meg arról, hogy az 1. lépésben sikeresen befejeződött-e. 

Ha az Azure parancssori felület nem települ az alapértelmezett mappát, jelentéskészítési hiba jelenhet `AzureServiceTokenProvider` Azure CLI használata az elérési út nem található.  Használja a **AzureCLIPath**környezeti változót az Azure parancssori felület telepítési mappa megadása. `AzureServiceTokenProvider`a megadott könyvtárat ad a **AzureCLIPath** környezeti változót a **elérési** környezeti változó, ha szükséges.

Ha be van jelentkezve Azure parancssori felület használatával több fiókot, vagy a fiókjának van hozzáférési joga több előfizetéssel, meg kell adnia a használandó előfizetéshez.  Ehhez használja:

```
az account set --subscription <subscription-id>
```

Ez a parancs csak a hiba a kimenetet.  A jelenlegi fiókot beállítások ellenőrzéséhez használja:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Az Azure AD-integráció hitelesítésről

Az Azure AD-alapú hitelesítés használata esetén ellenőrizze, hogy:

- A helyszíni active directory [az Azure AD szinkronizálások](/azure/active-directory/connect/active-directory-aadconnect).

- A kód egy tartományhoz csatlakoztatott számítógépen fut.


### <a name="authenticating-to-custom-services"></a>Egyéni szolgáltatások való hitelesítése

Ha a hívások Azure-szolgáltatásokkal, az előző lépések működik, mert a Azure-szolgáltatások engedélyezése a felhasználók és az alkalmazások elérésére.  

Egy szolgáltatás, amely meghívja a egy egyéni szolgáltatás létrehozásakor helyi fejlesztési hitelesítéshez használandó Azure AD ügyfél hitelesítő adatait.  Két lehetőség áll rendelkezésre: 

1.  Egy egyszerű szolgáltatás használatával jelentkezzen be Azure:

    1.  [Hozzon létre egy egyszerű](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Az Azure parancssori felület használatával jelentkezzen be:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Mivel a szolgáltatás egyszerű nincs hozzáférése az előfizetéshez, használja a `--allow-no-subscriptions` argumentum.

2.  Környezeti változók segítségével adja meg a szolgáltatás egyszerű adatait.  További információkért lásd: [használatával egy egyszerű alkalmazást futtató](#running-the-application-using-a-service-principal).

Az Azure-ba, bejelentkezés után `AzureServiceTokenProvider` egy helyi fejlesztési-jogkivonatot lekérdezni a szolgáltatásnevet használja.

Ez csak a helyi fejlesztési vonatkozik. A megoldás az Azure-bA telepítésekor MSI hitelesítési vált, a könyvtárban.

<a name="msi"></a>
## <a name="running-the-application-using-a-managed-service-identity"></a>Az alkalmazás használatával felügyelt Szolgáltatásidentitás futtatása 

Amikor futtatja a kódot az Azure App Service vagy egy Azure virtuális Gépen az MSI engedélyezve van, a szalagtár automatikusan használja Szolgáltatásidentitás felügyelt. Nincs kód módosításokra szükség. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Egy egyszerű szolgáltatás használatával az alkalmazás futtatása 

Elképzelhető, hogy az Azure AD-ügyfél hitelesítő adatát hitelesítéséhez létrehozásához szükséges. Gyakori példák:

1. A kódot egy helyi fejlesztési környezet, hanem a fejlesztői identitás alatt nem fut.  A Service Fabric, például használja a [NetworkService fiókot](/azure/service-fabric/service-fabric-application-secret-management) helyi fejlesztési.
 
2. A kódot egy helyi fejlesztési környezetet futtatja, és egy egyéni szolgáltatásban, ezért nem használható a fejlesztői identitás hitelesítést. 
 
3. A kód fut, amely még nem támogatja a felügyelt Szolgáltatásidentitás, például az Azure Batch egy Azure számítási erőforrás.

A tanúsítvány használatára az Azure AD-ba való bejelentkezéshez:

1. Hozzon létre egy [egyszerű szolgáltatástanúsítvány](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. A tanúsítvány központi telepítése vagy a _LocalMachine_ vagy _CurrentUser_ tárolja. 

3. Nevű környezeti változó értéke **AzureServicesAuthConnectionString** számára:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={LocalMachine or CurrentUser}.
    ```
 
    Cserélje le _{AppId}_, _{TenantId}_, és _{ujjlenyomat}_ 1. lépésben létrehozott értékekkel.

    **CertificateStoreLocation** kell lennie, vagy _CurrentUser_ vagy _LocalMachine_a telepítési terv alapján.

4. Futtassa az alkalmazást. 

Jelentkezzen be az Azure AD megosztott titkos hitelesítő adat:

1. Hozzon létre egy [szolgáltatás egyszerű jelszó](/azure/azure-resource-manager/resource-group-authenticate-service-principal) és a Key Vault hozzáférést biztosítania. 

2. Nevű környezeti változó értéke **AzureServicesAuthConnectionString** számára:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}. 
    ```

    Cserélje le _{AppId}_, _{TenantId}_, és _{ClientSecret}_ 1. lépésben létrehozott értékekkel.

3. Futtassa az alkalmazást. 

Ha minden készen áll megfelelően, nincs további kód módosítására szükség.  `AzureServiceTokenProvider`a környezeti változó és a tanúsítvány használ az Azure AD-hitelesítéséhez. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Kapcsolati karakterlánc-támogatás

Alapértelmezés szerint `AzureServiceTokenProvider` többféle módszer használja a-jogkivonatot lekérdezni. 

A folyamat szabályozásához használja a kapcsolat-karakterlánc a `AzureServiceTokenProvider` konstruktor vagy a megadott a *AzureServicesAuthConnectionString* környezeti változó. 

A következő beállításokat támogatja:

| Kapcsolat&nbsp;karakterlánc&nbsp;beállítás | Forgatókönyv | Megjegyzések|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Helyi fejlesztési | AzureServiceTokenProvider AzureCli jogkivonatának beszerzéséhez használja. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Helyi fejlesztési | AzureServiceTokenProvider Visual Studio használatával szerezze be a tokent. |
| `RunAs=CurrentUser;` | Helyi fejlesztési | AzureServiceTokenProvider az Azure AD integrált hitelesítés használatával szerezze be a tokent. |
| `RunAs=App;` | Felügyeltszolgáltatás-identitás | AzureServiceTokenProvider felügyelt szolgáltatás identitás használatával szerezze be a tokent. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Egyszerű szolgáltatásnév | `AzureServiceTokenProvider`az Azure ad-token beszerzése a tanúsítványt használja. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Egyszerű szolgáltatásnév | `AzureServiceTokenProvider`tanúsítványt használ az Azure AD-token beszerzése|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Egyszerű szolgáltatásnév |`AzureServiceTokenProvider`az Azure AD-token beszerzése a titkos kulcs használatával. |


## <a name="next-steps"></a>Következő lépések

- További információ [Szolgáltatásidentitás felügyelt](/azure/app-service/app-service-managed-service-identity).

- Ismerje meg a különböző módjai [hitelesítését és engedélyezését alkalmazások](/azure/app-service/app-service-authentication-overview).

- További tudnivalók az Azure AD [hitelesítési forgatókönyvek](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application).