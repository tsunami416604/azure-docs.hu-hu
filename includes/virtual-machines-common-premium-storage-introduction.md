---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74005557"
---
Ez a cikk útmutatást nyújt a nagy teljesítményű alkalmazások létrehozásához az Azure Premium Storage használatával. A jelen dokumentumban ismertetett útmutatást az alkalmazás által használt technológiákra vonatkozó legjobb teljesítményű gyakorlattal kombinálva is használhatja. Az irányelvek szemléltetése érdekében a jelen dokumentumban SQL Server a Premium Storage-on való futtatását használták példaként.

A cikkben található tárolási réteg teljesítmény-forgatókönyvei esetében optimalizálni kell az alkalmazás rétegét. Ha például egy SharePoint-farmot futtat az Azure Premium Storageon, a cikk SQL Server példáit követve optimalizálhatja az adatbázis-kiszolgálót. Emellett optimalizálja a SharePoint-farm webkiszolgálóját és az alkalmazáskiszolgáló a legtöbb teljesítmény eléréséhez.

Ez a cikk segítséget nyújt az alkalmazások teljesítményének az Azure Premium Storage-on való optimalizálásával kapcsolatos gyakori kérdések megválaszolásához.

* Az alkalmazás teljesítményének mérése  
* Miért nem látja a várt nagy teljesítményt?  
* Milyen tényezők befolyásolják az alkalmazás teljesítményét Premium Storage?  
* Hogyan befolyásolják ezek a tényezők az alkalmazás teljesítményét Premium Storage?  
* Hogyan lehet optimalizálni a IOPS, a sávszélességet és a késést?  

Ezeket az irányelveket kifejezetten a Premium Storagehoz, mert a Premium Storage futó munkaterhelések nagy teljesítményű. Szükség esetén példákat is biztosítottunk. Ezen irányelvek némelyikét a standard szintű IaaS virtuális gépeken futó alkalmazásokra is alkalmazhatja.