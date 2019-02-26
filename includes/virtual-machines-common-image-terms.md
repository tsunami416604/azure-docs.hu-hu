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
ms.openlocfilehash: 50d71a3967e61e5d531f4bfeae3582b56230a0d2
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56825867"
---
## <a name="terminology"></a>Terminológia

Piactéri lemezképet az Azure-ban az alábbi attribútumok tartoznak:

* **Közzétevő**: A rendszerképet létrehozó szervezet. Példák: Canonical, MicrosoftWindowsServer
* **Ajánlat**: A közzétevő által létrehozott kapcsolódó képek egy csoport nevét. Példák: UbuntuServer, WindowsServer
* **TERMÉKVÁLTOZAT**: Az ajánlat egy példánya, például egy disztribúció főverziója. Példák: 18.04-LTS, 2019-Datacenter
* **Verzió**: Lemezkép Termékváltozat verziószáma. 

Piactéri lemezképet azonosítására programozott módon üzembe helyezése egy virtuális Gépet, adja meg ezeket az értékeket egyesével paraméterekként. Egyes eszközök fogadja el a kép *URN*, amely kombinálja ezeket az értékeket, a kettőspont (:) elválasztva: *Közzétevő*:*ajánlat*:*termékváltozat*:*verzió*. Az URN lecserélheti a verziószámot a "legújabb", amely a rendszerkép legújabb verzióját választja ki. 

Ha a kép közzétevőjének biztosít további licenc és a vásárlási feltételeket, majd kell fogadniuk a feltételeket és programozott üzembe helyezés engedélyezése. Is kell adnia *megvásárlása* paramétereket a programozott módon üzembe helyezése egy virtuális Gépet. Lásd: [rendszerkép üzembe helyezése a Marketplace használati feltételei](#deploy-an-image-with-marketplace-terms).
