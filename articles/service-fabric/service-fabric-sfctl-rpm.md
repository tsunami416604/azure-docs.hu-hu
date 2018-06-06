---
title: Az Azure Service Fabric CLI - sfctl rpm |} Microsoft Docs
description: A Service Fabric CLI sfctl rpm parancsok ismerteti.
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 5e7cf1b28e8f38f0539e0084e1f745e0dab4dec3
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763612"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Lekérdezi és parancsainak elküldését a repair-kezelő szolgáltatás.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| approve-force | A megadott javítási feladat jóváhagyása kényszeríti. |
| törlés | Törli a befejezett javítása feladatot. |
| lista | Megfelelő a megadott szűrők javítási tevékenységek listájának lekérése. |

## <a name="sfctl-rpm-approve-force"></a>hagyja jóvá sfctl rpm-force
A megadott javítási feladat jóváhagyása kényszeríti.

Ez az API támogatja a Service Fabric-platformról; nem célja, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – a feladat-azonosító [szükséges] | A javítási feladat azonosítója. |
| --verziója | A javítási feladat aktuális verziószáma. Ha nullától eltérő, majd az a kérelem csak sikeres lesz, ha ez az érték megegyezik a javítási feladat a tényleges jelenlegi verziójával. Ha nulla, majd verzió végrehajtani. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm törlése
Törli a befejezett javítása feladatot.

Ez az API támogatja a Service Fabric-platformról; nem célja, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – a feladat-azonosító [szükséges] | A befejezett javítási feladat törlendő azonosítója. |
| --verziója | A javítási feladat aktuális verziószáma. Ha nullától eltérő, majd az a kérelem csak sikeres lesz, ha ez az érték megegyezik a javítási feladat a tényleges jelenlegi verziójával. Ha nulla, majd verzió végrehajtani. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-rpm-list"></a>sfctl rpm listája
Megfelelő a megadott szűrők javítási tevékenységek listájának lekérése.

Ez az API támogatja a Service Fabric-platformról; nem célja, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --executor-filter | A javítási végrehajtó, amelynek az igényelt feladatok szerepelnie kell a lista neve. |
| --Állapot-szűrő | Az eredménylista egy, a következő értékek megadásával, mely feladat állapota bitenkénti – vagy szerepelnie kell. <br> 1 - létrehozása <br>2 - engedte  <br>4 - előkészítése  <br>8 - jóváhagyása  <br>16 - végrehajtása  <br>32 - visszaállítása  <br>64 - befejeződött |
| --task-id-filter | A javítási feladat Azonosítójának előtagja megfeleltethetők. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="next-steps"></a>További lépések
- [Állítson be](service-fabric-cli.md) a Service Fabric CLI.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).