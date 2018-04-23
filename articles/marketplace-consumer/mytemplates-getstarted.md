---
title: Bevezetés a magánsablonok használatába | Microsoft Docs
description: Magánsablonok hozzáadása, kezelése és megosztása az Azure portál, az Azure parancssori felülete, illetve a PowerShell segítségével.
services: marketplace-customer
documentationcenter: ''
author: msmbaldwin
manager: asimm
editor: ''
tags: marketplace, azure-resource-manager
keywords: ''
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mbaldwin
ms.openlocfilehash: c716f54a1361d41dbad00e2e45562d5fbf8fd6ca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Bevezetés a magánsablonok használatába az Azure Portalon
Az [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) sablonjai olyan deklaratív sablonok, amelyek az üzemelő példány definiálására használatosak. Meghatározhatja az adott megoldáshoz üzembe helyezendő erőforrásokat, valamint megadhatja azokat a paramétereket és változókat, amelyek segítségével beviheti a különböző környezetekhez tartozó értékeket. A sablon JSON-okból és kifejezésekből áll, amelyek segítségével kialakíthatja az üzemelő példány értékeit.

Az [Azure Portal](https://portal.azure.com) új **Sablonok** funkciója, valamint a **Microsoft.Gallery** erőforrás-szolgáltató az [Azure Piactér](https://azure.microsoft.com/marketplace/) bővítményeként használható, amelynek segítségével a felhasználók saját könyvtárukból származó magánsablonokat hozhatnak létre, kezelhetnek és helyezhetnek üzembe.

> [!NOTE]
> A Microsoft a magánsablonok használata helyett azt javasolja, hogy hozzon létre egy szolgáltatáskatalógusban elérhető alkalmazást a [Felügyelt alkalmazások](../managed-applications/overview.md) segítségével. A szolgáltatáskatalógusban elérhető alkalmazást elérhetővé teheti a felhasználók számára a vállalatban.

Ebben a dokumentumban bemutatjuk, hogyan adhat hozzá, kezelhet és oszthat meg **magánsablonokat** az Azure Portalon.

## <a name="guidance"></a>Útmutatás
A következő javaslatok segítségével teljes mértékben kihasználhatja a **sablonok** előnyeit a megoldásaival való munkavégzés során:

* A **sablonok** beágyazott erőforrások, amelyek egy Resource Manager-sablont, illetve további metaadatokat tartalmaznak. Hasonlóan viselkednek, mint a Piactér elemei. A legfontosabb különbség, hogy a Piactér elemei nyilvánosak, míg ezek a sablonok privát felhasználásra szolgálnak.
* A **Sablonok** könyvtár hasznos segítséget nyújt a felhasználóknak üzemelő példányaik testre szabásában.
* A **sablonok** egyszerű Azure-beli tárházat biztosítanak a felhasználóknak.
* Kezdje egy meglévő Resource Manager-sablonnal. Keresse meg a kívánt sablont a [GitHubon](https://github.com/Azure/azure-quickstart-templates), vagy [exportálja a sablont](../azure-resource-manager/resource-manager-export-template.md) egy meglévő erőforráscsoportból.
* A **sablonok** ahhoz a felhasználóhoz kötődnek, aki közzéteszi őket. Az olvasási hozzáféréssel rendelkezők szabadon megtekinthetik a közzétevő nevét.
* A **sablonok** a Resource Managerhez tartozó erőforrások, amelyeket közzététel után nem lehet átnevezni.

## <a name="add-a-template-resource"></a>Sablonerőforrás hozzáadása
Az Azure Portalon két módszer áll rendelkezésre **sablonerőforrás** létrehozására.

### <a name="method-1-create-a-new-template-resource-from-a-running-resource-group"></a>1. módszer: Új sablonerőforrás létrehozása már futó erőforráscsoportból
1. Nyisson meg egy meglévő erőforráscsoportot az Azure Portalon. A **Beállítások** menüben válassza a **Sablon exportálása** lehetőséget.
2. A Resource Manager-sablon exportálását követően használja a **Sablon mentése** gombot az exportált elemnek a **Sablonok** tárházba mentéséhez. A Sablon exportálása funkcióról részletes leírást [itt](../azure-resource-manager/resource-manager-export-template.md) talál.
   <br /><br />
   ![Erőforráscsoport exportálása](media/rg-export-portal1.PNG)
3. Kattintson a **Save to Template** (Mentés sablonba) parancsgombra.
   <br /><br />
4. Adja meg a következő információkat:
   
   * Név – A sablonobjektum neve (MEGJEGYZÉS: ez a mező az Azure Resource Manageren alapul. Így minden vonatkozó elnevezési korlátozás érvényes rá, és létrehozását követően nem módosítható).
   * Leírás – A sablon rövid ismertetése.
     
     ![Sablon mentése](media/save-template-portal1.PNG)
5. Kattintson a **Save** (Mentés) gombra.
   
   > [!NOTE]
   > A portálon értesítések jelennek meg, ha a rendszer hibát talál az exportált Resource Manager-sablonban. A Resource Manager-sablon azonban ebben az esetben is menthető a Sablonok tárházba. Az exportált Resource Manager-sablon ismételt üzembe helyezése előtt ellenőrizze, és szükség esetén javítsa ki a Resource Manager-sablonnal kapcsolatos problémákat.
   > 
   > 

### <a name="method-2-add-a-new-template-resource-from-browse"></a>2. módszer: Új sablonerőforrás hozzáadása tallózással
Beállításokat még nem tartalmazó **sablont** is hozzáadhat. Ehhez kattintson a **Tallózás > Sablonok** menüpontban elérhető +Hozzáadás parancsgombra. Adja meg a nevet, a leírást és a Resource Manager-sablon JSON-ját.

![Sablon hozzáadása](media/add-template-portal1.PNG)

> [!NOTE]
> A Microsoft.Gallery egy bérlőalapú Azure-erőforrás-szolgáltató. A sablonerőforrás kötődik ahhoz a felhasználóhoz kötődik, aki létrehozta. Nem kötődik azonban előfizetéshez. A sablonok üzembe helyezésekor válasszon egy előfizetést.
> 
> 

## <a name="view-template-resources"></a>Sablonerőforrás megtekintése
A **Tallózás > Sablonok** menüben az összes elérhető **sablon** megtekinthető. Az elérhető sablonok között megjelennek az Ön által létrehozott sablonok, valamint az Önnel különböző szintű engedélyekkel megosztott sablonok egyaránt. További információért tekintse meg a [hozzáférés-vezérlés](#access-control-for-a-tenant-resource-provider) szakaszt.

![Sablon megtekintése](media/view-template-portal1.PNG)

A **sablon** részletes adatainak megtekintéséhez kattintson a lista kívánt elemére.

![Sablon megtekintése](media/view-template-portal2c.png)

## <a name="edit-a-template-resource"></a>Sablonerőforrás módosítása
A **sablon** szerkesztési folyamatának elindításához kattintson jobb gombbal a kívánt elemre a Tallózás listában, vagy válassza a Szerkesztés parancsgombot.

![Sablon szerkesztése](media/edit-template-portal1a.PNG)

Módosíthatja a leírást vagy a Resource Manager-sablon szövegét. A nevet nem változtathatja meg, mivel az a Resource Manager-erőforrás neve. Ha átírja a Resource Manager-sablon JSON-ját, a Microsoft ellenőrzi, hogy érvényes maradt-e a JSON. A módosított sablon mentéséhez kattintson az **OK**, majd a **Mentés** gombra.

![Sablon szerkesztése](media/edit-template-portal2a.PNG)

A sablon mentését követően megjelenik a megerősítési értesítés.

![Sablon szerkesztése](media/edit-template-portal3b.png)

## <a name="deploy-a-template-resource"></a>Sablonerőforrás üzembe helyezése
Bármely **sablon** üzembe helyezhető, amelyhez **olvasási** engedélyekkel rendelkezik. Az üzembe helyezés folytatásához adja meg a Resource Manager-sablon paramétereihez tartozó értékeket.

![Sablon üzembe helyezése](media/deploy-template-portal1b.png)

## <a name="share-a-template-resource"></a>Sablonerőforrás megosztása
A **sablonerőforrást** megoszthatja kollégáival. A megosztás hasonlóan működik, mint [a szerepkör-hozzárendelés az Azure-erőforrásoknál](../role-based-access-control/role-assignments-portal.md). A **sablon** tulajdonosa engedélyt ad a többi felhasználónak, akik így műveleteket végezhetnek a sablonerőforrással. Azok a személyek vagy csoportok, akikkel megosztja a **sablont**, jogosulttá válnak a Resource Manager-sablon és a hozzá tartozó katalógusbeli tulajdonságok megtekintésére.

### <a name="access-control-for-the-microsoftgallery-resources"></a>A Microsoft.Gallery erőforrások hozzáférés-vezérlése
| Szerepkör | Engedélyek |
| --- | --- |
| Tulajdonos |Lehetővé teszi a sablonerőforrás teljes körű ellenőrzését, ideértve a megosztást is |
| Olvasó |Lehetővé teszi a sablonerőforrás olvasását és futtatását (üzembe helyezését) |
| Közreműködő |Lehetővé teszi a sablonerőforrás szerkesztését és törlését. A felhasználó nem oszthatja meg másokkal a sablont |

Válassza a **Megosztás** lehetőséget a jobb gombbal az elemre kattintva vagy az adott elem megtekintésekor.

![Sablon megosztása](media/share-template-portal1a.png)

 A **sablonhoz** való hozzáférés biztosítása érdekében válasszon egy felhasználót vagy csoportot, majd adja meg a kívánt szerepkört. A következő szerepkörök választhatók: Tulajdonos, Olvasó és Közreműködő.

![Sablon megosztása](media/share-template-portal2b.png)

![Sablon megosztása](media/share-template-portal3b.png)

Kattintson a **Kijelölés**, majd az **OK** gombra. A megjelenő képernyőn láthatja az erőforráshoz adott felhasználókat és csoportokat.

![Sablon megosztása](media/share-template-portal4b.png)

> [!NOTE]
> A sablont kizárólag az azonos Azure Active Directory-bérlő alá tartozó felhasználókkal és csoportokkal oszthatja meg. Ha olyan e-mail-címmel rendelkező felhasználóval próbálja meg megosztani a sablont, aki nem az Ön bérlőjéhez tartozik, a rendszer meghívót küld a felhasználónak, amelyben felkéri, hogy csatlakozzon vendégként az Ön bérlőjéhez.
> 
> 

## <a name="next-steps"></a>További lépések
* A Resource Manager-sablonok létrehozásával kapcsolatos további információk: [Authoring templates](../azure-resource-manager/resource-group-authoring-templates.md) (Sablonok készítése)
* A Resource Manager-sablonokban használható függvények ismertetése: [Sablonfüggvények](../azure-resource-manager/resource-group-template-functions.md)

