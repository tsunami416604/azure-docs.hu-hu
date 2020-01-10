---
title: A flow naplózásának bemutatása a NSG
titleSuffix: Azure Network Watcher
description: Ez a cikk azt ismerteti, hogyan használható az Azure Network Watcher NSG flow-naplói szolgáltatás.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 509545443bc08e8613d5f7a9ba7f33d2a90684b8
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830513"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>A hálózati biztonsági csoportok flow-naplózásának bemutatása

A hálózati biztonsági csoport (NSG) forgalomnaplózása a Network Watcher szolgáltatása, amellyel megtekintheti az NSG-n keresztül bejövő és kimenő IP-forgalom adatait. A folyamatnaplók JSON formátumban vannak megírva, és szabályonként jelenítik meg a kimenő és bejövő forgalmat, a forgalom esetében érintett hálózati adaptert (NIC), a forgalomhoz tartozó ötféle információt (forrás/cél IP, forrás-/célport és protokoll), hogy a forgalmat a rendszer engedélyezte vagy elutasította, valamint a 2. verzióban az átviteli sebességre vonatkozó adatokat is (bájtok és csomagok).


![a flow naplói – áttekintés](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

A flow naplózza a célként megadott NSG, de nem ugyanazokat a naplókat jeleníti meg. A flow-naplókat a rendszer csak egy Storage-fiókon belül tárolja, és az alábbi példában látható naplózási útvonalat követi:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
A [Traffic Analytics](traffic-analytics.md)segítségével elemezheti a folyamat naplóit, és betekintést nyerhet a hálózati forgalomba.

A más naplókra vonatkozó adatmegőrzési szabályzatok a folyamat naplófájljaira is érvényesek. A naplózási adatmegőrzési szabályzatot 1 napról 365 napra állíthatja be. Ha nincs beállítva adatmegőrzési szabály, a naplók megőrzése korlátlan időre szól.

> [!NOTE] 
> A NSG adatmegőrzési házirend funkciójának használata nagy mennyiségű tárolási műveletet eredményezhet, és a kapcsolódó költségeket is. Ha nincs szüksége az adatmegőrzési házirend funkcióra, azt javasoljuk, hogy állítsa 0 értékre.


## <a name="log-file"></a>Naplófájl

A flow-naplók a következő tulajdonságokat tartalmazzák:

* az esemény naplózásának **időpontja**
* **systemId** – hálózati biztonsági csoport erőforrás-azonosítója.
* **Kategória** – az esemény kategóriája. A kategória mindig **NetworkSecurityGroupFlowEvent**
* **ResourceId** – a NSG erőforrás-azonosítója
* **operationName** – mindig NetworkSecurityGroupFlowEvents
* **Tulajdonságok** – a folyamat tulajdonságainak gyűjteménye
    * A flow log esemény sémájának **verziószáma**
    * **flow** -folyamatok gyűjteménye. Ez a tulajdonság több bejegyzést tartalmaz a különböző szabályokhoz
        * **szabály** – a folyamatok listáját tartalmazó szabály
            * **folyamatok** – folyamatok gyűjteménye
                * **Mac** – a virtuális gép hálózati adapterének MAC-címe, ahová a folyamat begyűjtése történik
                * **flowTuples** – a flow-rekord több tulajdonságát tartalmazó sztring vesszővel tagolt formátumban
                    * **Időbélyegző – ez** az érték a folyamat UNIX EPOCH formátumban való előfordulásának időbélyegzője.
                    * **Forrás IP** -címe – a forrás IP-címe
                    * **Cél IP** -címe – a cél IP-címe
                    * **Forrásoldali port** – a forrásport
                    * **Célport** – a célport
                    * **Protokoll** – a folyamat protokollja. Az UDP esetében érvényes értékek: **T** , TCP és **U**
                    * **Forgalmi folyamat** – a forgalom iránya. Az érvényes értékek **a bejövő** és kimenő **kimeneti** értékekhez tartoznak.
                    * **Forgalmi döntés** – azt, hogy a forgalom engedélyezett vagy tiltott. **Az érvényes** értékek a megengedettek, a **D** megtagadva.
                    * **Flow State – csak 2-es verzió** – rögzíti a folyamat állapotát. A lehetséges állapotok a **B**: Begin, amikor létrejön egy folyamat. Nincs megadva statisztika. **C**: folyamatos folyamat folytatása. A statisztikák 5 perces időközönként vannak megadva. **E**: Befejezés, ha egy folyamat leáll. Statisztikákat biztosítunk.
                    * **Csomagok – forrás – cél – csak 2-es verzió** A forrásról a célhelyre küldött TCP-vagy UDP-csomagok teljes száma az utolsó frissítés óta.
                    * **Eljuttatott bájtok – forrás – cél – csak 2-es verzió** A forrás és a célhely között a legutóbbi frissítés óta küldött TCP-vagy UDP-csomagok teljes száma. A csomagok bájtjai a csomagok fejlécét és a hasznos adatokat tartalmazzák.
                    * **Csomagok – cél a forrás – csak 2-es verzió** Az utolsó frissítés óta a célhelyről a forrásba küldött TCP-vagy UDP-csomagok teljes száma.
                    * **Eljuttatott bájtok – cél – forrás – csak 2-es verzió** A célhelyről a forrásig a legutóbbi frissítés óta küldött TCP-és UDP-csomagok teljes száma. A csomagok bájtjai tartalmazzák a csomagok fejlécét és a hasznos adatokat.

## <a name="nsg-flow-logs-version-2"></a>NSG flow-naplók 2. verzió

A naplók 2-es verziója bevezeti a flow állapotát. Beállíthatja, hogy a rendszer milyen verziójú flow-naplókat kapjon. A flow-naplók engedélyezésével kapcsolatos további információkért lásd: a [NSG folyamat naplózásának engedélyezése](network-watcher-nsg-flow-logging-portal.md).

A " *B* " folyamat a folyamat indításakor kerül rögzítésre. A *C* és a *flow állapot olyan* állapotú, amely a folyamat folytatását és a folyamat befejezését jelöli. A *C* és az *E* -Államok forgalmi sávszélességgel kapcsolatos információkat tartalmaznak.

**Példa**: a flow rekordok TCP-beszélgetésből a 185.170.185.105:35370 és a 10.2.0.4:23 között:

"1493763938, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, B,,,," "1493695838, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, C, 1021, 588096, 8005, 4610880" "1493696138, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, E, 52, 29952, 47, 27072"

A " *C* " és a "End *E* flow" állapot esetén a bájt és a csomagok száma az előző folyamat rekordjának időpontjától számított összesített szám. Az előző példában szereplő beszélgetésre hivatkozva az átvitt csomagok teljes száma 1021 + 52 + 8005 + 47 = 9125. Az átvitt bájtok teljes száma: 588096 + 29952 + 4610880 + 27072 = 5256000.

Az alábbi szöveg egy folyamat naplóját szemlélteti. Ahogy láthatja, több rekord is van, amelyek követik az előző szakaszban ismertetett tulajdonságokat.

## <a name="nsg-flow-logging-considerations"></a>NSG-folyamatok naplózási szempontjai

A **Storage-fiókkal kapcsolatos megfontolások**: 

- Hely: a használt Storage-fióknak ugyanabban a régióban kell lennie, mint a NSG.
- A kulcs önálló kezelése: Ha módosítja vagy elforgatja a hozzáférési kulcsokat a Storage-fiókhoz, a NSG-naplók nem fognak működni. A probléma megoldásához le kell tiltania, majd újra engedélyeznie kell a NSG folyamat naplóit.

**NSG-naplózás engedélyezése az erőforráshoz csatolt összes NSG**: az Azure-ban a flow naplózása a NSG-erőforráson van konfigurálva. Egy folyamat csak egyetlen NSG-szabályhoz lesz társítva. Olyan helyzetekben, ahol több NSG van használatban, javasoljuk, hogy a NSG flow naplózása engedélyezve legyen minden olyan NSG, amely az erőforrás alhálózatát vagy hálózati adapterét alkalmazza az összes forgalom rögzítésének biztosításához. További információ: a [forgalom kiértékelése](../virtual-network/security-overview.md#how-traffic-is-evaluated) a hálózati biztonsági csoportokban.

A **flow naplózási költségei**: a NSG folyamatának naplózása a létrehozott naplók mennyiségétől függ. A nagy forgalmú kötetek nagy flow-naplózási kötetet és a hozzájuk kapcsolódó költségeket okozhatják. A NSG-forgalmi napló díjszabása nem tartalmazza a tárterület alapjául szolgáló költségeket. A NSG adatmegőrzési házirend funkciójának használata nagy mennyiségű tárolási műveletet eredményezhet, és a kapcsolódó költségeket is. Ha nincs szüksége az adatmegőrzési házirend funkcióra, azt javasoljuk, hogy állítsa 0 értékre. További információkért tekintse meg a [Network Watcher díjszabását](https://azure.microsoft.com/pricing/details/network-watcher/) és az [Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/) ismertető témakört.

Az internetes IP-címekről a nyilvános IP-címek **nélküli virtuális gépekre naplózott bejövő folyamatok**: olyan virtuális gépek, amelyek nem rendelkeznek nyilvános IP-címmel a hálózati adapterhez társított nyilvános IP-címen keresztül, vagy amelyek egy alapszintű terheléselosztó-készlet részét képezik, az [alapértelmezett SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) használják, és az Azure által hozzárendelt IP-címmel rendelkeznek a kimenő kapcsolatok megkönnyítéséhez. Ennek eredményeképpen előfordulhat, hogy az internetes IP-címekről érkező adatfolyamok esetében a flow-naplóbejegyzések megjelennek, ha a folyamat a SNAT hozzárendelt portok tartományában lévő portra van szánva. Amíg az Azure nem engedélyezi ezeket a folyamatokat a virtuális gép számára, a rendszer naplózza a kísérletet, és a Network Watcher NSG flow-naplójában jelenik meg. Javasoljuk, hogy a nem kívánt bejövő internetes forgalmat explicit módon tiltsa le a NSG.

**Az állapot nélküli folyamatok esetében helytelen a bájt és a csomagok száma**: a [hálózati biztonsági csoportok (NSG-EK)](https://docs.microsoft.com/azure/virtual-network/security-overview) [állapot-nyilvántartó tűzfalként](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true)vannak implementálva. A forgalom áramlását szabályozó alapértelmezett/belső szabályok azonban állapot nélküli módon lesznek implementálva. A platformok korlátai miatt a rendszer nem rögzíti a bájtok és a csomagok számát az állapot nélküli folyamatok esetében (azaz az állapot nélküli szabályokon keresztüli forgalom), csak állapot-nyilvántartó folyamatokban lesznek rögzítve. Következésképpen a NSG-naplók (és Traffic Analytics) által jelentett bájtok és csomagok száma eltérő lehet a tényleges folyamatokban. Ezt a korlátozást az ütemezi, hogy a 2020-es időpontban rögzítettek legyenek.

## <a name="sample-log-records"></a>Minta napló rekordjai

Az alábbi szöveg egy folyamat naplóját szemlélteti. Ahogy láthatja, több rekord is van, amelyek követik az előző szakaszban ismertetett tulajdonságokat.


> [!NOTE]
> A **flowTuples* tulajdonságban szereplő értékek vesszővel tagolt lista.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>1\. verzió NSG folyamat naplójának formátuma minta
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
### <a name="version-2-nsg-flow-log-format-sample"></a>2\. verzió NSG-folyamatábra-formátum minta
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

## <a name="next-steps"></a>Következő lépések

- A flow-naplók engedélyezésével kapcsolatos további információkért lásd: a [NSG folyamat naplózásának engedélyezése](network-watcher-nsg-flow-logging-portal.md).
- A flow-naplók beolvasásával kapcsolatos további információkért lásd: [NSG flow-naplók olvasása](network-watcher-read-nsg-flow-logs.md).
- A NSG-naplózással kapcsolatos további tudnivalókért tekintse meg [a hálózati biztonsági csoportok (NSG) naplóinak Azure monitor](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Annak megállapításához, hogy a forgalom engedélyezett vagy letiltott egy virtuális gépről, tekintse meg [a virtuális gép hálózati forgalmának szűrése problémával kapcsolatos problémát](diagnose-vm-network-traffic-filtering-problem.md) .
