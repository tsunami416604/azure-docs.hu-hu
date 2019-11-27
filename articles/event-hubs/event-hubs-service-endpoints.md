---
title: Virtuális hálózati Szolgáltatásvégpontok – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan adhat hozzá Microsoft. EventHub szolgáltatási végpontot egy virtuális hálózathoz.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 5b02b79980ebe5ea91a1cf16d3ea453ebef3bf08
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279783"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Virtuális hálózati Szolgáltatásvégpontok használata az Azure Event hubs szolgáltatással

Event Hubs és [Virtual Network (VNet) szolgáltatás-végpontok][vnet-sep] integrációja lehetővé teszi az üzenetkezelési funkciók biztonságos elérését olyan munkaterhelések esetén, mint a virtuális hálózatokhoz kötött virtuális gépek, valamint a hálózati forgalom elérési útja végződik.

Ha úgy van konfigurálva, hogy legalább egy virtuális hálózati alhálózat szolgáltatási végpontra legyen kötve, a megfelelő Event Hubs névtér többé nem fogadja el a forgalmat bárhonnan, de a virtuális hálózatokban engedélyezett alhálózatokat. A virtuális hálózati szempontból a kötés Event Hubs-névtér egy végpontot az üzenetküldő szolgáltatás a virtuális hálózati alhálózatról egy elkülönített hálózati alagút állítja be. 

Ez a privát és elkülönített kapcsolat, az az alhálózat és a megfelelő Event Hubs-névtér értéket a megfigyelhető hálózati cím az az üzenetkezelési szolgáltatás végpontja egy nyilvános IP-címtartomány kötött számítási feladatok között. Ez a viselkedés kivételt jelent. A szolgáltatás végpontjának engedélyezése alapértelmezés szerint engedélyezi az denyall szabályt a virtuális hálózathoz társított IP-tűzfalon. Adott IP-címeket adhat hozzá az IP-tűzfalon az Event hub nyilvános végponthoz való hozzáférés engedélyezéséhez. 


>[!WARNING]
> A virtuális hálózatok integrálásának megvalósításával megakadályozható, hogy más Azure-szolgáltatások a Event Hubs használatával kommunikálnak.
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
> - Azure Web Apps
> - Azure Functions

> [!IMPORTANT]
> A virtuális hálózatok a **standard** és a **dedikált** Event Hubsban támogatottak. Az alapszintű díjcsomagban nem támogatott.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet-integráció által engedélyezett speciális biztonsági forgatókönyvek 

Azok a megoldások, amelyek feszes és compartmentalized biztonságot igényelnek, és ahol a virtuális hálózati alhálózatok biztosítják a compartmentalized szolgáltatások közötti szegmentálást, továbbra is szükség van kommunikációs útvonalakra az ezekben a rekeszekben található szolgáltatások között.

A környezetben, beleértve a szerepkör végrehajtsa a HTTPS TCP/IP, felett közötti bármely azonnali IP útvonal a hálózati réteg biztonsági rések kiaknázását kockázatát hordozza az fel. Üzenetkezelési szolgáltatások teljesen szigetelt kommunikációs útvonalat, ahol üzeneteket is írja a lemezre, azok tarifacsomagokból a felek biztosítják. Két különböző virtuális hálózatok mindkét kötött ugyanarra az Event Hubs-példányra a számítási feladatok kommunikálhatnak hatékonyan és megbízhatóan keresztül üzenetek, míg a megfelelő hálózati elkülönítési határ integritása.
 
Azt jelenti, hogy a bizalmas felhőmegoldások csak nem férnek hozzá az Azure-szolgáltatás piacvezető megbízható és méretezhető aszinkron üzenetkezelési képességeket, de most használhatnak üzenetkezelés a biztonságos megoldás közötti kommunikációs útvonalat hozzon létre biztonsági kocsiban olyan eleve biztonságosabb, mint bármely társ-társ-kommunikációs móddal, többek között a HTTPS és az egyéb TLS biztonságos szoftvercsatorna-protokollok kezelhető.

## <a name="bind-event-hubs-to-virtual-networks"></a>Az Event Hubs kötést létrehozni a virtuális hálózatokhoz

A *virtuális hálózati szabályok* a tűzfal biztonsági funkciója, amely azt szabályozza, hogy az Azure Event Hubs-névtér fogadjon-e kapcsolatokat egy adott virtuális hálózati alhálózatból.

Event Hubs-névtér kötése egy virtuális hálózatot két lépésből áll. Először létre kell hoznia egy **Virtual Network szolgáltatási végpontot** egy Virtual Network alhálózaton, és engedélyeznie kell azt a "Microsoft. EventHub" számára a [szolgáltatási végpont áttekintése][vnet-sep]című részben leírtak szerint. A szolgáltatás végpontjának hozzáadása után a Event Hubs névteret egy *virtuális hálózati szabállyal*kell kötnie.

A virtuális hálózati szabály a Event Hubs névtér egy virtuális hálózati alhálózattal való társítása. A szabály létezik, míg az alhálózat kötött összes számítási feladatokhoz, amelyekhez hozzáférést az Event Hubs-névtér. Az Event Hubs maga soha nem kimenő kapcsolatot hoz létre, nem kell hozzáférést, és ezért soha nem kap hozzáférést az alhálózat szerint ez a szabály engedélyezése.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Hozzon létre egy virtuális hálózati szabályt az Azure Resource Manager-sablonok

A következő Resource Manager-sablon lehetővé teszi, hogy egy virtuális hálózati szabályt ad hozzá egy meglévő Event Hubs-névtér.

Sablon paraméterei:

* **namespacename tulajdonság**: Event Hubs névtér.
* **vnetRuleName**: a létrehozandó Virtual Network szabály neve.
* **virtualNetworkingSubnetId**: a virtuális hálózati alhálózat teljes erőforrás-kezelő útvonala; például `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` egy virtuális hálózat alapértelmezett alhálózatához.

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

## <a name="next-steps"></a>További lépések

Virtuális hálózatokkal kapcsolatos további információkért lásd az alábbi hivatkozásokat:

- [Azure Virtual Network szolgáltatásbeli végpontok][vnet-sep]
- [Azure Event Hubs IP-szűrés][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
