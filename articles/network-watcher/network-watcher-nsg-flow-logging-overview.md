---
title: "Hálózati biztonsági csoportok Azure hálózati figyelőt a naplózás folyamata bemutatása |} Microsoft Docs"
description: "Ez a lap ismerteti, hogyan NSG folyamata naplók Azure hálózati figyelőt szolgáltatása"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 4eaffba08ccf601e440709d804891668340a376d
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>A hálózati biztonsági csoportok folyamata naplózási bemutatása

Hálózati biztonsági csoport folyamat egyik funkciója, amely lehetővé teszi, hogy tekintse meg a hálózati biztonsági csoporton keresztül bemenő és kimenő IP-forgalom hálózati figyelőt feldolgozásra. A folyamat naplók json formátumban vannak megírva, és a kimenő és bejövő forgalom alapon egy szabályt, a hálózati adapter a folyamat vonatkozik, a folyamat (forrás vagy a cél IP-, forrás vagy a cél Port protokoll), 5 rekordos információk megjelenítése, és ha a forgalom lett engedélyez vagy tilt.

![Attribútumfolyam naplók – áttekintés][1]

Attribútumfolyam naplózza a cél hálózati biztonsági csoportok, amíg azok nem azonos a többi naplófájlt jelenik meg. Attribútumfolyam naplófájljainak tárolása csak egy tárfiókon belül, majd a naplózás elérési út a következő példában látható módon:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Az azonos megőrzési házirendek, a többi naplófájlt látott folyamata naplók vonatkoznak. Naplók rendelkezik egy megőrzési házirend – 365 nap beállítható az 1 nap. Ha nincs beállítva adatmegőrzési szabály, a naplók megőrzése korlátlan időre szól.

## <a name="log-file"></a>Naplófájl

Attribútumfolyam naplók több tulajdonságokkal rendelkezik. Az alábbi lista a NSG folyamata napló belül visszaadott tulajdonságainak listája:

* **idő** - idő, amikor az esemény
* **Rendszerazonosító** -hálózati biztonsági csoport erőforrás azonosítóját.
* **kategória** -kategória az eseményről, a rendszer mindig kell NetworkSecurityGroupFlowEvent
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


Egy folyamat napló példát a következő: Ahogy látja, kövesse az előző szakaszban leírt keresésitulajdonság-lista több rekordot. 

> [!NOTE]
> A flowTuples tulajdonság értékei egy vesszővel elválasztott listában.
 
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

Attribútumfolyam naplók engedélyezése ellátogatva [naplózás engedélyezésével Flow](network-watcher-nsg-flow-logging-portal.md).

Látogasson el az NSG-naplózás megismerése [elemzések a hálózati biztonsági csoportokkal (NSG-k) jelentkezzen](../virtual-network/virtual-network-nsg-manage-log.md).

Megtudhatja, ha a forgalom engedélyezett vagy megtagadott a virtuális gép ellátogatva [ellenőrizze forgalmat az IP-adatfolyam ellenőrzése](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-overview/figure1.png

