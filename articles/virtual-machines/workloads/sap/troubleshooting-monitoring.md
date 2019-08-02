---
title: Az Azure-beli SAP HANA figyelése (nagyméretű példányok) | Microsoft Docs
description: SAP HANA figyelése az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de7066004c4baa6e3086f2909d9d5150b50d8e41
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570545"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>SAP HANA (nagyméretű példányok) figyelése az Azure-ban

SAP HANA az Azure-ban (nagyméretű példányok) nem különböznek a többi IaaS-telepítéstől – figyelnie kell az operációs rendszer és az alkalmazás működéséről, valamint arról, hogy az alkalmazások hogyan használják a következő erőforrásokat:

- CPU
- Memory (Memória)
- Hálózati sávszélesség
- Lemezterület

Az Azure Virtual Machines segítségével meg kell állapítania, hogy a fent nevezett erőforrás-osztályok elegendőek-e vagy kimerítve. Az alábbiakban részletesebben ismertetjük a különböző osztályokat:

**CPU-erőforrások felhasználása:** A HANA-val kapcsolatos bizonyos számítási feladatokhoz meghatározott SAP-arány kényszerítve van, hogy a memóriában tárolt adatokon rendelkezésre álljon elegendő CPU-erőforrás. Előfordulhat azonban, hogy a HANA a hiányzó indexek vagy hasonló problémák miatt sok processzort használ a lekérdezések végrehajtásához. Ez azt jelenti, hogy figyelnie kell a HANA nagyméretű példány-egység CPU-erőforrás-felhasználását, valamint az adott HANA-szolgáltatások által felhasznált CPU-erőforrásokat.

**Memóriahasználat:** Fontos, hogy figyelje a HANA-on belül, valamint a HANA-on kívül az egységen. A HANA-n belül figyelje meg, hogyan fogyasztja el a HANA lefoglalt memóriát az SAP szükséges méretezési irányelvei között. A nagyméretű példányok szintjén is figyelnie kell a memória felhasználását, hogy a további telepített, nem HANA szoftver ne fogyasszon túl sok memóriát, így a HANA for Memory számára is versenyképesek legyenek.

**Hálózati sávszélesség:** Az Azure VNet-átjáró az Azure VNet-re áthelyezett adatforgalomban korlátozott, ezért hasznos lehet a VNet belüli összes Azure-beli virtuális gép által fogadott adat figyelése, hogy megtudja, Hogyan közelítheti meg a kiválasztott Azure Gateway SKU korlátait. A HANA nagyméretű példányok egységében a bejövő és a kimenő hálózati forgalom figyelésére is érdemes, valamint az idő múlásával kezelt kötetek nyomon követését.

**Lemezterület:** A lemezterület-felhasználás általában az idő múlásával növekszik. A leggyakoribb okok a következők: az adatmennyiség nő, a tranzakciónaplók biztonsági másolatainak végrehajtása, a nyomkövetési fájlok tárolása és a tárolási Pillanatképek végrehajtása. Ezért fontos a lemezterület-használat figyelése és a HANA nagyméretű példány-egységhez társított lemezterület kezelése.

A HANA nagyméretű példányainak **II. típusú SKU** -ban a kiszolgáló az előre betöltött diagnosztikai eszközöket tartalmazza. Ezeket a diagnosztikai eszközöket a rendszerállapot-ellenőrzés elvégzéséhez használhatja. A következő parancs futtatásával hozza létre az állapot-ellenőrzési naplófájlt a/var/log/health_check. címen.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Ha a Microsoft ügyfélszolgálata csapatával dolgozik a probléma megoldásában, a naplófájlokat a diagnosztikai eszközök használatával is megkérheti. A fájlt a következő parancs használatával zip-fájlba is elvégezheti.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Következő lépések**

- Tekintse át [a SAP HANA (nagyméretű példányok) figyelését az Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)-ban.
