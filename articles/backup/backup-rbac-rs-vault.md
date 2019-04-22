---
title: 'Az Azure szerepköralapú hozzáférés-vezérlés biztonsági másolatok kezelése:'
description: Szerepköralapú hozzáférés-vezérlés használatával a biztonsági mentési műveletek a Recovery Services-tárolóban való hozzáférés kezelése.
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: trinadhk
ms.openlocfilehash: ed3797183e13a00d2c5381fa6449c111c3bc9ab9
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682525"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Szerepköralapú hozzáférés-vezérlés használata kezelheti az Azure Backup helyreállítási pontok
Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az RBAC használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amelyre a felhasználóknak a feladataik elvégzéséhez szüksége van.

> [!IMPORTANT]
> Szerepkörök az Azure Backup által biztosított korlátozva, az Azure Portalon vagy a REST API-val elvégezhető műveleteket, vagy a Recovery Services-tároló PowerShell vagy parancssori felület parancsmagok. Az Azure biztonsági mentési ügynök ügyfél felhasználói felületének vagy a System center Data Protection Manager felhasználói felületén vagy az Azure Backup Server felhasználói felületén ezek a szerepkörök irányítását esnek végrehajtott műveleteket.

Az Azure Backup biztonsági mentési műveletek szabályozásához három beépített szerepkört biztosít. További információ az [Azure beépített RBAC-szerepköreivel](../role-based-access-control/built-in-roles.md) kapcsolatban.

* [Biztonsági mentési közreműködő](../role-based-access-control/built-in-roles.md#backup-contributor) – Ez a szerepkör létrehozásához és kezeléséhez a biztonsági mentés a Recovery Services-tároló létrehozásának és a másoknak való hozzáférés megadását kivételével minden olyan engedéllyel rendelkezik. Képzelje el ezt a szerepkört, akik minden biztonságimásolat-felügyeleti műveletet végezhet biztonsági másolatokat kezelő rendszergazdájaként.
* [Biztonsági mentési operátor](../role-based-access-control/built-in-roles.md#backup-operator) – Ez a szerepkör jogosult mindent közreműködő kivéve a biztonsági mentési és kezelését a biztonsági mentési szabályzatok eltávolítása. Ez a szerepkör közreműködői egyenértékű, azzal a különbséggel, nem romboló műveletek végrehajtása, például a biztonsági mentés leállítása az adatok törlése, vagy távolítsa el a helyszíni erőforrások regisztrációját.
* [Biztonsági mentési olvasó](../role-based-access-control/built-in-roles.md#backup-reader) – Ez a szerepkör az összes biztonsági mentési műveletek megtekintéséhez szükséges engedélyekkel rendelkezik. Képzelje el ezt a szerepkört olyan figyelési személy.

Ha még több vezérlő saját szerepköröket definiál keres, tekintse meg, hogyan [hozhat létre egyéni szerepkörök](../role-based-access-control/custom-roles.md) az Azure RBAC-ben.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Beépített biztonsági szerepkörök hozzárendelése a biztonságimásolat-kezelési műveletek
Az alábbi táblázat a biztonsági mentés felügyeleti műveletek és a művelet végrehajtásához szükséges megfelelő minimális RBAC szerepkör rögzíti.

| Ügynökfelügyeleti művelet | Minimálisan szükséges RBAC-szerepkör | Hatókör szükséges |
| --- | --- | --- |
| Helyreállítási tár létrehozása | Közreműködő | A tároló tartalmazó erőforráscsoportot |
| Azure virtuális gépek biztonsági mentésének engedélyezése | Biztonsági mentési operátor | A tároló tartalmazó erőforráscsoportot |
| | Virtuális gépek közreműködője | VM-erőforrás |
| Igény szerinti biztonsági mentést a virtuális gép | Biztonsági mentési operátor | Helyreállítási tár erőforrás |
| Virtuális gép visszaállítása | Biztonsági mentési operátor | Recovery Services-tároló |
| | Közreműködő | Az erőforráscsoport, amelyben virtuális gép lesz üzembe helyezve. |
| | Virtuális gépek közreműködője | Forrás virtuális gép, amelyről biztonsági másolatot készített |
| Nem felügyelt lemezek virtuális gép biztonsági másolatának visszaállítása | Biztonsági mentési operátor | Helyreállítási tár erőforrás |
| | Virtuális gépek közreműködője | Forrás virtuális gép, amelyről biztonsági másolatot készített |
| | Tárfiók-közreműködő | Ahol lemezek fog állítani tárfiók típusú erőforrást |
| Felügyelt lemezek visszaállítását a virtuális gép biztonsági mentése | Biztonsági mentési operátor | Helyreállítási tár erőforrás |
| | Virtuális gépek közreműködője | Forrás virtuális gép, amelyről biztonsági másolatot készített |
| | Tárfiók-közreműködő | A tároló adatok tárolásához előtt konvertálja azokat a felügyelt lemezek visszaállítása részeként kiválasztott ideiglenes tárfiók |
| | Közreműködő | Az erőforráscsoportot, amelyhez felügyelt lemez lesz visszaállítva |
| Egyedi fájlok visszaállítása virtuális gép biztonsági mentése | Biztonsági mentési operátor | Helyreállítási tár erőforrás |
| | Virtuális gépek közreműködője | Forrás virtuális gép, amelyről biztonsági másolatot készített |
| Azure VM Backup biztonsági mentési szabályzat létrehozása | Biztonsági mentési közreműködő | Helyreállítási tár erőforrás |
| Az Azure VM backup biztonsági mentési szabályzat módosítása | Biztonsági mentési közreműködő | Helyreállítási tár erőforrás |
| Az Azure VM backup biztonsági mentési szabályzat törlése | Biztonsági mentési közreműködő | Helyreállítási tár erőforrás |
| Biztonsági mentés leállítása (az adatok megőrzésével vagy adatok törlése) a virtuális gép biztonsági mentése | Biztonsági mentési közreműködő | Helyreállítási tár erőforrás |
| A helyszíni Windows Server/ügyfélen/SCDPM vagy az Azure Backup Server regisztrálása | Biztonsági mentési operátor | Helyreállítási tár erőforrás |
| Regisztrált helyszíni Windows Server/ügyfélen/SCDPM vagy az Azure Backup Server törlése | Biztonsági mentési közreműködő | Helyreállítási tár erőforrás |

> [!IMPORTANT]
> Adja meg a virtuális gép közreműködő egy VM-erőforrás hatókörben, és kattintson a biztonsági mentés részeként a virtuális gép beállításait, ha az "Biztonsági mentés engedélyezése" képernyő nyílik annak ellenére, hogy a virtuális gép már készült biztonsági másolat, ellenőrizze a biztonsági mentés állapotát működését előfizetési szinten csak a hívást. Ennek elkerülése érdekében vagy tárba, és nyissa meg a biztonsági mentési elemei nézet a virtuális gép, vagy adja meg az előfizetés szintjén a Virtuálisgép-közreműködői szerepkör.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Az Azure fájlmegosztás biztonsági mentése szerepkör minimális követelményei
Az alábbi tábla bemutatja a biztonsági mentés felügyeleti műveletek és a megfelelő szerepkört kell végeznie az Azure fájlmegosztási műveletnél.

| Ügynökfelügyeleti művelet | A szerepkör szükséges | További források |
| --- | --- | --- |
| Az Azure-fájlmegosztások biztonsági mentésének engedélyezése | Biztonsági mentési közreműködő | Recovery Services-tároló |
| | Tárfiók | Tárfiók típusú erőforrást közreműködője |
| Igény szerinti biztonsági mentést a virtuális gép | Biztonsági mentési operátor | Recovery Services-tároló |
| Fájlmegosztás visszaállítása | Biztonsági mentési operátor | Recovery Services-tároló |
| | Tárfiók-közreműködő | Tárfiók erőforrásainak, ahol visszaállítási forrás és cél fájlmegosztások találhatók |
| Egyedi fájlok visszaállítása | Biztonsági mentési operátor | Recovery Services-tároló |
| | Tárfiók-közreműködő |   Tárfiók erőforrásainak, ahol visszaállítási forrás és cél fájlmegosztások találhatók |
| Védelem leállítása | Biztonsági mentési közreműködő | Recovery Services-tároló |      
| A tároló tárfiók regisztrációját |   Biztonsági mentési közreműködő | Recovery Services-tároló |
| | Tárfiók-közreműködő | Tárfiók típusú erőforrást|


## <a name="next-steps"></a>További lépések
* [Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md): Ismerkedés az RBAC az Azure Portalon.
* Ismerje meg, hogyan való hozzáférés kezelése:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Szerepköralapú hozzáférés-vezérlés hibáinak elhárítása](../role-based-access-control/troubleshooting.md): Gyakori hibák rögzítésére vonatkozó javaslatokat kérhet.
