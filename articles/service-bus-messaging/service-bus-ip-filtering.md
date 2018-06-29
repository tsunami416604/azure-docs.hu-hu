---
title: Az Azure Service Bus IP-kapcsolat szűrők |} Microsoft Docs
description: Hogyan használható az IP-szűrés blokk-kapcsolatokat a megadott IP-címekről Azure Service Bus.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: e009bb9120fafc6edf60b68fab3336b9d1add507
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036065"
---
# <a name="use-ip-filters"></a>IP-szűrők használata

A forgatókönyvekben, ahol Azure Service Bus csak bizonyos jól ismert helyeken érhető el a *IP-szűrő* funkciója lehetővé teszi elutasítása, vagy meghatározott IPv4-címeket származó forgalmat fogad szabályok konfigurálása. Például a ezeknél a címeknél lehet a NAT vállalati átjáró.

## <a name="when-to-use"></a>A következő esetekben használja

Két konkrét használati esetekben, ahol hasznos letilthatja egyes IP-címek Service Bus-végpontokat van:

- A Service Bus kell forgalom fogadására csak a megadott IP-címekről, és minden más elutasítása. A Service Bus használata esetén például [Azure Express Route] [ express-route] a helyszíni infrastruktúra saját kapcsolatot létrehozni.
- A Service Bus-rendszergazda által azonosított, gyanús IP-címekről érkező forgalom elutasítása kell.

## <a name="how-filter-rules-are-applied"></a>Állapotszűrő szabályok alkalmazása

Az IP-szűrő szabályok vonatkoznak a Service Bus-névtér szinten. Ezért a szabályok vonatkoznak az összes kapcsolat bármely támogatott protokollt használó ügyfelektől.

A kapcsolódási kísérlet egy IP-címről, amely megfelel a rejecting IP-szabályok a Service Bus-névtér elutasítva, nem engedélyezett. A válasz nem említi az IP-szabály.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a **IP-szűrő** rács a Service Bus-portálon értéke üres. Ez az alapértelmezett beállítás azt jelenti, hogy a névtér kapcsolatokat fogad IP-címeket. Ez az alapértelmezett beállítás megegyezik egy szabályt, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály értékelése

IP-szűrési szabályokat a rendszer sorrendben alkalmazza, és az első szabály, amely megfelel az IP-cím határozza meg, a elfogadása vagy elutasítása művelet.

Például ha a tartomány 70.37.104.0/24 címek elfogadása vagy elutasítása minden más, a rács lévő első szabály kell fogadnia a cím tartomány 70.37.104.0/24. A következő szabály kell utasítania összes cím a tartomány 0.0.0.0/0 használatával.

> [!NOTE]
> IP-címek elutasítása megakadályozhatják az más Azure-szolgáltatásokhoz (például az Azure Stream Analytics, Azure virtuális gépek vagy az eszköz Explorer, a portálon) Service Bus való interakció.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Virtuális hálózati szabályt hoz létre, az Azure Resource Manager-sablonok

A következő Resource Manager-sablon lehetővé teszi, hogy a virtuális hálózati szabály hozzáadása egy meglévő Service Bus-névtér.

Sablonparaméterek:

- **ipFilterRuleName** legfeljebb 128 karakter hosszú egyedi, nem betűérzékeny, alfanumerikus karakterláncnak kell lennie.
- **ipFilterAction** vagy **elutasítása** vagy **elfogadás** az IP-szűrő szabályt alkalmazni kívánt műveletet.
- **ipMask** egy IPv4-cím vagy egy blokkot az IP-címek a CIDR-formátumban van. Például a CIDR jelölésrendszer 70.37.104.0/24 képviseli a 256 IPv4-címeket a 70.37.104.0 70.37.104.255 rendelkező jelző jelentős előtag bit a címtartomány 24.

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

A sablon telepítéséhez kövesse az utasításokat a [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>További lépések

Korlátozási access Azure Service Bus virtuális hálózatok, tekintse meg a következő hivatkozásra:

- [Virtuális hálózati Szolgáltatásvégpontok a Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services