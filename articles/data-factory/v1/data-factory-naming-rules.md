---
title: Azure Data Factory entitások elnevezésére vonatkozó szabályok
description: Leírja Data Factory entitások elnevezési szabályait.
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
ms.openlocfilehash: 8fa1340b586434bf98d51437d4dc6b08594f0afa
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931889"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – elnevezési szabályok
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a Data Factory elnevezési szabályait](../naming-rules.md).

Az alábbi táblázat a Data Factory összetevők elnevezési szabályait tartalmazza.

| Név | Név egyedisége | Ellenőrzés ellenőrzése |
|:--- |:--- |:--- |
| Data Factory |Egyedi a Microsoft Azure között. A nevek kis-és nagybetűket nem megkülönböztetőek, azaz `MyDF` és `mydf` ugyanarra az adat-előállítóra vonatkoznak. |<ul><li>Minden egyes adatfeldolgozó pontosan egy Azure-előfizetéshez van kötve.</li><li>Az objektumok nevének betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjel (-) karaktert tartalmazhat.</li><li>Minden kötőjel (-) karaktert közvetlenül előtt kell megadni, majd betűvel vagy számmal kell kiegészíteni. Az egymást követő kötőjelek nem engedélyezettek a tárolók neveiben.</li><li>A név 3-63 karakter hosszú lehet.</li></ul> |
| Társított szolgáltatások/táblák/folyamatok |Egyedi a-ben egy adatelőállítóban. A nevek nem megkülönböztetik a kis-és nagybetűket. |<ul><li>A tábla nevében szereplő karakterek maximális száma: 260.</li><li>Az objektumok nevének betűvel, számmal vagy aláhúzással (_) kell kezdődnie.</li><li>A következő karakterek nem engedélyezettek: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |
| Erőforráscsoport |Egyedi a Microsoft Azure között. A nevek nem megkülönböztetik a kis-és nagybetűket. |<ul><li>Karakterek maximális száma: 1000.</li><li>A név csak betűket, számokat és a következő karaktereket tartalmazhatja: "-", "_", "," és "."</li></ul> |

