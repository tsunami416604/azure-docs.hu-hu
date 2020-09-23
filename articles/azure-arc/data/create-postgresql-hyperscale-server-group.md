---
title: Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása az Azure arc-ban
description: Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása az Azure arc-ban
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e845136c4fed5a3d2e6863fdab0aa9f70fb30b5d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939921"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása

Ez a dokumentum a PostgreSQL nagy kapacitású-kiszolgálócsoport Azure arc-beli létrehozásának lépéseit ismerteti.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Első lépések
Ha már ismeri az alábbi témaköröket, akkor kihagyhatja ezt a bekezdést.
Fontos témaköröket érdemes elolvasni a létrehozás folytatása előtt:
- [Az Azure arc-kompatibilis adatszolgáltatások áttekintése](overview.md)
- [Csatlakozási módok és követelmények](connectivity.md)
- [Tárolási konfiguráció és Kubernetes-tárolási fogalmak](storage-configuration.md)
- [Kubernetes erőforrás-modell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Ha szeretné kipróbálni a teljes környezet kiépítését anélkül, hogy az Azure [arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) az Azure Kubernetes szolgáltatás (ak), az AWS rugalmas Kubernetes szolgáltatás (EKS), a Google Cloud Kubernetes Engine (GKE) vagy egy Azure-beli virtuális gép üzembe helyezése nélkül próbálja ki.


## <a name="login-to-the-azure-arc-data-controller"></a>Bejelentkezés az Azure arc-adatkezelőbe

A példány létrehozása előtt először be kell jelentkeznie az Azure arc-adatvezérlőbe. Ha már bejelentkezett az adatkezelőbe, kihagyhatja ezt a lépést.

```console
azdata login
```

Ekkor a rendszer kérni fogja a felhasználónevet, a jelszót és a rendszernévtért.  

> Ha a parancsfájlt használta az adatvezérlő létrehozásához, akkor a névtérnek **ív** típusúnak kell lennie

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Előzetes és ideiglenes lépés csak a OpenShift-felhasználók számára

A következő lépésre való áttérés előtt implementálja ezt a lépést. Ha a PostgreSQL nagy kapacitású-kiszolgáló csoportját nem az alapértelmezett értékre szeretné telepíteni a Red Hat OpenShift, az alábbi parancsokat kell futtatnia a fürtön a biztonsági megkötések frissítéséhez. Ez a parancs megadja a szükséges jogosultságokat a PostgreSQL nagy kapacitású-kiszolgálói csoportot futtató szolgáltatásfiókok számára. Az Azure arc-adatkezelő üzembe helyezése során felvette a biztonsági környezeti megkötés (SCC) **_ív-adathalmazát_** .

```console
oc adm policy add-scc-to-group arc-data-scc -z <server-group-name> -n <namespace name>
```

_A **kiszolgáló-csoport neve** a következő lépésben létrehozandó kiszolgálócsoport neve._
   
A OpenShift SCCs kapcsolatos további információkért tekintse meg a [OpenShift dokumentációját](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Most már megvalósíthatja a következő lépést.

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása

Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozásához az Azure-ív esetében használja a következő parancsot:

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!NOTE]
> - **Más parancssori paraméterek is elérhetők.  A lehetőségek teljes listáját a futtatásával tekintheti meg `azdata arc postgres server create --help` .**
> - Az előzetes verzióban a biztonsági mentéshez és a visszaállításhoz meg kell adnia egy tárolási osztályt a biztonsági mentésekhez (_--Storage-Class-Backups-SCB_), amikor létrehoz egy kiszolgálói csoportot.
> - A--Volume-size-* paraméterek által elfogadott egység egy Kubernetes-erőforrás mennyisége (egy egész szám, amelyet a fenti SI-K egyike is elegendő (T, G, M, K, m), vagy a két megfelelője (ti, GI, mi, ki)).
> - A névnek 10 karakternél rövidebbnek kell lennie, és meg kell felelnie a DNS elnevezési konvencióinak.
> - A rendszer kérni fogja, hogy adja meg a _postgres_ standard rendszergazdai jogú felhasználó jelszavát.  Az interaktív kérdés kihagyható úgy, hogy a `AZDATA_PASSWORD` create parancs futtatása előtt beállítja a munkamenet környezeti változóját.
> - Ha a AZDATA_USERNAME és AZDATA_PASSWORD használatával telepítette az adatvezérlőt ugyanazon a terminál-munkamenetben, akkor a rendszer az AZDATA_USERNAME és AZDATA_PASSWORD értékeit is felhasználja a PostgreSQL nagy kapacitású-kiszolgálócsoport telepítéséhez. A PostgreSQL nagy kapacitású adatbázismotor alapértelmezett rendszergazda felhasználójának neve _PostgreSQL_ , és ezen a ponton nem módosítható.
> - A PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása nem fogja azonnal regisztrálni az erőforrásokat az Azure-ban. Az [erőforrás-leltár](upload-metrics-and-logs-to-azure-monitor.md)  vagy a [használati adatok](view-billing-data-in-azure.md) Azure-ba való feltöltése során az erőforrások az Azure-ban lesznek létrehozva, és az erőforrások megjelennek a Azure Portalban.
> - A--Port paraméter nem módosítható ezen a ponton.
> - Ha nem rendelkezik alapértelmezett tárolási osztállyal a Kubernetes-fürtben, a--metadataStorageClass paraméterrel kell megadnia egyet. Ha ezt nem teszi meg, a Create parancs meghibásodását fogja eredményezni. Annak ellenőrzéséhez, hogy rendelkezik-e a Kubernetes-fürtön deklarált alapértelmezett tárolási osztállyal, a következő parancsot kell megállapítania: 
>
>   ```console
>   kubectl get sc
>   ```
>
> - Ha a tárolási osztály alapértelmezett tárolási osztályként van konfigurálva, akkor az **(alapértelmezett)** érték jelenik meg a tárolási osztály nevéhez. Például:
>
>   ```output
>   NAME                       PROVISIONER                        AGE
>   local-storage (default)    kubernetes.io/no-provisioner       4d18h
>   ```


## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>Az ív-telepítőben létrehozott Azure Database for PostgreSQL-kiszolgálócsoportok listázása

Ha szeretné megtekinteni a PostgreSQL nagy kapacitású Server-csoportokat az Azure arc-ban, használja a következő parancsot:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>Végpontok beszerzése a Azure Database for PostgreSQL-kiszolgálócsoportok összekapcsolásához

A PostgreSQL-példány végpontjait a következő parancs futtatásával tekintheti meg:

```console
azdata arc postgres endpoint list -n <server group name>
```
Például:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

A PostgreSQL-példány végpontjának használatával kapcsolódhat a PostgreSQL nagy kapacitású-kiszolgáló csoportjához a kedvenc eszközéről:  [Azure Data Studio](https://aka.ms/getazuredatastudio), [Pgcli](https://www.pgcli.com/) psql, pgAdmin stb.

Ha Azure-beli virtuális gépet használ a teszteléshez, kövesse az alábbi utasításokat:

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Speciális Megjegyzés az Azure-beli virtuális gépek üzembe helyezéséről

Ha Azure-beli virtuális gépet használ, a végpont IP-címe nem fogja megjeleníteni a _nyilvános_ IP-címet. A nyilvános IP-cím megkereséséhez használja a következő parancsot:

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Ezután összekapcsolhatja a nyilvános IP-címet a porttal a csatlakozás elvégzéséhez.

Előfordulhat, hogy a hálózati biztonsági átjárón (NSG) keresztül fel kell tenni a PostgreSQL nagy kapacitású-kiszolgáló csoportjának portját is. A (NSG) szolgáltatáson keresztüli adatforgalom engedélyezéséhez hozzá kell adnia egy olyan szabályt, amely a következő paranccsal végezhető el:

Egy szabály beállításához ismernie kell a NSG nevét. A NSG az alábbi parancs használatával határozhatja meg:

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Ha megkapta a NSG nevét, a következő parancs használatával adhat hozzá tűzfalszabály-szabályt. A példában szereplő értékek egy NSG-szabályt hoznak létre a 30655-es porthoz, és lehetővé teszik a csatlakozást **bármely** forrás IP-címről.  Ez nem az ajánlott biztonsági eljárás!  Az ügyfél IP-címére vagy a csoport vagy a szervezet IP-címeire jellemző IP-címtartomány megadásával jobban lezárhatja a dolgokat.

Cserélje le a--Destination-port-Ranges paraméter értékét a lenti "azdata arc postgres Server List" parancsból kapott portszámra.

```console
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Csatlakozás az Azure Data Studióval

Nyissa meg Azure Data Studiot, és kapcsolódjon a példányhoz a fenti külső végpont IP-címével és portszámával, valamint a példány létrehozásakor megadott jelszóval.  Ha a PostgreSQL nem érhető el a *kapcsolattípus* legördülő listájában, akkor a PostgreSQL-bővítményt a Bővítmények lapon megkeresve keresse meg a PostgreSQL-t.

> [!NOTE]
> A port számának megadásához a kapcsolódási panelen kattintson a [speciális] gombra.

Ne feledje, hogy ha Azure-beli virtuális gépet használ, szüksége lesz a _nyilvános_ IP-címére, amely a következő parancs segítségével érhető el:

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Kapcsolat a psql

A PostgreSQL nagy kapacitású-kiszolgáló csoport eléréséhez adja át a fentiekben lekért PostgreSQL nagy kapacitású-kiszolgálócsoport külső végpontját:

Most már csatlakozhat bármelyik psql:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Következő lépések

- Olvassa el a Azure Database for PostgreSQL nagy kapacitású kapcsolatos fogalmakat és útmutatókat az adatok több PostgreSQL nagy kapacitású-csomóponton keresztüli terjesztéséhez, valamint a Azure Database for PostgreSQL nagy kapacitású teljesítményének kihasználása érdekében. :
    * [Csomópontok és táblák](../../postgresql/concepts-hyperscale-nodes.md)
    * [Alkalmazás típusának meghatározása](../../postgresql/concepts-hyperscale-app-type.md)
    * [Elosztási oszlop kiválasztása](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tábla közös elhelyezése](../../postgresql/concepts-hyperscale-colocation.md)
    * [Táblák elosztása és módosítása](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Több-bérlős adatbázis tervezése](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Valós idejű elemzési irányítópult kialakítása](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* A fenti dokumentumokban hagyja ki a **bejelentkezett szakaszt a Azure Portalba**, & **hozzon létre egy Azure Database for PostgreSQL-nagy kapacitású (Citus)**. Implementálja az Azure arc üzembe helyezésének hátralévő lépéseit. Ezek a részek Azure Database for PostgreSQL az Azure-felhőben nagy kapacitású (Citus) jellemzőek, de a dokumentumok egyéb részei közvetlenül alkalmazhatók az Azure arc-kompatibilis PostgreSQL-nagy kapacitású.

- [A Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport felskálázása](scale-out-postgresql-hyperscale-server-group.md)
- [Tárolási konfiguráció és Kubernetes-tárolási fogalmak](storage-configuration.md)
- [Állandó mennyiségi jogcímek kiterjesztése](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes erőforrás-modell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
