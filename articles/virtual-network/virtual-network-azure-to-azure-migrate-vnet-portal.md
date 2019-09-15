---
title: Az Azure Virtual Network egy másik Azure-régióba helyezheti át a Azure Portal használatával.
description: Azure Resource Manager sablonnal áthelyezheti az Azure-Virtual Network az egyik Azure-régióból a másikba a Azure Portal használatával.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: d0f8a52eace9af4dc0865e1a2f277b9ab31c6858
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997418"
---
# <a name="move-azure-virtual-network-to-another-region-using-the-azure-portal"></a>Az Azure Virtual Network áthelyezése másik régióba a Azure Portal használatával

Különböző helyzetekben érdemes áthelyeznie meglévő Azure-beli virtuális hálózatait (virtuális hálózatok) az egyik régióból a másikba. Előfordulhat például, hogy létre szeretne hozni egy virtuális hálózatot ugyanazzal a konfigurációval a meglévő virtuális hálózat teszteléséhez és rendelkezésre állásához. Előfordulhat, hogy a vész-helyreállítási tervezés részeként egy éles virtuális hálózatot is át szeretne helyezni egy másik régióba.

A virtuális hálózat áthelyezését egy másik régióba Azure Resource Manager sablon használatával végezheti el. Ezt úgy teheti meg, hogy a virtuális hálózatot sablonba exportálja, módosítja a paramétereket, hogy azok megfeleljenek a célként megadott régiónak, majd üzembe helyezi a sablont az új régióban.  A Resource Managerrel és a sablonokkal kapcsolatos további információkért [lásd: gyors útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése a Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure Virtual Network abban az Azure-régióban található, amelyről át kívánja helyezni.

- Virtuális hálózat exportálásához és sablon üzembe helyezéséhez egy másik régióban lévő virtuális hálózat létrehozásához szüksége lesz a hálózati közreműködő szerepkörre vagy magasabbra.

- A virtuális hálózati társítások nem lesznek újra létrehozva, és sikertelenek lesznek, ha még jelen vannak a sablonban.  A sablon exportálása előtt el kell távolítania minden virtuális hálózati társat, majd újra létre kell hoznia a társait a virtuális hálózat áthelyezése után.
    
- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés tartalmaz, de nem korlátozódik a terheléselosztó, a hálózati biztonsági csoportok (NSG) és a nyilvános IP-címek számára.

- Győződjön meg arról, hogy az Azure-előfizetése lehetővé teszi, hogy virtuális hálózatokat hozzon létre a használt célként megadott régióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a virtuális hálózatok ezen folyamathoz való hozzáadásának támogatásához.  Tekintse meg az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Előkészítés és áthelyezés
A következő lépések bemutatják, hogyan készítse elő a virtuális hálózatot az áthelyezéshez Resource Manager-sablonnal, és a portál használatával helyezze át a virtuális hálózatot a célként megadott régióba.

### <a name="export-the-template-and-deploy-from-the-portal"></a>A sablon exportálása és üzembe helyezése a portálról

1. Jelentkezzen be az [Azure Portal](http://portal.azure.com) > **erőforráscsoporthoz**.
2. Keresse meg a forrás virtuális hálózatot tartalmazó erőforráscsoportot, és kattintson rá.
3. Válassza > **Beállítások** > **Exportálás sablon**lehetőséget.
4. A **sablon exportálása** panelen válassza a **telepítés** lehetőséget.
5. Kattintson a **sablon** > **szerkesztése paraméterek** lehetőségre a **Parameters. JSON** fájl megnyitásához az online szerkesztőben.
6. A virtuális hálózat nevének paraméterének szerkesztéséhez módosítsa a **Value** tulajdonságot a **Paraméterek**alatt:
    
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
7. Módosítsa a forrás virtuális hálózat nevének értékét a szerkesztőben a cél VNET kívánt névre. Győződjön meg arról, hogy a nevet idézőjelek közé adja.

8.  Kattintson a **Mentés** gombra a szerkesztőben.

9.  Kattintson a sablon**szerkesztése** elemre, hogy megnyissa a **template. JSON** fájlt az online szerkesztőben. >  

10. Ha módosítani szeretné a VNET áthelyezni kívánt célpontot, módosítsa a **Location (hely** ) tulajdonságot az online szerkesztő **erőforrásai** alatt:

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
 
11. A régióbeli hely kódjának beszerzéséhez tekintse meg az [Azure-helyeket](https://azure.microsoft.com/global-infrastructure/locations/).  A régió kódja a régió neve szóközök nélkül, **Közép-USA** = **CentralUS**.
 
12. A sablon egyéb paramétereit is módosíthatja, és a követelményektől függően választható:

    * **Címterület** – a VNET megváltoztatható a sablonban a Mentés előtt, ha módosítja az **erőforrások** > **addressSpace** szakaszt, és megváltoztatja a **addressPrefixes** tulajdonságot a **template. JSON fájlban.** fájl:
    
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

    * **Alhálózat** – az alhálózat neve és az alhálózati címtartomány módosítható vagy hozzáadható a **sablon. JSON** fájl **alhálózatok** szakaszának módosításával. Az alhálózat neve módosítható a **template. JSON** fájl Name ( **név** ) tulajdonságának módosításával.  Az alhálózati címtartomány a **template. JSON** fájl **addressPrefix** tulajdonságának módosításával módosítható:
    
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
                    ],
    ```

    A **sablon. JSON** fájljában a címzési előtag módosításához két helyen kell szerkesztenie, a fent felsorolt szakaszt és az alább felsorolt **típusok** szakaszt.  Módosítsa a **addressPrefix** tulajdonságot úgy, hogy az megfeleljen a fentieknek:
                
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

13. Kattintson a **Save (Mentés** ) gombra az online szerkesztőben.

14. Az alapértékek**előfizetése** lehetőségre kattintva válassza ki azt az előfizetést, amelyben a cél VNET telepíteni fogja. > 

15. Az alapszintű erőforráscsoport **elemre kattintva válassza ki**azt az erőforráscsoportot, amelyben a cél VNET telepíteni fogja.  >   Az **új létrehozása** lehetőségre kattintva létrehozhat egy új erőforráscsoportot a cél VNET.  Győződjön meg arról, hogy a név nem ugyanaz, mint a meglévő VNET forrásoldali erőforráscsoport. 

16. Győződjön meg arról, hogy az alapértékek**helye** arra a célhelyre van beállítva, ahol a VNET telepíteni kívánja. > 

17. Ellenőrizze a **Beállítások** területen, hogy a név megegyezik-e a fenti Parameters Editorban megadott névvel.

18. Jelölje be a jelölőnégyzetet a **feltételek és KIkötések**területen.

19. A cél virtuális hálózat üzembe helyezéséhez kattintson a **vásárlás** gombra.

## <a name="discard"></a>Elvetés 

Ha el szeretné vetni a cél virtuális hálózatot, törölje a célként szolgáló virtuális hálózatot tartalmazó erőforráscsoportot.  Ehhez válassza ki az erőforráscsoportot az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az Áttekintés oldal tetején.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások elvégzéséhez és a virtuális hálózat áthelyezésének befejezéséhez törölje a forrás virtuális hálózatot vagy az erőforráscsoportot. Ehhez válassza ki a virtuális hálózatot vagy erőforráscsoportot az irányítópulton a portálon, és válassza a **Törlés** lehetőséget az egyes lapok tetején.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-Virtual Network helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
