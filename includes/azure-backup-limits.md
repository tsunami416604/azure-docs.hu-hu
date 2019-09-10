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
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "67179519"
---
Az alábbi korlátok vonatkoznak az Azure Backup szolgáltatásban.

| **Korlát** | **Alapértelmezett** |
| --- | --- |
| A tárolóban regisztrálható kiszolgálók vagy gépek. | Windows Server/Windows ügyfél/System Center Data Protection Manager: 50. <br/><br/> IaaS virtuális gépek: 1 000.  |
| Egy adatforrás mérete a tár tárolójában. |54 400 – legfeljebb GB. A korlát nem vonatkozik a IaaS virtuális gép biztonsági mentésére. |
| Backup-tárolók egy Azure-előfizetésben. |500-tároló régiónként. |
| Napi biztonsági mentések ütemezése. |Windows Server/Client: Három nap.<br/> System Center DPM: Két nap. <br/> IaaS virtuális gépek: Naponta egyszer.  |
| Azure-beli virtuális géphez csatlakoztatott adatlemezek biztonsági mentéshez. | 16 |
| Az Azure virtuális géphez a biztonsági mentéshez csatolt egyes adatlemezek.| 4,095 GB|
