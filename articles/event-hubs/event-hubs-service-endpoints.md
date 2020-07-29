---
title: Virtual Network szolgáltatási végpontok – Azure Event Hubs | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan adhat hozzá Microsoft. EventHub szolgáltatási végpontot egy virtuális hálózathoz.
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 5d1f6bb8e1160a328c30cfd6ef1726e3cf011aee
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288010"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Virtual Network szolgáltatási végpontok használata az Azure-Event Hubs

Event Hubs és [Virtual Network (VNet) szolgáltatás-végpontok][vnet-sep] integrációja lehetővé teszi az üzenetkezelési funkciók biztonságos elérését olyan munkaterhelések esetén, mint például a virtuális hálózatokhoz kötött virtuális gépek, és a hálózati forgalom elérési útja mindkét végén védett.

Ha úgy van konfigurálva, hogy legalább egy virtuális hálózati alhálózat szolgáltatási végpontra legyen kötve, a megfelelő Event Hubs névtér többé nem fogadja el a forgalmat bárhonnan, de a virtuális hálózatokban engedélyezett alhálózatokat. A virtuális hálózat szempontjából a Event Hubs névterek egy szolgáltatás-végponthoz kötése egy elkülönített hálózati alagutat állít be a virtuális hálózat alhálózatáról az üzenetküldési szolgáltatásba. 

Az eredmény az alhálózathoz és a megfelelő Event Hubs névtérhez kötött munkaterhelések közötti privát és elkülönített kapcsolat, annak ellenére, hogy az üzenetküldési szolgáltatás végpontjának megfigyelhető hálózati címe egy nyilvános IP-tartományban van. Ez a viselkedés kivételt jelent. A szolgáltatás végpontjának engedélyezése alapértelmezés szerint engedélyezi a `denyall` szabályt a virtuális hálózathoz társított [IP-tűzfalon](event-hubs-ip-filtering.md) . Adott IP-címeket adhat hozzá az IP-tűzfalon az Event hub nyilvános végponthoz való hozzáférés engedélyezéséhez. 

>[!WARNING]
> A virtuális hálózatok integrálásának megvalósításával megakadályozható, hogy más Azure-szolgáltatások a Event Hubs használatával kommunikálnak.
>
> A megbízható Microsoft-szolgáltatások nem támogatottak, ha a virtuális hálózatok implementálva vannak.
>
> Olyan általános Azure-forgatókönyvek, amelyek nem működnek a virtuális hálózatokkal (vegye figyelembe, hogy a lista **nem** teljes) –
> - Azure Stream Analytics
> - Azure IoT Hub útvonalak
> - Azure IoT Device Explorer
>
> A következő Microsoft-szolgáltatások szükségesek virtuális hálózaton
> - Azure-webalkalmazások
> - Azure Functions


> [!IMPORTANT]
> A virtuális hálózatok a **standard** és a **dedikált** Event Hubsban támogatottak. Az alapszintű **csomag** nem támogatja.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>A VNet-integráció által engedélyezett speciális biztonsági forgatókönyvek 

Azok a megoldások, amelyek feszes és compartmentalized biztonságot igényelnek, és ahol a virtuális hálózati alhálózatok biztosítják a compartmentalized szolgáltatások közötti szegmentálást, továbbra is szükség van kommunikációs útvonalakra az ezekben a rekeszekben található szolgáltatások között.

A rekeszek közötti közvetlen IP-útvonal, beleértve a TCP/IP protokollon keresztüli HTTPS-t is, a hálózati réteg biztonsági réseinak kiaknázásának kockázatát hordozza. Az üzenetkezelési szolgáltatások olyan szigetelt kommunikációs útvonalakat biztosítanak, amelyekben a felek közötti átmenet során az üzenetek lemezre is írhatók. Az ugyanahhoz a Event Hubs-példányhoz kötött két különálló virtuális hálózatban a munkaterhelések hatékonyan és megbízhatóan kommunikálhatnak az üzeneteken keresztül, miközben a hálózat elkülönítési határának megfelelő integritása megmarad.
 
Ez azt jelenti, hogy a biztonsági szempontból bizalmas Felhőbeli megoldások nem csupán az Azure piacvezető megbízható és skálázható üzenetkezelési képességeihez férnek hozzá, de mostantól az üzenetküldés használatával kommunikációs útvonalakat hozhatnak létre a biztonságos Megoldási rekeszek között, amelyek eleve biztonságosabbak, mint bármely egyenrangú kommunikációs mód, beleértve a HTTPS-t és más TLS-védelemmel ellátott szoftvercsatorna-protokollokat is.

## <a name="bind-event-hubs-to-virtual-networks"></a>Esemény-hubok kötése virtuális hálózatokhoz

A **virtuális hálózati szabályok** a tűzfal biztonsági funkciója, amely azt szabályozza, hogy az Azure Event Hubs-névtér fogadjon-e kapcsolatokat egy adott virtuális hálózati alhálózatból.

Egy Event Hubs névtér egy virtuális hálózathoz kötése kétlépéses folyamat. Először létre kell hoznia egy **virtuális hálózati szolgáltatás végpontját** egy virtuális hálózat alhálózatán, és engedélyeznie kell azt a **Microsoft. EventHub** számára a [szolgáltatási végpont áttekintése című][vnet-sep] cikkben leírtak szerint. A szolgáltatás végpontjának hozzáadása után a Event Hubs névteret egy **virtuális hálózati szabállyal**kell kötnie.

A virtuális hálózati szabály a Event Hubs névtér egy virtuális hálózati alhálózattal való társítása. Amíg a szabály létezik, az alhálózathoz kötött összes munkaterhelés hozzáférést kap a Event Hubs névtérhez. Event Hubs maga soha nem hoz létre kimenő kapcsolatokat, nem kell elérnie a hozzáférést, ezért a szabály engedélyezésével soha nem kap hozzáférést az alhálózathoz.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz bemutatja, hogyan használható a Azure Portal virtuális hálózati szolgáltatásbeli végpont hozzáadására. A hozzáférés korlátozásához integrálnia kell a virtuális hálózati szolgáltatás végpontját ehhez a Event Hubs névtérhez.

1. Navigáljon a **Event Hubs névtérhez** a [Azure Portal](https://portal.azure.com).
2. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget. Ha a **minden hálózat** lehetőséget választja, az Event hub bármely IP-címről fogad kapcsolatokat. Ez a beállítás egyenértékű egy olyan szabállyal, amely elfogadja a 0.0.0.0/0 IP-címtartományt. 

    ![Tűzfal – az összes hálózat lehetőség ki van választva](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Ha korlátozni szeretné a hozzáférést bizonyos hálózatokra, válassza ki a **kijelölt hálózatok** lehetőséget az oldal tetején.
2. A lap **Virtual Network** szakaszában válassza a * * + meglévő virtuális hálózat hozzáadása * * * lehetőséget. Válassza az **+ új virtuális hálózat létrehozása** lehetőséget, ha új VNet szeretne létrehozni. 

    ![meglévő virtuális hálózat hozzáadása](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Jelölje ki a virtuális hálózatot a virtuális hálózatok listájából, majd válassza ki az **alhálózatot**. Engedélyeznie kell a szolgáltatás végpontját, mielőtt hozzáadja a virtuális hálózatot a listához. Ha a szolgáltatási végpont nincs engedélyezve, akkor a portál felszólítja, hogy engedélyezze.
   
   ![alhálózat kiválasztása](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Az alhálózat szolgáltatási végpontjának engedélyezése után a következő sikeres üzenetnek kell megjelennie: **Microsoft. EventHub**. A hálózat hozzáadásához kattintson a lap alján található **Hozzáadás** gombra. 

    ![alhálózat kiválasztása és végpont engedélyezése](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Ha nem tudja engedélyezni a szolgáltatási végpontot, a Resource Manager-sablonnal figyelmen kívül hagyhatja a hiányzó virtuális hálózati szolgáltatás végpontját. Ez a funkció nem érhető el a portálon.
6. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra az eszköztáron. Várjon néhány percet, hogy a megerősítés megjelenjen a portál értesítésein.

    ![Hálózat mentése](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>Resource Manager-sablon használata

A következő Resource Manager-sablon lehetővé teszi egy virtuális hálózati szabály hozzáadását egy meglévő Event Hubs névtérhez.

Sablon paraméterei:

* `namespaceName`: Event Hubs névtér.
* `vnetRuleName`: A létrehozandó Virtual Network szabály neve.
* `virtualNetworkingSubnetId`: Teljes körű erőforrás-kezelő útvonal a virtuális hálózat alhálózatához; például `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` egy virtuális hálózat alapértelmezett alhálózata esetében.

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

A virtuális hálózatokkal kapcsolatos további információkért tekintse meg az alábbi hivatkozásokat:

- [Azure Virtual Network szolgáltatásbeli végpontok][vnet-sep]
- [Azure Event Hubs IP-szűrés][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
