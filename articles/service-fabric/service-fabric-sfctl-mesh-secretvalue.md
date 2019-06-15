---
title: Az Azure sfctl háló secretvalue Service Fabric parancssori felület - |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl háló secretvalue parancsokat.
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
ms.openlocfilehash: 3f8e46f063d3e725e2174fd907169f3e0167586a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60836945"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh titkos kódjának értéke
GET és delete háló secretvalue erőforrásokat.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | A megadott értéket az elnevezett titkos erőforrás törlése. |
| list | Az összes érték a megadott titkos erőforrás nevét. |
| Show | Kérje le az értéket, a titkos erőforrás adott verzióját. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl-háló secretvalue delete
A megadott értéket az elnevezett titkos erőforrás törlése.

Törli a titkos érték erőforrás nevével. Az erőforrás neve általában értékkel rendelkező verzióját. Törlés sikertelen lesz, ha a megadott érték használatban van.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – titkos kulcs-name - n [kötelező] | A titkos erőforrás neve. |
| --verzió - v [kötelező] | A titkos kód verziója neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl-háló secretvalue listája
Az összes érték a megadott titkos erőforrás nevét.

A megadott titkos erőforrás az összes titkos érték erőforrás adatainak beolvasása. Az információ tartalmazza a titkos érték erőforrásokat, de nem a tényleges értékek nevei.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – titkos kulcs-name - n [kötelező] | A titkos erőforrás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl-háló secretvalue show
Kérje le az értéket, a titkos erőforrás adott verzióját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – titkos kulcs-name - n [kötelező] | A titkos erőforrás neve. |
| --verzió - v [kötelező] | A titkos kód verziója neve. |
| --show-value | A titkos kód verziója a tényleges értéket jeleníti meg. |

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