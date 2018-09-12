---
title: fájl belefoglalása
description: fájl belefoglalása
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 9/10/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 64101ea5a3bbaac4a6b2e349a04d06ea84a87081
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381085"
---
Az alábbi korlátok vonatkoznak az Azure Backup szolgáltatásban.

| Korlátazonosító | Alapértelmezett korlát |
| --- | --- |
| Egyes tárolókhoz regisztrálható kiszolgálók/gépek száma |a Windows Server/ügyfélen/SCDPM 50 <br/> 1000 IaaS virtuális gépekhez |
| Egy adatforrás tároló Azure storage-ban tárolt adatok mérete |54400 GB maximális<sup>1</sup> |
| Az egyes Azure-előfizetésekben létrehozható biztonsági mentési tárolók száma |Régiónként 500 recovery Services-tárolók |
| Hányszor ütemezett biztonsági mentés naponta |a Windows Server vagy Windows-ügyfél naponta 3 <br/> 2-es SCDPM <br/> IaaS virtuális gépekhez naponta egyszer |
| Az Azure virtuális gép biztonsági mentésének csatlakoztatott adatlemezek |16 |
| Egyéni adatok lemez csatlakozik az Azure virtuális gép biztonsági mentésének mérete| 4095 GB|

* <sup>1</sup>a 54400 GB-os korlát nem vonatkozik a IaaS virtuális gépek biztonsági mentését.
 

