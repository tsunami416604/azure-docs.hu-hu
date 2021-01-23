---
title: Portál sablon konvertálása a sablonra spec
description: Ismerteti, hogyan alakíthat át egy meglévő sablont a Azure Portal-katalógusban egy sablon specifikációba.
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 8fe02f55348f2cdcabb43e05bb547819d4b51228
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739107"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>A sablon katalógusának átalakítása a portálon a sablon specifikációinak megfelelően

A Azure Portal a fiókjában Azure Resource Manager sablonok (ARM-sablonok) tárolására szolgál. **A szolgáltatás elavult.** Ha továbbra is szeretné használni a katalógusban található sablonokat, alakítsa át őket [sablon típusú specifikációba](template-specs.md).

Ez a cikk bemutatja, hogyan alakíthatja át a meglévő sablonokat a sablon-katalógusban a specifikációk alapján.

A portálon az elavult szolgáltatás neve **sablonok (előzetes verzió)**. Ha szeretné megtekinteni, hogy rendelkezik-e konvertálásra alkalmas sablonokkal, tekintse meg a [portálon a sablon galériáját](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems). Ezek a sablonok rendelkeznek az erőforrás típusával `Microsoft.Gallery/myareas/galleryitems` .

## <a name="deprecation-of-portal-feature"></a>A portál funkciójának elavulása

A portálon található sablon-katalógus a 2021-es január 21-én elavult. Továbbra is használhatja azt február 21-én. Február 22-én nem hozhat létre új sablonokat a portálon, de továbbra is megtekintheti és telepítheti a meglévő sablonokat.

Június 22-én a szolgáltatás el lesz távolítva a portálról, és minden API-művelet le lesz tiltva. A katalógusból nem tekintheti meg és nem helyezheti üzembe a sablonokat.

Június 22. előtt minden olyan sablont át kell telepíteni, amelyet továbbra is használni kíván. A sablonok áttelepíthetők az ebben a cikkben látható módszerek egyikével is. A szolgáltatás eltávolítását követően meg kell nyitnia egy támogatási esetet az áttelepített sablonok beszerzéséhez.

## <a name="convert-with-powershell-script"></a>Konvertálás PowerShell-parancsfájllal

A sablonok katalógusban való átalakításának egyszerűbbé tételéhez használjon PowerShell-parancsfájlt az Azure Gyorsindítás sablonok tárházában. A parancsfájl futtatásakor hozzon létre egy új sablont az egyes sablonokhoz, vagy töltsön le egy sablont, amely létrehozza a specifikációt. A parancsfájl nem törli a sablont a sablon-gyűjteményből.

1. Másolja az [áttelepítési parancsfájlt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1). Mentse a *Migrate-GalleryItems.ps1* nevű helyi másolatot.
1. Az új sablon specifikációinak létrehozásához adja meg a `-ResourceGroupName` és a paraméterek értékeit `-Location` . 

   Állítsa be a- `ItemsToExport` t a `MyGalleryItems` sablon exportálásához. Állítsa be az értékre az `AllGalleryItems` összes olyan sablon exportálásához, amelyhez hozzáférése van.

   A következő példa új sablon-specifikációkat hoz létre egy **migratedRG** nevű erőforráscsoport egyes sablonjaihoz. A parancsfájl létrehozza az erőforráscsoportot, ha az nem létezik.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. A sablonhoz tartozó specifikációk létrehozásához használható sablonok letöltéséhez ne adjon meg értékeket az erőforráscsoport vagy a hely számára. Ehelyett a értéket kell megadnia `-ExportToFile` . A sablon nem egyezik meg a katalógusban szereplő sablonnal. Ehelyett egy olyan sablon specifikációját tartalmazza, amely létrehozza a sablon specifikációját.

   A következő példa a sablonokat a sablon specifikációinak létrehozása nélkül tölti le.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   A sablon specifikációját létrehozó sablon üzembe helyezéséről a rövid útmutató [: sablon létrehozása és üzembe helyezése spec (előzetes verzió)](quickstart-create-template-specs.md)című cikkből tájékozódhat.

További információ a parancsfájlról és a hozzá tartozó paraméterekről: [create TemplateSpecs to template Gallery templates](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create).

## <a name="manually-convert-through-portal"></a>Manuális átalakítás a portálon keresztül

Manuálisan is másolhat sablonokat a katalógusból egy új sablonra vonatkozó specifikációba.

1. Nyissa meg a [sablonokat (előzetes verzió)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) a portálon.
1. Válassza ki az áttelepíteni kívánt sablont.
1. Válassza a **sablon megtekintése** lehetőséget.
1. Másolja a sablon tartalmát.
1. A portálon keresse meg a **sablonra vonatkozó specifikációkat**. Válassza ki ezt a beállítást.
1. Válassza a **sablon létrehozása spec** elemet.
1. Adja meg a név, az előfizetés, az erőforráscsoport, a hely és a verzió értékeit.
1. Válassza a **Tovább: sablon szerkesztése** lehetőséget.
1. A sablon tartalmához illessze be a vágólapra másolt sablont a sablon galériából.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Az érvényesítés sikeres befejeződése után válassza a **Létrehozás** lehetőséget.

Ha meg kell osztania a sablon specifikációját a szervezet más felhasználóival, [állítsa be a szerepköralapú hozzáférés-vezérlést](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) a csoport vagy a felhasználók számára, akiknek hozzáférésre van szükségük.

## <a name="next-steps"></a>További lépések

A sablon specifikációinak megismeréséhez lásd: [sablon létrehozása és telepítése specifikációk](template-specs.md).
