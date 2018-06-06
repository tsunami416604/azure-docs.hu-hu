---
title: Az Azure Service Fabric CLI - sfctl összeállítása |} Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl állítható össze a parancsok.
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
ms.openlocfilehash: cc3d3e35ce3dd457d981dfe9420be765cf9fc45a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763408"
---
# <a name="sfctl-compose"></a>sfctl-összeállítás
Létrehozása, törlése és kezelése a Docker Compose alkalmazások.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás | A Service Fabric hoz létre központi telepítési összeállítása. |
| lista | Lekérdezi a listájának összeállítása a Service Fabric-fürt létrehozott központi telepítések. |
| eltávolítás | Törlések egy meglévő Service Fabric fürt üzembe helyezést állítható össze. |
| status | A Service Fabric lekérdezi információ állítható össze a központi telepítés. |
| Frissítés | Elindít egy új központi telepítés a Service Fabric-fürt frissítése. |
| frissítés-állapot | Lekérdezi a legújabb frissítést a Service Fabric végre részletes telepítési állítható össze. |

## <a name="sfctl-compose-create"></a>sfctl összeállítása létrehozása
A Service Fabric hoz létre központi telepítési összeállítása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] telepítési neve | Az üzemelő példány neve. |
| --fájlútvonal [szükséges] | A tároló Docker Compose fájl elérési útja. |
| --titkosított fázis | Ahelyett, hogy a tároló beállításjegyzék jelszót kér, használjon egy már titkosított hozzáférési kódot. |
| --rendelkezik fázis | A tároló beállításjegyzék jelszót kérni fogja. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |
| --user | A felhasználónév való kapcsolódást tároló. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-compose-list"></a>sfctl állítható össze a listán
Lekérdezi a listájának összeállítása a Service Fabric-fürt létrehozott központi telepítések.

A létrehozott új telepítések vagy a Service Fabric-fürt létrehozása folyamatban állapotát olvassa be. A válasz a neve, állapota és az új központi telepítések egyéb adatait tartalmazza. Nem felelnek meg a központi telepítések a lista lapon, ha egy oldalra, valamint a folytatási kód, amely segítségével a következő oldal adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| ---folytatási | A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású. |
| --max-results | A lapozható lekérdezéseket részeként vissza kell adni az eredmények maximális száma. Ez a paraméter határozza meg, amelyet a felső határa visszaadott eredmények száma. Az eredmények visszaadása kisebb, mint a megadott maximális eredményeket, ha azok nem felelnek meg az üzenetben szereplő max üzenet mérete korlátozások meghatározása a konfigurációban. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezés tartalmaz annyi eredményeket, előfordulhat, hogy elférjen a visszatérési üzenetben. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-compose-remove"></a>sfctl összeállítása eltávolítása
Törlések egy meglévő Service Fabric fürt üzembe helyezést állítható össze.

Törli a meglévő Service Fabric telepítési állítható össze.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] telepítési neve | A központi telepítés identitását. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-compose-status"></a>sfctl összeállítása állapota
A Service Fabric lekérdezi információ állítható össze a központi telepítés.

A Service Fabric-fürt, és amelynek a neve megegyezik a paraméterként megadott létrehozása folyamatban vagy a létrehozott új központi telepítési állapotának beolvasása. A válasz a neve, állapota és egyéb a központi telepítés részleteit tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] telepítési neve | A központi telepítés identitását. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-compose-upgrade"></a>sfctl összeállítása frissítése
Elindít egy új központi telepítés a Service Fabric-fürt frissítése.

Ellenőrzi a megadott frissítési paramétert, és elindítja az üzemelő példány verziófrissítése, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] telepítési neve | Az üzemelő példány neve. |
| --fájlútvonal [szükséges] | A Docker cél elérési útja fájl írása. |
| --default-svc-type-health-map | JSON-NÁ, amely leírja a szolgáltatások állapotának értékeléséhez használt állapotházirend szótár kódolva. |
| --titkosított fázis | Ahelyett, hogy a tároló beállításjegyzék jelszót kér, használjon egy már titkosított hozzáférési kódot. |
| --failure-action | Lehetséges értékek a következők\: "Érvénytelen", "Visszaállítási", "Manual". |
| – force-újraindítás | Újraindítását kényszeríti ki. |
| --rendelkezik fázis | A tároló beállításjegyzék jelszót kérni fogja. |
| --health-check-retry | Állapotfigyelő ellenőrizze újra időkorlát ezredmásodpercben. |
| – rendszerállapot-ellenőrzés-stabil | Állapotának ellenőrzése stabil időtartam ezredmásodpercben. |
| --állapot-ellenőrzés-várakozási | Állapotfigyelő ellenőrzés várakozási időtartama ezredmásodpercben. |
| --replica-set-check | Jelölőnégyzet időtúllépés másodpercben mért frissítési replika beállítása |
| --svc-típus-rendszerállapot-leképezés | JSON-objektumok, amelyek ismertetik a különböző szolgáltatástípusok állapotának értékeléséhez használt házirendek listáját kódolva. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |
| --unhealthy-app | Az engedélyezett maximális százalékos aránya a nem megfelelő alkalmazások mielőtt hibajelzést. <br><br> Ahhoz, hogy 10 %-a nem kifogástalan, hogy az alkalmazások, például ez az érték lenne 10. A százalékos alkalmazásokat, amelyek nem megfelelő állapotú lehet, mielőtt a fürt tekinthető hiba a maximális megengedett százalékát jelenti. Ha százalékos tiszteletben tartják, de legalább egy nem megfelelő alkalmazás, az egészségügyi értékeli – figyelmeztetés. Ez kiszámítása a nem megfelelő alkalmazások száma keresztül alkalmazáspéldányok a fürt teljes száma. |
| --frissítés-tartományi-időtúllépés | A frissítési tartomány időkorlátjának ezredmásodpercben. |
| --frissítés-típusa | Alapértelmezett\: működés közbeni. |
| --frissítés-mód | Lehetséges értékek a következők\: "Érvénytelen", "UnmonitoredAuto", "UnmonitoredManual", "A figyelt".  Alapértelmezett\: UnmonitoredAuto. |
| --frissítés – időtúllépés | Frissítési időkorlát ezredmásodpercben. |
| --user | A felhasználónév való kapcsolódást tároló. |
| --warning-as-error | Figyelmeztetés hibaként a azonos kiegészített kell kezelni. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl állítható össze a frissítés-állapot
Lekérdezi a legújabb frissítést a Service Fabric végre részletes telepítési állítható össze.

Az új központi telepítési frissítés részletei támogatási hibakeresési alkalmazás ügynökállapottal kapcsolatos hibákkal együtt állapotára vonatkozó adatokat ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] telepítési neve | A központi telepítés identitását. |
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