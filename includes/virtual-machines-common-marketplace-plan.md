---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a3c5290eb0179fe5842c495c2e08f22580d02bda
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Piactér feltételeket a lemezkép telepítése

Az Azure piactéren olyan VM képek további licenccel rendelkezik, és beszerzési feltételeket, hogy el kell fogadnia programozott módon központi telepítésük előtt.  

A virtuális gépek az ilyen lemezkép központi telepítéséhez kell elfogadja a feltételeket a lemezkép, és engedélyezze a programozott telepítés. Csak kell tennie, hogy ez az előfizetés tartozott. Ezt követően minden alkalommal, amikor egy virtuális gép programozott módon a lemezkép központi telepítése szükség meg *csomag megvásárlásától* paraméterek.

A következő szakaszok megjelenítése hogyan:

* Annak megállapítása, hogy rendelkezik-e a Piactéri rendszerkép további licencfeltételek 
* A feltételek elfogadásának programozott módon
* Adja meg a beszerzési terv paraméterek központi telepítésekor a virtuális gépek programozott módon

