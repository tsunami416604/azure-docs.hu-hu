---
title: Azure Event Hubs tűzfalszabályok | Microsoft Docs
description: A tűzfalszabályok használatával engedélyezheti az adott IP-címekről az Azure Event Hubs való kapcsolódást.
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
ms.openlocfilehash: a988fbb089bd94456e0b91b377574ab27a67617f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437197"
---
# <a name="azure-event-hubs---use-firewall-rules"></a>Azure Event Hubs – tűzfalszabályok használata

Azokban az esetekben, amelyekben az Azure Event Hubs csak bizonyos jól ismert helyekről elérhető, a tűzfalszabályok lehetővé teszik az adott IPv4-címekről érkező forgalom fogadására vonatkozó szabályok konfigurálását. Ezek a címek lehetnek például a vállalati NAT-átjárók.

## <a name="when-to-use"></a>When to use

Ha úgy szeretné beállítani a Event Hubs névteret, hogy az csak a megadott IP-címtartományból érkező adatforgalmat fogadja, és minden mást visszautasítson, akkor kihasználhat egy *tűzfalszabályet* az Event hub-végpontok más IP-címekről való letiltásához. Ha például az [Azure Express Route][express-route]Event Hubst használja, létrehozhat egy *tűzfalszabályet* a helyszíni infrastruktúra IP-címeiről érkező forgalom korlátozására.

## <a name="how-filter-rules-are-applied"></a>A szűrési szabályok alkalmazása

Az IP-szűrési szabályok a Event Hubs névtér szintjén lesznek alkalmazva. Ezért a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával.

Olyan IP-címről érkező csatlakozási kísérletek, amely nem felel meg a Event Hubs névtérben lévő engedélyezett IP-szabálynak, a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a Event Hubs-portálon található **IP-szűrő** rács üres. Ez az alapértelmezett beállítás azt jelenti, hogy az Event hub bármely IP-címről fogad kapcsolatokat. Ez az alapértelmezett beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály kiértékelése

Az IP-szűrési szabályok sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

>[!WARNING]
> A tűzfalak bevezetésével megakadályozhatja, hogy más Azure-szolgáltatások is interakciót Event Hubs.
>
> A megbízható Microsoft-szolgáltatások használata nem támogatott, ha az IP-szűrés (tűzfalak) be van vezetve, és hamarosan elérhetővé válik.
>
> Általános Azure-forgatókönyvek, amelyek nem működnek az IP-szűréssel (vegye figyelembe, hogy a lista **nem** teljes) –
> - Azure Stream Analytics
> - Integráció a Azure Event Grid
> - Azure IoT Hub útvonalak
> - Azure IoT Device Explorer
>
> Az alábbi Microsoft-szolgáltatások szükségesek virtuális hálózaton
> - Azure Web Apps
> - Azure Functions

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Tűzfalszabály létrehozása Azure Resource Manager-sablonokkal

> [!IMPORTANT]
> A tűzfalszabályok a **standard** és a **dedikált** Event Hubs szinten támogatottak. Alapszintű csomag esetén nem támogatott.

A következő Resource Manager-sablon lehetővé teszi az IP-szűrési szabály hozzáadását egy meglévő Event Hubs névtérhez.

Sablon paraméterei:

- a **ipMask** egy IPv4-cím vagy IP-cím CIDR-jelölésű blokkja. A CIDR 70.37.104.0/24 jelölése például 256 a 70.37.104.0 és a 70.37.104.255 közötti IPv4-címeket jelöli, és 24 a tartomány jelentős előtagjának számát jelzi.

> [!NOTE]
> Habár a megtagadási szabályok nem lehetségesek, a Azure Resource Manager sablon az **"engedélyezés"** értékre van állítva, amely nem korlátozza a kapcsolatokat.
> Virtual Network vagy tűzfalakra vonatkozó szabályok végrehajtásakor módosítania kell a ***"defaultAction"***
> 
> forrás
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

A sablon üzembe helyezéséhez kövesse az [Azure Resource Manager][lnk-deploy]utasításait.

## <a name="next-steps"></a>Következő lépések

Az Azure-beli virtuális hálózatokhoz való Event Hubs hozzáférésének korlátozásához tekintse meg a következő hivatkozást:

- [Event Hubs Virtual Network szolgáltatási végpontok][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
