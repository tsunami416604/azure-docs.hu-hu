---
title: Az Azure Service Fabric parancssori felület sfctl fordulat |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl rpm parancsokat.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 04080d75042bfa8a07533336936165e0abef051b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664132"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Lekérdezés, és parancsokat küldjön a repair-kezelő szolgáltatás.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| approve-force | Arra kényszeríti a jóváhagyás, az adott helyreállítási feladat. |
| delete | A befejezett javítási feladat törlése. |
| lista | A megadott szűrők megfelelő helyreállítási feladatok listáját kéri le. |

## <a name="sfctl-rpm-approve-force"></a>hagyja jóvá sfctl rpm-kényszerítése
Arra kényszeríti a jóváhagyás, az adott helyreállítási feladat.

Ez az API támogatja a Service Fabric platformot; azt nem jelenti, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --task-id [Required] | A javítási feladat azonosítója. |
| --version | A javítási feladat aktuális verzió száma. Ha nem nulla, majd az a kérelem csak sikeres lesz, ha ez az érték megegyezik a javítási feladat tényleges jelenlegi verziójával. Ha nulla, majd nem verzió hajthatók végre. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-rpm-delete"></a>sfctl-rpm törlése
A befejezett javítási feladat törlése.

Ez az API támogatja a Service Fabric platformot; azt nem jelenti, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --task-id [Required] | A törlendő a befejezett javítási feladat azonosítója. |
| --version | A javítási feladat aktuális verzió száma. Ha nem nulla, majd az a kérelem csak sikeres lesz, ha ez az érték megegyezik a javítási feladat tényleges jelenlegi verziójával. Ha nulla, majd nem verzió hajthatók végre. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-rpm-list"></a>sfctl rpm listája
A megadott szűrők megfelelő helyreállítási feladatok listáját kéri le.

Ez az API támogatja a Service Fabric platformot; azt nem jelenti, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --executor-filter | A javítási végrehajtó, amelynek igényelt feladatok szerepelnie kell a lista neve. |
| --state-filter | Egy, a következő értékek megadásával, mely a feladat állapota bitenkénti-vagy szerepelnie kell a találatok listájában. <br> 1 – létrehozva <br>2 – igényelt  <br>4 – előkészítése  <br>8 – engedélyezett  <br>16 – végrehajtása  <br>32 - visszaállítása  <br>64 - befejeződött |
| --task-id-filter | A javítási feladat azonosító-előtag egyezést kell keresni. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |


## <a name="next-steps"></a>További lépések
- [Állítsa be a](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).