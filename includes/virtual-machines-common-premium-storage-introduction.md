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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717122"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Az Azure premium storage: nagy teljesítményű rendszer tervezése

Ez a cikk az Azure Premium Storage nagy teljesítményű alkalmazások létrehozásához nyújt útmutatást. Ez a dokumentum az alkalmazása által használt technológiák alkalmazandó ajánlott eljárások teljesítményének kombinálva szereplő utasítások is használhatja. Az irányelvek mutatja be, ebben a dokumentumban példaként a Premium Storage futó SQL Server rendelkezik használtuk.

Azt a ebben a cikkben a tárolási réteg teljesítmény-forgatókönyveket érintenek, miközben kell optimalizálni az alkalmazásrétegre. Például ha a SharePoint-farmok Azure Premium Storage üzemelteti, használhatja az SQL Server példákat, ez a cikk a optimalizálhatja az adatbázis-kiszolgáló. Ezenkívül optimalizálhatja a SharePoint-Farm webkiszolgáló és a legtöbb teljesítmény alkalmazáskiszolgáló.

Ez a cikk segít a válasz a következő gyakori kérdésekre az Azure Premium Storage, az alkalmazás teljesítményének optimalizálása

* Hogyan lehet az alkalmazás teljesítményének méréséhez?  
* Miért nem lát várt nagy teljesítményű?  
* Milyen tényezők befolyásolják az alkalmazás teljesítményét a Premium Storage?  
* Hogyan hajtsa végre ezeket a tényezők befolyásolják a Premium Storage az alkalmazás teljesítményét?  
* Hogyan, optimalizálhatja az IOPS, sávszélesség és késés?  

Adtunk ezeket az irányelveket kifejezetten a Premium Storage számára, mert a prémium szintű Storage futó számítási feladatok magas teljesítmény-és nagybetűket. Példák adtunk meg, ahol szükséges. Is alkalmazhat az egyes ezeket az irányelveket, Standard szintű Storage-lemezekkel IaaS virtuális gépeken futó alkalmazások.