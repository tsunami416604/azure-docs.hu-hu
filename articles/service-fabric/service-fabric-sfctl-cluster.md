---
title: Azure Service Fabric CLI- sfctl-fürt
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A fürtök kezelésére vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 007ad6f59f0ce304db579f4faa1bb95611a93a37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906152"
---
# <a name="sfctl-cluster"></a>sfctl-fürt
Válassza ki, kezelje és kezelje a Service Fabric-fürtöket.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| kódverziók | A Service Fabric-fürtben kiépített hálókód-verziók listájának letöltője. |
| konfigurációs verziók | A Service Fabric-fürtben kiépített konfigurációs verziók listáját kapja. |
| Egészségügyi | Egy Service Fabric-fürt állapotát kapja. |
| manifest | A Service Fabric-fürt jegyzékfájljának beszerezni. |
| művelet-megszakítás | Megszakítja a felhasználó által okozott hibaműveletet. |
| műveletlista | A megadott bemenet által szűrt, a felhasználó által kiváltott hibaműveletek listájának leése. |
| Rendelkezés | A Service Fabric-fürt kód- vagy konfigurációs csomagjainak kiépítése. |
| visszaszerzési rendszer | Azt jelzi a Service Fabric-fürtnek, hogy meg kell próbálnia helyreállítani a kvórumveszteségben jelenleg beragadt rendszerszolgáltatásokat. |
| jelentés-egészségügy | Állapotjelentést küld a Service Fabric-fürtről. |
| Válassza ki | Egy Service Fabric-fürt végponthoz csatlakozik. |
| show-kapcsolat | Annak megjelenítése, hogy melyik Service Fabric-fürthöz csatlakozik az sfctl-példány. |
| kiépítés nélküli | A Service Fabric-fürt kód- vagy konfigurációs csomagjainak kioldása. |
| Frissítés | Indítsa el egy Service Fabric-fürt kódjának vagy konfigurációs verziójának frissítését. |
| frissítés-folytatás | A fürtfrissítés áthelyezése a következő frissítési tartományra. |
| frissítés-visszaállítás | Állítsa vissza a Service Fabric-fürt frissítését. |
| frissítési állapot | Leveszi az aktuális fürtfrissítés folyamatát. |
| frissítés-frissítés | Frissítse a Service Fabric-fürt frissítési paramétereinek frissítési paramétereit. |

## <a name="sfctl-cluster-code-versions"></a>sfctl fürtkód-verziók
A Service Fabric-fürtben kiépített hálókód-verziók listájának letöltője.

A fürtben kiépített hálókód-verziók listájának leképezi. A CodeVersion paraméter segítségével tetszés szerint csak az adott verzióra szűrhető a kimenet.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --kód-verzió | A Service Fabric termékverziója. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-config-versions"></a>sfctl fürt konfigurációs verziói
A Service Fabric-fürtben kiépített konfigurációs verziók listáját kapja.

A fürtben kiépített konfigurációs verziók ra vonatkozó információk listájának lesoka. A ConfigVersion paraméter segítségével a kimenet et csak az adott verzióra szűrheti.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --config-verzió | A Service Fabric konfigurációs verziója. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-health"></a>sfctl fürt állapota
Egy Service Fabric-fürt állapotát kapja.

Az EventsHealthStateFilter használatával szűrheti a fürtön jelentett állapotesemények gyűjteményét az állapot alapján. Hasonlóképpen használja a NodesHealthStateFilter és az ApplicationsHealthStateFilter segítségével az összesített állapotuk alapján visszaadott csomópontok és alkalmazások gyűjteményének szűréséhez.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --alkalmazások-állapot-állapot-szűrő | Lehetővé teszi az alkalmazás állapotállapot-objektumainak szűrését a fürt állapotlekérdezésének eredményeként az állapotuk alapján. A paraméter lehetséges értékei közé tartoznak a HealthStateFilter enumerálási tagoktól kapott egész érték vagy bitenkénti műveletek. Csak a szűrőnek megfelelő alkalmazásokat adja vissza a rendszer. Az összes alkalmazás az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a program az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték lehet ezeknek az értékeknek a kombinációja, amelyeket bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, majd az OK (2) és a Figyelmeztetés (4) HealthState értékkel rendelkező alkalmazások állapotának állapotát adja vissza.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --események-állapot-állapot-szűrő | Lehetővé teszi az állapot alapján visszaadott HealthEvent objektumok gyűjteményének szűrését. A paraméter lehetséges értékei közé tartozik az alábbi állapotok egyikének egész értéke. A rendszer csak a szűrőnek megfelelő eseményeket adja vissza. Az összes esemény az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a program az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték ezeknek az értékeknek a kombinációja lehet, amelyet a bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, akkor az összes eseményt, amelynek HealthState értéke OK (2) és Figyelmeztetés (4) visszaadja.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --kizárás-egészségügyi statisztikák | Azt jelzi, hogy az állapotstatisztikát vissza kell-e adni a lekérdezés eredményének részeként. Alapértelmezés szerint hamis. A statisztikák az Ok, Figyelmeztetés és Hiba állapotban lévő gyermekentitások számát mutatják. |
| --include-system-application-health-statistics | Azt jelzi, hogy az állapotstatisztikának tartalmaznia kell-e a /Rendszeralkalmazás\:állapotstatisztikáját. Alapértelmezés szerint hamis. Ha az IncludeSystemApplicationHealthStatistics értéke igaz, az állapotstatisztika tartalmazza a\:háló /Rendszer alkalmazáshoz tartozó entitásokat. Ellenkező esetben a lekérdezés eredménye csak a felhasználói alkalmazások állapotstatisztikáit tartalmazza. A paraméter alkalmazásához az állapotstatisztikákat bele kell foglalni a lekérdezés eredményébe. |
| --csomópontok-állapot-állapot-szűrő | Lehetővé teszi a fürt állapotlekérdezésének eredményeként visszaadott csomópontállapot-objektumok szűrését az állapotuk alapján. A paraméter lehetséges értékei közé tartozik az alábbi állapotok egyikének egész értéke. A rendszer csak a szűrőnek megfelelő csomópontokat adja vissza. Az összes csomópont az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a program az összes bejegyzést visszaadja. Az állapotértékek jelzőalapú felsorolás, így az érték lehet ezeknek az értékeknek a kombinációja, amelyeket bitenkénti "OR" operátorral kapunk. Ha például a megadott érték 6, majd az OK (2) és a Figyelmeztetés (4) HealthState értékkel rendelkező csomópontok állapota.  <br> - Alapértelmezett - Alapértelmezett érték. Megfelel bármely HealthState.Matches any HealthState. Az érték nulla.  <br> - Nincs - Szűrő, amely nem felel meg semmilyen HealthState érték. Annak érdekében, hogy nem ad vissza eredményt egy adott gyűjtemény állapotok. Az érték 1.  <br> - Ok - Szűrő, amely megfelel a bemenet HealthState érték Ok. Az érték 2.  <br> - Figyelmeztetés - Szűrő, amely megfelel a bemenet healthstate érték Figyelmeztetés. Az érték 4.  <br> - Hiba - A bemenetnek a HealthState értékhibával egyeztetett szűrő. Az érték 8.  <br> - All - Szűrő, amely megfelel a bemenet bármely HealthState érték. Az érték 65535. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-manifest"></a>sfctl fürtjegyzék
A Service Fabric-fürt jegyzékfájljának beszerezni.

A Service Fabric-fürt jegyzékfájljának beszerezni. A fürtjegyzék tartalmazza a fürt tulajdonságait, amelyek különböző csomóponttípusokat tartalmaznak a fürtön, biztonsági konfigurációkat, hibát és frissítési tartomány topológiákat stb. Ezek a tulajdonságok a ClusterConfig.JSON fájl részeként vannak megadva egy önálló fürt telepítése kor. Azonban a fürtjegyzékben lévő információk nagy részét azonban a szolgáltatásháló hozza létre a fürt telepítése során más központi telepítési forgatókönyvekben (például az Azure Portal használatakor). A fürtjegyzék tartalma csak tájékoztató jellegű, és a felhasználóktól nem várható el, hogy a fájl tartalmának formátumától vagy értelmezésétől függjenek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-operation-cancel"></a>sfctl fürt művelet-megszakítás
Megszakítja a felhasználó által okozott hibaműveletet.

A következő API-k olyan hibaműveleteket indítanak\: el, amelyek a StartDataLoss, StartQuorumLoss, StartPartitionRestart és StartNodeTransition művelettel megszakíthatók. Ha az erő hamis, akkor a megadott felhasználó által kiváltott művelet szabályosan leáll és törlődik.  Ha az erő igaz, a parancs megszakad, és néhány belső állapot is hátramarad.  Az erő igazként történő megadását óvatosan kell alkalmazni. Az API true értékre beállított kényszerítéssel történő hívása nem engedélyezett, amíg ez az API-t már meg nem hívták ugyanazon a tesztparancson, amelynek kényszerítése először hamis, vagy ha a tesztparancs már rendelkezik operationstate of OperationState.RollingBack műveletállapottal. Pontosítás\: OperationState.RollingBack azt jelenti, hogy a rendszer lesz / a tisztítási belső rendszer állapota által okozott végrehajtása a parancsot.  Nem állítja vissza az adatokat, ha a tesztparancs adatvesztést okoz.  Ha például a StartDataLoss-t hívja meg, akkor ezt az API-t hívja meg, a rendszer csak a parancs futtatásából törli a belső állapotot. Nem állítja vissza a célpartíció adatait, ha a parancs elég messzire haladt ahhoz, hogy adatvesztést okozzon. Fontos\: megjegyzés, ha ezt az API-t force==true-nal hívja meg, a belső állapot hátramaradhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --művelet-azonosító [Kötelező] | Az API hívását azonosító GUID.  Ez a megfelelő GetProgress API-ba kerül. |
| --erő | Azt jelzi, hogy a rendszer a felhasználó által létrehozott művelet végrehajtásával szabályosan visszaállítja-e és törli-e a belső rendszerállapotot. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-operation-list"></a>sfctl fürt műveletlistája
A megadott bemenet által szűrt, a felhasználó által kiváltott hibaműveletek listájának leése.

Leveszi a felhasználó által kiváltott hibaműveletek listája a megadott bemenet által szűrt.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --állapotszűrő | Az OperationState-műveletek szűrésére szolgál a felhasználó által kiváltott műveletekhez. - 65535 - select All - 1 - select Running - 2 - select RollingBack - 8 - select Completed - 16 - select Faulted - 32 - select Cancelled - 64 - select ForceCancelled.  Alapértelmezett\: 65535. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |
| --típus-szűrő | Az OperationType szűrésére szolgál a felhasználó által kiváltott műveletekhez. - 65535 - válassza ki az összes - 1 - válassza PartitionDataLoss. - 2 - válassza PartitionQuorumLoss. - 4 - válassza PartitionRestart. - 8 - válassza a NodeTransition lehetőséget.  Alapértelmezett\: 65535. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-provision"></a>sfctl-fürt szolgáltatás
A Service Fabric-fürt kód- vagy konfigurációs csomagjainak kiépítése.

A Service Fabric-fürt kód- vagy konfigurációs csomagjainak ellenőrzése és kiépítése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --cluster-manifest-file-path | A fürtjegyzék fájljának elérési útja. |
| --kód-fájl-elérési út | A fürtkódcsomag fájljának elérési útja. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-recover-system"></a>sfctl-fürt visszaállító rendszere
Azt jelzi a Service Fabric-fürtnek, hogy meg kell próbálnia helyreállítani a kvórumveszteségben jelenleg beragadt rendszerszolgáltatásokat.

Azt jelzi a Service Fabric-fürtnek, hogy meg kell próbálnia helyreállítani a kvórumveszteségben jelenleg beragadt rendszerszolgáltatásokat. Ezt a műveletet csak akkor kell végrehajtani, ha ismeretes, hogy a nem működő replikák nem állíthatók helyre. Az API helytelen használata adatvesztést okozhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-report-health"></a>sfctl cluster jelentés-állapot
Állapotjelentést küld a Service Fabric-fürtről.

Állapotjelentést küld egy Service Fabric-fürtről. A jelentésnek tartalmaznia kell az állapotjelentés forrására és a jelentett tulajdonságra vonatkozó információkat. A jelentés egy Service Fabric átjáró csomópont, amely továbbítja a health Store. A jelentést az átjáró elfogadhatja, de az egészségügyi tároló további ellenőrzés után elutasítja. Például a health store elutasíthatja a jelentést, mert egy érvénytelen paraméter, mint például egy elavult sorszám. Ha meg szeretné tudni, hogy a jelentés alkalmazva lett-e a health store-ban, futtassa a GetClusterHealth futtatása, és ellenőrizze, hogy a jelentés megjelenik-e a HealthEvents szakaszban.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --health-property [Kötelező] | Az egészségügyi információk tulajdonsága. <br><br> Egy entitás különböző tulajdonságokállapot-jelentésekkel rendelkezhet. A tulajdonság egy karakterlánc, és nem egy rögzített felsorolás, amely lehetővé teszi az előjelentés rugalmasságát a jelentést kiváltó állapot kategorizálására. Például egy "LocalWatchdog" SourceId azonosítóval rendelkező jelentéskészítő figyelheti a csomóponton rendelkezésre álló lemez állapotát, így jelentheti az "AvailableDisk" tulajdonságot az adott csomóponton. Ugyanaz a jelentéskészítő figyelheti a csomópont-kapcsolatot, így jelentheti a "Kapcsolat" tulajdonságot ugyanazon a csomóponton. Az állapottárolóban ezeket a jelentéseket külön állapoteseményekként kezeli a megadott csomópont. A SourceId-dal együtt a tulajdonság egyedileg azonosítja az állapotinformációkat. |
| --állapot[Kötelező] | A lehetséges\: értékek a következők: "Érvénytelen", "Ok", "Figyelmeztetés", "Hiba", "Ismeretlen". |
| --source-id [Kötelező] | A forrásnév, amely azonosítja az ügyfél/figyelő/rendszerösszetevő, amely létrehozta az állapotadatokat. |
| --leírás | Az egészségügyi információk leírása. <br><br> Szabad szöveget jelöl, amely a jelentéssel kapcsolatos emberi olvasható információk hozzáadására szolgál. A leírás maximális karakterlánchossza 4096 karakter. Ha a megadott karakterlánc hosszabb, a program automatikusan csonkolja. Csonkoláskor a leírás utolsó karakterei a "[Csonka]" jelölőt tartalmazzák, a teljes karakterlánc mérete pedig 4096 karakter. A jelölő jelenléte azt jelzi a felhasználóknak, hogy csonkolás történt. Ne feledje, hogy csonkoláskor a leírás kevesebb mint 4096 karaktert tartalmaz az eredeti karakterláncból. |
| --azonnali | Egy jelző, amely jelzi, hogy a jelentést azonnal el kell-e küldeni. <br><br> Egy állapotjelentés egy Service Fabric átjáró alkalmazás, amely továbbítja a health Store. Ha az Immediate értéke igaz, a rendszer azonnal elküldi a jelentést a HTTP-átjáróból a health Store-ba, függetlenül attól, hogy a HTTP átjáróalkalmazás által használt háló-ügyfél-beállításokat használja.If Immediate is set to true, the report is sent immediately from HTTP Gateway to the health store, regardless of the fabric client settings that the HTTP Gateway Application is using. Ez akkor hasznos, ha a kritikus jelentéseket a lehető leghamarabb el kell küldeni. Az időzítéstől és az egyéb feltételektől függően a jelentés küldése továbbra is sikertelen lehet, például ha a HTTP-átjáró be van zárva, vagy ha az üzenet nem éri el az átjárót. Ha az Immediate értéke hamis, a rendszer a http-átjáró ból származó állapotügyfél-beállítások alapján küldi el a jelentést. Ezért a rendszer a HealthReportSendInterval konfigurációnak megfelelően lesz kötegelve. Ez az ajánlott beállítás, mert lehetővé teszi, hogy az állapotjelző ügyfél optimalizálja az állapotjelentési üzeneteket az állapottárolónak, valamint az állapotjelentés feldolgozását. Alapértelmezés szerint a jelentések nem kerülnek azonnal elküldésre. |
| --remove-when-expired | Érték, amely azt jelzi, hogy a jelentés törlődik-e az állapottárolóból, amikor lejár. <br><br> Ha értéke igaz, a jelentés törlődik a health Store lejárta után. Ha hamis ra van állítva, a jelentés lejártakor hibaként lesz kezelve. A tulajdonság értéke alapértelmezés szerint hamis. Amikor az ügyfelek rendszeres időközönként jelentést, meg kell állítani a RemoveWhenExpired false (alapértelmezett). Ily módon a jelentéskészítő problémák (pl. holtpont), és nem tud jelentést, az entitás kiértékelése hiba, amikor az állapotjelentés lejár. Ez az entitást hibaállapotként jelzi. |
| --szekvenciaszám | Az állapotjelentés sorozatszáma numerikus karakterláncként. <br><br> A jelentéssorszámot az állapottároló az elavult jelentések észlelésére használja. Ha nincs megadva, a jelentés hozzáadásakor az állapotügyfél automatikusan létrehoz egy sorszámot. |
| --idő-out -t | Alapértelmezett\: 60. |
| --ttl | Az az időtartam, amelyre ez az állapotjelentés érvényes. Ez a mező Az Időtartam megadásához Az ISO8601 formátumot használja. <br><br> Amikor az ügyfelek rendszeres időközönként jelentést küldenek, az életidejéneknél nagyobb gyakorisággal kell jelentéseket küldeniük. Ha az ügyfelek az átmenetről számolnak be, beállíthatják, hogy az élet ideje végtelen. Amikor lejár az életidő, az állapotadatokat tartalmazó állapotesemény törlődik az állapottárolóból, ha az RemoveWhenExpired igaz, vagy hiba esetén kiértékelve, ha az RemoveWhenExpired hamis. Ha nincs megadva, az élethez való idő nem éri el a végtelen értéket. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-select"></a>sfctl cluster kiválasztása
Egy Service Fabric-fürt végponthoz csatlakozik.

Ha biztonságos fürthöz csatlakozik, adjon meg egy abszolút elérési utat egy tanúsítványhoz (.crt) és egy kulcsfájlhoz (.key) vagy egy mindkét fájlt tartalmazó fájlhoz (.pem). Ne adja meg mindkettőt. Ha biztonságos fürthöz csatlakozik, adja meg a hitelesítésszolgáltatók csomagfájljának vagy megbízható hitelesítésszolgáltatók könyvtárának abszolút elérési útját is.  Nincs kapcsolat a fürthöz anélkül, hogy először futtatna ezt a parancsot, beleértve a localhost-hoz való csatlakozást is. A helyi fürthöz való csatlakozáshoz azonban nincs szükség explicit végpontra.  Ha egy önaláírt tanúsítványt vagy más tanúsítványt nem írt alá egy jól ismert hitelesítésszolgáltató, adja át a --ca paramétert, hogy az érvényesítés sikeres legyen. Ha nem éles fürtön, az ügyféloldali érvényesítés megkerüléséhez (az önaláírt vagy nem jól ismert hitelesítésnél) használja a --no-verify kapcsolót. Bár lehetséges, éles fürtök számára nem ajánlott. A tanúsítvány-ellenőrzési hiba másként fordulhat eredmény.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --aad | Az Azure Active Directory használata a hitelesítéshez. |
| --ca | A hitelesítési adatok könyvtárának abszolút elérési útja érvényesként vagy hitelesítéshitelesítési kötegfájlként való kezeléshez. Ha a hitelesítésszolgáltatói tanúsítványok könyvtárát használja, `c_rehash <directory>` az OpenSSL által biztosított könyvtárat először futtatni kell a tanúsítványkivonatok kiszámításához és a megfelelő szimbolikus hivatkozások létrehozásához. Ezzel ellenőrizhető, hogy a fürt által visszaadott tanúsítvány érvényes-e. |
| --tanúsítvány | Az ügyféltanúsítvány-fájl abszolút elérési útja. |
| --végpont | Fürtvégpont URL-címe, beleértve a portot és a HTTP vagy HTTPS előtagot. A végpont általában a https\://<a-url \:>19080-hoz hasonló lesz. Ha nincs megadva végpont, akkor\:alapértelmezés szerint\:a http //localhost 19080 lesz.  Alapértelmezett\: \:http //localhost\:19080. |
| --gomb | Az ügyféltanúsítványkulcs fájljának abszolút elérési útja. |
| --nincs ellenőrzés | Tiltsa le a tanúsítványok\: ellenőrzését https használata esetén, vegye figyelembe, hogy ez nem biztonságos beállítás, és nem használható éles környezetben. |
| --pem | Az ügyféltanúsítvány abszolút elérési útja .pem fájlként. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-connection
Annak megjelenítése, hogy melyik Service Fabric-fürthöz csatlakozik az sfctl-példány.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-unprovision"></a>sfctl-fürt kioldása
A Service Fabric-fürt kód- vagy konfigurációs csomagjainak kioldása.

A szabályzat és a konfiguráció külön-külön támogatott.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --kód-verzió | A fürtkódcsomag verziója. |
| --config-verzió | A fürtjegyzék verziója. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-upgrade"></a>sfctl fürt frissítése
Indítsa el egy Service Fabric-fürt kódjának vagy konfigurációs verziójának frissítését.

Ellenőrizze a megadott frissítési paramétereket, és indítsa el a Service Fabric-fürt kódjának vagy konfigurációs verziójának frissítését, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-egészségügyi térkép | JSON kódolt szótár az alkalmazás neve párok és a maximális százalékos nem kifogástalan, mielőtt a hiba kiemelése. |
| --app-típus-egészségügyi térkép | JSON kódolású szótár az alkalmazás típusneve párok és a maximális százalékos nem kifogástalan, mielőtt a hiba kiemelése. |
| --kód-verzió | A fürtkód verziója. |
| --config-verzió | A fürt konfigurációs verziója. |
| --delta-egészségügyi értékelés | Az egyes frissítési tartományok befejezése után az abszolút állapotkiértékelés helyett engedélyezi a különbözeti állapot kiértékelését. |
| --delta-nem kifogástalan-csomópontok | A fürtfrissítések során engedélyezett maximális százalékos teljesítménycsökkenés.  Alapértelmezett\: 10. <br><br> A delta a frissítés kezdetén lévő csomópontok állapota és a csomópontok állapota között van az állapotkiértékelés időpontjában. Az ellenőrzés minden frissítési tartomány frissítésének befejezése után történik, hogy a fürt globális állapota a tűrhető határokon belül legyen. |
| --hiba-művelet | A lehetséges\: értékek a következők: "Érvénytelen", "Visszaállítás", "Kézi". |
| --erő-újraindítás | A folyamatok a frissítés során akkor is erőteljesen újraindulnak, ha a kódverzió nem változott. <br><br> A frissítés csak a konfigurációt vagy az adatokat módosítja. |
| --health-check-retry | Az állapot-ellenőrzések végrehajtása között az állapotellenőrzések végrehajtása között, ha az alkalmazás vagy a fürt nem kifogástalan. |
| --health-check-stable | Az az idő, amerre az alkalmazásnak vagy fürtnek kifogástalan állapotban kell maradnia, mielőtt a frissítés a következő frissítési tartományra lépne. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --health-check-wait | A frissítési tartomány befejezése után a várakozási idő időtartama az állapotellenőrzési folyamat megkezdése előtt. |
| --replika-set-check-időout | A frissítési tartomány feldolgozásának letiltására és a rendelkezésre állás elvesztésének megakadályozására rendelkezésre álló idő letiltására rendelkezésre álló idő, ha nem várt problémák merülnek fel. <br><br> Ha ez az időbevétel lejár, a frissítési tartomány feldolgozása a rendelkezésre állási veszteségi problémáktól függetlenül folytatódik. Az időhosszabbítás minden frissítési tartomány elején alaphelyzetbe áll. Az érvényes értékek 0 és 42949672925 között vannak. |
| --gördülő-upgrade-mód | A lehetséges\: értékek a következők: "Érvénytelen", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Alapértelmezett\: UnmonitoredAuto. |
| --idő-out -t | Alapértelmezett\: 60. |
| --egészségtelen alkalmazások | A nem megfelelő állapotú alkalmazások maximálisan engedélyezett százaléka a hiba jelentésének bejelentése előtt. <br><br> Ha például az alkalmazások 10%-a nem megfelelő állapotú, ez az érték 10. A százalékos érték az alkalmazások maximális tolerálható százalékát jelöli, amelyek nem megfelelő állapotúak lehetnek, mielőtt a fürt hibásnak minősülne. Ha a százalékos érték tiszteletben van, de legalább egy nem megfelelő állapotú alkalmazás van, az állapot figyelmeztetésként lesz kiértékelve. Ezt úgy számítja ki, hogy elosztja a nem megfelelő állapotú alkalmazások számát a fürtben lévő alkalmazáspéldányok teljes számával, kivéve az ApplicationTypeHealthPolicyMap-ben szereplő alkalmazástípusok alkalmazásait. A számítás kerekíti, hogy tolerálja egy hiba kis számú alkalmazások. |
| --nem kifogástalan-csomópontok | A nem megfelelő állapotú csomópontok maximálisan engedélyezett százaléka a hiba jelentése előtt. <br><br> Ha például a csomópontok 10%-a nem megfelelő állapotú, ez az érték 10. A százalékos érték a csomópontok maximális tolerálható százalékát jelöli, amely nem megfelelő állapotú lehet, mielőtt a fürt hibásnak minősülne. Ha a százalékos érték tiszteletben van, de legalább egy nem megfelelő állapotú csomópont van, az állapot figyelmeztetésként lesz kiértékelve. A százalékos értéket úgy számítja ki, hogy elosztja a nem megfelelő állapotú csomópontok számát a fürt ben lévő csomópontok teljes számával. A számítás kerekít, hogy tolerálja a kis számú csomópontok egy hiba. Nagy fürtök, egyes csomópontok mindig le vagy ki a javításokat, ezért ezt a százalékot úgy kell beállítani, hogy tolerálja ezt. |
| --upgrade-domain-delta-unhealthy-csomópontok | A fürtfrissítések során a frissítési tartomány csomópontjainak maximálisan engedélyezett százalékos aránya.  Alapértelmezett\: 15. <br><br> A különbözetet a frissítési tartomány csomópontjainak állapota és a frissítési tartomány csomópontjainak állapota között méri a rendszer az állapotkiértékelés időpontjában. Az ellenőrzés az összes befejezett frissítési tartomány minden frissítési tartományának befejezése után történik, hogy megbizonyosodjon arról, hogy a frissítési tartományok állapota a tűrhető határokon belül van. |
| --upgrade-domain-időout | Az egyes frissítési tartományoknak a FailureAction végrehajtása előtt be kell fejezniük az időt. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --upgrade-időeltetés | A teljes frissítés befejezéséhez szükséges idő a FailureAction végrehajtása előtt. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --figyelmeztetés hibaként | Azt jelzi, hogy a figyelmeztetések kezelése ugyanolyan súlyos, mint a hibák. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl fürt frissítés-folytatás
A fürtfrissítés áthelyezése a következő frissítési tartományra.

Szükség esetén tegye függővé a fürtkód vagy a konfigurációs frissítés áthelyezését a következő frissítési tartományra.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --upgrade-domain [Kötelező] | A fürtfrissítés következő frissítési tartománya. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl fürt frissítés-visszaállítás
Állítsa vissza a Service Fabric-fürt frissítését.

Állítsa vissza a Service Fabric-fürt kódját vagy konfigurációs frissítését.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-upgrade-status"></a>sfctl fürt frissítési állapota
Leveszi az aktuális fürtfrissítés folyamatát.

Leveszi a folyamatban lévő fürtfrissítés aktuális állapotát. Ha jelenleg nincs folyamatban frissítés, az előző fürtfrissítés utolsó állapotának leése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-upgrade-update"></a>sfctl fürt frissítés-frissítés
Frissítse a Service Fabric-fürt frissítési paramétereinek frissítési paramétereit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-egészségügyi térkép | JSON kódolt szótár az alkalmazás neve párok és a maximális százalékos nem kifogástalan, mielőtt a hiba kiemelése. |
| --app-típus-egészségügyi térkép | JSON kódolású szótár az alkalmazás típusneve párok és a maximális százalékos nem kifogástalan, mielőtt a hiba kiemelése. |
| --delta-egészségügyi értékelés | Az egyes frissítési tartományok befejezése után az abszolút állapotkiértékelés helyett engedélyezi a különbözeti állapot kiértékelését. |
| --delta-nem kifogástalan-csomópontok | A fürtfrissítések során engedélyezett maximális százalékos teljesítménycsökkenés.  Alapértelmezett\: 10. <br><br> A delta a frissítés kezdetén lévő csomópontok állapota és a csomópontok állapota között van az állapotkiértékelés időpontjában. Az ellenőrzés minden frissítési tartomány frissítésének befejezése után történik, hogy a fürt globális állapota a tűrhető határokon belül legyen. |
| --hiba-művelet | A lehetséges\: értékek a következők: "Érvénytelen", "Visszaállítás", "Kézi". |
| --erő-újraindítás | A folyamatok a frissítés során akkor is erőteljesen újraindulnak, ha a kódverzió nem változott. <br><br> A frissítés csak a konfigurációt vagy az adatokat módosítja. |
| --health-check-retry | Az állapot-ellenőrzések végrehajtása között az állapotellenőrzések végrehajtása között, ha az alkalmazás vagy a fürt nem kifogástalan. |
| --health-check-stable | Az az idő, amerre az alkalmazásnak vagy fürtnek kifogástalan állapotban kell maradnia, mielőtt a frissítés a következő frissítési tartományra lépne. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --health-check-wait | A frissítési tartomány befejezése után a várakozási idő időtartama az állapotellenőrzési folyamat megkezdése előtt. |
| --replika-set-check-időout | A frissítési tartomány feldolgozásának letiltására és a rendelkezésre állás elvesztésének megakadályozására rendelkezésre álló idő letiltására rendelkezésre álló idő, ha nem várt problémák merülnek fel. <br><br> Ha ez az időbevétel lejár, a frissítési tartomány feldolgozása a rendelkezésre állási veszteségi problémáktól függetlenül folytatódik. Az időhosszabbítás minden frissítési tartomány elején alaphelyzetbe áll. Az érvényes értékek 0 és 42949672925 között vannak. |
| --gördülő-upgrade-mód | A lehetséges\: értékek a következők: "Érvénytelen", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Alapértelmezett\: UnmonitoredAuto. |
| --idő-out -t | Alapértelmezett\: 60. |
| --egészségtelen alkalmazások | A nem megfelelő állapotú alkalmazások maximálisan engedélyezett százaléka a hiba jelentésének bejelentése előtt. <br><br> Ha például az alkalmazások 10%-a nem megfelelő állapotú, ez az érték 10. A százalékos érték az alkalmazások maximális tolerálható százalékát jelöli, amelyek nem megfelelő állapotúak lehetnek, mielőtt a fürt hibásnak minősülne. Ha a százalékos érték tiszteletben van, de legalább egy nem megfelelő állapotú alkalmazás van, az állapot figyelmeztetésként lesz kiértékelve. Ezt úgy számítja ki, hogy elosztja a nem megfelelő állapotú alkalmazások számát a fürtben lévő alkalmazáspéldányok teljes számával, kivéve az ApplicationTypeHealthPolicyMap-ben szereplő alkalmazástípusok alkalmazásait. A számítás kerekíti, hogy tolerálja egy hiba kis számú alkalmazások. |
| --nem kifogástalan-csomópontok | A nem megfelelő állapotú csomópontok maximálisan engedélyezett százaléka a hiba jelentése előtt. <br><br> Ha például a csomópontok 10%-a nem megfelelő állapotú, ez az érték 10. A százalékos érték a csomópontok maximális tolerálható százalékát jelöli, amely nem megfelelő állapotú lehet, mielőtt a fürt hibásnak minősülne. Ha a százalékos érték tiszteletben van, de legalább egy nem megfelelő állapotú csomópont van, az állapot figyelmeztetésként lesz kiértékelve. A százalékos értéket úgy számítja ki, hogy elosztja a nem megfelelő állapotú csomópontok számát a fürt ben lévő csomópontok teljes számával. A számítás kerekít, hogy tolerálja a kis számú csomópontok egy hiba. Nagy fürtök, egyes csomópontok mindig le vagy ki a javításokat, ezért ezt a százalékot úgy kell beállítani, hogy tolerálja ezt. |
| --upgrade-domain-delta-unhealthy-csomópontok | A fürtfrissítések során a frissítési tartomány csomópontjainak maximálisan engedélyezett százalékos aránya.  Alapértelmezett\: 15. <br><br> A különbözetet a frissítési tartomány csomópontjainak állapota és a frissítési tartomány csomópontjainak állapota között méri a rendszer az állapotkiértékelés időpontjában. Az ellenőrzés az összes befejezett frissítési tartomány minden frissítési tartományának befejezése után történik, hogy megbizonyosodjon arról, hogy a frissítési tartományok állapota a tűrhető határokon belül van. |
| --upgrade-domain-időout | Az egyes frissítési tartományoknak a FailureAction végrehajtása előtt be kell fejezniük az időt. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --upgrade-típusú | A lehetséges\: értékek a következők: "Érvénytelen", "Gördülő", "Rolling_ForceRestart".  Alapértelmezett\: működés közben. |
| --upgrade-időeltetés | A teljes frissítés befejezéséhez szükséges idő a FailureAction végrehajtása előtt. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --figyelmeztetés hibaként | Azt jelzi, hogy a figyelmeztetések kezelése ugyanolyan súlyos, mint a hibák. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.