---
title: Időponthoz való visszaállítás engedélyezése és kezelése a blokk Blobok számára (előzetes verzió)
titleSuffix: Azure Storage
description: Megtudhatja, hogyan állíthatja vissza az időponthoz tartozó visszaállítást (előzetes verzió) a blokkos Blobok egy korábbi időpontban állapotba való visszaállításához.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6948d4d786e918e5f3e32e6bdf2f7e23940f6815
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445440"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Időponthoz való visszaállítás engedélyezése és kezelése a blokk Blobok számára (előzetes verzió)

Az időponthoz tartozó visszaállítás (előzetes verzió) használatával visszaállíthatja a blokk blobokat az állapotukra egy korábbi időpontban. Ez a cikk azt ismerteti, hogyan engedélyezhető az időponthoz való visszaállítás egy Storage-fiókhoz a PowerShell használatával. Azt is bemutatja, hogyan hajtható végre visszaállítási művelet a PowerShell-lel.

További információt és az előzetes verzióra való regisztrációról a következő témakörben talál: [blokk-időpontra történő visszaállítás a Blobok számára (előzetes verzió)](point-in-time-restore-overview.md).

> [!CAUTION]
> Az időponthoz való visszaállítás támogatja a csak blokkos Blobok műveleteinek visszaállítását. A tárolók műveletei nem állíthatók vissza. Ha töröl egy tárolót a Storage-fiókból úgy, hogy meghívja a tároló [törlése](/rest/api/storageservices/delete-container) műveletet az időponthoz tartozó visszaállítási előzetes verzióban, a tároló nem állítható vissza visszaállítási művelettel. Az előzetes verzióban a tároló törlése helyett törölje az egyes blobokat, ha vissza szeretné állítani őket.

> [!IMPORTANT]
> Az időponthoz tartozó visszaállítás előzetes verziója csak nem éles használatra készült. Az üzemi szolgáltatási szintű szerződések (SLA-kat) jelenleg nem érhetők el.

## <a name="install-the-preview-module"></a>Az előzetes verziójú modul telepítése

Az Azure időponthoz való visszaállításának PowerShell használatával történő konfigurálásához először telepítse az az. Storage Preview modul verzióját 1.14.1-Preview vagy újabb verzióra. A legújabb előzetes verzió használata ajánlott, de az időponthoz tartozó visszaállítás a 1.14.1-Preview és újabb verziókban is támogatott. Távolítsa el az az. Storage modul bármely más verzióját.

A következő parancs az az. Storage [2.0.1 – Preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) modult telepíti:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.0.1-preview -AllowPrerelease
```

A fenti parancshoz telepíteni kell a PowerShellGet 2.2.4.1 vagy újabb verzióját. A jelenleg betöltött verzió meghatározása:
```powershell
Get-Module PowerShellGet
```
További információ a Azure PowerShell telepítéséről: [Azure PowerShell telepítése a PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Időponthoz tartozó visszaállítás engedélyezése és konfigurálása

Az időponthoz való visszaállítás engedélyezése és konfigurálása előtt engedélyezze a Storage-fiókra vonatkozó előfeltételeket: Soft DELETE, Change feed és blob Versioning. Az egyes funkciók engedélyezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [A Blobok helyreállítható törlésének engedélyezése](soft-delete-enable.md)
- [A módosítási csatorna engedélyezése és letiltása](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [BLOB-verziószámozás engedélyezése és kezelése](versioning-enable.md)

Az Azure időponthoz való visszaállításának PowerShell-lel történő konfigurálásához hívja meg az Enable-AzStorageBlobRestorePolicy parancsot. A következő példa lehetővé teszi a Soft delete használatát, és beállítja a helyreállítható törlés megőrzési időtartamát, lehetővé teszi a hírcsatorna módosítását, majd engedélyezi az időponthoz való visszaállítást. A példa futtatása előtt a Azure Portal vagy egy Azure Resource Manager sablonnal is engedélyezheti a blob verziószámozását.

A példa futtatásakor ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>Visszaállítási művelet végrehajtása

A visszaállítási művelet elindításához hívja meg a **Restore-AzStorageBlobRange** parancsot, és adja meg a visszaállítási pontot UTC **datetime** értékként. Megadhatja az lexicographical-tartományokat, amelyekkel visszaállíthatja a blobokat, vagy kihagyhat egy tartományt a Storage-fiók összes tárolójában lévő összes blob visszaállításához. Egy visszaállítási műveletben legfeljebb 10 lexicographical-tartomány támogatott. Az oldal blobok és a hozzáfűzési Blobok nem szerepelnek a visszaállításban. A visszaállítási művelet végrehajtása több percet is igénybe vehet.

A visszaállítandó Blobok tartományának megadásakor vegye figyelembe a következő szabályokat:

- A kezdési tartományhoz és a befejezési tartományhoz megadott tároló mintázatnak legalább három karaktert kell tartalmaznia. A tároló nevének a blob nevéből való elkülönítéséhez használt perjel (/) nem számít bele ebbe a minimumba.
- Egy visszaállítási művelet legfeljebb 10 tartományt adhat meg.
- A helyettesítő karakterek használata nem támogatott. Ezeket standard karakterként kezeli a rendszer.
- A és a tárolóban lévő Blobok visszaállításához `$root` `$web` explicit módon megadhatja őket egy visszaállítási műveletnek átadott tartományban. A `$root` és a `$web` tárolók csak akkor állíthatók vissza, ha explicit módon vannak megadva. Más rendszertárolók nem állíthatók vissza.

> [!IMPORTANT]
> Ha visszaállítási műveletet hajt végre, az Azure Storage blokkolja a művelet időtartamára visszaállított tartományokban lévő Blobok adatműveleteit. Az olvasási, írási és törlési műveletek blokkolva vannak az elsődleges helyen. Ezért az olyan műveletek, mint például a tárolók listázása a Azure Portalban előfordulhat, hogy nem a várt módon hajtják végre a visszaállítási műveletet.
>
> A másodlagos hely olvasási műveletei a visszaállítási művelet során folytatódnak, ha a Storage-fiók földrajzilag replikálódik.

### <a name="restore-all-containers-in-the-account"></a>A fiókban lévő összes tároló visszaállítása

Ha az összes tárolót és blobot vissza szeretné állítani a Storage-fiókban, hívja meg a **Restore-AzStorageBlobRange** parancsot, és hagyja ki a `-BlobRestoreRange` paramétert. Az alábbi példa a Storage-fiókban lévő tárolókat a jelen pillanatban 12 órával visszaállítja a következő időpont előtt:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Blokkos Blobok egyetlen tartományának visszaállítása

Blobok egy tartományának visszaállításához hívja meg a **Restore-AzStorageBlobRange** parancsot, és adja meg a paraméterhez a lexicographical és a Blobok nevét `-BlobRestoreRange` . A tartomány kezdete a befogadóban van, a tartomány vége pedig kizárólagos.

Például egy *minta-tároló*nevű tárolóban lévő Blobok visszaállításához megadhat egy olyan tartományt, amely *minta-tárolóval* kezdődik, és a *minta-container1*végződik. A kezdő és a záró tartományokban megnevezett tárolók nem kötelezőek. Mivel a tartomány vége kizárólagos, még akkor is, ha a Storage-fiók tartalmaz egy *minta-container1*nevű tárolót, a rendszer csak a *minta-tároló* nevű tárolót állítja vissza:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Ha meg szeretné adni a Blobok egy részhalmazát egy tárolóban a visszaállításhoz, egy perjel (/) használatával válassza el a tároló nevét a blob mintából. A következő tartomány például kijelöli a blobokat egyetlen tárolóban, amelynek neve a *d* – *f*betűvel kezdődik:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Ezután adja meg a tartományt a **Restore-AzStorageBlobRange** parancs számára. Adja meg a visszaállítási pontot a paraméter UTC **datetime** értékének megadásával `-TimeToRestore` . Az alábbi példa a megadott tartományba tartozó blobokat a jelen pillanat előtti 3 nappal visszaállítja.

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Blokkos Blobok több tartományának visszaállítása

A blokkos Blobok több tartományának visszaállításához adja meg a paraméter tartományának tömbjét `-BlobRestoreRange` . Egy visszaállítási művelet legfeljebb 10 tartományt támogat. Az alábbi példa két tartományt határoz meg a *container1* és a *container4*teljes tartalmának visszaállításához:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

### <a name="restore-block-blobs-asynchronously"></a>A blokk Blobok aszinkron visszaállítása

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
- A restores egy részhalmaza, ahol a hozzáfűző Blobok jelennek meg, a visszaállítás sikertelen lesz. Egyelőre ne végezzen visszaállítást, ha a fiókban található hozzáfűzési Blobok szerepelnek.

## <a name="next-steps"></a>További lépések

- [Időponthoz való visszaállítás a blokk Blobok számára (előzetes verzió)](point-in-time-restore-overview.md)
- [Helyreállítható törlés](soft-delete-overview.md)
- [Hírcsatorna módosítása (előzetes verzió)](storage-blob-change-feed.md)
- [BLOB verziószámozása (előzetes verzió)](versioning-overview.md)
