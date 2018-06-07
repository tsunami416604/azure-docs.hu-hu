---
title: Az Azure Service Fabric Docker Compose előzetes központi telepítés
description: Az Azure Service Fabric a Docker Compose formátum használatával könnyebben lehet levezényelni a Service Fabric használatával meglévő tárolók fogad el. Ez a támogatás jelenleg előzetes verzió.
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
ms.openlocfilehash: 43845a55589be9550e64b4a491b7d3675fb22e8c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641781"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Docker Compose telepítési támogatása az Azure Service Fabric (előzetes verzió)

Docker használja a [docker-compose.yml](https://docs.docker.com/compose) fájl több tároló alkalmazások meghatározásához. Megkönnyítheti a felhasználók ismernie kell levezényelni a meglévő tárolóhoz alkalmazások az Azure Service Fabric Docker, vannak megadva Docker Compose üzembe helyezés preview támogatása natív módon a platform. A Service Fabric fogadhat 3 és az újabb `docker-compose.yml` fájlokat. 

Ez a támogatás jelenleg előzetes verzióban érhető, mert a Compose irányelvek csak egy részhalmazát esetén támogatott. Például alkalmazás frissítések nem támogatottak. Azonban mindig távolítsa el és telepíthet központilag alkalmazásokat helyett őket.

Ez az előzetes kiadás használatához hozzon létre a fürt verziójával 5.7-es vagy nagyobb a Service Fabric-futtatókörnyezet, valamint a megfelelő SDK az Azure portálon keresztül. 

> [!NOTE]
> Ez a funkció jelenleg előzetes verzióban érhető és éles környezetben nem támogatott.
> Az alábbi példák futásidejű 6.0-s verziója és az SDK 2.8-as verziója alapulnak.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>A Service Fabric a Docker Compose fájl központi telepítése

Az alábbi parancsokat a Service Fabric-alkalmazás létrehozása (nevű `fabric:/TestContainerApp`), amelyet figyelése és kezelése, mint bármely más Service Fabric-alkalmazás. A megadott alkalmazásnév állapotlekérdezések is használhatja.
A Service Fabric "DeploymentName" felismeri az új központi telepítési azonosítója.

### <a name="use-powershell"></a>A PowerShell használata

A Service Fabric összeállítása központi telepítés létrehozása egy docker-compose.yml fájlt a PowerShellben a következő parancs futtatásával:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` és `RegistryPassword` tekintse meg a tároló beállításjegyzék felhasználónevet és jelszót. A telepítés befejezése után ellenőrizheti annak állapotát a következő parancsot:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Törölni szeretné az új központi telepítést a Powershellen keresztül, a következő paranccsal:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Indítsa el egy új központi telepítési frissítését a PowerShell segítségével, használja a következő parancsot:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Frissítés elfogadása után a frissítési folyamat állapota sikerült nyomon követni a következő parancsot:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric CLI (sfctl) használata

Másik lehetőségként használhatja a következő Service Fabric CLI parancsot:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

A központi telepítés létrehozása után annak állapotát a következő paranccsal ellenőrizheti:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Törölni szeretné az új központi telepítést, a következő paranccsal:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Indítsa el egy új központi telepítési frissítését, használja a következő parancsot:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Frissítés elfogadása után a frissítési folyamat állapota sikerült nyomon követni a következő parancsot:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Támogatott Compose irányelvek

Ez az előzetes kiadás egy részét a konfigurációs beállítások formátumból a Compose 3-as verzió, beleértve a következő primitívek támogatja:

* Szolgáltatások > telepítése > replikák
* Szolgáltatások > telepítése > elhelyezési > megkötések
* Szolgáltatások > telepítése > erőforrások > korlátok
    * -cpu-megosztások
    * -memória
    * -memória-csere
* Szolgáltatások > parancsok
* Szolgáltatások > környezet
* Szolgáltatások > portok
* Szolgáltatások > kép
* Szolgáltatások > elszigetelése (csak Windows)
* Szolgáltatások > Naplózás > illesztőprogram
* Szolgáltatások > Naplózás > illesztőprogram > Beállítások
* Kötet & telepítése > kötet

A felelősek az erőforrás-korlátok, a fürt beállítása [Service Fabric erőforrás irányítás](service-fabric-resource-governance.md). Minden más Docker Compose irányelvek nem támogatottak az előzetes verzió.

## <a name="servicednsname-computation"></a>ServiceDnsName számítási

Ha a szolgáltatás neve meg egy új fájlt a teljes tartománynév (Ez azt jelenti, hogy pontot tartalmaz [.]), a DNS-nevet a Service Fabric által regisztrált `<ServiceName>` (beleértve a pont). Ha nem, akkor minden elérésiút-szegmens az alkalmazás nevét a tartomány címkét a szolgáltatás DNS-név, az első elérésiút-szegmens, a legfelső szintű tartomány címke váljon a válik.

Ha a megadott alkalmazás neve például `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` lenne regisztrált DNS-nevét.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Állítsa össze a központi telepítés (példány-definíció) és a Service Fabric az app model (típusdefinícióban)

Egy docker-compose.yml fájlt a tárolók, beleértve azok tulajdonságait és konfigurációk telepíthető együttesét írja le.
Például a fájl tartalmazhat környezeti változókat és a portok. Is üzembe helyezéshez megadott paraméterek, például egy elhelyezési korlátozás, erőforrás-korlátozások és DNS-nevek, megadhatja a docker-compose.yml fájlt.

A [Service Fabric-alkalmazás modell](service-fabric-application-model.md) használ szolgáltatás és alkalmazás típusainak, ahol nincs sok alkalmazáspéldányok ugyanabba a típusba tartozik. Lehet például egy ügyfél egy alkalmazáspéldányt. Ez a típus-alapú modell regisztrálva van a futtatókörnyezet az alkalmazás ugyanolyan több verzióit támogatja.

Például A felhasználói lehet egy példányként létrehozott típussal 1.0 AppTypeA rendelkező alkalmazást, és B ügyfél rendelkezhet azonos típusú és verzió példányként létrehozott egy másik alkalmazás. Alkalmazástípusok az alkalmazásjegyzékeknek határozhat meg, és az alkalmazás létrehozásakor adja meg az alkalmazás nevét és a központi telepítési paramétereit.

Bár ez a modell rugalmasságot nyújt, azt is tervezi, hogy hol típusok tartoznak, amely implicit a jegyzékfájl egyszerűbb, a példány-alapú telepítési modell támogatja. Ebben a modellben minden alkalmazás saját független jegyzékfájl lekérdezi. Ebből a törekvésből támogatásának hozzáadásával a docker-compose.yml, ez az egy példány-alapú üzembe helyezési formátum jelenleg előzetes.

## <a name="next-steps"></a>További lépések

* Olvassa a a [Service Fabric alkalmazásmodellt.](service-fabric-application-model.md)
* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)
