---
title: Tárfiók utolsó szinkronizálási ideje tulajdonságának ellenőrzése
titleSuffix: Azure Storage
description: Ismerje meg, hogyan ellenőrizheti az **utolsó szinkronizálási idő** tulajdonságot egy georeplikált tárfiókhoz. Az **utolsó szinkronizálási idő** tulajdonság azt jelzi, hogy az elsődleges régióösszes írása mikor lett sikeresen beírva a másodlagos régióba.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165487"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Tárfiók utolsó szinkronizálási ideje tulajdonságának ellenőrzése

A tárfiók konfigurálásakor megadhatja, hogy az adatok másolása egy másodlagos régióba, amely több száz mérföldre van az elsődleges régiótól. A georeplikáció tartósságot biztosít az adatok számára az elsődleges régióban jelentős kimaradás esetén, például természeti katasztrófa esetén. Ha emellett engedélyezi az olvasási hozzáférést a másodlagos régióhoz, az adatok olvasási műveletekhez is elérhetők maradnak, ha az elsődleges régió elérhetetlenné válik. Az alkalmazás úgy tervezheti meg, hogy zökkenőmentesen váltson a másodlagos régióból történő olvasásra, ha az elsődleges régió nem válaszol.

Georedundáns tárolás (GRS) és a geozóna-redundáns tárolás (GZRS) (előzetes verzió) egyaránt replikálja az adatokat aszinkron módon egy másodlagos régióba. Olvasási hozzáférés a másodlagos régió, engedélyezze az olvasási hozzáférés georedundáns tárolás (RA-GRS) vagy olvasási hozzáférés geozóna redundáns tárolás (RA-GZRS). Az Azure Storage által kínált különböző redundancialehetőségekről az [Azure Storage redundanciája](storage-redundancy.md)című témakörben talál további információt.

Ez a cikk ismerteti, hogyan ellenőrizheti az **utolsó szinkronizálási idő** tulajdonsága a tárfiók, így kiértékelheti az elsődleges és a másodlagos régiók közötti eltérések.

## <a name="about-the-last-sync-time-property"></a>Az utolsó szinkronizálás időpontja tulajdonság

Mivel a georeplikáció aszinkron, lehetséges, hogy az elsődleges régióba írt adatok még nem lettek megírva a másodlagos régióba a kimaradás bekövetkeztekor. Az **Utolsó szinkronizálás iódje** tulajdonság azt jelzi, hogy az elsődleges régióból származó adatok utoljára mikor lett sikeresen beírva a másodlagos régióba. Az elsődleges régióba az utolsó szinkronizálási idő előtt végzett összes írás a másodlagos helyről olvasható. Az utolsó szinkronizálási idő tulajdonság után az elsődleges régióba írt írások lehet, hogy még nem érhetők el az olvasáshoz.

Az **Utolsó szinkronizálási idő** tulajdonság a GMT dátum/idő értéke.

## <a name="get-the-last-sync-time-property"></a>Az utolsó szinkronizálás időpontja tulajdonság beszereznie

A PowerShell vagy az Azure CLI segítségével lekérheti az **utolsó szinkronizálási idő** tulajdonság értékét.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

A powershell-fiók tárfiókjának utolsó szinkronizálási idejének lekérnie, telepítsen egy Azure Storage-előzetes modult, amely támogatja a georeplikációs statisztikák beszerzését. Például:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Ezután ellenőrizze a tárfiók **GeoReplicationStats.LastSyncTime** tulajdonságát. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A tárfiók azure CLI-vel való utolsó szinkronizálási idejének lekérni, ellenőrizze a tárfiók **geoReplicationStats.lastSyncTime** tulajdonságát. A `--expand` paraméter segítségével adja vissza a **geoReplicationStats**alá ágyazott tulajdonságok értékeit. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire:

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

- [Az Azure Storage redundanciája](storage-redundancy.md)
- [Tárfiók redundanciabeállításának módosítása](redundancy-migration.md)
- [Magas rendelkezésre állású alkalmazások tervezése olvasási hozzáférésű georedundáns tárolással](storage-designing-ha-apps-with-ragrs.md)