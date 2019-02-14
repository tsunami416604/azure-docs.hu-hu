---
title: Virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure Service Bus |} A Microsoft Docs
description: A Microsoft.ServiceBus szolgáltatás végpont hozzáadása egy virtuális hálózatot.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: c467c963a558f84a0a08887d3777dccf7cd8554d
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234067"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Virtuális hálózati Szolgáltatásvégpontok használata az Azure Service busszal

A Service Bus-integráció [virtuális hálózat (VNet) Szolgáltatásvégpontok] [ vnet-sep] lehetővé teszi a biztonságos hozzáférést üzenetkezelési képességeket, például a virtuális gépek, virtuális hálózatok vannak kötve , a hálózati forgalom elérési út védeni kívánt mindkét végén.

Legalább egy virtuális hálózati alhálózat szolgáltatásvégpont kell kötni konfigurálása után a megfelelő Service Bus-névtér már nem fogadják el a forgalmat bárhonnan, de engedélyezett a virtuális hálózat. A virtuális hálózati szempontból a Service Bus-névtér kötése egy végpontot az üzenetküldő szolgáltatás a virtuális hálózati alhálózatról egy elkülönített hálózati alagút állítja be.

Ez a privát és elkülönített kapcsolat, az az alhálózat és a megfelelő Service Bus-névtér értéket a megfigyelhető hálózati cím az az üzenetkezelési szolgáltatás végpontja egy nyilvános IP-címtartomány kötött számítási feladatok között.

>[!WARNING]
> Virtuális hálózatok integráció megvalósítása megakadályozhatja az egyéb Azure-szolgáltatások Service Bus szolgáltatással való interakcióhoz.
>
> A megbízható Microsoft-szolgáltatások nem támogatottak, amikor a virtuális hálózatok vannak megvalósítva.
>
> Gyakori Azure forgatókönyvek, amelyek nem használhatók a virtuális hálózatok (vegye figyelembe, hogy a lista **nem** teljes körű) –
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

> [!IMPORTANT]
> Virtuális hálózatok csak a támogatott [prémium szintű](service-bus-premium-messaging.md) Service Bus-névterek.

## <a name="enable-service-endpoints-with-service-bus"></a>Engedélyezze a szolgáltatásvégpontokat a Service busszal

A Service Bus virtuális hálózati Szolgáltatásvégpontok használata esetén fontos szempont az, hogy ne engedélyezze ezeket a végpontokat az alkalmazásokat, amelyek vegyesen Standard és prémium szintű Service Bus-névterek. Standard szintű csomag nem támogatja a virtuális hálózatok, mert a végpont korlátozódik, csak a prémium szintű névteret. A virtuális hálózatok közötti forgalom a standard szintű névtér blokkolja.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet-integráció által engedélyezett speciális biztonsági forgatókönyvek 

Megoldásokat igénylő szoros és elméleti biztonsági, és hogy a virtuális hálózat alhálózataiban, adja meg a szegmentálási a compartmentalized szolgáltatások közötti továbbra is általánosan kell ezeket a veremből a hozzá tartozó szolgáltatások közötti kommunikációs útvonalat.

A környezetben, beleértve a szerepkör végrehajtsa a HTTPS TCP/IP, felett közötti bármely azonnali IP útvonal a hálózati réteg biztonsági rések kiaknázását kockázatát hordozza az fel. Üzenetkezelési szolgáltatások teljesen szigetelt kommunikációs útvonalat, ahol üzeneteket is írja a lemezre, azok tarifacsomagokból a felek biztosítják. Számítási feladatok két különböző virtuális hálózatokban, amelyek mindkét köti a Service Bus ugyanazon kommunikálhatnak hatékonyan és megbízhatóan keresztül üzenetek, míg a megfelelő hálózati elkülönítési határ integritása.
 
Azt jelenti, hogy a bizalmas felhőmegoldások csak nem férnek hozzá az Azure-szolgáltatás piacvezető megbízható és méretezhető aszinkron üzenetkezelési képességeket, de most használhatnak üzenetkezelés a biztonságos megoldás közötti kommunikációs útvonalat hozzon létre biztonsági kocsiban olyan eleve biztonságosabb, mint bármely társ-társ-kommunikációs móddal, többek között a HTTPS és az egyéb TLS biztonságos szoftvercsatorna-protokollok kezelhető.

## <a name="binding-service-bus-to-virtual-networks"></a>Kötés a Service Bus virtuális hálózatokhoz

*A virtuális hálózati szabályok* van a tűzfal biztonsági szolgáltatás, amely szabályozza, hogy az Azure Service Bus-kiszolgáló egy adott virtuális hálózati alhálózat érkező kapcsolatokat fogad-e.

Virtuális hálózat és egy Service Bus-névtér kötés két lépésből áll. Először hozzon létre egy **virtuális hálózati szolgáltatásvégpont** egy virtuális hálózat alhálózatán, és engedélyezze a Microsoft.servicebus"", azt mutatjuk be a [szolgáltatási végpont áttekintő] [ vnet-sep]. Miután hozzáadta a szolgáltatásvégpont, azt a kötést létrehozni a Service Bus-névtér egy *virtuális hálózati szabályt*.

A virtuális hálózati szabály a Service Bus-névtér egy virtuális hálózat alhálózatához van társítva. A szabály létezik, míg az alhálózat kötött összes számítási feladatokhoz, amelyekhez hozzáférést a Service Bus-névtér. Service Bus maga soha nem kimenő kapcsolatot hoz létre, nem kell hozzáférést, és ezért soha nem kap hozzáférést az alhálózat szerint ez a szabály engedélyezése.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager-sablonokkal a virtuális hálózati szabály létrehozása

A következő Resource Manager-sablon lehetővé teszi, hogy egy virtuális hálózati szabályt ad hozzá egy meglévő Service Bus-névteret.

Sablon paraméterei:

* **namespaceName**: A Service Bus-névtér.
* **virtualNetworkingSubnetId**: A virtuális hálózat alhálózatához; teljesen minősített erőforrás-kezelő elérési útja Ha például `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` az alapértelmezett alhálózat egy virtuális hálózat.

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

A sablon üzembe helyezéséhez kövesse az utasításokat [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>További lépések

Virtuális hálózatokkal kapcsolatos további információkért lásd az alábbi hivatkozásokat:

- [Az Azure virtuális hálózati Szolgáltatásvégpontok][vnet-sep]
- [Az Azure Service Bus IP-szűrés][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md
