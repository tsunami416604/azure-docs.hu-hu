---
title: A Terraform használata az Azure-ral
description: Bevezetés a Terraform használatára és az Azure-infrastruktúra üzembe helyezésére.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 1c6ac9b67f556b039b9ffd5ed725ea1f24aeeb3a
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969242"
---
# <a name="terraform-with-azure"></a>A Terraform használata az Azure-ral

A [Hashicorp Terraform](https://www.terraform.io/) egy nyílt forráskódú eszköz, amely felhőinfrastruktúra kiépítésére és kezelésére használható. A kodifikálja az infrastruktúrát a felhőalapú erőforrások topológiáját leíró konfigurációs fájlokban. Ezek az erőforrások a következők: Virtual Machines, Storage-fiókok és hálózati adapterek. A Terraform CLI egyszerű mechanizmust biztosít a konfigurációs fájlok Azure-ba történő üzembe helyezéséhez és verziójának telepítéséhez.

Ez a cikk az Azure-infrastruktúra Terraformmal végzett felügyeletének előnyeit ismerteti.

## <a name="automate-infrastructure-management"></a>Az infrastruktúra-kezelés automatizálása.

A Terraform sablonalapú konfigurációs fájljai lehetővé teszik Azure-erőforrások ismételhető és kiszámítható módon történő megadását, kiépítését és konfigurálását. Az infrastruktúra automatizálásának számos előnye van:

- Csökkenti az emberi hibák esélyét az infrastruktúra üzembe helyezése és kezelése során.
- Ugyanazt a sablont helyezi többször üzembe, ezzel azonos fejlesztési, tesztelési és éles környezetet hoz létre.
- Csökkenti a fejlesztési és tesztelési környezetek költségeit, mivel igény szerint hozza létre őket.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Az infrastruktúra változásainak megismerése az alkalmazás előtt

Ahogy az erőforrás-topológia összetetté válik, az infrastruktúra-módosítások jelentésének és hatásának megértése bonyolult lehet.

A Terraform CLI lehetővé teszi, hogy a felhasználók érvényesítsék és előtekintsenek az infrastruktúra változásait az alkalmazás előtt. Az infrastrukturális változások biztonságos módon történő megtekintésének számos előnye van:
- A javasolt módosítások és azok hatásainak gyors megismerésével a csoporttagok közötti együttműködés hatékonyabb lehet.
- A nem szándékos módosítások a fejlesztési folyamat korai szakaszában észrevehetők

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Infrastruktúra üzembe helyezése több felhőbe

A Terraform egy infrastruktúra üzembe helyezése több felhőalapú szolgáltatón keresztül. Lehetővé teszi, hogy a fejlesztők konzisztens eszközöket használjanak az egyes infrastruktúra-definíciók kezeléséhez.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a Terraformot és előnyeit, tekintse meg a javasolt következő lépéseket:

- Első lépésként a [telepítse a Terraformot, majd konfigurálja az Azure használatához](/azure/virtual-machines/linux/terraform-install-configure).
- [Hozzon létre egy Azure-beli virtuális gépet a Terraformmal](/azure/virtual-machines/linux/terraform-create-complete-vm).
- Fedezze fel a [Terraform Azure Resource Manager-modulját](https://www.terraform.io/docs/providers/azurerm/). 