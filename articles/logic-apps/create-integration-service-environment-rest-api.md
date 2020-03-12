---
title: Integrációs szolgáltatási környezetek (ISEs) létrehozása Logic Apps REST API
description: Hozzon létre egy integrációs szolgáltatási környezetet (ISE) a Logic Apps REST API használatával, hogy elérhető legyen az Azure Virtual Networks (virtuális hálózatok) az Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 2c6e35b1e7d160064998004f87c5b14d0eaeac5e
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127660"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Integrációs szolgáltatási környezet (ISE) létrehozása a Logic Apps használatával REST API

Ez a cikk bemutatja, hogyan hozhat létre [ *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) a Logic apps REST API olyan helyzetekben, ahol a logikai alkalmazások és az integrációs fiókok hozzáférést igényelnek egy Azure-beli [virtuális hálózathoz](../virtual-network/virtual-networks-overview.md). Az ISE egy elszigetelt környezet, amely dedikált tárolót és más erőforrásokat használ, amelyeket a "globális" több-bérlős Logic Apps szolgáltatástól elkülönítve tartanak. Ez a elkülönítés azt is csökkenti, hogy más Azure-bérlők milyen hatással lehetnek az alkalmazások teljesítményére. Az ISE a saját statikus IP-címeivel is rendelkezik. Ezek az IP-címek a nyilvános, több-bérlős szolgáltatásban a logikai alkalmazások által megosztott statikus IP-címektől eltérnek.

Ha ehelyett a Azure Portal használatával szeretne létrehozni egy ISE-t, tekintse meg a következőt: [Kapcsolódás Azure-beli virtuális hálózatokhoz Azure Logic Appsról](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> A Logic apps, a beépített triggerek, a beépített műveletek és az ISE-ben futó összekötők a fogyasztáson alapuló díjszabási csomagtól eltérő díjszabási csomagot használnak. A ISEs díjszabásának és számlázásának megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatban lásd: [Logic apps díjszabása](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Előfeltételek

* Ugyanazok az [Előfeltételek](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) és [követelmények, amelyek lehetővé teszik az ISE hozzáférését](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) , mint amikor egy ISE-t hoz létre a Azure Portal

* Egy eszköz, amellyel létrehozhatja az ISE-t úgy, hogy meghívja a Logic Apps REST API egy HTTPS PUT-kéréssel. Használhatja például a [Poster](https://www.getpostman.com/downloads/)-t, vagy létrehozhat egy logikai alkalmazást, amely elvégzi ezt a feladatot.

## <a name="send-the-request"></a>A kérelem elküldése

Az ISE létrehozásához a Logic Apps REST API meghívásával végezze el ezt a HTTPS PUT-kérelmet:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A Logic Apps REST API 2019-05-01-es verziójának a saját HTTP PUT-kérelmét kell használnia az ISE-összekötők számára.

Az üzembe helyezés általában két órán belül befejeződik. Alkalmanként az üzembe helyezés akár négy órát is igénybe vehet. A központi telepítés állapotának megtekintéséhez a [Azure Portal](https://portal.azure.com)az Azure eszköztárán válassza az értesítések ikont, amely megnyitja az értesítések panelt.

> [!NOTE]
> Ha az üzembe helyezés sikertelen, vagy törli az ISE-t, az Azure akár egy órát is igénybe vehet az alhálózatok felszabadítása előtt. Ez azt jelenti, hogy előfordulhat, hogy várnia kell, mielőtt újra felhasználja ezeket az alhálózatokat egy másik ISE-ben.
>
> Ha törli a virtuális hálózatot, az Azure általában akár két órával az alhálózatok felszabadítása előtt is eltarthat, de ez a művelet hosszabb időt is igénybe vehet. 
> A virtuális hálózatok törlésekor győződjön meg arról, hogy egyetlen erőforrás sincs még csatlakoztatva. 
> Lásd: [virtuális hálózat törlése](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Kérelem fejléce

A kérelem fejlécében adja meg a következő tulajdonságokat:

* `Content-type`: a tulajdonság értékét állítsa `application/json`re.

* `Authorization`: állítsa a tulajdonság értékét azon ügyfél tulajdonosi jogkivonatára, aki hozzáfér a használni kívánt Azure-előfizetéshez vagy erőforráscsoporthoz.

### <a name="request-body-syntax"></a>Kérelem törzsének szintaxisa

Itt látható a kérelem törzsének szintaxisa, amely leírja az ISE létrehozásakor használandó tulajdonságokat:

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

### <a name="request-body-example"></a>Példa a kérelem szövegtörzsére

A példaként szolgáló kérelem törzse a következő minta értékeket jeleníti meg:

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

## <a name="next-steps"></a>Következő lépések

* [Erőforrások hozzáadása az integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integrációs szolgáltatási környezetek kezelése](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

