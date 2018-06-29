---
title: Az Azure Event Hubs IP-kapcsolat szűrők |} Microsoft Docs
description: Azure Event hubs adott IP-címekről blokk-kapcsolatokat a szűrési IP-címet használ.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: 425a5b641fbfd2e52e1294c6317b51ff2a584aa3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036084"
---
# <a name="use-ip-filters"></a>IP-szűrők használata

Forgatókönyvek, amelyben az Azure Event Hubs elérhető-e csak bizonyos jól ismert helyek a *IP-szűrő* funkciója lehetővé teszi elutasítása, vagy meghatározott IPv4-címeket származó forgalmat fogad szabályok konfigurálása. Például a ezeknél a címeknél lehet a NAT vállalati átjáró.

## <a name="when-to-use"></a>A következő esetekben használja

Két fontos használja az esetekben, ahol hasznos letilthatja egyes IP-címek a következők az Event Hubs végpontok:

- Az eseményközpontok kell forgalom fogadására csak a megadott IP-címről, és minden más elutasítása. Az Event Hubs használatával például [Azure Express Route] [ express-route] a helyszíni infrastruktúra saját kapcsolatot létrehozni. 
- Az Event Hubs-rendszergazda által azonosított, gyanús IP-címekről érkező forgalom elutasítása kell.

## <a name="how-filter-rules-are-applied"></a>Állapotszűrő szabályok alkalmazása

Az IP-szűrési szabályokat az Event Hubs névtér szinten alkalmazott. Ezért a szabályok vonatkoznak az összes kapcsolat bármely támogatott protokollt használó ügyfelektől.

A kapcsolódási kísérlet egy IP-címről, amely megfelel az Event Hubs névtéren rejecting IP szabály elutasítva, nem engedélyezett. A válasz nem említi az IP-szabály.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a **IP-szűrő** az Event Hubs-portálon rács értéke üres. Ez az alapértelmezett beállítás azt jelenti, hogy az eseményközpont IP-címeket érkező kapcsolatokat fogad. Ez az alapértelmezett beállítás megegyezik egy szabályt, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály értékelése

IP-szűrési szabályokat a rendszer sorrendben alkalmazza, és az első szabály, amely megfelel az IP-cím határozza meg, a elfogadása vagy elutasítása művelet.

Például ha a tartomány 70.37.104.0/24 címek elfogadása vagy elutasítása minden más, a rács lévő első szabály kell fogadnia a cím tartomány 70.37.104.0/24. A következő szabály kell utasítania összes cím a tartomány 0.0.0.0/0 használatával.

> [!NOTE]
> IP-címek elutasítása megakadályozhatják az más Azure-szolgáltatásokhoz (például az Azure Stream Analytics, Azure virtuális gépek vagy az eszköz Explorer, a portálon) az Event Hubs való interakció.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Virtuális hálózati szabályt hoz létre, az Azure Resource Manager-sablonok

A következő Resource Manager-sablon lehetővé teszi, hogy a virtuális hálózati szabály hozzáadása egy meglévő Event Hubs-névtérhez.

Sablonparaméterek:

- **ipFilterRuleName** nem lehet egyedi, nem betűérzékeny, alfanumerikus, mely legfeljebb 128 karakter hosszúságú lehet.
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

A sablon telepítéséhez kövesse az utasításokat a [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>További lépések

Az Event Hubs korlátozási hozzáférést az Azure virtuális hálózatokhoz tekintse meg az alábbi hivatkozásra:

- [Virtuális hálózati szolgáltatás végpontokat az Event Hubs számára][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md