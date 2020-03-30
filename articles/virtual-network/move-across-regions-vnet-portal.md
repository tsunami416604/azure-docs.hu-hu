---
title: Az Azure-portál használatával áthelyezhet egy Azure-beli virtuális hálózatot egy másik Azure-régióba.
description: Az Azure virtuális hálózat áthelyezése az egyik Azure-régióból a másikba egy Resource Manager-sablon és az Azure Portal használatával.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: c54725d9a947b0c912a822686d7b2cffe1a7b5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640788"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Azure-beli virtuális hálózat áthelyezése másik régióba az Azure Portal használatával

Különböző forgatókönyvek egy meglévő Azure virtuális hálózat áthelyezése az egyik régióból a másikba. Előfordulhat például, hogy a meglévő virtuális hálózattal azonos tesztelési és rendelkezésre állási konfigurációval rendelkező virtuális hálózatot szeretne létrehozni. Vagy előfordulhat, hogy egy éles virtuális hálózatot át szeretne helyezni egy másik régióba a vész-helyreállítási tervezés részeként.

Használhatja az Azure Resource Manager sablont a virtuális hálózat áthelyezése egy másik régióba. Ehhez exportálja a virtuális hálózatot egy sablonba, módosítja a paramétereket a célrégiónak megfelelően, majd telepíti a sablont az új régióba. Az Erőforrás-kezelő sablonjairól további információt a [Rövid útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése az Azure Portal használatával című témakörben talál.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a virtuális hálózat az Azure-régióban, amelyről át szeretne helyezni.

- Virtuális hálózat exportálásához és egy sablon központi telepítéséhez egy másik régióban lévő virtuális hálózat létrehozásához a Hálózati közreműködő szerepkörrel vagy magasabb szintű szerepkörrel kell rendelkeznie.

- A virtuális hálózati társviszony-létesítések nem jönnek létre újra, és sikertelenek lesznek, ha még mindig jelen vannak a sablonban. A sablon exportálása előtt el kell távolítania a virtuális hálózati társakat. Ezután a virtuális hálózat áthelyezése után újra létrehozhatja őket.

- Azonosítsa a forráshálózati elrendezést és az összes jelenleg használt erőforrást. Ez az elrendezés tartalmazza, de nem kizárólagosan a terheléselosztók, hálózati biztonsági csoportok (NSG-k) és a nyilvános IP-k.

- Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e, hogy virtuális hálózatokat hozzon létre a célrégióban. A szükséges kvóta engedélyezéséhez forduljon az ügyfélszolgálathoz.

- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik a virtuális hálózatok hozzáadásának támogatásához ehhez a folyamathoz. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Készüljetek a költözésre!
Ebben a szakaszban előkészíti a virtuális hálózatot az áthelyezéshez egy Erőforrás-kezelő sablon használatával. Ezután helyezze át a virtuális hálózatot a célrégióba az Azure Portal használatával.

A virtuális hálózat exportálásához és a célvirtuális hálózat azure-portálon történő központi telepítéséhez tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza **az Erőforráscsoportok**lehetőséget.
1. Keresse meg a virtuális forráshálózatot tartalmazó erőforráscsoportot, majd jelölje ki.
1. Válassza a **Beállítások** > **exportálása sablont**.
1. A **Sablon exportálása** ablaktáblán válassza a **Telepítés**lehetőséget.
1. A *parameters.json* fájl online szerkesztőben való megnyitásához válassza a **Sablon** > **szerkesztési paraméterek lehetőséget.**
1. A virtuális hálózat nevének paraméterének szerkesztéséhez módosítsa az **értéktulajdonságot** a **paraméterek**alatt:

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

1. A szerkesztőben módosítsa a forrás virtuális hálózat nevének értékét a szerkesztőben a célvirtuális hálózat kívánt nevére. Ügyeljen arra, hogy a nevet idézőjelek közé tegye.

1. Válassza a **Mentés** a szerkesztőben lehetőséget.

1. A *template.json* fájl online szerkesztőben való megnyitásához válassza a **Sablonszerkesztés** > **sablon lehetőséget.**

1. Az online szerkesztőben a virtuális hálózat áthelyezésének célrégiójának szerkesztéséhez módosítsa a **helytulajdonságot** az **erőforrások**alatt:

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

1. A régióhely-kódok beszerzéséhez olvassa el az Azure Locations ( Azure Locations ) ( Azure Locations ) ( Azure Locations ) ( Azure Locations ) [(A régió hely](https://azure.microsoft.com/global-infrastructure/locations/) A régió kódja a régió neve szóközök nélkül (például **az USA** = középső**része centralus**).

1. (Nem kötelező) A sablon ban a követelményektől függően más paramétereket is módosíthat:

    * **Címtér**: A fájl mentése előtt módosíthatja a virtuális hálózat címterét az **erőforrások** > **addressSpace** szakaszának módosításával és a **addressPrefixes** tulajdonság módosításával:

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

    * **Alhálózat**: Az alhálózat nevét és az alhálózati címteret a sablon alhálózati szakaszának módosításával módosíthatja vagy **hozzáadhatja.** Az alhálózat nevét a **névtulajdonság** módosításával módosíthatja. Az alhálózati címteret pedig a **addressPrefix** tulajdonság módosításával módosíthatja:

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

        A *template.json* fájl címelőtagának módosításához két helyen módosítsa azt: az előző szakaszban és a következő kód **típus** szakaszában. Módosítsa a **addressPrefix** tulajdonságot a következő kódban, hogy megfeleljen az előző szakaszban található kódban található **addressPrefix** tulajdonságnak.

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

1. A célvirtuális hálózat telepítéséhez szükséges előfizetés kiválasztásához válassza **az Alapjai** > **előfizetés lehetőséget.**

1. A célvirtuális hálózat központi telepítésének helyéhez válassza az **Alapvető** > **erőforráscsoport**lehetőséget. 

    Ha új erőforráscsoportot kell létrehoznia a célvirtuális hálózathoz, válassza az **Új létrehozása lehetőséget.** Győződjön meg arról, hogy a név nem ugyanaz, mint a forráserőforrás-csoport neve a meglévő virtuális hálózatban.

1. Ellenőrizze, hogy **az Alapok** > **helye** be van-e állítva arra a célhelyre, ahol a virtuális hálózatot telepíteni szeretné.

1. A **Beállítások csoportban**ellenőrizze, hogy a név megegyezik-e a paraméterek szerkesztőjében korábban megadott névvel.

1. Jelölje be az **Általános szerződési feltételek** jelölőnégyzetet.

1. A célvirtuális hálózat telepítéséhez válassza a **Vásárlás**lehetőséget.

## <a name="delete-the-target-virtual-network"></a>A célvirtuális hálózat törlése

A célvirtuális hálózat elvetéséhez törölje a célvirtuális hálózatot tartalmazó erőforráscsoportot. Ehhez tegye a következőket:
1. Az Azure Portal irányítópultján válassza ki az erőforráscsoportot.
1. Az **Áttekintő** ablaktábla tetején válassza a **Törlés**lehetőséget.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások véglegesítéséhez és a virtuális hálózat áthelyezésének befejezéséhez törölje a forrásvirtuális hálózatot vagy erőforráscsoportot. Ehhez tegye a következőket:
1. Az Azure Portal irányítópultján válassza ki a virtuális hálózatot vagy erőforráscsoportot.
1. Az egyes ablaktáblák tetején válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áthelyezett egy Azure virtuális hálózatot az egyik régióból a másikba az Azure Portal használatával, majd törölte a szükségtelen forráserőforrásokat. Ha többet szeretne tudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vészhelyreállításról:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Az Azure virtuális gépei áthelyezése egy másik régióba](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
