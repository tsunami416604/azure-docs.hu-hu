---
title: Hozzon létre egy privát fürtaz Azure Red Hat OpenShift 3.11 | Microsoft dokumentumok
description: Privát fürt létrehozása az Azure Red Hat OpenShift 3.11-es sel
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, privát klaszter, piros kalap
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399418"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Privát fürt létrehozása az Azure Red Hat OpenShift 3.11-es sel

> [!IMPORTANT]
> Az Azure Red Hat OpenShift (ARO) privát fürtök jelenleg csak privát előzetes verzióban érhetők el az USA keleti részén 2. A privát előnézet elfogadása csak meghívással történik. A funkció engedélyezése előtt regisztrálja az előfizetést.

A privát fürtök a következő előnyöket biztosítják:

* A privát fürtök nem teszik elérhetővé a fürtvezérlő sík összetevőit (például az API-kiszolgálókat) egy nyilvános IP-címen.
* A privát fürt virtuális hálózatát az ügyfelek konfigurálhatják, így a hálózatkezelés lehetővé teszi a más virtuális hálózatokkal való társviszony-létesítést, beleértve az ExpressRoute-környezeteket is. A virtuális hálózaton beállíthatja az egyéni DNS-t is, hogy integrálódjon a belső szolgáltatásokkal.

## <a name="before-you-begin"></a>Előkészületek

> [!NOTE]
> Ehhez a funkcióhoz az ARO HTTP API 2019-10-27-preview verziója szükséges. Az Azure CLI még nem támogatott.

A következő konfigurációs kódrészlet mezői újak, és szerepelniük kell a fürtkonfigurációban. `managementSubnetCidr`a fürt virtuális hálózatán belül kell lennie, és az Azure a fürt kezeléséhez használja.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

A magánfürt az alábbi mintaparancsfájlok használatával telepíthető. A fürt telepítése után hajtsa végre a `cluster get` parancsot, és tekintse meg a `properties.FQDN` tulajdonságot az OpenShift API-kiszolgáló privát IP-címének meghatározásához.

A fürt virtuális hálózata engedélyekkel lett létrehozva, így módosítható. Ezután beállíthatja a hálózati hozzáférést a virtuális hálózat (ExpressRoute, VPN, virtuális hálózati társviszony-létesítés) az igényeinek megfelelően.

Ha módosítja a DNS-névkiszolgálók a fürt virtuális hálózaton, akkor meg `properties.RefreshCluster` kell kiadnia egy frissítést a fürta tulajdonság `true` beállítása, hogy a virtuális gépek lehet újralemásolni. Ez a frissítés lehetővé teszi számukra, hogy felvegyék az új névkiszolgálókat.

## <a name="sample-configuration-scripts"></a>Minta konfigurációs parancsfájlok

Az ebben a szakaszban található mintaparancsfájlok segítségével állítsa be és telepítse a saját fürtöt.

### <a name="environment"></a>Környezet

Töltse ki az alábbi környezeti változókat a saját értékei használatával.

> [!NOTE]
> A helyet be `eastus2` kell állítani, mivel jelenleg ez a magánfürtök egyetlen támogatott helye.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>privát-cluster.json

A fent meghatározott környezeti változók használatával az alábbiakban egy mintafürt-konfiguráció látható, amelyen engedélyezve van a saját fürt.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>Privát fürt telepítése

Miután konfigurálta a privát fürtöt a fenti mintaparancsfájlokkal, futtassa a következő parancsot a saját fürt központi telepítéséhez.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>További lépések

Az OpenShift konzol eléréséről a [Webkonzol forgatókönyve című témakörben olvashat.](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html)
