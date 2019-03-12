---
title: A Terraform használata az Azure-ral
description: Bevezetés a Terraform használatával verzióra és üzembe helyezése az Azure-infrastruktúra.
services: terraform
ms.service: azure
keywords: terraform, devops, áttekintés, megtervezés, alkalmazás, automatizálás
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/31/2018
ms.openlocfilehash: 8b5ad3f060e2c8760014d7fc1990b971976388f7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779198"
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