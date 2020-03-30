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
ms.openlocfilehash: 59cddf04493333b441dcf130d1d99d4fa946748c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837834"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - elnevezési szabályok
Az alábbi táblázat a Data Factory összetevőinek elnevezési szabályait tartalmazza.

| Név | Név egyedisége | Érvényesítési ellenőrzések |
|:--- |:--- |:--- |
| Data Factory |Egyedülálló a Microsoft Azure-ban. A nevek nem különböznek a `MyDF` kis- és nagybetűktől, és `mydf` ugyanarra az adat-előállítóra hivatkoznak. |<ul><li>Minden adatgyár pontosan egy Azure-előfizetéshez van kötve.</li><li>Az objektumneveknek betűvel vagy számmal kell kezdődniük, és csak betűket, számokat és a kötőjel (-) karaktert tartalmazhatják.</li><li>Minden kötőjel (-) karaktert közvetlenül egy betűnek vagy számnak kell megelőznie, majd követnie. A tárolónevekben nem engedélyezettek egymást követő kötőjelek.</li><li>A név 3-63 karakter hosszú lehet.</li></ul> |
| Csatolt szolgáltatások/adatkészletek/folyamatok |Egyedi egy adat-előállítóban. A nevek nem különböznek a kis- és nagybetűktől. |<ul><li>Az objektumneveknek betűvel, számmal vagy aláhúzásjellel (_) kell kezdődniük.</li><li>A következő karakterek nem engedélyezettek: ".",",","?","/", "<", ">","*""%,"&","","\\</li><li>A szaggatott ("-") nem engedélyezett a csatolt szolgáltatások és adatkészletek nevében.</li></ul>  |
| Erőforráscsoport |Egyedülálló a Microsoft Azure-ban. A nevek nem különböznek a kis- és nagybetűktől. | További információ: [Azure-névhasználati szabályok és korlátozások.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) |

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan hozhat létre adatfeldolgozókat az alábbi lépésenkénti útmutatóval [a rövid útmutatóban: hozzon létre egy adatgyári](quickstart-create-data-factory-powershell.md) cikket. 
