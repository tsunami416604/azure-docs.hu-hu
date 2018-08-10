---
title: Az Azure Event Hubs IP-kapcsolat szűrők |} A Microsoft Docs
description: Használja az IP-szűrés blokk-kapcsolatokat, az adott IP-címek az Azure Event Hubsba.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: 0ecce667584f522b5bd6aac28291bda427f37608
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005352"
---
# <a name="use-ip-filters"></a>IP-szűrők használata

Forgatókönyvek, amelyben az Azure Event Hubs csak érhető el az egyes jól ismert helyről a *IP-szűrő* funkciója lehetővé teszi visszautasítja, vagy a forgalom származik az adott IPv4-címeket fogad szabályok konfigurálása. Például ezek a címek lehet a vállalati hálózati Címfordítás az átjáró.

## <a name="when-to-use"></a>A következő esetekben használja

Két fontos eseteinek is hasznos, ha szeretné az egyes IP-címek az alábbiak szerint az Event Hubs-végpontok letiltása:

- Az event hubs kell forgalom fogadására csak a megadott IP-címről, és elvetheti a minden mást. Az Event Hubs használata esetén például [Azure Express Route] [ express-route] a helyszíni infrastruktúra magánkapcsolatokat hozhat létre. 
- Az Event Hubs-rendszergazda által azonosított gyanús, IP-címekről érkező forgalom elutasítása kell.

## <a name="how-filter-rules-are-applied"></a>Szűrési szabályok alkalmazása

Az IP-szűrési szabályok érvényesek az Event Hubs-névterek szintjén. Ezért a szabályok érvényesek lesznek az összes kapcsolat bármely támogatott protokollt használó ügyfelektől.

Minden kapcsolódási kísérlet IP-címről, amely megfelel az Event Hubs-névtér rejecting IP szabály van elutasítottuk, mert nem engedélyezett. A válasz nem említi az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a **IP-szűrő** rács a portálon az Event Hubs, az üres. Ez az alapértelmezett beállítás, az azt jelenti, hogy az eseményközpont IP-címeket érkező kapcsolatokat fogad-e. Ez az alapértelmezett beállítás megegyezik egy szabályt, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály értékelése

IP-szűrési szabályok a rendszer sorrendben alkalmazza, és az első szabály, amely az IP-cím megegyezik a elfogadása vagy elutasítása műveletet határozza meg.

Például ha azt szeretné, a tartomány 70.37.104.0/24 címeit fogadja el, és minden más elutasítása, az első szabály a rácson a cím-tartományt 70.37.104.0/24 kell fogadnia. A következő szabályt kell utasítania összes címet a tartomány 0.0.0.0/0 használatával.

> [!NOTE]
> IP-címek visszautasítja megakadályozhatja az egyéb Azure-szolgáltatások (például az Azure Stream Analytics, Azure Virtual Machines vagy a portálon a Device Explorer) az Event hubs szolgáltatással való interakcióhoz.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager-sablonokkal a virtuális hálózati szabály létrehozása

A következő Resource Manager-sablon lehetővé teszi, hogy egy virtuális hálózati szabályt ad hozzá egy meglévő Event Hubs-névtér.

Sablon paraméterei:

- **ipFilterRuleName** legfeljebb 128 karakter hosszúságú egyedi, kis-és nagybetűket, alfanumerikus karakterláncnak kell lennie.
- **ipFilterAction** vagy **elutasítása** vagy **elfogadás** a alkalmazni az IP-szűrési szabály művelet.
- **ipMask** egyetlen IPv4-cím vagy IP-címeket a CIDR-jelölésrendszerben egy kódblokkot. Például a CIDR jelölésrendszerben 70.37.104.0/24 jelöli 256 IPv4-címek 70.37.104.0 a 70.37.104.255, az a tartomány jelentős előtag bitek számát jelző 24.

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
          "ipFilterRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "ipFilterAction":{  
             "type":"string",
             "allowedValues": ["Reject", "Accept"],
             "metadata":{  
                "description":"IP Filter Action"
             }
          },
          "IpMask":{  
             "type":"string",
             "metadata":{  
                "description":"IP Mask"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('ipFilterRuleName'))]",
            "type": "Microsoft.EventHub/Namespaces/IPFilterRules",
            "properties": {
                "FilterName":"[parameters('ipFilterRuleName')]",
                "Action":"[parameters('ipFilterAction')]",              
                "IpMask": "[parameters('IpMask')]"
            }
        } 
    ]
}
```

A sablon üzembe helyezéséhez kövesse az utasításokat [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>További lépések

Azure virtuális hálózatok és az Event Hubs korlátozási hozzáférés tekintse meg a következő hivatkozásra:

- [Virtuális hálózati Szolgáltatásvégpontok az Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md