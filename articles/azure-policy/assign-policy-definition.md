---
title: "Szabályzat-hozzárendelés létrehozása Azure környezetben a nem megfelelő erőforrások azonosításához | Microsoft Docs"
description: "Ez a cikk részletesen ismerteti a lépéseket, amelyekkel létrehozhat egy szabályzatdefiníciót a nem megfelelő erőforrások azonosítására."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/10/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 4287b139f26d17e58f6caffbadb2c7da2a9b7b82
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Szabályzat-hozzárendelés létrehozása Azure környezetben a nem megfelelő erőforrások azonosításához
Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést.

A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából *nem megfelelőnek* minősülnek.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és hozzárendeli a *Felügyelt lemezek nélküli virtuális gépek naplózása* szabályzatdefiníciót.

1. Válassza ki a **Hozzárendelések** elemet az Azure Policy webhely bal oldali panelén.
2. Válassza a **Szabályzat hozzárendelése** lehetőséget a **Hozzárendelések** panel tetején.

   ![Szabályzatdefiníció hozzárendelése](media/assign-policy-definition/select-assign-policy.png)

3. A **Szabályzat hozzárendelése** oldalon kattintson a ![Szabályzatdefiníció gombra](media/assign-policy-definition/definitions-button.png) a **Szabályzat** mező mellett az elérhető definíciók listájának megnyitásához.

   ![Elérhető szabályzatdefiníciók megnyitása](media/assign-policy-definition/open-policy-definitions.png)

   Az Azure Policy beépített szabályzatdefiníciókat tartalmaz, amelyeket felhasználhat. Többek között a következő beépített szabályzat-definíciókat láthatja:

   - Címke és a hozzá tartozó érték kényszerítése
   - Címke és a hozzá tartozó érték alkalmazása
   - SQL Server 12.0-ás verziójának megkövetelése

    Az elérhető beépített szabályzatok teljes listájáért tekintse meg a [Szabályzatsablonok](json-samples.md) szakaszt.

4. Keresse meg a *Felügyelt lemezeket nem használó virtuális gépek naplózása* definíciót a szabályzatdefiníciók között. Kattintson a szabályzatra, majd kattintson a **Kiválasztás** elemre.

   ![A megfelelő szabályzatdefiníció megtalálása](media/assign-policy-definition/select-available-definition.png)

5. Írjon be egy a szabályzat-hozzárendeléshez tartozó megjelenítendő **Nevet**. Ebben az esetben a név legyen *Felügyelt lemezeket nem használó virtuális gépek naplózása*. Ha szeretné hozzáadhat egy **Leírást**. A leírás információt nyújt arról, hogyan azonosítja a szabályzat-hozzárendelés a felügyelt lemezeket nem használó virtuális gépeket.
6. Módosítsa a tarifacsomagot a **Standard** szintre, hogy a szabályzat kiterjedjen a meglévő erőforrásokra.

   Az Azure Policy-ban két tarifacsomag létezik – *Ingyenes* és *Standard*. Az Ingyenes szinten csak a jövőbeli erőforrások esetében kényszeríthet szabályzatokat, míg a Standard szinten már meglévő erőforrások esetében is, hogy jobban átlássa a megfelelőségi állapotot. A díjszabással kapcsolatos további információkért lásd: [Azure Policy – díjszabás](https://azure.microsoft.com/pricing/details/azure-policy/).

7. Válassza ki azt a **Hatókört**, amelyben a szabályzatot alkalmazni szeretné.  A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ez egyetlen előfizetéstől teljes erőforráscsoportokig terjedhet.
8. Válassza ki az előzőleg regisztrált előfizetést (vagy erőforráscsoportot). Ebben a példában az **Azure Analytics Capacity Dev** előfizetést használjuk, de az Ön lehetőségei ettől eltérőek lehetnek. Kattintson a **Kiválasztás** gombra.

   ![A megfelelő szabályzatdefiníció megtalálása](media/assign-policy-definition/assign-policy.png)

9. A **Kizárások** mezőt ezúttal hagyja üresen, majd kattintson a **Hozzárendelés** gombra.

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelési állapotának megismerése céljából.

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

Válassza a **Megfelelőség** elemet a bal oldali panelen, majd keresse meg az imént létrehozott szabályzat-hozzárendelést.

![Szabályzatmegfelelőség](media/assign-policy-definition/policy-compliance.png)

Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, azok a **Nem megfelelő erőforrások** lapon jelennek meg.

Ha a meglévő erőforrások kiértékelésekor egy feltétel igaznak bizonyul, ezek az erőforrások a szabályzatnak nem megfelelőként lesznek megjelölve. Az előző képen nem megfelelő erőforrások láthatóak. A következő táblázat azt mutatja be, hogyan működnek a különböző szabályzatműveletek a feltételek kiértékelésével a megfelelőségi állapot eléréséhez. A kiértékelési logika nem jelenik meg az Azure Portalon, a megfelelőségi állapotok eredményei azonban igen. A megfelelőségi állapotok eredménye lehet megfelelő vagy nem megfelelő.

|Erőforrás  |Ha egy feltétel a szabályzatban  |Művelet a szabályzatban   |Megfelelőségi állapot  |
|-----------|---------|---------|---------|
|Létezik     |True (Igaz)     |Megtagadás     |Nem megfelelő |
|Létezik     |False (Hamis)    |Megtagadás     |Megfelelő     |
|Létezik     |True (Igaz)     |Hozzáfűzés   |Nem megfelelő |
|Létezik     |False (Hamis)    |Hozzáfűzés   |Megfelelő     |
|Létezik     |True (Igaz)     |Naplózás    |Nem megfelelő |
|Létezik     |False (Hamis)    |Naplózás    |Nem megfelelő |

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.
1. Válassza ki a **Hozzárendelések** elemet a bal oldali panelen.
2. Keresse meg az Ön által létrehozott hozzárendelést, majd kattintson rá a jobb gombbal.

   ![Hozzárendelés törlése](media/assign-policy-definition/delete-assignment.png)

3.  Válassza ki a **Hozzárendelés törlése** elemet.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy hatókörhöz rendelt hozzá egy szabályzatdefiníciót. A szabályzatdefiníció biztosítja a hatókörbe tartozó erőforrások megfelelőségét, illetve azonosítja a nem megfelelő erőforrásokat.

A szabályzatok a **jövőben** létrehozott erőforrások megfelelőségének biztosítása érdekében történő hozzárendeléséről a következő oktatóanyagban találhat több információt:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./create-manage-policy.md)
