---
title: Azure Event Hubs tűzfalszabályok | Microsoft Docs
description: A tűzfalszabályok használatával engedélyezheti az adott IP-címekről az Azure Event Hubs való kapcsolódást.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: fb9fa72af7127224afdcf70ecca1c851e9212c4d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85320422"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>IP-tűzfalszabályok konfigurálása Azure Event Hubs-névtérhez
Alapértelmezés szerint a Event Hubs névterek az internetről érhetők el, feltéve, hogy a kérés érvényes hitelesítéssel és engedélyezéssel rendelkezik. Az IP-tűzfallal továbbra is korlátozhatja, hogy csak IPv4-címek vagy IPv4-címtartományok legyenek a [CIDR (osztály nélküli tartományok közötti útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel.

Ez a funkció olyan helyzetekben hasznos, amikor az Azure Event Hubs csak bizonyos jól ismert helyekről elérhető. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekből származó forgalom fogadásához. Ha például az [Azure Express Route][express-route]Event Hubst használja, létrehozhat egy **tűzfalszabályet** , amely lehetővé teszi, hogy csak a helyszíni infrastruktúra IP-címeiről érkező forgalmat engedélyezze. 

>[!WARNING]
> Az IP-szűrés engedélyezésével megakadályozhatja, hogy más Azure-szolgáltatások a Event Hubs használatával kommunikálnak.
>
> A megbízható Microsoft-szolgáltatások nem támogatottak, ha a virtuális hálózatok implementálva vannak.
>
> Olyan általános Azure-forgatókönyvek, amelyek nem működnek a virtuális hálózatokkal (vegye figyelembe, hogy a lista **nem** teljes) –
> - Azure Monitor (diagnosztikai beállítás)
> - Azure Stream Analytics
> - Integráció a Azure Event Grid
> - Azure IoT Hub útvonalak
> - Azure IoT Device Explorer
>
> A következő Microsoft-szolgáltatások szükségesek virtuális hálózaton
> - Azure Web Apps
> - Azure Functions


## <a name="ip-firewall-rules"></a>IP-tűzfalszabályok
Az IP-tűzfalszabályok a Event Hubs névtér szintjén lesznek alkalmazva. Ezért a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával. Olyan IP-címről érkező csatlakozási kísérletek, amely nem felel meg a Event Hubs névtérben lévő engedélyezett IP-szabálynak, a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt. Az IP-szűrési szabályok sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ebből a szakaszból megtudhatja, hogyan hozhat létre IP-tűzfalszabályok Event Hubs névtérhez a Azure Portal használatával. 

1. Navigáljon a **Event Hubs névtérhez** a [Azure Portal](https://portal.azure.com).
2. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget. Ha a **minden hálózat** lehetőséget választja, az Event hub bármely IP-címről fogad kapcsolatokat. Ez a beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt. 

    ![Tűzfal – az összes hálózat lehetőség ki van választva](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Ha korlátozni szeretné a hozzáférést bizonyos hálózatokra és IP-címekre, válassza a **kiválasztott hálózatok** lehetőséget. A **tűzfal** szakaszban kövesse az alábbi lépéseket:
    1. Válassza az **ügyfél IP-címének hozzáadása** lehetőséget, hogy a jelenlegi ügyfél IP-címe hozzáférjen a névtérhez. 
    2. A **címtartomány**mezőben adjon meg egy adott IPv4-címeket vagy IPv4-CÍMTARTOMÁNYT a CIDR-jelölésben. 
    3. Itt adhatja meg, hogy szeretné- **e engedélyezni a megbízható Microsoft-szolgáltatások számára a tűzfal megkerülését**. 

        > [!WARNING]
        > Ha a **kiválasztott hálózatok** lehetőséget választja, és nem ad meg IP-címet vagy címtartományt, akkor a szolgáltatás minden hálózatról engedélyezi a forgalmat. 

        ![Tűzfal – az összes hálózat lehetőség ki van választva](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra az eszköztáron. Várjon néhány percet, hogy a megerősítés megjelenjen a portál értesítésein.


## <a name="use-resource-manager-template"></a>Resource Manager-sablon használata

> [!IMPORTANT]
> A tűzfalszabályok a **standard** és a **dedikált** Event Hubs szinten támogatottak. Alapszintű csomag esetén nem támogatott.

A következő Resource Manager-sablon lehetővé teszi az IP-szűrési szabály hozzáadását egy meglévő Event Hubs névtérhez.

Sablon paraméterei:

- a **ipMask** egy IPv4-cím vagy IP-cím CIDR-jelölésű blokkja. A CIDR 70.37.104.0/24 jelölése például 256 a 70.37.104.0 és a 70.37.104.255 közötti IPv4-címeket jelöli, és 24 a tartomány jelentős előtagjának számát jelzi.

> [!NOTE]
> Habár a megtagadási szabályok nem lehetségesek, a Azure Resource Manager sablon az **"engedélyezés"** értékre van állítva, amely nem korlátozza a kapcsolatokat.
> Virtual Network vagy tűzfalakra vonatkozó szabályok végrehajtásakor módosítania kell a ***"defaultAction"***
> 
> a
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
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

A sablon üzembe helyezéséhez kövesse az [Azure Resource Manager][lnk-deploy]utasításait.

## <a name="next-steps"></a>További lépések

Az Azure-beli virtuális hálózatokhoz való Event Hubs hozzáférésének korlátozásához tekintse meg a következő hivatkozást:

- [Event Hubs Virtual Network szolgáltatási végpontok][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
