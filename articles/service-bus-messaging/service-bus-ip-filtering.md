---
title: Azure Service Bus tűzfalszabályok | Microsoft Docs
description: A tűzfalszabályok használata az adott IP-címekről Azure Service Bus való csatlakozás engedélyezéséhez.
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
ms.openlocfilehash: 59afdb0e273511f3d8255a9c859b86f93e0b7269
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462409"
---
# <a name="azure-service-bus---use-firewall-rules"></a>Azure Service Bus – tűzfalszabályok használata

Olyan helyzetekben, amikor a Azure Service Bus csak bizonyos jól ismert helyekről érhető el, a tűzfalszabályok lehetővé teszik az adott IPv4-címekből származó forgalom fogadására vonatkozó szabályok konfigurálását. Ezek a címek lehetnek például a vállalati NAT-átjárók.

## <a name="when-to-use"></a>When to use

Ha úgy szeretné beállítani Service Bus, hogy csak a megadott IP-címtartományból érkező forgalmat kapja, és minden mást visszautasítson, akkor a *tűzfal* használatával blokkolhatja Service Bus végpontokat más IP-címekről. Például Service Bust használ az [Azure Express Route][express-route] használatával a helyszíni infrastruktúrához való magánhálózati kapcsolatok létrehozásához. 

## <a name="how-filter-rules-are-applied"></a>A szűrési szabályok alkalmazása

Az IP-szűrési szabályok a Service Bus névtér szintjén lesznek alkalmazva. Ezért a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával.

Olyan IP-címről érkező csatlakozási kísérletek, amely nem felel meg a Service Bus névtérben lévő engedélyezett IP-szabálynak, a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a Service Bus-portálon található **IP-szűrő** rács üres. Ez az alapértelmezett beállítás azt jelenti, hogy a névtér elfogad minden IP-címet. Ez az alapértelmezett beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály kiértékelése

Az IP-szűrési szabályok a sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

>[!WARNING]
> A tűzfalszabályok bevezetésével megakadályozhatja, hogy más Azure-szolgáltatások a Service Bus használatával kommunikálnak.
>
> A megbízható Microsoft-szolgáltatások használata nem támogatott, ha az IP-szűrés (tűzfalszabályok) be van vezetve, és hamarosan elérhetővé válik.
>
> Általános Azure-forgatókönyvek, amelyek nem működnek az IP-szűréssel (vegye figyelembe, hogy a lista **nem** teljes) –
> - Azure Stream Analytics
> - Integráció a Azure Event Grid
> - Azure IoT Hub útvonalak
> - Azure IoT Device Explorer
>
> Az alábbi Microsoft-szolgáltatások szükségesek virtuális hálózaton
> - Azure App Service
> - Azure Functions

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Virtuális hálózati és tűzfalszabály létrehozása Azure Resource Manager-sablonokkal

> [!IMPORTANT]
> A tűzfalak és a virtuális hálózatok csak a **prémium** szintű Service Bus támogatottak.

A következő Resource Manager-sablon lehetővé teszi egy virtuális hálózati szabály hozzáadását egy meglévő Service Bus névtérhez.

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
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

A sablon üzembe helyezéséhez kövesse az [Azure Resource Manager][lnk-deploy]utasításait.

## <a name="next-steps"></a>Következő lépések

Az Azure-beli virtuális hálózatokhoz való Service Bus hozzáférésének korlátozásához tekintse meg a következő hivatkozást:

- [Service Bus Virtual Network szolgáltatási végpontok][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
