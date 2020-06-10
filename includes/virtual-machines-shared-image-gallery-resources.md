---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 433e909563602a2ef32b7986959b428c9afaf9f4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788993"
---
| Erőforrás | Leírás|
|----------|------------|
| **Rendszerkép forrása** | Ez egy olyan erőforrás, amellyel rendszerkép- **verziót** hozhat létre egy Képtárban. A rendszerkép forrása lehet egy meglévő Azure-beli virtuális gép, amely [általánosított vagy specializált](../articles/virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images), felügyelt képet, pillanatképet vagy rendszerkép-verziót használ egy másik rendszerkép-katalógusban. |
| **Képtár** | Az Azure Marketplace-hez hasonlóan a képkatalógus **is a lemezképek** kezeléséhez és megosztásához használható tárház, de Ön szabályozhatja, hogy ki férhet hozzá. |
| **Rendszerkép definíciója** | A képdefiníciók egy gyűjteményen belül jönnek létre, és a rendszerképekkel és a belső használat követelményeivel kapcsolatos információkat hordozzák. Ez magában foglalja azt is, hogy a rendszerkép Windows vagy Linux, kibocsátási megjegyzések, valamint minimális és maximális memória-követelmény. Ez egy adott típusú rendszerkép definíciója. |
| **Rendszerkép verziója** | A **rendszerkép verziója** az, amit a virtuális gép létrehozásához használ gyűjtemény létrehozásakor. A környezethez szükség lehet a rendszerkép több verziójára. A felügyelt rendszerképekhez hasonlóan, amikor **rendszerkép-verziót** használ egy virtuális gép létrehozásához, a rendszerkép verziója a virtuális gép új lemezének létrehozására szolgál. A rendszerkép verziója többször is használható. |