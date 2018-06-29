---
title: Virtuális hálózati szolgáltatási végpont és az Azure Event Hubs szabályok |} Microsoft Docs
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
ms.openlocfilehash: a23e5414cd3c60192badfee65b14c49cd5e96f4e
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036076"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Virtuális hálózati szolgáltatás végpontok használata az Azure Event Hubs

Az integráció az Event hubs [Virtual Network (VNet) Szolgáltatásvégpontok] [ vnet-sep] lehetővé teszi a biztonságos hozzáférés az üzenetkezelési képességeket munkaterhelések esetén, mint a virtuális kötött virtuális gépek hálózatok, a hálózati forgalom elérési mindkét vége titkosítását. 

Legalább egy virtuális hálózati alhálózat szolgáltatásvégpont kell kötni beállítása után már nem a megfelelő Event Hubs névtér bárhonnan forgalmat fogad, de engedélyezett virtuális hálózatok felé. A virtuális hálózati szempontjából az Event Hubs névtér kötése szolgáltatásvégpont konfigurálja az üzenetkezelési szolgáltatás a virtuális hálózati alhálózat egy elszigetelt hálózati alagút.

Az alhálózat és a megfelelő Event Hubs névtér, függetlenül az üzenetkezelő szolgáltatás végpont folyamatban egy nyilvános IP-címtartományban megfigyelhető hálózati címe kötött munkaterhelések magán- és elkülönített kapcsolatát eredménye.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Speciális biztonsági forgatókönyvek szerint virtuális integráció engedélyezve 

Szoros és elméleti biztonsági igénylő, és ahol a virtuális hálózati alhálózat nyújt a compartmentalized szolgáltatások közötti szegmentálást megoldások továbbra is általában kell ezeket a helyiségekben tartozó szolgáltatások közötti kommunikációs útvonala.

Bármely azonnali IP-útvonal között a szegmenst, beleértve a HTTPS átvitelnek TCP/IP, azzal a kockázattal jár, a hálózati réteg biztonsági rések kihasználásának on fel. Üzenetkezelési szolgáltatások teljesen szigetelt kommunikációs útvonala, ahol az üzenetek még íródtak lemezre, mivel azok átmenet felek közötti adja meg. A két különböző virtuális hálózatokon egyaránt kötött Event Hubs ugyanazon munkaterhelések képes kommunikálni a hatékony és megbízhatóan keresztül üzeneteket, amíg a megfelelő hálózati elkülönítési határt integritása.
 
Ez azt jelenti, hogy a biztonsági megoldások nem csak eléréséhez Azure iparágvezető megbízható és méretezhető aszinkron üzenetkezelési képességeket, de most használhatnak üzenetküldési biztonságos megoldás közötti kommunikáció útvonalak létrehozásához bizalmas kocsiban olyan eredendően biztonságosabb, mint bármely társ-társ kommunikációs üzemmódot, beleértve a HTTPS és az egyéb TLS biztonságos szoftvercsatorna protokollok elérhető.

## <a name="bind-event-hubs-to-virtual-networks"></a>Az Event Hubs kapcsolódjon a virtuális hálózatok

*Virtuális hálózati szabályok* a tűzfal biztonsági szolgáltatása, amely meghatározza, hogy az Azure Event Hubs kiszolgáló adott virtuális hálózati alhálózat érkező kapcsolatokat fogad.

Az Event Hubs névtér kötése egy virtuális hálózat két lépésből áll. Először hozzon létre egy **virtuális hálózati szolgáltatási végpont** egy virtuális hálózati alhálózat és a "Microsoft.ServiceBus", tekintse meg a engedélyezése a [végpont – áttekintés] [ vnet-sep]. A szolgáltatásvégpont hozzáadását követően hozzá a kötési az Event Hubs névtér egy *virtuális hálózati szabály*.

A virtuális hálózati szabály egy elnevezett hozzárendelését az Event Hubs névtér a virtuális hálózati alhálózat. A szabály létezik, amíg az alhálózat kötött összes munkaterhelések az Event Hubs névtér hozzáférési engedéllyel. Az Event Hubs maga soha nem kimenő kapcsolatot hoz létre, nem kell ahhoz, hogy hozzáférjenek, és ezért soha nem kap hozzáférést a alhálózati azáltal, hogy ez a szabály.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Virtuális hálózati szabály létrehozása az Azure Resource Manager-sablonok

A következő Resource Manager-sablon lehetővé teszi, hogy a virtuális hálózati szabály hozzáadása egy meglévő Event Hubs-névtérhez.

Sablonparaméterek:

* **namespaceName**: az Event Hubs névtér.
* **vnetRuleName**: létrehozni a virtuális hálózati szabály nevét.
* **virtualNetworkingSubnetId**: erőforrás-kezelő megadott abszolút elérési út a virtuális hálózati alhálózat; például `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` az alapértelmezett alhálózat virtuális hálózat.

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

A sablon telepítéséhez kövesse az utasításokat a [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>További lépések

Virtuális hálózatok kapcsolatos további információkért tekintse meg a következőket:

- [Azure-beli virtuális hálózat Szolgáltatásvégpontok][vnet-sep]
- [Az Azure Event Hubs IP-szűrés][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md