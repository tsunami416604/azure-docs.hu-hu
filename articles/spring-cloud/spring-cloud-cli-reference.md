---
title: az Spring Cloud
description: Az Azure Spring Cloud kezelése az Azure CLI használatával
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 957931407987e116924677341df4effe5347590e
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187642"
---
# <a name="az-spring-cloud"></a>az Spring-Cloud

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Az Azure Spring Cloud kezelése az Azure CLI használatával

>[!Note]
> Az Azure Spring Cloud jelenleg előzetes verzióban érhető el.  Előfordulhat, hogy ezek a parancsok egy későbbi kiadásban módosíthatók vagy eltávolíthatók.

| az Spring-Cloud | Parancsok |
|------|------:|
| [az Spring-Cloud Create](#az-spring-cloud-create) | Hozzon létre egy Azure Spring Cloud-példányt. |
| [az Spring-Cloud delete](#az-spring-cloud-delete) | Azure Spring Cloud-példány törlése. |
| [az Spring-Cloud List](#az-spring-cloud-list) | Az adott erőforráscsoport összes Azure Spring Cloud-példányának listázása, egyéb esetben az előfizetési azonosítók listázása. |
| [az Spring-Cloud show](#az-spring-cloud-show) | Egy Azure Spring-felhő részleteinek megjelenítése. |

| az Spring-Cloud app | Parancsok az Azure Spring-Felhőbeli alkalmazások kezeléséhez.  |
| ---- | ----: |
| [az Spring-Cloud app Create](#az-spring-cloud-app-create) | Hozzon létre egy új alkalmazást alapértelmezett üzembe helyezéssel az Azure Spring Cloud-ban. |
| [az Spring-Cloud app delete](#az-spring-cloud-app-delete) | Egy alkalmazás törlése az Azure Spring-felhőben. |
| [az Spring-Cloud app Deploy](#az-spring-cloud-app-deploy) | Üzembe helyezés forráskódból vagy egy előre elkészített bináris fájlból egy alkalmazásba és a kapcsolódó konfigurációk frissítése. |
| [az Spring-Cloud app List](#az-spring-cloud-app-list) | Az Azure Spring Cloud összes alkalmazásának listázása. |
| [az Spring-Cloud app restart](#az-spring-cloud-app-restart) | Indítsa újra az alkalmazás példányait az éles üzembe helyezési Alapértelmezések használatával. |
| [az Spring-Cloud app Scale](#az-spring-cloud-app-scale) | Egy alkalmazás vagy üzembe helyezésének manuális skálázása. |
| [az Spring-Cloud app set-Deployment](#az-spring-cloud-app-set-deployment) | Alkalmazás éles üzembe helyezésének beállítása. |
| [az Spring-Cloud app show](#az-spring-cloud-app-show) | Egy alkalmazás részleteinek megjelenítése az Azure Spring-felhőben. |
| [az Spring-Cloud app show-Deploy-log](#az-spring-cloud-app-show-deploy-log) | A forrásról származó legújabb központi telepítéshez tartozó összeállítási naplók megjelenítése. Az alapértelmezett érték az éles üzembe helyezés. |
| [az Spring-Cloud app Start](#az-spring-cloud-app-start) | Az alkalmazás példányainak elindítása éles üzembe helyezési alapértékek használatával. |
| [az Spring-Cloud app stop](#az-spring-cloud-app-stop) | Az alkalmazás példányainak leállítása éles üzembe helyezési alapértékek használatával. |
| [az Spring-Cloud app Update](#az-spring-cloud-app-update) | A megadott alkalmazás konfigurációjának frissítése. |

| az Spring-Cloud app kötés | Parancsok az Azure Data Services-vel való kötések kezeléséhez.  A beállítások érvénybe léptetéséhez újra kell indítani az alkalmazást. |
| --- | ---: |
| [az Spring-Cloud app kötési listája](#az-spring-cloud-app-binding-list) | Egy alkalmazás összes szolgáltatási kötésének listázása. |
| [az Spring-Cloud app bind Remove](#az-spring-cloud-app-binding-remove) | Távolítson el egy szolgáltatási kötést az alkalmazásból. |
| [az Spring-Cloud app bind show](#az-spring-cloud-app-binding-show) | Egy szolgáltatás kötésének részleteit jeleníti meg. |
| [az Spring-Cloud app bind Cosmos Add](#az-spring-cloud-app-binding-cosmos-add) | Egy Azure-CosmosDB kötése az alkalmazással. |
| [az Spring-Cloud app bind Cosmos Update](#az-spring-cloud-app-binding-cosmos-update) | Azure CosmosDB szolgáltatás kötésének frissítése. |
| [az Spring-Cloud app bind MySQL Add](#az-spring-cloud-app-binding-mysql-add) | Azure Database for MySQL kötése az alkalmazással. |
| [az Spring-Cloud app bind MySQL Update](#az-spring-cloud-app-binding-mysql-update) | Azure Database for MySQL szolgáltatás kötésének frissítése. |
| [az Spring-Cloud app kötési Redis hozzáadása](#az-spring-cloud-app-binding-redis-add) | Egy Azure-gyorsítótár kötése a Redis az alkalmazással. |
| [az Spring-Cloud app kötési Redis frissítése](#az-spring-cloud-app-binding-redis-update) | Azure cache frissítése a Redis Service-kötéshez. |

| az Spring-Cloud app Deployment | Parancsok egy alkalmazás üzembe helyezési életciklusának kezeléséhez az Azure Spring Cloud-ban. |
| --- | ---: |
| [az Spring-Cloud app Deployment Create](#az-spring-cloud-app-deployment-create) | Hozzon létre egy átmeneti üzembe helyezést az alkalmazáshoz. |
| [az Spring-Cloud app Deployment delete](#az-spring-cloud-app-deployment-delete) | Az alkalmazás központi telepítésének törlése. |
| [az Spring-Cloud app Deployment List](#az-spring-cloud-app-deployment-list) | Egy alkalmazás összes központi telepítésének listázása. |
| [az Spring-Cloud app Deployment show](#az-spring-cloud-app-deployment-show) | A központi telepítés részleteinek megjelenítése. |

| az Spring-Cloud config-Server | Parancsok az Azure Spring Cloud config-kiszolgáló kezeléséhez. |
| --- | ---: |
| [az Spring-Cloud config-Server Clear](#az-spring-cloud-config-server-clear) | A konfigurációs kiszolgáló összes beállításának törlése. |
| [az Spring-Cloud config-Server set](#az-spring-cloud-config-server-set) | Adja meg a konfigurációs kiszolgálót egy YAML-fájlból. |
| [az Spring-Cloud config-Server show](#az-spring-cloud-config-server-show) | A konfigurációs kiszolgáló konfigurációjának megjelenítése. |
| [az Spring-Cloud config Server git set](#az-spring-cloud-config-server-git-set) | Adja meg a konfigurációs kiszolgáló git-tulajdonságait.  A rendszer felülírja az előző értékeket. |
| [az Spring-Cloud config Server git-tárház hozzáadása](#az-spring-cloud-config-server-git-repo-add) | Adja hozzá a git-adattár új konfigurációját a konfigurációs kiszolgálóhoz. |
| [az Spring-Cloud config Server git-tárház listája](#az-spring-cloud-config-server-git-repo-list) | A konfigurációs kiszolgáló összes git-tárház-konfigurációjának listázása. |
| [az Spring-Cloud config Server git repo Remove](#az-spring-cloud-config-server-git-repo-remove) | Távolítsa el a megadott git-tárházat a konfigurációs kiszolgálóról. |

| az Spring-Cloud test-Endpoint | Parancsok a végpontok tesztelésének kezeléséhez az Azure Spring Cloud-ban |
| --- | ---: |
| [az Spring-Cloud test-Endpoint Disable](#az-spring-cloud-test-endpoint-disable) | Teszt végpont letiltása. |
| [az Spring-Cloud test-Endpoint Enable](#az-spring-cloud-test-endpoint-enable) | Tesztelési végpont engedélyezése. |
| [az Spring-Cloud test-Endpoint List](#az-spring-cloud-test-endpoint-list) | A tesztelési végpont kulcsainak listázása. |
| [az Spring-Cloud test-Endpoint megújít-Key](#az-spring-cloud-test-endpoint-renew-key) | Egy teszt-végponti kulcs újbóli létrehozása. |

## <a name="az-spring-cloud-create"></a>az Spring-Cloud Create

Hozzon létre egy új alkalmazást alapértelmezett üzembe helyezéssel az Azure Spring Cloud-ban.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | Az Azure Spring Cloud-példány neve. |
| --erőforrás-csoport-g | Megadja az alkalmazáshoz tartozó erőforráscsoportot.  Az alapértelmezett csoport konfigurálása a használatával`az configure --defaults group=<name>` |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --Location-l | Megadja az alkalmazás kiszolgálójának helyét.  Érvényes helyszínek keresése a`az account list-locations` |
| --No-WAIT | A hosszú ideig futó műveletek befejezéséhez ne.

### <a name="examples"></a>Példák

Új Azure Spring-felhő létrehozása a WestUS-ben

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az Spring-Cloud delete

Azure Spring Cloud-példány törlése.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | A törlendő Azure Spring Cloud-példány neve. |
| --erőforrás-csoport-g | Azon erőforráscsoport neve, amelyhez az Azure Spring-felhő tartozik. |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| -No-WAIT | Várjon, amíg a hosszú ideig futó műveletek befejeződik. |

### <a name="example"></a>Példa

Egy "MyService" nevű Azure Spring Cloud-példány törlése a következőből: "MyResourceGroup".

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az Spring-Cloud List

Az adott erőforráscsoporthoz társított összes Azure Spring Cloud-példány listázása. Ha nincs megadva erőforráscsoport, sorolja fel az előfizetési azonosítókat.

```azurecli
az spring-cloud list --resource-group -g
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --erőforrás-csoport-g | Az erőforráscsoport neve. |

## <a name="az-spring-cloud-show"></a>az Spring-Cloud show

A megadott Azure Spring Cloud-példány részleteinek megjelenítése.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | Az Azure Spring Cloud-példány neve. |
| --erőforrás-csoport-g | Azon erőforráscsoport neve, amelyhez az Azure Spring Cloud-példány tartozik.

## <a name="az-spring-cloud-app-create"></a>az Spring-Cloud app Create

Hozzon létre egy új alkalmazást egy Azure Spring-felhőben.

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

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --CPU | Virtuális magok száma egy példányban.  Alapértelmezett: 1. |
| --Enable-perzisztens-Storage | Logikai érték.  Ha az értéke igaz, a egy 50 GB-lemezt csatlakoztat az alapértelmezett elérési úttal. |
| – példányok száma | A példányok száma.  Alapértelmezett: 1. |
| – nyilvános | Logikai érték.  Ha az értéke igaz, egy nyilvános tartományt rendel hozzá. |
| – memória | A memória GB memóriájának száma egy példányban.  Alapértelmezett: 1. |

### <a name="examples"></a>Példák

Hozzon létre egy alkalmazást az alapértelmezett konfigurációval.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Hozzon létre egy nyilvánosan elérhető alkalmazást 3 példányban.  Minden példány 3 GB memóriával és 2 CPU-maggal rendelkezik.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az Spring-Cloud app delete

Töröl egy alkalmazást az Azure Spring-felhőben.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-deploy"></a>az Spring-Cloud app Deploy

Helyezzen üzembe egy alkalmazást az Azure Spring Cloud forráskódból vagy egy előre elkészített bináris fájlból, és frissítse a kapcsolódó konfigurációkat.

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

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --CPU | A virtuális CPI-magok száma példányban. |
| --Deployment-d | Egy meglévő alkalmazás központi telepítésének neve.  Ha nincs megadva, az alapértelmezett érték az éles üzembe helyezés. |
| --env | Szóközzel tagolt környezeti változók a "Key [= Value]" formátumban. |
| – példányok száma | Példányok száma. |
| --jar-elérési út | Ha meg van adva, telepítse a jar-t a megadott elérési útról. Egyéb esetben a jelenlegi mappát tar-ként telepítse. |
| --JVM – beállítások | JVM-beállításokat tartalmazó karakterlánc.  A rendszerhéj-elemzési hibák elkerülése érdekében használja a "=" helyett a következőt: "". Például: `--jvm-options='-Xms1024m -Xmx2048m` . |
| – memória | A memória GB memóriájának száma egy példányban. |
| --No-WAIT | Várjon, amíg a hosszú ideig futó műveletek befejeződik. |
| --futtatókörnyezet-verzió | Az alkalmazásban használt nyelv futtatókörnyezet-verziója.  Megengedett értékek: `Java_11` , `Java_8` . |
| --cél-modul | A telepítendő alárendelt modul.  Kötelező, ha több jar-csomag van kiépítve a forráskódból. |
| --Version | Központi telepítés verziója.  Ha nincs beállítva, változatlan marad. |

### <a name="examples"></a>Példák

Forráskód üzembe helyezése egy alkalmazásban. Ezzel becsomagolja az aktuális könyvtárat, létrehoz egy bináris fájlt a Pivotal Build szolgáltatás használatával, majd üzembe helyezi az alkalmazást.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Egy előre összeállított jar üzembe helyezése az JVM-beállításokkal és környezeti változók használatával.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Forráskód üzembe helyezése egy alkalmazás adott központi telepítéséhez.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az Spring-Cloud app List

Az Azure Spring Cloud-példány összes alkalmazásának listázása.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Kötelező paraméterek | Leírás |
| --- | :--- |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-restart"></a>az Spring-Cloud app restart

Indítsa újra az alkalmazás példányait.  Alapértelmezés szerint az éles környezetben üzemelő példány.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --Deployment-d | Az alkalmazás meglévő központi telepítésének neve.  Ha nincs megadva, az alapértelmezett érték az éles üzembe helyezés. |
| --No-WAIT | Várjon, amíg a hosszú ideig futó műveletek befejeződik. |

## <a name="az-spring-cloud-app-scale"></a>az Spring-Cloud app Scale

Egy alkalmazás vagy üzembe helyezésének manuális skálázása.

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

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --CPU | A virtuális CPU-magok száma az egyes alkalmazás-példányokban. |
| --Deployment-d | Az alkalmazás meglévő központi telepítésének neve.  Ha nincs megadva, az alapértelmezett érték az éles üzembe helyezés. |
| – példányok száma | Az alkalmazás példányainak száma. |
| – memória | A memória GB memóriájának száma az alkalmazás példányaiban. |
| --No-WAIT | Várjon, amíg a hosszan futó műveletek befejeződik. |

### <a name="examples"></a>Példák

Egy alkalmazás felskálázása 4 CPU-mag és 8 GB memória/példány esetén.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Az alkalmazás 5 példányra történő központi telepítésének felskálázása.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az Spring-Cloud app set-Deployment

Adja meg az alkalmazás éles környezetben történő üzembe helyezésének konfigurációs beállításait.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --Deployment-d | Az alkalmazás meglévő központi telepítésének neve. |
| --név-n | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --No-WAIT | Várjon, amíg a hosszan futó műveletek befejeződik. |

### <a name="examples"></a>Példák

Az alkalmazás átmeneti üzembe helyezésének felcserélése az éles környezetbe.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az Spring-Cloud app show

Egy alkalmazás részleteinek megjelenítése az Azure Spring-felhőben.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az Spring-Cloud app show-Deploy-log

A forráskód utolsó telepítésének felépítési naplójának megjelenítése.  Az alapértelmezett érték az éles környezet.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --Deployment-d | Az alkalmazás meglévő központi telepítésének neve.  Az alapértelmezett érték az éles környezet. |

## <a name="az-spring-cloud-app-start"></a>az Spring-Cloud app Start

Elindítja az alkalmazás példányait.  Az alapértelmezett érték az éles környezet.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --Deployment-d | Az alkalmazás meglévő központi telepítésének neve.  Az alapértelmezett érték az éles környezet. |
| --No-WAIT | Várjon, amíg a hosszan futó műveletek befejeződik. |

## <a name="az-spring-cloud-app-stop"></a>az Spring-Cloud app stop

Az alkalmazás példányainak leállítása.  Az alapértelmezett érték az éles környezet.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --Deployment-d | Az alkalmazás meglévő központi telepítésének neve.  Az alapértelmezett érték az éles környezet. |
| --No-WAIT | Várjon, amíg a hosszan futó műveletek befejeződik. |

## <a name="az-spring-cloud-app-update"></a>az Spring-Cloud app Update

Egy alkalmazás tárolt konfigurációjának frissítése.

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

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név-n | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --Deployment-d | Az alkalmazás meglévő központi telepítésének neve.  Az alapértelmezett érték az éles környezet. |
| --Enable-perzisztens-Storage | Logikai.  Ha az értéke igaz, csatlakoztassa az alapértelmezett elérési úttal rendelkező 50 GB-lemezt. |
| --env | Szóközzel tagolt környezeti változók a "Key [= Value]" formátumban. |
| – nyilvános | Logikai.  Ha az értéke igaz, rendeljen hozzá egy nyilvános tartományt az alkalmazáshoz. |
| --JVM – beállítások | JVM-beállításokat tartalmazó karakterlánc.  A rendszerhéj-elemzési hibák elkerülése érdekében használja a "=" helyett a következőt: "". Például: `--jvm-options='-Xms1024m -Xmx2048m` . |
| --No-WAIT | Várjon, amíg a hosszan futó műveletek befejeződik. |
| --futtatókörnyezet-verzió | Az alkalmazásban használt nyelv futtatókörnyezet-verziója.  Megengedett értékek: `Java_11` , `Java_8` . |

### <a name="example"></a>Példa

Adjon hozzá egy környezeti változót az alkalmazáshoz.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az Spring-Cloud app kötési listája

Egy alkalmazás összes szolgáltatási kötésének listázása.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-binding-remove"></a>az Spring-Cloud app bind Remove

Távolítson el egy szolgáltatási kötést az alkalmazásból.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --név | Az eltávolítandó szolgáltatási kötés neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-binding-show"></a>az Spring-Cloud app bind show

Egy szolgáltatás kötésének részleteit jeleníti meg.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatás kötésének neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az Spring-Cloud app bind Cosmos Add

Azure Cosmos DB kötése az alkalmazással.

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

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --API-Type | Adja meg az API-típust a következő értékek egyikének használatával: Cassandra, Gremlin, Mongo, SQL, table. |
| – alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatás kötésének neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

|Opcionális paraméterek | Leírás |
| --- | :--- |
| --gyűjtemény-név | A gyűjtemény neve.  A Gremlin használatakor szükséges. |
| --adatbázis-név | Az adatbázis neve.  A Mongo, az SQL és a Gremlin használatakor szükséges. |
| – kulcs – szóköz | Cassandra kulcs – szóköz.  A Cassandra használatakor szükséges. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az Spring-Cloud app bind Cosmos Update

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatás kötésének neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

|Opcionális paraméterek | Leírás |
| --- | :--- |
| --gyűjtemény-név | A gyűjtemény neve.  A Gremlin használatakor szükséges. |
| --adatbázis-név | Az adatbázis neve.  A Mongo, az SQL és a Gremlin használatakor szükséges. |
| – kulcs – szóköz | Cassandra kulcs – szóköz.  A Cassandra használatakor szükséges. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az Spring-Cloud app bind MySQL Add

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --adatbázis-név | Az adatbázis neve. |
| – kulcs | A szolgáltatás API-kulcsa. |
| --név | A szolgáltatás kötésének neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| – erőforrás-azonosító | A szolgáltatáshoz kötődő Azure-erőforrás azonosítója. |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |
| --username | Az adatbázis-hozzáféréshez használt Felhasználónév. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az Spring-Cloud app bind MySQL Update

Frissítse a szolgáltatás kötési kapcsolatát az alkalmazáshoz egy Azure Database for MySQL.

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatás kötésének neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --adatbázis-név | Az adatbázis neve. |
| – kulcs | A szolgáltatás API-kulcsa. |
| --username | Az adatbázis-hozzáféréshez használt Felhasználónév. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az Spring-Cloud app kötési Redis hozzáadása

Egy Azure-gyorsítótár kötése a Redis az alkalmazással.

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatás kötésének neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| – erőforrás-azonosító | A szolgáltatáshoz kötni kívánt szolgáltatás Azure-erőforrás-azonosítója. |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --letiltás-SSL | Tiltsa le a TLS-t. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az Spring-Cloud app kötési Redis frissítése

Az Azure cache szolgáltatáshoz tartozó Redis frissítése.

| Kötelező paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatás kötésének neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --letiltás-SSL | Tiltsa le a TLS-t. |

## <a name="az-spring-cloud-app-deployment-create"></a>az Spring-Cloud app Deployment Create

Hozzon létre egy átmeneti üzembe helyezést az alkalmazáshoz.

A kód üzembe helyezéséhez, illetve a beállítások egy meglévő központi telepítésre való frissítéséhez használja a `az spring-cloud app deploy --deployment <staging-deployment>` vagy az "az Spring-Cloud app Update – Deployment" beállítást <staging deployment> .

| Kötelező paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --név | A szolgáltatás kötésének neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --CPU | A virtuális CPU-magok száma példányban.  Alapértelmezett: 1 |
| --env | Szóközzel tagolt környezeti változók a "Key [= Value]" formátumban. |
| – példányok száma | Példányok száma. Alapértelmezett: 1. |
| --jar-elérési út | Ha meg van adni, telepítse a jar-t.  Egyéb esetben a jelenlegi mappát tar-ként telepítse. |
| --JVM – beállítások | JVM-beállításokat tartalmazó karakterlánc.  A rendszerhéj-elemzési hibák elkerülése érdekében használja a "=" helyett a következőt: "". Például: `--jvm-options='-Xms1024m -Xmx2048m` . |
| – memória | A memória GB memóriájának száma egy példányban. |
| --No-WAIT | Várjon, amíg a hosszú ideig futó műveletek befejeződik. |
| --futtatókörnyezet-verzió | Az alkalmazásban használt nyelv futtatókörnyezet-verziója.  Megengedett értékek: `Java_11` , `Java_8` . |
| --Skip-Clone-Settings | Hozzon létre egy átmeneti üzembe helyezést a jelenlegi éles üzembe helyezési beállítások klónozásával. |
| --cél-modul | A telepítendő alárendelt modul.  Kötelező, ha több jar-csomag van kiépítve a forráskódból. |
| --Version | Központi telepítés verziója.  Ha nincs beállítva, változatlan marad. |

### <a name="examples"></a>Példák

A forráskód üzembe helyezése az alkalmazás új központi telepítéséhez.  Ezzel becsomagolja az aktuális könyvtárat, létrehoz egy bináris fájlt a Pivotal Build rendszer használatával, majd üzembe helyezi azt.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Egy előre összeállított jar üzembe helyezése JVM-beállításokkal és környezeti változók használatával.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az Spring-Cloud app Deployment delete

Az alkalmazás központi telepítésének törlése.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --név | A központi telepítés neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-deployment-list"></a>az Spring-Cloud app Deployment List

Egy alkalmazás összes központi telepítésének listázása.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-app-deployment-show"></a>az Spring-Cloud app Deployment show

Egy központi telepítés részleteinek megjelenítése.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --név | A központi telepítés neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --Service-s | Az Azure Spring-felhő neve.  Az alapértelmezett szolgáltatást a használatával konfigurálhatja `az configure --defaults spring-cloud=<name>` . |

## <a name="az-spring-cloud-config-server-clear"></a>az Spring-Cloud config-Server Clear

Törölje az összes konfigurációs beállítást a konfigurációs kiszolgálón.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név | Az Azure Spring-felhő neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |

## <a name="az-spring-cloud-config-server-set"></a>az Spring-Cloud config-Server set

Konfigurációs beállítások beállítása a konfigurációs kiszolgálón YAML-fájl használatával.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --config-file | A konfigurációs kiszolgáló konfigurációjához tartozó YAML-jegyzékfájl elérési útja. |
| --név | Az Azure Spring-felhő neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --No-WAIT | A hosszú ideig futó műveletek befejezéséhez ne.

## <a name="az-spring-cloud-config-server-show"></a>az Spring-Cloud config-Server show

A konfigurációs kiszolgáló beállításainak megjelenítése.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név | Az Azure Spring-felhő neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |

## <a name="az-spring-cloud-config-server-git-set"></a>az Spring-Cloud config-Server git set

Állítsa be a konfigurációs kiszolgáló git-tulajdonságait.  Ezzel felülírja az összes meglévő git-tulajdonságot.

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

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név | Az Azure Spring-felhő neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --URI | A hozzáadott konfiguráció URI-ja. |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --Elhalasztva | Az objektum ideiglenes tárolása a helyi gyorsítótárban az Azure-ba való küldés helyett.  `az cache`A használatával megtekintheti vagy törölheti. |
| --host-key | A hozzáadott konfigurációhoz tartozó gazdagép kulcsa. |
| --host-key-algoritmus | A hozzáadott konfigurációhoz tartozó host key algoritmus. |
| --Label | A hozzáadott konfiguráció címkéje. |
| --password | A hozzáadott konfiguráció jelszava. |
| --titkos kulcs | A hozzáadott konfiguráció titkos kulcsa. |
| --Keresés – elérési utak | Keresési útvonalak a hozzáadott konfigurációhoz.  Vesszővel elválasztó karakterek használata több elérési útra. |
| --Strict-host-key-check | A hozzáadott konfiguráció szigorú gazdagép-ellenőrzésének engedélyezése. |
| --username | A hozzáadott konfiguráció felhasználóneve. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az Spring-Cloud config-Server git-tárház hozzáadása

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

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név | Az Azure Spring-felhő neve. |
| --repó-név | A tárház URI-ja. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| --URI | A hozzáadott konfiguráció URI-ja. |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --Elhalasztva | Az objektum ideiglenes tárolása a helyi gyorsítótárban az Azure-ba való küldés helyett.  `az cache`A használatával megtekintheti vagy törölheti. |
| --host-key | A hozzáadott konfigurációhoz tartozó gazdagép kulcsa. |
| --host-key-algoritmus | A hozzáadott konfigurációhoz tartozó host key algoritmus. |
| --Label | A hozzáadott konfiguráció címkéje. |
| --password | A hozzáadott konfiguráció jelszava. |
| – minta | A tárház mintája.  Vesszővel elválasztó karakterek használata több elérési útra.|
| --titkos kulcs | A hozzáadott konfiguráció titkos kulcsa. |
| --Keresés – elérési utak | Keresési útvonalak a hozzáadott konfigurációhoz.  Vesszővel elválasztó karakterek használata több elérési útra. |
| --Strict-host-key-check | A hozzáadott konfiguráció szigorú gazdagép-ellenőrzésének engedélyezése. |
| --username | A hozzáadott konfiguráció felhasználóneve. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az Spring-Cloud config-Server git-tárház listája

A konfigurációs kiszolgáló által definiált összes git-repó listázása

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név | Az Azure Spring-felhő neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --Elhalasztva | Az objektum ideiglenes tárolása a helyi gyorsítótárban az Azure-ba való küldés helyett.  `az cache`A használatával megtekintheti vagy törölheti. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az Spring-Cloud config-Server git-tárház eltávolítása

Távolítsa el a git-tárház meglévő konfigurációját a konfigurációs kiszolgálóról.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név | Az Azure Spring-felhő neve. |
| --repó-név | A tárház URI-ja. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| --Elhalasztva | Az objektum ideiglenes tárolása a helyi gyorsítótárban az Azure-ba való küldés helyett.  `az cache`A használatával megtekintheti vagy törölheti. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az Spring-Cloud test-Endpoint Disable

Az Azure Spring Cloud tesztelési végpontjának letiltása

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név | Az Azure Spring-felhő neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az Spring-Cloud test-Endpoint Enable

Az Azure Spring Cloud tesztelési végpontjának engedélyezése. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név | Az Azure Spring-felhő neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |

## <a name="az-spring-cloud-test-endpoint-list"></a>az Spring-Cloud test-Endpoint List 

Az Azure Spring Cloud számára elérhető teszt Endpoint kulcsok listázása.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név | Az Azure Spring-felhő neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |

| Opcionális paraméterek | Leírás |
| --- | :--- |
| – alkalmazás | Az alkalmazás neve. |
| --Deployment-d | Az alkalmazás meglévő központi telepítésének neve.  Ha nincs megadva, az alapértelmezett érték a termelés. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az Spring-Cloud test-Endpoint megújít-Key

Egy teszt-Endpoint kulcs újbóli létrehozása az Azure Spring Cloud-hoz.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Kötelező paraméterek | Leírás |
| --- | :--- |
| --név | Az Azure Spring-felhő neve. |
| --erőforrás-csoport-g | Az erőforráscsoport neve.  Az alapértelmezett csoportot a használatával állíthatja be `az configure --defaults group=<name>` . |
| – típus | A teszt végpont kulcsának típusa.  Engedélyezett értékek: elsődleges, másodlagos. |
