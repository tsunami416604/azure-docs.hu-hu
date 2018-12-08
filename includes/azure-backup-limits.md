---
title: fájl belefoglalása
description: fájl belefoglalása
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 63922eb623576379058c9a8a367d6e52249115f2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109093"
---
Az alábbi korlátok vonatkoznak az Azure Backup szolgáltatásban.

| **Korlát** | **Alapértelmezett** |
| --- | --- |
| Egy tároló regisztrálható kiszolgálók/gépek | A Windows Server vagy Windows ügyfél és a System Center DPM: 50 <br/><br/> IaaS virtuális gépek: 1000  |
| Egy adatforrás tároló Storage mérete |Legfeljebb 54400 GB. A korlát nem vonatkozik a IaaS VM biztonsági mentése |
| Az Azure-előfizetésre a Backup-tárolók |régiónként 500 tárolóra |
| Az ütemezett napi biztonsági mentések |A Windows-kiszolgáló vagy ügyfél: 3 nap<br/> A System Center DPM: 2 nap <br/> IaaS virtuális gépek: Naponta egyszer  |
| A biztonsági mentéshez egy Azure virtuális Géphez csatolt adatlemezek | 32 |
| Azure virtuális Géphez csatolt a biztonsági mentéshez egyedi adatlemez| 4095 GB|



