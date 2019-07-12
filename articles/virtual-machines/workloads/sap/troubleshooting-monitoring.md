---
title: SAP Hana az Azure-ban (nagyméretű példányok) figyelése |} A Microsoft Docs
description: Ez a figyelő SAP HANA az Azure-beli (nagyméretű példányok).
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
ms.openlocfilehash: 93b8a03a46645683c98b6be3f7af83b3c7ac0e06
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708959"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Azure-beli SAP HANA (nagyméretű példányok) figyelése

SAP HANA az Azure-ban (nagyméretű példányok) nem különbözik bármely más konfigurációért IaaS – kell figyelnie, mi az operációs rendszer és az alkalmazás állapotát, és hogyan az alkalmazások használnak-e a következő erőforrásokat:

- CPU
- Memory (Memória)
- Hálózati sávszélesség
- Lemezterület

Az Azure Virtual Machines, döntse el, hogy elegendő-e a fent megnevezett erőforrásosztályokat kell, vagy azok lekérése elfogytak. Az egyes a különböző osztályokhoz a következő további részletek:

**CPU-felhasználás erőforrás:** Győződjön meg arról, hogy az SAP az egyes számítási feladatok HANA ellen definiálva arány kényszerítve van, győződjön meg arról, hogy az elegendő Processzor-erőforrások a memóriában tárolt adatokon keresztül elérhető legyen. Mindazonáltal néhány esetben előfordulhat használ fel, ahol HANA miatt indexek vagy hasonló problémák hiányzik a lekérdezések végrehajtása több processzort. Ez azt jelenti, célszerű figyelemmel kísérni a HANA nagyméretű példányok egység, valamint a Processzor-erőforrások megadott konkrét szolgáltatások HANA által igénybe vett erőforrás-használat Processzor.

**Memória-felhasználás:** Fontos a egységen belüli HANA, valamint a HANA-en kívül a figyelése. Belül HANA az adatok hogyan fogyassza az lefoglalt memória ahhoz, hogy belül a szükséges méretre irányelveket az SAP HANA figyelése. Is figyelni kívánt memóriát, győződjön meg arról, hogy további telepített nem-HANA szoftver nem túl sok memóriát, és ezért versenyezni HANA memóriában, a nagyméretű példányok szintjén.

**Hálózati sávszélesség:** Az Azure VNet-átjáró csak korlátozott sávszélesség az adatok helyezi át az Azure virtuális hálózat, ezért érdemes az összes az Azure virtuális gépek egy VNet és döntse el, hogyan zárja be, amelyek az Azure-átjáró kiválasztott Termékváltozat keretein belül által fogadott adatok figyelése. A nagyméretű HANA-példány egységen, jelentéssel bírnak a bejövő és kimenő hálózati forgalom figyelésére, valamint és a kötetek idővel kezelt nyomon követéséhez.

**Lemezterület:** Lemezterület-felhasználást általában idővel növekszik. Leggyakoribb okok a következők: adatok mennyisége növekszik, a tranzakciónapló biztonsági mentéseivel, nyomkövetési fájlok tárolásához és a pillanatképek tárolási végrehajtása végrehajtásának. Ezért fontos lemezterület-használat figyelése és felügyelete a lemezterület, a nagyméretű HANA-példány egységhez társított.

Az a **típus II termékváltozatok** HANA nagyméretű példányok az a kiszolgáló tartalmaz az előre betöltött diagnosztikai eszközei. Ezek a diagnosztikai eszközök az állapot-ellenőrzés végrehajtásához használhat. A következő parancsot a health ellenőrzési naplófájl /var/log/health_check állít elő.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Ha a probléma elhárításához Support csapat dolgozik, előfordulhat, hogy is megkérdezi, hogy a naplófájlok biztosít a diagnosztikai eszközök használatával. A fájl a következő paranccsal is zip.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Következő lépések**

- Tekintse meg [figyelése az Azure-beli SAP HANA (nagyméretű példányok)](troubleshooting-monitoring.md).