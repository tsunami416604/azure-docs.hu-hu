---
title: Privát fürt létrehozása az Azure Red Hat OpenShift 3,11-mel | Microsoft Docs
description: Privát fürt létrehozása az Azure Red Hat OpenShift 3,11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: ARO, openshift, privát fürt, Red Hat
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399418"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Privát fürt létrehozása az Azure Red Hat OpenShift 3,11

> [!IMPORTANT]
> Az Azure Red Hat OpenShift (ARO) privát fürtök jelenleg csak privát előzetes verzióban érhetők el az USA 2. keleti régiójában. A privát előzetes verzió elfogadása csak meghívóval történik. A funkció engedélyezése előtt ne felejtse el regisztrálni az előfizetését.

A privát fürtök a következő előnyöket nyújtják:

* A privát fürtök nem teszik elérhetővé a fürtök vezérlési síkjainak összetevőit (például az API-kiszolgálókat) egy nyilvános IP-címen.
* A privát fürtök virtuális hálózata az ügyfelek által konfigurálható, így a hálózatkezelést úgy állíthatja be, hogy engedélyezze a többi virtuális hálózattal való társítást, beleértve a ExpressRoute-környezeteket is. A belső szolgáltatásokkal való integrációhoz egyéni DNS-t is beállíthat a virtuális hálózaton.

## <a name="before-you-begin"></a>Előkészületek

> [!NOTE]
> Ehhez a szolgáltatáshoz a 2019-10-27-es verzió szükséges – az ARO HTTP API előzetes verziója. Az Azure CLI-ben még nem támogatott.

A következő konfigurációs kódrészlet mezői újEK, és szerepelniük kell a fürt konfigurációjában. `managementSubnetCidr` a fürt virtuális hálózatán belül kell lennie, és az Azure a fürt felügyeletére használja.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Egy privát fürt üzembe helyezhető az alábbi minta-szkriptek használatával. Miután telepítette a fürtöt, hajtsa végre a `cluster get` parancsot, és tekintse meg a `properties.FQDN` tulajdonságot a OpenShift API-kiszolgáló magánhálózati IP-címének meghatározásához.

A fürt virtuális hálózata engedélyekkel lett létrehozva, hogy módosítható legyen. Ezután beállíthatja a hálózatkezelést a virtuális hálózat (ExpressRoute, VPN, virtuális hálózati társítás) eléréséhez az igényeinek megfelelően.

Ha megváltoztatja a DNS-kiszolgálókat a fürt virtuális hálózatán, akkor a fürtön egy frissítést kell kiállítania, amelynek `properties.RefreshCluster` tulajdonsága `true`, hogy a virtuális gépek alaphelyzetbe állíthatók. Ez a frissítés lehetővé teszi az új névszerverek felvételét.

## <a name="sample-configuration-scripts"></a>Példa konfigurációs parancsfájlok

Az ebben a szakaszban található minta szkriptekkel beállíthatja és telepítheti a privát fürtöt.

### <a name="environment"></a>Környezet

Adja meg az alábbi környezeti változókat a saját értékek alapján.

> [!NOTE]
> A helyet úgy kell beállítani, hogy a `eastus2`, mivel jelenleg ez az egyetlen támogatott hely a privát fürtök számára.

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

### <a name="private-clusterjson"></a>Private-cluster. JSON

A fent definiált környezeti változók használatával az alábbi példa egy fürtre épülő fürtözött konfigurációt engedélyez a privát fürtön.

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

## <a name="deploy-a-private-cluster"></a>Privát fürt üzembe helyezése

Miután konfigurálta a privát fürtöt a fenti minta-parancsfájlokkal, futtassa a következő parancsot a privát fürt üzembe helyezéséhez.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Következő lépések

További információ a OpenShift-konzol eléréséről: [webkonzol – útmutató](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
