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
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71174958"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Lemezkép üzembe helyezése piactéri kifejezésekkel

Az Azure Piactéren egyes virtuálisgép-lemezképek további licenc- és vásárlási feltételekkel rendelkeznek, amelyeket el kell fogadnia, mielőtt programozott módon üzembe helyezheti őket.  

Egy virtuális gép üzembe helyezéséhez egy ilyen rendszerkép, akkor el kell fogadnia a lemezkép feltételeit, és lehetővé teszi a programozott központi telepítés. Ezt előfizetésenként csak egyszer kell megtennie. Ezt követően minden alkalommal, amikor egy virtuális gép telepítése programozott módon a rendszerképről is meg kell adnia *a vásárlási terv* paramétereit.

A következő szakaszok bemutatják, hogyan:

* Annak megállapítása, hogy egy Marketplace-lemezkép rendelkezik-e további licencfeltételekkel 
* A feltételek programozott elfogadása
* Beszerzési terv paramétereinek megadása virtuális gép programozott telepítésekor

