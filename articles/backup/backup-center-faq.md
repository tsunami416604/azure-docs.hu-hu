---
title: Backup Center – gyakori kérdések
description: Ez a cikk a Backup centerrel kapcsolatos gyakori kérdésekre ad választ.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: b10a9e73e65cf12c43ce28b429a8f12e0b960a76
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995779"
---
# <a name="backup-center---frequently-asked-questions"></a>Backup Center – gyakori kérdések

## <a name="management"></a>Kezelés

### <a name="can-backup-center-be-used-across-tenants"></a>Használható a biztonsági mentési központ a bérlők között?

Igen, ha az [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) -t használja, és a különböző bérlők számára delegált hozzáférési jogosultságokkal rendelkezik, a Backup centert egyetlen üvegtáblaként használhatja a bérlők közötti biztonsági másolatok kezeléséhez.

### <a name="can-backup-center-be-used-to-manage-both-recovery-services-vaults-and-backup-vaults"></a>Használható a Backup Center a Recovery Services-tárolók és a Backup-tárolók felügyeletére is?

Igen, a Backup Center képes a [Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) - [tárolók és a Backup-tárolók](backup-vault-overview.md)kezelésére.

### <a name="is-there-a-delay-before-data-surfaces-in-backup-center"></a>Van késés a Backup Center adatfelülete előtt?

A Backup Center célja valós idejű információk biztosítása. Előfordulhat, hogy néhány másodperc telik el, amikor egy entitás egy egyedi tár képernyőjén jelenik meg, és az az idő, amikor az adott entitás megjelenik a Backup Centerben.

## <a name="configuration"></a>Konfiguráció

### <a name="do-i-need-to-configure-anything-to-see-data-in-backup-center"></a>Be kell-e állítani bármit a Backup Centerben lévő adat megtekintéséhez?

Nem. A Backup Center készen áll a dobozra. Azonban a Backup Center alatt a [biztonsági mentési jelentések](https://docs.microsoft.com/azure/backup/configure-reports) megtekintéséhez konfigurálnia kell a jelentéskészítést a tárolók számára.

### <a name="do-i-need-to-have-any-special-permissions-to-use-backup-center"></a>Szükségem van-e különleges engedélyekre a Backup Center használatához?

A Backup Centernek nincs szüksége új engedélyekre. Ha a felügyelt erőforrások számára megfelelő szintű RBAC-hozzáférésre van szüksége, használhatja a biztonsági mentési központot ezekhez az erőforrásokhoz. Ha például a biztonsági másolatok adatait szeretné megtekinteni, az **olvasónak** hozzá kell férnie a tárolóhoz. A biztonsági mentés konfigurálásához és a biztonsági mentéssel kapcsolatos egyéb műveletek végrehajtásához **biztonsági mentési közreműködő** vagy **biztonságimásolat-felelős** szerepkörre van szükség. További információ a [Azure Backup RBAC szerepköreiről](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="pricing"></a>Díjszabás

### <a name="do-i-need-to-pay-anything-extra-to-use-backup-explorer"></a>Külön kell fizetnem a Backup Explorer használatához?

Jelenleg a Backup Center használata nem jár további költségekkel (a biztonsági mentési költségektől eltekintve). Ha azonban biztonsági mentési [jelentéseket](https://docs.microsoft.com/azure/backup/configure-reports) használ a Backup Centerben [, a biztonsági](https://azure.microsoft.com/pricing/details/monitor/) mentési jelentésekhez Azure monitor naplókat kell használnia.

## <a name="next-steps"></a>Következő lépések

További gyakori kérdéseket is áttekinthet:

* [Recovery Services-tárolókkal kapcsolatos gyakori kérdések](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq)
* [Az Azure-beli virtuális gépek biztonsági mentésével kapcsolatos gyakori kérdések](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq)
