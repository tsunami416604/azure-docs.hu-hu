---
title: Az Azure Service Fabric parancssori felület - sfctl-tároló |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl store parancsokat.
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
ms.openlocfilehash: 034ae29b5fabae15aa2b6b96e7fefaef23c1c5a1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275227"
---
# <a name="sfctl-store"></a>sfctl-tároló
Hajtsa végre a fürt lemezképtárolójába alapvető szolgáltatói műveletek.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli a meglévő lemezkép tartalom tárolásához. |
| root-info | Lekérdezi a tartalominformációkat az image store gyökérmappájában. |
| állapot | Lekéri a rendszerképet tároló tartalommal kapcsolatos információkat. |

## <a name="sfctl-store-delete"></a>sfctl-tároló törlése
Törli a meglévő lemezkép tartalom tárolásához.

Törli a lemezképet tároló tartalmat a megadott lemezképben található meglévő relatív elérési útját tárolja. Ez a parancs segítségével törölheti a feltöltött alkalmazás csomagokat kiépítésüket követően.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – tartalom-path [kötelező] | Relatív elérési útja fájl vagy mappa a lemezképtároló az médiamappa gyökere. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-store-root-info"></a>sfctl-tároló-gyökérinformációval
Lekérdezi a tartalominformációkat az image store gyökérmappájában.

A lemezképtároló információt ad vissza tartalom az image store gyökérmappájában.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-store-stat"></a>sfctl-tároló stat
Lekéri a rendszerképet tároló tartalommal kapcsolatos információkat.

A lemezképet tároló tartalmat a megadott contentpath értékének információt ad vissza. A contentpath értékének van a lemezképtároló gyökeréhez viszonyítva.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – tartalom-path [kötelező] | Relatív elérési útja fájl vagy mappa a lemezképtároló az médiamappa gyökere. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |


## <a name="next-steps"></a>További lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).