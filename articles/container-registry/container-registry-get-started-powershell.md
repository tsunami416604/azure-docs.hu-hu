---
title: Rövid útmutató – Privát Docker regisztrációs adatbázis létrehozása az Azure-ban a PowerShell-lel
description: Az útmutató azt ismerteti, hogyan hozhat létre egy privát Docker regisztrációs adatbázist az Azure-ban a PowerShell-lel.
services: container-registry
author: marsma
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/08/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 282cd4bc9256fc483014b53626c02106d0de236a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885119"
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>Rövid útmutató: Azure Container Registry létrehozása a PowerShell-lel

Az Azure Container Registry egy felügyelt, privát Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a Docker-tárolók rendszerképeinek létrehozására, tárolására és kiszolgálására szolgál. Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Azure-tárolóregisztrációs adatbázist a PowerShell használatával. A regisztrációs adatbázis létrehozását követően le fog küldeni egy tárolórendszerképet az adatbázisba, majd üzembe helyezi a tárolót a regisztrációs adatbázisból az Azure Container Instances (ACI) szolgáltatásban.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. Futtassa a `Get-Module -ListAvailable AzureRM` parancsot a telepített verzió meghatározásához. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

A Dockert is telepítenie kell helyileg. A Docker [macOS][docker-mac], [Windows][docker-windows] és [Linux][docker-linux] operációs rendszerekhez biztosít csomagokat.

Mivel az Azure Cloud Shell nem tartalmazza az összes szükséges Docker-összetevőt (a `dockerd`-démont), ehhez a rövid útmutatóhoz nem használható a Cloud Shell.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [Connect-AzureRmAccount][Connect-AzureRmAccount] paranccsal, majd kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-hitelesítés után hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup][New-AzureRmResourceGroup] paranccsal. Az erőforráscsoport egy olyan logikai tároló, amelyben üzembe helyezheti és kezelheti az Azure-erőforrásokat.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ezt követően hozzon létre egy tárolóregisztrációs adatbázist az új erőforráscsoportban a [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry] paranccsal.

A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában egy „myContainerRegistry007” nevű adatbázis jön létre. Írja be a *myContainerRegistry007* kifejezést az alábbi parancsba, majd futtassa azt az adatbázis létrehozásához:

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tárolórendszerképek mozgatásához először be kell jelentkeznie a beállításjegyzékbe. Először a [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential] paranccsal kérje le a beállításjegyzék rendszergazdai hitelesítő adatait:

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Ezután a bejelentkezéshez futtassa a [docker login][docker-login] parancsot:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

A sikeres bejelentkezés a `Login Succeeded` üzenetet adja vissza:

```console
PS Azure:\> $creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
Login Succeeded
```

## <a name="push-image-to-registry"></a>Rendszerkép leküldése a beállításjegyzékbe

A bejelentkezést követően tárolórendszerképeket küldhet a beállításjegyzékbe. A beállításjegyzékbe leküldhető rendszerkép lekéréséhez húzza át a nyilvános [aci-helloworld][aci-helloworld-image] rendszerképet a Docker Hubról. Az [aci-helloworld] [ aci-helloworld-github] lemezkép egy kis Node.js-alkalmazás, amely egy, az Azure Container Instances emblémát megjelenítő, statikus HTML-oldalt szolgál ki.

```powershell
docker pull microsoft/aci-helloworld
```

A rendszerkép áthúzását követően a kimenet a következőhöz hasonló lesz:

```console
PS Azure:\> docker pull microsoft/aci-helloworld
Using default tag: latest
latest: Pulling from microsoft/aci-helloworld
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
3be0618266da: Pull complete
9e232827e52f: Pull complete
b53c152f538f: Pull complete
Digest: sha256:a3b2eb140e6881ca2c4df4d9c97bedda7468a5c17240d7c5d30a32850a2bc573
Status: Downloaded newer image for microsoft/aci-helloworld:latest
```

Mielőtt leküldhetné a rendszerképet az Azure tárolóregisztrációs adatbázisba, fel kell címkéznie azt a beállításjegyzék teljes tartománynevével. Az Azure tárolóregisztrációs adatbázisainak formátuma a következő: *\<beállításjegyzék-név\>.azurecr.io*.

Adja meg egy változó számára a teljes rendszerkép-címkét. Ebbe foglalja bele a bejelentkezési kiszolgáló, adattár nevét („aci-helloworld”) és a rendszerkép verziószámát („v1”) is:

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
```

Ezután címkézze fel a rendszerképet a [docker tag][docker-tag] paranccsal:

```powershell
docker tag microsoft/aci-helloworld $image
```

Végül a [docker push][docker-push] paranccsal küldje le a rendszerképet a beállításjegyzékbe:

```powershell
docker push $image
```

Amikor a Docker-ügyfél elvégzi a rendszerkép leküldését, a kimenet az alábbihoz hasonló lesz:

```console
PS Azure:\> docker push $image
The push refers to repository [myContainerRegistry007.azurecr.io/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

Gratulálunk! Ezzel leküldte az első tárolórendszerképét a beállításjegyzékbe.

## <a name="deploy-image-to-aci"></a>Rendszerkép üzembe helyezése az ACI szolgáltatásban

Most, hogy a rendszerkép a beállításjegyzékben található, helyezzen üzembe egy tárolót közvetlenül az Azure Container Instancesben, hogy annak Azure-beli futását megfigyelhesse.

Először is konvertálja a beállításjegyzék hitelesítő adatait *PSCredential* adattá. A tárolópéldány létrehozásához használt `New-AzureRmContainerGroup` parancs futtatásához az adatoknak ebben a formátumban kell lenniük.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Ezen kívül a tároló DNS-névcímkéjének egyedinek kell lennie abban az Azure-régióban, amelyben tárolót létrehozza. A következő parancs futtatásával adja meg a változó számára a létrehozott nevet:

```powershell
$dnsname = "aci-demo-" + (Get-Random -Maximum 9999)
```

Végül a tárolónak a beállításjegyzék-rendszerképből 1 processzormaggal és 1 GB memóriával történő üzembe helyezéséhez futtassa a [New-AzureRmContainerGroup][New-AzureRmContainerGroup] parancsot:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
```

Az Azure kezdeti válaszát kell megkapnia a tároló részleteivel, ahol a tároló állapota kezdetben „Függőben”:

```console
PS Azure:\> New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials : {myContainerRegistry007}
RestartPolicy            : Always
IpAddress                : 40.117.255.198
DnsNameLabel             : aci-demo-8751
Fqdn                     : aci-demo-8751.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Linux
Volumes                  :
State                    : Pending
Events                   : {}
```

Futtassa néhányszor a [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] parancsot a tároló állapotának monitorozásához és futási állapotának meghatározásához. A tároló indítása kevesebb mint egy percet vesz igénybe.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Itt azt láthatja, hogy a tároló ProvisioningState értéke kezdetben *Létrehozás*, majd a tároló elkészültekor *Sikeres* értékre vált:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Creating
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Succeeded
```

## <a name="view-running-application"></a>A futó alkalmazás megtekintése

Ha az ACI-n történő üzembe helyezés sikeresen megtörtént, és a tároló működik, nyissa meg egy böngészőben a tároló teljes tartománynevét (FQDN) annak ellenőrzésére, hogy fut-e az alkalmazás az Azure-ban.

A tároló teljes tartománynevét a [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] paranccsal kérheti le:


```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
```

A parancs kimenete a tárolópéldány teljes tartományneve:

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
aci-demo-8571.eastus.azurecontainer.io
```

A teljes tartománynév ismeretében nyissa meg azt a böngészőben:

![A Hello World alkalmazás a böngészőben][qs-psh-01-running-app]

Gratulálunk! Most már rendelkezik egy, az Azure-ban alkalmazást futtató tárolóval, amelyet közvetlenül helyezett üzembe egy tárolórendszerképből a privát Azure tárolóregisztrációs adatbázisában.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a jelen rövid útmutatóban létrehozott erőforrásokra, a [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] paranccsal eltávolíthatja az erőforráscsoportot, a tárolóregisztrációs adatbázist és a tárolót is:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry-példányt az Azure CLI segítségével, és futtatta annak egy példányát az Azure Container Instances szolgáltatásban. Folytassa az Azure Container Instances oktatóanyagával, amelyben alaposabban megismerheti az ACI szolgáltatást.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[aci-helloworld-github]: https://github.com/Azure-Samples/aci-helloworld
[aci-helloworld-image]: https://hub.docker.com/r/microsoft/aci-helloworld/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[New-AzureRMContainerRegistry]: /powershell/module/containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-psh-01-running-app]: ./media/container-registry-get-started-powershell/qs-psh-01-running-app.png
