---
title: Azure Data Factory entitások elnevezésére vonatkozó szabályok
description: Leírja Data Factory entitások elnevezési szabályait.
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
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837834"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – elnevezési szabályok
Az alábbi táblázat a Data Factory összetevők elnevezési szabályait tartalmazza.

| Név | Név egyedisége | Ellenőrzés ellenőrzése |
|:--- |:--- |:--- |
| Data Factory |Egyedi a Microsoft Azure között. A nevek kis-és nagybetűket nem megkülönböztetőek, azaz `MyDF` és `mydf` ugyanarra az adat-előállítóra vonatkoznak. |<ul><li>Minden egyes adatfeldolgozó pontosan egy Azure-előfizetéshez van kötve.</li><li>Az objektumok nevének betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjel (-) karaktert tartalmazhat.</li><li>Minden kötőjel (-) karaktert közvetlenül előtt kell megadni, majd betűvel vagy számmal kell kiegészíteni. Az egymást követő kötőjelek nem engedélyezettek a tárolók neveiben.</li><li>A név 3-63 karakter hosszú lehet.</li></ul> |
| Társított szolgáltatások/adatkészletek/folyamatok |Egyedi a-ben egy adatelőállítóban. A nevek nem megkülönböztetik a kis-és nagybetűket. |<ul><li>Az objektumok nevének betűvel, számmal vagy aláhúzással (_) kell kezdődnie.</li><li>A következő karakterek nem engedélyezettek: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li><li>A kötőjelek ("-") nem engedélyezettek a társított szolgáltatások és az adatkészletek nevében.</li></ul>  |
| Erőforráscsoport |Egyedi a Microsoft Azure között. A nevek nem megkülönböztetik a kis-és nagybetűket. | További információ: [Azure elnevezési szabályok és korlátozások](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan hozhat létre adatgyárat a gyors üzembe helyezési útmutató lépéseit követve [: hozzon létre egy adatfeldolgozási](quickstart-create-data-factory-powershell.md) cikket. 
