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
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31613636"
---
A következő korlátozások vonatkoznak az Azure biztonsági mentés.

| Korlát azonosítója | Alapértelmezett korlát |
| --- | --- |
| Minden egyes tárolóban elleni regisztrálható kiszolgálók/gépek számát |a Windows Server/ügyfél/SCDPM 50 <br/> 1000 IaaS virtuális gépekhez |
| Egy adatforrás tároló Azure storage-ban tárolt adatok mérete |54400 GB maximális<sup>1</sup> |
| Mentési tárolók hozható létre az egyes Azure-előfizetések száma |Régiónként 500 recovery Services-tárolók |
| Ennyiszer naponta biztonsági mentés ütemezése |a Windows Server vagy Windows-ügyfélen naponta 3 <br/> az SCDPM naponta 2 <br/> Infrastruktúra-szolgáltatási virtuális gépek naponta egyszer |
| A biztonsági mentéshez Azure virtuális géphez csatolt adatok lemezek |16 |
| Egy Azure virtuális géphez a biztonsági mentéshez egyedi adatokat lemez mérete| 4095 GB <sup>2</sup>|

* <sup>1</sup>infrastruktúra-szolgáltatási virtuális gép biztonsági mentése nem vonatkozik a 54400 GB-os korlátot.
 

