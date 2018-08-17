---
title: Azure-beli hálózati biztonsági csoport a folyamat log módosításokat |} A Microsoft Docs
description: Tudnivalók az Azure-beli hálózati biztonsági csoport a folyamat log módosításokat.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: b3a5ca929c83f70c31d667c2d9c811623691cff8
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180821"
---
# <a name="network-security-group-flow-logs--version-2--upcoming-changes"></a>Hálózati biztonsági csoport Folyamatnaplók - 2-es verzió – közelgő változások

A hálózati biztonsági csoport a folyamat naplók formátuma változnak, 2018. szeptember 15 kezdve. A hozzáadott mezők-információkat biztosít Önnek a folyamatot az állami és a növekményes bájtok és az összes csatlakozás minden irányában továbbított csomagok számát. Az alkalmazásokat, amelyek a folyamat-naplók elemzése a módosítás érinti, és ennek megfelelően kell frissíteni. Ez a cikk ismerteti a változás részleteit.

## <a name="what-is-changing"></a>Mi változik?

A verzió a hálózati biztonsági csoport folyamatnaplóit "Verziójú" változik: "Verziójú" 1: 2, a további mezőket hozzáadni a *flowTuples* tulajdonság a naplókban. További részletek a formátumnak [hogyan egy folyamatot a 2. verzió van modellezve](#how-is-a-flow-modeled-in-version-2).

### <a name="version-1-flow-tuple-format"></a>1. verziójának folyamat rekord formátum

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"
```

### <a name="version-2-flow-tuple-format"></a>2. verzió folyamat rekord formátum

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186"
```

## <a name="when-will-this-change-take-place"></a>Ha ez a változás történik?

Ez a módosítás akkor lépnek érvénybe elején **2018. szeptember 15** az USA nyugati középső régiójában található. Bevezetés a változás a nyilvános felhő-régiók befejezéséhez November 31-ig a 2018 Miután naplók csak verziószámának 2 lesz elérhető lesz.

## <a name="am-i-affected-by-the-change"></a>Érint engem a változás?

Ön hatással lehet ez a változás Ha hozhat létre, vagy egy alkalmazás, amely feldolgozza a hálózati biztonsági csoport a folyamat naplóadatok használatát.

### <a name="if-i-use-traffic-analytics"></a>A Traffic Analytics használata?

Nem. A TRAFFIC Analytics 2-es verzió csoportforgalom naplózása az 1. verzió való váltás során nem lesz hatással. Fejlesztések hamarosan további munkamenet, és a sávszélesség a információk verzió 2 Folyamatnaplók fog előnyeit.

### <a name="if-im-using-third-party-tooling"></a>Ha a harmadik féltől származó eszközök használok?

A módosítás a napló formátuma bejelentett előzetes az összes [Network Watcher partnerek](https://azure.microsoft.com/services/network-watcher). Részletekért a szállító kapcsolatba.

### <a name="if-i-have-built-a-custom-application-or-integration"></a>Ha készítettem egy egyéni alkalmazást vagy integrációs?

**Igen**, módosítsa az alkalmazást az új formátum az NSG-folyamat-naplók folyamatot kell.

## <a name="what-is-the-new-flow-logging-format"></a>Mi az a folyamat új naplózási formátum?

Folyamat-naplók verzió 2 folyamat rekordokat tartalmazó, a következő formátumban tartalmazza:

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186".
```

Az alábbi táblázat tulajdonság neveinek és leírásainak a hálózati biztonsági csoport verzió 2 folyamat rekord biztosít. Teljes minta rekordok található [2-es verzió mintaesemény](#version2sampleevent).

| Tulajdonság neve                        | Érték           | Leírás                                                                                                                                                 |
| ------------------------------------ | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Időbélyeg                           | 1493763938      | A flow-bejegyzést a megfelelő időbélyeg. UNIX ALAPIDŐPONT formátumot használja.                                                                                       |
| Forrás IP-címe                    | 185.170.185.105 |                                                                                                                                                             |
| Cél IP-cím               | 10.2.0.4        |                                                                                                                                                             |
| Forrásport                          | 35370           |                                                                                                                                                             |
| Célport                     | 23              |                                                                                                                                                             |
| Forgalom protokoll                     | T               | **T**: TCP és **U**: UDP.                                                                                                                                  |
| Forgalom folyamat iránya               | I               | **E**: bejövő forgalom és **O**: kimenő forgalmat.                                                                                                         |
| Forgalom döntés                     | A               | **A**: a rendszer engedélyezte a folyamat és **D**: a folyamat meg lett tagadva.                                                                                                         |
| A folyamat állapota                           | C               | **B**: kezdje, ha egy folyamat jön létre. Statisztikák nem biztosított. **C**: egy folyamatban lévő folyamat folytatása. Statisztika 5 perces időközönként állnak rendelkezésre. **E**: záró, amikor a folyamat megszakadt. Statisztika állnak rendelkezésre.                                                                                                                                                        |
| Csomagok - forrás és a cél      | 1               | Legutóbbi frissítés óta küldeni a forrás célhelyre TCP és UDP-csomagok teljes száma.                                                                  |
| Bájt lett elküldve – a forrás és a cél   | 103             | A legutóbbi frissítés óta küldeni a forrás célhelyre TCP és UDP-csomag bájtok száma. Csomag bájt közé tartozik, a csomag fejlécének és adattartalmának bontása.         |
| – Küldött csomagok forrás-cél | 1               | Legutóbbi frissítés óta forrás célhelyre küldött TCP és UDP-csomagok teljes száma.                                                                  |
| Bájt lett elküldve – a forrás-cél   | 186             | A TCP és UDP-csomag által küldött bájtok cél forrás legutóbbi frissítés óta teljes száma. Csomag bájt közé tartozik a csomag fejlécének és adattartalmának bontása.             |

## <a name="how-is-a-flow-modeled-in-version-2"></a>Egy folyamat hogyan van modellezve a 2. verzió?

2. verzióját a naplók vezet be a folyamat állapota. Folyamat állapota *B* keletkezik, amikor egy olyan folyamatot kezdeményez. Folyamat állapota *C* és teljesítményfolyamati állapot *E* folytatása, a flow és a flow-lezárást, illetve megjelölése állapotok vannak. Mindkét *C* és *E* állapotok forgalom a sávszélesség-információkat tartalmaznak.

**Példa**: TCP 185.170.185.105:35370 és 10.2.0.4:23 közötti beszélgetés származó rekordokat tartalmazó folyamat:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

### <a name="how-does-the-format-differ-from-version-1"></a>A formátum Miben különbözik az 1. verzió?

1. verzió, a folyamat rekordot ["1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"] jön létre minden alkalommal, amikor egy folyamatot létrehozni, vagy megpróbálta létrehozni (megtagadása eset).

### <a name="how-are-bytes-and-packets-accounted-for"></a>Hogyan bájtok és csomagok számolják el?

A folytatási *C* és a záró *E* flow-állapotokat bájt és csomagok számát is összesített számát az előző folyamat rekord rekord időpontból. Az előző példában beszélgetés hivatkozik, továbbított csomagok teljes száma a 1021 + 52 + 8005 + 47 = 9125. Az átvitt bájtok teljes száma a 588096 + 29952 + 4610880 + 27072 = 5256000.

### <a name="if-my-application-doesnt-understand-the-traffic-bandwidth-fields-how-does-version-2-affect-the-application"></a>Ha az alkalmazás nem ismeri a forgalom a sávszélesség mezőket, hogyan 2-es verzió befolyásolja az alkalmazás?

Alkalmazások használatával az 1. verzió hálózati biztonsági csoport a folyamat általában naplózás száma egyedi folyamatok száma. Nem elemzése történik változás áthidalhatók az állapot, jelentett folyamatok száma pontos növekedése jelenhet meg. Leltár egyedi folyamatokat is valósítható meg, figyelmen kívül hagyja a flow rekordok *C* és *E* flow állapotok.

## <a name="can-i-control-the-version-format-i-receive"></a>Szabályozhatja a verzió formátumban jelenik meg?

Nem. Engedélyezése és letiltása a forgalmi naplók nem befolyásolják a kimeneti formátum a naplók. 2. verziójú naplókat az 1. verzió módosítása régiónként alapon történik. Ha egy régió 2-es verzióra az 1-es verzió, láthatja a Folyamatnaplók NSG mindkét formátumot. A bevezetés befejezése után csak a 2. verziójú naplókat lesz elérhető.

## <a name="while-the-change-occurs-can-i-see-both-formats-for-the-same-network-security-group"></a>A változás következik be, amíg tekinthetem meg mindkét formátumot az azonos hálózati biztonsági csoport?

Igen. Egy adott régión belül az áttérés egy-mac cím alapján történik. A hálózati biztonsági csoport több gép is alkalmazható, mivel mindkét formátumokban írt Storage-naplók jelenhet meg. Naplók lehet 1-es verzió vagy a 2. verzióban.

## <a name="will-i-see-duplicate-data"></a>Megjelenik a duplikált adatok?

A párhuzamos naplózási adatok formátumok folyamat nem lesz. Egy folyamat lesz rögzítve 1-es vagy 2-es verzió formátumban, amíg a módosítás történik.

## <a name="how-can-i-test-the-new-format-ahead-of-time"></a>Hogyan tesztelheti az új formátumot az időben?

Igen. Is [letöltése](https://aka.ms/nsgflowlogsv2blobsample) minta 2-es verzió folyamat naplófájlt, a megoldás teszteléséhez használja.

## <a name="are-there-changes-to-configuration-and-management-of-network-security-group-flow-logging"></a>Vannak-e konfigurációs és felügyeleti hálózati biztonsági csoportforgalom naplózása a módosításokat?

Nem. Támogatás az Azure Storage-fiókok, előfizetések, amelyek ugyanahhoz az Azure Active Directory-bérlőhöz volt [kiadott](https://azure.microsoft.com/blog/new-azure-network-watcher-integrations-and-network-security-group-flow-logging-updates/) év korábbi részében. Ez a változás a megoldással csökkenthetők a hálózati biztonsági csoport folyamatnaplóit kezeléséhez szükséges tárfiókok száma.

## <a name="questions-or-feedback"></a>Kérdések és visszajelzés?

Kíváncsian várjuk visszajelzését a hálózati biztonsági csoport folyamatnaplóit és a Network Watcher tapasztalatait. Megadhat észrevételeit és javaslatait online, vagy e-mailt AzureNetworkWatcher@microsoft.com.

## <a name="version-2-sample"></a>2. verzió minta

```json
{

"records": [

{

"time": "2018-08-03T17:00:54.5657764Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315592,10.1.1.6,204.79.197.200,62375,80,T,O,A,B,,,,",

"1533315595,10.1.1.6,204.79.197.200,62373,80,T,O,A,E,30,1784,92,123631",

"1533315597,10.1.1.6,204.79.197.200,62376,80,T,O,A,B,,,,",

"1533315601,10.1.1.6,204.79.197.200,62374,80,T,O,A,E,13,866,87,123079",

"1533315603,10.1.1.6,204.79.197.200,62377,80,T,O,A,B,,,,",

"1533315604,10.1.1.6,204.79.197.200,62375,80,T,O,A,E,33,1946,90,123247",

"1533315608,10.1.1.6,204.79.197.200,62378,80,T,O,A,B,,,,",

"1533315610,10.1.1.6,204.79.197.200,62376,80,T,O,A,E,20,1244,92,123355",

"1533315613,10.1.1.6,204.79.197.200,62379,80,T,O,A,B,,,,",

"1533315616,10.1.1.6,204.79.197.200,62377,80,T,O,A,E,24,1460,92,123355",

"1533315619,10.1.1.6,204.79.197.200,62380,80,T,O,A,B,,,,",

"1533315622,10.1.1.6,204.79.197.200,62378,80,T,O,A,E,23,1406,93,123409",

"1533315624,10.1.1.6,204.79.197.200,62381,80,T,O,A,B,,,,",

"1533315628,10.1.1.6,204.79.197.200,62379,80,T,O,A,E,16,1028,88,123133",

"1533315630,10.1.1.6,204.79.197.200,62382,80,T,O,A,B,,,,",

"1533315631,10.1.1.6,204.79.197.200,62380,80,T,O,A,E,13,866,87,123079",

"1533315635,10.1.1.6,204.79.197.200,62384,80,T,O,A,B,,,,",

"1533315637,10.1.1.6,204.79.197.200,62381,80,T,O,A,E,15,974,86,123025",

"1533315640,10.1.1.6,204.79.197.200,62385,80,T,O,A,B,,,,",

"1533315643,10.1.1.6,204.79.197.200,62382,80,T,O,A,E,16,1028,88,123139",

"1533315646,10.1.1.6,204.79.197.200,62386,80,T,O,A,B,,,,",

"1533315649,10.1.1.6,204.79.197.200,62384,80,T,O,A,E,21,1298,92,123355",

"1533315651,10.1.1.6,204.79.197.200,62387,80,T,O,A,B,,,,"

]

}

]

}

]

}

},

{

"time": "2018-08-03T17:01:54.5668880Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_DenyAllInBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315685,80.211.83.37,10.1.1.6,45321,81,T,I,D,B,,,,"

]

}

]

},

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315655,10.1.1.6,204.79.197.200,62385,80,T,O,A,E,20,1244,87,123079",

"1533315657,10.1.1.6,204.79.197.200,62388,80,T,O,A,B,,,,",

"1533315658,10.1.1.6,204.79.197.200,62386,80,T,O,A,E,26,1568,92,123355",

"1533315662,10.1.1.6,204.79.197.200,62389,80,T,O,A,B,,,,",

"1533315664,10.1.1.6,204.79.197.200,62387,80,T,O,A,E,15,974,88,123133",

"1533315667,10.1.1.6,204.79.197.200,62390,80,T,O,A,B,,,,",

"1533315670,10.1.1.6,204.79.197.200,62388,80,T,O,A,E,18,1136,88,123139",

"1533315673,10.1.1.6,204.79.197.200,62391,80,T,O,A,B,,,,",

"1533315676,10.1.1.6,204.79.197.200,62389,80,T,O,A,E,14,920,87,123079",

"1533315678,10.1.1.6,204.79.197.200,62392,80,T,O,A,B,,,,",

"1533315679,10.1.1.6,204.79.197.200,62390,80,T,O,A,E,31,1838,94,123739",

"1533315684,10.1.1.6,204.79.197.200,62393,80,T,O,A,B,,,,",

"1533315685,10.1.1.6,204.79.197.200,62391,80,T,O,A,E,28,1676,101,141199",

"1533315689,10.1.1.6,204.79.197.200,62394,80,T,O,A,B,,,,",

"1533315691,10.1.1.6,204.79.197.200,62392,80,T,O,A,E,21,1298,93,123409",

"1533315694,10.1.1.6,204.79.197.200,62395,80,T,O,A,B,,,,",

"1533315697,10.1.1.6,204.79.197.200,62393,80,T,O,A,E,26,1568,91,123393",

"1533315700,10.1.1.6,204.79.197.200,62396,80,T,O,A,B,,,,",

"1533315703,10.1.1.6,204.79.197.200,62394,80,T,O,A,E,14,920,89,123187",

"1533315705,10.1.1.6,204.79.197.200,62397,80,T,O,A,B,,,,",

"1533315706,10.1.1.6,204.79.197.200,62395,80,T,O,A,E,13,866,87,123079",

"1533315711,10.1.1.6,204.79.197.200,62398,80,T,O,A,B,,,,"

]

}

]

}

]

}

}

]

}
```

## <a name="version-1-sample"></a>1. verziójának minta

```json
{ 

    "records": [ 

        { 

            "time": "2017-02-16T22:00:32.8950000Z", 

            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434", 

            "category": "NetworkSecurityGroupFlowEvent", 

            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG", 

            "operationName": "NetworkSecurityGroupFlowEvents", 

            "properties": { 

                "Version": 1, 

                "flows": [ 

                    { 

                        "rule": "DefaultRule_DenyAllInBound", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D" 

                                ] 

                            } 

                        ] 

                    }, 

                    { 

                        "rule": "UserRule_default-allow-rdp", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A", 

                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A", 

                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A", 

                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A" 

                                ] 

                            } 

                        ] 

                    } 

                ] 

            } 

        }, 

        { 

            "time": "2017-02-16T22:01:32.8960000Z", 

            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434", 

            "category": "NetworkSecurityGroupFlowEvent", 

            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG", 

            "operationName": "NetworkSecurityGroupFlowEvents", 

            "properties": { 

                "Version": 1, 

                "flows": [ 

                    { 

                        "rule": "DefaultRule_DenyAllInBound", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D" 

                                ] 

                            } 

                        ] 

                    }, 

                    { 

                        "rule": "UserRule_default-allow-rdp", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",

                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",

                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"

                                ] 

                            } 

                        ] 

                    } 

                ] 

            } 

        } 

    ] 
}
```