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
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174959"
---
## <a name="terminology"></a>Terminológia

Az Azure Piactéri rendszerképének jellemzői a következők:

* **Közzétevő**: A rendszerképet létrehozó szervezet. Példák: Canonical, MicrosoftWindowsServer
* **Ajánlat**: A közzétevő által létrehozott kapcsolódó rendszerképek csoportjának neve. Példák: UbuntuServer, WindowsServer
* **SKU**: Az ajánlat egy példánya, például egy disztribúció főverziója. Példák: 18.04-LTS, 2019-Datacenter
* **Verzió**: Egy rendszerkép-SKU verziószáma. 

Ha programozott módon telepíti a piactér lemezképét, adja meg ezeket az értékeket paraméterekként. Egyes eszközök elfogadnak egy *rendszerképet,* amely egyesíti ezeket az értékeket a kettősponttal elválasztva (:) karakter *Közzétevő*:*ajánlat*:*SKU*:*Version*. Az URN-ben a verziószámot a "legújabb" értékre cserélve kiválaszthatja a lemezkép legújabb verzióját. 

Ha a rendszerkép kiadója további licenceket és vásárlási feltételeket biztosít, akkor el kell fogadnia ezeket a feltételeket, és engedélyeznie kell a programozott telepítést. Ha programozott módon telepíti a virtuális gépet, meg kell adnia a *vásárlási terv* paramétereit is. Lásd: [lemezkép üzembe helyezése Piactéri feltételekkel](#deploy-an-image-with-marketplace-terms).
