---
title: Egy Azure-beli virtuális hálózat áthelyezése egy másik Azure-régióba az Azure Portal használatával.
description: Azure-beli virtuális hálózat áthelyezése egyik Azure-régióból a másikba egy Resource Manager-sablon és a Azure Portal használatával.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: d6f417e53e7d7a1a242a0c0dc56c2356f78f5344
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828954"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Azure-beli virtuális hálózat áthelyezése másik régióba a Azure Portal használatával

A meglévő Azure-beli virtuális hálózatok egyik régióból a másikba való áthelyezésének különböző forgatókönyvei vannak. Előfordulhat például, hogy egy virtuális hálózatot szeretne létrehozni ugyanazzal a konfigurációval a teszteléshez és a rendelkezésre álláshoz, mint a meglévő virtuális hálózatot. Vagy előfordulhat, hogy a vész-helyreállítási terv részeként egy éles virtuális hálózatot szeretne áthelyezni egy másik régióba.

A virtuális hálózat áthelyezését egy másik régióba Azure Resource Manager sablon használatával végezheti el. Ezt úgy teheti meg, hogy a virtuális hálózatot sablonba exportálja, módosítja a paramétereket, hogy azok megfeleljenek a célként megadott régiónak, majd üzembe helyezi a sablont az új régióban. A Resource Manager-sablonokkal kapcsolatos további információkért lásd: [Quickstart: Azure Resource Manager-sablonok létrehozása és üzembe helyezése a](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)Azure Portal használatával.


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a virtuális hálózat abban az Azure-régióban található, amelyet át szeretne helyezni.

- Virtuális hálózat exportálásához és sablon üzembe helyezéséhez egy másik régióban lévő virtuális hálózat létrehozásához szükség van a hálózati közreműködő szerepkörre vagy magasabbra.

- A virtuális hálózati partnerek nem jönnek létre újra, és sikertelenek lesznek, ha még jelen vannak a sablonban. A sablon exportálása előtt el kell távolítania minden virtuális hálózati társat. Ezután újra létrehozhatók a virtuális hálózat áthelyezése után.

- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés tartalmaz, de nem korlátozódik a terheléselosztó, a hálózati biztonsági csoportok (NSG) és a nyilvános IP-címek számára.

- Ellenőrizze, hogy az Azure-előfizetése lehetővé teszi-e a virtuális hálózatok létrehozását a célcsoportban. A szükséges kvóta engedélyezéséhez forduljon az ügyfélszolgálathoz.

- Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a virtuális hálózatok ezen folyamathoz való hozzáadásának támogatásához. További információk: [Az Azure-előfizetésekre és -szolgáltatásokra vonatkozó korlátozások, kvóták és megkötések](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Felkészülés az áthelyezésre
Ebben a szakaszban egy Resource Manager-sablon használatával készíti elő a virtuális hálózatot az áthelyezéshez. Ezután a Azure Portal használatával helyezheti át a virtuális hálózatot a célként megadott régióba.

A virtuális hálózat exportálásához és a cél virtuális hálózatnak a Azure Portal használatával történő üzembe helyezéséhez tegye a következőket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd válassza az **erőforráscsoportok**lehetőséget.
1. Keresse meg azt az erőforráscsoportot, amely a forrás virtuális hálózatot tartalmazza, majd jelölje ki.
1. Válassza a **Beállítások** > **Exportálás sablon**lehetőséget.
1. A **sablon exportálása** panelen válassza a **telepítés**lehetőséget.
1. Ha meg szeretné nyitni a *Parameters. JSON* fájlt az online szerkesztőben, válassza a **sablon**@no__t – 2**Paraméterek szerkesztése**lehetőséget.
1. A virtuális hálózat nevének paraméterének szerkesztéséhez módosítsa a **Value** tulajdonságot a **Paraméterek**alatt:

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

1. A szerkesztőben módosítsa a forrás virtuális hálózat nevének értékét a szerkesztőben egy olyan névre, amelyet a célként szolgáló virtuális hálózathoz szeretne használni. Ügyeljen arra, hogy idézőjelek közé foglalja a nevet.

1. Válassza a **Mentés** lehetőséget a szerkesztőben.

1. A *template. JSON* fájl megnyitásához az online szerkesztőben **válassza a sablon @no__t**-2 sablon**szerkesztése**lehetőséget.

1. Az online szerkesztőben annak a célcsoportnak a szerkesztéséhez, ahol a virtuális hálózat át lesz helyezve, módosítsa a **Location (hely** ) tulajdonságot az **erőforrások**területen.

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

1. A régióbeli hely kódjának beszerzéséhez tekintse meg az [Azure-helyeket](https://azure.microsoft.com/global-infrastructure/locations/). A régió kódja a régió neve, szóközök nélkül (például az **USA középső**@no__t – 1**CentralUS**).

1. Választható A követelményektől függően a sablon egyéb paramétereit is módosíthatja:

    * **Címterület**: A fájl mentése előtt a virtuális hálózat címterület módosításával módosíthatja az **erőforrások** > **addressSpace** szakaszt, és módosíthatja a **addressPrefixes** tulajdonságot:

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

    * **Alhálózat**: A sablon **alhálózatai** szakaszának módosításával módosíthatja vagy hozzáadhatja az alhálózat nevét és az alhálózati címtartomány méretét. A **Name (név** ) tulajdonság módosításával módosíthatja az alhálózat nevét. Az alhálózati címterület a **addressPrefix** tulajdonság módosításával is módosítható:

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

        Ha módosítani szeretné a *sablon. JSON* fájljának előtagját, szerkessze azt két helyen: az előző szakaszban található kódban, valamint a következő kód **Type (típus** ) szakaszában. Módosítsa a **addressPrefix** tulajdonságot a következő kódban, hogy az megfeleljen az előző szakaszban található kódban szereplő **addressPrefix** tulajdonságnak.

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

1. Az online szerkesztőben válassza a **Mentés**lehetőséget.

1. Válassza ki azt az előfizetést, amelybe a célként megadott virtuális hálózatot telepíteni kívánja, majd válassza az **alapok** > **előfizetés**lehetőséget.

1. Válassza ki azt az erőforráscsoportot, amelyben a célként szolgáló virtuális hálózat üzembe lesz helyezve, válassza az **alapok** > **erőforráscsoport**elemet. 

    Ha létre kell hoznia egy új erőforráscsoportot a célként megadott virtuális hálózathoz, válassza az **új létrehozása**lehetőséget. Győződjön meg arról, hogy a név nem ugyanaz, mint a forrás erőforráscsoport neve a meglévő virtuális hálózaton.

1. Győződjön meg arról, hogy az **alapvető** > **hely** a virtuális hálózat központi telepítésének célhelyére van beállítva.

1. A **Beállítások**területen ellenőrizze, hogy a név megegyezik-e a korábban a paraméterek szerkesztőjében megadott névvel.

1. Jelölje be a **feltételek és kikötések** jelölőnégyzetet.

1. A cél virtuális hálózat üzembe helyezéséhez válassza a **vásárlás**lehetőséget.

## <a name="delete-the-target-virtual-network"></a>A célként megadott virtuális hálózat törlése

A cél virtuális hálózat elvetéséhez törölje a célként szolgáló virtuális hálózatot tartalmazó erőforráscsoportot. Ehhez tegye a következőket:
1. A Azure Portal irányítópulton válassza ki az erőforráscsoportot.
1. Az **Áttekintés** panel felső részén válassza a **Törlés**lehetőséget.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások elvégzéséhez és a virtuális hálózati áthelyezés befejezéséhez törölje a forrás virtuális hálózatot vagy az erőforráscsoportot. Ehhez tegye a következőket:
1. A Azure Portal irányítópulton válassza ki a virtuális hálózatot vagy az erőforráscsoportot.
1. Az egyes ablaktábla tetején válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-beli virtuális hálózatot helyezett át egyik régióból a másikba a Azure Portal használatával, majd megtisztította a szükségtelen forrás-erőforrásokat. Ha többet szeretne megtudni a régiók és a vész-helyreállítási erőforrások közötti áthelyezésről az Azure-ban, tekintse meg a következőket:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
