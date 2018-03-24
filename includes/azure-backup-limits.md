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
ms.openlocfilehash: b345283f87c446ff3b583b0c5dd8a4be222303ae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
A következő korlátozások vonatkoznak az Azure biztonsági mentés.

| Korlát azonosítója | Alapértelmezett korlát |
| --- | --- |
| Minden egyes tárolóban elleni regisztrálható kiszolgálók/gépek számát |a Windows Server/ügyfél/SCDPM 50 <br/> 200 IaaS virtuális gépekhez |
| Egy adatforrás tároló Azure storage-ban tárolt adatok mérete |54400 GB max<sup>1</sup> |
| Mentési tárolók hozható létre az egyes Azure-előfizetések száma |Régiónként 25 recovery Services-tárolók |
| Ennyiszer naponta biztonsági mentés ütemezése |a Windows Server vagy Windows-ügyfélen naponta 3 <br/> az SCDPM naponta 2 <br/> Infrastruktúra-szolgáltatási virtuális gépek naponta egyszer |
| A biztonsági mentéshez Azure virtuális géphez csatolt adatok lemezek |16 |
| Egy Azure virtuális géphez a biztonsági mentéshez egyedi adatokat lemez mérete| 4095 GB <sup>2</sup>|

* <sup>1</sup>infrastruktúra-szolgáltatási virtuális gép biztonsági mentése nem vonatkozik a 54400 GB-os korlátot.
 

