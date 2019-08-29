---
title: A Azure Data Factory entitások elnevezésére vonatkozó szabályok | Microsoft Docs
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
ms.openlocfilehash: 4397f7efdb9ff797375584f5a43af2833a6a2989
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140995"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – elnevezési szabályok
Az alábbi táblázat a Data Factory összetevők elnevezési szabályait tartalmazza.

| Name (Név) | Név egyedisége | Ellenőrzés ellenőrzése |
|:--- |:--- |:--- |
| Data Factory |Egyedi a Microsoft Azure között. A nevek nem megkülönböztetik a kis- `MyDF` és `mydf` nagybetűket, azaz ugyanarra az adat-előállítóra hivatkoznak. |<ul><li>Minden egyes adatfeldolgozó pontosan egy Azure-előfizetéshez van kötve.</li><li>Az objektumok nevének betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjel (-) karaktert tartalmazhat.</li><li>Minden kötőjel (-) karaktert közvetlenül előtt kell megadni, majd betűvel vagy számmal kell kiegészíteni. Az egymást követő kötőjelek nem engedélyezettek a tárolók neveiben.</li><li>A név 3-63 karakter hosszú lehet.</li></ul> |
| Társított szolgáltatások/adatkészletek/folyamatok |Egyedi a-ben egy adatelőállítóban. A nevek nem megkülönböztetik a kis-és nagybetűket. |<ul><li>Az objektumok nevének betűvel, számmal vagy aláhúzással (_) kell kezdődnie.</li><li>A következő karakterek nem engedélyezettek: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li><li>A kötőjelek ("-") nem engedélyezettek a társított szolgáltatások és az adatkészletek nevében.</li></ul>  |
| Erőforráscsoport |Egyedi a Microsoft Azure között. A nevek nem megkülönböztetik a kis-és nagybetűket. | További információ: [Azure elnevezési szabályok és korlátozások](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). |

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan hozhat létre adatgyárat a gyors üzembe helyezési útmutató lépéseit követve [: hozzon létre egy](quickstart-create-data-factory-powershell.md) adatfeldolgozási cikket. 
