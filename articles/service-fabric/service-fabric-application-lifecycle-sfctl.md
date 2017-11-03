---
title: "Azure Service Fabric parancssori felület használatával Azure Service Fabric-alkalmazások kezelése"
description: "Ismerje meg, hogyan helyezhet üzembe és alkalmazások eltávolítása egy Azure Service Fabric-fürt Azure Service Fabric parancssori felület használatával"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 08/22/2017
ms.author: edwardsa
ms.openlocfilehash: 6eb58b31f20f239d310415d44f61e7455918dae9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli"></a>Az Azure Service Fabric-alkalmazás kezelése az Azure Service Fabric parancssori felület használatával

Ismerje meg, hogyan hozhat létre, és törölnie kell az Azure Service Fabric-fürt futó alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

* A Service Fabric parancssori felület telepítése. Ezután válassza ki a Service Fabric-fürt. További információkért lásd: [Ismerkedés a Service Fabric CLI](service-fabric-cli.md).

* A Service Fabric alkalmazáscsomagok telepítésre kész rendelkezik. Szerző és csomagot alkalmazássá kapcsolatos további információkért olvassa el a [Service Fabric-alkalmazás modell](service-fabric-application-model.md).

## <a name="overview"></a>Áttekintés

Egy új alkalmazás központi telepítése, végezze el ezeket a lépéseket:

1. Alkalmazáscsomag feltöltése a Service Fabric lemezképtárolóhoz.
2. Az alkalmazástípus kiépítéséhez.
3. A lemezképet tároló tartalom törlése.
4. Adja meg, és hozzon létre egy alkalmazást.
5. Adja meg, és a szolgáltatások létrehozására.

Távolítsa el a meglévő alkalmazás, végezze el ezeket a lépéseket:

1. Az alkalmazás törlése.
2. A társított alkalmazások típus leépítése.

## <a name="deploy-a-new-application"></a>Egy új alkalmazás központi telepítése

Új alkalmazás központi telepítéséhez hajtsa végre a következőket:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Az image store egy új alkalmazáscsomag feltöltése

Hoz létre egy alkalmazást, mielőtt az alkalmazáscsomag feltöltése a Service Fabric lemezképtárolóhoz.

Például, ha az alkalmazás csomagot a `app_package_dir` könyvtár, töltse fel a könyvtár a következő parancsokkal:

```azurecli
sfctl application upload --path ~/app_package_dir
```

Nagy alkalmazáscsomagok esetén megadhatja a `--show-progress` a feltöltés állapotának megjelenítése lehetőséget.

### <a name="provision-the-application-type"></a>Az alkalmazástípus kiépítése

A feltöltés befejeződése után telepíteni az alkalmazást. Az alkalmazás telepítéséhez, a következő paranccsal:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

A következő `application-type-build-path` a könyvtárban, ahol az alkalmazáscsomag feltöltött neve.

### <a name="delete-the-application-package"></a>Az alkalmazáscsomag törlése

Ajánlott az alkalmazáscsomag eltávolítása után az alkalmazás regisztrálása sikeres volt.  Rendszererőforrások szabaddá alkalmazáscsomagok az image store való törlésekor.  Nem használt alkalmazáscsomagok használ fel a lemezes tárolás és alkalmazást teljesítményproblémák vezet. 

Az image store alkalmazáscsomag törléséhez használja a következő parancsot:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path`annak a könyvtárnak az alkalmazás létrehozásakor feltöltött nevének kell lennie.

### <a name="create-an-application-from-an-application-type"></a>Alkalmazástípusok az alkalmazás létrehozása

Az alkalmazás kiépítése után a következő paranccsal nevet, és az alkalmazás létrehozása:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name`Ez a név az alkalmazáspéldány használni szeretne. További paraméterek lekérheti az előzőleg létrehozott alkalmazás jegyzékében.

Az alkalmazás nevét a előtaggal kell kezdődnie `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Szolgáltatások az új alkalmazás létrehozása

Miután létrehozott egy alkalmazást, a szolgáltatások létrehozására az alkalmazásból. A következő példában azt hozzon létre egy új állapotmentes az alkalmazásból. A szolgáltatásokat, amelyeket létrehozhat egy alkalmazást a korábban kiépített alkalmazáscsomagban szolgáltatás jegyzékfájl vannak definiálva.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Alkalmazások üzembe helyezését és állapotának ellenőrzése

Minden épségének ellenőrzése, hogy az alábbi parancsokkal állapotát:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Győződjön meg arról, hogy a szolgáltatás nem működik megfelelően, hasonló parancsok segítségével a szolgáltatás és az alkalmazás állapotának beolvasása:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Kifogástalan szolgáltatások és alkalmazások egy `HealthState` értékének `Ok`.

## <a name="remove-an-existing-application"></a>Távolítsa el a meglévő alkalmazás

Az alkalmazás eltávolításához hajtsa végre a következő feladatokat:

### <a name="delete-the-application"></a>Az alkalmazás törlése

Az alkalmazás törléséhez használja a következő parancsot:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Az alkalmazástípus leépíteni a következőt:

Az alkalmazás törlése után is leválasztja az alkalmazástípus, ha már nincs szüksége. Az alkalmazástípus leépítése, a következő paranccsal:

```azurecli
sfctl application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

A típus neve és típusa verziója meg kell egyeznie a nevét és verzióját az előzőleg létrehozott alkalmazásjegyzékben.

## <a name="upgrade-application"></a>Alkalmazás frissítése

Miután létrehozta az alkalmazást, ismételje meg ugyanazokat a lépéseket egy második verziója az alkalmazás telepítéséhez. Ezt követően a Service Fabric-alkalmazás frissítését, is átmenet az alkalmazás a második verzióját. További információkért lásd: a dokumentáció a [Service Fabric az alkalmazásfrissítéseket](service-fabric-application-upgrade.md).

A frissítés végrehajtásához először az alkalmazás azt korábban az használja ugyanazokat a parancsokat a következő verziójában kiépítése:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Ajánlott majd, végezheti el a figyelt automatikus frissítése a frissítés a következő parancs futtatásával:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Frissítések bírálja felül a meglévő paramétereket bármilyen készlet van megadva. Alkalmazás paraméterei kell átadni argumentumként a frissítési parancs, ha szükséges. Alkalmazás paraméterei kell kódolható egy JSON-objektum.

Korábban a megadott paraméterek lekéréséhez használja a `sfctl application info` parancsot.

Ha egy alkalmazás frissítése folyamatban van, az állapot lekérhető használatával a `sfctl application upgrade-status` parancsot.

Végül, ha egy frissítés folyamatát, és hozzá lehet megszakítani, használhatja a `sfctl application upgrade-rollback` vissza tudják állítani a frissítést.

## <a name="next-steps"></a>Következő lépések

* [Service Fabric CLI alapjai](service-fabric-cli.md)
* [A Service Fabric Linux első lépések](service-fabric-get-started-linux.md)
* [A Service Fabric-alkalmazás frissítés indítása](service-fabric-application-upgrade.md)
