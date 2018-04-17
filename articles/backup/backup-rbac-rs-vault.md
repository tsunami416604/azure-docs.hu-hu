---
title: Azure szerepköralapú hozzáférés-vezérlés biztonsági mentéseit |} Microsoft Docs
description: Szerepköralapú hozzáférés-vezérlés segítségével kezelheti a biztonságimásolat-felügyeleti műveletek a Recovery Services-tároló elérésére.
services: backup
documentationcenter: ''
author: trinadhk
manager: shreeshd
editor: ''
ms.assetid: 3bd46b97-4b29-47a5-b5ac-ac174dd36760
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/22/2017
ms.author: trinadhk;markgal
ms.openlocfilehash: 442d998d8898dc40ee23ca541d35c340edf64dbd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Azure biztonsági mentési helyreállítási pontok kezelése szerepköralapú hozzáférés-vezérlés használatával
Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az RBAC használata, feladatokat elkülönítse a munkacsoporton belül, és csak olyan mértékű hozzáférést biztosítania a felhasználóknak a feladataik elvégzéséhez szükséges.

> [!IMPORTANT]
> Azure Backup szolgáltatás által biztosított szerepkörök korlátozódnak, amelyek az Azure-portálon végezhető műveletek vagy Recovery Services-tároló PowerShell-parancsmagokkal. Az Azure biztonsági mentési ügynök ügyfél felhasználói felületének vagy a System center Data Protection Manager Kezelőfelületének vagy az Azure Backup Server felhasználói felületén ezek a szerepkörök irányítását kívül esnek a végrehajtott műveleteket.

Azure biztonsági mentés 3 beépített szerepkörök, biztonsági mentési műveletek szabályozásához biztosít. További információ az [Azure beépített RBAC-szerepköreivel](../role-based-access-control/built-in-roles.md) kapcsolatban.

* [Biztonsági mentés közreműködői](../role-based-access-control/built-in-roles.md#backup-contributor) -Ez a szerepkör létrehozása és kezelése, kivéve a Recovery Services-tároló létrehozása és mások adjon hozzáférést a biztonsági mentés minden engedéllyel rendelkezik. Képzelje el a szerepkör a biztonságimásolat-felügyeleti ki minden biztonságimásolat-felügyeleti műveletet végezhet rendszergazdaként.
* [Biztonsági mentés operátor](../role-based-access-control/built-in-roles.md#backup-operator) -ezt a szerepkört minden olyan munkatárs kivéve eltávolítása a biztonsági mentési és kezelését egy biztonsági mentési házirendek engedélyekkel rendelkezik. Ez a szerepkör megegyezik közreműködői azzal a különbséggel, nem végezhet ilyen beállítások mellett pusztító műveleteket, például a stop biztonsági mentés a törli az adatokat, vagy eltávolíthatja azok regisztrációját a helyi erőforrások.
* [Biztonsági mentés olvasó](../role-based-access-control/built-in-roles.md#backup-reader) -ezt a szerepkört jogosult az összes biztonságimásolat-felügyeleti műveletek megtekintése. Képzelje el ezt a szerepkört olyan figyelési személy.

Ha még nagyobb mértékben vezérelheti a saját szerepköröket definiál, lásd: hogyan [egyéni szerepkörök létrehozása](../role-based-access-control/custom-roles.md) az Azure RBAC.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Beépített biztonsági szerepkörök hozzárendelése a biztonságimásolat-felügyeleti műveletek
A következő táblázat a biztonsági mentés felügyeleti műveletek és a művelet elvégzéséhez szükséges megfelelő minimális RBAC szerepkör rögzíti.

| Ügynökfelügyeleti művelet | Szükséges minimális RBAC szerepkör |
| --- | --- |
| Recovery Services-tároló létrehozása | A tároló erőforráscsoport közreműködő |
| Azure virtuális gépek biztonsági mentésének engedélyezése | A tároló, virtuális gép közreműködő, virtuális gépeken biztonságimásolat-felelős |
| A tárolt virtuális gépek biztonsági mentése | Biztonságimásolat-felelős |
| Virtuális gép visszaállítása | Biztonságimásolat-felelősként, erőforrás csoport közreműködő, amelyben virtuális gép és a Vnetek lesznek telepítve |
| Állítsa vissza a lemezek, a fájlokat a virtuális gép biztonsági mentése | Biztonságimásolat-felelősként, virtuális gép közreműködő, virtuális gépeken |
| Az Azure virtuális gép biztonsági mentése a biztonsági mentési házirend létrehozása | Biztonsági mentési közreműködő |
| Az Azure virtuális gép biztonsági mentése a biztonsági mentési házirend módosítása | Biztonsági mentési közreműködő |
| Az Azure virtuális gép biztonsági mentése a biztonsági mentési házirend törlése | Biztonsági mentési közreműködő |
| STOP biztonsági mentés (az adatok megőrzésével, illetve adatokat törölhet) a virtuális gép biztonsági mentése | Biztonsági mentési közreműködő |
| A helyi Windows Server/ügyfél/SCDPM vagy az Azure Backup-kiszolgáló regisztrálása | Biztonságimásolat-felelős |
| Regisztrált helyszíni Windows Server/ügyfél/SCDPM vagy az Azure Backup Server törlése | Biztonsági mentési közreműködő |

## <a name="next-steps"></a>További lépések
* [Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md): az RBAC első lépései az Azure portálon.
* Útmutató: a hozzáférés kezelése:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Szerepköralapú hozzáférés-vezérlés hibaelhárítási](../role-based-access-control/troubleshooting.md): kapcsolatos gyakori hibák elhárítására vonatkozó javaslatok beolvasása.
