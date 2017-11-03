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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: dc2ed59d6adaca97b23dddcb7ec968d90171b483
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-cluster"></a>sfctl fürt
Válassza ki, kezelése és a Service Fabric-fürtök működik.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
|    kód-verziók| A Service Fabric-fürt kiépített kód verziók háló listájának lekérése.|
|    konfiguráció-verziók | A Service Fabric-fürt kiépített config verziója háló listájának lekérése.|
|    Rendszerállapot       | Lekérdezi a Service Fabric-fürt állapotát.|
|    Manifest     | A Service Fabric-fürt jegyzékfájl beolvasása.|
|    művelet – Mégse| A felhasználó által előidézett tartalék művelet visszavonása.|
|    operationgit | Lekérdezi a megadott adatok alapján szűrt felhasználói által előidézett tartalék műveletek listáját.|
|    Kiépítés     | A kódban, illetve konfigurációs csomagokat a Service Fabric-fürt telepítéséhez.|
|    helyreállítás a rendszerhez  | A Service Fabric-fürt jelzi, hogy azt kell megpróbálja helyreállítani a rendszer szolgáltatások, amelyek jelenleg a kvórum elvesztése marad.|
|a jelentés-állapota   | A Service Fabric-fürt állapotát jelentést küld.|
|    Válassza ki       | A kapcsolatot a Service Fabric-fürt végpontja.|
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
| – alkalmazások – rendszerállapot-állapot-szűrő| Lehetővé teszi, hogy az alkalmazás állapotával szűrése
                                                    objects returned in the result of cluster health
                                                    query based on their health state. The possible
                                                    values for this parameter include integer value
                                                    obtained from members or bitwise operations on
                                                    members of HealthStateFilter enumeration. Only
                                                    applications that match the filter are returned.
                                                    All applications are used to evaluate the
                                                    aggregated health state. If not specified, all
                                                    entries are returned. The state values are flag
                                                    based enumeration, so the value could be a
                                                    combination of these values obtained using
                                                    bitwise 'OR' operator. For example, if the
                                                    provided value is 6 then health state of
                                                    applications with HealthState value of OK (2)
                                                    and Warning (4) are returned. - Default -
                                                    Default value. Matches any HealthState. The
                                                    value is zero. - None - Filter that doesn't
                                                    match any HealthState value. Used in order to
                                                    return no results on a given collection of
                                                    states. The value is 1. - Ok - Filter that
                                                    matches input with HealthState value Ok. The
                                                    value is 2. - Warning - Filter that matches
                                                    input with HealthState value Warning. The value
                                                    is 4. - Error - Filter that matches input with
                                                    HealthState value Error. The value is 8. - All -
                                                    Filter that matches input with any HealthState value. The value is 65535.|
| --események – rendszerállapot-Állapotszűrő |} A gyűjtemény által visszaadott HealthEvent objektumok állapota alapján szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a szűrőnek megfelelő események adja vissza. Összes esemény összesített állapotát értékeléséhez használt. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző alapú számbavételi, ezért az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban 6 majd az OK (2) és a figyelmeztető (4) HealthState értékkel rendelkező események is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés.
Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |} |}--kizárási állapotstatisztika |} Azt jelzi, hogy a health statisztika vissza kell adni az a lekérdezés eredménye részeként. Alapértelmezés szerint FALSE. A statisztika megjelenítése a hány gyermeke lehet entitások állapota Ok, figyelmeztetés és hiba. |} |   --tartalmazzák-rendszer-alkalmazás-egészségügyi-statisztikák |} Azt jelzi, hogy a állapotstatisztika tartalmaznia kell a fabric: / System alkalmazás állapotának statisztikáit. Alapértelmezés szerint FALSE. Ha IncludeSystemApplicationHealthStatistics értéke igaz, az egészségügyi statisztika közé tartozik a tartozó entitások is szerepelnek a háló: / System alkalmazás. Ellenkező esetben a lekérdezés eredménye tartalmaz egészségügyi statisztikák csak felhasználói alkalmazások esetében. A health statisztikai adatainak szerepelnie kell a lekérdezés eredményében alkalmazandó paraméter. |} | --csomópontok – rendszerállapot-Állapotszűrő |} A csomópont állapotát állapot objektumok tért vissza a állapota alapján a fürt állapotának lekérdezés eredménye szűrését teszi lehetővé. Ez a paraméter lehetséges értékei közé tartozik a következő állapotok közül az egyik egész értéket. Csak a csomópontok a szűrőnek megfelelő adja vissza. Összesített állapotát értékeléséhez használt összes csomópontján. Ha nincs megadva, a rendszer összes bejegyzés is megjelennek. Az állapot értékei jelző-alapú számbavétel, az érték lehet ezeket az értékeket bitenkénti "Vagy" operátor segítségével. Például ha a megadott érték azonban "6" majd csomópontok állapota az OK (2) és a figyelmeztető (4) a HealthState értékét is megjelennek. -Alapértelmezett - alapértelmezett értéket. Bármely HealthState megegyezik. A tulajdonság értéke nulla. -None - szűrőt, amely nem egyezik a HealthState értékét. Ahhoz, hogy nincs eredményeket egy adott gyűjtemény állapotok használt. Az érték 1. -Ok - szűrheti, hogy egyező bemeneti érték a HealthState az OK gombra. A 2 érték. -A figyelmeztetési - szűrő, hogy egyező bemeneti healthstate értékét figyelmeztetés.
Az érték 4. -Hiba – hiba HealthState értékű bemeneti egyező szűrő. A értéke 8. -Az összes - szűrő, amely megfelel a bemeneti bármely HealthState értékkel. Az érték 65535. |} | – időtúllépés -t |} Időtúllépését másodpercben.  Alapértelmezett: 60. |}

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                        | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
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
| --hibakeresése  | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h| Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés  | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes| Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-cluster-provision"></a>sfctl fürt létesítése
A kódban, illetve konfigurációs csomagokat a Service Fabric-fürt telepítéséhez.

        Validate and provision the code or configuration packages of a Service Fabric cluster.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
|--fürt-jegyzékfájl-fájlútvonal| A fürt Alkalmazásjegyzék-fájl elérési útja.|
|    --kód fájl elérési útja            | A fürt kód csomag fájl elérési útja.|
|    – időtúllépés -t                | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
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
| --hitelesítésszolgáltató              | Hitelesítésszolgáltatói tanúsítványok directory szerint érvényes kezelni vagy CA kötegfájl elérési útja.|
| --tanúsítvány            | Egy ügyfél tanúsítványfájl elérési útja.|
| --kulcs             | Ügyfél kulcs tanúsítványfájl elérési útja.|
| --nem ellenőrzése       | Tiltsa le a tanúsítványok ellenőrzése a HTTPS használata esetén, vegye figyelembe: Ez egy nem biztonságos beállítás, és nem használható termelési környezetben.|
| --pem             | Az ügyféltanúsítványt, mint a .pem fájl elérési útja.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése           | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h         | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o       | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés           | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes         | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-cluster-unprovision"></a>sfctl fürt unprovision
Leépíteni a következőt: a kódban, illetve konfigurációs csomagokat a Service Fabric-fürt.

        Unprovision the code or configuration packages of a Service Fabric cluster.

### <a name="arguments"></a>Argumentumok
|Argumentum|Leírás|
| --- | --- |
|--kód-verzió  | A fürt kód csomag verziója.|
|    --config-verzió| A fürtjegyzék verziója.|
|    – időtúllépés -t    | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok
|Argumentum|Leírás|
| --- | --- |
|--hibakeresése         | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
 |   – Súgó -h       | Ez egy súgóüzenet és kilépési megjelenítése.|
 |   – a kimeneti -o     | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
 |   --lekérdezés         | JMESPath lekérdezési karakterlánc. Lásd: http://jmespath.org/ további információt és
                      Példák.|
 |   – részletes       | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|


## <a name="sfctl-cluster-upgrade"></a>sfctl fürtök frissítése
Indítsa el a Service Fabric-fürt kód vagy a konfigurációs verzió frissítése.

        Validate the supplied upgrade parameters and start upgrading the code or configuration
        version of a Service Fabric cluster if the parameters are valid.

### <a name="arguments"></a>Argumentumok
|Argumentum|Leírás|
| --- | --- |
|    – alkalmazás-rendszerállapot-leképezés                      | Alkalmazásnév pár kódolású JSON szótári és
                                            maximális százalék hiba kiváltása előtt nem kifogástalan.|
 |   – alkalmazás-típus-rendszerállapot-leképezés                 | JSON-kódolású szótár alkalmazástípus pár
                                            name and maximum percentage unhealthy before raising
                                            error.|
 |   --kód-verzió |} A fürt code verziójának. |} |   --config-verzió |} A fürt konfigurációjának verziószáma. |} |   --delta-állapot-kiértékelésekor |} Lehetővé teszi, hogy mindegyik frissítési tartományon befejezése után abszolút állapotának kiértékelését helyett különbözeti állapot kiértékelésekor. |} |   --különbözeti nem kifogástalan csomópontokat |} Az engedélyezett maximális százalékos aránya a csomópontok fürt frissítéskor engedélyezett állapotát teljesítménycsökkenést.  Alapértelmezett: 10.
A különbözeti frissítés elején a csomópontok állapotát és a csomópontok az állapot kiértékelésekor időpontjában állapotának mérik. Az ellenőrzés minden frissítési tartomány frissítési művelet befejezését követően ellenőrizze, hogy a fürt globális állapotának megengedett határokon belül történik. |} |   --Hiba műveleti |} Lehetséges értékek a következők: "Érvénytelen", "Visszaállítási", "Manual". |} |   – kényszerített újraindítás |} Újraindítását kényszeríti ki. |} |   – rendszerállapot-ellenőrzés-újrapróbálkozási |} Állapotfigyelő ellenőrizze újra időkorlát ezredmásodpercben. |} |   – rendszerállapot-ellenőrzés-stabil |} Az állapot-ellenőrzéssel stabil időtartam ezredmásodpercben. |} |  --állapot-ellenőrzés-várakozási |} Az állapot-ellenőrzéssel várakozás időtartama ezredmásodpercben. |} |  – a replika--ellenőrzés-időtúllépés beállítása |} Frissítési replikakészlet ellenőrzése időkorlátja, másodpercben mért. |} |   – működés közbeni frissítés-módú |} Lehetséges értékek a következők: "Érvénytelen", "UnmonitoredAuto", "UnmonitoredManual", "A figyelt".  Alapértelmezett: UnmonitoredAuto. |} |  – időtúllépés -t |} Időtúllépését másodpercben.  Alapértelmezett: 60. |} |  – a nem megfelelő alkalmazások |} Az engedélyezett maximális százalékos aránya a nem megfelelő alkalmazások mielőtt hibajelzést.
Ahhoz, hogy 10 %-a nem kifogástalan, hogy az alkalmazások, például ez az érték lenne 10. A százalékos alkalmazásokat, amelyek nem megfelelő állapotú lehet, mielőtt a fürt tekinthető hiba a maximális megengedett százalékát jelenti. Ha százalékos tiszteletben tartják, de legalább egy nem megfelelő alkalmazás, az egészségügyi értékeli – figyelmeztetés. Ez kiszámítása a nem megfelelő alkalmazások száma a teljes száma a fürtben, alkalmazások, amelyek szerepelnek az applicationtypehealthpolicymap paraméter hiányzó értékei alkalmazástípus kizárásának alkalmazáspéldányok keresztül. A számítási kerekít alkalmazások kis számú hiba elviselni. |} |   --nem kifogástalan csomópontokat |} Az engedélyezett maximális százalékos aránya nem kifogástalan csomópontokat mielőtt hibajelzést.
Ahhoz, hogy 10 % a csomópontok sérült állapotot jelez, például ez az érték lenne 10. A százalékos arányát a csomópontokra, amelyeket nem megfelelő állapotú lehet, mielőtt a fürt tekinthető hiba a maximális megengedett százalékát jelenti. A százalékos tiszteletben tartják, de legalább egy nem megfelelő állapotú csomóponti, ha a rendszerállapot – figyelmeztetés értékeli. Az érték kiszámításához igény szerinti felosztásával sérült csomópontok száma a fürtben található csomópontok száma keresztül. A számítási csomópontok kis számú hiba tűrését kerekít. Nagy fürtök egyes csomópontok mindig lesz le, vagy ki a javításához, így ezen százalékos arány kell konfigurálni, amely elviselni. |} |   --frissítés-tartományi-delta-sérült-csomópontok |} Az engedélyezett maximális frissítési tartományi csomópontok százaléka fürt frissítéskor engedélyezett állapotát teljesítménycsökkenést.
Alapértelmezett: 15.
A különbözeti mérik a frissítési tartományi csomópontok frissítés elején állapotát és a frissítési tartományi csomópontok az állapot kiértékelésekor időpontjában állapotát. Az ellenőrzés után minden frissítési tartomány frissítési létrehozása után az összes befejezett frissítési tartományt kell győződjön meg arról, hogy a frissítési tartományok állapotának megengedett határokon belül történik. |} |   --frissítés-tartományi-timeout |} A frissítési tartomány időkorlátjának ezredmásodpercben. |} |   --frissítés-időtúllépés |} Frissítési időkorlát ezredmásodpercben. |} |   – Figyelmeztetés, hiba |} Figyelmeztetés hibaként azonos súlyossági kell kezelni. |}

### <a name="global-arguments"></a>Globális argumentumok
    |Argumentum|Leírás|
| --- | --- |
|--hibakeresése                               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
|    – Súgó -h                             | Ez egy súgóüzenet és kilépési megjelenítése.|
|    – a kimeneti -o                           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.
                                            Alapértelmezett: JSON-ná.|
|    --lekérdezés                               | JMESPath lekérdezési karakterlánc. Lásd: http://jmespath.org/ további
                                            útmutatást és példákat.|
|    – részletes                             | Naplózási növelése. Használatát – a teljes hibakeresési hibakeresési
                                            naplózza.|

## <a name="next-steps"></a>Következő lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).