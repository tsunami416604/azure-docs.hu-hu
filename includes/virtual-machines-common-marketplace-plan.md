---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 09/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: f9974aae1e5996ffeaa6cde690a5e10ccba4cc32
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019012"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Rendszerkép üzembe helyezése a Marketplace használati feltételei

Egyes Virtuálisgép-rendszerképek az Azure Marketplace-en további licenccel rendelkezik, és beszerzési feltételeit, hogy el kell fogadnia programozott módon központi telepítésük előtt.  

Virtuális gép üzembe helyezése egy rendszerképből, meg kell fogadniuk a feltételeket a lemezkép, és engedélyezze a programozott telepítés. Csak akkor kell elvégeznie ezt egyszer az előfizetésében. Ezután minden alkalommal, amikor telepít egy virtuális Gépet programozott módon a rendszerképről, meg is kell adnia *megvásárlása* paramétereket.

A következő szakaszok show hogyan:

* Ismerje meg, hogy rendelkezik-e Piactéri lemezképet további licencfeltételek 
* Fogadja el a használati programozott módon
* Adja meg a beszerzési terv paramétereket, virtuális gépek programozott módon üzembe

