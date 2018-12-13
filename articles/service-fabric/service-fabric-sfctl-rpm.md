---
title: Az Azure Service Fabric parancssori felület sfctl fordulat |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl rpm parancsokat.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: a8ac3899b6ae80668c4472f24b508ba6ba7e2644
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275312"
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
| --feladatazonosítót [kötelező] | A javítási feladat azonosítója. |
| --verzió | A javítási feladat aktuális verzió száma. Ha nem nulla, majd az a kérelem csak sikeres lesz, ha ez az érték megegyezik a javítási feladat tényleges jelenlegi verziójával. Ha nulla, majd nem verzió hajthatók végre. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-rpm-delete"></a>sfctl-rpm törlése
A befejezett javítási feladat törlése.

Ez az API támogatja a Service Fabric platformot; azt nem jelenti, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --feladatazonosítót [kötelező] | A törlendő a befejezett javítási feladat azonosítója. |
| --verzió | A javítási feladat aktuális verzió száma. Ha nem nulla, majd az a kérelem csak sikeres lesz, ha ez az érték megegyezik a javítási feladat tényleges jelenlegi verziójával. Ha nulla, majd nem verzió hajthatók végre. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-rpm-list"></a>sfctl rpm listája
A megadott szűrők megfelelő helyreállítási feladatok listáját kéri le.

Ez az API támogatja a Service Fabric platformot; azt nem jelenti, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --executor-filter | A javítási végrehajtó, amelynek igényelt feladatok szerepelnie kell a lista neve. |
| – Állapot-szűrő | Egy, a következő értékek megadásával, mely a feladat állapota bitenkénti-vagy szerepelnie kell a találatok listájában. <br> 1 – létrehozva <br>2 – igényelt  <br>4 – előkészítése  <br>8 – engedélyezett  <br>16 – végrehajtása  <br>32 - visszaállítása  <br>64 - befejeződött |
| --task-id-filter | A javítási feladat azonosító-előtag egyezést kell keresni. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |


## <a name="next-steps"></a>További lépések
- [Állítsa be a](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).