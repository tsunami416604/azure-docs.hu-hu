---
title: Időponthoz való visszaállítás engedélyezése és kezelése a blokk Blobok számára (előzetes verzió)
titleSuffix: Azure Storage
description: Megtudhatja, hogyan állíthatja vissza egy korábbi állapotba az időponthoz tartozó visszaállítást (előzetes verzió).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 140e1203a29dcebec9d6483e73e906591b2213fb
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068497"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Időponthoz való visszaállítás engedélyezése és kezelése a blokk Blobok számára (előzetes verzió)

Az időponthoz tartozó visszaállítás (előzetes verzió) használatával visszaállíthatja a blokkos Blobok egy készletét egy korábbi állapotba. Ez a cikk azt ismerteti, hogyan engedélyezhető az időponthoz való visszaállítás egy Storage-fiókhoz a PowerShell használatával. Azt is bemutatja, hogyan hajtható végre visszaállítási művelet a PowerShell-lel.

További információt és az előzetes verzióra való regisztrációról a következő témakörben talál: [blokk-időpontra történő visszaállítás a Blobok számára (előzetes verzió)](point-in-time-restore-overview.md).

> [!CAUTION]
> Az időponthoz való visszaállítás támogatja a csak blokkos Blobok műveleteinek visszaállítását. A tárolók műveletei nem állíthatók vissza. Ha töröl egy tárolót a Storage-fiókból úgy, hogy meghívja a tároló [törlése](/rest/api/storageservices/delete-container) műveletet az időponthoz tartozó visszaállítási előzetes verzióban, a tároló nem állítható vissza visszaállítási művelettel. Az előzetes verzióban a tároló törlése helyett törölje az egyes blobokat, ha vissza szeretné állítani őket.

> [!IMPORTANT]
> Az időponthoz tartozó visszaállítás előzetes verziója csak nem éles használatra készült.

## <a name="enable-and-configure-point-in-time-restore"></a>Időponthoz tartozó visszaállítás engedélyezése és konfigurálása

Az időponthoz való visszaállítás engedélyezése és konfigurálása előtt engedélyezze a Storage-fiókra vonatkozó előfeltételeket: Soft DELETE, Change feed és blob Versioning. Az egyes funkciók engedélyezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Blobok helyreállítható törlésének engedélyezése](soft-delete-enable.md)
- [A módosítási csatorna engedélyezése és letiltása](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [BLOB-verziószámozás engedélyezése és kezelése](versioning-enable.md)

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A Azure Portal időponthoz való visszaállításának konfigurálásához kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. A **Beállítások**területen válassza **az adatvédelem**lehetőséget.
1. Jelölje be az **időponthoz való visszaállítás bekapcsolása** beállítást. Ha ezt a beállítást választja, a rendszer a Blobok, verziószámozás és módosítási csatorna törlését is engedélyezi.
1. Adja meg a maximális visszaállítási pontot az időponthoz tartozó visszaállításhoz napokban. Ennek a számnak legalább egy nappal kisebbnek kell lennie, mint a blob törléséhez megadott megőrzési időtartam.
1. Mentse a módosításokat.

Az alábbi képen egy olyan Storage-fiók látható, amely az időponthoz tartozó visszaállításhoz van konfigurálva, és egy hét napja visszaállítási ponttal rendelkezik, valamint egy megőrzési időszakot, amely miatt a blobos törlés 14 napig nem kötelező.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="A Azure Portal időponthoz való visszaállításának konfigurálását bemutató képernyőkép":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az időponthoz való visszaállítás PowerShell használatával történő konfigurálásához először telepítse az az. Storage Preview modul Version 1.14.1-Preview vagy az előzetes verziójú modul újabb verzióját. Távolítsa el az az. Storage modul bármely más verzióját.

Ellenőrizze, hogy telepítette-e a PowerShellGet 2.2.4.1 vagy újabb verzióját. A jelenleg telepített verzió meghatározásához futtassa a következő parancsot:

```powershell
Get-InstalledModule PowerShellGet
```

Ezután telepítse az az. Storage Preview-modult. A következő parancs telepíti a [2.5.2-](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview) es verziót – az az. Storage modul előzetes verzióját:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
```

A Azure PowerShell telepítésével kapcsolatos további információkért lásd: a [PowerShellGet telepítése](/powershell/scripting/gallery/installing-psget) és [a Azure PowerShell telepítése a PowerShellGet](/powershell/azure/install-az-ps)-mel.

Az Azure időponthoz való visszaállításának PowerShell-lel történő konfigurálásához hívja meg az Enable-AzStorageBlobRestorePolicy parancsot. A következő példa lehetővé teszi a Soft delete használatát, és beállítja a helyreállítható törlés megőrzési időtartamát, lehetővé teszi a hírcsatorna módosítását, majd engedélyezi az időponthoz való visszaállítást. A példa futtatása előtt a Azure Portal vagy egy Azure Resource Manager sablonnal is engedélyezheti a blob verziószámozását.

A példa futtatásakor ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

---

## <a name="perform-a-restore-operation"></a>Visszaállítási művelet végrehajtása

Ha visszaállítási műveletet hajt végre, a visszaállítási pontot UTC **datetime** értékként kell megadnia. A tárolók és a Blobok ezen a napon és időpontban lesznek visszaállítva az állapotukra. A visszaállítási művelet végrehajtása több percet is igénybe vehet.

A Storage-fiókban lévő összes tárolót visszaállíthatja, vagy egy vagy több tárolóban is visszaállíthatja a Blobok egy tartományát. A Blobok köre lexicographically van definiálva, vagyis a szótári sorrendben. Egy visszaállítási műveletben legfeljebb tíz lexicographical-tartomány támogatott. A tartomány kezdete magában foglalja a tartományt, a tartomány végét pedig kizárólagos.

A kezdési tartományhoz és a befejezési tartományhoz megadott tároló mintázatnak legalább három karaktert kell tartalmaznia. A tároló nevének a blob nevéből való elkülönítéséhez használt perjel (/) nem számít bele ebbe a minimumba.

Lexicographical-tartományokban nem használhatók helyettesítő karakterek. A helyettesítő karakterek szabványos karakterekként lesznek kezelve.

A és a tárolóban lévő Blobok visszaállításához `$root` `$web` explicit módon megadhatja őket egy visszaállítási műveletnek átadott tartományban. A `$root` és a `$web` tárolók csak akkor állíthatók vissza, ha explicit módon vannak megadva. Más rendszertárolók nem állíthatók vissza.

A rendszer csak a blokk blobokat állítja vissza. Az oldal blobok és a hozzáfűzési Blobok nem szerepelnek a visszaállítási műveletekben. További információ a hozzáfűző Blobokkal kapcsolatos korlátozásokról: [ismert problémák](#known-issues).

> [!IMPORTANT]
> Ha visszaállítási műveletet hajt végre, az Azure Storage blokkolja a művelet időtartamára visszaállított tartományokban lévő Blobok adatműveleteit. Az olvasási, írási és törlési műveletek blokkolva vannak az elsődleges helyen. Ezért az olyan műveletek, mint például a tárolók listázása a Azure Portalban előfordulhat, hogy nem a várt módon hajtják végre a visszaállítási műveletet.
>
> A másodlagos hely olvasási műveletei a visszaállítási művelet során folytatódnak, ha a Storage-fiók földrajzilag replikálódik.

### <a name="restore-all-containers-in-the-account"></a>A fiókban lévő összes tároló visszaállítása

A Storage-fiókban lévő összes tárolót visszaállíthatja úgy, hogy egy adott időpontban visszaadja azokat az előző állapotába.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha a Storage-fiókban lévő összes tárolót és blobot vissza szeretné állítani a Azure Portal, kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókhoz tartozó tárolók listájához.
1. Az eszköztáron válassza a **tárolók visszaállítása**, majd az **összes visszaállítása**lehetőséget.
1. A **minden tároló visszaállítása** panelen adja meg a visszaállítási pontot a dátum és idő megadásával.
1. A jelölőnégyzet bejelölésével erősítse meg, hogy folytatni kívánja a folytatást.
1. A visszaállítási művelet elindításához válassza a **visszaállítás** lehetőséget.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="A tárolók egy adott visszaállítási pontra történő visszaállítását bemutató képernyőkép":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha a Storage-fiókban lévő összes tárolót és blobot a PowerShell-lel szeretné visszaállítani, hívja meg a **Restore-AzStorageBlobRange** parancsot, és hagyja ki a `-BlobRestoreRange` paramétert. Az alábbi példa a Storage-fiókban lévő tárolókat a jelen pillanatban 12 órával visszaállítja a következő időpont előtt:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

---

### <a name="restore-ranges-of-block-blobs"></a>Blokk-Blobok tartományának visszaállítása

A Blobok egy vagy több lexicographical egy tárolóban vagy több tárolóban is visszaállíthatja, hogy a blobokat egy adott időpontban visszaadja az előző állapotába.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha egy vagy több tárolóban lévő Blobok egy tartományát szeretné visszaállítani a Azure Portal, kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókhoz tartozó tárolók listájához.
1. Válassza ki a visszaállítani kívánt tárolót vagy tárolókat.
1. Az eszköztáron válassza a **tárolók visszaállítása**, majd a **kijelöltek visszaállítása**lehetőséget.
1. A **kijelölt tárolók visszaállítása** panelen adja meg a visszaállítási pontot a dátum és idő megadásával.
1. Adja meg a visszaállítani kívánt tartományokat. A perjelek (/) használatával megszabhatja a tároló nevét a blob előtagja alapján.
1. Alapértelmezés szerint a **kijelölt tárolók visszaállítása** ablaktábla egy olyan tartományt határoz meg, amely a tárolóban lévő összes blobot tartalmazza. Ha nem szeretné visszaállítani a teljes tárolót, törölje ezt a tartományt. Az alapértelmezett tartomány az alábbi képen látható.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Képernyőfelvétel a törlendő alapértelmezett blob-tartományról az egyéni tartomány megadását megelőzően":::

1. A jelölőnégyzet bejelölésével erősítse meg, hogy folytatni kívánja a folytatást.
1. A visszaállítási művelet elindításához válassza a **visszaállítás** lehetőséget.

Az alábbi képen egy tartományon futó visszaállítási művelet látható.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="A Blobok tartományának egy vagy több tárolóban való visszaállítását bemutató képernyőfelvétel":::

A rendszerképben látható visszaállítási művelet a következő műveleteket hajtja végre:

- Visszaállítja a *container1*teljes tartalmát.
- Visszaállítja a blobokat a lexicographical tartomány *blob1* a *container2* *blob5* keresztül. Ez a tartomány a blobokat olyan nevekkel állítja vissza, mint például a *blob1*, a *blob11*, a *blob100*, a *blob2*és így tovább. Mivel a tartomány vége kizárólagos, visszaállítja azokat a blobokat, amelyek nevei a *blob4*-vel kezdődnek, de nem állítja vissza azokat a blobokat, amelyek nevei a *blob5*-vel kezdődnek.
- Visszaállítja az összes blobot a *container3* és a *container4*. Mivel a tartomány vége kizárólagos, ez a tartomány nem állítja vissza a *container5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A Blobok egyetlen tartományának visszaállításához hívja meg a **Restore-AzStorageBlobRange** parancsot, és adja meg a paraméterhez tartozó lexicographical és blob-nevek tartományát `-BlobRestoreRange` . Például egy *minta-tároló*nevű tárolóban lévő Blobok visszaállításához megadhat egy olyan tartományt, amely *minta-tárolóval* kezdődik, és a *minta-container1*végződik. A kezdő és a záró tartományokban megnevezett tárolók nem kötelezőek. Mivel a tartomány vége kizárólagos, még akkor is, ha a Storage-fiók tartalmaz egy *minta-container1*nevű tárolót, a rendszer csak a *minta-tároló* nevű tárolót állítja vissza:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container `
    -EndRange sample-container1
```

Ha egy tárolóban lévő Blobok részhalmazát kívánja visszaállítani, egy perjel (/) használatával válassza el a tároló nevét a blob előtag-mintából. A következő tartomány például kijelöli a blobokat egyetlen tárolóban, amelynek neve a *d* – *f*betűvel kezdődik:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d `
    -EndRange sample-container/g
```

Ezután adja meg a tartományt a **Restore-AzStorageBlobRange** parancs számára. Adja meg a visszaállítási pontot a paraméter UTC **datetime** értékének megadásával `-TimeToRestore` . Az alábbi példa a megadott tartományba tartozó blobokat a jelen pillanat előtti 3 nappal visszaállítja.

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

A blokkos Blobok több tartományának visszaállításához adja meg a paraméter tartományának tömbjét `-BlobRestoreRange` . Az alábbi példa két tartományt határoz meg a *container1* és a *container4*teljes tartalmának visszaállításához:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

---

### <a name="restore-block-blobs-asynchronously-with-powershell"></a>A Blobok aszinkron visszaállítása a PowerShell-lel

Ha aszinkron módon szeretné futtatni a visszaállítási műveletet, adja hozzá a `-AsJob` paramétert a **Restore-AzStorageBlobRange** hívásához, és tárolja a hívás eredményét egy változóban. A **Restore-AzStorageBlobRange** parancs **AzureLongRunningJob**típusú objektumot ad vissza. Az objektum State ( **állapot** ) tulajdonságát megtekintve megállapíthatja, hogy a visszaállítási művelet befejeződött-e. Lehet, hogy az **állapot** tulajdonság értéke **fut** vagy **befejeződött**.

Az alábbi példa bemutatja, hogyan hívhat meg aszinkron módon a visszaállítási műveletet:

```powershell
$job = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-5) `
    -AsJob

# Check the state of the job.
$job.State
```

Ha a Futtatás után szeretné megvárni a visszaállítási művelet befejezését, hívja meg a [WAIT-Job](/powershell/module/microsoft.powershell.core/wait-job) parancsot az alábbi példában látható módon:

```powershell
$job | Wait-Job
```

## <a name="known-issues"></a>Ismert problémák

A Restore művelet sikertelen lesz, ha olyan visszaállítási műveletek egy részhalmaza van, ahol hozzáfűzési Blobok találhatók. A Microsoft azt javasolja, hogy az előzetes verzióban ne végezzen időponthoz való visszaállítást, ha a fiókban hozzáfűzési Blobok vannak.

## <a name="next-steps"></a>Következő lépések

- [Időponthoz való visszaállítás a blokk Blobok számára (előzetes verzió)](point-in-time-restore-overview.md)
- [Helyreállítható törlés](soft-delete-overview.md)
- [Hírcsatorna módosítása (előzetes verzió)](storage-blob-change-feed.md)
- [BLOB verziószámozása](versioning-overview.md)
