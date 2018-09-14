---
title: A Terraform használata az Azure-ral
description: Bevezetés a Terraform használatába Azure infrastruktúra verziókezeléséhez és üzembe helyezéshez.
services: terraform
ms.service: terraform
keywords: terraform, devops, áttekintés, megtervezés, alkalmazás, automatizálás
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/31/2018
ms.openlocfilehash: dd340238f8a70c1dd0cfc172976bef6b1ad282b1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665629"
---
# <a name="terraform-with-azure"></a>A Terraform használata az Azure-ral

A [Hashicorp Terraform](https://www.terraform.io/) egy nyílt forráskódú eszköz, amely felhőinfrastruktúra kiépítésére és kezelésére használható. Kodifikálja az infrastruktúrát a felhőerőforrások (például virtuális gépek, tárfiókok, hálózati adapter) topológiáját leíró konfigurációs fájlokban. A Terraform parancssori felülete (CLI) egy egyszerű mechanizmust biztosít a konfigurációs fájlok üzembe helyezéséhez és a verziókezeléshez az Azure-ban vagy más támogatott felhőszolgáltatásban.

Ez a cikk az Azure-infrastruktúra Terraformmal végzett felügyeletének előnyeit ismerteti.

## <a name="automate-infrastructure-management"></a>Az infrastruktúra-kezelés automatizálása.

A Terraform sablonalapú konfigurációs fájljai lehetővé teszik Azure-erőforrások ismételhető és kiszámítható módon történő megadását, kiépítését és konfigurálását. Az infrastruktúra automatizálásának számos előnye van:

- Csökkenti az emberi hibák esélyét az infrastruktúra üzembe helyezése és kezelése során.
- Ugyanazt a sablont helyezi többször üzembe, ezzel azonos fejlesztési, tesztelési és éles környezetet hoz létre.
- Csökkenti a fejlesztési és tesztelési környezetek költségeit, mivel igény szerint hozza létre őket.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Az infrastruktúra módosításainak megismerése azok alkalmazása előtt 

Ahogy az erőforrás-topológia összetetté válik, az infrastruktúra-módosítások jelentésének és hatásának megértése bonyolult lehet.

A Terraform olyan parancssori felületet (CLI) biztosít, amellyel a felhasználók érvényesíthetik és előnézetben megtekinthetik az infrastruktúra módosításait még azok üzembe helyezése előtt. Az infrastruktúra-módosítások biztonságos és hatékony előzetes megtekintésének számos előnye van:
- A javasolt módosítások és azok hatásainak gyors megismerésével a csoporttagok közötti együttműködés hatékonyabb lehet.
- A nem szándékos módosítások a fejlesztési folyamat korai szakaszában észrevehetők


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Infrastruktúra üzembe helyezése több felhőbe

A Terraform egy népszerű eszköz többfelhős forgatókönyvek esetében, amelyekben hasonló infrastruktúra van üzembe helyezve az Azure-ban és további felhőszolgáltatókon vagy helyszíni adatközpontokban. Lehetővé teszi a fejlesztők számára, hogy ugyanazokat az eszközöket és konfigurációs fájlokat használják az infrastruktúra kezelésére több felhőszolgáltatón is.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a Terraformot és előnyeit, tekintse meg a javasolt következő lépéseket:

- Első lépésként a [telepítse a Terraformot, majd konfigurálja az Azure használatához](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
- [Hozzon létre egy Azure-beli virtuális gépet a Terraformmal](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm).
- Fedezze fel a [Terraform Azure Resource Manager-modulját](https://www.terraform.io/docs/providers/azurerm/). 