---
title: Az Azure külső Load Balancer áthelyezése másik Azure-régióba az Azure Portal használatával
description: Azure Resource Manager sablon használatával áthelyezheti az Azure külső Load Balancer az egyik Azure-régióból a másikba a Azure Portal használatával.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: eda0d6e8fe56b985c3b29fa80cee880444d63741
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105288"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-the-azure-portal"></a>Az Azure külső Load Balancer áthelyezése másik régióba a Azure Portal használatával

Különböző helyzetekben érdemes áthelyezni a meglévő külső terheléselosztó egyik régióból a másikba. Előfordulhat például, hogy létre szeretne hozni egy külső terheléselosztó-t ugyanazzal a konfigurációval a teszteléshez. Előfordulhat, hogy a vész-helyreállítási tervezés részeként másik régióba is át szeretné helyezni a külső terheléselosztó-t.

Az Azure külső Load Balancer nem helyezhető át egyik régióból a másikba. A külső terheléselosztó meglévő konfigurációjának és nyilvános IP-címének exportálásához azonban Azure Resource Manager sablont is használhat.  Az erőforrást egy másik régióban is elvégezheti, ha a terheléselosztó és a nyilvános IP-címet egy sablonba exportálja, módosítja a paramétereket, hogy azok megfeleljenek a célként megadott régiónak, majd telepítse a sablonokat az új régióba.  A Resource Managerrel és a sablonokkal kapcsolatos további információkért lásd: [erőforráscsoportok exportálása sablonokba](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure külső terheléselosztó abban az Azure-régióban található, amelyről át kívánja helyezni.

- Az Azure külső terheléselosztó nem helyezhető át a régiók között.  Az új Load balancert a célként megadott régióban lévő erőforrásokhoz kell rendelnie.

- Külső terheléselosztó konfigurációjának exportálásához és sablon üzembe helyezéséhez külső terheléselosztó egy másik régióban való létrehozásához szüksége lesz a hálózati közreműködő szerepkörre vagy magasabbra.
   
- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés tartalmaz, de nem korlátozódik a terheléselosztó, a hálózati biztonsági csoportok, a nyilvános IP-címek és a virtuális hálózatok számára.

- Ellenőrizze, hogy az Azure-előfizetése lehetővé teszi-e külső terheléselosztó létrehozását a használt célcsoportban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Ellenőrizze, hogy az előfizetése rendelkezik-e elegendő erőforrással a folyamathoz tartozó terheléselosztó hozzáadásának támogatásához.  Tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Előkészítés és áthelyezés
A következő lépések bemutatják, hogyan készítse elő a külső terheléselosztó az áthelyezéshez Resource Manager-sablonnal, és a külső terheléselosztó konfigurációját helyezze át a cél régióba a Azure Portal használatával.  Ennek a folyamatnak a részeként a külső terheléselosztó nyilvános IP-konfigurációját is tartalmaznia kell, és először a külső Load Balancer áthelyezése előtt kell elvégezni.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-the-portal"></a>A nyilvános IP-sablon exportálása és üzembe helyezése a portálról

1. Jelentkezzen be az [Azure Portal](http://portal.azure.com) > **erőforráscsoporthoz**.
2. Keresse meg a forrás nyilvános IP-címet tartalmazó erőforráscsoportot, és kattintson rá.
3. Válassza > **Beállítások** > **Exportálás sablon**lehetőséget.
4. A **sablon exportálása** panelen válassza a **telepítés** lehetőséget.
5. Kattintson a **sablon** > **szerkesztése paraméterek** lehetőségre a **Parameters. JSON** fájl megnyitásához az online szerkesztőben.
8. A nyilvános IP-cím paraméterének szerkesztéséhez módosítsa a forrás nyilvános IP-címe **paraméter** > **értéke** alatt található tulajdonságot a cél nyilvános IP-címére, és győződjön meg arról, hogy a név idézőjelek közé esik:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Kattintson a **Mentés** gombra a szerkesztőben.

9.  Kattintson a sablon**szerkesztése** elemre, hogy megnyissa a **template. JSON** fájlt az online szerkesztőben. >  

10. A nyilvános IP-címet áthelyező cél régió szerkesztéséhez módosítsa a **Location (hely** ) tulajdonságot az **erőforrások**területen.

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
11. A régióbeli hely kódjának beszerzéséhez tekintse meg az [Azure-helyeket](https://azure.microsoft.com/global-infrastructure/locations/).  A régió kódja a régió neve szóközök nélkül, **Közép-USA** = **CentralUS**.
    
12. A sablon egyéb paramétereit is módosíthatja, és a követelményektől függően választható:

    * **SKU** – a konfigurációban a nyilvános IP-cím SKU-jának a standard és az alap közötti értékről a standard típusra módosítható a **sablon. JSON** fájljának **SKU** > **Name** tulajdonságának megváltoztatásával:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Az alapszintű és a standard SKU nyilvános IP-címei közötti különbségekkel kapcsolatos további információkért lásd: [nyilvános IP-cím létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address):

    * **Nyilvános IP-kiosztási módszer** és **Üresjárati időkorlát** – a sablon mindkét beállítását módosíthatja úgy, hogy a **PublicIPAllocationMethod** tulajdonságot **dinamikusról** **statikusra** vagy statikusra változtatja **a** dinamikus értékre. Az Üresjárat időkorlátja módosítható úgy, hogy módosítja a **idleTimeoutInMinutes** tulajdonságot a kívánt értékre.  Az alapértelmezett érték **4**:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
        
        ```

        A kiosztási módszerekkel és az üresjárati időtúllépési értékekkel kapcsolatos további információkért lásd: [nyilvános IP-cím létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

 
13. Kattintson a **Save (Mentés** ) gombra az online szerkesztőben.

14. Az alapértékek**előfizetése** lehetőségre kattintva válassza ki azt az előfizetést, ahol a cél nyilvános IP-címet telepíteni fogja. > 

15. Az alapszintű**erőforráscsoport** elemre kattintva kiválaszthatja azt az erőforráscsoportot, amelyben a célként megadott nyilvános IP-címet telepíteni fogja. >   Az **új létrehozása** lehetőségre kattintva létrehozhat egy új erőforráscsoportot a célként megadott nyilvános IP-címhez.  Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő forrás nyilvános IP-cím forrás-erőforráscsoport. 

16. Győződjön meg arról, hogy az **alapvető beállítások** > **helye** arra a célhelyre van beállítva, ahol a nyilvános IP-címet telepíteni kívánja.

17. Ellenőrizze a **Beállítások** területen, hogy a név megegyezik-e a fenti Parameters Editorban megadott névvel.

18. Jelölje be a jelölőnégyzetet a **feltételek és KIkötések**területen.

19. A cél nyilvános IP-cím üzembe helyezéséhez kattintson a **vásárlás** gombra.
20. Ha van egy másik nyilvános IP-címe, amely a kimenő NAT esetében használatos a terheléselosztó áthelyezéséhez, ismételje meg a fenti lépéseket a második kimenő nyilvános IP-cím exportálásához és üzembe helyezéséhez a célként megadott régióban.

### <a name="export-the-external-load-balancer-template-and-deploy-from-the-azure-portal"></a>A külső terheléselosztó sablonjának exportálása és üzembe helyezése a Azure Portal

1. Jelentkezzen be az [Azure Portal](http://portal.azure.com) > **erőforráscsoporthoz**.
2. Keresse meg a forrás külső Load balancert tartalmazó erőforráscsoportot, és kattintson rá.
3. Válassza > **Beállítások** > **Exportálás sablon**lehetőséget.
4. A **sablon exportálása** panelen válassza a **telepítés** lehetőséget.
5. Kattintson a **sablon** > **szerkesztése paraméterek** lehetőségre a **Parameters. JSON** fájl megnyitásához az online szerkesztőben.

5. A külső terheléselosztó nevének a paraméterének szerkesztéséhez módosítsa a forrás külső terheléselosztó neve tulajdonság **értékét** a cél külső terheléselosztó nevére, győződjön meg arról, hogy a név idézőjelek közé esik:

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  A fentiekben áthelyezett cél nyilvános IP-cím értékének szerkesztéséhez először be kell szereznie az erőforrás-azonosítót, majd be kell másolnia és beillesztenie a **Parameters. JSON** fájlba. Az azonosító beszerzése:
    
    1. Jelentkezzen be a [Azure Portal](http://portal.azure.com) > **erőforráscsoporthoz** egy másik böngésző lapon vagy ablakban.
    2. Keresse meg azt a célként megadott erőforráscsoportot, amely az áthelyezett nyilvános IP-címet tartalmazza a fenti lépésekből, majd kattintson rá.
    3. Válassza a > **Beállítások** > **Tulajdonságok**lehetőséget.
    4. A jobb oldali panelen jelölje ki az erőforrás- **azonosítót** , és másolja a vágólapra.  Azt is megteheti, hogy a **Másolás a vágólapra** gombra kattint az **erőforrás-azonosító** elérési útjának jobb oldalán.
    5. Illessze be az erőforrás-azonosítót a **Value (érték** ) tulajdonságba a **Paraméterek szerkesztése** szerkesztőben a másik böngészőablakban vagy lapon:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Kattintson a **Save (Mentés** ) gombra az online szerkesztőben.
   

7.  Ha a terheléselosztó kimenő NAT-és kimenő szabályait konfigurálta, akkor a rendszer egy harmadik bejegyzést fog tartalmazni ebben a fájlban a kimenő nyilvános IP-címhez tartozó külső AZONOSÍTÓhoz.  Ismételje meg a fenti lépéseket a **cél régióban** a kimenő nyilvános IP-cím azonosítójának beszerzéséhez, és illessze be a bejegyzést a **Parameters. JSON** fájlba:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",
                
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",
                
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                
            }
        },
    ```

8.  Kattintson a sablon**szerkesztése** elemre, hogy megnyissa a **template. JSON** fájlt az online szerkesztőben. > 
9.  A külső terheléselosztó konfigurációját áthelyező cél régió szerkesztéséhez módosítsa a **Location (hely** ) tulajdonságot a **template. JSON** fájlban található **erőforrások** területen:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. A régióbeli hely kódjának beszerzéséhez tekintse meg az [Azure-helyeket](https://azure.microsoft.com/global-infrastructure/locations/).  A régió kódja a régió neve szóközök nélkül, **Közép-USA** = **CentralUS**.

11. A sablon egyéb paramétereit is módosíthatja, és a követelményektől függően választható:
    
    * **SKU** – módosíthatja a külső terheléselosztó SKU-jának konfigurációját a standard értékről az alapszintű vagy az alapszintű értékre úgy, hogy megváltoztatja az **SKU** > **Name** tulajdonságát a **template. JSON** fájlban:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Az alapszintű és standard SKU-terheléselosztó közötti különbségekről az [Azure standard Load Balancer áttekintése](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) című témakörben olvashat bővebben.

    * Terheléselosztási **szabályok** – a konfigurációban hozzáadhat vagy eltávolíthat terheléselosztási szabályokat a **template. JSON** fájl **loadBalancingRules** szakaszának bejegyzéseinek hozzáadásával vagy eltávolításával:

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
       További információ a terheléselosztási szabályokról: [Mi az Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Mintavételek – hozzáadhat** vagy eltávolíthat egy mintavételt a terheléselosztó számára a konfigurációban a **sablon. JSON** fájl mintavételek **szakaszának** bejegyzések hozzáadásával vagy eltávolításával:

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
       Azure Load Balancer állapot-mintavételsel kapcsolatos további információkért lásd: [Load Balancer Health Szondák](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Bejövő NAT-szabályok** – a Load BALANCER bejövő NAT-szabályait hozzáadhatja vagy eltávolíthatja a **sablon. JSON** fájljának **inboundNatRules** szakaszának bejegyzések hozzáadásával vagy eltávolításával:

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
        Bejövő NAT-szabály hozzáadásának vagy eltávolításának befejezéséhez a szabálynak jelen kell lennie, vagy el kell távolítania a **template. JSON** fájl végén található **Type (típus** ) tulajdonsággal:

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
        További információ a bejövő NAT-szabályokról: [Mi az Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Kimenő szabályok** – a konfigurációban a **outboundRules** tulajdonság szerkesztésével adhat hozzá vagy távolíthat el kimenő szabályokat a **sablon. JSON** fájljában:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         További információ a kimenő szabályokról: [Load Balancer kimenő szabályok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

12. Kattintson a **Save (Mentés** ) gombra az online szerkesztőben.
    
13. Az alapértékek**előfizetése** lehetőségre kattintva válassza ki azt az előfizetést, ahol a cél külső terheléselosztó üzembe lesz helyezve. > 

15. Az alapszintű**erőforráscsoport** elemre kattintva kiválaszthatja azt az erőforráscsoportot, amelyben a cél terheléselosztó telepítve lesz. >   Az **új létrehozása** lehetőségre kattintva létrehozhat egy új erőforráscsoportot a cél külső terheléselosztó számára, vagy kiválaszthatja a fent létrehozott meglévő erőforráscsoportot a nyilvános IP-címhez.  Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő forrás külső terheléselosztó forrás-erőforráscsoport. 

16. Győződjön meg arról, hogy az **alapvető beállítások** > **helye** arra a célhelyre van beállítva, ahol a külső terheléselosztó üzembe helyezését szeretné.

17. Ellenőrizze a **Beállítások** területen, hogy a név megegyezik-e a fenti Parameters Editorban megadott névvel.  Ellenőrizze, hogy az erőforrás-azonosítók fel vannak-e töltve a konfigurációban lévő nyilvános IP-címekre.

18. Jelölje be a jelölőnégyzetet a **feltételek és KIkötések**területen.

19. A cél nyilvános IP-cím üzembe helyezéséhez kattintson a **vásárlás** gombra.

## <a name="discard"></a>Elvetés 

Ha el szeretné vetni a cél nyilvános IP-címet és a külső terheléselosztó-t, törölje a célként megadott nyilvános IP-címet és külső terheléselosztót tartalmazó erőforráscsoportot.  Ehhez válassza ki az erőforráscsoportot az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az Áttekintés oldal tetején.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások elvégzéséhez és a nyilvános IP-cím és a külső terheléselosztó áthelyezésének befejezéséhez törölje a forrásként szolgáló nyilvános IP-címet és a külső terheléselosztó vagy erőforráscsoportot. Ehhez válassza ki a nyilvános IP-címet és a külső terheléselosztó vagy az erőforráscsoport elemet az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az egyes oldalak tetején.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy külső Azure Load balancert helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
