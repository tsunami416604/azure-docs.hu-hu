---
title: Virtuális hálózati Szolgáltatásvégpontok – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk információt nyújt az adda hátralékának szolgáltatás végpontnak egy virtuális hálózathoz.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 2ad525ee0e10064d4d606dc1f899ef813fe92ab5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273501"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Virtuális hálózati Szolgáltatásvégpontok használata az Azure Event hubs szolgáltatással

Az Event hubs-integráció [virtuális hálózat (VNet) Szolgáltatásvégpontok] [ vnet-sep] lehetővé teszi, hogy biztonságos hozzáférést üzenetküldési funkciókat biztosít a számítási feladatok, például a virtuális gépek, virtuális vannak kötve hálózatok mindkét végén védeni kívánt hálózati forgalom elérési útját.

Legalább egy virtuális hálózati alhálózat szolgáltatásvégpont kell kötni konfigurálása után az adott Event Hubs-névtér már nem fogadja a forgalmat bárhonnan, de engedélyezett a virtuális hálózatok alhálózatokat. A virtuális hálózati szempontból a kötés Event Hubs-névtér egy végpontot az üzenetküldő szolgáltatás a virtuális hálózati alhálózatról egy elkülönített hálózati alagút állítja be.

Ez a privát és elkülönített kapcsolat, az az alhálózat és a megfelelő Event Hubs-névtér értéket a megfigyelhető hálózati cím az az üzenetkezelési szolgáltatás végpontja egy nyilvános IP-címtartomány kötött számítási feladatok között.

>[!WARNING]
> Virtuális hálózatok integráció megvalósítása megakadályozhatja az egyéb Azure-szolgáltatásokhoz az Event hubs szolgáltatással való interakcióhoz.
>
> A megbízható Microsoft-szolgáltatások nem támogatottak a virtuális hálózatok vannak megvalósítva, és a rendszer hamarosan közzétesszük.
>
> Gyakori Azure forgatókönyvek, amelyek nem használhatók a virtuális hálózatok (vegye figyelembe, hogy a lista **nem** teljes körű) –
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

> [!IMPORTANT]
> A virtuális hálózatok támogatottak **standard** és **dedikált** az Event hubs szinten. Az alapszintű díjcsomagban nem támogatott.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet-integráció által engedélyezett speciális biztonsági forgatókönyvek 

Megoldásokat igénylő szoros és elméleti biztonsági, és hogy a virtuális hálózat alhálózataiban, adja meg a szegmentálási a compartmentalized szolgáltatások közötti továbbra is általánosan kell ezeket a veremből a hozzá tartozó szolgáltatások közötti kommunikációs útvonalat.

A környezetben, beleértve a szerepkör végrehajtsa a HTTPS TCP/IP, felett közötti bármely azonnali IP útvonal a hálózati réteg biztonsági rések kiaknázását kockázatát hordozza az fel. Üzenetkezelési szolgáltatások teljesen szigetelt kommunikációs útvonalat, ahol üzeneteket is írja a lemezre, azok tarifacsomagokból a felek biztosítják. Két különböző virtuális hálózatok mindkét kötött ugyanarra az Event Hubs-példányra a számítási feladatok kommunikálhatnak hatékonyan és megbízhatóan keresztül üzenetek, míg a megfelelő hálózati elkülönítési határ integritása.
 
Azt jelenti, hogy a bizalmas felhőmegoldások csak nem férnek hozzá az Azure-szolgáltatás piacvezető megbízható és méretezhető aszinkron üzenetkezelési képességeket, de most használhatnak üzenetkezelés a biztonságos megoldás közötti kommunikációs útvonalat hozzon létre biztonsági kocsiban olyan eleve biztonságosabb, mint bármely társ-társ-kommunikációs móddal, többek között a HTTPS és az egyéb TLS biztonságos szoftvercsatorna-protokollok kezelhető.

## <a name="bind-event-hubs-to-virtual-networks"></a>Az Event Hubs kötést létrehozni a virtuális hálózatokhoz

*A virtuális hálózati szabályok* van a tűzfal biztonsági szolgáltatás, amely szabályozza, hogy az Azure Event Hubs-névtér egy adott virtuális hálózati alhálózat érkező kapcsolatokat fogad-e.

Event Hubs-névtér kötése egy virtuális hálózatot két lépésből áll. Először hozzon létre egy **virtuális hálózati szolgáltatásvégpont** egy virtuális hálózat alhálózatán, és engedélyezze a Microsoft.eventhub"", azt mutatjuk be a [szolgáltatási végpont áttekintő] [ vnet-sep]. Miután hozzáadta a szolgáltatásvégpont, azt a kötést létrehozni az Event Hubs-névtér egy *virtuális hálózati szabályt*.

A virtuális hálózati szabály az Event Hubs-névtér egy virtuális hálózat alhálózatához van társítva. A szabály létezik, míg az alhálózat kötött összes számítási feladatokhoz, amelyekhez hozzáférést az Event Hubs-névtér. Az Event Hubs maga soha nem kimenő kapcsolatot hoz létre, nem kell hozzáférést, és ezért soha nem kap hozzáférést az alhálózat szerint ez a szabály engedélyezése.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Hozzon létre egy virtuális hálózati szabályt az Azure Resource Manager-sablonok

A következő Resource Manager-sablon lehetővé teszi, hogy egy virtuális hálózati szabályt ad hozzá egy meglévő Event Hubs-névtér.

Sablon paraméterei:

* **namespaceName**: Event Hubs-névtér.
* **vnetRuleName**: A létrehozandó virtuális hálózati szabály neve.
* **virtualNetworkingSubnetId**: A virtuális hálózat alhálózatához; teljesen minősített erőforrás-kezelő elérési útja Ha például `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` az alapértelmezett alhálózat egy virtuális hálózat.

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

A sablon üzembe helyezéséhez kövesse az utasításokat [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>További lépések

Virtuális hálózatokkal kapcsolatos további információkért lásd az alábbi hivatkozásokat:

- [Az Azure virtuális hálózati Szolgáltatásvégpontok][vnet-sep]
- [Az Azure Event Hubs IP-szűrés][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md