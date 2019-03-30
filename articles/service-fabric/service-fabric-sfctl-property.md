---
title: Az Azure Service Fabric parancssori felület - sfctl tulajdonság |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl tulajdonság parancsokat.
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
ms.openlocfilehash: 54cb9f604e9d1b817947990e657390387df6c881
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664913"
---
# <a name="sfctl-property"></a>sfctl-tulajdonság
A Service Fabric neve Store és a lekérdezés tulajdonságai.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli a megadott Service Fabric-tulajdonságot. |
| Get | Lekérdezi a megadott Service Fabric-tulajdonságot. |
| lista | Az összes Service Fabric-tulajdonság a megadott név alatt információkat kér le. |
| PUT | Létrehozza vagy frissíti a Service Fabric-tulajdonság. |

## <a name="sfctl-property-delete"></a>sfctl-tulajdonság delete
Törli a megadott Service Fabric-tulajdonságot.

Törli a megadott Service Fabric-tulajdonság a megadott név alatt. A tulajdonság azt törlése előtt kell létrehozni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --névazonosító [kötelező] | A Service Fabric nélkül adja a "fabric\:" URI-séma. |
| – [kötelező] tulajdonság neve | Meghatározza a lekérdezni a tulajdonság nevét. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-property-get"></a>sfctl-tulajdonság lekérése
Lekérdezi a megadott Service Fabric-tulajdonságot.

Lekérdezi a megadott Service Fabric-tulajdonság a megadott név alatt. Ez mindig ad vissza értéket és a metaadatok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --névazonosító [kötelező] | A Service Fabric nélkül adja a "fabric\:" URI-séma. |
| – [kötelező] tulajdonság neve | Meghatározza a lekérdezni a tulajdonság nevét. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-property-list"></a>sfctl-tulajdonságlista
Az összes Service Fabric-tulajdonság a megadott név alatt információkat kér le.

A Service Fabric neve nem lehet egy vagy több elnevezett tulajdonságok, egyéni információkat tároló. A művelet beolvassa ezeket a tulajdonságokat az információ a lapozható listáját. Az információk közé tartozik a név, érték és metaadatokat egyes tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --névazonosító [kötelező] | A Service Fabric nélkül adja a "fabric\:" URI-séma. |
| --continuation-token | A folytatási token paraméter eredmények következő készletét beszerzésére használatos. Az eredményeket a rendszer nem férnek el egyetlen válasz egy folytatási tokent egy nem üres értékkel szerepel az API-válasz. Ha ez az érték átadott, a következő API-hívás az API-t az eredmények tovább készletet ad vissza. Ha nincsenek további eredmények, a folytatási token neobsahuje értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --include-values | Lehetővé teszi, hogy használjanak-e a visszaadott tulajdonságának értékét tartalmazza. IGAZ, ha az értékek a rendszer visszalépteti a metaadatokkal; Hamis csak tulajdonság metaadatokat visszaadni. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| --verbose | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-property-put"></a>sfctl-tulajdonság put
Létrehozza vagy frissíti a Service Fabric-tulajdonság.

Létrehozza vagy frissíti a megadott Service Fabric-tulajdonság a megadott név alatt.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --névazonosító [kötelező] | A Service Fabric nélkül adja a "fabric\:" URI-séma. |
| – [kötelező] tulajdonság neve | A Service Fabric-tulajdonság neve. |
| – [kötelező] érték | Ismerteti a Service Fabric-tulajdonság értéke. Ez a JSON-karakterláncot. <br><br> A json-karakterláncot tartalmaz két mezőt, a "Kind" az adatok és a "Value" az adatok. A "Kind" értéket kell lennie az első elem jelenik meg a JSON-karakterlánc, és értékek "Binary", "Int64", "Double", "String" vagy "Guid" lehet. Az érték kell tudni szerializálni – a megadott típusok. "Kind" és a "Adatok" értékeket karakterláncként kell megadni. |
| --custom-id-type | A tulajdonság egyedi azonosítója. Ezt a tulajdonságot használja, a felhasználó nem felcímkézheti a tulajdonság értékének típusa. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

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