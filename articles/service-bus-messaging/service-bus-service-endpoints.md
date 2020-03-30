---
title: Virtuális hálózati szolgáltatásvégpontok konfigurálása az Azure Service Bus szolgáltatáshoz
description: Ez a cikk a Microsoft.ServiceBus szolgáltatásvégpont okainak virtuális hálózathoz való hozzáadásáról nyújt tájékoztatást.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 9dbf65522d5c85e1054ed3f1f6ca9f86180e7f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454981"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>Virtuális hálózati szolgáltatásvégpontok konfigurálása az Azure Service Bus szolgáltatáshoz

A Service Bus [virtuális hálózati (VNet) szolgáltatásvégpontokkal][vnet-sep] való integrációja lehetővé teszi a virtuális hálózatokhoz kötött számítási feladatokból, például a virtuális hálózatokhoz kötött virtuális gépekről származó üzenetküldési képességek biztonságos elérését, és a hálózati forgalom elérési útja mindkét végén biztonságos.

Ha úgy van beállítva, hogy legalább egy virtuális hálózati alhálózati szolgáltatás végponthoz legyen kötve, a megfelelő Service Bus-névtér már nem fogadja el a forgalmat bárhonnan, csak az engedélyezett virtuális hálózat(ok)hoz. A virtuális hálózati perspektíva, a Service Bus névtér és a szolgáltatás végpontja konfigurálja egy elszigetelt hálózati alagút a virtuális hálózati alhálózat az üzenetküldő szolgáltatás.

Az eredmény egy privát és elkülönített kapcsolat az alhálózathoz és a megfelelő Service Bus-névtérhez kötött munkaterhelések között, annak ellenére, hogy az üzenetküldési szolgáltatás végpontjának megfigyelhető hálózati címe nyilvános IP-tartományban van.

> [!IMPORTANT]
> A virtuális hálózatok csak [a Prémium szintű](service-bus-premium-messaging.md) Service Bus-névterekben támogatottak.
> 
> VNet szolgáltatásvégpontok használatakor a Service Bus, nem szabad engedélyezni ezeket a végpontokat az alkalmazásokban, amelyek keverik a standard és a prémium szintű Service Bus névterek. Mivel a standard szint nem támogatja a virtuális hálózatokat. A végpont csak a prémium szintű névterekre korlátozódik.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>A virtuális hálózat integrációja által engedélyezett speciális biztonsági forgatókönyvek 

Olyan megoldások, amelyek szigorú és széttagolt biztonságot igényelnek, és ahol a virtuális hálózati alhálózatok biztosítják a széttagolt szolgáltatások közötti szegmentálást, általában továbbra is szükség van az ezekben a rekeszekben található szolgáltatások közötti kommunikációs útvonalakra.

A rekeszek közötti bármely közvetlen IP-útvonal, beleértve a TCP/IP-n keresztül https-t hordozókat is, magában hordozza a biztonsági rések kihasználásának kockázatát a hálózati rétegből felfelé. Az üzenetküldő szolgáltatások teljesen szigetelt kommunikációs útvonalakat biztosítanak, ahol az üzenetek et még a lemezekre is írják, amint a felek között áttérnek. Két különböző virtuális hálózat, amelyek ugyanahhoz a Service Bus-példányhoz vannak kötve, hatékonyan és megbízhatóan kommunikálhatnak az üzeneteken keresztül, miközben a megfelelő hálózati elkülönítési határ integritása megmarad.
 
Ez azt jelenti, hogy a biztonsági szempontból érzékeny felhőalapú megoldásai nem csak az Azure iparágvezető megbízható és skálázható aszinkron üzenetkezelési képességeihez férnek hozzá, hanem mostantól az üzenetküldés segítségével kommunikációs útvonalakat hozhatnak létre a biztonságos megoldási rekeszek között, amelyek eredendően biztonságosabbak, mint bármely peer-to-peer kommunikációs mód, beleértve a HTTPS-t és más TLS-vel védett szoftvercsatorna protokollokat.

## <a name="binding-service-bus-to-virtual-networks"></a>Szolgáltatásbusz kötése virtuális hálózatokhoz

*A virtuális hálózati szabályok* a tűzfal biztonsági szolgáltatása, amely azt szabályozza, hogy az Azure Service Bus-kiszolgáló fogadja-e a kapcsolatot egy adott virtuális hálózati alhálózatból.

A Service Bus-névtér virtuális hálózathoz kötése kétlépésből áll. Először létre kell hoznia egy **virtuális hálózati szolgáltatás végpontját** egy virtuális hálózat alhálózaton, és engedélyeznie kell azt a **Microsoft.ServiceBus** számára a [szolgáltatás végpontjának áttekintése szerint.][vnet-sep] Miután hozzáadta a szolgáltatásvégpontot, a Service Bus névterét **egy virtuális hálózati szabállyal**köti hozzá.

A virtuális hálózati szabály a Service Bus névtér és a virtuális hálózati alhálózat társítása. Amíg a szabály létezik, az alhálózathoz kötött összes számítási feladat hozzáférést kap a Service Bus névtérhez. A Service Bus maga soha nem hoz létre kimenő kapcsolatokat, nem kell hozzáférnie, és ezért soha nem kap hozzáférést az alhálózathoz, ha engedélyezi ezt a szabályt.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz bemutatja, hogyan használhatja az Azure Portalon egy virtuális hálózati szolgáltatás végpontjának hozzáadásához. A hozzáférés korlátozásához integrálnia kell az Event Hubs névtér virtuális hálózati szolgáltatásvégpontját.

1. Nyissa meg a **Service Bus-névteret** az [Azure Portalon.](https://portal.azure.com)
2. A bal oldali menüben válassza a **Hálózat lehetőséget.** Alapértelmezés szerint a **Minden hálózat** beállítás be van jelölve. A névtér bármely IP-címről fogad kapcsolatot. Ez az alapértelmezett beállítás megegyezik a 0.0.0.0/0 IP-címtartományt elfogadó szabállyal. 

    ![Tűzfal – Minden hálózat beállítás kiválasztva](./media/service-endpoints/firewall-all-networks-selected.png)
1. Válassza a Lap tetején a **Kijelölt hálózatok** lehetőséget.
2. A lap **Virtuális hálózat** szakaszában válassza a **+Meglévő virtuális hálózat hozzáadása**lehetőséget. 

    ![meglévő virtuális hálózat hozzáadása](./media/service-endpoints/add-vnet-menu.png)
3. Válassza ki a virtuális hálózatot a virtuális hálózatok listájából, majd válassza ki az **alhálózatot.** A virtuális hálózat hozzáadása előtt engedélyeznie kell a szolgáltatásvégpontot. Ha a szolgáltatás végpontja nincs engedélyezve, a portál kérni fogja, hogy engedélyezze azt.
   
   ![alhálózat kiválasztása](./media/service-endpoints/select-subnet.png)

4. A következő sikeres üzenetnek kell megjelennie, miután az alhálózat szolgáltatásvégpontja engedélyezve van a **Microsoft.ServiceBus**számára. A hálózat hozzáadásához válassza a **Hozzáadás** gombot a lap alján. 

    ![alhálózat kiválasztása és végpont engedélyezése](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Ha nem tudja engedélyezni a szolgáltatásvégpontot, figyelmen kívül hagyhatja a hiányzó virtuális hálózati szolgáltatás végpontját az Erőforrás-kezelő sablon használatával. Ez a funkció nem érhető el a portálon.
6. A beállítások mentéséhez válassza a **Mentés** gombot az eszköztáron. Várjon néhány percet, amíg a visszaigazolás megjelenik a portál értesítéseiközött. A **Mentés** gombot le kell tiltani. 

    ![Hálózat mentése](./media/service-endpoints/save-vnet.png)

## <a name="use-resource-manager-template"></a>Resource Manager-sablon használata
A következő Erőforrás-kezelő sablon lehetővé teszi egy virtuális hálózati szabály hozzáadását egy meglévő Service Bus-névtérhez.

Sablon paraméterei:

* **namespaceName**: Service Bus névtér.
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
- [Az Azure Service Bus IP-szűrése][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
