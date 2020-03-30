---
title: A Terraform használata az Azure-ral
description: Bevezetés a Terraform használatának verzióra és az Azure-infrastruktúra üzembe helyezéséhez.
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 05b92fdf8c0a0f84d2f29b4aa7479850b2721441
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472162"
---
# <a name="terraform-with-azure"></a>A Terraform használata az Azure-ral

A [Hashicorp Terraform](https://www.terraform.io/) egy nyílt forráskódú eszköz, amely felhőinfrastruktúra kiépítésére és kezelésére használható. Kodifikálja az infrastruktúrát a felhőalapú erőforrások topológiáját leíró konfigurációs fájlokban. Ezek az erőforrások közé tartoznak a virtuális gépek, a tárfiókok és a hálózati adapterek. A Terraform CLI egy egyszerű mechanizmust biztosít a konfigurációs fájlok azure-ba való üzembe helyezéséhez és az Azure-ba való verziózatához.

Ez a cikk az Azure-infrastruktúra Terraformmal végzett felügyeletének előnyeit ismerteti.

## <a name="automate-infrastructure-management"></a>Az infrastruktúra-kezelés automatizálása.

A Terraform sablonalapú konfigurációs fájljai lehetővé teszik Azure-erőforrások ismételhető és kiszámítható módon történő megadását, kiépítését és konfigurálását. Az infrastruktúra automatizálásának számos előnye van:

- Csökkenti az emberi hibák esélyét az infrastruktúra üzembe helyezése és kezelése során.
- Ugyanazt a sablont helyezi többször üzembe, ezzel azonos fejlesztési, tesztelési és éles környezetet hoz létre.
- Csökkenti a fejlesztési és tesztelési környezetek költségeit, mivel igény szerint hozza létre őket.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Az infrastruktúra változásainak megértése az alkalmazás előtt

Ahogy az erőforrás-topológia összetetté válik, az infrastruktúra-módosítások jelentésének és hatásának megértése bonyolult lehet.

A Terraform CLI lehetővé teszi a felhasználók számára az infrastruktúra-módosítások érvényesítését és előnézetét az alkalmazás előtt. Az infrastruktúra-módosítások biztonságos módon történő előzetes megtekintése számos előnnyel jár:
- A javasolt módosítások és azok hatásainak gyors megismerésével a csoporttagok közötti együttműködés hatékonyabb lehet.
- A nem szándékos módosítások a fejlesztési folyamat korai szakaszában észrevehetők

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Infrastruktúra üzembe helyezése több felhőbe

A Terraform ügyesen telepít egy infrastruktúrát több felhőszolgáltató között. Lehetővé teszi a fejlesztők számára, hogy egységes eszközök használatával kezeljék az egyes infrastruktúra-definíciókat.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a Terraformot és előnyeit, tekintse meg a javasolt következő lépéseket:

- Első lépésként a [telepítse a Terraformot, majd konfigurálja az Azure használatához](terraform-install-configure.md).
- [Hozzon létre egy Azure-beli virtuális gépet a Terraformmal](terraform-create-complete-vm.md).
- Fedezze fel a [Terraform Azure Resource Manager-modulját](https://www.terraform.io/docs/providers/azurerm/). 