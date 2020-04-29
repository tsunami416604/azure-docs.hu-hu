---
title: A Storage-fiók utolsó szinkronizálási ideje tulajdonságának megtekintése
titleSuffix: Azure Storage
description: Ismerje meg, hogyan ellenőrizhető a földrajzilag replikált Storage-fiók **utolsó szinkronizálási ideje** tulajdonsága. A **Legutóbbi szinkronizálási idő** tulajdonság azt jelzi, hogy az elsődleges régióból származó összes írás sikeres volt-e a másodlagos régióba való írás során.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77165487"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>A Storage-fiók utolsó szinkronizálási ideje tulajdonságának megtekintése

A Storage-fiók konfigurálásakor megadhatja, hogy az adatok egy olyan másodlagos régióba legyenek másolva, amely több száz mérföldre van az elsődleges régióból. A Geo-replikáció az elsődleges régióban jelentős leállás, például természeti katasztrófák esetén tartósságot biztosít az adatai számára. Ha a másodlagos régió olvasási hozzáférését is engedélyezi, az adatok az olvasási műveletekhez is elérhetők maradnak, ha az elsődleges régió elérhetetlenné válik. Az alkalmazás úgy is megtervezhető, hogy zökkenőmentesen váltson a másodlagos régióból való olvasásra, ha az elsődleges régió nem válaszol.

A Geo-redundáns tárolás (GRS) és a Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió) egyaránt aszinkron módon replikálja az adatait egy másodlagos régióba. Ha olvasási hozzáférést szeretne a másodlagos régióhoz, engedélyezze az olvasási hozzáférésű geo-redundáns tárolást (RA-GRS) vagy az olvasási hozzáférésű földrajzi zóna-redundáns tárolást (RA-GZRS). További információ az Azure Storage által kínált redundancia különböző lehetőségeiről: [Azure Storage redundancia](storage-redundancy.md).

Ez a cikk azt ismerteti, hogyan ellenőrizhető a Storage-fiók **utolsó szinkronizálási ideje** tulajdonsága, hogy ki lehessen értékelni az elsődleges és a másodlagos régiók közötti eltéréseket.

## <a name="about-the-last-sync-time-property"></a>Az utolsó szinkronizálás időpontjának tulajdonsága

Mivel a Geo-replikáció aszinkron, lehetséges, hogy az elsődleges régióba írt adatvesztést a másodlagos régióba még nem írták le a leállás időpontjában. A **legutóbbi szinkronizálás ideje** tulajdonság azt jelzi, hogy az elsődleges régióból származó adatok mikor lettek sikeresen beírva a másodlagos régióba. Az elsődleges régióba az utolsó szinkronizálási idő előtt végrehajtott összes írás elérhető a másodlagos helyről való olvasáshoz. A legutóbbi szinkronizálási idő tulajdonsága után az elsődleges régióba írt írások még nem érhetők el.

Az **utolsó szinkronizálás időpontja** tulajdonság egy GMT dátum/idő érték.

## <a name="get-the-last-sync-time-property"></a>A legutóbbi szinkronizálási idő tulajdonság beolvasása

A PowerShell vagy az Azure CLI használatával kérheti le a **Legutóbbi szinkronizálási idő** tulajdonság értékét.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha a Storage-fiók utolsó szinkronizálási idejét szeretné lekérni a PowerShell-lel, telepítsen egy Azure Storage betekintő modult, amely támogatja a Geo-replikáció statisztikáit. Például:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Ezután keresse meg a Storage-fiók **GeoReplicationStats. LastSyncTime** tulajdonságát. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A Storage-fiók Azure CLI-vel való utolsó szinkronizálási időpontjának lekéréséhez tekintse meg a Storage-fiók **geoReplicationStats. lastSyncTime** tulajdonságát. Használja a `--expand` paramétert a **geoReplicationStats**alatt beágyazott tulajdonságok értékének visszaadásához. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Lásd még

- [Azure Storage-redundancia](storage-redundancy.md)
- [Storage-fiók redundancia beállításának módosítása](redundancy-migration.md)
- [A magasan elérhető alkalmazások tervezése olvasási hozzáférésű geo-redundáns tárolással](storage-designing-ha-apps-with-ragrs.md)