---
title: Időponthoz való visszaállítás engedélyezése és kezelése a blokk Blobok számára (előzetes verzió)
titleSuffix: Azure Storage
description: Megtudhatja, hogyan állíthatja vissza az időponthoz tartozó visszaállítást (előzetes verzió) a blokkos Blobok egy korábbi időpontban állapotba való visszaállításához.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: fe98e04c37172dc6b91c86fab8200022ed860d4f
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170103"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Időponthoz való visszaállítás engedélyezése és kezelése a blokk Blobok számára (előzetes verzió)

Az időponthoz tartozó visszaállítás (előzetes verzió) használatával visszaállíthatja a blokk blobokat az állapotukra egy korábbi időpontban. Ez a cikk azt ismerteti, hogyan engedélyezhető az időponthoz való visszaállítás egy Storage-fiókhoz a PowerShell használatával. Azt is bemutatja, hogyan hajtható végre visszaállítási művelet a PowerShell-lel.

További információt és az előzetes verzióra való regisztrációról a következő témakörben talál: [blokk-időpontra történő visszaállítás a Blobok számára (előzetes verzió)](point-in-time-restore-overview.md).

> [!CAUTION]
> Az időponthoz való visszaállítás támogatja a csak blokkos Blobok műveleteinek visszaállítását. A tárolók műveletei nem állíthatók vissza. Ha töröl egy tárolót a Storage-fiókból úgy, hogy meghívja a tároló [törlése](/rest/api/storageservices/delete-container) műveletet az időponthoz tartozó visszaállítási előzetes verzióban, a tároló nem állítható vissza visszaállítási művelettel. Az előzetes verzióban a tároló törlése helyett törölje az egyes blobokat, ha vissza szeretné állítani őket.

> [!IMPORTANT]
> Az időponthoz tartozó visszaállítás előzetes verziója csak nem éles használatra készült. Az üzemi szolgáltatási szintű szerződések (SLA-kat) jelenleg nem érhetők el.

## <a name="install-the-preview-module"></a>Az előzetes verziójú modul telepítése

Az Azure időponthoz való visszaállításának a PowerShell-lel való konfigurálásához először telepítse az az. Storage PowerShell [-modul 1.14.1-](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) verziójának előzetes verzióját. Az előzetes verziójú modul telepítéséhez kövesse az alábbi lépéseket:

1. Távolítsa el a Azure PowerShell korábbi telepítését a Windows rendszerből a **Beállítások**területen található **alkalmazások & szolgáltatások** beállítással.

1. Győződjön meg arról, hogy a PowerShellGet legújabb verziója van telepítve. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsot a legújabb verzió telepítéséhez:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. A PowerShellGet telepítése után zárjuk be és nyissa meg újra a PowerShell ablakot.

1. Telepítse a Azure PowerShell legújabb verzióját:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Telepítse az az. Storage Preview modult:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

További információ a Azure PowerShell telepítéséről: [Azure PowerShell telepítése a PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Időponthoz tartozó visszaállítás engedélyezése és konfigurálása

Az adott időponthoz tartozó visszaállítás engedélyezése és konfigurálása előtt engedélyezze az előfeltételeket: helyreállítható törlés, a hírcsatorna módosítása és a blob verziószámozása. Az egyes funkciók engedélyezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

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

A visszaállítási művelet elindításához hívja meg a Restore-AzStorageBlobRange parancsot, és adja meg a visszaállítási pontot UTC **datetime** értékként. Megadhatja az lexicographical-tartományokat, amelyekkel visszaállíthatja a blobokat, vagy kihagyhat egy tartományt a Storage-fiók összes tárolójában lévő összes blob visszaállításához. Egy visszaállítási műveletben legfeljebb 10 lexicographical-tartomány támogatott. A visszaállítási művelet végrehajtása több percet is igénybe vehet.

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

Ha az összes tárolót és blobot vissza szeretné állítani a Storage-fiókban, hívja meg a Restore-AzStorageBlobRange parancsot, és hagyja ki a `-BlobRestoreRange` paramétert. Az alábbi példa a Storage-fiókban lévő tárolókat a jelen pillanatban 12 órával visszaállítja a következő időpont előtt:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Blokkos Blobok egyetlen tartományának visszaállítása

Blobok egy tartományának visszaállításához hívja meg a Restore-AzStorageBlobRange parancsot, és adja meg a paraméterhez a lexicographical és a Blobok nevét `-BlobRestoreRange` . A tartomány kezdete a befogadóban van, a tartomány vége pedig kizárólagos.

Például egy *minta-tároló*nevű tárolóban lévő Blobok visszaállításához megadhat egy olyan tartományt, amely *minta-tárolóval* kezdődik, és a *minta-container1*végződik. A kezdő és a záró tartományokban megnevezett tárolók nem kötelezőek. Mivel a tartomány vége kizárólagos, még akkor is, ha a Storage-fiók tartalmaz egy *minta-container1*nevű tárolót, a rendszer csak a *minta-tároló* nevű tárolót állítja vissza:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Ha meg szeretné adni a Blobok egy részhalmazát egy tárolóban a visszaállításhoz, egy perjel (/) használatával válassza el a tároló nevét a blob mintából. A következő tartomány például kijelöli a blobokat egyetlen tárolóban, amelynek neve a *d* – *f*betűvel kezdődik:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Ezután adja meg a tartományt a Restore-AzStorageBlobRange parancs számára. Adja meg a visszaállítási pontot a paraméter UTC **datetime** értékének megadásával `-TimeToRestore` . Az alábbi példa a megadott tartományba tartozó blobokat a jelen pillanat előtti 3 nappal visszaállítja.

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
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>Következő lépések

- [Időponthoz való visszaállítás a blokk Blobok számára (előzetes verzió)](point-in-time-restore-overview.md)
- [Helyreállítható törlés](soft-delete-overview.md)
- [Hírcsatorna módosítása (előzetes verzió)](storage-blob-change-feed.md)
- [BLOB verziószámozása (előzetes verzió)](versioning-overview.md)
