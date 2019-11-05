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
ms.openlocfilehash: b1287f9c7e946c7b4d035b2ad6301947ffad3cea
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523077"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: nagy teljesítményű kialakítás

Ez a cikk útmutatást nyújt a nagy teljesítményű alkalmazások létrehozásához az Azure Premium Storage használatával. A jelen dokumentumban ismertetett útmutatást az alkalmazás által használt technológiákra vonatkozó legjobb teljesítményű gyakorlattal kombinálva is használhatja. Az irányelvek szemléltetése érdekében a jelen dokumentumban SQL Server a Premium Storage-on való futtatását használták példaként.

A cikkben található tárolási réteg teljesítmény-forgatókönyvei esetében optimalizálni kell az alkalmazás rétegét. Ha például egy SharePoint-farmot futtat az Azure Premium Storageon, a cikk SQL Server példáit követve optimalizálhatja az adatbázis-kiszolgálót. Emellett optimalizálja a SharePoint-farm webkiszolgálóját és az alkalmazáskiszolgáló a legtöbb teljesítmény eléréséhez.

Ez a cikk segítséget nyújt az alkalmazások teljesítményének az Azure Premium Storage-on való optimalizálásával kapcsolatos gyakori kérdések megválaszolásához.

* Az alkalmazás teljesítményének mérése  
* Miért nem látja a várt nagy teljesítményt?  
* Milyen tényezők befolyásolják az alkalmazás teljesítményét Premium Storage?  
* Hogyan befolyásolják ezek a tényezők az alkalmazás teljesítményét Premium Storage?  
* Hogyan lehet optimalizálni a IOPS, a sávszélességet és a késést?  

Ezeket az irányelveket kifejezetten a Premium Storagehoz, mert a Premium Storage futó munkaterhelések nagy teljesítményű. Szükség esetén példákat is biztosítottunk. Ezen irányelvek némelyikét a standard szintű IaaS virtuális gépeken futó alkalmazásokra is alkalmazhatja.