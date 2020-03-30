---
title: Azure Service Fabric-alkalmazások kezelése sfctl használatával
description: Ismerje meg, hogyan telepíthet és távolíthat el alkalmazásokat egy Azure Service Fabric-fürtből az Azure Service Fabric CLI használatával
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259070"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric-alkalmazás kezelése az Azure Service Fabric CLI (sfctl) használatával

Ismerje meg, hogyan hozhat létre és törölhet i. Azure Service Fabric-fürtön futó alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a Service Fabric CLI-t. Ezután válassza ki a Service Fabric-fürtöt. További információ: [Első lépések a Service Fabric CLI-vel.](service-fabric-cli.md)

* A Service Fabric-alkalmazáscsomag készen áll az üzembe helyezésre. Az alkalmazások szerkesztéséről és csomagolásáról a [Service Fabric alkalmazásmodellről](service-fabric-application-model.md)olvashat bővebben.

## <a name="overview"></a>Áttekintés

Új alkalmazás üzembe helyezéséhez hajtsa végre az alábbi lépéseket:

1. Töltsön fel egy alkalmazáscsomagot a Service Fabric-lemezképtárba.
2. Alkalmazástípus kiépítése.
3. Törölje a képtároló tartalmát.
4. Adjon meg és hozzon létre egy alkalmazást.
5. Szolgáltatások megadása és létrehozása.

Meglévő alkalmazás eltávolításához hajtsa végre az alábbi lépéseket:

1. Törölje az alkalmazást.
2. A társított alkalmazástípus kioldása.

## <a name="deploy-a-new-application"></a>Új alkalmazás telepítése

Új alkalmazás üzembe helyezéséhez hajtsa végre a következő feladatokat:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Új alkalmazáscsomag feltöltése a képtárba

Mielőtt egy alkalmazást hoz létre, töltse fel az alkalmazáscsomagot a Service Fabric-lemezkép-tárolóba.

Ha például az alkalmazáscsomag `app_package_dir` a könyvtárban található, a következő parancsokkal töltse fel a könyvtárat:

```shell
sfctl application upload --path ~/app_package_dir
```

Nagy alkalmazáscsomagok esetén megadhatja `--show-progress` a feltöltés előrehaladásának megjelenítésére szolgáló lehetőséget.

### <a name="provision-the-application-type"></a>Az alkalmazástípus kiépítése

Amikor a feltöltés befejeződött, kiépíti az alkalmazást. Az alkalmazás kiépítéséhez használja a következő parancsot:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

`application-type-build-path` Az érték annak a könyvtárnak a neve, amelybe az alkalmazáscsomagot feltöltötte.

### <a name="delete-the-application-package"></a>Az alkalmazáscsomag törlése

Javasoljuk, hogy távolítsa el az alkalmazáscsomagot, miután az alkalmazás sikeresen regisztrált.  Az alkalmazáscsomagok lemezképtárolóból történő törlése rendszererőforrásokat szabadít fel.  A nem használt alkalmazáscsomagok megtartása lemezes tárolást eredményez, és az alkalmazás teljesítményével kapcsolatos problémákhoz vezet. 

Ha törölni szeretné az alkalmazáscsomagot a lemezképtárolóból, használja a következő parancsot:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path`az alkalmazás létrehozásakor feltöltött könyvtár nevének kell lennie.

### <a name="create-an-application-from-an-application-type"></a>Alkalmazás létrehozása alkalmazástípusból

Az alkalmazás üzembe követése után a következő paranccsal nevezze el és hozza létre az alkalmazást:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name`Az alkalmazáspéldányhoz használni kívánt név. A korábban kiépített alkalmazásjegyzékből további paramétereket is beszerezhet.

Az alkalmazás nevének előtaggal `fabric:/`kell kezdődnie.

### <a name="create-services-for-the-new-application"></a>Szolgáltatások létrehozása az új alkalmazáshoz

Miután létrehozott egy alkalmazást, hozzon létre szolgáltatásokat az alkalmazásból. A következő példában egy új állapotmentes szolgáltatást hozunk létre az alkalmazásunkból. Az alkalmazásból létrehozható szolgáltatások a korábban kiépített alkalmazáscsomag szolgáltatásjegyzékében vannak definiálva.

```shell
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Az alkalmazások telepítésének és állapotának ellenőrzése

Ha ellenőrizni szeretné, hogy minden kifogástalan állapotú-e, használja a következő állapotparancsokat:

```shell
sfctl application list
sfctl service list --application-id TestApp
```

Annak ellenőrzéséhez, hogy a szolgáltatás kifogástalan állapotú-e, használjon hasonló parancsokat a szolgáltatás és az alkalmazás állapotának lekéréséhez:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Az egészséges szolgáltatások `HealthState` és `Ok`alkalmazások értéke .

## <a name="remove-an-existing-application"></a>Meglévő alkalmazás eltávolítása

Alkalmazás eltávolításához hajtsa végre a következő feladatokat:

### <a name="delete-the-application"></a>Az alkalmazás törlése

Az alkalmazás törléséhez használja a következő parancsot:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Az alkalmazástípus kioldása

Az alkalmazás törlése után megszüntetheti az alkalmazástípusát, ha már nincs rá szüksége. Az alkalmazástípus kioldásának kioldásához használja a következő parancsot:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

A típusnévnek és a típusverziónak meg kell egyeznie a korábban kiépített alkalmazásjegyzékben lévő névvel és verzióval.

## <a name="upgrade-application"></a>Alkalmazás frissítése

Az alkalmazás létrehozása után megismételheti ugyanazokat a lépéseket az alkalmazás második verziójának kiépítéséhez. Ezután a Service Fabric-alkalmazás frissítésével áttérhet az alkalmazás második verziójának futtatására. További információt a Service [Fabric-alkalmazások frissítésének dokumentációjában](service-fabric-application-upgrade.md)talál.

Frissítés végrehajtásához először az alkalmazás következő verzióját használja a korábbiparancsokkal:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Javasoljuk, hogy végre egy figyelt automatikus frissítés, indítsa el a frissítést futanak a következő parancsot:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

A frissítések felülírják a meglévő paramétereket a megadott készlettel. Az alkalmazás paramétereit szükség esetén argumentumként kell átadni a frissítési parancsnak. Az alkalmazás paramétereit JSON-objektumként kell kódolni.

A korábban megadott paraméterek beolvasásához `sfctl application info` használhatja a parancsot.

Amikor egy alkalmazás frissítés folyamatban van, az állapot `sfctl application upgrade-status` a parancs segítségével beolvasható.

Végül, ha egy frissítés folyamatban van, és meg kell `sfctl application upgrade-rollback` szakítani, használhatja a frissítés visszaállításához.

## <a name="next-steps"></a>További lépések

* [A Service Fabric CLI alapjai](service-fabric-cli.md)
* [A Service Fabric linuxos alkalmazásának első lépései](service-fabric-get-started-linux.md)
* [Service Fabric-alkalmazásfrissítés indítása](service-fabric-application-upgrade.md)
