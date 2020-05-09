---
title: Megosztott képgyűjtemények létrehozása az Azure CLI-vel
description: Ebből a cikkből megtudhatja, hogyan használhatja az Azure CLI-t egy virtuális gép megosztott rendszerképének létrehozásához az Azure-ban.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d64b5f94dae7ad0213fa231b5603064ad3647da1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796693"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Megosztott képgyűjtemény létrehozása az Azure CLI-vel

A [megosztott képgyűjtemény](./linux/shared-image-galleries.md) egyszerűbbé teszi a szervezeten belüli Egyéni rendszerképek megosztását. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A megosztott képkatalógus lehetővé teszi az egyéni virtuálisgép-rendszerképek megosztását másokkal. Válassza ki a megosztani kívánt képeket, mely régiókat szeretné elérhetővé tenni a alkalmazásban, és hogy kivel szeretné megosztani azokat. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>További lépések

Hozzon létre egy rendszerkép-verziót egy [virtuális](image-version-vm-cli.md)gépről vagy egy [felügyelt rendszerképből](image-version-managed-image-cli.md) az Azure CLI használatával.

A megosztott képtárakkal kapcsolatos további információkért tekintse meg az [áttekintést](./linux/shared-image-galleries.md). Ha problémákba ütközik, tekintse meg a [megosztott képtárak hibaelhárítása](troubleshooting-shared-images.md)című témakört.
