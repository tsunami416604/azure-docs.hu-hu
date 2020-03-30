---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74005557"
---
Ez a cikk az Azure Premium Storage használatával nagy teljesítményű alkalmazások készítésére vonatkozó irányelveket ismerteti. A jelen dokumentumban található utasításokat az alkalmazás által használt technológiákra vonatkozó gyakorlati tanácsok együttesen használhatja. Az irányelvek szemléltetése érdekében példaként használtuk a prémium szintű storage-on futó SQL Servert ebben a dokumentumban.

Bár ebben a cikkben a tárolási réteg teljesítményforgatókönyveit kezeljük, optimalizálnia kell az alkalmazásréteget. Ha például egy SharePoint Farmot üzemeltet az Azure Premium Storage szolgáltatásban, a cikkből származó SQL Server-példákkal optimalizálhatja az adatbázis-kiszolgálót. Emellett optimalizálja a SharePoint Farm webkiszolgálóját és alkalmazáskiszolgálóját a legnagyobb teljesítmény érdekében.

Ez a cikk segít megválaszolni a következő gyakori kérdésekoptimalizálása alkalmazás teljesítményét az Azure Premium Storage,

* Hogyan mérjük az alkalmazás teljesítményét?  
* Miért nem látod a várt nagy teljesítményt?  
* Milyen tényezők befolyásolják az alkalmazás teljesítményét a Prémium szintű storage-ban?  
* Hogyan befolyásolják ezek a tényezők az alkalmazás teljesítményét a Prémium szintű storage-ban?  
* Hogyan optimalizálhatja az IOPS, sávszélesség és a késés?  

Ezeket az irányelveket kifejezetten a prémium szintű storage-hoz biztosítottuk, mivel a prémium szintű storage-on futó számítási feladatok nagy teljesítményérzékenyek. Adott esetben példákat is hoztunk. Ezen irányelvek némelyike a szabványos tárolólemezekkel rendelkező IaaS virtuális gépeken futó alkalmazásokra is alkalmazhatja.