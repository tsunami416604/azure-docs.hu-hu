---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 2af8b429b6addf6da32b34773525c51a36624e78
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242154"
---
Az Azure-ban lehetőség van arra, hogy a lemezes tárolás IOPS és a MB/s teljesítményt is felgyorsítsa, amely a Virtual Machines és a lemezekre is feltört. A mellszobor sok esetben hasznos, például a váratlan lemezes adatforgalom kezelése vagy a Batch-feladatok feldolgozása. A virtuális gép és a lemez szintjének hatékony kihasználásával hatékonyan kihasználhatja a virtuális gépeken és a lemezeken elérhető kiváló alapkonfigurációt és a feltört teljesítményt. Így a virtuális gépen és a lemezen is nagy teljesítményű és feltört teljesítményt érhet el. 

Vegye figyelembe, hogy a lemezek és a virtuális gépek betörése egymástól független. Ha kitört lemez van, nincs szükség a feltört virtuális gépre, hogy a lemez fel legyen tört. Ha kitört virtuális géppel rendelkezik, nincs szükség a kitört lemezre, hogy a virtuális gép fel lehessen robbanni. 