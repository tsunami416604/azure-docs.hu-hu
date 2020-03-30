---
title: Azure Event Hubs tűzfalszabályok | Microsoft dokumentumok
description: A tűzfalszabályok használatával engedélyezheti a kapcsolatokat adott IP-címekről az Azure Event Hubs-hoz.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: fb11d1bdcf8145d4e78285833789b41c92b0ce4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064882"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>IP-tűzfalszabályok konfigurálása Egy Azure Event Hubs névtérhez
Alapértelmezés szerint az Event Hubs névterek elérhetők az internetről, amíg a kérelem érvényes hitelesítéssel és engedélyezéssel érkezik. Az IP-tűzfal lal tovább korlátozhatja a [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelölésben lévő IPv4-címek vagy IPv4-címtartományok készletére.

Ez a funkció olyan esetekben hasznos, amelyekben az Azure Event Hubs csak bizonyos jól ismert helyekről érhető el. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekről származó forgalom fogadására. Ha például az Event Hubs szolgáltatást az [Azure Express Route-szal][express-route]használja, létrehozhat egy **tűzfalszabályt,** amely csak a helyszíni infrastruktúra IP-címeiből engedélyezi a forgalmat. 

## <a name="ip-firewall-rules"></a>IP tűzfalszabályai
Az IP-tűzfal szabályok az Event Hubs névtér szintjén kerülnek alkalmazásra. Ezért a szabályok a támogatott protokollt használó ügyfelek összes kapcsolatára vonatkoznak. Az Eseményközpontok névterében engedélyezett IP-szabálynak nem megfelelő IP-címről érkező csatlakozási kísérlet jogosulatlanként elutasításra kerül. A válasz nem említi az IP-szabályt. Az IP-szűrőszabályok sorrendben kerülnek alkalmazásra, és az IP-címnek megfelelő első szabály határozza meg az elfogadási vagy elutasítási műveletet.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz bemutatja, hogyan használhatja az Azure Portalon az Event Hubs névtér IP-tűzfalszabályainak létrehozásához. 

1. Nyissa meg az **Event Hubs névterét** az [Azure Portalon.](https://portal.azure.com)
2. A bal oldali menüben válassza a **Hálózat lehetőséget.** Ha a **Minden hálózat** lehetőséget választja, az eseményközpont bármely IP-címről fogadja a kapcsolatokat. Ez a beállítás megegyezik a 0.0.0.0/0 IP-címtartományt elfogadó szabállyal. 

    ![Tűzfal – Minden hálózat beállítás kiválasztva](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Ha korlátozni szeretné a hozzáférést bizonyos hálózatokhoz és IP-címekhez, jelölje be a **Kiválasztott hálózatok jelölőnégyzetet.** A **Tűzfal** szakaszban hajtsa végre az alábbi lépéseket:
    1. Válassza **az Ügyfél IP-címének hozzáadása** lehetőséget, ha az aktuális ügyfél IP-címét szeretné elérni a névtérhez. 
    2. A **címtartományhoz**adjon meg egy adott IPv4-címet vagy egy IPv4-címet CIDR-jelöléssel. 
    3. Adja meg, hogy engedélyezi-e a **megbízható Microsoft-szolgáltatásoknak a tűzfal megkerülését.** 

        ![Tűzfal – Minden hálózat beállítás kiválasztva](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. A beállítások mentéséhez válassza a **Mentés** gombot az eszköztáron. Várjon néhány percet, amíg a visszaigazolás megjelenik a portálértesítésein.


## <a name="use-resource-manager-template"></a>Resource Manager-sablon használata

> [!IMPORTANT]
> A tűzfalszabályokat az Event Hubs **szabványos** és **dedikált** szintjei támogatják. Az alapszintű alapszintű nem támogatott.

A következő Erőforrás-kezelő sablon lehetővé teszi, hogy ip-szűrőszabályt adjon egy meglévő Eseményközpont-névtérhez.

Sablon paraméterei:

- **Az ipMask** egyetlen IPv4-cím vagy IP-címek blokkja CIDR jelöléssel. A CIDR 70.37.104.0/24 jelölésében például a 256 IPv4-cím 70.37.104.0 és 70.37.104.255 között, 24 pedig a tartomány jelentős előtagbiteinek számát jelzi.

> [!NOTE]
> Bár nincsenek megtagadási szabályok lehetséges, az Azure Resource Manager-sablon az alapértelmezett művelet beállítása **"Engedélyezés",** amely nem korlátozza a kapcsolatokat.
> A virtuális hálózat vagy a tűzfalak szabályainak megalkotásakor meg kell változtatnunk a ***"defaultAction"***
> 
> honnan
> ```json
> "defaultAction": "Allow"
> ```
> erre:
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

A sablon üzembe helyezéséhez kövesse az [Azure Resource Manager][lnk-deploy]utasításait.

## <a name="next-steps"></a>További lépések

Az Event Hubs azure-beli virtuális hálózatokhoz való hozzáférés korlátozását az alábbi hivatkozásban veheti el:

- [Virtuális hálózati szolgáltatás végpontjai az eseményközpontokhoz][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
