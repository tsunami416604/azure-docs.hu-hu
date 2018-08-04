---
title: Az Azure Service Fabric parancssori felület - sfctl-összeállítás |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl-összeállítás parancsokat.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 3ce0b63c579412d9d8d35b835803becab09f7ef4
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494152"
---
# <a name="sfctl-compose"></a>sfctl-összeállítás
Létrehozása, törlése és a Docker Compose alkalmazások kezeléséhez.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás | Létrehoz egy Service Fabric compose üzemelő példánya. |
| lista | Lekérdezi listájának összeállítása a Service Fabric-fürtben létrehozott központi telepítések. |
| eltávolítás | Törli a meglévő Service Fabric compose üzemelő példánya fürtből. |
| status | A compose üzemelő példánya a Service Fabric információ beolvasása. |
| frissítés | Elindítja a frissíti compose központi telepítés a Service Fabric-fürtben. |
| frissítés – állapot | A compose üzemelő példánya a legújabb frissítés a Service Fabric végrehajtott részleteinek beolvasása. |

## <a name="sfctl-compose-create"></a>sfctl-összeállítás létrehozása
Létrehoz egy Service Fabric compose üzemelő példánya.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] üzembe helyezés neve | Az üzemelő példány neve. |
| --fájlútvonal [kötelező] | A cél a Docker Compose-fájl elérési útja. |
| --titkosított fázis | Ahelyett, hogy a tárolójegyzék jelszavát kéri, használjon egy már a titkosított hozzáférési kódot. |
| --rendelkezik fázis | A tároló-beállításjegyzék jelszó fogja kérni. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |
| --user | Felhasználónév a tárolóregisztrációs adatbázis csatlakozni. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-compose-list"></a>sfctl-összeállítás listája
Lekérdezi listájának összeállítása a Service Fabric-fürtben létrehozott központi telepítések.

Lekérdezi az állapot, a compose üzemelő példányok, amelyek lettek létrehozva, vagy a Service Fabric-fürt létrehozása folyamatban. A válasz tartalmazza a neve, állapota és a compose üzemelő példányok más részleteit. Ha telepítések listáját az oldal nem férnek el, valamint egy folytatási kód, amely a következő lap beolvasásához használható egy oldalnyi találatot ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| ---folytatási kód | A folytatási token paraméter eredmények következő készletét beszerzésére használatos. Az eredményeket a rendszer nem férnek el egyetlen válasz egy folytatási tokent egy nem üres értékkel szerepel az API-válasz. Ha ez az érték átadott, a következő API-hívás az API-t az eredmények tovább készletet ad vissza. Ha nincsenek további eredmények, a folytatási token neobsahuje értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --max-results | A lapozható lekérdezés részeként visszaadandó eredmények maximális száma. Ez a paraméter határozza meg, visszaadott eredmények számának felső határnál. Az eredmények vissza is lehet kisebb, mint a megadott maximális eredményeket, ha azok nem férnek el megfelelően az üzenetek maximális mérete korlátozások az üzenetben a konfigurációban meghatározott. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés annyi eredmények, amelyek illeszkednek az visszaadott üzenet a lehető tartalmazza. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-compose-remove"></a>sfctl-összeállítás eltávolítása
Törli a meglévő Service Fabric compose üzemelő példánya fürtből.

Törli a meglévő Service Fabric compose üzemelő példánya.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] üzembe helyezés neve | Az üzembe helyezés identitását. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-compose-status"></a>sfctl-összeállítás állapota
A compose üzemelő példánya a Service Fabric információ beolvasása.

A compose üzemelő példány létrehozott, vagy éppen létrehozás alatt álló a Service Fabric-fürtöt, és amelynek a neve megegyezik a paraméterként megadott állapotát adja vissza. A válasz tartalmazza a neve, állapota és az üzembe helyezéssel kapcsolatos egyéb részleteket.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] üzembe helyezés neve | Az üzembe helyezés identitását. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-compose-upgrade"></a>sfctl-összeállítás frissítése
Elindítja a frissíti compose központi telepítés a Service Fabric-fürtben.

A megadott frissítési paraméterek érvényesíti, és elindítja az üzemelő példány verziófrissítése, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] üzembe helyezés neve | Az üzemelő példány neve. |
| --fájlútvonal [kötelező] | Elérési út a cél a Docker compose fájlt. |
| --default-svc-type-health-map | JSON kódolású tartalmaz, amely a szolgáltatások állapotának értékeléséhez használt állapotházirend ismerteti. |
| --titkosított fázis | Ahelyett, hogy a tárolójegyzék jelszavát kéri, használjon egy már a titkosított hozzáférési kódot. |
| --failure-action | Lehetséges értékek a következők\: "Érvénytelen", "Visszaállítás" Manual (manuális). |
| --kényszerített újraindítás | Kényszerített újraindítás. |
| --rendelkezik fázis | A tároló-beállításjegyzék jelszó fogja kérni. |
| --health-check-retry | Állapotellenőrzéssel való újrapróbálkozás időkorlátja ezredmásodpercben. |
| – állapot-ellenőrzés – stabil | Állapotának ellenőrzése stabilitásának időtartama ezredmásodpercben. |
| – állapot-ellenőrzés-wait | Állapotellenőrzés várakozási időtartama ezredmásodpercben. |
| --replica-set-check | Replikakészlet frissítési ellenőrzés időkorlátja, másodpercben. |
| --svc-típusa-állapot – térkép | JSON kódolású objektumok, amelyek a különféle szolgáltatástípusokról állapotának értékeléséhez használt házirendek listáját. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |
| --unhealthy-app | A maximálisan engedélyezett sérült alkalmazások százaléka előtt egy hibát jelez. <br><br> Ahhoz, hogy 10 %-a nem megfelelő állapotú, hogy az alkalmazások, például ez az érték lenne 10. A százalékos eltűrt maximális százalékos aránya, amely előtt a fürt hibás lehet nem megfelelő alkalmazások jelöli. Ha a százalékos tiszteletben tartják, de legalább egy sérült alkalmazás, az egészségügyi figyelmeztetés minősül. Ez kiszámítása a nem megfelelő állapotú alkalmazások száma keresztül alkalmazáspéldányok a fürt teljes száma. |
| --frissítés-tartomány-időkorlátja | Frissítési tartomány időkorlátja ezredmásodpercben. |
| --egyedülálló frissítése | Alapértelmezett\: működés közbeni. |
| --frissítés-mód | Lehetséges értékek a következők\: "Érvénytelen", 'UnmonitoredAuto', 'UnmonitoredManual', "Figyelt".  Alapértelmezett\: UnmonitoredAuto. |
| --frissítés – időtúllépés | Frissítési időkorlát ezredmásodpercben. |
| --user | Felhasználónév a tárolóregisztrációs adatbázis csatlakozni. |
| --warning-as-error | Figyelmeztetések az azonos súlyossági hibákként kell kezelni. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl-összeállítás frissítés – állapot
A compose üzemelő példánya a legújabb frissítés a Service Fabric végrehajtott részleteinek beolvasása.

A compose üzemelő példány frissítése is tartalmaz, ezzel elősegítve a hibakeresési alkalmazás állapotbeli problémák állapota információt ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] üzembe helyezés neve | Az üzembe helyezés identitását. |
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
- [Állítsa be a](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).