---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518063"
---
![Képernyőkép az f i o kimenetről, amely az r = 22.8 k Kiemelt értékre mutat.](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

A Standard_D8s_v3 összesen 28 600 IOPS érhető el. A metrikák használatával vizsgáljuk meg, mi történik, és azonosítjuk a Storage IO szűk keresztmetszetét. A bal oldali panelen válassza a **metrikák**elemet:

![Képernyőfelvétel a bal oldali ablaktáblán Kiemelt metrikák megjelenítéséhez.](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Először vessünk egy pillantást a **virtuális gép gyorsítótárazott IOPS felhasznált százalékos** metrikára:

![Képernyőfelvétel: V M gyorsítótárazott I O P S felhasznált százalék.](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Ez a mérőszám azt jelzi, hogy a virtuális gép gyorsítótárazott IOPS kiosztott 16 000 IOPS 61%-a használatban van. Ez a százalék azt jelenti, hogy a tároló i/o-szűk keresztmetszete nem a gyorsítótárazott lemezekkel van ellátva, mert nem 100%-on. Most nézzük meg, mi a **virtuális gép nem gyorsítótárazott IOPS felhasznált százalékos** mérőszáma:

![Képernyőfelvétel: a V M gyorsítótárba helyezett I O P-k százalékos aránya.](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

A metrika értéke 100%. Azt mondja nekünk, hogy a virtuális gép nem gyorsítótárazott IOPS kiosztott összes 12 800-IOPS használatban van. A probléma megoldásának egyik módja, ha megváltoztatjuk a virtuális gép méretét egy nagyobb méretűre, amely képes kezelni a további IO-t. Mielőtt azonban megtesszük ezt, nézzük meg a csatlakoztatott lemezt, hogy megtudja, hány IOPS látnak. Az operációsrendszer-lemez ellenőrzéséhez tekintse meg az **operációsrendszer-lemez IOPS felhasznált százalékát**:

![Képernyőfelvétel: O S lemez – o P S felhasznált százalék.](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Ez a metrika azt jelzi, hogy a P30 operációsrendszer-lemezre kiépített 5 000 IOPS körülbelül 90%-a használatban van. Ez a százalék azt jelenti, hogy az operációsrendszer-lemezen nincs szűk keresztmetszet. Most nézzük meg a virtuális géphez csatolt adatlemezeket, ha megtekinti az **adatlemez IOPS felhasznált százalékát**:

![Az adatlemez I O P S felhasznált százalékát ábrázoló képernyőkép.](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Ez a metrika azt jelzi, hogy az összes csatlakoztatott lemez átlagos IOPS-értéke 42% körül van. Ez a százalék a lemezek által használt IOPS alapján számítható ki, és nem a gazdagép-gyorsítótárból van kiszolgálva. Részletesebben is megvizsgáljuk ezt a metrikát a metrikák *felosztásának* alkalmazásával és a LUN értékének felosztásával:

![Képernyőfelvétel: az adatlemez I O P-S kihasználtságának százalékos aránya a felosztással.](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Ez a metrika azt jelzi, hogy a 3. és 2. logikai egységben csatolt adatlemezek a kiépített IOPS körülbelül 85%-át használják. Az i/o-ábra a virtuális gép és a lemezek architektúrájának megjelenését mutatja be:

![Példa a Storage I O mérőszámokra.](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
