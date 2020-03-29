---
title: Az Azure Data Factory-entitások elnevezésére vonatkozó szabályok
description: A Data Factory entitások elnevezési szabályainak ismertetése.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931889"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - elnevezési szabályok
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el [az elnevezési szabályokat a Data Factory alkalmazásban.](../naming-rules.md)

Az alábbi táblázat a Data Factory összetevőinek elnevezési szabályait tartalmazza.

| Név | Név egyedisége | Érvényesítési ellenőrzések |
|:--- |:--- |:--- |
| Data Factory |Egyedülálló a Microsoft Azure-ban. A nevek nem különböznek a `MyDF` kis- és nagybetűktől, és `mydf` ugyanarra az adat-előállítóra hivatkoznak. |<ul><li>Minden adatgyár pontosan egy Azure-előfizetéshez van kötve.</li><li>Az objektumneveknek betűvel vagy számmal kell kezdődniük, és csak betűket, számokat és a kötőjel (-) karaktert tartalmazhatják.</li><li>Minden kötőjel (-) karaktert közvetlenül egy betűnek vagy számnak kell megelőznie, majd követnie. A tárolónevekben nem engedélyezettek egymást követő kötőjelek.</li><li>A név 3-63 karakter hosszú lehet.</li></ul> |
| Csatolt szolgáltatások/táblák/folyamatok |Egyedi egy adat-előállítóban. A nevek nem különböznek a kis- és nagybetűktől. |<ul><li>A táblanévben szereplő karakterek maximális száma: 260.</li><li>Az objektumneveknek betűvel, számmal vagy aláhúzásjellel (_) kell kezdődniük.</li><li>A következő karakterek nem engedélyezettek: ".",",","?","/", "<", ">","*""%,"&","","\\</li></ul> |
| Erőforráscsoport |Egyedülálló a Microsoft Azure-ban. A nevek nem különböznek a kis- és nagybetűktől. |<ul><li>A karakterek maximális száma: 1000.</li><li>A név betűket, számjegyeket és a következő karaktereket tartalmazhatja: "-","_", "" és "."</li></ul> |

