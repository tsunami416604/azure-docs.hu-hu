---
title: "Az Azure biztonsági mentést a Recovery Services-tároló frissítési Backup-tárolóban |} Microsoft Docs"
description: "Az új szolgáltatások, mint az erőforrás-kezelő virtuális gépeken, a fokozott biztonság, a VMware virtuális gép biztonsági mentése és a rendszerállapot biztonsági mentése a Windows-kiszolgálók biztonsági mentését a beolvasandó Recovery Services-tároló frissítési mentési tárolóba"
services: backup
documentationcenter: 
author: trinadhk
manager: vijayts
editor: 
keyword: backup vault; upgrade vault; recovery services vault
ms.assetid: d037a8bf-49f2-4578-974a-3471d87ca278
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: trinadhk, sogup
ms.openlocfilehash: cfc2fde552b029412042474e31a1b28dd80b3021
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Recovery Services-tároló frissített mentési tároló
Ez a cikk áttekintést nyújt milyen Recovery Services-tároló, a Recovery Services-tároló és a frissítés utáni lépések tároló létező biztonsági másolathoz történő frissítéssel kapcsolatos gyakran ismételt kérdések. Recovery Services-tároló megfelel az Azure Resource Manager egy biztonsági mentési tárolót, amely a biztonsági mentési adatokat. Az adatokat a program általában másolja az adatokat, vagy a konfigurációs adatokat a virtuális gépek (VM), a munkaterhelések, a kiszolgálók vagy a munkaállomások, hogy a helyszíni vagy az Azure-ban.

## <a name="what-is-a-recovery-services-vault"></a>Mi az a Recovery Services-tároló?
A Recovery Services-tároló olyan online tárolóentitás az Azure-ban, amely az adatok, például a biztonsági másolatok, a helyreállítási pontok és a biztonsági mentésre vonatkozó szabályzatok tárolására szolgál. Recovery Services-tárolók használatával a különböző Azure-szolgáltatásokra például IaaS virtuális gépeket (Linux- vagy Windows-) és az Azure SQL-adatbázisok biztonsági mentési adatok tárolására. Helyreállítási szolgáltatások tárolók támogatása a System Center DPM, a Windows Server, Azure Backup Server, és több. A Recovery Services-tárolók leegyszerűsítik a biztonsági mentési adatok szervezését, miközben minimálisra csökkentik a munkaterhelést.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Összehasonlító Recovery Services-tárolók és a mentési tárolókat
Helyreállítási szolgáltatások tárolók Azure-ban Azure Resource Manager modelljét alapulnak, mivel az Azure Service Manager modell alapuló biztonsági mentési tárolóból. Amikor frissít egy biztonsági mentési tárolót Recovery Services-tároló, a biztonsági mentési adatok sértetlenek maradnak alatt és után a frissítési folyamat. Helyreállítási szolgáltatások tárolók funkciók nem érhető el a biztonsági mentési tárolóból, például a biztosítására:

- **Bővített képességet biztonságos biztonsági mentési adatok segítségével**: A Recovery Services-tárolók, Azure biztonsági mentési képességeket biztosít a biztonsági felhőbeli biztonsági másolatok védelme érdekében. Biztonsági funkció ellenőrizze, hogy a biztonsági másolatok biztonságos, és biztonságosan tárolt adatok helyreállításához felhőbeli biztonsági mentések akkor is, ha az éles tárhely és a biztonsági mentés kiszolgálók kerülnek veszélybe. [További információ](backup-azure-security-feature.md)

- **A hibrid informatikai környezetben központi figyelését**: A Recovery Services-tárolók, figyelheti nem csak a [Azure IaaS virtuális gépeket](backup-azure-manage-vms.md) , hanem a [a helyszíni eszközök](backup-azure-manage-windows-server.md#manage-backup-items) az egy központi portál. [További információ](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Szerepköralapú hozzáférés-vezérlés (RBAC)**: RBAC biztosít a minden részletre kiterjedő felügyeleti hozzáférés az Azure-ban. [Azure biztosít a különféle beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md), és az Azure biztonsági mentés van három [beépített szerepkörök kezelése a helyreállítási pontok](backup-rbac-rs-vault.md). Helyreállítási szolgáltatások tárolók kompatibilisek-e az RBAC, amely korlátozza a biztonsági mentés, és állítsa vissza az elérését a felhasználói szerepkörök adott csoportján. [További információ](backup-rbac-rs-vault.md)

- **Az összes konfiguráció Azure virtuális gépek védelme**: Recovery Services-tárolók a Resource Manager-alapú virtuális gépek többek között a Premium lemezek, a felügyelt lemezek és a titkosított virtuális gépek védelme. A biztonsági másolatok tárolóját frissítése a Recovery Services-tároló lehetőséget nyújt a Service Manager-alapú virtuális gépek frissítése a Resource Manager-alapú virtuális gépeket. A tároló a frissítés során a Service Manager-alapú virtuális gép helyreállítási pontok megőrzése, és konfigurálja a frissített (Resource Manager-kompatibilis) virtuális gépek védelmét. [További információ](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Infrastruktúra-szolgáltatási virtuális gépek azonnali visszaállítási**: használatával Recovery Services-tárolók, állíthatja vissza fájlokat és mappákat az infrastruktúra-szolgáltatási virtuális gép helyreállítása a teljes virtuális gép, amely lehetővé teszi a gyorsabb visszaállítás nélkül. Infrastruktúra-szolgáltatási virtuális gépek azonnali visszaállítási Windows és Linux virtuális gépek érhető el. [További információ](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Ha regisztrált egy mentési tárolóba rendszernél korábbi 2.0.9083.0, a MARS agent cikkekhez [töltse le a legfrissebb MARS agent]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) verziójára a Recovery Services-tároló összes funkciójának előnyeit. 
> 

## <a name="managing-your-recovery-services-vaults"></a>A Recovery Services-tárolók kezelése
A következő képernyőkön megjelenítése egy új Recovery Services-tároló, a biztonsági mentési tárolóból, az Azure portálon frissítve. A frissített tároló "Alapértelmezett-RecoveryServices-erőforráscsoport-földrajzi" nevű alapértelmezett erőforráscsoport megtalálható lesz. Példa: Ha a Backup-tárolóban található lett USA nyugati régiója, azt fogja kell helyeznie egy alapértelmezett alapértelmezett-RecoveryServices-erőforráscsoport-westus nevű RG.
> [!NOTE]
> A CPS Standard ügyfelek erőforráscsoport a tároló frissítés után nem változik, és változatlan marad, a frissítés előtt.

Az első képernyőn látható a tároló irányítópultot, amelyen a tároló kulcs entitásokat.
![a biztonsági másolatok tárolóját verzióról frissített Recovery Services-tároló – példa](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

A második képernyőn látható hivatkozások nyújtanak segítséget a Recovery Services-tároló használatának megkezdésében a Súgó.

![az első lépések panelen található hivatkozások segítségével](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>A frissítés utáni lépések
Recovery Services-tároló megadását időzóna-információk támogatja a biztonsági mentési házirend. Tároló sikeres frissítése után nyissa meg a biztonsági mentési házirendek tároló-beállítások menüjében, és az időzóna-adatok a tárolóban lévő konfigurált házirendek frissítése. Ez a képernyő már jeleníti meg a helyi időzónát használják, amikor egy létrehozott házirend megadott biztonsági mentési ütemezést. 

## <a name="enhanced-security"></a>Fokozott biztonság
A biztonsági másolatok tárolóját a Recovery Services-tároló történő frissítésekor a biztonsági bekapcsolja a beállításokat, hogy a tároló automatikusan. Ha a biztonsági beállítások, bizonyos műveletek, például törlés, biztonsági mentések, vagy módosítása egy hozzáférési kódot igényel egy [Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) PIN-kódot. A fokozott biztonsági további információkért lásd: a cikk [funkciókat a hibrid biztonsági másolatának](backup-azure-security-feature.md). A fokozott biztonságot be van kapcsolva, amikor adatok megőrződnek fel és 14 nap után a helyreállítási pont adatok törölve lett a tárolóból. Az ügyfelek számlázása a biztonsági adatok tárolására. Biztonsági adatok megőrzése érvényes helyreállítási pontot az Azure Backup ügynök, az Azure Backup Server és a System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>A tároló adatgyűjtés
Egyszer, frissítse a Recovery Services-tároló, jelentések konfigurálása az Azure biztonsági mentés (az infrastruktúra-szolgáltatási virtuális gépek és a Microsoft Azure Recovery Services agent), és a jelentésekhez a Power BI használatával. Az adatgyűjtés további információkért lásd: a cikk [Azure Backup konfigurálása jelentések](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**A frissítési csomag befolyásolja a folyamatban levő biztonsági másolatok?**</br>
Nem. A folyamatban levő biztonsági másolatok megszakításmentes folytatásához során, és a frissítés után.

**A meglévő eszközt használunk erre a frissítési középértéket funkciója?**</br>
A Resource Manager üzembe helyezési modellben annak érdekében, hogy az tovább működni a frissítés után frissítse a meglévő automatizációk vagy tooling. Tekintse meg a PowerShell-parancsmagok hivatkozásokat az a [Service Manager telepítési modell](backup-client-automation-classic.md) és a [Resource Manager üzembe helyezési modellben](backup-client-automation.md).

**I vissza lehet vonni a frissítés után?**</br>
Nem. Miután az erőforrások frissítése sikeresen befejeződött a rollback utasítás nem támogatott.

**Megtekintheti a post frissítési klasszikus tárolót?**</br>
Nem. Nem tekinthetők meg és kezelheti a klasszikus tároló utólagos frissítése. Csak kell tudni használni az új Azure-portálon a tárolóban lévő összes felügyeleti műveletet.

**Miért nem látom, hogy a frissített tárolóban MARS-ügynök által védett kiszolgálók?**</br>
A legújabb a MARS agent megtekintéséhez a MARS agent a tárolóban lévő által védett összes kiszolgálójára telepíteni szeretné. Az ügynök a legújabb verzióját letöltheti [Itt]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**A frissítés után a MARS agent által védett kiszolgálók biztonsági mentési házirend nem látható**</br>
A biztonsági mentési házirend vault előfordulhat, hogy elavult, és ezért nem szinkronizálható, a frissített tárolóba. Frissítse a kezelő rendszergazdákkal annak biztosítására, továbbra is megjelenik a házirendek a frissített tárolóban.
A házirend frissítése, navigáljon a MARS agent, és a beállított biztonsági mentési házirendjének frissítése.

**Miért nem lehet frissíteni a frissítés után a biztonsági mentési házirend?**</br>
Ez akkor fordul elő, amikor a régi biztonsági mentési ügynök, és válassza ki a minimális megőrzési időtartam az engedélyezett minimális értékének kisebbnek kell lennie. A biztonsági másolatok tárolóját a Recovery Services-tároló történő frissítésekor a biztonsági bekapcsolja a beállításokat, hogy a tároló automatikusan. Győződjön meg arról, hogy számos mindig érvényes helyreállítási pont érhető el, van néhány minimális megőrzési időtartam, amely szerint a biztonsági szolgáltatás fenntartásához szükséges. További részletekért lásd a [Itt](backup-azure-security-feature.md).
Emellett szükség Azure biztonsági mentés ügynököket is frissítenie kell az Azure Backup szolgáltatás legújabb funkcióit előnyei legújabb verziójára.

**Az ügynök frissítése befejeződött, de továbbra sem láthatók a frissítés után akár napokig szinkronizált objektumok**</br>
Győződjön meg arról, ha több tárolóból az egyazon számítógépen regisztrált. Győződjön meg arról, hogy éppen megtekintett a azonos tárolót, amelyben a MARS Agent regisztrálva van-e. A MARS Agent regisztrálva van, mely tároló nyissa meg a Windows beállításjegyzéket, és ellenőrizze a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config ServiceResourceName kulcs értékét a tárolóban regisztrált jelenik meg, hogy a MARS agent nincs. Ha a ServiceResourceName kulcs nem látható, a rendszer, kapcsolatba velünk a ResourceId és MachineId kulcsokat a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config értékét, és segíteni fogunk megoldani a problémát.

**Miért nem olvashatom a feladatok információkat az erőforrások a frissítés után?**</br>
(A MARS agent és IaaS) biztonsági mentés figyelése az új szolgáltatása, ha a Recovery Services-tároló frissítenie a mentési tárolóba. A figyelési adatokat a szolgáltatás-vel való szinkronizálásának 12 órát vesz igénybe.

**Hogyan jelenthetem a problémát?**</br>
Ha nem sikerül bármely részét a tároló frissítési, vegye figyelembe a OperationID azonosítójú szerepel a hiba. A probléma megoldásához a Microsoft Support proaktív módon működnek. Támogatási érheti el, vagy e-mail-nekünk az rsvaultupgrade@service.microsoft.com az előfizetés-azonosító, a tároló neve és a OperationID azonosítójú. Megpróbáljuk a lehető leggyorsabban tegye a probléma megoldásához. Próbálja megismételni a műveletet nem kivéve, ha explicit módon kifejezetten kéri a Microsoft által.

## <a name="next-steps"></a>Következő lépések
Használja a következő cikkeket:</br>
[Készítsen biztonsági másolatot az infrastruktúra-szolgáltatási virtuális gép](backup-azure-arm-vms-prepare.md)</br>
[Készítsen biztonsági másolatot az Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Készítsen biztonsági másolatot a Windows Server](backup-configure-vault.md)
