---
title: "Azure Data Factory entitások elnevezési szabályai |} Microsoft Docs"
description: "Adat-előállító entitások elnevezési szabályainak ismerteti."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: shlo
ms.openlocfilehash: 8f0207853097a3d31e06a722025f3964678414bf
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="azure-data-factory---naming-rules"></a>Az Azure Data Factory - elnevezési szabályok
A következő táblázat elnevezési szabályoknak az adat-előállító összetevők.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás 1 verzióját használja, amely általánosan elérhető (GA) című [elnevezési szabályait a Data Factory version1](v1/data-factory-naming-rules.md).

| Név | Név egyedisége | Érvényességi ellenőrzéseket |
|:--- |:--- |:--- |
| Data Factory |Egyedi Microsoft Azure között. Nevek nem különböztetik meg, ez azt jelenti, hogy `MyDF` és `mydf` adat-előállító hivatkozik. |<ul><li>Minden adat-előállító pontosan egy Azure-előfizetés van kötve.</li><li>Objektumnevek betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és a kötőjel (-) karaktert tartalmazhat.</li><li>Minden kötőjel (-) karaktert legyen azonnal előtt, és betűvel vagy számmal követ. A tároló neve nem szerepelhetnek egymást követő kötőjeleket.</li><li>Neve 3 – 63 karakter hosszú lehet.</li></ul> |
| Szolgáltatások/táblák/folyamatok csatolt |Egyedi az adat-előállítóban. Nevek nem különböztetik meg. |<ul><li>A táblanév maximális karakterszámot: 260.</li><li>Objektumnevek betű, szám vagy aláhúzásjel (_) kell kezdődnie.</li><li>Következő karakterek nem engedélyezettek: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul> |
| Erőforráscsoport |Egyedi Microsoft Azure között. Nevek nem különböztetik meg. |<ul><li>Karakterek maximális száma: 1000.</li><li>Név tartalmazhat betűket, számjegyeket és a következő karaktereket: "-", "_",","és"."</li></ul> |

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan hozzon létre adat-előállítók a részletes insturctions [gyors üzembe helyezés: hozzon létre egy adat-előállító](quickstart-create-data-factory-powershell.md) cikk. 