---
title: Virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure Event hubs |} A Microsoft Docs
description: Hátralékának szolgáltatásvégpont hozzáadása egy virtuális hálózatot.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: 3746c4b7d1b53d7522f317fd2e349d31ba77f406
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136338"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Virtuális hálózati Szolgáltatásvégpontok használata az Azure Event hubs szolgáltatással

Az Event hubs-integráció [virtuális hálózat (VNet) Szolgáltatásvégpontok] [ vnet-sep] lehetővé teszi, hogy biztonságos hozzáférést üzenetküldési funkciókat biztosít a számítási feladatok, például a virtuális gépek, virtuális vannak kötve hálózatok mindkét végén védeni kívánt hálózati forgalom elérési útját. 

Legalább egy virtuális hálózati alhálózat szolgáltatásvégpont kell kötni konfigurálása után az adott Event Hubs-névtér már nem fogadja a forgalmat bárhonnan, de virtuális hálózat engedélyezett. A virtuális hálózati szempontból a kötés Event Hubs-névtér egy végpontot az üzenetküldő szolgáltatás a virtuális hálózati alhálózatról egy elkülönített hálózati alagút állítja be.

Ez a privát és elkülönített kapcsolat, az az alhálózat és a megfelelő Event Hubs-névtér értéket a megfigyelhető hálózati cím az az üzenetkezelési szolgáltatás végpontja egy nyilvános IP-címtartomány kötött számítási feladatok között.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet-integráció által engedélyezett speciális biztonsági forgatókönyvek 

Megoldásokat igénylő szoros és elméleti biztonsági, és hogy a virtuális hálózat alhálózataiban, adja meg a szegmentálási a compartmentalized szolgáltatások közötti továbbra is általánosan kell ezeket a veremből a hozzá tartozó szolgáltatások közötti kommunikációs útvonalat.

A környezetben, beleértve a szerepkör végrehajtsa a HTTPS TCP/IP, felett közötti bármely azonnali IP útvonal a hálózati réteg biztonsági rések kiaknázását kockázatát hordozza az fel. Üzenetkezelési szolgáltatások teljesen szigetelt kommunikációs útvonalat, ahol üzeneteket is írja a lemezre, azok tarifacsomagokból a felek biztosítják. Két különböző virtuális hálózatok mindkét kötött ugyanarra az Event Hubs-példányra a számítási feladatok kommunikálhatnak hatékonyan és megbízhatóan keresztül üzenetek, míg a megfelelő hálózati elkülönítési határ integritása.
 
Azt jelenti, hogy a bizalmas felhőmegoldások csak nem férnek hozzá az Azure-szolgáltatás piacvezető megbízható és méretezhető aszinkron üzenetkezelési képességeket, de most használhatnak üzenetkezelés a biztonságos megoldás közötti kommunikációs útvonalat hozzon létre biztonsági kocsiban olyan eleve biztonságosabb, mint bármely társ-társ-kommunikációs móddal, többek között a HTTPS és az egyéb TLS biztonságos szoftvercsatorna-protokollok kezelhető.

## <a name="bind-event-hubs-to-virtual-networks"></a>Az Event Hubs kötést létrehozni a virtuális hálózatokhoz

*A virtuális hálózati szabályok* van a tűzfal biztonsági szolgáltatás, amely szabályozza, hogy az Azure Event Hubs-kiszolgáló egy adott virtuális hálózati alhálózat érkező kapcsolatokat fogad-e.

Event Hubs-névtér kötése egy virtuális hálózatot két lépésből áll. Először hozzon létre egy **virtuális hálózati szolgáltatásvégpont** egy virtuális hálózat alhálózatán, és engedélyezze a Microsoft.eventhub"", azt mutatjuk be a [szolgáltatási végpont áttekintő] [ vnet-sep]. Miután hozzáadta a szolgáltatásvégpont, azt a kötést létrehozni az Event Hubs-névtér egy *virtuális hálózati szabályt*.

A virtuális hálózati szabályt egy elnevezett társítást az Event Hubs-névtér egy virtuális hálózat alhálózatához. A szabály létezik, míg az alhálózat kötött összes számítási feladatokhoz, amelyekhez hozzáférést az Event Hubs-névtér. Az Event Hubs maga soha nem kimenő kapcsolatot hoz létre, nem kell hozzáférést, és ezért soha nem kap hozzáférést az alhálózat szerint ez a szabály engedélyezése.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Hozzon létre egy virtuális hálózati szabályt az Azure Resource Manager-sablonok

A következő Resource Manager-sablon lehetővé teszi, hogy egy virtuális hálózati szabályt ad hozzá egy meglévő Event Hubs-névtér.

Sablon paraméterei:

* **namespaceName**: Event Hubs-névtér.
* **vnetRuleName**: létrehozni a virtuális hálózati szabály nevét.
* **virtualNetworkingSubnetId**: teljesen minősített erőforrás-kezelő elérési útját a virtuális hálózat alhálózatához; például `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` az alapértelmezett alhálózat egy virtuális hálózat.

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "vnetRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "virtualNetworkSubnetId":{  
             "type":"string",
             "metadata":{  
                "description":"subnet Azure Resource Manager ID"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('vnetRuleName'))]",
            "type":"Microsoft.EventHub/namespaces/VirtualNetworkRules",         
            "properties": {             
                "virtualNetworkSubnetId": "[parameters('virtualNetworkSubnetId')]"  
            }
        } 
    ]
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