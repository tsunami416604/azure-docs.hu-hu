---
title: Az Azure Service Fabric CLI - sfctl tulajdonság |} Microsoft Docs
description: A Service Fabric CLI sfctl tulajdonság parancsok ismerteti.
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
ms.openlocfilehash: acade3d828c785af9468baa30086d3b79542f9b7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764014"
---
# <a name="sfctl-property"></a>sfctl tulajdonság
A Service Fabric neve tároló és a lekérdezési tulajdonságok.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| törlés | Törli a megadott Service Fabric-tulajdonságot. |
| Get | Lekérdezi a megadott Service Fabric-tulajdonságot. |
| lista | Lekéri az adatokat az összes Service Fabric-tulajdonság a megadott néven. |
| A PUT | Létrehozza vagy frissíti a Service Fabric-tulajdonságot. |

## <a name="sfctl-property-delete"></a>sfctl tulajdonság törlése
Törli a megadott Service Fabric-tulajdonságot.

Törli a megadott Service Fabric-tulajdonság a megadott néven. A tulajdonság azt törlése előtt létre kell hozni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --névazonosító [szükséges] | A Service Fabric nélkül nevével, a "fabric\:" URI-séma. |
| – [szükséges] tulajdonság neve | Megadja a tulajdonság használatával beolvassa a nevét. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-property-get"></a>sfctl tulajdonság lekérése
Lekérdezi a megadott Service Fabric-tulajdonságot.

Lekérdezi a megadott Service Fabric-tulajdonság a megadott néven. Ez mindig ad vissza értéket és a metaadatok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --névazonosító [szükséges] | A Service Fabric nélkül nevével, a "fabric\:" URI-séma. |
| – [szükséges] tulajdonság neve | Megadja a tulajdonság használatával beolvassa a nevét. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-property-list"></a>sfctl keresésitulajdonság-lista
Lekéri az adatokat az összes Service Fabric-tulajdonság a megadott néven.

A Service Fabric neve lehet egy vagy több elnevezett tulajdonságok egyéni információt tároló. Ez a művelet ezeket a tulajdonságokat a információ lekérése a lapozható listáját. Az információk közé tartozik a nevét, a érték és az egyes tulajdonságok metaadatait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --névazonosító [szükséges] | A Service Fabric nélkül nevével, a "fabric\:" URI-séma. |
| ---folytatási | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --tartalmaznak értékeket | Lehetővé teszi, hogy-e a visszaadott tulajdonság értékét adja meg. Értéke TRUE, ha az értékek vissza kell adni a metaadatok; Csak a tulajdonság metaadatok visszaadását hamis. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-property-put"></a>sfctl tulajdonság put
Létrehozza vagy frissíti a Service Fabric-tulajdonságot.

Létrehozza vagy frissíti a megadott Service Fabric-tulajdonság a megadott néven.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --névazonosító [szükséges] | A Service Fabric nélkül nevével, a "fabric\:" URI-séma. |
| – [szükséges] tulajdonság neve | A Service Fabric-tulajdonság neve. |
| – [szükséges] érték | Ismerteti a Service Fabric tulajdonság értéke. Ez a JSON karakterláncnak. <br><br> A json-karakterláncban van két mező, a "fajta" az adatok és a "Value" az adatok. A "Fajta" érték jelenik meg a JSON-karakterláncban első elemének kell lennie, és értékek "Binary", "Int64", "Double", "String" vagy "Guid" lehet. Az értéket kell szerializálni-képes a megadott típusú lehet. "Fajta" és a "Data" értékeket karakterláncként kell megadni. |
| – egyéni-azonosító-típus | A tulajdonság egyéni írja be az azonosítót. E tulajdonság használatával, a felhasználó nem tudja a tulajdonság értékének típusa címkét. |
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
- [Állítson be](service-fabric-cli.md) a Service Fabric CLI.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).