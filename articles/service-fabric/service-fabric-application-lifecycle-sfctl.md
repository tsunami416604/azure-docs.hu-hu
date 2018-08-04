---
title: Az Azure Service Fabric parancssori felület (sfctl) használatával az Azure Service Fabric-alkalmazások kezelése
description: Ismerje meg, hogyan telepíthet és távolíthat el alkalmazásokat az Azure Service Fabric-fürt az Azure Service Fabric parancssori felület használatával
services: service-fabric
author: Christina-Kang
manager: timlt
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 2e71996e22fee34b29139fdf19764c47616beb1d
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492749"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Az Azure Service Fabric-alkalmazás kezelése az Azure Service Fabric parancssori felület (sfctl) használatával

Ismerje meg, hogyan hozhat létre, és törölje az alkalmazást, amely egy Azure Service Fabric-fürtön futnak.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a Service Fabric parancssori felület. Ezután válassza ki a Service Fabric-fürtön. További információkért lásd: [Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md).

* Rendelkezik egy Service Fabric-alkalmazáscsomag központilag telepíthető. Szerző és a egy alkalmazás csomag kapcsolatos további információkért olvassa el a [Service Fabric-alkalmazásmodell](service-fabric-application-model.md).

## <a name="overview"></a>Áttekintés

Új alkalmazás üzembe helyezéséhez hajtsa végre ezeket a lépéseket:

1. A Service Fabric lemezképtárolójába töltsön fel egy alkalmazáscsomagot.
2. Alkalmazástípusok üzembe helyezhető.
3. Törli a lemezképet tároló tartalmat.
4. Adja meg, és hozzon létre egy alkalmazást.
5. Adja meg, és szolgáltatások létrehozása.

A meglévő alkalmazások eltávolításához hajtsa végre ezeket a lépéseket:

1. Törölje az alkalmazást.
2. A társított alkalmazástípus telepítésének.

## <a name="deploy-a-new-application"></a>Új alkalmazás üzembe helyezése

Új alkalmazás üzembe helyezéséhez hajtsa végre a következő feladatokat:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Az image store alkalmazás új csomag feltöltése

Mielőtt alkalmazást hoz létre, a Service Fabric lemezképtároló töltse fel az alkalmazáscsomagot.

Például, ha az alkalmazás-csomagot a `app_package_dir` directory, a következő parancsok használatával töltse fel a könyvtár:

```azurecli
sfctl application upload --path ~/app_package_dir
```

Nagy alkalmazáscsomagok esetén megadhatja az `--show-progress` lehetőséget a feltöltés állapotának megjelenítéséhez.

### <a name="provision-the-application-type"></a>Az alkalmazástípus kiépítése

Amikor a feltöltés befejeződött, az alkalmazás üzembe helyezése. Üzembe helyezi az alkalmazást, használja a következő parancsot:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

Az érték `application-type-build-path` a könyvtárban, ahol az alkalmazáscsomag feltöltött neve.

### <a name="delete-the-application-package"></a>Az alkalmazáscsomag törlése

Ajánlott az alkalmazáscsomag törlése után az alkalmazás regisztrálása sikeres volt.  Rendszer-erőforrásokat szabadít alkalmazáscsomagok törlése a képet tárból.  A fel nem használt alkalmazáscsomagok lemezes tárolást használ fel, és alkalmazásteljesítménnyel kapcsolatos problémák vezet. 

Az alkalmazáscsomag a képet tárból törléséhez használja a következő parancsot:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` a könyvtár, amely az alkalmazás létrehozásakor a feltöltött nevének kell lennie.

### <a name="create-an-application-from-an-application-type"></a>Hozzon létre egy alkalmazást egy alkalmazás típusból

Miután üzembe az alkalmazást, a következő paranccsal nevet, és az alkalmazás létrehozása:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` az a név, amelyet az alkalmazáspéldány használni szeretne. A korábban üzembe helyezett alkalmazás jegyzékfájlja további paramétereket is kérhet.

Az alkalmazás nevét a előtaggal kell kezdődnie `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Az új alkalmazás-szolgáltatások létrehozásához

Miután létrehozott egy alkalmazást, az alkalmazás szolgáltatások létrehozása. A következő példában az alkalmazás hozunk létre egy új állapotmentes szolgáltatás. A szolgáltatásokat, létrehozhat egy alkalmazást a korábban üzembe helyezett alkalmazás csomagban szolgáltatásjegyzékben vannak meghatározva.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Alkalmazások üzembe helyezését és állapotának ellenőrzése

Annak ellenőrzéséhez, hogy minden kifogástalan állapotban, az alábbi parancsokkal állapota:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Annak ellenőrzéséhez, hogy a szolgáltatás állapota megfelelő, hasonló parancsok használatával a szolgáltatás és az alkalmazás állapotának lekéréséhez:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Kifogástalan állapotú szolgáltatások és alkalmazások rendelkeznek egy `HealthState` értékét `Ok`.

## <a name="remove-an-existing-application"></a>Meglévő alkalmazás eltávolítása

Az alkalmazás eltávolításához hajtsa végre a következő feladatokat:

### <a name="delete-the-application"></a>Az alkalmazás törlése

Az alkalmazás törléséhez használja a következő parancsot:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Az alkalmazástípus telepítésének visszavonása

Után törli az alkalmazást, ha már nincs szüksége az alkalmazás típusát is leválasztja. Az alkalmazástípus telepítésének, használja a következő parancsot:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

A típus neve és az alkalmazástípus-verzió meg kell egyeznie a nevét és verzióját, a korábban kiépített alkalmazásjegyzékben.

## <a name="upgrade-application"></a>Alkalmazás frissítése

Miután létrehozta az alkalmazást, ismételje meg ugyanazokat a lépéseket az alkalmazás második verziójának kiépítése. Ezután egy Service Fabric-alkalmazás frissítése az átválthat a az alkalmazás második verzióját futtatja. További információkért lásd a dokumentációt [Service Fabric alkalmazásfrissítések](service-fabric-application-upgrade.md).

A frissítés végrehajtásához először is létre a következő verziójára az alkalmazás, mint korábban használja ugyanazokat a parancsokat:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Javasolt majd figyelt automatikus frissítésének végrehajtása, indítsa el a frissítés a következő parancs futtatásával:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Frissítések bármilyen készlet van megadva a meglévő paraméterek bírálhatja felül. Alkalmazás paramétereket kell adható át argumentumként a verzióváltás parancs szükség esetén. Alkalmazás paraméterek JSON-objektumként kódolásúnak kell lennie.

A korábban megadott paramétereket lekéréséhez használja a `sfctl application info` parancsot.

Ha egy alkalmazás frissítése folyamatban van, a állapotát lehet lekérni a `sfctl application upgrade-status` parancsot.

Végül, ha egy frissítés van folyamatban, és igényeinek megfelelően kell lennie, használhatja a `sfctl application upgrade-rollback` szeretné visszaállítani a frissítést.

## <a name="next-steps"></a>További lépések

* [Service Fabric parancssori felület alapjai](service-fabric-cli.md)
* [Ismerkedés a Service Fabric Linux rendszeren](service-fabric-get-started-linux.md)
* [A Service Fabric-alkalmazás frissítése elindítása](service-fabric-application-upgrade.md)
