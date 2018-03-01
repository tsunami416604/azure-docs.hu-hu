---
title: "Az Azure Service Fabric CLI - sfctl fürt |} Microsoft Docs"
description: "A Service Fabric CLI sfctl fürt parancsok ismerteti."
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
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: c83dc3eeb6ca0d66b0c70236354fd7bab80f355f
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-cluster"></a>sfctl-fürt
Válassza ki, kezelése és a Service Fabric-fürtök működik.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
|    code-versions| A Service Fabric-fürt kiépített kód verziók háló listájának lekérése.|
|    config-versions | A Service Fabric-fürt kiépített config verziója háló listájának lekérése.|
|    állapot       | Lekérdezi a Service Fabric-fürt állapotát.|
|    Manifest     | A Service Fabric-fürt jegyzékfájl beolvasása.|
|    művelet – Mégse| A felhasználó által előidézett tartalék művelet visszavonása.|
|    operationgit | Lekérdezi a megadott adatok alapján szűrt felhasználói által előidézett tartalék műveletek listáját.|
|    Kiépítés     | A kódban, illetve konfigurációs csomagokat a Service Fabric-fürt telepítéséhez.|
|    recover-system  | A Service Fabric-fürt jelzi, hogy azt kell megpróbálja helyreállítani a rendszer szolgáltatások, amelyek jelenleg a kvórum elvesztése marad.|
|report-health   | A Service Fabric-fürt állapotát jelentést küld.|
|    kiválasztás       | A kapcsolatot a Service Fabric-fürt végpontja.|
| Unprovision     | Leépíteni a következőt: a kódban, illetve konfigurációs csomagokat a Service Fabric-fürt.|
|    Frissítés         | Indítsa el a Service Fabric-fürt kód vagy a konfigurációs verzió frissítése.|
|    frissítés-folytatása  | Ellenőrizze a fürt frissítése, lépjen tovább a következő frissítési tartományra.|
|    frissítés-visszaállítás| Állítsa vissza a Service Fabric-fürt frissítését.|
|    frissítés-állapot  | Lekérdezi az aktuális Fürtfrissítés előrehaladását.|
|frissítés – frissítés  | A Service Fabric-fürtök frissítése frissítési paramétereinek frissítése.|


## <a name="sfctl-cluster-health"></a>sfctl fürt állapotát
Lekérdezi a Service Fabric-fürt állapotát.

Lekérdezi a Service Fabric-fürt állapotát. Az EventsHealthStateFilter használatával szűrhetők a fürt állapota alapján jelentett állapotával kapcsolatos események gyűjtése. Ehhez hasonlóan szűréséhez használja a NodesHealthStateFilter és az ApplicationsHealthStateFilter csomópont, és az összesített állapota alapján visszaadott alkalmazások gyűjteménye. 

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --applications-health-state-filter| Az alkalmazás állapotát állapota objektumok tért vissza a állapota alapján a fürt állapotának lekérdezés eredménye szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a tagokat, vagy bitenkénti műveletek HealthStateFilter számbavétel tagjainak egész érték. Csak olyan alkalmazások, a szűrőnek megfelelő adja vissza.  Összesített állapotát értékeléséhez használt összes alkalmazást. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző alapú enumerálása, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd OK (2) és a figyelmeztető (4) a HealthState értékét az alkalmazások állapotának is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés. Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535.|
| --events-health-state-filter   | A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző alapú számbavételi, ezért az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés.  Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535.|
|--exclude-health-statistics                   | Azt jelzi, hogy a health statisztika vissza kell adni az a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a hány gyermeke lehet entitások állapota Ok, figyelmeztetés és hiba.|
 |   --include-system-application-health-statistics| Azt jelzi, hogy a állapotstatisztika tartalmaznia kell a fabric: / System alkalmazás állapotának statisztikáit. Alapértelmezés szerint FALSE. Ha IncludeSystemApplicationHealthStatistics értéke igaz, az egészségügyi statisztika közé tartozik a tartozó entitások is szerepelnek a háló: / System alkalmazás. Ellenkező esetben a lekérdezés eredménye tartalmaz egészségügyi statisztikák csak felhasználói alkalmazások esetében. A lekérdezés eredményében alkalmazandó paraméter a állapotstatisztika szerepelnie kell.|
| --nodes-health-state-filter    | A csomópont állapotát állapot objektumok tért vissza a állapota alapján a fürt állapotának lekérdezés eredménye szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a csomópontok a szűrőnek megfelelő adja vissza. Összesített állapotát értékeléséhez használt összes csomópontján. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban "6" majd csomópontok állapota az OK (2) és a figyelmeztető (4) a HealthState értékét is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés.  Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535.|
| – időtúllépés -t                   | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug                        | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                      | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                    | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.                    Alapértelmezett: JSON-ná.|
| --lekérdezés                        | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                      | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-cluster-manifest"></a>a fürtjegyzékben sfctl
A Service Fabric-fürt jegyzékfájl beolvasása.

A Service Fabric-fürt jegyzékfájl beolvasása. A fürtjegyzékben, amelyek tartalmazzák a fürt, a biztonsági beállításokkal, a tartalék és a frissítési tartomány topológiák stb különböző csomóponttípusok a fürt tulajdonságait tartalmazza. Ezek a tulajdonságok vannak megadva a művelet fájl egy önálló fürt telepítése közben. Azonban a legtöbb a a fürtjegyzékben információ van által belsőleg generált a service fabric fürt telepítésekor a központi telepítési forgatókönyvek esetén (például amikor használatával a [Azure-portálon](https://portal.azure.com)). A fürtjegyzékben tartalma csak tájékoztató jellegű, és a felhasználók nem vehet igénybe a függőség tartalmát, vagy a értelmezés formátumát. 

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – időtúllépés -t| Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug  | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h| Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés  | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes| Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-cluster-provision"></a>sfctl fürt létesítése
A kódban, illetve konfigurációs csomagokat a Service Fabric-fürt telepítéséhez.
Ellenőrzi, és jogosultságok kiosztása a kódban, illetve konfigurációs csomagokat a Service Fabric-fürt.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
|--cluster-manifest-file-path| A fürt Alkalmazásjegyzék-fájl elérési útja.|
|    --code-file-path            | A fürt kód csomag fájl elérési útja.|
|    – időtúllépés -t                | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h  | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o| Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes  | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-cluster-select"></a>sfctl fürt kiválasztása
A kapcsolatot a Service Fabric-fürt végpontja.

Ha csatlakozni a biztonságos fürthöz, adja meg (.crt) tanúsítványt és kulcsot tartalmazó fájlt (.key) vagy akár egyetlen fájlt a mindkét (.pem). Nem adható meg egyszerre. Ha egy biztonságos fürt szeretne csatlakozni, megadhat is egy hitelesítésszolgáltató kötegfájl vagy a megbízható CA-tanúsítványok könyvtár elérési útját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] végpont| A fürt végponti URL-cím, beleértve a port és a HTTP vagy HTTPS-előtagot.|
| – aad-ben             | Azure Active Directory használata a hitelesítéshez.|
| --ca              | Hitelesítésszolgáltatói tanúsítványok directory szerint érvényes kezelni vagy CA kötegfájl elérési útja.|
| --tanúsítvány            | Egy ügyfél tanúsítványfájl elérési útja.|
| --kulcs             | Ügyfél kulcs tanúsítványfájl elérési útja.|
| --no-verify       | Tiltsa le a tanúsítványok ellenőrzése a HTTPS használata esetén, vegye figyelembe: Ez egy nem biztonságos beállítás, és nem használható termelési környezetben.|
| --pem             | Az ügyféltanúsítványt, mint a .pem fájl elérési útja.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug           | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h         | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o       | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés           | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes         | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-cluster-unprovision"></a>sfctl fürt unprovision
Leépíteni a következőt: a kódban, illetve konfigurációs csomagokat a Service Fabric-fürt.

Leépíteni a következőt: a kódban, illetve konfigurációs csomagokat a Service Fabric-fürt. Külön-külön leépítése kód és a konfiguráció támogatott.

### <a name="arguments"></a>Argumentumok
|Argumentum|Leírás|
| --- | --- |
|--code-version  | A fürt kód csomag verziója.|
|    --config-version| A fürtjegyzék verziója.|
|    – időtúllépés -t    | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok
|Argumentum|Leírás|
| --- | --- |
|--debug         | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
 |   – Súgó -h       | Ez egy súgóüzenet és kilépési megjelenítése.|
 |   – a kimeneti -o     | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
 |   --lekérdezés         | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
 |   – részletes       | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|


## <a name="sfctl-cluster-upgrade"></a>sfctl fürtök frissítése
Indítsa el a Service Fabric-fürt kód vagy a konfigurációs verzió frissítése.
Érvényesítse a megadott frissítési paramétereket, és indítsa el a Service Fabric-fürt kód vagy a konfigurációs verzió frissítése, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok
|Argumentum|Leírás|
| --- | --- |
|    --app-health-map                      | JSON-kódolású szótár tartalmazó alkalmazás neve és a maximális százalék hiba kiváltása előtt nem kifogástalan.|
 |   --app-type-health-map                 | JSON-kódolású szótár pár alkalmazás nevét, és nem kifogástalan hiba kiváltása előtt maximális százalék.|
 |   --code-version                        | A fürt kód verziója.|
 |   --config-version                      | A fürt konfigurációs verziója.|
 |   --delta-health-evaluation             | Lehetővé teszi, hogy mindegyik frissítési tartományon befejezése után abszolút állapotának kiértékelését helyett különbözeti állapot kiértékelésekor.|
 |   --delta-unhealthy-nodes               | Az engedélyezett maximális százalékos aránya a csomópontok fürt frissítéskor engedélyezett állapotát teljesítménycsökkenést.  Alapértelmezett: 10. A különbözeti frissítés elején a csomópontok állapotát és a csomópontok az állapot kiértékelésekor időpontjában állapotának mérik. Az ellenőrzés minden frissítési tartomány frissítési művelet befejezését követően ellenőrizze, hogy a fürt globális állapotának megengedett határokon belül történik.|
 |   --failure-action                      | Lehetséges értékek a következők: "Érvénytelen", "Visszaállítási", "Manual".|
 |   --force-restart                       | Újraindítását kényszeríti ki.|
 |   --health-check-retry                  | Állapotfigyelő ellenőrizze újra időkorlát ezredmásodpercben.|
 |   – rendszerállapot-ellenőrzés-stabil                 | Állapotának ellenőrzése stabil időtartam ezredmásodpercben.|
  |  --állapot-ellenőrzés-várakozási                   | Állapotfigyelő ellenőrzés várakozási időtartama ezredmásodpercben.|
  |  --replica-set-check-timeout           | Jelölőnégyzet időtúllépés másodpercben mért frissítési replika beállítása|
 |   --rolling-upgrade-mode                | Lehetséges értékek a következők: "Érvénytelen", "UnmonitoredAuto", "UnmonitoredManual", "A figyelt".  Alapértelmezett: UnmonitoredAuto.|
  |  – időtúllépés -t                          | Időtúllépését másodpercben.  Alapértelmezett: 60.|
  |  – a nem megfelelő alkalmazások              | Az engedélyezett maximális százalékos aránya a nem megfelelő alkalmazások mielőtt hibajelzést. Ahhoz, hogy 10 %-a nem kifogástalan, hogy az alkalmazások, például ez az érték lenne 10. A százalékos alkalmazásokat, amelyek nem megfelelő állapotú lehet, mielőtt a fürt tekinthető hiba a maximális megengedett százalékát jelenti. Ha százalékos tiszteletben tartják, de legalább egy nem megfelelő alkalmazás, az egészségügyi értékeli – figyelmeztetés. Ez kiszámítása a nem megfelelő alkalmazások száma a teljes száma a fürtben, alkalmazások, amelyek szerepelnek az applicationtypehealthpolicymap paraméter hiányzó értékei alkalmazástípus kizárásának alkalmazáspéldányok keresztül. A számítási kerekít alkalmazások kis számú hiba működését.|
 |   --unhealthy-nodes                     | Az engedélyezett maximális százalékos aránya nem kifogástalan csomópontokat mielőtt hibajelzést. Ahhoz, hogy 10 % a csomópontok sérült állapotot jelez, például ez az érték lenne 10. A százalékos arányát a csomópontokra, amelyeket nem megfelelő állapotú lehet, mielőtt a fürt tekinthető hiba a maximális megengedett százalékát jelenti. A százalékos tiszteletben tartják, de legalább egy nem megfelelő állapotú csomóponti, ha a rendszerállapot – figyelmeztetés értékeli. Az érték kiszámításához igény szerinti felosztásával sérült csomópontok száma a fürtben található csomópontok száma keresztül. A számítási csomópontok kis számú hiba tűrését kerekít. Nagy fürtök egyes csomópontok mindig lesz le, vagy ki a javításához, így ezen százalékos arány kell konfigurálni, amely elviselni.|
 |   --upgrade-domain-delta-unhealthy-nodes| Az engedélyezett maximális frissítési tartományi csomópontok százaléka fürt frissítéskor engedélyezett állapotát teljesítménycsökkenést. Alapértelmezett: 15. A különbözeti mérik a frissítési tartományi csomópontok frissítés elején állapotát és a frissítési tartományi csomópontok az állapot kiértékelésekor időpontjában állapotát. Az ellenőrzés után minden frissítési tartomány frissítési befejezési összes frissítési tartományt kell győződjön meg arról, hogy a frissítési tartományok állapota megengedett határokon belül történik.|
 |   --upgrade-domain-timeout              | A frissítési tartomány időkorlátjának ezredmásodpercben.|
 |   --frissítés – időtúllépés                     | Frissítési időkorlát ezredmásodpercben.|
 |   --warning-as-error                    | Figyelmeztetés hibaként a azonos kiegészített kell kezelni.|

### <a name="global-arguments"></a>Globális argumentumok
|Argumentum|Leírás|
| --- | --- |
|--debug                               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
|    – Súgó -h                             | Ez egy súgóüzenet és kilépési megjelenítése.|
|    – a kimeneti -o                           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv. Alapértelmezett: JSON-ná.|
|    --lekérdezés                               | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
|    – részletes                             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>További lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).