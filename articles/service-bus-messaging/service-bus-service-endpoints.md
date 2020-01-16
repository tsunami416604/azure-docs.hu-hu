---
title: Virtuális hálózati szolgáltatás végpontjai – Azure Service Bus
description: Ez a cikk azt ismerteti, hogyan adhat hozzá Microsoft. ServiceBus szolgáltatási végpontot egy virtuális hálózathoz.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 212cd96571561362003e7dcbd89efc5d2c54ab48
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980800"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Virtual Network szolgáltatási végpontok használata Azure Service Bus

Service Bus és [Virtual Network (VNet) szolgáltatás-végpontok][vnet-sep] integrációja lehetővé teszi az üzenetkezelési funkciók biztonságos elérését olyan munkaterhelések esetén, mint a virtuális hálózatokhoz kötött virtuális gépek, és a hálózati forgalom elérési útja mindkét végén védett.

Ha úgy konfigurálták, hogy legalább egy virtuális hálózati alhálózat szolgáltatási végponthoz legyen kötve, a megfelelő Service Bus névtér többé nem fogadja el a forgalmat bárhonnan, de engedélyezett virtuális hálózat (ok) ból. A virtuális hálózat szempontjából a Service Bus névtér kötése egy szolgáltatási végponthoz egy elkülönített hálózati alagutat konfigurál a virtuális hálózat alhálózatáról az üzenetküldési szolgáltatáshoz.

Az eredmény az alhálózathoz és a megfelelő Service Bus névtérhez kötött munkaterhelések közötti privát és elkülönített kapcsolat, annak ellenére, hogy az üzenetküldési szolgáltatás végpontjának megfigyelhető hálózati címe egy nyilvános IP-tartományban van.

>[!WARNING]
> A virtuális hálózatok integrálásának megvalósításával megakadályozható, hogy más Azure-szolgáltatások a Service Bus használatával kommunikálnak.
>
> A megbízható Microsoft-szolgáltatások nem támogatottak, ha a virtuális hálózatok implementálva vannak.
>
> Olyan általános Azure-forgatókönyvek, amelyek nem működnek a virtuális hálózatokkal (vegye figyelembe, hogy a lista **nem** teljes) –
> - Azure Stream Analytics
> - Integráció a Azure Event Grid
> - Azure IoT Hub útvonalak
> - Azure IoT Device Explorer
>
> Az alábbi Microsoft-szolgáltatások szükségesek virtuális hálózaton
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> A virtuális hálózatok csak [prémium szintű](service-bus-premium-messaging.md) Service Bus névterek esetén támogatottak.

## <a name="enable-service-endpoints-with-service-bus"></a>Szolgáltatási végpontok engedélyezése Service Bus

Fontos szempont, hogy a VNet szolgáltatásbeli végpontok a Service Bus használatával történő használata esetén ne engedélyezze ezeket a végpontokat olyan alkalmazásokban, amelyek a standard és a prémium szintű Service Bus névtereket keverik. Mivel a standard szint nem támogatja a virtuális hálózatok, a végpont csak a prémium szintű névterek számára van korlátozva.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>A VNet-integráció által engedélyezett speciális biztonsági forgatókönyvek 

A szigorú és compartmentalized biztonságot igénylő megoldások, valamint a virtuális hálózatok alhálózatai biztosítják a compartmentalized szolgáltatások közötti szegmentálást, általánosságban továbbra is szükség van a kommunikációs útvonalakra az ezekben a rekeszekben található szolgáltatások között.

A rekeszek közötti közvetlen IP-útvonal, beleértve a TCP/IP protokollon keresztüli HTTPS-t is, a hálózati réteg biztonsági réseinak kiaknázásának kockázatát hordozza. Az üzenetkezelési szolgáltatások teljes mértékben szigetelt kommunikációs útvonalakat biztosítanak, ahol az üzenetek a felek közötti váltáskor lemezre is írhatók. Az ugyanahhoz a Service Bus-példányhoz kötött két különálló virtuális hálózatban a munkaterhelések hatékonyan és megbízhatóan kommunikálhatnak az üzeneteken keresztül, miközben a hálózat elkülönítési határának megfelelő integritása megmarad.
 
Ez azt jelenti, hogy a biztonsági szempontból érzékeny felhőalapú megoldások nem csupán az Azure piacvezető megbízható és skálázható üzenetkezelési képességeihez férnek hozzá, de mostantól az üzenetküldés használatával kommunikációs útvonalakat hozhatnak létre a biztonságos Megoldási rekeszek között, amelyek a szolgáltatás természeténél fogva biztonságosabb, mint bármely egyenrangú kommunikációs mód, beleértve a HTTPS-t és más TLS-védelemmel ellátott szoftvercsatorna-protokollokat.

## <a name="binding-service-bus-to-virtual-networks"></a>Kötési Service Bus a virtuális hálózatokhoz

A *virtuális hálózati szabályok* a tűzfal biztonsági funkciója, amely azt szabályozza, hogy a Azure Service Bus-kiszolgáló egy adott virtuális hálózati alhálózat kapcsolatait fogadja-e.

A Service Bus névterek virtuális hálózathoz való kötése kétlépéses folyamat. Először létre kell hoznia egy **Virtual Network szolgáltatási végpontot** egy Virtual Network alhálózaton, és engedélyeznie kell azt a "Microsoft. ServiceBus" számára a [szolgáltatási végpont áttekintése][vnet-sep]című részben leírtak szerint. A szolgáltatás végpontjának hozzáadása után a Service Bus névteret egy *virtuális hálózati szabállyal*kell kötnie.

A virtuális hálózati szabály a Service Bus névtér egy virtuális hálózati alhálózattal való társítása. Amíg a szabály létezik, az alhálózathoz kötött összes munkaterhelés hozzáférést kap a Service Bus névtérhez. Service Bus maga soha nem hoz létre kimenő kapcsolatokat, nem kell elérnie a hozzáférést, ezért a szabály engedélyezésével soha nem kapnak hozzáférést az alhálózathoz.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Virtuális hálózati szabály létrehozása Azure Resource Manager sablonokkal

A következő Resource Manager-sablon lehetővé teszi egy virtuális hálózati szabály hozzáadását egy meglévő Service Bus névtérhez.

Sablon paraméterei:

* **namespacename tulajdonság**: Service Bus névtér.
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

Sablon:

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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
                    "service": "Microsoft.ServiceBus"
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
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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
- [Azure Service Bus IP-szűrés][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
