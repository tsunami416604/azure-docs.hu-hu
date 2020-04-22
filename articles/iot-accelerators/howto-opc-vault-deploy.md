---
title: Az OPC Vault tanúsítványkezelési szolgáltatásának telepítése - Azure | Microsoft dokumentumok
description: Az OPC Vault tanúsítványkezelési szolgáltatásának telepítése a nulláról.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 7ee186684b702a42335c6e1a7832cc5c761a69d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686941"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Az OPC Vault tanúsítványkezelési szolgáltatásának létrehozása és üzembe helyezése

Ez a cikk bemutatja, hogyan telepítheti az OPC Vault tanúsítványkezelési szolgáltatás az Azure-ban.

> [!NOTE]
> További információt a GitHub [OPC Vault tárháza](https://github.com/Azure/azure-iiot-opc-vault-service)tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

### <a name="install-required-software"></a>A szükséges szoftverek telepítése

Jelenleg a build és üzembe helyezési művelet a Windows rendszerre korlátozódik.
A minták mind a C# .NET Standard, amely a szolgáltatás és a minták üzembe helyezéshez kell készíteni.
A .NET Standard minden szükséges eszköze a . Lásd: [A .NET Core ismerkedése](https://docs.microsoft.com/dotnet/articles/core/getting-started).

1. [Telepítse a .NET Core 2.1+ programot.][dotnet-install]
2. [Telepítse a Dockert][docker-url] (nem kötelező, csak akkor, ha a helyi Docker-build szükséges).
4. Telepítse a [PowerShell Hez használt Azure parancssori eszközeit.][powershell-install]
5. Regisztráljon egy [Azure-előfizetésre.][azure-free]

### <a name="clone-the-repository"></a>A tárház klónozása

Ha még nem tette meg, klónozza ezt a GitHub-tárházat. Nyisson meg egy parancssort vagy terminált, és futtassa a következőket:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Másik lehetőségként klónozhathatja a tárta közvetlenül a Visual Studio 2017.Alternatively, you can clone the repo directly in Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Az Azure-szolgáltatás létrehozása és üzembe helyezése Windows rendszeren

A PowerShell-parancsfájl oka az OPC Vault mikroszolgáltatás és az alkalmazás üzembe helyezésének egyszerű módja.

1. Nyisson meg egy PowerShell-ablakot a tárház gyökér. 
3. Nyissa meg a `cd deploy`központi telepítés mappát.
3. Olyan nevet `myResourceGroup` válasszon, amely valószínűleg nem okoz ütközést más telepített weblapokkal. Lásd a cikk későbbi, "A webhely neve már használatban van" című részét.
5. Indítsa el `.\deploy.ps1` a telepítést interaktív telepítéssel, vagy adjon meg egy teljes parancssort:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Ha azt tervezi, hogy dolgozzon ezzel a központi telepítéssel, adja hozzá, `-development 1` hogy a Swagger felhasználói felület, és a hibakeresési buildek üzembe helyezéséhez.
6. Kövesse a parancsfájlutasításait, hogy jelentkezzen be az előfizetésbe, és további információkat adjon meg.
9. A sikeres létrehozási és üzembe helyezési művelet után a következő üzenetnek kell megjelennie:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > Problémák esetén olvassa el a cikk későbbi, "Központi telepítési hibák elhárítása" című szakaszát.

8. Nyissa meg kedvenc böngészőjét, és nyissa meg az alkalmazáslapját:`https://myResourceGroup.azurewebsites.net`
8. Adjon a webalkalmazás és az OPC Vault mikroszolgáltatás néhány percig, hogy felmelegedjen a telepítés után. Előfordulhat, hogy a kezdőlap első használatkor akár egy percig is lefagy, amíg meg nem kapja az első válaszokat.
11. A Swagger API-t meg nyithatja:`https://myResourceGroup-service.azurewebsites.net`
13. Ha egy helyi GDS-kiszolgálót szeretne `.\myResourceGroup-gds.cmd`indítani a dotnet tel, indítsa el a programot. A Docker, `.\myResourceGroup-dockergds.cmd`indítsa el .

Lehetőség van egy build pontosan ugyanazokat a beállításokat újratelepíteni. Vegye figyelembe, hogy egy ilyen művelet megújítja az összes alkalmazástitkos kulcsokat, és előfordulhat, hogy az Azure Active Directory (Azure AD) alkalmazásregisztrációk egyes beállításait alaphelyzetbe állítja.

Lehetőség van csak a webes alkalmazás bináris fájljainak újratelepítésére is. A paraméterrel `-onlyBuild 1`a szolgáltatás és az alkalmazás új zip-csomagjai kerülnek a webes alkalmazásokba.

A sikeres telepítés után elkezdheti használni a szolgáltatásokat. Lásd: [Az OPC Vault tanúsítványkezelési szolgáltatásának kezelése.](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>A szolgáltatások törlése az előfizetésből

Ezt a következőképpen teheti meg:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg azt az erőforráscsoportot, amelyben a szolgáltatást telepítették.
3. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.
4. Rövid idő elteltével az összes üzembe helyezett szolgáltatásösszetevő törlődik.
5. Nyissa meg az **Azure Active Directory** > **alkalmazásregisztrációit.**
6. Minden telepített erőforráscsoporthoz három regisztrációnak kell szerepelnie. A regisztrációk neve a `resourcegroup-client` `resourcegroup-module`következő: , , `resourcegroup-service`. Törölje az egyes regisztrációkat külön-külön.

Most az összes üzembe helyezett összetevő eltávolításra kerül.

## <a name="troubleshooting-deployment-failures"></a>Telepítési hibák elhárítása

### <a name="resource-group-name"></a>Erőforráscsoport neve

Használjon rövid és egyszerű erőforráscsoport nevet. A név az erőforrások és a szolgáltatás URL-előtagja elnevezésére is szolgál. Mint ilyen, meg kell felelnie az erőforrás-elnevezési követelményeknek.  

### <a name="website-name-already-in-use"></a>A webhely neve már használatban van

Lehetséges, hogy a webhely neve már használatban van. Másik erőforráscsoport nevet kell használnia. A központi telepítési parancsfájl által használt állomásnevek a következők: https:\//resourcegroupname.azurewebsites.net és https:\//resourgroupname-service.azurewebsites.net.
A szolgáltatások más nevei a rövid névkiírások kombinációjával épülnek fel, és nem valószínű, hogy ütköznek más szolgáltatásokkal.

### <a name="azure-ad-registration"></a>Azure AD-regisztráció 

A központi telepítési parancsfájl három Azure AD-alkalmazást próbál regisztrálni az Azure AD-ben. A ttól függően, hogy a kiválasztott Azure AD-bérlő engedélyeket, ez a művelet sikertelen lehet. Két lehetőség érhető el:

- Ha egy Azure AD-bérlőt választott a bérlők listájából, indítsa újra a parancsfájlt, és válasszon egy másikat a listából.
- Másik lehetőségként üzembe helyezhet egy privát Azure AD-bérlőt egy másik előfizetésben. Indítsa újra a parancsfájlt, és válassza a használatát.

## <a name="deployment-script-options"></a>Központi telepítési parancsfájl beállításai

A parancsfájl a következő paramétereket veszi figyelembe:


```
-resourceGroupName
```

Ez lehet egy meglévő vagy egy új erőforráscsoport neve.

```
-subscriptionId
```


Ez az az előfizetés-azonosító, ahol az erőforrások üzembe kerülnek. Ez nem kötelező.

```
-subscriptionName
```


Másik lehetőségként használhatja az előfizetés nevét.

```
-resourceGroupLocation
```


Ez egy erőforráscsoport helye. Ha meg van adva, ez a paraméter megpróbál új erőforráscsoportot létrehozni ezen a helyen. Ez a paraméter is nem kötelező.


```
-tenantId
```


Ez az Azure AD-bérlő használata. 

```
-development 0|1
```

Ez a fejlesztéshez szükséges. Használja a hibakeresési buildet, és állítsa a ASP.NET környezetet Fejlesztési beállításra. Hozzon létre `.publishsettings` importálásra a Visual Studio 2017-ben, hogy lehetővé tegye az alkalmazás és a szolgáltatás közvetlen üzembe helyezését. Ez a paraméter is nem kötelező.

```
-onlyBuild 0|1
```

Ez az újraépítés és a webalkalmazások újratelepítése, és a Docker-tárolók újratelepítése. Ez a paraméter is nem kötelező.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az OPC Vault telepítését a semmiből, a következőket teheti:

> [!div class="nextstepaction"]
> [OPC-tároló kezelése](howto-opc-vault-manage.md)
