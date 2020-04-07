---
title: Tervrajzok importálása és exportálása a PowerShell használatával
description: Ismerje meg, hogyan dolgozhat a tervezetdefiníciók kódként. Megoszthatja, megszerezheti és kezelheti őket az exportálási és importálási parancsokkal.
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: 98bd21aad944346a17d8bdce7fb74c0eb8be2ed7
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677136"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Tervezetdefiníciók importálása és exportálása a PowerShell használatával

Az Azure Blueprints teljes körűen kezelhető az Azure Portalon keresztül. Ahogy a szervezetek előre az Azure Blueprints használata, el kell kezdeniük gondolkodni tervezetdefiníciók felügyelt kódként. Ezt a fogalmat gyakran nevezik infrastruktúra kódnak (IaC). A tervezetdefiníciók kódként való kezelése további előnyöket kínál az Azure Portal által kínált lehetőségeken túl. Ezek az előnyök a következők:

- Tervdefiníciók megosztása
- A tervezetdefiníciók biztonsági mentése
- Tervdefiníciók újrafelhasználása különböző bérlőkben vagy előfizetésekben
- A tervezetdefiníciók elhelyezése a forrásvezérlőben
  - A tervezetdefiníciók automatizált tesztelése tesztkörnyezetekben
  - Folyamatos integrációs és folyamatos üzembe helyezési (CI/CD) csővezetékek támogatása

Bármi legyen is az oka, kezelése a tervezet definíciók kódként előnyökkel jár. Ez a cikk bemutatja, hogyan kell használni a `Import-AzBlueprintWithArtifact` és `Export-AzBlueprintWithArtifact` a parancsokat az [Az.Blueprint](https://powershellgallery.com/packages/Az.Blueprint/) modulban.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk az Azure Blueprints mérsékelt munkaismeretet feltételezi. Ha még nem tette meg, a következő cikkeket kell átdolgoznia:

- [Tervrajz létrehozása a portálon](../create-blueprint-portal.md)
- További információ a [telepítési szakaszokról](../concepts/deployment-stages.md) és [a terv életciklusáról](../concepts/lifecycle.md)
- [Tervdefiníciók](../create-blueprint-powershell.md) és hozzárendelések létrehozása és [kezelése](./manage-assignments-ps.md) a PowerShell használatával

Ha még nincs telepítve, kövesse [az Az.Blueprint modul hozzáadása az Az.Blueprint modul](./manage-assignments-ps.md#add-the-azblueprint-module) telepítéséhez és érvényesítéséhez az **Az.Blueprint** modult a PowerShell-galériából.

## <a name="folder-structure-of-a-blueprint-definition"></a>Tervrajzdefiníció mappaszerkezete

Mielőtt megvizsgálna a tervrajzok exportálását és importálását, nézzük meg, hogyan épülnek fel a tervmeghatározást kialakító fájlok. A tervezetdefiníciót a saját mappájában kell tárolni.

> [!IMPORTANT]
> Ha a rendszer nem **Name** ad `Import-AzBlueprintWithArtifact` értéket a parancsmag Név paraméterének, a rendszer annak a mappának a nevét használja, amelyben a tervezetdefiníció tárolódik.

A tervezet definíciójával együtt, `blueprint.json`amelyet meg kell nevezni, azok a műtárgyak, amelyekből a tervezet definíciója áll. Minden műnek a . `artifacts`
Összerakni, a szerkezet a tervezet meghatározása JSON fájlok mappákban kell kinéznie a következő:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>A tervezetdefiníció exportálása

A tervezetdefiníció exportálásának lépései egyszerűek. A tervezetdefiníció exportálása hasznos lehet a megosztáshoz, a biztonsági mentéshez vagy a forrásellenőrzésbe való behelyezéshez.

- **Tervrajz** [kötelező]
  - Megadja a tervezet definícióját
  - A `Get-AzBlueprint` referenciaobjektum lefoglalásának használata
- **OutputPath** [kötelező]
  - Megadja a json-definíciós json-fájlok mentési útvonalát
  - A kimeneti fájlok egy almappában vannak, amelynek neve a tervezet definíciója
- **Verzió** (nem kötelező)
  - Megadja a kimeneti verziót, ha a **Blueprint** referenciaobjektum egynél több verzióra mutató hivatkozásokat tartalmaz.

1. Hivatkozás a tervezet definíciójára, amelyet a `{subId}`következőként ábrázolt előfizetésből exportál:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. A `Export-AzBlueprintWithArtifact` parancsmag segítségével exportálhatja a megadott tervezetdefiníciót:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>A tervezetdefiníció importálása

Miután egy [exportált tervezet definíciója,](#export-your-blueprint-definition) vagy egy manuálisan létrehozott tervezet definíciója a [szükséges mappastruktúra,](#folder-structure-of-a-blueprint-definition)importálhatja, hogy a tervezet definícióját egy másik felügyeleti csoport vagy előfizetés.

A beépített tervezetdefiníciók példáit az [Azure Blueprint GitHub-tárházban talál.](https://github.com/Azure/azure-blueprints/tree/master/samples/builtins)

- **Név** [kötelező]
  - Megadja az új tervezetdefiníció nevét.
- **InputPath** [kötelező]
  - Megadja azt az elérési utat, amelyből a tervezetdefiníció t
  - Meg kell egyeznie a [szükséges mappastruktúrával](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (nem kötelező)
  - A felügyeleti csoport azonosítója a tervezetdefiníció mentéséhez, ha nem az aktuális környezet alapértelmezett
  - Meg kell adni **a ManagementGroupId** vagy **az SubscriptionId** azonosítót.
- **SubscriptionId** (nem kötelező)
  - A tervezetdefiníció mentéséhez szükséges előfizetésazonosító, ha nem az aktuális környezet alapértelmezett
  - Meg kell adni **a ManagementGroupId** vagy **az SubscriptionId** azonosítót.

1. A `Import-AzBlueprintWithArtifact` parancsmag segítségével importálhatja a megadott tervezetdefiníciót:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

A tervezetdefiníció importálása után [rendelje hozzá a PowerShellhez.](./manage-assignments-ps.md#create-blueprint-assignments)

A speciális tervezetdefiníciók létrehozásáról az alábbi cikkekben talál további információt:

- Használjon [statikus és dinamikus paramétereket](../concepts/parameters.md).
- A [tervezet szekvenálási sorrendjének](../concepts/sequencing-order.md)testreszabása.
- A központi telepítések védelme [a tervezet erőforrás-zárolásával.](../concepts/resource-locking.md)
- [Tervrajzok kezelése kódként](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>További lépések

- További információ a [tervterv életciklusáról.](../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../concepts/resource-locking.md)
- Az [általános hibaelhárítással](../troubleshoot/general.md)kapcsolatos tervtervezet ekhozzárendelése során fellépő problémák megoldása.