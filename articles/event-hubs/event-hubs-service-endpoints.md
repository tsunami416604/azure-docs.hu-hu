---
title: Virtual Network szolgáltatási végpontok – Azure Event Hubs | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan adhat hozzá Microsoft. EventHub szolgáltatási végpontot egy virtuális hálózathoz.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 2ac89444bde4e2efc918aced9d76c099eb792557
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966014"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Virtual Network szolgáltatási végpontok használata az Azure-Event Hubs

Event Hubs és [Virtual Network (VNet) szolgáltatás-végpontok][vnet-sep] integrációja lehetővé teszi az üzenetkezelési funkciók biztonságos elérését olyan munkaterhelések esetén, mint például a virtuális hálózatokhoz kötött virtuális gépek, és a hálózati forgalom elérési útja mindkét végén védett.

Ha úgy van konfigurálva, hogy legalább egy virtuális hálózati alhálózat szolgáltatási végpontra legyen kötve, a megfelelő Event Hubs névtér többé nem fogadja el a forgalmat bárhonnan, de a virtuális hálózatokban engedélyezett alhálózatokat. A virtuális hálózat szempontjából a Event Hubs névterek egy szolgáltatás-végponthoz kötése egy elkülönített hálózati alagutat állít be a virtuális hálózat alhálózatáról az üzenetküldési szolgáltatásba. 

Az eredmény az alhálózathoz és a megfelelő Event Hubs névtérhez kötött munkaterhelések közötti privát és elkülönített kapcsolat, annak ellenére, hogy az üzenetküldési szolgáltatás végpontjának megfigyelhető hálózati címe egy nyilvános IP-tartományban van. Ez a viselkedés kivételt jelent. A szolgáltatás végpontjának engedélyezése alapértelmezés szerint engedélyezi az denyall szabályt a virtuális hálózathoz társított IP-tűzfalon. Adott IP-címeket adhat hozzá az IP-tűzfalon az Event hub nyilvános végponthoz való hozzáférés engedélyezéséhez. 


>[!WARNING]
> A virtuális hálózatok integrálásának megvalósításával megakadályozható, hogy más Azure-szolgáltatások a Event Hubs használatával kommunikálnak.
>
> A megbízható Microsoft-szolgáltatások nem támogatottak, ha a virtuális hálózatok implementálva vannak.
>
> Olyan általános Azure-forgatókönyvek, amelyek nem működnek a virtuális hálózatokkal (vegye figyelembe, hogy a lista **nem** teljes) –
> - Integráció a Azure Monitorsal. **Más** Azure-szolgáltatásoktól származó diagnosztikai naplók nem továbbíthatók Event Hubsba. Az Azure diagnosztikai naplókat azonban saját maga is engedélyezheti az Event hub-ban. Ez ugyanaz az eset, ha engedélyezve van a tűzfal (IP-szűrés).
> - Azure Stream Analytics
> - Integráció a Azure Event Grid
> - Azure IoT Hub útvonalak
> - Azure IoT Device Explorer
>
> Az alábbi Microsoft-szolgáltatások szükségesek virtuális hálózaton
> - Azure Web Apps
> - Azure Functions

> [!IMPORTANT]
> A virtuális hálózatok a **standard** és a **dedikált** Event Hubsban támogatottak. Alapszintű csomag esetén nem támogatott.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>A VNet-integráció által engedélyezett speciális biztonsági forgatókönyvek 

Azok a megoldások, amelyek feszes és compartmentalized biztonságot igényelnek, és ahol a virtuális hálózati alhálózatok biztosítják a compartmentalized szolgáltatások közötti szegmentálást, továbbra is szükség van kommunikációs útvonalakra az ezekben a rekeszekben található szolgáltatások között.

A rekeszek közötti közvetlen IP-útvonal, beleértve a TCP/IP protokollon keresztüli HTTPS-t is, a hálózati réteg biztonsági réseinak kiaknázásának kockázatát hordozza. Az üzenetkezelési szolgáltatások teljes mértékben szigetelt kommunikációs útvonalakat biztosítanak, ahol az üzenetek a felek közötti váltáskor lemezre is írhatók. Az ugyanahhoz a Event Hubs-példányhoz kötött két különálló virtuális hálózatban a munkaterhelések hatékonyan és megbízhatóan kommunikálhatnak az üzeneteken keresztül, miközben a hálózat elkülönítési határának megfelelő integritása megmarad.
 
Ez azt jelenti, hogy a biztonsági szempontból érzékeny felhőalapú megoldások nem csupán az Azure piacvezető megbízható és skálázható üzenetkezelési képességeihez férnek hozzá, de mostantól az üzenetküldés használatával kommunikációs útvonalakat hozhatnak létre a biztonságos Megoldási rekeszek között, amelyek a szolgáltatás természeténél fogva biztonságosabb, mint bármely egyenrangú kommunikációs mód, beleértve a HTTPS-t és más TLS-védelemmel ellátott szoftvercsatorna-protokollokat.

## <a name="bind-event-hubs-to-virtual-networks"></a>Event Hubs kötése virtuális hálózatokhoz

A *virtuális hálózati szabályok* a tűzfal biztonsági funkciója, amely azt szabályozza, hogy az Azure Event Hubs-névtér fogadjon-e kapcsolatokat egy adott virtuális hálózati alhálózatból.

Egy Event Hubs névtér egy virtuális hálózathoz kötése kétlépéses folyamat. Először létre kell hoznia egy **Virtual Network szolgáltatási végpontot** egy Virtual Network alhálózaton, és engedélyeznie kell azt a "Microsoft. EventHub" számára a [szolgáltatási végpont áttekintése][vnet-sep]című részben leírtak szerint. A szolgáltatás végpontjának hozzáadása után a Event Hubs névteret egy *virtuális hálózati szabállyal*kell kötnie.

A virtuális hálózati szabály a Event Hubs névtér egy virtuális hálózati alhálózattal való társítása. Amíg a szabály létezik, az alhálózathoz kötött összes munkaterhelés hozzáférést kap a Event Hubs névtérhez. Event Hubs maga soha nem hoz létre kimenő kapcsolatokat, nem kell elérnie a hozzáférést, ezért a szabály engedélyezésével soha nem kapnak hozzáférést az alhálózathoz.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Virtuális hálózati szabály létrehozása Azure Resource Manager-sablonokkal

A következő Resource Manager-sablon lehetővé teszi egy virtuális hálózati szabály hozzáadását egy meglévő Event Hubs névtérhez.

Sablon paraméterei:

* **namespacename tulajdonság**: Event Hubs névtér.
* **vnetRuleName**: a létrehozandó Virtual Network szabály neve.
* **virtualNetworkingSubnetId**: a virtuális hálózati alhálózat teljes erőforrás-kezelő útvonala; például `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` egy virtuális hálózat alapértelmezett alhálózatához.

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

A sablon üzembe helyezéséhez kövesse az [Azure Resource Manager][lnk-deploy]utasításait.

## <a name="next-steps"></a>Következő lépések

A virtuális hálózatokkal kapcsolatos további információkért tekintse meg az alábbi hivatkozásokat:

- [Azure Virtual Network szolgáltatásbeli végpontok][vnet-sep]
- [Azure Event Hubs IP-szűrés][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
