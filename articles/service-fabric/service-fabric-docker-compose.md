---
title: Az Azure Service Fabric Docker Compose előzetes központi telepítés
description: Az Azure Service Fabric Docker Compose formátumban, hogy egyszerűbb legyen a Service Fabricet használja meglévő tárolók vezénylésére fogad el. Ez a támogatás jelenleg előzetes verzióban érhető el.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: ff846717287fb2b125b549f6ca0de6c7908d4c35
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344813"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Docker-Compose üzembe helyezési támogatás az Azure Service Fabric (előzetes verzió)

Használja a docker a [docker-compose.yml](https://docs.docker.com/compose) fájlt definiál többtárolós alkalmazásokat. Megkönnyíti az ügyfelek az Azure Service Fabricban meglévő tároló alkalmazások koordinálása a Docker ismerős, szerepel a Docker Compose szolgáltatástelepítés támogatása előzetes natív módon a platform. A Service Fabric elfogadhat 3 és újabb verziók, `docker-compose.yml` fájlokat. 

Mivel ez a támogatás előzetes verzióban érhető el, csak egy részhalmazát összeállítás irányelvek használata támogatott. Alkalmazásfrissítések például nem támogatott. Azonban Ön mindig eltávolíthatja, és helyezzen üzembe alkalmazásokat a frissítésük helyett.

Ez az előzetes verzió használatához hozzon létre a fürt verziójával 5.7-es vagy nagyobb, mint a Service Fabric-futtatókörnyezet, valamint a megfelelő SDK-t az Azure Portalon keresztül. 

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el, és éles környezetben nem támogatott.
> Az alábbi példák verze modulu runtime 6.0-s és az SDK 2.8-as verziója alapulnak.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>A Docker Compose-fájlt a Service Fabric üzembe helyezése

A következő parancsokat egy Service Fabric-alkalmazás létrehozása (nevű `fabric:/TestContainerApp`), amely figyelheti és kezelheti, mint bármely más Service Fabric-alkalmazás. Az alkalmazásnév az állapotlekérdezések is használhatja.
A Service Fabric "DeploymentName" felismeri a Compose üzemelő példány azonosítóként.

### <a name="use-powershell"></a>A PowerShell használata

Hozzon létre egy Service Fabric Compose központi telepítés egy docker-compose.yml fájlt a PowerShell a következő parancs futtatásával:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` és `RegistryPassword` tekintse meg a tároló-beállításjegyzék felhasználónevet és jelszót. Az üzembe helyezés befejezése után ellenőrizheti annak állapotát a következő paranccsal:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Az összeállítás üzembe helyezést, a PowerShell törléséhez használja a következő parancsot:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

A Compose üzemelő példány frissítése a PowerShell indításához használja a következő parancsot:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Frissítés elfogadása után a frissítési folyamat állapotát sikerült nyomon követheti a következő paranccsal:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Az Azure Service Fabric parancssori felület (sfctl) használata

A következő Service Fabric parancssori felület parancsot is használhatja:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Miután létrehozta az üzembe helyezés, a következő paranccsal ellenőrizheti annak állapotát:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

A compose üzemelő példányának törlése, használja a következő parancsot:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

A Compose üzemelő példány frissítése indításához használja a következő parancsot:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Frissítés elfogadása után a frissítési folyamat állapotát sikerült nyomon követheti a következő paranccsal:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Támogatott összeállítás irányelvek

Ebben az előzetes verzióban a formátumból az összeállítás 3-as verziójú, többek között a következő primitívek lehetőségek egy részét támogatja:

* Szolgáltatások > üzembe helyezése > replikák
* Szolgáltatások > üzembe helyezése > elhelyezési > megkötései
* Szolgáltatások > üzembe helyezése > erőforrások > korlátok
    * – a cpu-megosztások
    * – memória
    * – memória – swap
* Szolgáltatások > parancsok
* Szolgáltatások > környezetre
* Szolgáltatások > portok
* Szolgáltatások > kép
* Szolgáltatások > elkülönítését (csak Windows)
* Szolgáltatások > Naplózás > illesztőprogram
* Szolgáltatások > Naplózás > illesztőprogram > Beállítások
* Kötet & üzembe helyezése > kötet

Ezekkel az erőforrás-korlátok, a fürt beállítása a leírtak szerint [Service Fabric erőforrás-szabályozás](service-fabric-resource-governance.md). Egyéb Docker Compose irányelvek ebben az előzetes verzióban nem támogatott.

### <a name="ports-section"></a>Portok szakaszában

A Service Fabric-szolgáltatást figyelő által használt portok szakaszában adja meg a http vagy https protokollt. Ez biztosítja, hogy a végpont protokoll megfelelően van közzétéve, hogy a fordított proxy továbbítja a kérelmeket az elnevezési szolgáltatással:
* Irányíthatja a nem biztonságos Service Fabric Compose szolgáltatások, adja meg a **/http**. Ha például- **"80:80 / http"**.
* Irányíthatja a biztonságos Service Fabric Compose szolgáltatások, adja meg a **/https**. Ha például- **"443:443 / https"**.

> [!NOTE]
> A Service Fabric a Service Fabric figyelő helyes URL-cím regisztrálása csak a /http és /https portok szakasz szintaxisa.  Ha a Docker compose file szintaxissal programozott módon érvényesítése, az érvényesítési hiba okozhatja.

## <a name="servicednsname-computation"></a>ServiceDnsName számítási

Ha a szolgáltatás nevét, amely a Compose-fájlt ad meg egy teljesen minősített tartománynevét (azaz pontot tartalmaz [.]), a Service Fabric által regisztrált DNS-név `<ServiceName>` (beleértve a pont). Ha nem, akkor mindegyik elérési út szegmens az alkalmazásnév szolgáltatás DNS-nevet, a legfelső szintű tartományt címke válik első elérési út szegmens tartomány címke válik.

Ha a megadott alkalmazás neve például `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` regisztrált DNS-neve lesz.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Állítsa össze a központi telepítés (példány-definíció) és a Service Fabric-alkalmazásmodell (típusdefiníció)

A tárolók, beleértve azok tulajdonságait és konfigurációk üzembe helyezhető egy docker-compose.yml fájl bemutatja.
Például a fájl tartalmazhat környezeti változókat és a portok. Üzembe helyezési paraméterek, például elhelyezési korlátozások, az erőforrás-korlátozások és a DNS-nevek, megadhatja a docker-compose.yml fájl is.

A [Service Fabric-alkalmazásmodell](service-fabric-application-model.md) használ szolgáltatás és alkalmazás típusainak, ahol számos alkalmazáspéldány azonos típusú lehet. Például rendelkezhet egy alkalmazáspéldány ügyfelenként. Ez a típus-alapú modell azonos típusú alkalmazás, amely regisztrálva van a modul több verzióit támogatja.

Például egy ügyfél lehet egy alkalmazás AppTypeA 1.0 típusú példányt, és B ügyfél is rendelkezik, az azonos típusú és verzió példányosítása egy másik alkalmazás. Alkalmazástípusok határozhatja meg az alkalmazásjegyzékeket, és az alkalmazás létrehozásakor adja meg az alkalmazás nevét és a központi telepítési paramétereit.

Bár ez a modell rugalmasságot nyújt, is tervezzük ahol típusok a következők implicit a jegyzékfájl az egyszerűbb, a példány-alapú üzemi modell támogatásához. Ebben a modellben minden alkalmazás saját független jegyzékfájl beolvasása. Ebből a törekvésből támogatásának hozzáadásával a docker-compose.yml, ez az egy példány-alapú üzembe helyezési formátum előzetes azt.

## <a name="next-steps"></a>További lépések

* Olvassa a a [Service Fabric-alkalmazásmodell](service-fabric-application-model.md)
* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)
