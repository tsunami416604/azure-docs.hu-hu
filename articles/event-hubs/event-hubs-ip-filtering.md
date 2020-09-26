---
title: Azure Event Hubs tűzfalszabályok | Microsoft Docs
description: A tűzfalszabályok használatával engedélyezheti az adott IP-címekről az Azure Event Hubs való kapcsolódást.
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: ab7f835187a33b5e4d95c160831337172a5ed74e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318534"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>Azure Event Hubs-névterek elérésének engedélyezése adott IP-címekről vagy tartományokból
Alapértelmezés szerint a Event Hubs névterek az internetről érhetők el, feltéve, hogy a kérés érvényes hitelesítéssel és engedélyezéssel rendelkezik. Az IP-tűzfallal továbbra is korlátozhatja, hogy csak IPv4-címek vagy IPv4-címtartományok legyenek a [CIDR (osztály nélküli tartományok közötti útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel.

Ez a funkció olyan helyzetekben hasznos, amikor az Azure Event Hubs csak bizonyos jól ismert helyekről elérhető. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekből származó forgalom fogadásához. Ha például az [Azure Express Route][express-route]Event Hubst használja, létrehozhat egy **tűzfalszabályet** , amely lehetővé teszi, hogy csak a helyszíni infrastruktúra IP-címeiről érkező forgalmat engedélyezze. 

>[!IMPORTANT]
> A tűzfalszabályok bekapcsolása a Event Hubs névtérben alapértelmezés szerint blokkolja a bejövő kérelmeket, hacsak a kérelmek nem az engedélyezett nyilvános IP-címekről származó szolgáltatásból származnak. A letiltott kérések közé tartoznak a más Azure-szolgáltatások, a Azure Portal, a naplózási és a metrikai szolgáltatások, valamint így tovább. 
>
> Íme néhány olyan szolgáltatás, amely nem fér hozzá Event Hubs erőforrásokhoz, amikor az IP-szűrés engedélyezve van. Vegye figyelembe, hogy a lista **nem** teljes.
>
> - Azure Stream Analytics
> - Azure IoT Hub útvonalak
> - Azure IoT Device Explorer
> - Azure Event Grid
> - Azure Monitor (diagnosztikai beállítások)
>
> Kivételként engedélyezheti a hozzáférést bizonyos megbízható szolgáltatásoktól Event Hubs erőforrásaihoz, még akkor is, ha az IP-szűrés engedélyezve van. A megbízható szolgáltatások listáját lásd: [megbízható Microsoft-szolgáltatások](#trusted-microsoft-services).

## <a name="ip-firewall-rules"></a>IP-tűzfalszabályok
Az IP-tűzfalszabályok a Event Hubs névtér szintjén lesznek alkalmazva. Így a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával. Olyan IP-címről érkező csatlakozási kísérletek, amely nem felel meg az engedélyezett IP-szabálynak a Event Hubs-névtérben, a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt. Az IP-szűrési szabályok sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ebből a szakaszból megtudhatja, hogyan hozhat létre IP-tűzfalszabályok Event Hubs névtérhez a Azure Portal használatával. 

1. Navigáljon a **Event Hubs névtérhez** a [Azure Portal](https://portal.azure.com).
4. A bal oldali menü **Beállítások** területén válassza a **hálózatkezelés** lehetőséget. A **hálózatkezelés** lap csak a **standard** vagy a **dedikált** névtér esetében jelenik meg. 
    > [!NOTE]
    > Alapértelmezés szerint a **kiválasztott hálózatok** lehetőség van kiválasztva, ahogy az az alábbi képen is látható. Ha nem ad meg IP-tűzfalszabály-szabályt, vagy nem ad hozzá virtuális hálózatot ezen a lapon, a névtér a **nyilvános interneten** keresztül érhető el (a hozzáférési kulccsal).  

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Hálózatok lap – kiválasztott hálózatok lehetőség" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Ha a **minden hálózat** lehetőséget választja, az Event hub bármely IP-címről fogad kapcsolatokat (a hozzáférési kulccsal). Ez a beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt. 

    ![Képernyőkép, amely a "minden hálózat" lehetőséggel megjeleníti a "tűzfal és virtuális hálózatok" lapot.](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. A megadott IP-címekre való hozzáférés korlátozásához ellenőrizze, hogy a **kiválasztott hálózatok** lehetőség van-e kiválasztva. A **tűzfal** szakaszban kövesse az alábbi lépéseket:
    1. Válassza az **ügyfél IP-címének hozzáadása** lehetőséget, hogy a jelenlegi ügyfél IP-címe hozzáférjen a névtérhez. 
    2. A **címtartomány**mezőben adjon meg egy adott IPv4-címeket vagy IPv4-CÍMTARTOMÁNYT a CIDR-jelölésben. 
3. Itt adhatja meg, hogy szeretné- **e engedélyezni a megbízható Microsoft-szolgáltatások számára a tűzfal megkerülését**. További részletekért lásd a [megbízható Microsoft-szolgáltatások](#trusted-microsoft-services) című témakört. 

      ![Tűzfal – az összes hálózat lehetőség ki van választva](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra az eszköztáron. Várjon néhány percet, hogy a megerősítés megjelenjen a portál értesítésein.

    > [!NOTE]
    > Ha korlátozni szeretné a hozzáférést az adott virtuális hálózatokhoz, tekintse meg a [hozzáférés engedélyezése adott hálózatokból](event-hubs-service-endpoints.md)című témakört.

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]


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
> a következőre:
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

## <a name="next-steps"></a>Következő lépések

Az Azure-beli virtuális hálózatokhoz való Event Hubs hozzáférésének korlátozásához tekintse meg a következő hivatkozást:

- [Event Hubs Virtual Network szolgáltatási végpontok][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
