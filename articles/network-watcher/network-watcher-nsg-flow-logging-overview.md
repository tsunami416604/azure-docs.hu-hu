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
ms.openlocfilehash: ae4edb82fa5e192a30d297dae82199bb7efca0c2
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344971"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Bevezetés a hálózati biztonsági csoportok csoportforgalom naplózása

Hálózati biztonsági csoport (NSG) folyamatnaplóit érhetők el a Network Watcher, amely lehetővé teszi, hogy egy NSG-n keresztül bejövő és kimenő IP-forgalomra vonatkozó információk megtekintése. Folyamatnaplók json formátumban készültek, és bejövő és kimenő folyamatok megjelenítése / szabály alapján, a hálózati adapter (NIC) a folyamat vonatkozik, a folyamat (forrás és cél IP-cím, forrás és a cél-port és protokoll) 5-ször több információt, és ha volt-e a forgalmat engedélyezett vagy tiltott.

![a folyamat-Naplók áttekintése](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

A Folyamatnaplók cél NSG-k, amíg azok nem jelennek meg ugyanaz, mint a többi naplófájlt. Forgalmi naplók csak egy tárfiók tárolja, és kövesse az alábbi példában látható, a naplózás elérési útja:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Forgalmi naplók más naplók látható ugyanazon adatmegőrzési szabályzatok érvényesek. Napló adatmegőrzési 1 nap és 2147483647 nap beállítása. Ha nincs beállítva adatmegőrzési szabály, a naplók megőrzése korlátlan időre szól.

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
                    * **Forgalom** – akár engedélyezett vagy tiltott forgalmat. Érvényes értékek a következők **A** engedélyezett és **D** a megtagadva.

A következő szöveget, amelyek egy folyamat napló. Amint láthatja, nincsenek az alábbi lista az előző szakaszban leírt több rekord.

> [!NOTE]
> Az értékek a **flowTuples* tulajdonság egy vesszővel tagolt lista.
 
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

- A folyamat-naplók engedélyezésére, lásd: [engedélyezése NSG csoportforgalom naplózása](network-watcher-nsg-flow-logging-portal.md).
- NSG-t naplózásával kapcsolatos további tudnivalókért lásd: [Naplóelemzés hálózati biztonsági csoportok (NSG-k)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Annak megállapításához, hogy engedélyezett vagy tiltott forgalmat, illetve a virtuális gép, lásd: [egy virtuális gép hálózati forgalomszűrési problémáinak diagnosztizálása](diagnose-vm-network-traffic-filtering-problem.md)
