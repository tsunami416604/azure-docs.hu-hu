---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6c7a9857f6d6d57dc9e314bcb1ef848a326b7ed2
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49437109"
---
## <a name="terminology"></a>Terminológia

Piactéri lemezképet az Azure-ban az alábbi attribútumok tartoznak:

* **Közzétevő**: A szervezet, amely a lemezkép létrehozása. Példák: Canonical, MicrosoftWindowsServer
* **Ajánlat**: a közzétevő által létrehozott kapcsolódó képek egy csoport nevét. Példák: Ubuntu Server, WindowsServer
* **Termékváltozat**: egy ajánlatot, például egy terjesztési jelentős kiadása egy példányát. Példák: 16.04-LTS, 2016-Datacenter
* **Verzió**: Termékváltozat lemezkép verziószámát. 

Piactéri lemezképet azonosítására programozott módon üzembe helyezése egy virtuális Gépet, adja meg ezeket az értékeket egyesével paraméterekként. Egyes eszközök fogadja el a kép *URN*, amely kombinálja ezeket az értékeket, a kettőspont (:) elválasztva: *közzétevő*:*ajánlat*:*termékváltozat*: *Verzió*. Az URN lecserélheti a verziószámot a "legújabb", amely a rendszerkép legújabb verzióját választja ki. 

Ha a kép közzétevőjének biztosít további licenc és a vásárlási feltételeket, majd kell fogadniuk a feltételeket és programozott üzembe helyezés engedélyezése. Is kell adnia *megvásárlása* paramétereket a programozott módon üzembe helyezése egy virtuális Gépet. Lásd: [rendszerkép üzembe helyezése a Marketplace használati feltételei](#deploy-an-image-with-marketplace-terms).