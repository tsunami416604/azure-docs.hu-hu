---
title: Az Azure Data Factory-entitások elnevezésére vonatkozó szabályok
description: A Data Factory entitások elnevezési szabályainak ismertetése.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: f922ada663391cf65a61f4e18bba53668f9c4a1a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419408"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - elnevezési szabályok

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az alábbi táblázat a Data Factory összetevőinek elnevezési szabályait tartalmazza.

| Name (Név) | Név egyedisége | Érvényesítési ellenőrzések |
|:--- |:--- |:--- |
| Data Factory |Egyedülálló a Microsoft Azure-ban. A nevek nem különböznek a `MyDF` kis- és nagybetűktől, és `mydf` ugyanarra az adat-előállítóra hivatkoznak. |<ul><li>Minden adatgyár pontosan egy Azure-előfizetéshez van kötve.</li><li>Az objektumneveknek betűvel vagy számmal kell kezdődniük, és csak betűket, számokat és a kötőjel (-) karaktert tartalmazhatják.</li><li>Minden kötőjel (-) karaktert közvetlenül egy betűnek vagy számnak kell megelőznie, majd követnie. A tárolónevekben nem engedélyezettek egymást követő kötőjelek.</li><li>A név 3-63 karakter hosszú lehet.</li></ul> |
| Csatolt szolgáltatások/adatkészletek/folyamatok |Egyedi egy adat-előállítóban. A nevek nem különböznek a kis- és nagybetűktől. |<ul><li>Az objektumneveknek betűvel, számmal vagy aláhúzásjellel (_) kell kezdődniük.</li><li>A következő karakterek nem engedélyezettek: ".",",","?","/", "<", ">","*""%,"&","","\\</li><li>A szaggatott ("-") nem engedélyezett a csatolt szolgáltatások és adatkészletek nevében.</li></ul>  |
| Erőforráscsoport |Egyedülálló a Microsoft Azure-ban. A nevek nem különböznek a kis- és nagybetűktől. | További információ: [Azure-névhasználati szabályok és korlátozások.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) |

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan hozhat létre adatfeldolgozókat az alábbi lépésenkénti útmutatóval [a rövid útmutatóban: hozzon létre egy adatgyári](quickstart-create-data-factory-powershell.md) cikket. 
