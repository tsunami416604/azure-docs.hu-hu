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
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: 2350586501fae84726aa2aa2438ea676b90c1dbb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279686"
---
# <a name="use-firewall-rules"></a>Tűzfalszabályok használata

Azokban az esetekben, amelyekben az Azure Event Hubs csak bizonyos jól ismert helyekről elérhető, a tűzfalszabályok lehetővé teszik az adott IPv4-címekről érkező forgalom fogadására vonatkozó szabályok konfigurálását. Például ezek a címek lehet a vállalati hálózati Címfordítás az átjáró.

## <a name="when-to-use"></a>A következő esetekben használja

Ha úgy szeretné beállítani a Event Hubs névteret, hogy az csak a megadott IP-címtartományból érkező adatforgalmat fogadja, és minden mást visszautasítson, akkor kihasználhat egy *tűzfalszabályet* az Event hub-végpontok más IP-címekről való letiltásához. Ha például az [Azure Express Route][express-route]Event Hubst használja, létrehozhat egy *tűzfalszabályet* a helyszíni infrastruktúra IP-címeiről érkező forgalom korlátozására.

## <a name="how-filter-rules-are-applied"></a>Szűrési szabályok alkalmazása

Az IP-szűrési szabályok érvényesek az Event Hubs-névterek szintjén. Ezért a szabályok érvényesek lesznek az összes kapcsolat bármely támogatott protokollt használó ügyfelektől.

Olyan IP-címről érkező csatlakozási kísérletek, amely nem felel meg a Event Hubs névtérben lévő engedélyezett IP-szabálynak, a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a Event Hubs-portálon található **IP-szűrő** rács üres. Ez az alapértelmezett beállítás, az azt jelenti, hogy az eseményközpont IP-címeket érkező kapcsolatokat fogad-e. Ez az alapértelmezett beállítás megegyezik egy szabályt, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály értékelése

IP-szűrési szabályok a rendszer sorrendben alkalmazza, és az első szabály, amely az IP-cím megegyezik a elfogadása vagy elutasítása műveletet határozza meg.

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
> A tűzfalszabályok a **standard** és a **dedikált** Event Hubs szinten támogatottak. Az alapszintű díjcsomagban nem támogatott.

A következő Resource Manager-sablon lehetővé teszi, hogy egy IP-szűrési szabály hozzáadása egy meglévő Event Hubs-névtér.

Sablon paraméterei:

- a **ipMask** egy IPv4-cím vagy IP-cím CIDR-jelölésű blokkja. Például a CIDR jelölésrendszerben 70.37.104.0/24 jelöli 256 IPv4-címek 70.37.104.0 a 70.37.104.255, az a tartomány jelentős előtag bitek számát jelző 24.

> [!NOTE]
> Habár a megtagadási szabályok nem lehetségesek, a Azure Resource Manager sablon az **"engedélyezés"** értékre van állítva, amely nem korlátozza a kapcsolatokat.
> Virtual Network vagy tűzfalakra vonatkozó szabályok végrehajtásakor módosítania kell a ***"defaultAction"***
> 
> from
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

## <a name="next-steps"></a>További lépések

Azure virtuális hálózatok és az Event Hubs korlátozási hozzáférés tekintse meg a következő hivatkozásra:

- [Event Hubs Virtual Network szolgáltatási végpontok][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
