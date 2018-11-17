---
title: Bevezetés a hálózati biztonsági csoportforgalom naplózása csoportok Azure Network Watcher szolgáltatással |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Network Watcher NSG folyamat naplók funkcióját használja.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: addd901e1b3a9bb537278082763081a7e39b21da
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824285"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Bevezetés a hálózati biztonsági csoportok csoportforgalom naplózása

Hálózati biztonsági csoport (NSG) folyamatnaplóit érhetők el a Network Watcher, amely lehetővé teszi, hogy egy NSG-n keresztül bejövő és kimenő IP-forgalomra vonatkozó információk megtekintése. Folyamatnaplók JSON formátumban nyelven íródtak, és megjelenítése a kimenő és bejövő adatfolyamok szabály szerint történik, a hálózati adapter (NIC) a folyamat vonatkozik, 5-ször több információt a folyamat (forrás és cél IP-cím forrás és a cél-port és protokoll), ha a forgalom engedélyezett vagy megtagadott, és a 2-es verzió, átviteli sebességgel kapcsolatos információkat (bájtok és csomagok).


![a folyamat-Naplók áttekintése](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

A Folyamatnaplók cél NSG-k, amíg azok nem jelennek meg ugyanaz, mint a többi naplófájlt. Forgalmi naplók csak egy tárfiók tárolja, és kövesse az alábbi példában látható, a naplózás elérési útja:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
 
Forgalmi naplók más naplók látható ugyanazon adatmegőrzési szabályzatok érvényesek. Napló adatmegőrzési 1 nap és 2147483647 nap beállítása. Ha nincs beállítva adatmegőrzési szabály, a naplók megőrzése korlátlan időre szól.

Folyamatnaplók használatával is elemezheti [traffic analytics](traffic-analytics.md).


## <a name="log-file"></a>Naplófájl

Forgalmi naplók az alábbi tulajdonságokat tartalmazzák:

* **idő** – idő, amikor az esemény naplózásának
* **Rendszerazonosító** – a hálózati biztonsági csoport erőforrás-azonosító.
* **kategória** – az esemény kategóriáját. A kategória minden esetben van **NetworkSecurityGroupFlowEvent**
* **erőforrás-azonosító** – erőforrás-azonosítóját az NSG-hez
* **operationName** -mindig NetworkSecurityGroupFlowEvents
* **Tulajdonságok** – a folyamat tulajdonságainak gyűjteménye
    * **Verzió** – a Flow eseménysémája verziószáma
    * **folyamatok** -gyűjteményre. Ez a tulajdonság különböző szabályok több bejegyzést tartalmaz
        * **a szabály** -folyamatok szerepelnek szabálya
            * **folyamatok** -gyűjteményre
                * **Mac** – a hálózati Adaptert a virtuális gép, ahol a folyamatot gyűjtötte a program a MAC-címét
                * **flowTuples** – egy karakterlánc, amely tartalmazza a flow rekord vesszővel tagolt formátumú több tulajdonság
                    * **Időbélyeg** – Ez az érték esetén időbélyegzőjét a folyamat a következő UNIX ALAPIDŐPONT formátum
                    * **Forrás IP-cím** – a forrás IP-címe
                    * **Cél IP** – a cél IP-cím
                    * **Forrásport** -forrásport
                    * **Célport** – a célhely portja
                    * **Protokoll** – a folyamat által használt protokoll. Érvényes értékek a következők **T** TCP és **U** UDP-hez
                    * **Forgalom áramlása** -adatforgalmat irányát. Érvényes értékek a következők **I** a bejövő és **O** a kimenő.
                    * **Döntési forgalom** – akár engedélyezett vagy tiltott forgalmat. Érvényes értékek a következők **A** engedélyezett és **D** a megtagadva.
                    * **A folyamat állapota - verzió 2 csak** – a folyamat állapotát rögzíti. Lehetséges állapotok a következők **B**: kezdje, ha egy folyamat jön létre. Statisztikák nem biztosított. **C**: egy folyamatban lévő folyamat folytatása. Statisztika 5 perces időközönként állnak rendelkezésre. **E**: záró, amikor a folyamat megszakadt. Statisztika állnak rendelkezésre.
                    * **Cél - verzió 2 csak a forrás - csomagok** a legutóbbi frissítés óta küldeni a forrás célhelyre TCP vagy UDP-csomagok teljes száma.
                    * **Bájt lett elküldve – a forrás és a cél - verzió 2 csak** a legutóbbi frissítés óta küldeni a forrás célhelyre TCP vagy UDP-csomag bájtok teljes száma. Csomag bájt közé tartozik, a csomag fejlécének és adattartalmának bontása.
                    * **Csomagok - célt a forrás - verzió 2 csak** célhelyről küldött forrás legutóbbi frissítés óta TCP vagy UDP-csomagok teljes száma.
                    * **Bájt lett elküldve – a célt a forrás - verzió 2 csak** TCP és UDP-csomag által küldött bájtok cél forrás legutóbbi frissítés óta teljes száma. Csomag bájt közé tartozik a csomag fejlécének és adattartalmának bontása.

## <a name="nsg-flow-logs-version-2"></a>NSG-Folyamatnaplók 2-es verzió
> [!NOTE] 
> Flow 2-es naplók verzió csak érhetők el az USA nyugati középső régiójában. Beállítási lehetőségek érhetők el az Azure Portal és a REST API-t. 2-es verzió engedélyezése egy nem támogatott régióban naplókat eredményez 1-es verziójú naplókat, a storage-fiókhoz használt kimeneti adattípus.

2. verzióját a naplók vezet be a folyamat állapota. Konfigurálhatja a Folyamatnaplók melyik verzióját megjelenhet. A folyamat-naplók engedélyezésére, lásd: [engedélyezése NSG csoportforgalom naplózása](network-watcher-nsg-flow-logging-portal.md).

Folyamat állapota *B* keletkezik, amikor egy olyan folyamatot kezdeményez. Folyamat állapota *C* és teljesítményfolyamati állapot *E* folytatása, a flow és a flow-lezárást, illetve megjelölése állapotok vannak. Mindkét *C* és *E* állapotok forgalom a sávszélesség-információkat tartalmaznak.

A folytatási *C* és a záró *E* flow-állapotokat bájt és csomagok számát is összesített számát az előző folyamat rekord rekord időpontból. Az előző példában beszélgetés hivatkozik, továbbított csomagok teljes száma a 1021 + 52 + 8005 + 47 = 9125. Az átvitt bájtok teljes száma a 588096 + 29952 + 4610880 + 27072 = 5256000.

**Példa**: TCP 185.170.185.105:35370 és 10.2.0.4:23 közötti beszélgetés származó rekordokat tartalmazó folyamat:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

A folytatási *C* és a záró *E* flow-állapotokat bájt és csomagok számát is összesített számát az előző folyamat rekord rekord időpontból. Az előző példában beszélgetés hivatkozik, továbbított csomagok teljes száma a 1021 + 52 + 8005 + 47 = 9125. Az átvitt bájtok teljes száma a 588096 + 29952 + 4610880 + 27072 = 5256000.

A következő szöveget, amelyek egy folyamat napló. Amint láthatja, nincsenek az alábbi lista az előző szakaszban leírt több rekord.

## <a name="sample-log-records"></a>Minta rekordok naplózása

A következő szöveget, amelyek egy folyamat napló. Amint láthatja, nincsenek az alábbi lista az előző szakaszban leírt több rekord.


> [!NOTE]
> Az értékek a **flowTuples* tulajdonság egy vesszővel tagolt lista.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>1. verziójának NSG folyamat napló formátuma minta
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
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```
### <a name="version-2-nsg-flow-log-format-sample"></a>2. verzió NSG folyamat napló formátuma minta
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>További lépések

- A folyamat-naplók engedélyezésére, lásd: [engedélyezése NSG csoportforgalom naplózása](network-watcher-nsg-flow-logging-portal.md).
- NSG-t naplózásával kapcsolatos további tudnivalókért lásd: [Naplóelemzés hálózati biztonsági csoportok (NSG-k)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Annak megállapításához, hogy engedélyezett vagy tiltott forgalmat, illetve a virtuális gép, lásd: [egy virtuális gép hálózati forgalomszűrési problémáinak diagnosztizálása](diagnose-vm-network-traffic-filtering-problem.md)
