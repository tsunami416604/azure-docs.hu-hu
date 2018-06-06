---
title: Az Azure Service Fabric CLI - sfctl tároló |} Microsoft Docs
description: A Service Fabric CLI sfctl tároló parancsok ismerteti.
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
ms.openlocfilehash: 39ecf568c5c41c0007b358670af755be1dd5d99e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763238"
---
# <a name="sfctl-store"></a>sfctl-tároló
Alapszintű fájl szint a végre műveleteket a fürt lemezképtárolóhoz.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| törlés | Törli a meglévő lemezkép tartalom tárolásához. |
| root-info | Lekérdezi a tartalommal kapcsolatos információkat az image store gyökérmappájában. |
| stat | Lekérdezi a lemezképet tároló tartalommal kapcsolatos információkat. |

## <a name="sfctl-store-delete"></a>sfctl tároló törlése
Törli a meglévő lemezkép tartalom tárolásához.

Törli a meglévő lemezkép store tartalmát a megadott lemezképben talált relatív elérési út tárolja. Ez a parancs segítségével törölje a feltöltött alkalmazáscsomagok kiépítésüket követően.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --tartalom-path [szükséges] | Relatív elérési útja a fájl vagy mappa az az image store saját gyökere. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-store-root-info"></a>sfctl tároló gyökér-adatai
Lekérdezi a tartalommal kapcsolatos információkat az image store gyökérmappájában.

Az image store információt ad vissza a tartalom az image store gyökérmappájában.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-store-stat"></a>sfctl tároló stat
Lekérdezi a lemezképet tároló tartalommal kapcsolatos információkat.

A megadott contentpath tulajdonsághoz megadott érték a lemezképet tároló tartalmat információt ad vissza. A contentpath tulajdonsághoz megadott érték van az image store gyökeréhez viszonyítva.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --tartalom-path [szükséges] | Relatív elérési útja a fájl vagy mappa az az image store saját gyökere. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="next-steps"></a>További lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).