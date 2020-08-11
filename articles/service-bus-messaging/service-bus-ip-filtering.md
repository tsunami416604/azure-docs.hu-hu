---
title: A Azure Service Bus IP-tűzfalszabályok konfigurálása
description: A tűzfalszabályok használata az adott IP-címekről Azure Service Bus való csatlakozás engedélyezéséhez.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 699ece2e78ff0605ff4076b09c023d14e289b1f7
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88064638"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>Azure Service Bus névtér elérésének engedélyezése adott IP-címekről vagy tartományokból
Alapértelmezés szerint a Service Bus névterek az internetről érhetők el, feltéve, hogy a kérés érvényes hitelesítéssel és engedélyezéssel rendelkezik. Az IP-tűzfallal továbbra is korlátozhatja, hogy csak IPv4-címek vagy IPv4-címtartományok legyenek a [CIDR (osztály nélküli tartományok közötti útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel.

Ez a funkció olyan helyzetekben hasznos, amikor a Azure Service Bus csak bizonyos jól ismert helyekről lehet elérhető. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekből származó forgalom fogadásához. Ha például az [Azure Express Route][express-route]Service Bust használja, létrehozhat egy **tűzfalszabályet** , amely lehetővé teszi, hogy csak a helyszíni infrastruktúra IP-címeiről vagy egy vállalati NAT-átjáró címéről érkező forgalom legyen. 

> [!IMPORTANT]
> A tűzfalak és a virtuális hálózatok csak a **prémium** szintű Service Bus támogatottak. Ha a **Premier** szintre való frissítés nem egy lehetőség, javasoljuk, hogy a közös hozzáférésű aláírás (SAS) tokenjét biztonságos módon őrizze meg, és csak a jogosult felhasználók férhessenek hozzá. További információ az SAS-hitelesítéssel kapcsolatban: [hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md#shared-access-signature).

## <a name="ip-firewall-rules"></a>IP-tűzfalszabályok
Az IP-tűzfalszabályok a Service Bus névtér szintjén lesznek alkalmazva. Ezért a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával. Olyan IP-címről érkező csatlakozási kísérletek, amely nem felel meg a Service Bus névtérben lévő engedélyezett IP-szabálynak, a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt. Az IP-szűrési szabályok sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

>[!WARNING]
> A tűzfalszabályok bevezetésével megakadályozhatja, hogy más Azure-szolgáltatások a Service Bus használatával kommunikálnak.
>
> A megbízható Microsoft-szolgáltatások használata nem támogatott, ha az IP-szűrés (tűzfalszabályok) be van vezetve, és hamarosan elérhetővé válik.
>
> Általános Azure-forgatókönyvek, amelyek nem működnek az IP-szűréssel (vegye figyelembe, hogy a lista **nem** teljes) –
> - Integráció a Azure Event Grid
> - Azure IoT Hub útvonalak
> - Azure IoT Device Explorer
>
> A következő Microsoft-szolgáltatások szükségesek virtuális hálózaton
> - Azure App Service
> - Azure Functions
> - Azure Monitor (diagnosztikai beállítás)

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ebből a szakaszból megtudhatja, hogyan használhatja a Azure Portal IP-tűzfalszabályok létrehozásához egy Service Bus névtérhez. 

1. Navigáljon a **Service Bus névtérhez** a [Azure Portal](https://portal.azure.com).
2. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget a **Beállítások**területen.  

    > [!NOTE]
    > A **hálózatkezelés** lap csak a **prémium** szintű névterek esetében jelenik meg.  
    
    Alapértelmezés szerint a **kiválasztott hálózatok** lehetőség van kiválasztva. Ha nem ad hozzá legalább egy IP-tűzfalszabály vagy virtuális hálózat ezen a lapon, a névtér a nyilvános interneten keresztül érhető el (a hozzáférési kulcs használatával).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Hálózatkezelés lap – alapértelmezett" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Ha a **minden hálózat** lehetőséget választja, akkor a Service Bus névtér bármely IP-címről fogad kapcsolatokat. Ez az alapértelmezett beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt. 

    ![Tűzfal – az összes hálózat lehetőség ki van választva](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Ha csak a megadott IP-címről szeretne hozzáférést engedélyezni, válassza a **kiválasztott hálózatok** lehetőséget, ha még nincs kiválasztva. A **tűzfal** szakaszban kövesse az alábbi lépéseket:
    1. Válassza az **ügyfél IP-címének hozzáadása** lehetőséget, hogy a jelenlegi ügyfél IP-címe hozzáférjen a névtérhez. 
    2. A **címtartomány**mezőben adjon meg egy adott IPv4-címeket vagy IPv4-CÍMTARTOMÁNYT a CIDR-jelölésben. 
    3. Itt adhatja meg, hogy szeretné- **e engedélyezni a megbízható Microsoft-szolgáltatások számára a tűzfal megkerülését**. 

        > [!WARNING]
        > Ha a **kiválasztott hálózatok** lehetőséget választja, és nem ad meg IP-címet vagy címtartományt, akkor a szolgáltatás minden hálózatról engedélyezi a forgalmat. 

        ![Tűzfal – az összes hálózat lehetőség ki van választva](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra az eszköztáron. Várjon néhány percet, hogy a megerősítés megjelenjen a portál értesítésein.

    > [!NOTE]
    > Ha korlátozni szeretné a hozzáférést az adott virtuális hálózatokhoz, tekintse meg a [hozzáférés engedélyezése adott hálózatokból](service-bus-service-endpoints.md)című témakört.

## <a name="use-resource-manager-template"></a>Resource Manager-sablon használata
Ez a szakasz egy minta Azure Resource Manager sablonnal rendelkezik, amely létrehoz egy virtuális hálózatot és egy tűzfalszabály-szabályt.


A következő Resource Manager-sablon lehetővé teszi egy virtuális hálózati szabály hozzáadását egy meglévő Service Bus névtérhez.

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

Az Azure-beli virtuális hálózatokhoz való Service Bus hozzáférésének korlátozásához tekintse meg a következő hivatkozást:

- [Service Bus Virtual Network szolgáltatási végpontok][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services