---
title: A Azure Data Factory entitások elnevezési szabályai – 1. verzió
description: Leírja Data Factory v1 entitások elnevezési szabályait.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9a329a9c25b9ea85886938a170eb4194ca84643e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91282595"
---
# <a name="rules-for-naming-azure-data-factory-entities"></a>Azure Data Factory entitások elnevezésére vonatkozó szabályok
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a Data Factory elnevezési szabályait](../naming-rules.md).

Az alábbi táblázat a Data Factory összetevők elnevezési szabályait tartalmazza.

| Név | Név egyedisége | Ellenőrzés ellenőrzése |
|:--- |:--- |:--- |
| Data Factory |Egyedi a Microsoft Azure között. A nevek nem megkülönböztetik a kis-és nagybetűket, azaz `MyDF` `mydf` ugyanarra az adat-előállítóra hivatkoznak. |<ul><li>Minden egyes adatfeldolgozó pontosan egy Azure-előfizetéshez van kötve.</li><li>Az objektumok nevének betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjel (-) karaktert tartalmazhat.</li><li>Minden kötőjel (-) karaktert közvetlenül előtt kell megadni, majd betűvel vagy számmal kell kiegészíteni. Az egymást követő kötőjelek nem engedélyezettek a tárolók neveiben.</li><li>A név 3-63 karakter hosszú lehet.</li></ul> |
| Társított szolgáltatások/táblák/folyamatok |Egyedi a-ben egy adatelőállítóban. A nevek nem megkülönböztetik a kis-és nagybetűket. |<ul><li>A tábla nevében szereplő karakterek maximális száma: 260.</li><li>Az objektumok nevének betűvel, számmal vagy aláhúzással (_) kell kezdődnie.</li><li>A következő karakterek nem engedélyezettek: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":" \\ , ""</li></ul> |
| Erőforráscsoport |Egyedi a Microsoft Azure között. A nevek nem megkülönböztetik a kis-és nagybetűket. |<ul><li>Karakterek maximális száma: 1000.</li><li>A név csak betűket, számokat és a következő karaktereket tartalmazhatja: "-", "_", "," és "."</li></ul> |

