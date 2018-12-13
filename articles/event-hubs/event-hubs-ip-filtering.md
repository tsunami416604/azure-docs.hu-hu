---
title: Az Azure Event Hubs tűzfalszabályok |} A Microsoft Docs
description: Tűzfal-szabályok használatával engedélyezi az adott IP-címekről érkező kapcsolatok az Azure Event Hubsba.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: 707290d7bf453ca71dd3c5cf8b39c917b3a1c479
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268274"
---
# <a name="use-firewall-rules"></a>Tűzfalszabályok

A forgatókönyvek, amelyben az Azure Event Hubs elérhetőnek kell lenniük csak bizonyos ismert helyek esetén tűzfalszabályok engedélyezése, hogy a forgalom származik az adott IPv4-címek elfogadására vonatkozó szabályok konfigurálása. Például ezek a címek lehet a vállalati hálózati Címfordítás az átjáró.

## <a name="when-to-use"></a>A következő esetekben használja

Az Event Hubs-névtér beállítása szeretne minden más elutasítás és csak a megadott IP-címtartományt a forgalom úgy, hogy azt kell kapnia, akkor használhatja egy *tűzfalszabály* blokkolja az Event Hub-végpontok más IP-címek. Az Event Hubs használata esetén például [Azure Express Route] [ express-route] a helyszíni infrastruktúra magánkapcsolatokat hozhat létre.

## <a name="how-filter-rules-are-applied"></a>Szűrési szabályok alkalmazása

Az IP-szűrési szabályok érvényesek az Event Hubs-névterek szintjén. Ezért a szabályok érvényesek lesznek az összes kapcsolat bármely támogatott protokollt használó ügyfelektől.

Minden olyan IP-címről, amely nem felel meg egy engedélyezett IP-szabályt az Event Hubs névtér van elutasítottuk, mert a csatlakozási kísérlet nem engedélyezett. A válasz nem említi az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a **IP-szűrő** rács a portálon az Event Hubs, az üres. Ez az alapértelmezett beállítás, az azt jelenti, hogy az eseményközpont IP-címeket érkező kapcsolatokat fogad-e. Ez az alapértelmezett beállítás megegyezik egy szabályt, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály értékelése

IP-szűrési szabályok a rendszer sorrendben alkalmazza, és az első szabály, amely az IP-cím megegyezik a elfogadása vagy elutasítása műveletet határozza meg.

>[!WARNING]
> Tűzfalak végrehajtási megakadályozhatja az egyéb Azure-szolgáltatásokhoz az Event hubs szolgáltatással való interakcióhoz.
>
> Megbízható Microsoft-szolgáltatások nem támogatottak. Ha IP-szűrés (tűzfal) vannak megvalósítva, és fog hamarosan közzétesszük.
>
> Gyakori Azure forgatókönyvek nem működnek az IP-szűrés (vegye figyelembe, hogy a lista **nem** teljes körű) –
> - Azure Monitor
> - Azure Stream Analytics
> - Az Azure Event Grid-integráció
> - Az Azure IoT Hub-útvonalak
> - Az Azure IoT Device Explorer
> - Azure Data Explorer
>
> Az alábbi Microsoft szolgáltatásokra van szükség a virtuális hálózaton
> - Azure Web Apps
> - Azure Functions

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Egy tűzfalszabály létrehozása az Azure Resource Manager-sablonok

> [!IMPORTANT]
> Tűzfal-szabályok használata támogatott a **standard** és **dedikált** az Event hubs szinten. Az alapszintű díjcsomagban nem támogatott.

A következő Resource Manager-sablon lehetővé teszi, hogy egy IP-szűrési szabály hozzáadása egy meglévő Event Hubs-névtér.

Sablon paraméterei:

- **ipMask** egyetlen IPv4-cím vagy IP-címeket a CIDR-jelölésrendszerben egy kódblokkot. Például a CIDR jelölésrendszerben 70.37.104.0/24 jelöli 256 IPv4-címek 70.37.104.0 a 70.37.104.255, az a tartomány jelentős előtag bitek számát jelző 24.

> [!NOTE]
> Nincsenek Megtagadás szabályok lehetséges, amíg az Azure Resource Manager-sablon rendelkezik-e beállítva alapértelmezett művelet **"Engedélyezés"** amely nem korlátozza a kapcsolatokat.
> Amikor a virtuális hálózathoz vagy a tűzfalak szabályokat, hogy módosítania kell a ***"defaultAction"***
> 
> forrás:
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
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

A sablon üzembe helyezéséhez kövesse az utasításokat [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>További lépések

Azure virtuális hálózatok és az Event Hubs korlátozási hozzáférés tekintse meg a következő hivatkozásra:

- [Virtuális hálózati Szolgáltatásvégpontok az Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md