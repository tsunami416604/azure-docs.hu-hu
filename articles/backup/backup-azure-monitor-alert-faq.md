---
title: Figyelési riasztás és jelentések – gyakori kérdések
description: Ebben a cikkben az Azure Biztonsági mentés figyelési riasztásával és az Azure Backup-jelentésekkel kapcsolatos gyakori kérdésekre adott válaszok at.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: f5be97458ba658f315c31ae34e540842b64e3ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989569"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure biztonsági mentésfigyelési riasztás – gyakori kérdések

Ez a cikk az Azure Biztonsági mentés figyelésével és jelentéskészítésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="configure-azure-backup-reports"></a>Azure Backup-jelentések konfigurálása

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Hogyan ellenőrizhetem, hogy a jelentési adatok elkezdtek-e beáramlani egy Log Analytics (LA) munkaterületre?

Keresse meg a konfigurált LA-munkaterületet, keresse meg a **Naplók** menüelemet, és futtassa a CoreAzureBackup lekérdezést | 1. Ha egy rekord visszaadása látható, az azt jelenti, hogy az adatok elkezdtek beáramlani a munkaterületre. A kezdeti adatleküldések akár 24 órát is igénybe vehetnek.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Mi az adatleküldések gyakorisága egy LA-munkaterületre?

A trezor diagnosztikai adatait a naplóanalytics munkaterületre szivattyúzza némi késéssel. Minden esemény 20–30 perccel a Recovery Services-tárolóból való leküldése után érkezik meg a Log Analytics-munkaterületre. Itt van további részleteket a lag:

* Az összes megoldás, a biztonsági mentési szolgáltatás beépített riasztások leküldéses, amint azok létrehozása. Így általában 20–30 perc elteltével jelennek meg a Log Analytics-munkaterületen.
* Az összes megoldásban az igény szerinti biztonsági mentési feladatok és a visszaállítási feladatok leküldése, amint befejeződnek.
* Az SQL biztonsági mentés kivételével az összes megoldás esetében az ütemezett biztonsági mentési feladatok leküldése, amint befejeződnek.
* Az SQL biztonsági mentés, mert a napló biztonsági mentések 15 percenként is előfordulhat, az összes befejezett ütemezett biztonsági mentési feladatok adatait, beleértve a naplókat, kötegelt és leadott 6 óránként.
* Az összes megoldásban az egyéb információk, például a biztonsági mentési elem, a házirend, a helyreállítási pontok, a tárolás és így tovább, naponta legalább egyszer leküldéses.
* A biztonsági mentési konfiguráció (például a házirend módosítása vagy a szerkesztési házirend) módosítása az összes kapcsolódó biztonsági mentési információ leküldéses indítását eredményezi.

### <a name="how-long-can-i-retain-reporting-data"></a>Mennyi ideig őrizhetem meg a jelentési adatokat?

La munkaterület létrehozása után legfeljebb 2 évig őrizheti meg az adatokat. Alapértelmezés szerint az LA-munkaterület 31 napig őrzi meg az adatokat.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>A los angelesi munkaterület konfigurálása után minden adatmegjelenik a jelentésekben?

 A diagnosztikai beállítások konfigurálása után létrehozott összes adat a LA-munkaterületre kerül, és elérhető a jelentésekben. A folyamatban lévő feladatok nem lesznek leküldéses jelentéskészítésre. Miután a feladat befejeződik vagy sikertelen, a rendszer elküldi a jelentéseknek.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Megtekinthetim a jelentéseket a tárolók és előfizetések között?

Igen, megtekintheti a jelentéseket a tárolók és előfizetések, valamint a régiók között. Az adatok egyetlen LA-munkaterületen vagy LA-munkaterületek egy csoportjában lehetnek.

### <a name="can-i-view-reports-across-tenants"></a>Megtekinthetim a jelentéseket a bérlők között?

Ha Ön [Azure Lighthouse-felhasználó,](https://azure.microsoft.com/services/azure-lighthouse/) aki delegált hozzáféréssel rendelkezik az ügyfelek előfizetéseihez vagy az LA-munkaterületekhez, a Biztonsági másolat jelentések segítségével megtekintheti az összes bérlő adatait.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Mennyi ideig tart az Azure biztonsági mentési ügynök feladat állapota tükrözni a portálon?

Az Azure Portal akár 15 min is eltarthat, hogy tükrözze az Azure biztonsági másolat ügynök feladat állapotát.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>A biztonsági mentési feladat sikertelensége után mennyi időt vesz igénybe egy riasztás küldése?

Egy riasztás az Azure biztonsági mentési hibája 20 percen belül jelenik meg.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Van olyan eset, amikor az értesítések konfigurálása esetén nem kerül e-mail küldése?

Igen. A következő esetekben a rendszer nem küld értesítéseket.

* Ha az értesítések óránként vannak konfigurálva, és egy órán belül riasztás jelenik meg és oldódik fel
* Feladat megszakításakor
* Ha egy második biztonsági mentési feladat sikertelen, mert az eredeti biztonsági mentési feladat folyamatban van

## <a name="recovery-services-vault"></a>Recovery Services-tároló

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Mennyi ideig tart az Azure biztonsági mentési ügynök feladat állapota tükrözni a portálon?

Az Azure Portal akár 15 min is eltarthat, hogy tükrözze az Azure biztonsági másolat ügynök feladat állapotát.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>A biztonsági mentési feladat sikertelensége után mennyi időt vesz igénybe egy riasztás küldése?

Egy riasztás az Azure biztonsági mentési hibája 20 percen belül jelenik meg.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Van olyan eset, amikor az értesítések konfigurálása esetén nem kerül e-mail küldése?

Igen. A következő esetekben a rendszer nem küld értesítéseket:

* Ha az értesítések óránként vannak konfigurálva, és egy órán belül riasztás jelenik meg és oldódik fel
* Feladat megszakításakor
* Ha egy második biztonsági mentési feladat sikertelen, mert az eredeti biztonsági mentési feladat folyamatban van

## <a name="next-steps"></a>További lépések

További gyakori kérdéseket is áttekinthet:

* [Gyakori kérdések](backup-azure-vm-backup-faq.md) az Azure-beli virtuális gépek biztonsági mentéséről.
* [Gyakori kérdések](backup-azure-file-folder-backup-faq.md) az Azure Backup-ügynökről
