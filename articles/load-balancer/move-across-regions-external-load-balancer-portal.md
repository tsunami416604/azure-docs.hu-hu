---
title: Azure-beli külső terheléselosztó áthelyezése egy másik Azure-régióba az Azure Portal használatával
description: Azure Resource Manager-sablon használatával helyezzen át egy külső terheléselosztót az egyik Azure-régióból a másikba az Azure Portal használatával.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 5cd5ce2635ce05c4d5962f12ddc3945342897ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638503"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Külső terheléselosztó áthelyezése másik régióba az Azure Portal használatával

Vannak különböző forgatókönyvek, amelyekben szeretné áthelyezni egy külső terheléselosztó egyik régióból a másikba. Például érdemes lehet létrehozni egy másik külső terheléselosztót ugyanazzal a konfigurációval a teszteléshez. Azt is érdemes áthelyezni egy külső terheléselosztó egy másik régióba a vész-helyreállítási tervezés részeként.

A szó szoros értelmében nem helyezhetát át az Azure külső terheléselosztó egyik régióból a másikba. De egy Azure Resource Manager sablon használatával exportálhatja a meglévő konfigurációt és nyilvános IP-címet egy külső terheléselosztó. Ezután egy másik régióban is elhelyezheti az erőforrást a terheléselosztó és a nyilvános IP-cím sablonba exportálásával, a paraméterek módosításával a célrégiónak megfelelően, majd üzembe helyezheti a sablont az új régióba. Az Erőforrás-kezelőről és a sablonokról az [Erőforráscsoportok exportálása sablonokba című](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)témakörben olvashat bővebben.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure külső terheléselosztó az Azure-régióban, ahonnan át szeretne helyezni.

- Az Azure külső terheléselosztók nem helyezhetők át a régiók között. Az új terheléselosztót a célrégió erőforrásaihoz kell társítania.

- Külső terheléselosztó konfigurációjának exportálásához és egy sablon központi telepítéséhez egy külső terheléselosztó létrehozásához egy másik régióban, hozzá kell rendelnie a Hálózati közreműködő szerepkört vagy magasabb szintű szerepkört.

- Azonosítsa a forráshálózati elrendezést és az összes jelenleg használt erőforrást. Ez az elrendezés tartalmazza, de nem korlátozódik a terheléselosztók, a hálózati biztonsági csoportok, a nyilvános IP-k és a virtuális hálózatok.

- Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e, hogy külső terheléselosztókat hozzon létre a célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik a terheléselosztók hozzáadásának támogatásához. Lásd: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).

## <a name="prepare-and-move"></a>Felkészülés és mozgás
A következő eljárások bemutatják, hogyan készítse elő a külső terheléselosztó az áthelyezés egy Erőforrás-kezelő sablon használatával, és helyezze át a külső terheléselosztó konfigurációa a célrégióba az Azure Portal használatával. Először exportálnia kell a külső terheléselosztó nyilvános IP-konfigurációját.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>A nyilvános IP-sablon exportálása és a nyilvános IP-cím telepítése a portálról

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.
2. Keresse meg a forrásnyilvános IP-címet tartalmazó erőforráscsoportot, és jelölje ki.
3. Válassza a **Beállítások** > **exportálása sablont**.
4. Válassza **a Telepítés lehetőséget** az Exportálás sablon **csoportban.**
5. Válassza **a TEMPLATE** > **Edit paraméterek lehetőséget** a parameters.json fájl online szerkesztőben való megnyitásához.
8. A nyilvános IP-név paraméterének szerkesztéséhez módosítsa az **értéktulajdonságot** a **paraméterek** alatt a forrás nyilvános IP-névről a nyilvános célIP nevére. A nevet tegye idézőjelek közé.

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

    Válassza a **Mentés** a szerkesztőben lehetőséget.

9.  Válassza **a TEMPLATE** > **Edit sablon lehetőséget** a template.json fájl online szerkesztőben való megnyitásához.

10. A nyilvános IP-cím áttelepülési céljának szerkesztéséhez módosítsa a **helytulajdonságot** az **erőforrások**alatt:

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
  
    A régióhely-kódok beolvassa az [Azure-helyeket.](https://azure.microsoft.com/global-infrastructure/locations/) A régió kódja a terület neve szóközök nélkül. Például az USA középső részén található kód a **centralus**.
    
12. A sablon más paramétereit is módosíthatja, ha igényszerint szeretné vagy szüksége van rá:

    * **Termékváltozat**. A nyilvános IP-cím termékváltozatát a konfigurációban szabványosról alapszintre vagy alapszintről szabványosra módosíthatja **a** template.json fájlban lévő **sku** névtulajdonság módosításával:

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

        Az alapszintű és a szabványos termékváltozat nyilvános IP-címei közötti különbségekről a [Nyilvános IP-cím létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)című témakörben talál.

    * **Nyilvános IP-foglalási módszer** és **tétlen időtúlfoglalás**. A nyilvános IPAllocationMethod tulajdonság **dinamikusról** **statikusra** vagy **Statikusról** dinamikusra történő módosításával módosíthatja a nyilvános **IPAllocationMethod** **tulajdonságot.** Az indle timeout az **idleTimeoutInMinutes** tulajdonság kívánt értékre való módosításával módosítható. Az alapértelmezett érték **4**.

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

        A felosztási módszerekről és az alapjárati időtúlértékekkel kapcsolatban a [Nyilvános IP-cím létrehozása, módosítása és törlése](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)című témakörben talál további információt.

 
13. Válassza a **Mentés** lehetőséget az online szerkesztőben.

14. Válassza **a BASICS-előfizetés** > **Subscription** lehetőséget, ha kiválaszthatja azt az előfizetést, amelyen a célnyilvános IP lesz telepítve.

15. Válassza **a BASICS** > **erőforráscsoport** kiválasztásához az erőforráscsoport, ahol a cél nyilvános IP lesz telepítve. Az **Új létrehozása** lehetőséget választva új erőforráscsoportot hozhat létre a nyilvános ip-címhez. Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő forrás nyilvános IP forráserőforrás-csoportja.

16. Ellenőrizze, hogy a **BASICS** > **hely** be van-e állítva arra a célhelyre, ahol a nyilvános IP-címet telepíteni szeretné.

17. A **BEÁLLÍTÁSOK csoportban**ellenőrizze, hogy a név megegyezik-e a paraméterek szerkesztőjében korábban megadott névvel.

18. Jelölje be a **FELTÉTELEK** jelölőnégyzetet.

19. Válassza a **Vásárlás** lehetőséget a nyilvános cél IP üzembe helyezéséhez.

20. Ha egy másik nyilvános IP-cím, amely a kimenő NAT a terheléselosztó áthelyezése, ismételje meg az előző lépéseket az export és a második kimenő nyilvános IP-cím a célrégióba.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>A külső terheléselosztó sablon exportálása és a terheléselosztó üzembe helyezése az Azure Portalról

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.
2. Keresse meg a külső forrásterhelés-elosztót tartalmazó erőforráscsoportot, és jelölje ki.
3. Válassza a **Beállítások** > **exportálása sablont**.
4. Válassza **a Telepítés lehetőséget** az Exportálás sablon **csoportban.**
5. Válassza **a TEMPLATE** > **Edit paraméterek lehetőséget** a parameters.json fájl online szerkesztőben való megnyitásához.

5. A külső terheléselosztó nevének paraméterének szerkesztéséhez módosítsa a forrás külső terheléselosztó nevének **értéktulajdonságát** a cél külső terheléselosztó nevére. A nevet tegye idézőjelek közé.

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

6.  Az előző lépésekben áthelyezett nyilvános célIP-cím értékének szerkesztéséhez először be kell szereznie az erőforrás-azonosítót, majd be kell illesztenie a parameters.json fájlba. Az azonosító beszerzéséhez:

    1. Egy másik böngészőlapon vagy ablakban jelentkezzen be az [Azure Portalra,](https://portal.azure.com) és válassza az **Erőforráscsoportok**lehetőséget.
    2. Keresse meg azt a célerőforrás-csoportot, amely az előző lépésekben áthelyezett nyilvános IP-címet tartalmazza. Válassza ki.
    3. Válassza a **Beállítások** > **tulajdonságai lehetőséget.**
    4. A jobb oldali panelen jelölje ki az **erőforrás-azonosítót,** és másolja a vágólapra. Másik lehetőségként **kiválaszthatja a vágólapra másolását** az **Erőforrás-azonosító** elérési útját.
    5. Illessze be az erőforrás-azonosítót a másik böngészőablakban vagy lapon megnyitott **Paraméterek szerkesztése** szerkesztő **értéktulajdonságába:**

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
    6. Válassza a **Mentés** lehetőséget az online szerkesztőben.


7.  Ha konfigurált kimenő NAT és kimenő szabályok a terheléselosztó, megjelenik egy harmadik bejegyzés ebben a fájlban a külső azonosító a kimenő nyilvános IP-cím. Ismételje meg az előző lépéseket a **célrégióban** a kimenő nyilvános IP azonosítójának beszerzéséhez. Illessze be az azonosítót a parameters.json fájlba:

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

8.  Válassza **a TEMPLATE** > **Edit sablon lehetőséget** a template.json fájl online szerkesztőben való megnyitásához.
9.  A külső terheléselosztó konfigurációjának áthelyezéséhez szükséges célterület szerkesztéséhez módosítsa a **location** tulajdonságot a template.json fájl **erőforrásai** alatt:

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

10. A régióhely-kódok beolvassa az [Azure-helyeket.](https://azure.microsoft.com/global-infrastructure/locations/) A régió kódja a terület neve szóközök nélkül. Például az USA középső részén található kód a **centralus**.

11. A sablon más paramétereit is módosíthatja, ha igényszerint szeretné vagy szüksége van rá:

    * **Termékváltozat**. A külső terheléselosztó termékváltozatát a konfigurációban szabványosról alapszintre vagy alapszintről szabványosra módosíthatja **a** template.json fájlban lévő **sku** névtulajdonság módosításával:

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
      Az alapszintű és a szabványos termékváltozat terheléselosztók közötti különbségekről az [Azure Standard Load Balancer overview című témakörben olvashat.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Terheléselosztási szabályok**. A konfigurációban hozzáadhat vagy eltávolíthat terheléselosztási szabályokat, ha bejegyzéseket ad hozzá vagy távolít el a template.json fájl **loadBalancingRules** szakaszában:

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

    * **Szondák**. A template.json fájl **mintavételezési** szakaszában a mintavételek szakaszbejegyzéseinek hozzáadásával vagy eltávolításával hozzáadhat vagy eltávolíthat egy mintavételt a konfigurációban:

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
       További információ: [Load Balancer health probes](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Bejövő NAT szabályok**. A terheléselosztó bejövő NAT-szabályait a template.json fájl **inboundNatRules** szakaszában lévő bejegyzések hozzáadásával vagy eltávolításával veheti fel vagy távolíthatja el:

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
        A bejövő NAT-szabály hozzáadásának vagy eltávolításának befejezéséhez a szabálynak a template.json fájl végén jelen lévőnek vagy **típustulajdonságként** kell lennie:

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
        A bejövő NAT-szabályokról a [Mi az Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Kimenő szabályok**. A konfigurációban a kimenő szabályokat a template.json fájl **outboundRules** tulajdonságának szerkesztésével veheti fel vagy távolíthatja el:

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

         További információ: [Load Balancer kimenő szabályok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).

12. Válassza a **Mentés** lehetőséget az online szerkesztőben.

13. Válassza **a BASICS-előfizetés** > **Subscription** lehetőséget, ha kiválaszthatja azt az előfizetést, amelyen a cél külső terheléselosztó telepítve lesz.

15. Válassza **a BASICS** > **erőforráscsoport** kiválasztásához az erőforráscsoportot, ahol a cél terheléselosztó lesz telepítve. Az **Új létrehozása** lehetőséget választva új erőforráscsoportot hozhat létre a cél külső terheléselosztóhoz. Vagy kiválaszthatja a meglévő erőforráscsoportot, amelykorábban a nyilvános IP-címhez létrehozott. Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő külső forrás terheléselosztó forráserőforrás-csoportja.

16. Ellenőrizze, hogy a **BASICS** > **hely** be van-e állítva arra a célhelyre, ahol a külső terheléselosztót telepíteni szeretné.

17. A **BEÁLLÍTÁSOK csoportban**ellenőrizze, hogy a név megegyezik-e a paraméterek szerkesztőjében korábban megadott névvel. Ellenőrizze, hogy az erőforrásazonosítók fel vannak-e töltve a konfigurációban lévő nyilvános IP-khez.

18. Jelölje be a **FELTÉTELEK** jelölőnégyzetet.

19. Válassza a **Vásárlás** lehetőséget a nyilvános cél IP üzembe helyezéséhez.

## <a name="discard"></a>Elvetés

Ha el szeretné vetni a nyilvános ip-cím és a külső terheléselosztó, törölje az erőforráscsoportot, amely tartalmazza. Ehhez jelölje ki az erőforráscsoportot az irányítópulton a portálon, majd válassza a **Törlés** lehetőséget az áttekintő lap tetején.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások véglegesítéséhez és a nyilvános IP-cím és a külső terheléselosztó áthelyezésének befejezéséhez törölje a forrás nyilvános IP-címét és a külső terheléselosztót vagy erőforráscsoportot. Ehhez jelölje ki az adott erőforráscsoportot az irányítópulton a portálon, majd válassza a **Törlés** lehetőséget az egyes lapok tetején.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áthelyezett egy Azure-beli külső terheléselosztót egyik régióból a másikba, és megtisztította a forráserőforrásokat. Ha többet szeretne tudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vészhelyreállításról:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
