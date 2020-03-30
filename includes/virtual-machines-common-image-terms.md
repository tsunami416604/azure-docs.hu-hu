---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 98d765e2f6909f00f8dfe76d06aef017aad67adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71174959"
---
## <a name="terminology"></a>Terminológia

Az Azure-beli Piactér-lemezkép a következő attribútumokkal rendelkezik:

* **Közzétevő**: A lemezképet létrehozó szervezet. Példák: Canonical, MicrosoftWindowsServer
* **Ajánlat**: A közzétevő által létrehozott kapcsolódó képek csoportjának neve. Példák: UbuntuServer, WindowsServer
* **Termékváltozat:** egy ajánlat példánya, például egy disztribúció fő kiadása. Példák: 18.04-LTS, 2019-Datacenter
* **Verzió:** A képtermékváltozat verziószáma. 

A Marketplace-rendszerkép azonosításához, amikor egy virtuális gép telepítése programozott módon, adja meg ezeket az értékeket külön-külön paraméterekként. Egyes eszközök elfogadják a kép *URN-ét*, amely egyesíti ezeket az értékeket, kettősponttal elválasztva (:) karakter: *kiadó:**ajánlat:**Sku:**verzió*. Az URN-ben lecserélheti a verziószámot a "legújabb" kifejezésre, amely a lemezkép legújabb verzióját választja ki. 

Ha a lemezkép-közzétevő további licenc- és vásárlási feltételeket biztosít, akkor el kell fogadnia ezeket a feltételeket, és engedélyeznie kell az automatizált telepítést. A virtuális gép programozott telepítésekor is meg kell adnia *a beszerzési terv* paramétereit. Lásd: [Lemezkép üzembe helyezése piactéri kifejezésekkel.](#deploy-an-image-with-marketplace-terms)
