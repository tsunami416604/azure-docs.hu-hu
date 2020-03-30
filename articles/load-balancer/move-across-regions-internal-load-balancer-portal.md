---
title: Az Azure belső terheléselosztó áthelyezése egy másik Azure-régióba az Azure Portal használatával
description: Az Azure Resource Manager-sablon használatával áthelyezi az Azure belső terheléselosztóját az egyik Azure-régióból a másikba az Azure Portal használatával
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: f23923b9d847ef393ebd609eb5fbba530b1a07d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638807"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Az Azure belső terheléselosztó áthelyezése egy másik régióba az Azure Portal használatával

Vannak különböző forgatókönyvek, amelyben szeretné áthelyezni a meglévő belső terheléselosztó egyik régióból a másikba. Előfordulhat például, hogy egy belső terheléselosztót szeretne létrehozni ugyanazzal a konfigurációval a teszteléshez. Előfordulhat, hogy egy belső terheléselosztót is át szeretne helyezni egy másik régióba a vész-helyreállítási tervezés részeként.

Az Azure belső terheléselosztók nem helyezhetők át egyik régióból a másikba. Azonban egy Azure Resource Manager sablon használatával exportálhatja a belső terheléselosztó meglévő konfigurációját és virtuális hálózatát.  Ezután egy másik régióban elhelyezheti az erőforrást úgy, hogy a terheléselosztót és a virtuális hálózatot egy sablonba exportálja, módosítja a paramétereket a célrégiónak megfelelően, majd telepíti a sablonokat az új régióba.  Az Erőforrás-kezelőről és a sablonokról további információt a [Rövid útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése az Azure Portal használatával című témakörben talál.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure belső terheléselosztó az Azure-régióban, ahonnan át szeretne helyezni.

- Az Azure belső terheléselosztók nem helyezhetők át a régiók között.  Az új terheléselosztót a célrégió erőforrásaihoz kell társítania.

- Belső terheléselosztó konfiguráció exportálásához és egy sablon központi telepítéséhez egy belső terheléselosztó létrehozásához egy másik régióban, szüksége lesz a Hálózati közreműködő szerepkörvagy magasabb.

- Azonosítsa a forráshálózati elrendezést és az összes jelenleg használt erőforrást. Ez az elrendezés magában foglalja, de nem kizárólagosan a terheléselosztók, hálózati biztonsági csoportok, virtuális gépek és a virtuális hálózatok.

- Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e belső terheléselosztók létrehozását a használt célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik a folyamat terheléselosztók hozzáadásának támogatásához.  Tekintse meg [az Azure előfizetési és szolgáltatáskorlátait, kvótáit és korlátozásait](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Felkészülés és mozgás
A következő lépések bemutatják, hogyan készítse elő a belső terheléselosztót az áthelyezéshez egy Resource Manager-sablon használatával, és helyezze át a belső terheléselosztó konfigurációját a célrégióba az Azure Portal használatával.  Ennek a folyamatnak a részeként a belső terheléselosztó virtuális hálózati konfigurációját is bele kell foglalni, és először el kell végezni, mielőtt áthelyezi a belső terheléselosztót.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>A virtuális hálózati sablon exportálása és üzembe helyezése az Azure Portalról

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **erőforráscsoportokba.**
2. Keresse meg a forrás virtuális hálózatot tartalmazó erőforráscsoportot, és kattintson rá.
3. Válassza > **Beállítások** > **exportálása sablont.**
4. Válassza a **Telepítés lehetőséget** az **Exportálás sablon** panelen.
5. Kattintson **a TEMPLATE** > **Paraméterek szerkesztése parancsra** a **parameters.json** fájl online szerkesztőben való megnyitásához.
6. A virtuális hálózat nevének paraméterének szerkesztéséhez módosítsa az **értéktulajdonságot** a **paraméterek**alatt:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Módosítsa a forrás virtuális hálózat név értékét a szerkesztőben a cél virtuális hálózat által választott névre. Győződjön meg róla, hogy a nevet idézőjelek közé kell tetsznie.

8. Kattintson a **Mentés** a szerkesztőben gombra.

9. Kattintson **a TEMPLATE** > **Szerkesztés sablon** ra a **template.json** fájl online szerkesztőben való megnyitásához.

10. A virtuális hálózat áthelyezésének célrégiójának szerkesztéséhez módosítsa a **helytulajdonságot** az erőforrások alatt:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

11. A régióhely-kódok beszerzéséhez olvassa el az Azure Locations ( Azure Locations ) ( Azure Locations ) ( Azure Locations ) ( Azure Locations ) [(A régió hely](https://azure.microsoft.com/global-infrastructure/locations/)  A régió kódja a terület neve szóközök nélkül, **az USA** = középső**központja**.

12. A **template.json** fájlban más paramétereket is módosíthat, ha úgy dönt, és a követelményektől függően nem kötelező:

    * **Címtér** – A virtuális hálózat címtere a mentés előtt módosítható az **erőforrások** > **addressSpace** szakaszának módosításával és a **template.json** fájlban lévő **addressPrefixes** tulajdonság módosításával:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Alhálózat** – Az alhálózat neve és az alhálózati címtér a **template.json** fájl **alhálózati** szakaszának módosításával módosítható vagy hozzáadható. Az alhálózat neve a **névtulajdonság** módosításával módosítható. Az alhálózati címtér a **template.json** fájlban lévő **addressPrefix** tulajdonság módosításával módosítható:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         A **template.json** fájlban a címelőtag módosításához két helyen kell módosítani, a fent felsorolt szakaszt és az alább felsorolt **típusszakaszt.**  Módosítsa a **addressPrefix** tulajdonságot a fentinek megfelelően:

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

13. Kattintson a **Mentés** gombra az online szerkesztőben.

14. Kattintson **a BASICS-előfizetés** > **Subscription** elemre, és válassza ki azt az előfizetést, amelyen a cél virtuális hálózat telepítve lesz.

15. Kattintson **a BASICS** > **erőforráscsoport** válassza ki az erőforráscsoportot, ahol a cél virtuális hálózat lesz telepítve.  Az **Új létrehozása gombra** kattintva új erőforráscsoportot hozhat létre a célvirtuális hálózathoz.  Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő virtuális hálózat forráserőforrás-csoportja.

16. Ellenőrizze, hogy a **BASICS** > **hely** be van-e állítva arra a célhelyre, ahol a virtuális hálózat üzembe helyezését szeretné.

17. Ellenőrizze a **BEÁLLÍTÁSOK** csoportban, hogy a név megegyezik-e a fenti paraméterszerkesztőben megadott névvel.

18. Jelölje be a jelölőnégyzetet a **FELTÉTELEK ÉS FELTÉTELEK mezőben.**

19. A **célvirtuális** hálózat telepítéséhez kattintson a Vásárlás gombra.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>A belső terheléselosztó sablon exportálása és üzembe helyezése az Azure PowerShellből

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **erőforráscsoportokba.**
2. Keresse meg a forrás belső terheléselosztót tartalmazó erőforráscsoportot, és kattintson rá.
3. Válassza > **Beállítások** > **exportálása sablont.**
4. Válassza a **Telepítés lehetőséget** az **Exportálás sablon** panelen.
5. Kattintson **a TEMPLATE** > **Paraméterek szerkesztése parancsra** a **parameters.json** fájl online szerkesztőben való megnyitásához.

6. A belső terheléselosztó nevének módosításához módosítsa a forrás belső terheléselosztó nevének **defaultValue** tulajdonságát a cél belső terheléselosztó nevére, győződjön meg arról, hogy a név idézőjelek között van:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. A fent áthelyezett célvirtuális hálózat értékének szerkesztéséhez először be kell szereznie az erőforrás-azonosítót, majd át kell másolnia és be kell illesztenie a **parameters.json** fájlba. Az azonosító beszerzéséhez:

    1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **erőforráscsoportok** egy másik böngészőlapon vagy ablakban.
    2. Keresse meg a fenti lépésekből az áthelyezett virtuális hálózatot tartalmazó célerőforrás-csoportot, és kattintson rá.
    3. Válassza > **Beállítások** > **tulajdonságai lehetőséget**.
    4. A jobb oldali panelen jelölje ki az **erőforrás-azonosítót,** és másolja a vágólapra.  Másik lehetőségként az **Erőforrás-azonosító** elérési úttól jobbra található **Vágólap** ra kattintva is választhat.
    5. Illessze be az erőforrás-azonosítót a **defaultValue** tulajdonságba a másik böngészőablakban vagy lapon megnyitott **Paraméterek szerkesztése** szerkesztőbe:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Kattintson a **Mentés** gombra az online szerkesztőben.

7. Kattintson **a TEMPLATE** > **Szerkesztés sablon** ra a **template.json** fájl online szerkesztőben való megnyitásához.
8. A belső terheléselosztó konfigurációjának áthelyezéséhez szükséges célterület szerkesztéséhez módosítsa a **location** tulajdonságot a **template.json** fájl **erőforrásai** alatt:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  A régióhely-kódok beszerzéséhez olvassa el az Azure Locations ( Azure Locations ) ( Azure Locations ) ( Azure Locations ) ( Azure Locations ) [(A régió hely](https://azure.microsoft.com/global-infrastructure/locations/)  A régió kódja a terület neve szóközök nélkül, **az USA** = középső**központja**.

10. Ha úgy dönt, módosíthatja a sablon egyéb paramétereit is, és a követelményektől függően nem kötelező:

    * **Sku** - A belső terheléselosztó skuját a konfigurációban szabványosról alapszintre vagy alapszintűre módosíthatja a **template.json** fájlban lévő **sku** > **névtulajdonság** módosításával:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Az alapszintű és a szabványos termékváltozat terheléselosztók közötti különbségekről az [Azure Standard Load Balancer áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Terheléselosztási szabályok** – A **template.json** fájl **loadBalancingRules** szakaszának bejegyzéseinek hozzáadásával vagy eltávolításával hozzáadhat vagy eltávolíthat terheléselosztási szabályokat a konfigurációban:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       A terheléselosztási szabályokról a [Mi az Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Mintavételek** – A **template.json** fájl **mintavételezési** szakaszában a mintavételek szakaszának hozzáadásával vagy eltávolításával hozzáadhat vagy eltávolíthat egy mintavételezést a konfigurációban:

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Az Azure Load Balancer állapotmintáiról további információt a [Terheléselosztó állapotmintái című témakörben talál.](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Bejövő NAT-szabályok** – A load balancer bejövő NAT-szabályait úgy veheti fel vagy távolíthatja el, hogy bejegyzéseket ad hozzá vagy távolít el a **template.json** fájl **inboundNatRules** szakaszához:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        A bejövő NAT-szabály hozzáadásának vagy eltávolításának befejezéséhez a szabálynak a **template.json** fájl végén jelen lévőnek vagy **típustulajdonságként** kell lennie:

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        A bejövő NAT-szabályokról a [Mi az Azure Load Balancer című](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) témakörben talál további információt?

12. Kattintson a **Mentés** gombra az online szerkesztőben.

13. Kattintson **a BASICS-előfizetés** > **Subscription** elemre annak az előfizetésnek a kiválasztásához, amelyben a cél belső terheléselosztó telepítve lesz.

15. Kattintson **a BASICS** > **erőforráscsoport** ra annak az erőforráscsoportnak a kiválasztásához, amelyben a célterhelés-elosztó telepítve lesz.  Az **Új létrehozása gombra** kattintva új erőforráscsoportot hozhat létre a cél belső terheléselosztóhoz, vagy kiválaszthatja a virtuális hálózathoz fent létrehozott meglévő erőforráscsoportot.  Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő belső forrás terheléselosztó forráserőforrás-csoportja.

16. Ellenőrizze, hogy a **BASICS** > **helye** be van-e állítva arra a célhelyre, ahol a belső terheléselosztót telepíteni szeretné.

17. Ellenőrizze a **BEÁLLÍTÁSOK** csoportban, hogy a név megegyezik-e a fenti paraméterszerkesztőben megadott névvel.  Ellenőrizze, hogy az erőforrás-azonosítók a konfigurációban lévő virtuális hálózatokra vannak-e feltöltve.

18. Jelölje be a jelölőnégyzetet a **FELTÉTELEK ÉS FELTÉTELEK mezőben.**

19. A **célvirtuális** hálózat telepítéséhez kattintson a Vásárlás gombra.

## <a name="discard"></a>Elvetés

Ha el szeretné vetni a cél virtuális hálózatot és a belső terheléselosztót, törölje a cél virtuális hálózatot és a belső terheléselosztót tartalmazó erőforráscsoportot.  Ehhez jelölje ki az erőforráscsoportot az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az áttekintő lap tetején.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások véglegesítéséhez és a virtuális hálózat és a belső terheléselosztó áthelyezésének befejezéséhez törölje a forrásvirtuális hálózatot és a belső terheléselosztót vagy erőforráscsoportot. Ehhez válassza ki a virtuális hálózat és a belső terheléselosztó vagy erőforráscsoport az irányítópulton a portálon, és válassza a **Törlés** az egyes lapok tetején.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áthelyezett egy Azure belső terheléselosztó egyik régióból a másikba, és megtisztította a forráserőforrásokat.  Ha többet szeretne tudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vészhelyreállításról:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
