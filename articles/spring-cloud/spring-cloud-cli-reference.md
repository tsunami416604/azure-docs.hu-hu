---
title: az tavaszi felhő
description: Az Azure Spring Cloud kezelése az Azure CLI használatával
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298776"
---
# <a name="az-spring-cloud"></a>az tavaszi felhő

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Az Azure Spring Cloud kezelése az Azure CLI használatával

>[!Note]
> Az Azure Spring Cloud jelenleg előzetes verzióban érhető el.  Ezek a parancsok egy későbbi kiadásban módosíthatók vagy eltávolíthatók.

| az tavaszi felhő |  |
|------|------:|
| [az tavaszi felhő létrehozása](#az-spring-cloud-create) | Hozzon létre egy Azure Spring Cloud-példányt. |
| [az tavaszi felhő törlése](#az-spring-cloud-delete) | Töröljön egy Azure Spring Cloud-példányt. |
| [az tavaszi felhőlista](#az-spring-cloud-list) | Az adott erőforráscsoportban az összes Azure Spring Cloud-példány t, ellenkező esetben sorolja fel az előfizetési azonosítókat. |
| [az tavaszi felhő show](#az-spring-cloud-show) | Az Azure Spring Cloud részleteinek megjelenítése. |

| az tavaszi felhő alkalmazás | Az Azure Spring Cloud ban lévő alkalmazások kezelésére szolgáló parancsok.  |
| ---- | ----: |
| [az tavaszi felhő alkalmazás létrehozása](#az-spring-cloud-app-create) | Hozzon létre egy új alkalmazást az Azure Spring Cloud alapértelmezett központi telepítésével. |
| [az tavaszi felhő alkalmazás törlése](#az-spring-cloud-app-delete) | Töröljön egy alkalmazást az Azure Spring Cloud szolgáltatásban. |
| [az tavaszi felhőalkalmazás telepítése](#az-spring-cloud-app-deploy) | Telepítse a forráskódból vagy egy előre elkészített bináris fájlból egy alkalmazásba, és frissítse a kapcsolódó konfigurációkat. |
| [az tavaszi felhőalkalmazás-lista](#az-spring-cloud-app-list) | Az Azure Spring Cloud összes alkalmazásának listázása. |
| [az tavaszi felhőalkalmazás újraindítása](#az-spring-cloud-app-restart) | Indítsa újra az alkalmazás példányait az éles környezetben történő telepítés alapértelmezett beállításaival. |
| [az tavaszi felhőalkalmazás-méretezés](#az-spring-cloud-app-scale) | Manuálisan méretezz egy alkalmazást vagy annak központi példányait. |
| [az tavaszi felhőalapú alkalmazáskészlet-telepítés](#az-spring-cloud-app-set-deployment) | Állítsa be egy alkalmazás éles telepítését. |
| [az tavaszi felhő alkalmazás show](#az-spring-cloud-app-show) | Egy alkalmazás részleteinek megjelenítése az Azure Spring Cloud ban. |
| [az tavaszi felhőalkalmazás show-deploy-log](#az-spring-cloud-app-show-deploy-log) | Buildnaplók megjelenítése a legújabb központi telepítés forrásból. Alapértelmezés szerint éles környezetben. |
| [az tavaszi felhő alkalmazás indítása](#az-spring-cloud-app-start) | Indítsa el az alkalmazás példányait az éles környezetben történő központi telepítés alapértelmezett használatával. |
| [az tavaszi felhő alkalmazás leállítása](#az-spring-cloud-app-stop) | Az alkalmazás példányainak leállítása az éles környezetben történő központi telepítés alapértelmezett használatával. |
| [az tavaszi felhőalkalmazás frissítése](#az-spring-cloud-app-update) | Frissítse a megadott alkalmazás konfigurációját. |

| az tavaszi felhőalkalmazás-kötés | Az Azure Data Services használatával a kötések kezelésére szolgáló parancsok.  A beállítások érvénybe lépése előtt újra kell indítani az alkalmazást. |
| --- | ---: |
| [az tavaszi felhőalkalmazás-kötési lista](#az-spring-cloud-app-binding-list) | Az összes szolgáltatáskötés listázása egy alkalmazásban. |
| [az tavaszi felhőalkalmazás kötéseltávolítása](#az-spring-cloud-app-binding-remove) | Távolítsa el a szolgáltatáskötést az alkalmazásból. |
| [az tavaszi felhőalkalmazás-kötési show](#az-spring-cloud-app-binding-show) | A szolgáltatáskötés részleteinek megjelenítése. |
| [az tavaszi felhőalkalmazás kötési kozmosz hozzáadása](#az-spring-cloud-app-binding-cosmos-add) | Kösse össze az Azure CosmosDB-t az alkalmazással. |
| [az tavaszi felhőalkalmazás-kötési cosmos frissítés](#az-spring-cloud-app-binding-cosmos-update) | Frissítsen egy Azure CosmosDB szolgáltatáskötést. |
| [az tavaszi felhő alkalmazás kötés mysql hozzá](#az-spring-cloud-app-binding-mysql-add) | Kösse össze a MySQL Azure-adatbázisát az alkalmazással. |
| [az tavaszi felhőalkalmazás kötésmysql frissítés](#az-spring-cloud-app-binding-mysql-update) | Frissítsen egy Azure-adatbázist a MySQL szolgáltatáskötéshez. |
| [az tavaszi felhő alkalmazás kötés redis hozzá](#az-spring-cloud-app-binding-redis-add) | Kösse meg a Redis Azure-gyorsítótárát az alkalmazással. |
| [az tavaszi felhőalkalmazás-kötés redis frissítés](#az-spring-cloud-app-binding-redis-update) | Frissítsen egy Azure-gyorsítótárat a Redis szolgáltatáskötéshez. |

| az tavaszi felhőalapú alkalmazások telepítése | Parancsok egy alkalmazás üzembe helyezésének életciklusának kezelésére az Azure Spring Cloud ban. |
| --- | ---: |
| [az tavaszi felhőalapú alkalmazások üzembe helyezése](#az-spring-cloud-app-deployment-create) | Hozzon létre egy átmeneti központi telepítést az alkalmazáshoz. |
| [az tavaszi felhőalkalmazás telepítése törlés](#az-spring-cloud-app-deployment-delete) | Törölje az alkalmazás központi telepítését. |
| [az tavaszi felhőalkalmazás-telepítési lista](#az-spring-cloud-app-deployment-list) | Az alkalmazások összes központi telepítésének listázása. |
| [az tavaszi felhőalapú alkalmazás-telepítési show](#az-spring-cloud-app-deployment-show) | A központi telepítés részleteinek megjelenítése. |

| az tavaszi felhő config-szerver | Az Azure Spring Cloud Config Server kezeléséhez szükséges parancsok. |
| --- | ---: |
| [az tavaszi felhő config-server tiszta](#az-spring-cloud-config-server-clear) | Törölje a Config Server összes beállítását. |
| [az tavaszi felhőconfig-server set](#az-spring-cloud-config-server-set) | Definiálja a Config Server kiszolgálót YAML-fájlból. |
| [az tavaszi felhő config-szerver show](#az-spring-cloud-config-server-show) | A Config Server konfigurációjának megjelenítése. |
| [az tavaszi felhő config szerver git készlet](#az-spring-cloud-config-server-git-set) | Adja meg a Config Server git-tulajdonságait.  A korábbi értékek felülíródnak. |
| [az tavaszi felhő config szerver git társzóhozzáadása](#az-spring-cloud-config-server-git-repo-add) | Adjon hozzá egy új git-tárház konfigurációját a Config Server kiszolgálóhoz. |
| [az tavaszi felhő config szerver git repo lista](#az-spring-cloud-config-server-git-repo-list) | Sorolja fel a Config Server összes git-tárház-konfigurációját. |
| [az tavaszi felhő config szerver git repo eltávolítása](#az-spring-cloud-config-server-git-repo-remove) | Távolítsa el a megadott git-tárházat a Config Server kiszolgálóról. |

| az rugó-felhő teszt-végpont | A végponttesztelés kezeléséhez szükséges parancsok az Azure Spring Cloud ban |
| --- | ---: |
| [az rugó-felhő tesztvégpont letiltása](#az-spring-cloud-test-endpoint-disable) | A tesztvégpont letiltása. |
| [az rugós-felhő tesztvégpont engedélyezése](#az-spring-cloud-test-endpoint-enable) | Engedélyezze a tesztvégpontot. |
| [az rugós felhőteszt-végpontok listája](#az-spring-cloud-test-endpoint-list) | A tesztvégpont-kulcsok listázása. |
| [az rugó-felhő teszt-végpont megújítási-kulcs](#az-spring-cloud-test-endpoint-renew-key) | Tesztvégpont-kulcs újragenerálása. |

## <a name="az-spring-cloud-create"></a>az tavaszi felhő létrehozása

Hozzon létre egy új alkalmazást az Azure Spring Cloud alapértelmezett központi telepítésével.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | Az Azure Spring Cloud-példány neve. |
| --erőforrás-csoport -g | Az alkalmazás erőforráscsoportját adja meg.  Az alapértelmezett csoport konfigurálása a`az configure --defaults group=<name>` |

| Opcionális paraméterek | |
| --- | ---: |
| --hely -l | Megadja az alkalmazás kiszolgálói helyét.  Érvényes helyek keresése a következő vel:`az account list-locations` |
| --nem-várjon | Ne végezze el a hosszú ideig futó műveleteket.

### <a name="examples"></a>Példák

Új Azure Spring Cloud létrehozása a WestUS-ban

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az tavaszi felhő törlése

Töröljön egy Azure Spring Cloud-példányt.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | A törölni aandó Azure Spring Cloud-példány neve. |
| --erőforrás-csoport -g | Annak az erőforráscsoportnak a neve, amelyhez az Azure Spring Cloud tartozik. |

| Opcionális paraméterek | |
| --- | ---: |
| -no-wait | Ne várjon a hosszú ideig futó műveletek befejezésére. |

### <a name="example"></a>Példa

Töröljön egy "MyService" nevű Azure Spring Cloud-példányt a "MyResourceGroup" szolgáltatásból.

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az tavaszi felhőlista

Az adott erőforráscsoporthoz társított összes Azure Spring Cloud-példány listázása. Ha nincs megadva erőforráscsoport, sorolja fel az előfizetés-azonosítókat.

```azurecli
az spring-cloud list --resource-group -g
```

| Kötelező paraméterek | |
| --- | ---: |
| --erőforrás-csoport -g | Az erőforráscsoport neve. |

## <a name="az-spring-cloud-show"></a>az tavaszi felhő show

A megadott Azure Spring Cloud-példány részleteinek megjelenítése.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | Az Azure Spring Cloud-példány neve. |
| --erőforrás-csoport -g | Annak az erőforráscsoportnak a neve, amelyhez az Azure Spring Cloud-példány tartozik.

## <a name="az-spring-cloud-app-create"></a>az tavaszi felhő alkalmazás létrehozása

Hozzon létre egy új alkalmazást az Azure Spring Cloud ban.

```azurecli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --cpu | Virtuális magok száma példányonként.  Alapértelmezett: 1. |
| --enable-persistent-storage | Logikai érték.  Ha ez igaz, egy 50 GB-os lemezt csatlakoztat alapértelmezett elérési úttal. |
| --példány-szám | Példány száma.  Alapértelmezett: 1. |
| --nyilvános | Logikai érték.  Ha ez igaz, nyilvános tartományt rendel hozzá. |
| --memória | A példányonkénti GB memória száma.  Alapértelmezett: 1. |

### <a name="examples"></a>Példák

Hozzon létre egy alkalmazást az alapértelmezett konfigurációval.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Hozzon létre egy nyilvánosan elérhető alkalmazást 3 példányban.  Minden példány 3 GB memóriával és 2 CPU-maggal rendelkezik.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az tavaszi felhő alkalmazás törlése

Töröl egy alkalmazást az Azure Spring Cloud szolgáltatásban.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-app-deploy"></a>az tavaszi felhőalkalmazás telepítése

Telepítsen egy alkalmazást az Azure Spring Cloud-ba forráskódból vagy egy előre elkészített bináris fájlból, és frissítse a kapcsolódó konfigurációkat.

```azurecli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --cpu | A virtuális CPI-magok példányonkénti száma. |
| --telepítés -d | Meglévő alkalmazásközponti telepítés neve.  Alapértelmezés szerint az éles környezetben, ha nincs megadva. |
| --env | Szóközre tagolt környezeti változók "key[=value]" formátumban. |
| --példány-szám | Példányok száma. |
| --jar-path | Ha rendelkezésre áll, telepítse jar adott elérési útról. Ellenkező esetben telepítse az aktuális mappát kátrányként. |
| --jvm-opciók | JVM-beállításokat tartalmazó karakterlánc.  Használja a '=' helyett a ' '-t a rendszerhéj-elemzési hibák elkerülése érdekében. Pl. `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memória | A példányonkénti GB memória száma. |
| --nem-várjon | Ne várjon a hosszú ideig futó műveletek befejezésére. |
| --runtime-verzió | Az alkalmazásban használt nyelv futásidejű verziója.  Megengedett `Java_11`értékek: `Java_8`, . |
| --célmodul | A bevetendő gyermekmodul.  Akkor szükséges, ha több jar csomag épül a forráskódból. |
| --verzió | Központi telepítési verzió.  Változatlan, ha nincs beállítva. |

### <a name="examples"></a>Példák

Forráskód telepítése egy alkalmazásba. Ez becsomagolja az aktuális könyvtárat, bináris fájlt hoz létre a Pivotal Build szolgáltatás használatával, majd telepíti az alkalmazásba.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Előre elkészített jar üzembe helyezése egy alkalmazáshoz jvm-beállítások és környezeti változók használatával.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Forráskód központi telepítése egy alkalmazás adott központi telepítéséhez.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az tavaszi felhőalkalmazás-lista

Az Azure Spring Cloud-példányban az összes alkalmazás listázása.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Kötelező paraméterek | |
| --- | ---: |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-app-restart"></a>az tavaszi felhőalkalmazás újraindítása

Indítsa újra az alkalmazás példányait.  Alapértelmezés szerint az éles környezetben.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --telepítés -d | Az alkalmazás meglévő központi telepítésének neve.  Alapértelmezés szerint az éles környezetben, ha nincs megadva. |
| --nem-várjon | Ne várjon a hosszú ideig futó műveletek befejezésére. |

## <a name="az-spring-cloud-app-scale"></a>az tavaszi felhőalkalmazás-méretezés

Manuálisan méretezz egy alkalmazást vagy annak központi példányait.

```azurecli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --cpu | Virtuális processzormagok száma alkalmazáspéldányonként. |
| --telepítés -d | Az alkalmazás meglévő központi telepítésének neve.  Alapértelmezés szerint az éles környezetben, ha nincs megadva. |
| --példány-szám | Az alkalmazás példányainak száma. |
| --memória | A GB-os memória száma alkalmazáspéldányonként. |
| --nem-várjon | Ne várja meg, amíg a hosszú ideig futó műveletek befejeződnek. |

### <a name="examples"></a>Példák

Egy alkalmazást 4 PROCESSZORmagra és példányonként 8 GB memóriára méretezhet.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Az alkalmazás központi telepítésének horizontális felskálázása 5 példányra.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az tavaszi felhőalapú alkalmazáskészlet-telepítés

Adja meg az alkalmazás éles telepítésének konfigurációs beállításait.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Kötelező paraméterek | |
| --- | ---: |
| --telepítés -d | Az alkalmazás meglévő központi telepítésének neve. |
| --név -n | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --nem-várjon | Ne várja meg, amíg a hosszú ideig futó műveletek befejeződnek. |

### <a name="examples"></a>Példák

Az alkalmazás éles környezetbe való felhelyezése.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az tavaszi felhő alkalmazás show

Egy alkalmazás részleteinek megjelenítése az Azure Spring Cloud ban.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az tavaszi felhőalkalmazás show-deploy-log

Az utolsó központi telepítés buildnaplójának megjelenítése a forráskódból.  Alapértelmezés szerint éles környezetben.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --telepítés -d | Az alkalmazás meglévő központi telepítésének neve.  Alapértelmezés szerint az éles környezetben. |

## <a name="az-spring-cloud-app-start"></a>az tavaszi felhő alkalmazás indítása

Elindítja az alkalmazás példányait.  Alapértelmezés szerint éles környezetben.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --telepítés -d | Az alkalmazás meglévő központi telepítésének neve.  Alapértelmezés szerint az éles környezetben. |
| --nem-várjon | Ne várja meg, amíg a hosszú ideig futó műveletek befejeződnek. |

## <a name="az-spring-cloud-app-stop"></a>az tavaszi felhő alkalmazás leállítása

Az alkalmazás példányainak leállítása.  Alapértelmezés szerint az éles környezetben.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --telepítés -d | Az alkalmazás meglévő központi telepítésének neve.  Alapértelmezés szerint az éles környezetben. |
| --nem-várjon | Ne várja meg, amíg a hosszú ideig futó műveletek befejeződnek. |

## <a name="az-spring-cloud-app-update"></a>az tavaszi felhőalkalmazás frissítése

Az alkalmazás tárolt konfigurációjának frissítése.

```azurecli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| Kötelező paraméterek | |
| --- | ---: |
| --név -n | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --telepítés -d | Az alkalmazás meglévő központi telepítésének neve.  Alapértelmezés szerint az éles környezetben. |
| --enable-persistent-storage | Logikai.  Ha igaz, csatlakoztassa az 50 GB-os lemezt az alapértelmezett elérési úttal. |
| --env | Szóközre tagolt környezeti változók "key[=value]" formátumban. |
| --nyilvános | Logikai.  Ha ez igaz, rendeljen nyilvános tartományt az alkalmazáshoz. |
| --jvm-opciók | JVM-beállításokat tartalmazó karakterlánc.  Használja a '=' helyett a ' '-t a rendszerhéj-elemzési hibák elkerülése érdekében. Pl. `--jvm-options='-Xms1024m -Xmx2048m`. |
| --nem-várjon | Ne várja meg, amíg a hosszú ideig futó műveletek befejeződnek. |
| --runtime-verzió | Az alkalmazásban használt nyelv futásidejű verziója.  Megengedett `Java_11`értékek: `Java_8`, . |

### <a name="example"></a>Példa

Adjon hozzá egy környezeti változót az alkalmazáshoz.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az tavaszi felhőalkalmazás-kötési lista

Az összes szolgáltatáskötés listázása egy alkalmazásban.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Kötelező paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-app-binding-remove"></a>az tavaszi felhőalkalmazás kötéseltávolítása

Távolítsa el a szolgáltatáskötést az alkalmazásból.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Kötelező paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --név | Az eltávolítandó szolgáltatáskötés neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-app-binding-show"></a>az tavaszi felhőalkalmazás-kötési show

A szolgáltatáskötés részleteinek megjelenítése.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Kötelező paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatáskötés neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az tavaszi felhőalkalmazás kötési kozmosz hozzáadása

Kösse össze az Azure Cosmos DB-t az alkalmazással.

```azurecli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Kötelező paraméterek | |
| --- | ---: |
| --api-típus | Adja meg az API-típust a következő értékek egyikével: cassandra, gremlin, mongo, sql, table. |
| --alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatáskötés neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

|Opcionális paraméterek | |
| --- | ---: |
| --gyűjtemény neve | A gyűjtemény neve.  A Gremlin használatakor szükséges. |
| --adatbázis-név | Az adatbázis neve.  Mongo, SQL és Gremlin használataesetén szükséges. |
| --kulcs-tér | Cassandra kulcs-tér.  Cassandra használatakor szükséges. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az tavaszi felhőalkalmazás-kötési cosmos frissítés

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Kötelező paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatáskötés neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

|Opcionális paraméterek | |
| --- | ---: |
| --gyűjtemény neve | A gyűjtemény neve.  A Gremlin használatakor szükséges. |
| --adatbázis-név | Az adatbázis neve.  Mongo, SQL és Gremlin használataesetén szükséges. |
| --kulcs-tér | Cassandra kulcs-tér.  Cassandra használatakor szükséges. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az tavaszi felhő alkalmazás kötés mysql hozzá

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Kötelező paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --adatbázis-név | Az adatbázis neve. |
| --gomb | A szolgáltatás API-kulcsa. |
| --név | A szolgáltatáskötés neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --erőforrás-azonosító | A kötéshez kapcsolódó szolgáltatás Azure-erőforrásazonosítója. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |
| --username | Felhasználónév az adatbázis-hozzáféréshez. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az tavaszi felhőalkalmazás kötésmysql frissítés

Frissítse a szolgáltatáskötési kapcsolatot az alkalmazás egy Azure Database for MySQL.Update the service binding connection the app to an Azure Database for MySQL.

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Kötelező paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatáskötés neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --adatbázis-név | Az adatbázis neve. |
| --gomb | A szolgáltatás API-kulcsa. |
| --username | Felhasználónév az adatbázis-hozzáféréshez. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az tavaszi felhő alkalmazás kötés redis hozzá

Kösse meg a Redis Azure-gyorsítótárát az alkalmazással.

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Kötelező paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatáskötés neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --erőforrás-azonosító | Annak a szolgáltatásnak az Azure-erőforrásazonosítója, amelyhez kötődni szeretne. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --disable-ssl | Tiltsa le a TLS-t. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az tavaszi felhőalkalmazás-kötés redis frissítés

Frissítsen egy szolgáltatáskötést az Azure Cache for Redis számára.

| Kötelező paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatáskötés neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --disable-ssl | Tiltsa le a TLS-t. |

## <a name="az-spring-cloud-app-deployment-create"></a>az tavaszi felhőalapú alkalmazások üzembe helyezése

Hozzon létre egy átmeneti központi telepítést az alkalmazáshoz.

Kód központi telepítéséhez vagy a beállítások `az spring-cloud app deploy --deployment <staging-deployment>` meglévő központi telepítéshez való frissítéséhez <staging deployment>használja vagy használja az "az rugós felhőalkalmazás frissítését --üzembe helyezés.

| Kötelező paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatáskötés neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --cpu | A virtuális processzormagok száma példányonként.  Alapértelmezett: 1 |
| --env | Szóközre tagolt környezeti változók "key[=value]" formátumban. |
| --példány-szám | Példányok száma. Alapértelmezett: 1. |
| --jar-path | Ha rendelkezésre áll, telepítse jar.  Ellenkező esetben telepítse az aktuális mappát kátrányként. |
| --jvm-opciók | JVM-beállításokat tartalmazó karakterlánc.  Használja a '=' helyett a ' '-t a rendszerhéj-elemzési hibák elkerülése érdekében. Pl. `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memória | A példányonkénti GB memória száma. |
| --nem-várjon | Ne várjon a hosszú ideig futó műveletek befejezésére. |
| --runtime-verzió | Az alkalmazásban használt nyelv futásidejű verziója.  Megengedett `Java_11`értékek: `Java_8`, . |
| --skip-clone-beállítások | Átmeneti központi telepítés létrehozása az aktuális éles telepítési beállítások klónozásával. |
| --célmodul | A bevetendő gyermekmodul.  Akkor szükséges, ha több jar csomag épül a forráskódból. |
| --verzió | Központi telepítési verzió.  Változatlan, ha nincs beállítva. |

### <a name="examples"></a>Példák

Forráskód központi telepítése az alkalmazás új központi telepítéséhez.  Ez becsomagolja az aktuális könyvtárat, bináris fájlt hoz létre a Pivotal Build System használatával, majd telepíti.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Előre elkészített jar üzembe helyezése egy alkalmazásba JVM-beállításokkal és környezeti változókkal.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az tavaszi felhőalkalmazás telepítése törlés

Törölje az alkalmazás központi telepítését.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Kötelező paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --név | A központi telepítés neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-app-deployment-list"></a>az tavaszi felhőalkalmazás-telepítési lista

Az alkalmazások összes központi telepítésének listázása.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Kötelező paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-app-deployment-show"></a>az tavaszi felhőalapú alkalmazás-telepítési show

A központi telepítés részleteinek megjelenítése.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Kötelező paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --név | A központi telepítés neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --szolgáltatás -s | Az Azure Spring Cloud neve.  Az alapértelmezett szolgáltatást a `az configure --defaults spring-cloud=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-config-server-clear"></a>az tavaszi felhő config-server tiszta

Törölje a Config Server összes konfigurációs beállítását.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Kötelező paraméterek | |
| --- | ---: |
| --név | Az Azure Spring Cloud neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-config-server-set"></a>az tavaszi felhőconfig-server set

A konfigurációs kiszolgáló konfigurációs beállításainak megadása YAML-fájl használatával.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Kötelező paraméterek | |
| --- | ---: |
| --konfigurációs fájl | A Config Server konfigurációjához egy YAML-jegyzékfájl elérési útja. |
| --név | Az Azure Spring Cloud neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --nem-várjon | Ne végezze el a hosszú ideig futó műveleteket.

## <a name="az-spring-cloud-config-server-show"></a>az tavaszi felhő config-szerver show

A Konfigurációs kiszolgáló beállításainak megjelenítése.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Kötelező paraméterek | |
| --- | ---: |
| --név | Az Azure Spring Cloud neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-config-server-git-set"></a>az tavaszi felhő config-server git készlet

Állítsa be a Konfigurációs kiszolgáló git-tulajdonságait.  Ez felülírja az összes meglévő git tulajdonságot.

```azurecli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| Kötelező paraméterek | |
| --- | ---: |
| --név | Az Azure Spring Cloud neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --uri | A hozzáadott konfiguráció URI-ja. |

| Opcionális paraméterek | |
| --- | ---: |
| --halasztás | Ideiglenesen tárolja az objektumot a helyi gyorsítótárban, ahelyett, hogy az Azure-ba küldené.  A `az cache` megtekintéshez / törléshez használható. |
| --host-kulcs | A hozzáadott konfiguráció gazdakulcsa. |
| --host-kulcs-algoritmus | A hozzáadott konfiguráció gazdakulcs-algoritmusa. |
| --címke | A hozzáadott konfiguráció címkéje. |
| --password | A hozzáadott konfiguráció jelszava. |
| --titkos kulcs | A hozzáadott konfiguráció titkos kulcsa. |
| --keresési útvonalak | A hozzáadott konfiguráció elérési útjainak keresése.  Több útvonalhoz használjon vesszőhatárolókat. |
| --szigorú-host-kulcs-ellenőrzés | Lehetővé teszi a hozzáadott konfiguráció szigorú gazdakulcs-ellenőrzését. |
| --username | A hozzáadott konfiguráció felhasználóneve. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az tavaszi felhő config-server git repo hozzá

```azurecli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| Kötelező paraméterek | |
| --- | ---: |
| --név | Az Azure Spring Cloud neve. |
| --repo-név | A tárműtér URI-ja. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --uri | A hozzáadott konfiguráció URI-ja. |

| Opcionális paraméterek | |
| --- | ---: |
| --halasztás | Ideiglenesen tárolja az objektumot a helyi gyorsítótárban, ahelyett, hogy az Azure-ba küldené.  A `az cache` megtekintéshez / törléshez használható. |
| --host-kulcs | A hozzáadott konfiguráció gazdakulcsa. |
| --host-kulcs-algoritmus | A hozzáadott konfiguráció gazdakulcs-algoritmusa. |
| --címke | A hozzáadott konfiguráció címkéje. |
| --password | A hozzáadott konfiguráció jelszava. |
| --minta | Minta a tármű.  Több útvonalhoz használjon vesszőhatárolókat.|
| --titkos kulcs | A hozzáadott konfiguráció titkos kulcsa. |
| --keresési útvonalak | A hozzáadott konfiguráció elérési útjainak keresése.  Több útvonalhoz használjon vesszőhatárolókat. |
| --szigorú-host-kulcs-ellenőrzés | Lehetővé teszi a hozzáadott konfiguráció szigorú gazdakulcs-ellenőrzését. |
| --username | A hozzáadott konfiguráció felhasználóneve. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az tavaszi felhő config-server git repo lista

A Config Server ben definiált összes git-ellenőrző fájl felsorolása

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Kötelező paraméterek | |
| --- | ---: |
| --név | Az Azure Spring Cloud neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --halasztás | Ideiglenesen tárolja az objektumot a helyi gyorsítótárban, ahelyett, hogy az Azure-ba küldené.  A `az cache` megtekintéshez / törléshez használható. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az tavaszi felhő config-server git repo eltávolítás

Meglévő git-tármű-konfiguráció eltávolítása a Config Server kiszolgálóról.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Kötelező paraméterek | |
| --- | ---: |
| --név | Az Azure Spring Cloud neve. |
| --repo-név | A tárműtér URI-ja. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --halasztás | Ideiglenesen tárolja az objektumot a helyi gyorsítótárban, ahelyett, hogy az Azure-ba küldené.  A `az cache` megtekintéshez / törléshez használható. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az rugó-felhő tesztvégpont letiltása

Az Azure Spring Cloud tesztvégpontjának letiltása

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Kötelező paraméterek | |
| --- | ---: |
| --név | Az Azure Spring Cloud neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az rugós-felhő tesztvégpont engedélyezése

Engedélyezze a tesztvégpontot az Azure Spring Cloud számára. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Kötelező paraméterek | |
| --- | ---: |
| --név | Az Azure Spring Cloud neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |

## <a name="az-spring-cloud-test-endpoint-list"></a>az rugós felhőteszt-végpontok listája 

Az Azure Spring Cloud elérhető tesztvégpont-kulcsainak listázása.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Kötelező paraméterek | |
| --- | ---: |
| --név | Az Azure Spring Cloud neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |

| Opcionális paraméterek | |
| --- | ---: |
| --alkalmazás | Az alkalmazás neve. |
| --telepítés -d | Az alkalmazás meglévő központi telepítésének neve.  Alapértelmezés szerint a termelés, ha nincs megadva. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az rugó-felhő teszt-végpont megújítási-kulcs

Az Azure Spring Cloud tesztvégpont-kulcsának újragenerálása.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Kötelező paraméterek | |
| --- | ---: |
| --név | Az Azure Spring Cloud neve. |
| --erőforrás-csoport -g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a `az configure --defaults group=<name>`használatával konfigurálhatja. |
| --típus | A tesztvégpont-kulcs típusa.  Engedélyezett értékek: Elsődleges, Másodlagos. |
