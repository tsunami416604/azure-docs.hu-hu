---
title: Biztonsági másolatok kezelése szerepköralapú Access Control
description: Szerepköralapú Access Control segítségével kezelheti a biztonsági mentési felügyeleti műveletekhez való hozzáférést Recovery Services-tárolóban.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 1a4bb617511f5d5ccf882ccb11e7d88eb7a848ea
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017804"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Szerepköralapú Access Control használata Azure Backup helyreállítási pontok kezeléséhez

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi a részletes hozzáférés-kezelést az Azure-hoz. Az RBAC használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amelyre a felhasználóknak a feladataik elvégzéséhez szüksége van.

> [!IMPORTANT]
> Az Azure Backup által biztosított szerepkörök csak olyan műveletekre korlátozódnak, amelyek Azure Portal vagy REST API vagy Recovery Services Vault PowerShell vagy CLI parancsmagok segítségével hajthatók végre. A Azure Backup ügynök ügyfél felhasználói felületén vagy a System Center Data Protection Manager felhasználói felületen vagy Azure Backup Server felhasználói felületen végrehajtott műveletek nem tartoznak a szerepkörök felügyeletéhez.

A Azure Backup három beépített szerepkört biztosít a biztonságimásolat-kezelési műveletek vezérléséhez. További információ az [Azure beépített szerepköreiről](../role-based-access-control/built-in-roles.md)

* [Biztonsági másolat közreműködői](../role-based-access-control/built-in-roles.md#backup-contributor) – ez a szerepkör rendelkezik a biztonsági másolatok létrehozásához és kezeléséhez szükséges engedélyekkel, kivéve a Recovery Services-tároló törlését és másokhoz való hozzáférést biztosít. Képzelje el ezt a szerepkört a biztonsági mentési felügyelet rendszergazdájának, aki minden biztonsági mentési felügyeleti műveletet végrehajthat.
* [Biztonságimásolat-felelős](../role-based-access-control/built-in-roles.md#backup-operator) – ez a szerepkör a biztonsági mentést és a biztonsági mentési házirendek felügyeletét kivéve minden közreműködőhöz rendelkezik engedéllyel. Ez a szerepkör egyenértékű a közreműködővel, kivéve, ha nem tud olyan romboló műveleteket végezni, mint például a biztonsági mentés leállítása vagy a helyszíni erőforrások regisztrációjának törlése.
* [Backup Reader](../role-based-access-control/built-in-roles.md#backup-reader) – ez a szerepkör jogosult az összes biztonsági mentési felügyeleti művelet megtekintésére. Képzelje el, hogy ezt a szerepkört figyelő személynek kell lennie.

Ha a saját szerepköröket is meg szeretné határozni még több szabályozáshoz, tekintse meg az [Egyéni szerepkörök létrehozása](../role-based-access-control/custom-roles.md) az Azure RBAC című témakört.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Beépített szerepkörök biztonsági mentésének leképezése biztonsági mentési felügyeleti műveletekre

Az alábbi táblázat a művelet végrehajtásához szükséges biztonságimásolat-kezelési műveleteket és a hozzá tartozó minimális Azure-szerepkört rögzíti.

| Kezelési művelet | Minimálisan szükséges Azure-szerepkör | Hatókör szükséges |
| --- | --- | --- |
| Helyreállítási tár létrehozása | Biztonsági mentési közreműködő | A tárolót tartalmazó erőforráscsoport |
| Azure-beli virtuális gépek biztonsági mentésének engedélyezése | Biztonságimásolat-felelős | A tárolót tartalmazó erőforráscsoport |
| | Virtuális gépek közreműködője | VM-erőforrás |
| Virtuális gép igény szerinti biztonsági mentése | Biztonságimásolat-felelős | Helyreállítási tár |
| Virtuális gép visszaállítása | Biztonságimásolat-felelős | Helyreállítási tár |
| | Közreműködő | Az erőforráscsoport, amelyben a virtuális gép üzembe lesz helyezve |
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül |
| Nem felügyelt lemezek visszaállítása virtuális gép biztonsági mentése | Biztonságimásolat-felelős | Helyreállítási tár |
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül |
| | Tárfiók-közreműködő | A Storage-fiók erőforrása, ahol a lemezek vissza lesznek állítva |
| Felügyelt lemezek visszaállítása a virtuális gép biztonsági másolatából | Biztonságimásolat-felelős | Helyreállítási tár |
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül |
| | Tárfiók-közreműködő | A visszaállítás részeként kijelölt ideiglenes Storage-fiók, amely a tárolóból származó adatok tárolására szolgál, mielőtt átalakítja őket a felügyelt lemezekre. |
| | Közreműködő | Az erőforráscsoport, amelybe a felügyelt lemez (ek) vissza lesz állítva |
| Egyedi fájlok visszaállítása a virtuális gép biztonsági másolatából | Biztonságimásolat-felelős | Helyreállítási tár |
| | Virtuális gépek közreműködője | A forrásként szolgáló virtuális gép, amelyről biztonsági mentés készül |
| Biztonsági mentési szabályzat létrehozása az Azure-beli virtuális gépek biztonsági mentéséhez | Biztonsági mentési közreműködő | Helyreállítási tár |
| Azure-beli virtuális gép biztonsági mentési szabályzatának módosítása | Biztonsági mentési közreműködő | Helyreállítási tár |
| Azure-beli virtuális gép biztonsági mentési szabályzatának törlése | Biztonsági mentési közreműködő | Helyreállítási tár |
| Biztonsági mentés leállítása (az adat megőrzése vagy az adat törlése) a virtuális gép biztonsági mentésében | Biztonsági mentési közreműködő | Helyreállítási tár |
| Helyszíni Windows Server/Client/SCDPM vagy Azure Backup Server regisztrálása | Biztonságimásolat-felelős | Helyreállítási tár |
| Regisztrált helyszíni Windows Server/Client/SCDPM vagy Azure Backup Server törlése | Biztonsági mentési közreműködő | Helyreállítási tár |

> [!IMPORTANT]
> Ha a virtuálisgép-erőforrás hatókörében megad egy virtuálisgép-közreműködőt, és a virtuálisgép-beállítások részeként a biztonsági mentés elemre kattint, akkor a biztonsági mentés engedélyezése képernyő is megnyílik, bár a virtuális gép már biztonsági másolatként működik, mert a biztonsági mentési állapot ellenőrzésének hívása csak az előfizetés szintjén működik. Ennek elkerüléséhez nyissa meg a tárolót, és nyissa meg a virtuális gép biztonsági mentési elemének nézetét, vagy a virtuális gép közreműködői szerepkört egy előfizetési szinten válassza.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Az Azure-fájlmegosztás biztonsági mentésének minimális szerepköri követelményei

Az alábbi táblázat rögzíti az Azure fájlmegosztás művelet végrehajtásához szükséges biztonságimásolat-kezelési műveleteket és a hozzá tartozó szerepkört.

| Kezelési művelet | Szerepkör szükséges | További források |
| --- | --- | --- |
| Azure-fájlmegosztás biztonsági mentésének engedélyezése | Biztonsági mentési közreműködő |Helyreállítási tár |
| |Tárfiók | Közreműködői Storage-fiók erőforrása |
| Virtuális gép igény szerinti biztonsági mentése | Biztonságimásolat-felelős | Helyreállítási tár |
| Fájlmegosztás visszaállítása | Biztonságimásolat-felelős | Helyreállítási tár |
| | Tárfiók-közreműködő | A Storage-fiók erőforrásai, ahol a visszaállítási forrás és a célfájl-megosztások találhatók |
| Egyedi fájlok visszaállítása | Biztonságimásolat-felelős | Helyreállítási tár |
| |Tárfiók-közreműködő|A Storage-fiók erőforrásai, ahol a visszaállítási forrás és a célfájl-megosztások találhatók |
| A védelem kikapcsolása |Biztonsági mentési közreműködő | Helyreállítási tár |
| Storage-fiók regisztrációjának törlése a tárból |Biztonsági mentési közreműködő | Helyreállítási tár |
| |Tárfiók-közreműködő | Storage-fiók erőforrása|

## <a name="next-steps"></a>Következő lépések

* [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): első lépések a RBAC a Azure Portal.
* Ismerje meg, hogyan kezelheti a hozzáférést a következővel:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Szerepköralapú Access Control hibaelhárítás](../role-based-access-control/troubleshooting.md): javaslatok a gyakori problémák elhárítására.
