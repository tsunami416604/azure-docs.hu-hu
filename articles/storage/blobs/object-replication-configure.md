---
title: Objektum-replikáció konfigurálása (előzetes verzió)
titleSuffix: Azure Storage
description: Megtudhatja, hogyan konfigurálhatja az objektum-replikációt úgy, hogy aszinkron módon másolja a blokk blobokat egy tárolóból egy másikba.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9cb9f1a33c37487f4bfb1419d45d4e42a862d815
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84888119"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>Objektumok replikálásának konfigurálása a blokk Blobok számára (előzetes verzió)

Az objektum-replikáció (előzetes verzió) aszinkron módon másolja a blokk blobokat egy forrás Storage-fiók és egy cél fiók között. Az objektumok replikálásával kapcsolatos további információkért lásd: [objektum-replikáció (előzetes verzió)](object-replication-overview.md).

Az objektumok replikálásának konfigurálásakor létre kell hoznia egy replikációs házirendet, amely megadja a forrás Storage-fiókot és a célként megadott fiókot. A replikációs házirend egy vagy több olyan szabályt tartalmaz, amely a forrás tárolót és a célhelyet adja meg, és jelzi, hogy a rendszer melyik blokk blobokat replikálja a forrás tárolóban.

Ez a cikk azt ismerteti, hogyan konfigurálható az objektum replikálása a Storage-fiókhoz a Azure Portal, a PowerShell vagy az Azure CLI használatával. Az objektumok replikálásának konfigurálásához használhatja az Azure Storage erőforrás-szolgáltató ügyfél-kódtárainak egyikét is.

## <a name="create-a-replication-policy-and-rules"></a>Replikációs házirend és szabályok létrehozása

Az objektumok replikálásának konfigurálása előtt hozza létre a forrás és a cél Storage-fiókokat, ha azok még nem léteznek. Mindkét fióknak általános célú v2 Storage-fióknak kell lennie. További információ: [Azure Storage-fiók létrehozása](../common/storage-account-create.md).

A Storage-fiók legfeljebb két célobjektum forrásaként használható. A cél fiók legfeljebb két forrásoldali fiókkal rendelkezhet. A forrás- és célfiókok mind lehetnek eltérő régiókban. Külön replikációs házirendeket konfigurálhat az egyes célszámítógépekre történő adatreplikáláshoz.

Mielőtt elkezdené, győződjön meg arról, hogy regisztrált a következő szolgáltatás-előnézetekhez:

- [Objektum-replikáció (előzetes verzió)](object-replication-overview.md)
- [BLOB verziószámozása (előzetes verzió)](versioning-overview.md)
- [A hírcsatorna-támogatás módosítása az Azure Blob Storage (előzetes verzió)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Mielőtt az objektum-replikálást konfigurálja a Azure Portalban, hozza létre a forrás-és a cél tárolókat a megfelelő Storage-fiókjaiban, ha még nem léteznek. Emellett engedélyezte a blob verziószámozását, és megváltoztathatja a forrás fiókon a hírcsatornát, és engedélyezheti a blob verziószámozását a célhelyen.

Ha a Azure Portal replikációs szabályzatot szeretne létrehozni, kövesse az alábbi lépéseket:

1. Navigáljon a forrás Storage-fiókhoz a Azure Portal.
1. A **blob Service**területen válassza az **objektum-replikáció**elemet.
1. Válassza a **replikáció beállítása**lehetőséget.
1. Válassza ki a cél előfizetést és a Storage-fiókot.
1. A **Container Pairs (tároló párok** ) szakaszban válasszon ki egy forrásoldali tárolót a forrás fiókból, valamint egy célhelyet a célhelyről. Replikációs házirendben legfeljebb 10 tároló pár hozható létre.

    Az alábbi képen a replikációs szabályok halmaza látható.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="A Azure Portal replikációs szabályait ábrázoló képernyőkép":::

1. Ha kívánja, egy vagy több szűrőt is megtalálhat, hogy csak az előtag mintájának megfelelő blobokat másolja. Ha például egy előtagot ad meg `b` , csak azok a Blobok replikálódnak, amelyek neve ezzel a betűvel kezdődik. A virtuális könyvtárat az előtag részeként is megadhatja.

    Az alábbi képen olyan szűrők láthatók, amelyek korlátozzák, hogy mely Blobok legyenek átmásolva egy replikációs szabály részeként.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="A replikációs szabály szűrőinek megjelenítését bemutató képernyőkép":::

1. Alapértelmezés szerint a másolási hatókör úgy van beállítva, hogy csak az új objektumokat másolja. Ha a tárolóban lévő összes objektumot át szeretné másolni, vagy egyéni dátumból és időpontból szeretné másolni az objektumokat, válassza a **módosítás** hivatkozást, és konfigurálja a tároló pár másolási hatókörét.

    Az alábbi képen egy egyéni másolási hatókör látható.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Az objektumok replikálásának egyéni másolási hatókörét bemutató képernyőkép":::

1. Válassza a **Mentés és az alkalmaz** lehetőséget a replikációs házirend létrehozásához és az adatreplikálás megkezdéséhez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell használatával történő replikációs házirend létrehozásához először telepítse a [2.0.1-](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) es verziójának az az. Storage PowerShell-modul előzetes verzióját. Az előzetes verziójú modul telepítéséhez kövesse az alábbi lépéseket:

1. Távolítsa el a Azure PowerShell korábbi telepítését a Windows rendszerből a **Beállítások**területen található **alkalmazások & szolgáltatások** beállítással.

1. Győződjön meg arról, hogy a PowerShellGet legújabb verziója van telepítve. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsot a legújabb verzió telepítéséhez:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    A PowerShellGet telepítése után zárjuk be és nyissa meg újra a PowerShell ablakot.

1. Telepítse a Azure PowerShell legújabb verzióját:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Telepítse az az. Storage Preview modult:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

További információ a Azure PowerShell telepítéséről: [Azure PowerShell telepítése a PowerShellGet](/powershell/azure/install-az-ps).

Az alábbi példa azt szemlélteti, hogyan hozható létre replikációs házirend a forrás-és a célhelyen. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az Azure CLI-vel szeretne replikációs szabályzatot létrehozni, először telepítse az Azure Storage előzetes bővítményét:

```azurecli
az extension add -n storage-or-preview
```

Ezután jelentkezzen be az Azure-beli hitelesítő adataival:

```azurecli
az login
```

Engedélyezze a blob-verziószámozást a forrás-és a cél Storage-fiókokon, és engedélyezze a fiók módosítási csatornáját. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

```azurecli
az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Hozza létre a forrás-és a cél tárolókat a megfelelő Storage-fiókjaiban.

```azurecli
az storage container create --account-name <source-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <source-storage-account> --name source-container4 --auth-mode login

az storage container create --account-name <dest-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <dest-storage-account> --name source-container4 --auth-mode login
```

Hozzon létre egy új replikációs házirendet és a társított szabályokat a célhelyen.

```azurecli
az storage account or-policy create --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

Hozza létre a szabályzatot a forrásoldali fiókban a szabályzat azonosítója alapján.

```azurecli
az storage account or-policy show --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>Replikációs házirend eltávolítása

A replikációs házirend és a hozzá tartozó szabályok eltávolításához használja a Azure Portal, a PowerShell vagy a parancssori felületet.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Ha el szeretné távolítani egy replikációs házirendet a Azure Portalban, kövesse az alábbi lépéseket:

1. Navigáljon a forrás Storage-fiókhoz a Azure Portal.
1. A **Beállítások**területen válassza az **objektum replikálása**elemet.
1. Kattintson a házirend neve melletti **további** gombra.
1. Válassza a **szabályok törlése**lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A replikációs szabályzat eltávolításához törölje a megfelelő házirendet a forrás-és a cél fiókból is. A házirend törlése a hozzá társított szabályokat is törli.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A replikációs szabályzat eltávolításához törölje a megfelelő házirendet a forrás-és a cél fiókból is. A házirend törlése a hozzá társított szabályokat is törli.

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>További lépések

- [Objektum-replikáció áttekintése (előzetes verzió)](object-replication-overview.md)
