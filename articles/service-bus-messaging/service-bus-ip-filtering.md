---
title: Az Azure Service Bus tűzfalszabályok |} A Microsoft Docs
description: Hogyan használható a tűzfalszabályok az Azure Service Bus adott IP-címekről érkező kapcsolatok engedélyezéséhez.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: a8d29e7cae20c37adfeccaef01e1625b6ab3e0d0
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852677"
---
# <a name="use-firewall-rules"></a>Tűzfalszabályok

Forgatókönyvek, amelyben az Azure Service Bus csak érhető el az egyes jól ismert helyről a tűzfalszabályok engedélyezése, hogy a forgalom származik az adott IPv4-címek elfogadására vonatkozó szabályok konfigurálása. Például ezek a címek lehet a vállalati hálózati Címfordítás az átjáró.

## <a name="when-to-use"></a>A következő esetekben használja

Ha a telepítő a Service Bus úgy, hogy kell csak a megadott IP-címek egy forgalom fogadásához, valamint minden más elutasítja, akkor használhat egy *tűzfal* blokkolása a Service Bus-végpontokat más IP-címekről. A Service Bus használata esetén például [Azure Express Route] [ express-route] a helyszíni infrastruktúra magánkapcsolatokat hozhat létre. 

## <a name="how-filter-rules-are-applied"></a>Szűrési szabályok alkalmazása

Az IP-szűrési szabályok érvényesek a Service Bus-névterek szintjén. Ezért a szabályok érvényesek lesznek az összes kapcsolat bármely támogatott protokollt használó ügyfelektől.

Minden olyan IP-címről, amely nem egyezik meg az engedélyezett IP-szabály a Service Bus a névtérben van elutasítottuk, mert a csatlakozási kísérlet nem engedélyezett. A válasz nem említi az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a **IP-szűrő** rács a portálon, a Service Bus az üres. Ez az alapértelmezett beállítás, az azt jelenti, hogy a névtér fogad kapcsolatokat IP-címeket. Ez az alapértelmezett beállítás megegyezik egy szabályt, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály értékelése

IP-szűrési szabályok a rendszer sorrendben alkalmazza, és az első szabály, amely az IP-cím megegyezik a elfogadása vagy elutasítása műveletet határozza meg.

>[!WARNING]
> Tűzfalszabályok végrehajtási tudja megakadályozni más Azure-szolgáltatások Service Bus szolgáltatással való interakcióhoz.
>
> Megbízható Microsoft-szolgáltatások nem támogatottak. Ha IP-szűrés (tűzfalszabályok) vannak megvalósítva, és fog hamarosan közzétesszük.
>
> Gyakori Azure forgatókönyvek nem működnek az IP-szűrés (vegye figyelembe, hogy a lista **nem** teljes körű) –
> - Azure Monitor
> - Azure Stream Analytics
> - Az Azure Event Grid-integráció
> - Azure IoT Hub Routes
> - Azure IoT Device Explorer
> - Azure Data Explorer
>
> Az alábbi Microsoft szolgáltatásokra van szükség a virtuális hálózaton
> - Azure App Service
> - Azure Functions

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonokkal a virtuális hálózati és tűzfalbeállításokat szabály létrehozása

> [!IMPORTANT]
> Csak a virtuális hálózatok támogatottak a **prémium** a Service Bus szintjét.

A következő Resource Manager-sablon lehetővé teszi, hogy egy virtuális hálózati szabályt ad hozzá egy meglévő Service Bus-névteret.

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
          "name": "Standard",
          "tier": "Standard"
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

A sablon üzembe helyezéséhez kövesse az utasításokat [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>További lépések

Omezující vlastnost hozzáférés a Service Bus az Azure virtuális hálózatokhoz lásd a következő hivatkozást:

- [Service Bus számára, virtuális hálózati Szolgáltatásvégpontok][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services