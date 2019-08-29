---
title: A Azure Data Factory entitások elnevezésére vonatkozó szabályok | Microsoft Docs
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
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: fd1cdfac91a48d016857d06d5c9843fcdc4f5d9f
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70139209"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – elnevezési szabályok
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a Data Factory elnevezési szabályait](../naming-rules.md).

Az alábbi táblázat a Data Factory összetevők elnevezési szabályait tartalmazza.

| Name (Név) | Név egyedisége | Ellenőrzés ellenőrzése |
|:--- |:--- |:--- |
| Data Factory |Egyedi a Microsoft Azure között. A nevek nem megkülönböztetik a kis- `MyDF` és `mydf` nagybetűket, azaz ugyanarra az adat-előállítóra hivatkoznak. |<ul><li>Minden egyes adatfeldolgozó pontosan egy Azure-előfizetéshez van kötve.</li><li>Az objektumok nevének betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjel (-) karaktert tartalmazhat.</li><li>Minden kötőjel (-) karaktert közvetlenül előtt kell megadni, majd betűvel vagy számmal kell kiegészíteni. Az egymást követő kötőjelek nem engedélyezettek a tárolók neveiben.</li><li>A név 3-63 karakter hosszú lehet.</li></ul> |
| Társított szolgáltatások/táblák/folyamatok |Egyedi a-ben egy adatelőállítóban. A nevek nem megkülönböztetik a kis-és nagybetűket. |<ul><li>Karakterek maximális száma a táblázat nevében: 260.</li><li>Az objektumok nevének betűvel, számmal vagy aláhúzással (_) kell kezdődnie.</li><li>A következő karakterek nem engedélyezettek: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |
| Erőforráscsoport |Egyedi a Microsoft Azure között. A nevek nem megkülönböztetik a kis-és nagybetűket. |<ul><li>Karakterek maximális száma: 1000.</li><li>A név csak betűket, számokat és a következő karaktereket tartalmazhatja: "-", "_", "," és "."</li></ul> |

