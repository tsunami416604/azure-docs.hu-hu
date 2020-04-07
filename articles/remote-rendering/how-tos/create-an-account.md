---
title: Azure távoli leképezési fiók létrehozása
description: Az Azure távoli rendereléshez való fiók létrehozásának lépéseit ismerteti
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681297"
---
# <a name="create-an-azure-remote-rendering-account"></a>Azure távoli leképezési fiók létrehozása

Ez a fejezet végigvezeti az **Azure távoli leképezési** szolgáltatás hozlétre egy fiókot lépéseket. A rövid útmutatók vagy oktatóanyagok kitöltéséhez kötelező érvényes fiók.

## <a name="create-an-account"></a>Fiók létrehozása

Az Azure távoli leadási szolgáltatás fiókjának létrehozásához a következő lépések szükségesek:

1. Ugrás a [Vegyes valóság előnézete lapra](https://aka.ms/MixedRealityPrivatePreview)
1. Kattintson az "Erőforrás létrehozása" gombra
1. A keresőmezőbe ("Keresés a piactéren") írja be a "Távoli renderelés" kifejezést, és nyomja meg az "Enter" szót.
1. Az eredménylistában kattintson a "Távoli renderelés" csempére
1. A következő képernyőn kattintson a "Létrehozás" gombra. Megnyílik egy űrlap, amely új távoli leképezési fiókot hoz létre:
    1. Az "Erőforrás név" beállítása a fiók nevére
    1. Szükség esetén frissítse az "Előfizetés"
    1. Az "Erőforráscsoport" beállítása egy ön által választott erőforráscsoportra
1. A fiók létrehozása után keresse meg azt, és:
    1. Az *Áttekintés* lapon jegyezze fel a "Fiókazonosító"
    1. A *Beállítások > hozzáférési kulcsok* lapon jegyezze fel az "Elsődleges kulcs" - ez a fiók titkos fiókkulcsa

### <a name="retrieve-the-account-information"></a>A fiókadatok lekérése

A minták és oktatóanyagok megkövetelik, hogy adja meg a fiók azonosítóját és egy kulcsot. Például a PowerShell mintparancsfájljaihoz használt **arrconfig.json** fájlban:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

Tekintse meg a *régió* kitöltéséhez [rendelkezésre álló régiók listáját.](../reference/regions.md)

A portál **`arrAccountId`** **`arrAccountKey`** értékei a következő lépésekben leírtak szerint:

* Nyissa meg az [Azure Portalt](https://www.portal.azure.com)
* Keresse meg a **"Távoli renderelési fiók"** - meg kell a **"Legutóbbi források"** listán. A keresés a felső keresősávban is kereshető. Ebben az esetben győződjön meg arról, hogy a használni kívánt előfizetés az Alapértelmezett előfizetésszűrőben van kiválasztva (szűrőikon a keresősáv mellett):

![Előfizetésszűrő](./media/azure-subscription-filter.png)

A fiókra kattintva megjelenik ez a képernyő, amely azonnal megjeleníti a **fiókazonosítót:**

![Azure-fiók azonosítója](./media/azure-account-id.png)

A billentyűhöz válassza a bal oldali panel **hozzáférési billentyűparancsait.** A következő oldalon egy elsődleges és egy másodlagos kulcs látható:

![Azure-hozzáférési kulcsok](./media/azure-account-primary-key.png)

Az érték **`arrAccountKey`** lehet elsődleges vagy másodlagos kulcs.

## <a name="link-storage-accounts"></a>Tárfiókok összekapcsolása

Ez a bekezdés bemutatja, hogyan kapcsolhatja össze a tárfiókokat a távoli leképezési fiókkal. Ha egy tárfiók van kapcsolva, nem szükséges, hogy hozzon létre egy SAS URI minden alkalommal, amikor szeretne együttműködni a fiókjában lévő adatokkal, például egy modell betöltésekor. Ehelyett a tárfiók neveit közvetlenül használhatja a [modell betöltése szakaszban leírtak szerint.](../concepts/models.md#loading-models)

Az ebben a bekezdésben szereplő lépéseket minden olyan tárfiókesetében el kell végezni, amelynek ezt az alternatív hozzáférési módszert kell használnia. Ha még nem hozott létre tárfiókokat, a modell [konvertálása](../quickstarts/convert-model.md#storage-account-creation)a gyorsútmutató megfelelő lépésén haladhat végig.

Most feltételezzük, hogy van egy tárfiók. Keresse meg a portálon lévő tárfiókot, és nyissa meg az adott tárfiók **hozzáférés-vezérlési (IAM)** lapját:

![IAM tárfiók](./media/azure-storage-account.png)

 Győződjön meg arról, hogy tulajdonosi engedélyekkel rendelkezik a tárfiókhoz, hogy győződjön meg arról, hogy szerepkör-hozzárendeléseket adhat hozzá. Ha nincs hozzáférése, a **Szerepkör-hozzárendelés hozzáadása** beállítás le lesz tiltva.

 A következő lépésekben leírtak szerint három különböző szerepkört kell hozzáadnia. Ha nem adja meg mindhárom hozzáférési szintet, a tárfiók elérése közben engedélyproblémák lépnek fel.

 Az első szerepkör hozzáadásához kattintson a "Szerepkör-hozzárendelés hozzáadása" csempén található **Hozzáadás** gombra:

![IAM tárfiók](./media/azure-add-role-assignment.png)

* Az első hozzárendelhető szerepkör a **Tulajdonos,** ahogy az a fenti képernyőképen látható. 
* Válassza a **Távoli renderelési fiók lehetőséget** a * Access**hozzárendelése legördülő menüből.**
* Válassza ki az előfizetést és a távoli leképezési fiókot az utolsó legördülő menüben.

Ismételje meg az új szerepkörök hozzáadását még kétszer a **szerepkör** legördülő menü megfelelő választásaihoz:
* **Tárfiók-közreműködő**
* **Storage Blob-adatközreműködő**

A többi legördülő menü az első lépésben is kiválasztásra kerül.

Ha mind a három szerepkört hozzáadta, az Azure remote rendering fiók a rendszer hez rendelt felügyelt szolgáltatásidentitások használatával rendelkezik hozzáféréssel a tárfiókhoz.

## <a name="next-steps"></a>További lépések

* [Hitelesítés](authentication.md)
* [Az Azure előtér-API-k használata a hitelesítéshez](frontend-apis.md)
* [Példa PowerShell-parancsfájlokra](../samples/powershell-example-scripts.md)
