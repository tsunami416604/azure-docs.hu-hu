---
title: Az SAP HANA figyelése az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Az SAP HANA figyelése egy Azure-ban (nagy példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50a6b4f15a7de02533e3bb51e5659f7b4c078b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617290"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Az SAP HANA (nagy példányok) figyelése az Azure-ban

Az SAP HANA az Azure-ban (nagy példányok) nem különbözik bármely más IaaS-telepítéstől – figyelnie kell, hogy mit csinál az operációs rendszer és az alkalmazás, és hogy az alkalmazások hogyan használják fel a következő erőforrásokat:

- CPU
- Memory (Memória)
- Hálózati sávszélesség
- Lemezterület

Az Azure virtuális gépek, meg kell vizsgálnia, hogy a fent megnevezett erőforrásosztályok elegendőek- e, vagy kimerülnek. Itt van részletesebben az egyes osztályok:

**CPU-erőforrás-felhasználás:** Az sap által meghatározott, hana-val szemben bizonyos számítási feladatokhoz definiált arány takarása annak érdekében, hogy elegendő CPU-erőforrás legyen elérhető a memóriában tárolt adatok on-val. Mindazonáltal előfordulhatnak olyan esetek, amikor hana használ sok processzorok lekérdezések végrehajtása miatt hiányzó indexek vagy hasonló problémák. Ez azt jelenti, hogy figyelnie kell a HANA nagy példányegység és az adott HANA-szolgáltatások által felhasznált CPU-erőforrások CPU-erőforrás-felhasználását.

**Memóriafelhasználás:** Fontos, hogy a HANA-n belülről, valamint az egységen kívül is. Hana belül figyelheti, hogy az adatok hogyan fogyaszthana lefoglalt memóriát annak érdekében, hogy az SAP szükséges méretezési irányelvei között maradjon. Azt is szeretné figyelni a nagy példány szintjén a memóriafelhasználás, győződjön meg arról, hogy további telepített, nem HANA szoftver nem fogyaszt túl sok memóriát, és ezért versenyezni hana a memóriáért.

**Hálózati sávszélesség:** Az Azure virtuális hálózat átjáró korlátozott sávszélessége az adatok az Azure virtuális hálózatba, ezért hasznos figyelni az összes Azure virtuális gépek által egy virtuális hálózaton belül kapott adatokat, hogy kitaláljuk, milyen közel van a kiválasztott Azure-átjáró termékváltozatának korlátaihoz. A HANA nagy példány egység, célszerű figyelni a bejövő és kimenő hálózati forgalmat is, és nyomon követni a kötetek, amelyek idővel kezelt.

**Lemezterület:** A lemezterület-felhasználás általában idővel növekszik. A leggyakoribb okok a következők: adatmennyiség növelése, tranzakciós napló biztonsági mentések végrehajtása, nyomkövetési fájlok tárolása és a tárolási pillanatképek végrehajtása. Ezért fontos, hogy figyelje a lemezterület-használat és a HANA nagy példány egységtársított lemezterület kezelése.

A HANA nagy példányok **II típusú ski-k** esetében a kiszolgáló az előre betöltött rendszerdiagnosztikai eszközökkel érkezik. Ezeket a diagnosztikai eszközöket használhatja a rendszer állapot-ellenőrzéséhez. Futtassa a következő parancsot az állapotellenőrző naplófájl /var/log/health_check.Run the following command to generates the health check log file at /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Amikor a Microsoft támogatási csapatával együttműködve elhárít egy problémát, a rendszer kérheti, hogy ezekkel a diagnosztikai eszközökkel adja meg a naplófájlokat. A fájlt a következő paranccsal zárhatja be.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**További lépések**

- Lásd: [Az SAP HANA (nagy példányok) figyelése az Azure-ban.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)
