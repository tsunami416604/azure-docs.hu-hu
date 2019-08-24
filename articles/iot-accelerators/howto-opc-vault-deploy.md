---
title: Az OPC Vault Certificate Management szolgáltatás üzembe helyezése – Azure | Microsoft Docs
description: Az OPC-tár tanúsítványkezelő szolgáltatásának üzembe helyezése a semmiből.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1625f0e6f9bfe8297cae2770e63107bf4f19f95e
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012981"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Az OPC Vault Certificate Management szolgáltatás létrehozása és üzembe helyezése

Ez a cikk bemutatja, hogyan helyezheti üzembe az OPC Vault tanúsítványkezelő szolgáltatást az Azure-ban.

> [!NOTE]
> További információ a központi telepítés részleteiről és az utasításokról: GitHub [OPC Vault adattár](https://github.com/Azure/azure-iiot-opc-vault-service).

## <a name="prerequisites"></a>Előfeltételek

### <a name="install-required-software"></a>A szükséges szoftverek telepítése

Jelenleg a létrehozási és üzembe helyezési művelet a Windowsra korlátozódik.
A minták mind a C# .NET Standard verzióra vannak írva, ami szükséges a szolgáltatás és a minták üzembe helyezéséhez.
A .net szabványhoz szükséges összes eszköz a .net Core-eszközökkel együtt érhető el. [Itt](https://docs.microsoft.com/dotnet/articles/core/getting-started) találja a szükséges tudnivalókat.

1. [Telepítse a .net Core 2.1 +][dotnet-install]-ot.
2. A [Docker telepítése][docker-url] (nem kötelező, csak akkor, ha a helyi Docker-Build szükséges).
4. Telepítse a [powershellhez készült Azure parancssori eszközöket][powershell-install].
5. Regisztráljon egy [Azure][azure-free]-előfizetésre.

### <a name="clone-the-repository"></a>A tárház klónozása

Ha még nem tette meg, akkor a GitHub-tárház klónozásával.  Nyisson meg egy parancssort vagy terminált, és futtassa a parancsot:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

vagy a tárház klónozása közvetlenül a Visual Studio 2017-ben.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Az Azure-szolgáltatás létrehozása és üzembe helyezése Windows rendszeren

A PowerShell-parancsfájlok egyszerű módszert biztosítanak az OPC-tároló-szolgáltatás és az alkalmazás üzembe helyezésére.<br>

1. Nyisson meg egy PowerShell-ablakot a tárház gyökerében. 
3. Ugrás a mappa üzembe helyezése`cd deploy`
3. Válasszon egy nevet `myResourceGroup` , amely nem valószínű, hogy ütközik más központilag telepített weblapokkal. Tekintse meg, hogyan választják ki a weblapok nevét az erőforráscsoport neve alapján. [](#website-name-already-in-use)
5. A központi telepítés `.\deploy.ps1` elindítása interaktív telepítéssel<br>
vagy adjon meg egy teljes parancssort:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Ha ezt az üzembe helyezést tervezi, a Hozzáadás `-development 1` gombra kattintva engedélyezheti a hencegő felületet és telepítheti a hibakeresési buildeket.
6. A parancsfájl utasításait követve jelentkezzen be az előfizetésbe, és adjon meg további információkat.
9. Sikeres felépítési és üzembe helyezési művelet után a következő üzenetnek kell megjelennie:
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

Ha problémákba ütközik, kövesse az [alábbi](#troubleshooting-deployment-failures)lépéseket.

8. Nyissa meg a kedvenc böngészőjét, és nyissa meg az alkalmazás lapját:`https://myResourceGroup.azurewebsites.net`
8. Az üzembe helyezést követően néhány perc alatt bemelegítheti a webalkalmazást és az OPC-tárolót. A weboldalt az első használat után legfeljebb egy percig felfüggesztheti, amíg meg nem kapja az első válaszokat.
11. A hencegő API megnyitásához nyissa meg a következőt:`https://myResourceGroup-service.azurewebsites.net`
13. Egy helyi GDS-kiszolgáló elindítása a DotNet Start `.\myResourceGroup-gds.cmd` vagy a Docker Start `.\myResourceGroup-dockergds.cmd`használatával.

Sidenote lehetséges a buildek újbóli üzembe helyezése, pontosan ugyanazokkal a beállításokkal. Vegye figyelembe, hogy egy ilyen művelet megújítja az összes alkalmazás titkos kulcsát, és egyes beállítások alaphelyzetbe állíthatók a Azure Active Directory (Azure AD) alkalmazás regisztrációjában.

Az is lehetséges, hogy csak a webalkalmazás bináris fájljait telepíti újra. A szolgáltatáshoz tartozó új zip-csomagok és az alkalmazás telepítése a webalkalmazásokra történik. `-onlyBuild 1`

A sikeres üzembe helyezést követően bátran elkezdheti a szolgáltatások használatát: [Az OPC Vault tanúsítványkezelő szolgáltatás kezelése](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>Szolgáltatások törlése az előfizetésből

1. Jelentkezzen be a Azure Portalba `https://portal.azure.com`:.
2. Keresse meg azt az erőforráscsoportot, amelyben a szolgáltatást telepítették.
3. Válassza `Delete resource group` ki és erősítse meg.
4. Rövid idő elteltével az összes üzembe helyezett szolgáltatás-összetevő törlődik.
5. Most nyissa `Azure Active Directory/App registrations`meg a következőt:.
6. Minden telepített erőforráscsoport esetében három regisztrációnak kell szerepelnie a következő nevekkel: `resourcegroup-client`, `resourcegroup-module`, `resourcegroup-service`.
Minden regisztrációt külön kell törölni.
7. Mostantól az összes telepített összetevő el lesz távolítva.

## <a name="troubleshooting-deployment-failures"></a>Telepítési hibák elhárítása

### <a name="resource-group-name"></a>Erőforráscsoport neve

Győződjön meg arról, hogy rövid és egyszerű erőforráscsoport-nevet használ.  A név az erőforrások és a szolgáltatás URL-címének előtagját is használja, és ennek a névnek meg kell felelnie az erőforrás-elnevezési követelményeknek.  

### <a name="website-name-already-in-use"></a>A webhely neve már használatban van

Lehetséges, hogy a webhely neve már használatban van.  Ha ezt a hibát futtatja, másik erőforráscsoport-nevet kell használnia. Az üzembe helyezési parancsfájl által használt állomásnevek a következők: https://resourcegroupname.azurewebsites.net és https://resourgroupname-service.azurewebsites.net.
A szolgáltatások egyéb neveit a rövid névvel ellátott kivonatok kombinációjával építjük össze, és nem valószínű, hogy ütköznek más szolgáltatásokkal.

### <a name="azure-active-directory-azure-ad-registration"></a>Azure Active Directory (Azure AD) regisztráció 

Az üzembehelyezési parancsfájl három Azure AD-alkalmazást próbál regisztrálni Azure Active Directoryban.  
A kiválasztott Azure AD-bérlő engedélyeitől függően ez a művelet sikertelen lehet.   Két lehetőség közül választhat:

1. Ha az Azure AD-bérlőt választotta a bérlők listájáról, indítsa újra a szkriptet, és válasszon egy másikat a listából.
2. Alternatív megoldásként helyezzen üzembe egy privát Azure AD-bérlőt egy másik előfizetésben, indítsa újra a szkriptet, és válassza ki a használatát.

## <a name="deployment-script-options"></a>Üzembe helyezési parancsfájl beállításai

A parancsfájl a következő paramétereket veszi figyelembe:


```
-resourceGroupName
```

Egy meglévő vagy egy új erőforráscsoport neve lehet.

```
-subscriptionId
```


Nem kötelező megadni az előfizetés AZONOSÍTÓját, ahol az erőforrások telepítve lesznek.

```
-subscriptionName
```


Vagy másik lehetőségként az előfizetés nevét is használhatja.

```
-resourceGroupLocation
```


Az erőforráscsoport helye nem kötelező. Ha meg van adva, akkor megpróbál létrehozni egy új erőforráscsoportot ezen a helyen.


```
-tenantId
```


A használni kívánt Azure AD-bérlő. 

```
-development 0|1
```

Nem kötelező, a fejlesztéshez való üzembe helyezéshez. Használja a hibakeresési buildet, és állítsa be a ASP.Net környezetet a fejlesztéshez. Hozzon létre ". publishsettings"-t az importáláshoz a Visual Studio 2017-ben, hogy az alkalmazás és a szolgáltatás közvetlenül is üzembe helyezhető.

```
-onlyBuild 0|1
```

Nem kötelező, csak a webalkalmazások újraépítése és újbóli üzembe helyezése, valamint a Docker-tárolók újraépítése.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan helyezhet üzembe az OPC-tárolót, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [OPC-tároló kezelése](howto-opc-vault-manage.md)
