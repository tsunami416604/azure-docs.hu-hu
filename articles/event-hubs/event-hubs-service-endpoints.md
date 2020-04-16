---
title: Virtuális hálózati szolgáltatás végpontjai – Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk a Microsoft.EventHub szolgáltatásvégpont oktatásáról nyújt tájékoztatást a virtuális hálózathoz.
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
ms.openlocfilehash: 91b08d6130da640adc28a3b7d85bd33f0e876caf
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390285"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Virtuális hálózati szolgáltatásvégpontok használata az Azure Event Hubs szolgáltatással

Az Event Hubs és a [Virtuális hálózat szolgáltatásvégpontok][vnet-sep] integrációja lehetővé teszi a virtuális hálózatokhoz kötött számítási feladatok, például a virtuális hálózatokhoz kötött virtuális gépek üzenetkezelési képességeinek biztonságos elérését, és a hálózati forgalom elérési útja mindkét végén biztonságos.

Miután úgy konfigurálták, hogy legalább egy virtuális hálózati alhálózati szolgáltatás végponthoz kötődjön, a megfelelő Event Hubs névtér már nem fogadja el a forgalmat bárhonnan, csak a virtuális hálózatok engedélyezett alhálózatai. A virtuális hálózati perspektíva, az Event Hubs névtér és a szolgáltatás végpontja konfigurálja egy elszigetelt hálózati alagút a virtuális hálózati alhálózat az üzenetküldő szolgáltatás. 

Az eredmény egy privát és elkülönített kapcsolat az alhálózathoz és a megfelelő Event Hubs névtérhez kötött munkaterhelések között, annak ellenére, hogy az üzenetküldő szolgáltatás végpontjának megfigyelhető hálózati címe nyilvános IP-tartományban van. Ez alól a viselkedés alól kivétel van. A szolgáltatásvégpont engedélyezése alapértelmezés szerint engedélyezi `denyall` a virtuális hálózathoz társított [IP-tűzfalszabályát.](event-hubs-ip-filtering.md) Adott IP-címeket adhat hozzá az IP-tűzfalhoz, hogy engedélyezze a hozzáférést az Event Hub nyilvános végpontjához. 

>[!WARNING]
> Virtuális hálózatok integrációja megakadályozhatja, hogy más Azure-szolgáltatások együttműködjenek az Event Hubs.
>
> A megbízható Microsoft-szolgáltatások nem támogatottak a virtuális hálózatok megvalósításakor.
>
> A virtuális hálózatokkal nem működik gyakori Azure-forgatókönyvek (vegye figyelembe, hogy a lista **nem** teljes) -
> - Azure Monitor (diagnosztikai beállítás)
> - Azure Stream Analytics
> - Integráció az Azure Event Griddel
> - Az Azure IoT Hub-útvonalak
> - Azure IoT eszközkezelő
>
> A következő Microsoft-szolgáltatásoknak virtuális hálózaton kell lenniük
> - Azure Web Apps
> - Azure Functions


> [!IMPORTANT]
> A virtuális hálózatokat az Event Hubs **szabványos** és **dedikált** szintjei támogatják. Nem támogatott az **alapszinten.**

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>A virtuális hálózat integrációja által engedélyezett speciális biztonsági forgatókönyvek 

A szűk és széttagolt biztonságot igénylő megoldások, amelyeken a virtuális hálózati alhálózatok biztosítják a széttagolt szolgáltatások közötti szegmentálást, továbbra is szükség van az ezekben a rekeszekben található szolgáltatások közötti kommunikációs útvonalakra.

A rekeszek közötti bármely közvetlen IP-útvonal, beleértve a TCP/IP-n keresztül https-t hordozókat is, magában hordozza a biztonsági rések kihasználásának kockázatát a hálózati rétegből felfelé. Az üzenetküldő szolgáltatások szigetelt kommunikációs útvonalakat biztosítanak, ahol az üzenetek et még a felek közötti átmenet során is lemezre írják. Két különböző virtuális hálózat munkaterhelései, amelyek ugyanahhoz az Event Hubs-példányhoz vannak kötve, hatékonyan és megbízhatóan kommunikálhatnak az üzeneteken keresztül, miközben a megfelelő hálózati elkülönítési határ integritása megmarad.
 
Ez azt jelenti, hogy a biztonsági szempontból érzékeny felhőalapú megoldásai nem csak az Azure iparágvezető megbízható és skálázható aszinkron üzenetkezelési képességeihez férnek hozzá, hanem mostantól az üzenetküldés segítségével kommunikációs útvonalakat hozhatnak létre a biztonságos megoldási rekeszek között, amelyek természetüknél fogva biztonságosabbak, mint bármely egyenrangú kommunikációs mód, beleértve a HTTPS-t és más TLS-védelemmel rendelkező szoftvercsatorna-protokollokat.

## <a name="bind-event-hubs-to-virtual-networks"></a>Eseményközpontok kötése virtuális hálózatokhoz

**A virtuális hálózati szabályok** a tűzfal biztonsági szolgáltatás, amely szabályozza, hogy az Azure Event Hubs névtér fogadja-e a kapcsolatokat egy adott virtuális hálózati alhálózat.

Az Event Hubs névtér virtuális hálózathoz kötése két lépésből áll. Először létre kell hoznia egy **virtuális hálózati szolgáltatás végpontját** a virtuális hálózat alhálózatán, és engedélyeznie kell azt a **Microsoft.EventHub** számára a [szolgáltatásvégpont áttekintése][vnet-sep] című cikkben leírtak szerint. Miután hozzáadta a szolgáltatásvégpontot, az Event Hubs névteret **egy virtuális hálózati szabállyal**köti hozzá.

A virtuális hálózati szabály az Event Hubs névtér és a virtuális hálózati alhálózat társítása. Amíg a szabály létezik, az alhálózathoz kötött összes számítási feladat hozzáférést kap az Event Hubs névtérhez. Az Event Hubs maga soha nem hoz létre kimenő kapcsolatokat, nem kell hozzáférnie, és ezért soha nem kap hozzáférést az alhálózathoz a szabály engedélyezésével.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz bemutatja, hogyan használhatja az Azure Portalon egy virtuális hálózati szolgáltatás végpontjának hozzáadásához. A hozzáférés korlátozásához integrálnia kell az Event Hubs névtér virtuális hálózati szolgáltatásvégpontját.

1. Nyissa meg az **Event Hubs névterét** az [Azure Portalon.](https://portal.azure.com)
2. A bal oldali menüben válassza a **Hálózat lehetőséget.** Ha a **Minden hálózat** lehetőséget választja, az eseményközpont bármely IP-címről fogadja a kapcsolatokat. Ez a beállítás megegyezik a 0.0.0.0/0 IP-címtartományt elfogadó szabállyal. 

    ![Tűzfal – Minden hálózat beállítás kiválasztva](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Ha bizonyos hálózatokhoz szeretne hozzáférni, válassza a lap tetején a **Kijelölt hálózatok** lehetőséget.
2. A lap **Virtuális hálózat** szakaszában válassza a **+Meglévő virtuális hálózat hozzáadása***lehetőséget. Válassza **a + Új virtuális hálózat létrehozása lehetőséget,** ha új virtuális hálózatot szeretne létrehozni. 

    ![meglévő virtuális hálózat hozzáadása](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Válassza ki a virtuális hálózatot a virtuális hálózatok listájából, majd válassza ki az **alhálózatot.** A virtuális hálózat hozzáadása előtt engedélyeznie kell a szolgáltatásvégpontot. Ha a szolgáltatás végpontja nincs engedélyezve, a portál kérni fogja, hogy engedélyezze azt.
   
   ![alhálózat kiválasztása](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. A következő sikeres üzenetnek kell megjelennie, miután az alhálózat szolgáltatásvégpontja engedélyezve van a **Microsoft.EventHub számára.** A hálózat hozzáadásához válassza a **Hozzáadás** gombot a lap alján. 

    ![alhálózat kiválasztása és végpont engedélyezése](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Ha nem tudja engedélyezni a szolgáltatásvégpontot, figyelmen kívül hagyhatja a hiányzó virtuális hálózati szolgáltatás végpontját az Erőforrás-kezelő sablon használatával. Ez a funkció nem érhető el a portálon.
6. A beállítások mentéséhez válassza a **Mentés** gombot az eszköztáron. Várjon néhány percet, amíg a visszaigazolás megjelenik a portálértesítésein.

    ![Hálózat mentése](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>Resource Manager-sablon használata

A következő Erőforrás-kezelő sablon lehetővé teszi egy virtuális hálózati szabály hozzáadását egy meglévő Eseményközpont-névtérhez.

Sablon paraméterei:

* **namespaceName**: Event Hubs névtér.
* **vnetRuleName**: A létrehozandó virtuális hálózati szabály neve.
* **virtualNetworkingSubnetId**: A virtuális hálózat alhálózatának teljesen minősített Erőforrás-kezelőelérési útja; például `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` egy virtuális hálózat alapértelmezett alhálózatához.

> [!NOTE]
> Bár nincsenek megtagadási szabályok lehetséges, az Azure Resource Manager-sablon az alapértelmezett művelet beállítása **"Engedélyezés",** amely nem korlátozza a kapcsolatokat.
> A virtuális hálózat vagy a tűzfalak szabályainak megalkotásakor meg kell változtatnunk a ***"defaultAction"***
> 
> honnan
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

A virtuális hálózatokról az alábbi hivatkozásokon talál további információt:

- [Az Azure virtuális hálózati szolgáltatásának végpontjai][vnet-sep]
- [Az Azure Event Hubs IP-szűrése][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
