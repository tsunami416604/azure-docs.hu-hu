---
title: Azure Data Factory entitások elnevezési szabályai |} Microsoft Docs
description: Adat-előállító entitások elnevezési szabályainak ismerteti.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: eb4579c2d94ae610d21b7221c0b410b07268d419
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051799"
---
# <a name="azure-data-factory---naming-rules"></a>Az Azure Data Factory - elnevezési szabályok
> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [elnevezési szabályait adat-előállítóban](../naming-rules.md).

A következő táblázat elnevezési szabályoknak az adat-előállító összetevők.

| Name (Név) | Név egyedisége | Érvényességi ellenőrzéseket |
|:--- |:--- |:--- |
| Data Factory |Egyedi Microsoft Azure között. Nevek nem különböztetik meg, ez azt jelenti, hogy `MyDF` és `mydf` adat-előállító hivatkozik. |<ul><li>Minden adat-előállító pontosan egy Azure-előfizetés van kötve.</li><li>Objektumnevek betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és a kötőjel (-) karaktert tartalmazhat.</li><li>Minden kötőjel (-) karaktert legyen azonnal előtt, és betűvel vagy számmal követ. A tároló neve nem szerepelhetnek egymást követő kötőjeleket.</li><li>Neve 3 – 63 karakter hosszú lehet.</li></ul> |
| Szolgáltatások/táblák/folyamatok csatolt |Egyedi az adat-előállítóban. Nevek nem különböztetik meg. |<ul><li>A táblanév maximális karakterszámot: 260.</li><li>Objektumnevek betű, szám vagy aláhúzásjel (_) kell kezdődnie.</li><li>Következő karakterek nem engedélyezettek: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul> |
| Erőforráscsoport |Egyedi Microsoft Azure között. Nevek nem különböztetik meg. |<ul><li>Karakterek maximális száma: 1000.</li><li>Név tartalmazhat betűket, számjegyeket és a következő karaktereket: "-", "_",","és"."</li></ul> |

