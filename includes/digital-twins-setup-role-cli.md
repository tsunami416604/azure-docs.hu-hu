---
author: baanders
description: fájl belefoglalása a szerepkörre az Azure digitális Twins telepítőjének telepítéséhez
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407482"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Előfeltételek: engedélyezési követelmények

A cikkben szereplő összes lépés végrehajtásához az Azure-előfizetésében tulajdonosként kell besorolni. 

A következő parancs futtatásával megtekintheti a jogosultsági szintet Cloud Shellban:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Ha Ön a tulajdonosa, a `roleDefinitionName` kimenetben szereplő érték a *tulajdonos*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Cloud Shell ablak, amely az az role-hozzárendelési lista parancs kimenetét mutatja":::

Ha úgy találja, hogy az érték *közreműködő* vagy valamilyen más, mint a *tulajdonos*, a következő módokon léphet tovább:
* Lépjen kapcsolatba az előfizetés tulajdonosával, és kérje meg a tulajdonost, hogy hajtsa végre a cikkben szereplő lépéseket az Ön nevében.
* Vegye fel a kapcsolatot az előfizetés tulajdonosával vagy valakivel a felhasználói hozzáférés rendszergazdai szerepkörrel az előfizetésben, és kérje meg, hogy az előfizetés tulajdonosaként emelje a tulajdonost az előfizetésben, hogy jogosult legyen a folytatásra. Attól függően, hogy ez megfelelő-e, a szervezettől és a benne lévő szerepkörtől függ.