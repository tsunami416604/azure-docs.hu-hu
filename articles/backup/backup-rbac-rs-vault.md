---
title: 'Az Azure szerepköralapú hozzáférés-vezérlés biztonsági másolatok kezelése:'
description: Szerepköralapú hozzáférés-vezérlés használatával a biztonsági mentési műveletek a Recovery Services-tárolóban való hozzáférés kezelése.
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/11/2018
ms.author: trinadhk
ms.openlocfilehash: 855b75652fca421df12766f7711152d1e3ca2aeb
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009251"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Szerepköralapú hozzáférés-vezérlés használata kezelheti az Azure Backup helyreállítási pontok
Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az RBAC használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amelyre a felhasználóknak a feladataik elvégzéséhez szüksége van.

> [!IMPORTANT]
> Az Azure Backup által megadott szerepkörök korlátozódnak, amelyek az Azure Portalon végezhető műveletek vagy Recovery Services-tároló PowerShell-parancsmagokat. Az Azure biztonsági mentési ügynök ügyfél felhasználói felületének vagy a System center Data Protection Manager felhasználói felületén vagy az Azure Backup Server felhasználói felületén ezek a szerepkörök irányítását esnek végrehajtott műveleteket.

Az Azure Backup biztonsági mentési műveletek szabályozásához 3 beépített szerepkört biztosít. További információ az [Azure beépített RBAC-szerepköreivel](../role-based-access-control/built-in-roles.md) kapcsolatban.

* [Biztonsági mentési közreműködő](../role-based-access-control/built-in-roles.md#backup-contributor) – Ez a szerepkör létrehozásához és kezeléséhez a biztonsági mentés a Recovery Services-tároló létrehozásának és a másoknak való hozzáférés megadását kivételével minden olyan engedéllyel rendelkezik. Képzelje el ezt a szerepkört, akik minden biztonságimásolat-felügyeleti műveletet végezhet biztonsági másolatokat kezelő rendszergazdájaként.
* [Biztonsági mentési operátor](../role-based-access-control/built-in-roles.md#backup-operator) – Ez a szerepkör jogosult mindent közreműködő kivéve a biztonsági mentési és kezelését a biztonsági mentési szabályzatok eltávolítása. Ez a szerepkör közreműködői egyenértékű, azzal a különbséggel, nem romboló műveletek végrehajtása, például a biztonsági mentés leállítása az adatok törlése, vagy távolítsa el a helyszíni erőforrások regisztrációját.
* [Biztonsági mentési olvasó](../role-based-access-control/built-in-roles.md#backup-reader) – Ez a szerepkör az összes biztonsági mentési műveletek megtekintéséhez szükséges engedélyekkel rendelkezik. Képzelje el ezt a szerepkört olyan figyelési személy.

Ha még több vezérlő saját szerepköröket definiál keres, tekintse meg, hogyan [hozhat létre egyéni szerepkörök](../role-based-access-control/custom-roles.md) az Azure RBAC-ben.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Beépített biztonsági szerepkörök hozzárendelése a biztonságimásolat-kezelési műveletek
Az alábbi táblázat a biztonsági mentés felügyeleti műveletek és a művelet végrehajtásához szükséges megfelelő minimális RBAC szerepkör rögzíti.

| Ügynökfelügyeleti művelet | Minimálisan szükséges RBAC-szerepkör |
| --- | --- |
| Recovery Services-tároló létrehozása | A tár erőforráscsoportja közreműködője |
| Azure virtuális gépek biztonsági mentésének engedélyezése | A tárolóban, a virtuális gépeken a virtuális gépek közreműködője tartalmazó erőforráscsoportot, a hatókör meg van határozva biztonságimásolat-felelős |
| Igény szerinti biztonsági mentést a virtuális gép | Biztonságimásolat-felelős |
| Virtuális gép visszaállítása | Biztonságimásolat-felelős, erőforrás-csoport közreműködői, amelyben a virtuális gép üzembe helyezése, történik olvassa el a virtuális hálózaton, és csatlakozzon a kiválasztott alhálózatban |
| Lemezek, virtuális gépek biztonsági mentését az egyes fájlok visszaállítása | Biztonságimásolat-felelős, virtuális gépek a virtuális gépek közreműködője |
| Azure VM Backup biztonsági mentési szabályzat létrehozása | Biztonsági mentési közreműködő |
| Az Azure VM backup biztonsági mentési szabályzat módosítása | Biztonsági mentési közreműködő |
| Az Azure VM backup biztonsági mentési szabályzat törlése | Biztonsági mentési közreműködő |
| Biztonsági mentés leállítása (az adatok megőrzésével vagy adatok törlése) a virtuális gép biztonsági mentése | Biztonsági mentési közreműködő |
| A helyszíni Windows Server/ügyfélen/SCDPM vagy az Azure Backup Server regisztrálása | Biztonságimásolat-felelős |
| Regisztrált helyszíni Windows Server/ügyfélen/SCDPM vagy az Azure Backup Server törlése | Biztonsági mentési közreműködő |

## <a name="next-steps"></a>További lépések
* [Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md): Ismerkedés az RBAC az Azure Portalon.
* Ismerje meg, hogyan való hozzáférés kezelése:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Szerepköralapú hozzáférés-vezérlés hibáinak elhárítása](../role-based-access-control/troubleshooting.md): gyakori hibák rögzítésére vonatkozó javaslatokat kérhet.
