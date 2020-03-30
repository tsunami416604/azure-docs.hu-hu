---
title: Az Azure Service Fabric Docker írása – telepítési előzetes verzió
description: Az Azure Service Fabric elfogadja a Docker Compose formátumot, hogy megkönnyítse a meglévő tárolók vezénylése a Service Fabric használatával. Ez a támogatás jelenleg előzetes verzióban érhető el.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282457"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>A Docker Compose üzembe helyezési támogatása az Azure Service Fabricben (előzetes verzió)

A Docker a [docker-compose.yml](https://docs.docker.com/compose) fájlt használja a többtárolós alkalmazások definiálására. Annak érdekében, hogy a Docker-t ismerő ügyfelek könnyebben vezényeljék a meglévő tárolóalkalmazásokat az Azure Service Fabric-en, a Docker Compose üzembe helyezésének előzetes támogatásával a platformon is elérhetővé tettük. A Service Fabric képes elfogadni `docker-compose.yml` a fájlok 3-as és újabb verzióját. 

Mivel ez a támogatás előzetes verzióban érhető el, a Compose direktíváknak csak egy részhalmaza támogatott.

Az előzetes verzió használatához hozza létre a fürtöt a Service Fabric futásidejű 5.7-es vagy újabb verziójával az Azure Portalon keresztül a megfelelő SDK-val együtt. 

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el, és éles környezetben nem támogatott.
> Az alábbi példák a 6.0-s és az SDK 2.8-as verzióján alapulnak.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Docker-írási fájl üzembe helyezése a Service Fabric en

A következő parancsok hozzon létre `fabric:/TestContainerApp`egy Service Fabric-alkalmazást (named), amely figyelheti és kezelheti, mint bármely más Service Fabric-alkalmazás. Az állapotlekérdezésekhez használhatja a megadott alkalmazásnevet.
A Service Fabric a "DeploymentName" azonosítót ismeri fel a Compose központi telepítés azonosítójaként.

### <a name="use-powershell"></a>A PowerShell használata

Hozzon létre egy Service Fabric compose központi telepítést egy docker-compose.yml fájlból a következő parancs futtatásával a PowerShellben:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName`és `RegistryPassword` olvassa el a tároló rendszerleíró felhasználónevét és jelszavát. A telepítés befejezése után a következő paranccsal ellenőrizheti az állapotát:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

A Compose központi telepítés t a PowerShellen keresztültörténő törléséhez használja a következő parancsot:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

A Compose központi telepítési frissítés PowerShellen keresztüli indításához használja a következő parancsot:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

A Compose központi telepítési frissítés powershellen keresztüli visszaállításához használja a következő parancsot:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

A frissítés elfogadása után a frissítés folyamata a következő paranccsal követhető nyomon:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Az Azure Service Fabric CLI (sfctl) használata

Másik lehetőségként használhatja a következő Service Fabric CLI parancsot:

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

A központi telepítés létrehozása után a következő paranccsal ellenőrizheti az állapotát:

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

A Compose központi telepítés törléséhez használja a következő parancsot:

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

A Compose központi telepítési frissítés elindításához használja a következő parancsot:

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

A Compose központi telepítési frissítés visszaállításához használja a következő parancsot:

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

A frissítés elfogadása után a frissítés folyamata a következő paranccsal követhető nyomon:

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Támogatott írási irányelvek

Ez az előzetes verzió a 3-as verzió összeállítása formátumban lévő konfigurációs beállítások egy részét támogatja, beleértve a következő primitíveket:

* Szolgáltatások > > replikák telepítése
* Szolgáltatások > üzembe helyezése > elhelyezési > megkötések
* Szolgáltatások > > erőforrások telepítése > korlátok
    * -cpu-részvények
    * -memória
    * -memória-swap
* Szolgáltatások > parancsok
* Szolgáltatások > környezet
* Szolgáltatások > portok
* Szolgáltatások > kép
* Szolgáltatások > elkülönítés (csak Windows esetén)
* Szolgáltatások naplózási > illesztőprogram>
* Szolgáltatások naplózási > illesztőprogram-beállítások > >
* Kötet & > kötet telepítése

Állítsa be a fürterőforrás-korlátok érvényesítéséhez, a [Service Fabric erőforrás-szabályozás.](service-fabric-resource-governance.md) Az összes többi Docker Compose direktíva nem támogatott ehhez az előzetes verzióhoz.

### <a name="ports-section"></a>Portok szakasz

Adja meg a http vagy https protokollt a Portok szakaszban, amelyet a Service Fabric szolgáltatás figyelője fog használni. Ez biztosítja, hogy a végpontprotokoll megfelelően legyen közzétéve az elnevezési szolgáltatással, hogy a fordított proxy továbbíthassa a kérelmeket:
* A nem biztonságos Service Fabric compose szolgáltatásokhoz való átirányításhoz adja meg a **/http**kapcsolót. Például - **"80:80/http"**.
* A Service Fabric compose szolgáltatások biztonságos sáválasztásához adja meg a **/https**kapcsolót. Például - **"443:443/https"**.

> [!NOTE]
> A /http és /https Ports szakasz szintaxisa a Service Fabric a megfelelő Service Fabric figyelő URL-cím regisztrálásához.  Ha a Docker-fájl szintaxisa programozott módon érvényesítve van, az érvényesítési hibát okozhat.

## <a name="servicednsname-computation"></a>ServiceDnsName számítás

Ha a Compose fájlban megadott szolgáltatásnév teljesen minősített tartománynév (azaz egy pont [.]) tartalmazza, akkor `<ServiceName>` a Service Fabric által regisztrált DNS-név (a pontmal együtt). Ha nem, akkor az alkalmazás névben minden elérési útszegmens tartománycímkévé válik a szolgáltatás DNS-nevében, és az első elérési útszegmens lesz a legfelső szintű tartománycímke.

Ha például a megadott alkalmazásnév `<ServiceName>.MyComposeApp.SampleApp` , `fabric:/SampleApp/MyComposeApp`akkor a regisztrált DNS-név lesz.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Központi telepítés (példánydefiníció) és service fabric alkalmazásmodell (típusdefiníció) írása

A docker-compose.yml fájl a telepíthető tárolók készletét írja le, beleértve azok tulajdonságait és konfigurációit.
A fájl tartalmazhat például környezeti változókat és portokat. A docker-compose.yml fájlban telepítési paramétereket is megadhat, például elhelyezési megkötéseket, erőforráskorlátokat és DNS-neveket.

A [Service Fabric alkalmazásmodell](service-fabric-application-model.md) szolgáltatástípusokat és alkalmazástípusokat használ, ahol számos azonos típusú alkalmazáspéldányt használhat. Például vevőnként egy alkalmazáspéldány is lehet. Ez a típusalapú modell támogatja az azonos típusú, a futásidejű több verziót.

Például az "A" vevő rendelkezhet egy AppTypeA 1.0-s típussal példányosított alkalmazással, és "B" vevő egy másik alkalmazás sal rendelkezhet ugyanazzal a típussal és verzióval. Az alkalmazástípusokat az alkalmazásjegyzékekben definiálja, és az alkalmazás neve és telepítési paramétereit az alkalmazás létrehozásakor adja meg.

Bár ez a modell rugalmasságot biztosít, azt is tervezi, hogy támogatja az egyszerűbb, példányalapú telepítési modell, ahol a típusok implicit a jegyzékfájlból. Ebben a modellben minden alkalmazás saját független jegyzékfájlt kap. Ezt az erőfeszítést a docker-compose.yml, amely egy példányalapú telepítési formátum támogatásával tekintjük meg.

## <a name="next-steps"></a>További lépések

* Olvassa el a [Service Fabric alkalmazásmodell](service-fabric-application-model.md)
* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)
