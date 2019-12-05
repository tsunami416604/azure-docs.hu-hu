---
title: fájl belefoglalása
description: fájl belefoglalása
services: backup
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: ca6c57b139b195b94dc7171336cfcc34d18b1872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829055"
---
A következő korlátozások vonatkoznak a Azure Backupra.

| **Korlát** | **Alapértelmezett** |
| --- | --- |
| A tárolóban regisztrálható kiszolgálók vagy gépek. | Windows Server/Windows ügyfél/System Center Data Protection Manager: 50. <br/><br/> IaaS virtuális gépek: 1 000.  |
| Egy adatforrás mérete a tár tárolójában. |54 400 – legfeljebb GB. A korlát nem vonatkozik a IaaS virtuális gép biztonsági mentésére. |
| Backup-tárolók egy Azure-előfizetésben. |500-tároló régiónként. |
| Napi biztonsági mentések ütemezése. |Windows Server/Client: három nap.<br/> System Center DPM: két nap. <br/> IaaS virtuális gépek: naponta egyszer.  |
| Azure-beli virtuális géphez csatlakoztatott adatlemezek biztonsági mentéshez. | 16 |
| Az Azure virtuális géphez a biztonsági mentéshez csatolt egyes adatlemezek.| 32 TB|
