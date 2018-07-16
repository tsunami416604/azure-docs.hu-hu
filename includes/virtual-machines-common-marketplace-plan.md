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
ms.openlocfilehash: a3c5290eb0179fe5842c495c2e08f22580d02bda
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38942431"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Rendszerkép üzembe helyezése a Marketplace használati feltételei

Egyes Virtuálisgép-rendszerképek az Azure Marketplace-en további licenccel rendelkezik, és beszerzési feltételeit, hogy el kell fogadnia programozott módon központi telepítésük előtt.  

Virtuális gép üzembe helyezése egy rendszerképből, meg kell fogadniuk a feltételeket a lemezkép, és engedélyezze a programozott telepítés. Csak akkor kell elvégeznie ezt egyszer az előfizetésében. Ezután minden alkalommal, amikor telepít egy virtuális Gépet programozott módon a rendszerképről, meg is kell adnia *megvásárlása* paramétereket.

A következő szakaszok show hogyan:

* Ismerje meg, hogy rendelkezik-e Piactéri lemezképet további licencfeltételek 
* Fogadja el a használati programozott módon
* Adja meg a beszerzési terv paramétereket, virtuális gépek programozott módon üzembe

