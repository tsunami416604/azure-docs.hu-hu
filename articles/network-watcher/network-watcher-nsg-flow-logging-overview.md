---
title: Hálózati biztonsági naplózás folyamata bemutatása az Azure hálózati figyelőt csoportok |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan az NSG folyamata naplók szolgáltatása Azure hálózati figyelőt.
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
ms.openlocfilehash: c6a24fbca37d6aa1d775a70c708a139dfb70b813
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182425"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>A hálózati biztonsági csoportok folyamata naplózási bemutatása

Hálózati biztonsági csoport (NSG) folyamat naplók egyik újdonsága a hálózati figyelő, amely lehetővé teszi az IP-bemenő és kimenő forgalmat egy NSG keresztül vonatkozó információk megtekintése. Folyamat naplók json formátumban vannak megírva, és kimenő és bejövő forgalom megjelenítése / szabály alapján, a hálózati illesztőt (NIC), a folyamat vonatkozik, a folyamat (forrás vagy a cél IP, forrás vagy a cél-port és protokoll) 5 rekordos információt, és ha lett-e a forgalom engedélyezett vagy megtagadott.

![Attribútumfolyam naplók – áttekintés](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Amíg a folyamat célja NSG-ket naplózza, hogy ne legyenek azonos, a többi naplófájlt jelennek meg. Folyamat Naplók csak egy tárfiókon belül tárolják, és kövesse az alábbi példában látható módon, a naplózás elérési útja:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

A többi naplófájlt látott azonos adatmegőrzési folyamata naplók vonatkozik. Között állítható be napló adatmegőrzési 1 nap – 365 nap. Ha nincs beállítva adatmegőrzési szabály, a naplók megőrzése korlátlan időre szól.

## <a name="log-file"></a>Naplófájl

Attribútumfolyam naplókban szerepel a következő tulajdonságokkal:

* **idő** - idő, amikor az esemény
* **Rendszerazonosító** -hálózati biztonsági csoport erőforrás azonosítóját.
* **kategória** -esemény kategóriáját. A kategória mindig van **NetworkSecurityGroupFlowEvent**
* **ResourceId** -erőforrás-azonosító, az NSG
* **operationName** -mindig NetworkSecurityGroupFlowEvents
* **Tulajdonságok** -áramlását tulajdonságainak gyűjteménye
    * **Verzió** -az egymást követő esemény séma verziószáma
    * **adatfolyamok** -adatfolyamok gyűjteménye. Ez a tulajdonság különböző szabályok több bejegyzést tartalmaz
        * **a szabály** -a adatfolyamok találhatók a szabály
            * **adatfolyamok** -adatfolyamok gyűjteménye
                * **Mac** – a hálózati Adaptert a virtuális gép, ahol a folyamat gyűjtése történt a MAC-címe
                * **flowTuples** -karakterlánc, amely a folyamat rekord vesszővel tagolt formátumú több tulajdonságait tartalmazza
                    * **Időbélyeg** -ezt az értéket van, akkor, ha a folyamat történt UNIX EPOCH formátumban
                    * **Forrás IP-címe** -a forrás IP-címe
                    * **Cél IP** -a cél IP-címe
                    * **Forrásport** -forrásport
                    * **Célport** -Port cél
                    * **Protokoll** -megadott protokollal a folyamatot. Érvényes értékek a következők **T** TCP-hez és **U** UDP-hez
                    * **Forgalom bonyolódjon** -a forgalom áramlását irányát. Érvényes értékek a következők **I** bejövő és **O** a kimenő.
                    * **Forgalom** – akár forgalom lett engedélyez vagy tilt. Érvényes értékek a következők **A** engedélyezett, és **D** a megtagadva.

A szöveg, amelyet a következő folyamat a napló példája. Ahogy látja, nincsenek több azt jelzi, hogy kövesse az előző szakaszban leírt tulajdonságlistát.

> [!NOTE]
> Az értékek a **flowTuples* tulajdonság egy vesszővel elválasztott listában.
 
```json
{
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

## <a name="next-steps"></a>További lépések

- Attribútumfolyam naplók engedélyezése című témakörben talál [engedélyezése NSG folyamata naplózási](network-watcher-nsg-flow-logging-portal.md).
- NSG naplózási kapcsolatos további információkért lásd: [elemzések a hálózati biztonsági csoportokkal (NSG-k) jelentkezzen](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Annak eldöntéséhez, hogy forgalom engedélyezett vagy megtagadott vagy a virtuális gép, lásd: [diagnosztizálja a virtuális gép hálózati forgalom szűrő hiba](diagnose-vm-network-traffic-filtering-problem.md)