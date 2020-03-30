---
title: Bevezetés az NSG-k áramlásnaplózásának bevezetésébe
titleSuffix: Azure Network Watcher
description: Ez a cikk bemutatja, hogyan használhatja az Azure Network Watcher NSG-folyamatnaplók szolgáltatás.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: fb4a55b9757748581e26f3d6594f9be2139658cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228257"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Bevezetés a hálózati biztonsági csoportok folyamatnaplózásába

A hálózati biztonsági csoport (NSG) forgalomnaplózása a Network Watcher szolgáltatása, amellyel megtekintheti az NSG-n keresztül bejövő és kimenő IP-forgalom adatait. A folyamatnaplók JSON formátumban vannak megírva, és szabályonként jelenítik meg a kimenő és bejövő forgalmat, a forgalom esetében érintett hálózati adaptert (NIC), a forgalomhoz tartozó ötféle információt (forrás/cél IP, forrás-/célport és protokoll), hogy a forgalmat a rendszer engedélyezte vagy elutasította, valamint a 2. verzióban az átviteli sebességre vonatkozó adatokat is (bájtok és csomagok).


![folyamatnaplók – áttekintés](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Míg a folyamatnaplók a cél NSG-k, azok nem jelennek meg ugyanaz, mint a többi naplók. A folyamatnaplók csak egy tárfiókon belül tárolódnak, és a következő példában látható naplózási útvonalat követik:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
A [forgalomelemzés](traffic-analytics.md)segítségével elemezheti a folyamatnaplókat, és betekintést nyerhet a hálózati forgalomba.

A többi naplóesetében ugyanazok az adatmegőrzési házirendek vonatkoznak a folyamatnaplókra. A napló-megőrzési házirend et 1 napról 365 napra állíthatja be. Ha az adatmegőrzési szabály nincs beállítva, a naplók örökre megmaradnak.

## <a name="log-file"></a>Naplófájl

A folyamatnaplók a következő tulajdonságokat tartalmazzák:

* **idő** - Az esemény naplózásának időpontja
* **systemId** - Hálózati biztonsági csoport erőforrásazonosítója.
* **kategória** - Az esemény kategóriája. A kategória mindig **NetworkSecurityGroupFlowEvent**
* **resourceid** - Az NSG erőforrásazonosítója
* **operationName** - Mindig NetworkSecurityGroupFlowEvents
* **tulajdonságok** - A gyűjtemény tulajdonságait a folyamat
    * **Verzió** - A folyamatnapló eseménysémájának verziószáma
    * **folyamatok** - a folyamatok gyűjteménye. Ez a tulajdonság több bejegyzéssel rendelkezik a különböző szabályokhoz
        * **szabály** - Az a szabály, amelynél a folyamatok szerepelnek
            * **áramlások** - a folyamatok gyűjteménye
                * **mac** - A virtuális gép hálózati adapterének MAC-címe, ahol a folyamatot gyűjtötték
                * **flowTuples** - Olyan karakterlánc, amely a folyamatlemez több tulajdonságát tartalmazza vesszővel tagolt formátumban
                    * **Időbélyegző** – Ez az érték annak az időbélyegzőnek az időbélyegzője, amikor a folyamat UNIX korszakformátumban történt.
                    * **Forrás IP** - A forrás IP
                    * **Cél IP** - A cél IP
                    * **Forrásport** – a forrásport
                    * **Célport** - A célport
                    * **Protokoll** - A protokoll a flow. Érvényes értékek **T** a TCP és **az U** UDP esetén.
                    * **Traffic Flow** - A forgalom iránya. Az érvényes értékek a bejövő **és** **az O** a kimenő értékek.
                    * **Közlekedési döntés** - Hogy a forgalom engedélyezett vagy megtagadták. Az érvényes értékek az **Engedélyezett A,** a **"D"** érték pedig a megtagadva.
                    * **Flow-állapot – Csak 2-es verzió** – a folyamat állapotát rögzíti. Lehetséges állapotok: **B**: Kezdés, folyamat létrehozásakor. A statisztikák nincsenek megadva. **C**: Folyamatos áramlás folytatása. A statisztikák 5 perces időközönként állnak rendelkezésre. **E**: Vége, ha egy folyamat véget ér. Statisztikai adatok rendelkezésre állnak.
                    * **Csomagok - Forrás a célhoz - Csak 2-es verzió** A forrásból a célhoz a legutóbbi frissítés óta küldött TCP- vagy UDP-csomagok teljes száma.
                    * **Küldött bájtok - Forrás a célhoz - Csak 2-es verzió** A forrásból a célhoz a legutóbbi frissítés óta küldött TCP- vagy UDP-csomagbájtok teljes száma. A csomagbájtok tartalmazzák a csomagfejlécet és a hasznos adatot.
                    * **Csomagok - Cél a forráshoz - Csak 2-es verzió** A célról a forrásra küldött TCP- vagy UDP-csomagok teljes száma a legutóbbi frissítés óta.
                    * **Küldött bájtok - Cél a forráshoz - Csak 2-es verzió** A célról a forrásra küldött TCP- és UDP-csomagbájtok teljes száma a legutóbbi frissítés óta. A csomagbájtok tartalmazzák a csomagfejlécet és a hasznos adatot.

## <a name="nsg-flow-logs-version-2"></a>NSG-folyamatnaplók 2-es verziójú

A naplók 2-es verziója folyamatállapotot vezet be. Beállíthatja, hogy a folyamatnaplók melyik verzióját kapja meg. A folyamatnaplók engedélyezéséről az [NSG-folyamatnaplózás engedélyezése](network-watcher-nsg-flow-logging-portal.md)című témakörben olvashat.

A *"B"* áramlási állapotot az áramlás indításakor kell rögzíteni. A *C* áramlási állapot és az *E* áramlási állapot olyan állapotok, amelyek az áramlás és az áramlás végződésének folytatását jelölik. Mind *a C,* mind *az E* állapot forgalmi sávszélesség-információkat tartalmaz.

**Példa:** Flow-t a TCP-beszélgetésből 185.170.185.105:35370 és 10.2.0.4:23 között:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185 170.185.105,10.2.0.0.4,35370,23,T,I,I,A,C,1021,588096,8005,4610880" "1493696138,185.175.185.10.2.0.4,35370,23,I,A,E,52,29952,47,27072"

A *folytatási C* és end *E* folyamat állapotok esetén a bájt- és csomagszámok az előző folyamatrekord időpontjától kezdve összesítő számok. Az előző példabeszélgetésre hivatkozva az átvitt csomagok száma összesen 1021+52+8005+47 = 9125. Az átvitt bájtok teljes száma: 588096+29952+4610880+27072 = 5256000.

Az alábbi szöveg egy folyamatnapló példája. Amint láthatja, több rekord is található, amelyek követik az előző szakaszban ismertetett tulajdonságlistát.

## <a name="nsg-flow-logging-considerations"></a>NSG-folyamatnaplózási szempontok

**A tárfiókkal kapcsolatos szempontok:** 

- Hely: A használt tárfióknak ugyanabban a régióban kell lennie, mint az NSG.Location: The storage account used must be in the same region as the NSG.
- Saját kezelés kulcsrotáció: Ha módosítja/elforgatja a hozzáférési kulcsokat a tárfiókhoz, az NSG flow naplók leáll. A probléma megoldásához le kell tiltania, majd újra engedélyeznie kell az NSG-folyamatnaplókat.

**NSG-folyamatnaplózás engedélyezése az erőforráshoz csatlakoztatott összes NSG-n:** Az Azure-beli folyamatnaplózás az NSG-erőforráson van konfigurálva. A folyamat csak egy NSG-szabályhoz lesz társítva. Olyan esetekben, ahol több NSG-t használnak, azt javasoljuk, hogy az NSG-folyamatnaplózás engedélyezve legyen az összes NSG-n, amely egy erőforrás alhálózatát vagy hálózati illesztőjét alkalmazza annak érdekében, hogy az összes forgalom rögzítésre kerüljön. További információt a [forgalom kiértékelésének módjáról a](../virtual-network/security-overview.md#how-traffic-is-evaluated) hálózati biztonsági csoportokban talál.

**Flow naplózási költségek:** NSG folyamat naplózása a napló mennyisége előállított. A nagy forgalom nagy folyamatnapló-mennyiséget és a kapcsolódó költségeket eredményezhet. Az NSG-folyamatnapló díjszabása nem tartalmazza a tárolás mögöttes költségeit. Az adatmegőrzési házirend szolgáltatás NSG flow naplózása azt jelenti, hogy külön tárolási költségek hosszabb ideig. Ha nincs szüksége az adatmegőrzési házirend szolgáltatásra, azt javasoljuk, hogy állítsa ezt az értéket 0-ra. További információ: [Network Watcher Pricing](https://azure.microsoft.com/pricing/details/network-watcher/) and [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/) fortovábbi részletekért.

**Az internetes IP-kből nyilvános IP-cím nélküli virtuális gépekre naplózott bejövő forgalom:** olyan virtuális gépek, amelyek nem rendelkeznek nyilvános IP-címmel a hálózati adapterhez egy példányszintű nyilvános IP-címként társított nyilvános IP-címen keresztül, vagy amelyek egy alapszintű terheléselosztó háttérkészlet részét képezik, használja [az alapértelmezett SNAT-ot,](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) és rendelkezik az Azure által a kimenő kapcsolat megkönnyítése érdekében az Azure által hozzárendelt IP-címmel. Ennek eredményeképpen előfordulhat, hogy az internetes IP-címekről érkező folyamatok folyamatai hoz nak flow-naplóbejegyzéseket, ha a folyamat az SNAT-hoz rendelt portok tartományának portjára irányul. Bár az Azure nem engedélyezi ezeket a folyamatokat a virtuális gépre, a kísérlet naplózza, és megjelenik a Network Watcher NSG-folyamatnaplójában. Azt javasoljuk, hogy a nem kívánt bejövő internetes forgalmat explicit módon blokkolja az NSG.

**Helytelen bájt- és csomagszámok állapotnélküli folyamatok esetén**: [A hálózati biztonsági csoportok (NSG-k)](https://docs.microsoft.com/azure/virtual-network/security-overview) [állapotalapú tűzfalként](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true)vannak megvalósítva. Azonban sok alapértelmezett/belső szabályok, amelyek szabályozzák a forgalom forgalom végrehajtása állapot nélküli módon. A platform korlátai miatt a bájtok és a csomagok száma nem kerül rögzítésre az állapotnélküli folyamatok (azaz a forgalom folyamatai megy keresztül állapotmentes szabályok), ezek csak az állapotalapú folyamatok. Ennek következtében az NSG flow naplókban (és a Traffic Analytics) jelentett bájtok és csomagok száma eltérhet a tényleges folyamatoktól. Ezt a korlátozást a tervek szerint 2020 júniusára rögzítik.

## <a name="sample-log-records"></a>Mintanapló-rekordok

Az alábbi szöveg egy folyamatnapló példája. Amint láthatja, több rekord is található, amelyek követik az előző szakaszban ismertetett tulajdonságlistát.


> [!NOTE]
> A **flowTuples* tulajdonság értékei vesszővel tagolt lista.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>1. verziójú NSG-folyamatnapló-formátum minta
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
### <a name="version-2-nsg-flow-log-format-sample"></a>2-es verziójú NSG-folyamatnapló-formátum minta
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

- A folyamatnaplók engedélyezéséről az [NSG-folyamatnaplózás engedélyezése](network-watcher-nsg-flow-logging-portal.md)című témakörben olvashat.
- A folyamatnaplók olvasásáról az [NSG-folyamatnaplók olvasása](network-watcher-read-nsg-flow-logs.md)című témakörben olvashat.
- Az NSG-naplózásról az [Azure Monitor hálózati biztonsági csoportok (NSG-k) naplói](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)című témakörben olvashat bővebben.
- Annak megállapításához, hogy a forgalom engedélyezett vagy tiltott-e virtuális gépről vagy virtuális gépről, olvassa el [a Virtuálisgép hálózati forgalom szűrőproblémájának diagnosztizálása című témakört.](diagnose-vm-network-traffic-filtering-problem.md)
