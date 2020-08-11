---
title: Tárolók törlése (előzetes verzió)
titleSuffix: Azure Storage
description: A tárolók (előzetes verzió) helyreállítható törlésével megvédheti adatait, így könnyebben állíthatja helyre az adatokat, amikor az alkalmazás vagy egy másik Storage-fiók felhasználója hibásan módosítja vagy törölte azokat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 15e0312fab4558b9aeea1748c6ff70cf77973fb5
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057273"
---
# <a name="soft-delete-for-containers-preview"></a>Tárolók törlése (előzetes verzió)

A tárolók (előzetes verzió) helyreállítható törlésével az adatok véletlenül vagy hibásan módosíthatók vagy törölhetők. Ha a tároló-helyreállító törlés engedélyezve van egy Storage-fiókhoz, a rendszer megőrzi a törölt tárolókat és azok tartalmát az Azure Storage-ban az Ön által megadott időszakra vonatkozóan. A megőrzési időszak alatt visszaállíthatja a korábban törölt tárolókat és a bennük lévő blobokat.

A blob-adatai teljes körű védelme érdekében a Microsoft a következő adatvédelmi funkciók engedélyezését javasolja:

- Tároló-helyreállítható törlés a tároló véletlen törlésével vagy felülírásával szembeni védelem érdekében. A tárolók helyreállítható törlésének engedélyezéséről a tárolók helyreállítható [törlésének engedélyezése és kezelése](soft-delete-container-enable.md)című témakörben olvashat bővebben.
- BLOB-Törlés – az egyes Blobok véletlen törlésével vagy felülírásával szembeni védelem érdekében. Ha szeretné megtudni, hogyan engedélyezheti a Blobok törlését, olvassa el [a](soft-delete-blob-overview.md)Blobok eltávolítását ismertető témakört.
- BLOB verziószámozása (előzetes verzió), hogy automatikusan fenntartsa a blob korábbi verzióit. Ha a blob verziószámozása engedélyezve van, visszaállíthatja a blob egy korábbi verzióját az adatok helyreállításához, ha az hibásan van módosítva vagy törölve. A blob verziószámozásának engedélyezéséről a [blob verziószámozásának engedélyezése és kezelése](versioning-enable.md)című témakörben olvashat bővebben.

> [!IMPORTANT]
> Egy Storage-fiók véletlen törlésének megelőzése érdekében állítson be egy **CannotDelete** -zárolást a Storage-fiók erőforrásán. Az Azure-erőforrások zárolásával kapcsolatos további információkért lásd: [erőforrások zárolása a váratlan változások megelőzése érdekében](../../azure-resource-manager/management/lock-resources.md).

## <a name="how-container-soft-delete-works"></a>A tárolók törlésének működése

A tárolók helyreállított törlésének engedélyezésekor megadhat egy megőrzési időtartamot a törölt tárolók számára 1 és 365 nap között. Az alapértelmezett megőrzési időtartam 7 nap. A megőrzési időszak alatt helyreállíthatja a törölt tárolót a **tároló törlésének** visszavonása művelet meghívásával.

Tároló visszaállításakor visszaállíthatja az eredeti nevét, ha a nevet nem használták fel újra. Ha az eredeti tároló nevét használták, a tárolót új névvel állíthatja vissza.

A megőrzési időszak lejárta után a tároló véglegesen törlődik az Azure Storage-ból, és nem állítható helyre. Az óra azon a megőrzési időtartamon kezdődik, amelyen a tároló törlődik. Bármikor módosíthatja a megőrzési időszakot, de ne feledje, hogy a frissített megőrzési idő csak az újonnan törölt tárolók esetében érvényes. A korábban törölt tárolók véglegesen törlődnek a tároló törlésének időpontjában érvényben lévő megőrzési időtartam alapján.

A tárolók törlésének letiltása nem eredményezi végleges törlését a korábban törölt tárolók számára. A rendszer véglegesen törli a törölt tárolókat a tároló törlésének időpontjában érvényben lévő megőrzési időszak lejártakor.

## <a name="about-the-preview"></a>Az előzetes verzió ismertetése

A tároló Soft Delete a következő régiókban érhető el az előzetes verzióban:

- Közép-Franciaország
- Kelet-Kanada
- Közép-Kanada

> [!IMPORTANT]
> A tároló Soft delete előzetes verziója csak nem éles használatra készült. Az üzemi szolgáltatási szintű szerződések (SLA-kat) jelenleg nem érhetők el.

Az Azure Storage REST API 2019-12-12-es és újabb verziója támogatja a tárolók törlését.

### <a name="storage-account-support"></a>A Storage-fiók támogatása

A tárolók Soft Delete a következő típusú tárolási fiókok esetében érhető el:

- Általános célú v2 Storage-fiókok
- BLOB Storage-fiókok letiltása
- Blob Storage-fiókok

Ha a Storage-fiók egy általános célú v1-fiók, a Azure Portal használatával frissítsen egy általános célú v2-fiókra. A Storage-fiókokkal kapcsolatos további információkért lásd: az [Azure Storage-fiók áttekintése](../common/storage-account-overview.md).

A Azure Data Lake Storage Gen2-vel való használatra engedélyezett hierarchikus névtérrel rendelkező Storage-fiókok is támogatottak.

### <a name="register-for-the-preview"></a>Regisztráljon az előzetes verzióra

Ha az előzetes verzióban szeretné regisztrálni a tárolót, a PowerShell vagy az Azure CLI használatával küldjön be egy kérést a szolgáltatás regisztrálásához az előfizetésében. A kérés jóváhagyása után engedélyezheti a tárolók helyreállítható törlését bármely új vagy meglévő általános célú v2-, blob Storage-vagy prémium szintű blokk blob Storage-fiókkal.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell-lel való regisztráláshoz hívja meg a [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsot.

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel való regisztrációhoz hívja meg az az [Feature Register](/cli/azure/feature#az-feature-register) parancsot.

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>A regisztráció állapotának ellenõrzése

A regisztráció állapotának vizsgálatához használja a PowerShell vagy az Azure CLI-t.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha ellenőriznie szeretné a regisztráció állapotát a PowerShell-lel, hívja meg a [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) parancsot.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel való regisztráció állapotának megtekintéséhez hívja meg az az [Feature](/cli/azure/feature#az-feature-show) parancsot.

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Árak és számlázás

A tárolók törlésének engedélyezése nem díjköteles. A helyreállított törölt tárolókban lévő adatforgalom az aktív adatforgalommal megegyező sebességgel történik.

## <a name="next-steps"></a>További lépések

- [Tároló helyreállítható törlésének konfigurálása](soft-delete-container-enable.md)
- [A Blobok Soft delete](soft-delete-blob-overview.md)
- [BLOB verziószámozása (előzetes verzió)](versioning-overview.md)
