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
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016546"
---
![Metrikák menü](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

A Standard_D8s_v3 azonban összesen 28 600 IOPs-t érhet el, a metrikák használatával megvizsgálhatja, hogy mi történik, és azonosíthatja a tárhely IO-szűk keresztmetszetét. Először keresse meg a metrikák gombot a bal oldali menüben, és jelölje ki:

![Metrikák menü](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Először vessünk egy pillantást a **virtuális gép gyorsítótárazott IOPS felhasznált százalékos** metrikára:

![A virtuális gép gyorsítótárazott IOPS felhasznált százalék](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Ez a mérőszám azt jelzi, hogy a virtuális gép gyorsítótárazott IOPs kiosztott 16 000 IOPs a rendszer 61%-ot használ. Ez azt jelenti, hogy a tároló i/o-szűk keresztmetszete nem a gyorsítótárazott lemezekre van leképezve, mert nem 100%. Most nézzük meg a **virtuális gép gyorsítótárban lévő IOPS felhasznált százalékos** metrikáját:

![A virtuális gép gyorsítótárban lévő IOPS felhasznált százaléka](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Ez a metrika 100%-os, ami azt jelzi, hogy a virtuális gép nem gyorsítótárazott IOPs tartozó összes 12 800 IOPs használatban van. Ennek egyik módja, ha megváltoztatjuk a virtuális gép méretét egy nagyobb méretűre, amely képes kezelni a további IO-t. Azonban mielőtt ezt megtennénk, vessünk egy pillantást a csatlakoztatott lemezre, hogy lássa, hány IOPs látnak. Először tekintse meg az operációsrendszer-lemezt az **operációsrendszer-lemez IOPS felhasznált százalékával**:

![OPERÁCIÓSRENDSZER-lemez IOPS felhasznált százaléka](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Ez a mérőszám azt jelzi, hogy a P30 operációsrendszer-lemezre kiépített 5 000 IOPs a (z) 90%-a használatban van. Ez azt jelenti, hogy az operációsrendszer-lemezen nincs szűk keresztmetszet. Most vessünk egy pillantást a virtuális géphez csatolt adatlemezekre, ha megtekinti az **adatlemez IOPS felhasznált százalékát**:

![Adatlemez IOPS felhasznált százaléka](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Ez a mérőszám azt jelzi, hogy az összes csatlakoztatott lemez átlagos IOPs-értéke körülbelül 42%. Ezt a százalékarányt a lemezek által használt IOPs alapján számítjuk ki, és nem a gazdagép-gyorsítótárból lesznek kézbesítve. Részletesebben is bemutatjuk ezt a metrikát, hogy a következő lépéseket alkalmazzuk a metrikák **felosztásának** alkalmazásával, és a LUN értéke alapján felosztjuk:

![Adatlemez-IOPS felhasznált százalékos aránya felosztással](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Ez a mérőszám azt jelzi, hogy a 3. és 2. logikai egységben csatolt adatlemezek a kiépített IOPs körülbelül 85%-át használják. Az i/o-t a virtuális gép és a lemezek architektúrája alapján a következőképpen tekintheti meg:

![Storage IO-mérőszámok – példa diagram](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
