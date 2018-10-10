---
title: Az Azure Service Bus IP-kapcsolat szűrők |} A Microsoft Docs
description: Hogyan használható az IP-szűrés a blokk-kapcsolatokat, az Azure Service Bus adott IP-címről.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: clemensv
ms.openlocfilehash: c6e9eef762d4a9eb95685d94c61ce10d499bb155
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884803"
---
# <a name="use-ip-filters"></a>IP-szűrők használata

Forgatókönyvek, amelyben az Azure Service Bus csak érhető el az egyes jól ismert helyről a *IP-szűrő* funkciója lehetővé teszi visszautasítja, vagy a forgalom származik az adott IPv4-címeket fogad szabályok konfigurálása. Például ezek a címek lehet a vállalati hálózati Címfordítás az átjáró.

## <a name="when-to-use"></a>A következő esetekben használja

Nincsenek két adott használati eseteket is hasznos, ha szeretné a Service Bus-végpontokat bizonyos IP-címek letiltása:

- A Service Bus kell forgalom fogadására csak a megadott IP-címről, és elvetheti a minden mást. A Service Bus használata esetén például [Azure Express Route] [ express-route] a helyszíni infrastruktúra magánkapcsolatokat hozhat létre.
- A Service Bus-rendszergazda által azonosított gyanús, IP-címekről érkező forgalom elutasítása kell.

## <a name="how-filter-rules-are-applied"></a>Szűrési szabályok alkalmazása

Az IP-szűrési szabályok érvényesek a Service Bus-névterek szintjén. Ezért a szabályok érvényesek lesznek az összes kapcsolat bármely támogatott protokollt használó ügyfelektől.

Minden kapcsolódási kísérlet IP-címről, amely megegyezik a Service Bus-névtérben rejecting IP szabály van elutasítottuk, mert nem engedélyezett. A válasz nem említi az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a **IP-szűrő** rács a portálon, a Service Bus az üres. Ez az alapértelmezett beállítás, az azt jelenti, hogy a névtér fogad kapcsolatokat IP-címeket. Ez az alapértelmezett beállítás megegyezik egy szabályt, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály értékelése

IP-szűrési szabályok a rendszer sorrendben alkalmazza, és az első szabály, amely az IP-cím megegyezik a elfogadása vagy elutasítása műveletet határozza meg.

Például ha azt szeretné, a tartomány 70.37.104.0/24 címeit fogadja el, és minden más elutasítása, az első szabály a rácson a cím-tartományt 70.37.104.0/24 kell fogadnia. A következő szabályt kell utasítania összes címet a tartomány 0.0.0.0/0 használatával.

> [!NOTE]
> IP-címek visszautasítja megakadályozhatja az egyéb Azure-szolgáltatások (például az Azure Stream Analytics, Azure Virtual Machines vagy a portálon a Device Explorer) Service Bus szolgáltatással való interakcióhoz.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager-sablonokkal a virtuális hálózati szabály létrehozása

> ! [FONTOS] Csak a virtuális hálózatok támogatottak a **prémium** a Service Bus szintjét.

A következő Resource Manager-sablon lehetővé teszi, hogy egy virtuális hálózati szabályt ad hozzá egy meglévő Service Bus-névteret.

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
            "type": "Microsoft.ServiceBus/Namespaces/IPFilterRules",
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

Omezující vlastnost hozzáférés a Service Bus az Azure virtuális hálózatokhoz lásd a következő hivatkozást:

- [Service Bus számára, virtuális hálózati Szolgáltatásvégpontok][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services