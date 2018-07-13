---
title: fájl belefoglalása
description: fájl belefoglalása
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 1f41567f7d5dcc9b6536b47ef639ba53abff8ec4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755739"
---
Az alábbi korlátok vonatkoznak az Azure Backup szolgáltatásban.

| Korlátazonosító | Alapértelmezett korlát |
| --- | --- |
| Egyes tárolókhoz regisztrálható kiszolgálók/gépek száma |a Windows Server/ügyfélen/SCDPM 50 <br/> 1000 IaaS virtuális gépekhez |
| Egy adatforrás tároló Azure storage-ban tárolt adatok mérete |54400 GB maximális<sup>1</sup> |
| Az egyes Azure-előfizetésekben létrehozható biztonsági mentési tárolók száma |Régiónként 500 recovery Services-tárolók |
| Hányszor ütemezett biztonsági mentés naponta |a Windows Server vagy Windows-ügyfél naponta 3 <br/> 2-es SCDPM <br/> IaaS virtuális gépekhez naponta egyszer |
| Az Azure virtuális gép biztonsági mentésének csatlakoztatott adatlemezek |16 |
| Egyéni adatok lemez csatlakozik az Azure virtuális gép biztonsági mentésének mérete| 4095 GB <sup>2</sup>|

* <sup>1</sup>a 54400 GB-os korlát nem vonatkozik a IaaS virtuális gépek biztonsági mentését.
 

