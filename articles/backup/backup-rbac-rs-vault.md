---
title: Biztonsági mentések kezelése szerepköralapú hozzáférés-vezérléssel
description: A Szerepköralapú hozzáférés-vezérlés segítségével kezelheti a biztonsági mentéskezelési műveletekhez való hozzáférést a Recovery Services tárolóban.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: e2e32ac6981635e3b9885119fdf397783ac32cc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273201"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Szerepköralapú hozzáférés-vezérlés használata az Azure Biztonsági mentés helyreállítási pontjainak kezeléséhez

Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az RBAC használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amelyre a felhasználóknak a feladataik elvégzéséhez szüksége van.

> [!IMPORTANT]
> Az Azure Backup által biztosított szerepkörök az Azure Portalon, illetve a REST API-n vagy a Recovery Services-tárolón keresztül PowerShell vagy CLI-parancsmagok on keresztül hajthatók végre. Az Azure biztonsági másolat ügynökügyfél-felhasználói felületén vagy a System center Data Protection Manager felhasználói felületén vagy az Azure Backup Server felhasználói felületén végrehajtott műveletek e szerepkörök nem szabályozhatatlanok.

Az Azure Backup három beépített szerepkört biztosít a biztonsági mentéskezelési műveletek vezérléséhez. További információ az [Azure beépített RBAC-szerepköreivel](../role-based-access-control/built-in-roles.md) kapcsolatban.

* [Biztonsági másolat közreműködője](../role-based-access-control/built-in-roles.md#backup-contributor) – Ez a szerepkör rendelkezik a biztonsági mentés létrehozására és kezelésére vonatkozó összes engedéllyel, kivéve a Helyreállítási szolgáltatások tárolójának törlését és mások hozadékát. Képzelje el ezt a szerepkört a biztonsági mentés kezelés rendszergazdájaként, aki minden biztonsági mentéskezelési műveletet el tud végezni.
* [Biztonságimásolat-felelős](../role-based-access-control/built-in-roles.md#backup-operator) – Ez a szerepkör rendelkezik a közreműködő minden hez szükséges engedéllyel, kivéve a biztonsági mentési és biztonsági mentési házirendek kezelését. Ez a szerepkör egyenértékű a közreműködő, kivéve, hogy nem végezhet destruktív műveleteket, például a törlési adatok törlésével vagy a helyszíni erőforrások regisztrációjának megszüntetése.
* [Biztonsági másolat készítő –](../role-based-access-control/built-in-roles.md#backup-reader) Ez a szerepkör rendelkezik az összes biztonságimentési felügyeleti művelet megtekintéséhez szükséges engedélyekkel. Képzeld el, hogy ez a szerep egy megfigyelő személy.

Ha szeretné meghatározni a saját szerepkörök még több vezérlést, olvassa el, hogyan [hozhat létre egyéni szerepkörök](../role-based-access-control/custom-roles.md) az Azure RBAC-ban.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>A biztonsági mentés beépített szerepkörei hozzárendelése biztonsági mentési felügyeleti műveletekhez

Az alábbi táblázat a biztonsági mentési felügyeleti műveleteket és a művelet végrehajtásához szükséges minimális RBAC szerepkört rögzíti.

| Kezelési művelet | Minimális RBAC szerepkör szükséges | Szükséges hatókör |
| --- | --- | --- |
| Helyreállítási tár létrehozása | Biztonsági másolat közreműködője | A tárolót tartalmazó erőforráscsoport |
| Az Azure-beli virtuális gépek biztonsági mentésének engedélyezése | Biztonsági másolat operátora | A tárolót tartalmazó erőforráscsoport |
| | Virtuális gépek közreműködője | Virtuális gép erőforrás |
| A virtuális gép igény szerinti biztonsági mentése | Biztonsági másolat operátora | Recovery Services-tároló |
| Virtuális gép visszaállítása | Biztonsági másolat operátora | Recovery Services-tároló |
| | Közreműködő | Erőforráscsoport, amelyben a virtuális gép telepítve lesz |
| | Virtuális gépek közreműködője | Forrás virtuális gép, amely biztonsági másolatot kapott |
| Nem felügyelt lemezek virtuálisgép-biztonsági másolatának visszaállítása | Biztonsági másolat operátora | Recovery Services-tároló |
| | Virtuális gépek közreműködője | Forrás virtuális gép, amely biztonsági másolatot kapott |
| | Tárfiók-közreműködő | A tárfiók-erőforrás, ahol a lemezek vissza lesznek állítva |
| Felügyelt lemezek visszaállítása virtuális gép biztonsági mentéséből | Biztonsági másolat operátora | Recovery Services-tároló |
| | Virtuális gépek közreműködője | Forrás virtuális gép, amely biztonsági másolatot kapott |
| | Tárfiók-közreműködő | A visszaállítás részeként kiválasztott ideiglenes tárfiók a tárolóból származó adatok tárolásához felügyelt lemezekké történő konvertálás előtt |
| | Közreműködő | Az az erőforráscsoport, amelyre a felügyelt lemez(ek) vissza lesznek állítva |
| Az egyes fájlok visszaállítása a virtuális gép biztonsági mentéséből | Biztonsági másolat operátora | Recovery Services-tároló |
| | Virtuális gépek közreműködője | Forrás virtuális gép, amely biztonsági másolatot kapott |
| Biztonsági mentési szabályzat létrehozása az Azure vm biztonsági mentéséhez | Biztonsági másolat közreműködője | Recovery Services-tároló |
| Az Azure vm biztonsági mentési biztonsági mentési szabályzatának módosítása | Biztonsági másolat közreműködője | Recovery Services-tároló |
| Az Azure VM biztonsági mentési biztonsági mentésének biztonsági mentési szabályzatának törlése | Biztonsági másolat közreműködője | Recovery Services-tároló |
| A biztonsági mentés leállítása (adatok megőrzésével vagy adatok törlésével) a virtuális gépek biztonsági mentésén | Biztonsági másolat közreműködője | Recovery Services-tároló |
| Helyszíni Windows Server/client/SCDPM vagy Azure Backup Server regisztrálása | Biztonsági másolat operátora | Recovery Services-tároló |
| Regisztrált helyszíni Windows Server/client/SCDPM vagy Azure Backup Server törlése | Biztonsági másolat közreműködője | Recovery Services-tároló |

> [!IMPORTANT]
> Ha virtuálisgép-közreműködőt ad meg egy virtuális gép erőforrás-hatókörénél, és a virtuális gép beállításai nak részeként a Biztonsági mentés elemre kattint, akkor megnyílik a "Biztonsági mentés engedélyezése" képernyő, még akkor is, ha a virtuális gépről már készül biztonsági másolat, mivel a biztonsági mentés állapotának ellenőrzésére irányuló hívás csak az előfizetés szintjén működik. Ennek elkerülése érdekében vagy nyissa meg a virtuális gép biztonsági mentési elemnézetét, vagy adja meg a Virtuálisgép közreműködői szerepkört előfizetési szinten.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Az Azure-fájlmegosztás biztonsági mentésének minimális szerepkörkövetelményei

Az alábbi táblázat a biztonsági mentési felügyeleti műveleteket és az Azure File share-művelet végrehajtásához szükséges megfelelő szerepkört rögzíti.

| Kezelési művelet | Szükséges szerepkör | Források |
| --- | --- | --- |
| Az Azure-fájlmegosztások biztonsági másolatának engedélyezése | Biztonsági másolat közreműködője |Recovery Services-tároló |
| |Tárfiók | Közreműködői tárfiók erőforrás |
| A virtuális gép igény szerinti biztonsági mentése | Biztonsági másolat operátora | Recovery Services-tároló |
| Fájlmegosztás visszaállítása | Biztonsági másolat operátora | Recovery Services-tároló |
| | Tárfiók-közreműködő | A tárfiók azon erőforrásai, ahol a forrás- és a Célfájl-megosztások találhatók |
| Egyéni fájlok visszaállítása | Biztonsági másolat operátora | Recovery Services-tároló |
| |Tárfiók-közreműködő|A tárfiók azon erőforrásai, ahol a forrás- és a Célfájl-megosztások találhatók |
| A védelem kikapcsolása |Biztonsági másolat közreműködője | Recovery Services-tároló |
| Tárfiók regisztrációjának megszüntetése a tárolóból |Biztonsági másolat közreműködője | Recovery Services-tároló |
| |Tárfiók-közreműködő | Tárfiók erőforrása|

## <a name="next-steps"></a>További lépések

* [Szerepköralapú hozzáférés-vezérlés:](../role-based-access-control/role-assignments-portal.md)Az RBAC első lépései az Azure Portalon.
* További információ a hozzáférés kezeléséhez:
  * [Powershell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Szerepköralapú hozzáférés-vezérlés hibaelhárítása](../role-based-access-control/troubleshooting.md): Javaslatok a gyakori problémák elhárítására.
