---
title: Virtuális hálózati szolgáltatási végpont és az Azure Service Bus szabályokat |} Microsoft Docs
description: Microsoft.ServiceBus szolgáltatásvégpont hozzáadása egy virtuális hálózathoz.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: 7716ff503bd492cc4b5d510758cb20d74eb82a4f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036221"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Azure Service Bus használata a virtuális hálózati szolgáltatási végpont

A Service Bus integrálása [Virtual Network (VNet) Szolgáltatásvégpontok] [ vnet-sep] biztonságos hozzáférést a munkaterhelések virtuális hálózatok kötött virtuális gépek például lehetővé teszi az üzenetkezelési képességeket , a hálózati forgalom elérési mindkét vége titkosítását. 

Legalább egy virtuális hálózati alhálózat szolgáltatásvégpont kell kötni konfigurálása után a megfelelő Service Bus-névtér nem fogadja el a forgalmat bárhonnan, de engedélyezett virtuális hálózatok felé. A virtuális hálózati szempontjából Service Bus-névtér kötése szolgáltatásvégpont konfigurálja egy elszigetelt hálózati alagutat a virtuális hálózati alhálózat az Üzenetkezelő szolgáltatásnak.

Az alhálózat és a megfelelő Service Bus-névtér, függetlenül az üzenetkezelő szolgáltatás végpont folyamatban egy nyilvános IP-címtartományban megfigyelhető hálózati címe kötött munkaterhelések magán- és elkülönített kapcsolatát eredménye.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Speciális biztonsági forgatókönyvek szerint virtuális integráció engedélyezve 

Szoros és elméleti biztonsági igénylő, és ahol a virtuális hálózati alhálózat nyújt a compartmentalized szolgáltatások közötti szegmentálást megoldások továbbra is általában kell ezeket a helyiségekben tartozó szolgáltatások közötti kommunikációs útvonala.

Bármely azonnali IP-útvonal között a szegmenst, beleértve a HTTPS átvitelnek TCP/IP, azzal a kockázattal jár, a hálózati réteg biztonsági rések kihasználásának on fel. Üzenetkezelési szolgáltatások teljesen szigetelt kommunikációs útvonala, ahol az üzenetek még íródtak lemezre, mivel azok átmenet felek közötti adja meg. Két különböző virtuális hálózatokon egyaránt kötött a Service Bus-példányt munkaterhelések képes kommunikálni a hatékony és megbízhatóan keresztül üzenetek, amíg a megfelelő hálózati elkülönítési határt integritása.
 
Ez azt jelenti, hogy a biztonsági megoldások nem csak eléréséhez Azure iparágvezető megbízható és méretezhető aszinkron üzenetkezelési képességeket, de most használhatnak üzenetküldési biztonságos megoldás közötti kommunikáció útvonalak létrehozásához bizalmas kocsiban olyan eredendően biztonságosabb, mint bármely társ-társ kommunikációs üzemmódot, beleértve a HTTPS és az egyéb TLS biztonságos szoftvercsatorna protokollok elérhető.

## <a name="binding-service-bus-to-virtual-networks"></a>Kötési Service Bus számára, virtuális hálózatok

*Virtuális hálózati szabályok* a tűzfal biztonsági szolgáltatása, amely meghatározza, hogy az Azure Service Bus-kiszolgáló adott virtuális hálózati alhálózat érkező kapcsolatokat fogad.

A Service Bus-névtér kötése egy virtuális hálózat két lépésből áll. Először hozzon létre egy **virtuális hálózati szolgáltatási végpont** egy virtuális hálózati alhálózat és a "Microsoft.ServiceBus", tekintse meg a engedélyezése a [végpont – áttekintés] [ vnet-sep]. Miután hozzáadta a szolgáltatási végpont, azt a kötést létrehozni a Service Bus-névtér egy *virtuális hálózati szabály*.

A virtuális hálózati szabály egy elnevezett hozzárendelését a Service Bus-névtér a virtuális hálózati alhálózat. A szabály létezik, amíg kötve az alhálózat összes munkaterhelések hozzáférhetnek a Service Bus-névtér. A Service Bus maga soha nem kimenő kapcsolatot hoz létre, nem kell ahhoz, hogy hozzáférjenek, és ezért soha nem kap hozzáférést a alhálózati azáltal, hogy ez a szabály.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Virtuális hálózati szabályt hoz létre, az Azure Resource Manager-sablonok

A következő Resource Manager-sablon lehetővé teszi, hogy a virtuális hálózati szabály hozzáadása egy meglévő Service Bus-névtér.

Sablonparaméterek:

* **namespaceName**: Service Bus-névtér.
* **vnetRuleName**: létrehozni a virtuális hálózati szabály nevét.
* **virtualNetworkingSubnetId**: erőforrás-kezelő megadott abszolút elérési út a virtuális hálózati alhálózat; például `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` az alapértelmezett alhálózat virtuális hálózat.

Sablon:

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
            "type":"Microsoft.ServiceBus/namespaces/VirtualNetworkRules",           
            "properties": {             
                "virtualNetworkSubnetId": "[parameters('virtualNetworkSubnetId')]"  
            }
        } 
    ]
}
```

A sablon telepítéséhez kövesse az utasításokat a [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>További lépések

Virtuális hálózatok kapcsolatos további információkért tekintse meg a következőket:

- [Azure-beli virtuális hálózat Szolgáltatásvégpontok][vnet-sep]
- [Az Azure Service Bus IP-szűrés][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md