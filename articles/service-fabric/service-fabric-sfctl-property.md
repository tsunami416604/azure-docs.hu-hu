---
title: Az Azure Service Fabric CLI- sfctl tulajdonság
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A tulajdonságok tárolására és lekérdezésére vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a9bd75e0b7f8bfceb50a71ca83b60ff1e7b45508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905831"
---
# <a name="sfctl-property"></a>sfctl-tulajdonság
A Service Fabric-nevek alatt tárolja és lekérdezi a tulajdonságokat.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli a megadott Service Fabric tulajdonságot. |
| get | Letöltődik a megadott Service Fabric tulajdonság. |
| lista | Információt kap az összes Service Fabric-tulajdonságok egy adott név alatt. |
| Tesz | Létrehoz vagy frissít egy Service Fabric tulajdonságot. |

## <a name="sfctl-property-delete"></a>sfctl tulajdonság törlése
Törli a megadott Service Fabric tulajdonságot.

Törli a megadott Service Fabric tulajdonságot egy adott név alatt. A tulajdonságot a törlés előtt létre kell hozni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --név-azonosító [Kötelező] | A Service Fabric neve,\:a "fabric" URI-séma nélkül. |
| --tulajdonság neve [Kötelező] | Megadja a bekéselendő tulajdonság nevét. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-property-get"></a>sfctl ingatlan kap
Letöltődik a megadott Service Fabric tulajdonság.

Letöltődik a megadott Service Fabric tulajdonság egy adott név alatt. Ez mindig értéket és metaadatokat is visszaad.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --név-azonosító [Kötelező] | A Service Fabric neve,\:a "fabric" URI-séma nélkül. |
| --tulajdonság neve [Kötelező] | Megadja a bekéselendő tulajdonság nevét. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-property-list"></a>sfctl tulajdonságlista
Információt kap az összes Service Fabric-tulajdonságok egy adott név alatt.

A Service Fabric-név rendelkezhet egy vagy több elnevezett tulajdonságok, amelyek egyéni adatokat tárolnak. Ez a művelet egy lapozható listában kapja meg ezeket a tulajdonságokat. Az adatok tartalmazzák az egyes tulajdonságok nevét, értékét és metaadatait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --név-azonosító [Kötelező] | A Service Fabric neve,\:a "fabric" URI-séma nélkül. |
| --folytatás-token | A folytatási token paraméter az eredmények következő készletének elérésére szolgál. A folytatási jogkivonat egy nem üres érték szerepel az API válasza, ha a rendszer eredményei nem férnek el egyetlen válasz. Ha ezt az értéket a következő API-hívás, az API adja vissza a következő eredményhalmaz. Ha nincsenek további eredmények, akkor a folytatási jogkivonat nem tartalmaz értéket. Ennek a paraméternek az értékét nem szabad URL-kódolásra kódolni. |
| --include-értékek | Megadja, hogy a visszaadott tulajdonságok értékeit tartalmazza-e. Igaz, ha értékeket kell visszaadni a metaadatokkal; Hamis csak a tulajdonság metaadatainak visszaadásához. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-property-put"></a>sfctl ingatlan fel
Létrehoz vagy frissít egy Service Fabric tulajdonságot.

Létrehozza vagy frissíti a megadott Service Fabric tulajdonságot egy adott név alatt.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --név-azonosító [Kötelező] | A Service Fabric neve,\:a "fabric" URI-séma nélkül. |
| --tulajdonság neve [Kötelező] | A Service Fabric tulajdonság neve. |
| --érték [Kötelező] | A Service Fabric tulajdonságértékét ismerteti. Ez egy JSON karakterlánc. <br><br> A json karakterlánc két mezőből rendelkezik: az adatok "Fajtája" és az adatok "Adatok" néven megadott értéke. A "Kind" értéknek kell lennie az első elemnek, amely megjelenik a JSON-karakterláncban, és lehet "Bináris", "Int64", "Dupla", "Karakterlánc" vagy "Guid" érték. Az értéknek szerializálhatónak kell lennie az adott típusokhoz. Mind a "Kind", mind az "Adat" értéket karakterláncként kell megadni. |
| --egyéni azonosító-típus | A tulajdonság egyéni típusazonosítója. Ezzel a tulajdonsággal a felhasználó megjelölheti a tulajdonság értékének típusát. |
| --idő-out -t | Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI.Set up the Service Fabric CLI.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.