---
title: IP-tűzfalszabályok konfigurálása az Azure Service Bus szolgáltatáshoz
description: Tűzfalszabályok használata adott IP-címekről az Azure Service Bushoz való csatlakozás engedélyezéséhez.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: a20882de34cb306b767959e21327180ff284e658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475943"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>IP-tűzfalszabályok konfigurálása az Azure Service Bus szolgáltatáshoz
Alapértelmezés szerint a Service Bus névterek érhetők el az internetről, amíg a kérelem érvényes hitelesítéssel és engedélyezéssel érkezik. Az IP-tűzfal lal tovább korlátozhatja a [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelölésben lévő IPv4-címek vagy IPv4-címtartományok készletére.

Ez a funkció olyan esetekben hasznos, amelyekben az Azure Service Bus csak bizonyos jól ismert helyekről érhető el. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekről származó forgalom fogadására. Ha például a Service Bus szolgáltatást [az Azure Express Route-szal][express-route]használja, létrehozhat egy **tűzfalszabályt,** amely csak a helyszíni infrastruktúra IP-címeiből vagy egy vállalati NAT-átjáró címéből engedélyezi a forgalmat. 

## <a name="ip-firewall-rules"></a>IP tűzfalszabályai
Az IP-tűzfal szabályok a Service Bus névtér szintjén kerülnek alkalmazásra. Ezért a szabályok a támogatott protokollt használó ügyfelek összes kapcsolatára vonatkoznak. A Service Bus névterén engedélyezett IP-szabálynak nem megfelelő IP-címről érkező csatlakozási kísérlet nem engedélyezett. A válasz nem említi az IP-szabályt. Az IP-szűrőszabályok sorrendben kerülnek alkalmazásra, és az IP-címnek megfelelő első szabály határozza meg az elfogadási vagy elutasítási műveletet.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz bemutatja, hogyan használhatja az Azure Portalon egy Service Bus-névtér IP-tűzfalszabályainak létrehozásához. 

1. Nyissa meg a **Service Bus-névteret** az [Azure Portalon.](https://portal.azure.com)
2. A bal oldali menüben válassza a **Hálózat lehetőséget.** Alapértelmezés szerint a **Minden hálózat** beállítás be van jelölve. A Service Bus-névtér bármely IP-címről fogad kapcsolatokat. Ez az alapértelmezett beállítás megegyezik a 0.0.0.0/0 IP-címtartományt elfogadó szabállyal. 

    ![Tűzfal – Minden hálózat beállítás kiválasztva](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Válassza a **Kijelölt hálózatok** lehetőséget a lap tetején. A **Tűzfal** szakaszban hajtsa végre az alábbi lépéseket:
    1. Válassza **az Ügyfél IP-címének hozzáadása** lehetőséget, ha az aktuális ügyfél IP-címét szeretné elérni a névtérhez. 
    2. A **címtartományhoz**adjon meg egy adott IPv4-címet vagy egy IPv4-címet CIDR-jelöléssel. 
    3. Adja meg, hogy engedélyezi-e a **megbízható Microsoft-szolgáltatásoknak a tűzfal megkerülését.** 

        ![Tűzfal – Minden hálózat beállítás kiválasztva](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. A beállítások mentéséhez válassza a **Mentés** gombot az eszköztáron. Várjon néhány percet, amíg a visszaigazolás megjelenik a portálértesítésein.

## <a name="use-resource-manager-template"></a>Resource Manager-sablon használata
Ez a szakasz egy minta Azure Resource Manager sablon, amely létrehoz egy virtuális hálózatot és egy tűzfalszabályt.

> [!IMPORTANT]
> A tűzfalak és a virtuális hálózatok csak a Service Bus **prémium** szintű csomagjában támogatottak.

A következő Erőforrás-kezelő sablon lehetővé teszi egy virtuális hálózati szabály hozzáadását egy meglévő Service Bus-névtérhez.

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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

A Service Bus és az Azure virtuális hálózatok elérésének korlátozását az alábbi hivatkozásban veheti el:

- [Virtuális hálózati szolgáltatás végpontjai a service bushoz][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
