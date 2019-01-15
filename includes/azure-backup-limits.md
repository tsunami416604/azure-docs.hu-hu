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
ms.openlocfilehash: 9ad1208283527f35e04dede2706fd0e424096dc5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54300613"
---
Az alábbi korlátok vonatkoznak az Azure Backup szolgáltatásban.

| **Korlát** | **Alapértelmezett** |
| --- | --- |
| Egy tároló regisztrálható kiszolgálók/gépek | Windows Server/Windows Client/System Center DPM: 50 <br/><br/> IaaS virtuális gépek: 1000  |
| Egy adatforrás tároló Storage mérete |Legfeljebb 54400 GB. A korlát nem vonatkozik a IaaS VM biztonsági mentése |
| Az Azure-előfizetésre a Backup-tárolók |régiónként 500 tárolóra |
| Az ütemezett napi biztonsági mentések |A Windows-kiszolgáló vagy ügyfél: 3 nap<br/> System Center DPM: 2 nap <br/> IaaS virtuális gépek: Naponta egyszer  |
| A biztonsági mentéshez egy Azure virtuális Géphez csatolt adatlemezek | 16 |
| Azure virtuális Géphez csatolt a biztonsági mentéshez egyedi adatlemez| 4095 GB|
