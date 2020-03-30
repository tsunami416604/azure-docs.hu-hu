---
title: Integrációs szolgáltatási környezetek (ISEs-ek) létrehozása a Logic Apps REST API-val
description: Integrációs szolgáltatási környezet (ISE) létrehozása a Logic Apps REST API használatával, így az Azure-beli virtuális hálózatok (VNET-k) az Azure Logic Apps-ből érhetők el
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 2c6e35b1e7d160064998004f87c5b14d0eaeac5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127660"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Integrációs szolgáltatási környezet (ISE) létrehozása a Logic Apps REST API használatával

Ez a cikk bemutatja, hogyan hozhat létre [ *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) a Logic Apps REST API-n keresztül olyan forgatókönyvekhez, ahol a logikai alkalmazásoknak és az integrációs fiókoknak hozzáférésre van szükségük egy [Azure virtuális hálózathoz.](../virtual-network/virtual-networks-overview.md) Az ISE egy elszigetelt környezet, amely dedikált tárolót és más erőforrásokat használ, amelyek elkülönülnek a "globális" több-bérlős Logic Apps szolgáltatástól. Ez a szétválasztás is csökkenti a más Azure-bérlők esetleges hatása az alkalmazások teljesítményét. Az ISE saját statikus IP-címeket is biztosít. Ezek az IP-címek elkülönülnek a statikus IP-címek, amelyek a nyilvános, több-bérlős szolgáltatás logikai alkalmazások által megosztott.

Ha az Azure-portál használatával szeretne létrehozni egy ISE-t, olvassa el a [Csatlakozás az Azure-beli virtuális hálózatokhoz az Azure Logic Apps alkalmazásból című témakört.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

> [!IMPORTANT]
> A logikai alkalmazások, a beépített eseményindítók, a beépített műveletek és az ISE-ben futó összekötők a fogyasztásalapú díjcsomagtól eltérő díjszabási tervet használnak. Ha meg szeretné tudni, hogyan működik az ise-k díjszabása és számlázása, olvassa el a [Logic Apps díjszabási modelljét.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Az árak, lásd: [Logic Apps árképzés.](../logic-apps/logic-apps-pricing.md)

## <a name="prerequisites"></a>Előfeltételek

* Ugyanazok [az előfeltételek](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) és [követelmények az ISE-hez való hozzáférés engedélyezéséhez,](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) mint amikor ise-t hoz létre az Azure Portalon

* Az ise létrehozásához használható eszköz a Logic Apps REST API https PUT-kérelemmel történő hívásával. Használhatja például [a Postman](https://www.getpostman.com/downloads/)alkalmazást, vagy létrehozhat egy logikai alkalmazást, amely végrehajtja ezt a feladatot.

## <a name="send-the-request"></a>A kérelem elküldése

Az ISE létrehozásához hívja meg a Logic Apps REST API-t, tegye meg ezt a HTTPS PUT-kérelmet:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A Logic Apps REST API 2019-05-01 verzió megköveteli, hogy saját HTTP PUT-kérelmet ISE-összekötők.

A telepítés általában két órán belül befejeződik. Esetenként a telepítés akár négy órát is igénybe vehet. A központi telepítés állapotának ellenőrzéséhez az [Azure Portalon](https://portal.azure.com)az Azure eszköztáron válassza ki az értesítések ikonját, amely megnyitja az értesítések ablaktábláját.

> [!NOTE]
> Ha az üzembe helyezés sikertelen, vagy törli az ISE-t, az Azure-nak akár egy órába is telhet, mielőtt felszabadítja az alhálózatokat. Ez a késleltetés azt jelenti, hogy előfordulhat, hogy várnia kell, mielőtt újra felhasználná ezeket az alhálózatokat egy másik ISE-ben.
>
> Ha törli a virtuális hálózatot, az Azure általában akár két órát is igénybe vehet az alhálózatok felszabadítása előtt, de ez a művelet hosszabb időt vehet igénybe. 
> Virtuális hálózatok törlésekor győződjön meg arról, hogy nincs enek erőforrások csatlakoztatva. 
> Lásd: [Virtuális hálózat törlése](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Kérelem fejléce

A kérelem fejlécében adja meg a következő tulajdonságokat:

* `Content-type`: Állítsa ezt `application/json`a tulajdonságot a értékre.

* `Authorization`: Állítsa be ezt a tulajdonságértékét a tulajdonosi jogkivonatra a használni kívánt Azure-előfizetéshez vagy erőforráscsoporthoz hozzáféréssel rendelkező ügyfél számára.

### <a name="request-body-syntax"></a>Kérelem törzsének szintaxisa

Az ISE létrehozásakor használandó tulajdonságokat az alábbi hivatkozási alapként ismerteti:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>Törzs igénylése példa

Ez a példa kérelem törzse a mintaértékeket mutatja:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>További lépések

* [Erőforrások hozzáadása integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integrációs szolgáltatási környezetek kezelése](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

