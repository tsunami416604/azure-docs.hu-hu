---
title: "Az Azure Service Fabric CLI - sfctl összeállítása |} Microsoft Docs"
description: "Ismerteti a Service Fabric CLI sfctl állítható össze a parancsok."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: 0e35ac70125bc640114a4492498b12ea96800d42
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-compose"></a>sfctl összeállítása
Létrehozása, törlése és a Docker Compose központi telepítések felügyeletéhez szükséges.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
|    létrehozás| Telepítse a Service Fabric-alkalmazás egy új fájlból.|
|    lista  | Lekérdezi a listájának összeállítása a Service Fabric-fürt létrehozott központi telepítések.|
|   eltávolítás| Törlések egy meglévő Service Fabric fürt üzembe helyezést állítható össze.|
|   status| A Service Fabric lekérdezi információ állítható össze a központi telepítés.|
|Frissítés       | Elindít egy új központi telepítés a Service Fabric-fürt frissítése.|
|    frissítés-állapot| Lekérdezi a legújabb frissítést a Service Fabric végre részletes telepítési állítható össze.|


## <a name="sfctl-compose-create"></a>sfctl összeállítása létrehozása
A Service Fabric hoz létre központi telepítési összeállítása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --fájlútvonal [szükséges]| A tároló Docker Compose fájl elérési útja.|
 |   – [szükséges] telepítési neve| Az üzemelő példány neve.|
|    --titkosított fázis             | Ahelyett, hogy a tároló beállításjegyzék jelszót kér, használjon egy már titkosított jelszót.|
|    --rendelkezik fázis                   | A tároló beállításjegyzék jelszót kér.|
|    – időtúllépés -t                 | Kiszolgáló időkorlátja, másodpercben.  Alapértelmezett: 60.|
 |   --user                       | A felhasználónév való kapcsolódást tároló.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                 | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h               | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o             | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                 | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes               | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-compose-list"></a>sfctl állítható össze a listán
Lekérdezi a listájának összeállítása a Service Fabric-fürt létrehozott központi telepítések.

Lekérdezi a feladat állapotát a compose-telepítés hozta létre, vagy a Service Fabric-fürt létrehozása folyamatban van. A válasz a neve, állapota és egyéb az új központi telepítés részleteit tartalmazza. Lapon nem felelnek meg a központi telepítések, ha egy oldalra, valamint a folytatási kód, a következő lap eléréséhez használt adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| ---folytatási| A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat.      Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha nincsenek további eredmények, majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású.|
| --max-results    | A lapozható lekérdezéseket részeként vissza kell adni az eredmények maximális száma.      Ez a paraméter határozza meg, amelyet a felső határa visszaadott eredmények száma.      Ha azok nem felelnek meg az üzenetben szereplő konfigurációjában meghatározott maximális üzenet mérete korlátozások, a visszaadott eredmények lehet kisebb, mint a megadott eredmények maximális száma. Ha ez a paraméter értéke nulla, vagy nincs megadva, a lapozható lekérdezéseket annyi eredmények belefoglalása, előfordulhat, hogy elférjen a visszatérési üzenetben.|
| – időtúllépés -t     | Kiszolgáló időkorlátja, másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug          | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h        | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o      | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés          | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes        | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-compose-remove"></a>sfctl összeállítása eltávolítása
Törlések egy meglévő Service Fabric fürt üzembe helyezést állítható össze.

Törli a meglévő Service Fabric telepítési állítható össze. 

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] telepítési neve| A központi telepítés identitását. Ez általában az nélkül az alkalmazás teljes nevét a "fabric:" URI-séma.|
| – időtúllépés -t            | Kiszolgáló időkorlátja, másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                 | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h               | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o             | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                 | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes               | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-compose-status"></a>sfctl összeállítása állapota
A Service Fabric lekérdezi információ állítható össze a központi telepítés.

Állapotának állítható össze a központi telepítés, amely hozták létre, vagy folyamatban van, a Service Fabric létrehozása a fürt, és amelynek a neve megegyezik a paraméterként megadott értéket ad vissza. A válasz a neve, állapota és az alkalmazás egyéb adatait tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] telepítési neve| A központi telepítés identitását. |
| – időtúllépés -t            | Kiszolgáló időkorlátja, másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                 | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h               | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o             | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés                 | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes               | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-compose-upgrade"></a>sfctl összeállítása frissítése
Elindít egy új központi telepítés a Service Fabric-fürt frissítése.

Ellenőrzi a megadott frissítési paramétert, és elindítja az üzemelő példány verziófrissítése.

### <a name="arguments"></a>Argumentumok
|Argumentum|Leírás|
| --- | --- |
|    --fájlútvonal [szükséges]| A tároló Docker Compose fájl elérési útja.|
|    – [szükséges] telepítési neve| Az üzemelő példány neve.|
|    --default-svc-type-health-map| JSON-NÁ, amely leírja a szolgáltatások állapotának értékeléséhez használt állapotházirend szótár kódolva.|
|    --titkosított fázis             | Ahelyett, hogy a tároló beállításjegyzék jelszót kér, használjon egy már titkosított jelszót.|
 |   --failure-action             | Lehetséges értékek a következők: "Érvénytelen", "Visszaállítási", "Manual".|
|    --force-restart              | Újraindítását kényszeríti ki.|
 |   --rendelkezik fázis                   | A tároló beállításjegyzék jelszót kér.|
|    --health-check-retry         | Állapotfigyelő ellenőrizze újra időkorlátja ezredmásodpercben.|
|    – rendszerállapot-ellenőrzés-stabil        | Állapotának ellenőrzése stabil időtartam ezredmásodpercben.|
|    --állapot-ellenőrzés-várakozási          | Állapotfigyelő ellenőrzés várakozási időtartama ezredmásodpercben.|
|    --replica-set-check          | Frissítési replikakészlet ellenőrzése időkorlátja, másodpercben.|
|    --svc-type-health-map        | JSON-objektumok, amelyek ismertetik a különböző szolgáltatástípusok állapotának értékeléséhez használt házirendek listáját kódolva.|
|    – időtúllépés -t                 | Kiszolgáló időkorlátja, másodpercben.  Alapértelmezett: 60.|
|    --unhealthy-app              | Az engedélyezett maximális százalékos aránya a nem megfelelő alkalmazások mielőtt hibajelzést.        Ahhoz, hogy 10 %-a nem kifogástalan, hogy az alkalmazások, például ez az érték lenne 10. A százalékos alkalmazásokat, amelyek nem megfelelő állapotú lehet, mielőtt a fürt tekinthető hiba a maximális megengedett százalékát jelenti. Ha százalékos tiszteletben tartják, de legalább egy nem megfelelő alkalmazás, az egészségügyi értékeli – figyelmeztetés. Ez a százalékérték nem megfelelő alkalmazások számát elosztjuk keresztül alkalmazáspéldányok a fürt teljes száma.|
|    --upgrade-domain-timeout     | A frissítési tartomány időkorlátja ezredmásodpercben.|
|    --frissítés-típusa               | Alapértelmezett: működés közbeni.|
|    --frissítés-mód               | Lehetséges értékek a következők: "Érvénytelen", "UnmonitoredAuto", "UnmonitoredManual", "A figyelt".  Alapértelmezett: UnmonitoredAuto.|
|    --frissítés – időtúllépés            | Frissítési időkorlát ezredmásodpercben.|
|    --user                       | A felhasználónév való kapcsolódást tároló.|
|    --warning-as-error           | Figyelmeztetés hibaként a azonos kiegészített kell kezelni.|

### <a name="global-arguments"></a>Globális argumentumok
 |Argumentum|Leírás|
| --- | --- |
|   --debug                      | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
|    – Súgó -h                    | Ez egy súgóüzenet és kilépési megjelenítése.|
 |   – a kimeneti -o                  | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.
                                   Alapértelmezett: JSON-ná.|
 |   --lekérdezés                      | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
 |   – részletes                    | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>További lépések
- [Állítson be](service-fabric-cli.md) a Service Fabric CLI.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).