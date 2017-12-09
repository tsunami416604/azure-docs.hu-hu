---
title: "Az Azure-ral Terraform használatával"
description: "Terraform segítségével vesion bemutatása és központi telepítése az Azure-infrastruktúra."
ms.service: virtual-machines-linux
keywords: "terraform, devops, áttekintése, tervezze meg, alkalmazhatja és automatizálásához"
author: binderjoe
ms.author: jbinder
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 667752d8830cdac5e2338fd3ed7904917123be94
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="terraform-with-azure"></a>Az Azure-ral Terraform

[Hashicorp Terraform](https://www.terraform.io/) egy nyílt forráskódú eszköz kiépítése és kezelése a felhőalapú infrastruktúra. Az egységes infrastruktúra konfigurációs fájlokat, amelyek a felhőalapú erőforrások, például a virtuális gépek, a storage-fiókok és a hálózati adapterek topológia ismertetik. Terraform a parancssori felület (CLI) biztosít egy egyszerű módszer központi telepítéséhez és a verzió a konfigurációs fájlok Azure vagy bármely más támogatott felhő.

Ez a cikk ismerteti az Azure infrastruktúra kezelése érdekében Terraform használatának előnyeit.

## <a name="automate-infrastructure-management"></a>Automatizált infrastruktúra felügyelete.

Terraform meg sablon-alapú konfigurációs fájlok lehetővé teszik a meghatározását, telepítéséhez és ismételhető és kiszámítható módon Azure-erőforrások konfigurálása. Automatizálása infrastruktúra rendelkezik számos előnnyel jár:

- Csökkenti a lehetséges emberi hibákat telepítése és infrastruktúra kezelése közben.
- Hozzon létre azonos fejlesztői, tesztelési és éles környezetben telepíti többször a ugyanazt a sablont.
- Csökkenti a költségeket fejlesztési és tesztkörnyezetek hozza létre őket az igény szerint.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Infrastrukturális változtatást megértéséhez lépésük előtt 

Erőforrásként topológia összetett, pontos jelentése ismertetése válik, és infrastrukturális változtatást hatását bonyolult lehet.

Terraform biztosít egy parancssori felület (CLI), amely lehetővé teszi a felhasználóknak és infrastrukturális változtatást előzetes telepítésük előtt ellenőrizheti. Előnézet infrastrukturális változtatást széfben, hatékony módon van számos előnnyel jár:
- Csoporttagok hatékonyabban együttműködhetnek gyorsan megismerni a változtatási és kihatásukat.
- A fejlesztési folyamat korai szakaszaiban nem szándékolt módosításokat is kezelve


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Több felhők infrastruktúra telepítése

Terraform esetén több felhős rendszerekben, amelyen üzembe van helyezve hasonló infrastruktúra Azure és a további szolgáltatók vagy a helyszíni adatközpontokkal népszerű eszköz választani. Ez lehetővé teszi a fejlesztők számára, hogy az azonos eszközök és a konfigurációs fájlok segítségével kezelheti az infrastruktúra több felhőalapú szolgáltatók.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintést Terraform és előnyeit, az alábbiakban javasolt lépéseket:

- Első lépések [Terraform telepítése és konfigurálása az Azure használatára](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
- [Hozzon létre egy Azure virtuális gép Terraform használatával](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm)
- Megismerkedhet a [Terraform Azure Resource Manager-modul](https://www.terraform.io/docs/providers/azurerm/) 