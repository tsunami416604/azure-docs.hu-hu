---
title: "fájl"
description: "fájl"
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk
ms.custom: include file
ms.openlocfilehash: 7ca5b34961b4d0e3d4fcecb8175e3e0901d7049d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
A következő korlátozások vonatkoznak az Azure biztonsági mentés.

| Korlát azonosítója | Alapértelmezett korlát |
| --- | --- |
| Minden egyes tárolóban elleni regisztrálható kiszolgálók/gépek számát |a Windows Server/ügyfél/SCDPM 50 <br/> 200 IaaS virtuális gépekhez |
| Egy adatforrás tároló Azure storage-ban tárolt adatok mérete |54400 GB max<sup>1</sup> |
| Mentési tárolók hozható létre az egyes Azure-előfizetések száma |Régiónként 25 recovery Services-tárolók |
| Ennyiszer naponta biztonsági mentés ütemezése |a Windows Server vagy Windows-ügyfélen naponta 3 <br/> az SCDPM naponta 2 <br/> Infrastruktúra-szolgáltatási virtuális gépek naponta egyszer |
| A biztonsági mentéshez Azure virtuális géphez csatolt adatok lemezek |16 |
| Egy Azure virtuális géphez a biztonsági mentéshez egyedi adatokat lemez mérete| 1024 GB <sup>2</sup>|

* <sup>1</sup>infrastruktúra-szolgáltatási virtuális gép biztonsági mentése nem vonatkozik a 54400 GB-os korlátot.
* <sup>2</sup> tudunk egy [private Preview verziójára](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a?redir=0) lemezek legfeljebb 4 TB-os támogatásához. 

