---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 47ef014f31c628fed9d7b2b005e67e8138d3e7e9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38944578"
---
## <a name="terminology"></a>Terminológia

Piactéri lemezképet az Azure-ban az alábbi attribútumok tartoznak:

* **Közzétevő** – a szervezet, amely a lemezkép létrehozása. Példák: Canonical, MicrosoftWindowsServer
* **Ajánlat** -közzétevő által létrehozott kapcsolódó képek csoport nevét. Példák: Ubuntu Server WindowsServer
* **Termékváltozat** – egy ajánlatot, például egy terjesztési jelentős kiadása példányát. Példák: 16.04-LTS, 2016-Datacenter
* **Verzió** -Termékváltozat lemezkép verziószámát. 

Piactéri lemezképet azonosítására programozott módon üzembe helyezése egy virtuális Gépet, ezeket az értékeket egyesével paraméterekként megadni, vagy bizonyos eszközök, fogadja el a kép *URN*. Az URN kombinálja ezeket az értékeket, a kettőspont (:) elválasztva: *közzétevő*:*ajánlat*:*termékváltozat*:*verzió*. Az URN lecserélheti a verziószámot a "legújabb", amely a rendszerkép legújabb verzióját választja ki. 

Ha a kép közzétevőjének biztosít további licenc és a vásárlási feltételeket, kell fogadniuk a feltételeket, és programozott üzembe helyezés engedélyezése. Emellett meg kell adnia *megvásárlása* paramétereket a programozott módon üzembe helyezése egy virtuális Gépet. Lásd: [rendszerkép üzembe helyezése a Marketplace használati feltételei](#deploy-an-image-with-marketplace-terms).