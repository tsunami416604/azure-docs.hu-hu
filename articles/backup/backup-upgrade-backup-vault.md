---
title: 'Recovery Services-tárolót az Azure Backup szolgáltatás frissítési Backup-tárolóban:'
description: Beolvasni az új funkciók, például a Resource manager-beli virtuális gépek fokozott biztonságot, a VMware virtuális gépek biztonsági mentését és a rendszerállapot biztonsági mentése a Windows-kiszolgálók biztonsági mentése Recovery Services-tároló frissítési Backup-tárolóban
services: backup
author: raynew
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: b7671271e569802311884861265a7825404c9c75
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490345"
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Mentési tároló frissítése Recovery Services-tárolóba
Ez a cikk milyen Recovery Services-tárolót biztosít áttekintést nyújt, és a Recovery Services-tárolót a frissítés utáni lépések tároló – gyakori kérdések a meglévő biztonsági másolat frissítése. Recovery Services-tárolót az Azure Resource Manager megegyezik a Backup-tároló, amely a biztonsági mentési adatok rá. Az adatokat a program általában másolja, vagy virtuális gépeken (VM), a számítási feladatok, a kiszolgálók vagy a munkaállomásokat, konfigurációs információkat e a helyszíni vagy Azure-ban.

## <a name="what-is-a-recovery-services-vault"></a>Mi az a Recovery Services-tároló?
A Recovery Services-tároló olyan online tárolóentitás az Azure-ban, amely az adatok, például a biztonsági másolatok, a helyreállítási pontok és a biztonsági mentésre vonatkozó szabályzatok tárolására szolgál. Recovery Services-tárolók segítségével különböző Azure-szolgáltatások például az Azure SQL-adatbázisok és az IaaS virtuális gépek (Linux vagy Windows) a biztonsági mentési adatok tárolásához. Helyreállítási szolgáltatások tárolók támogatása a System Center DPM, a Windows Server, az Azure Backup Server és több. A Recovery Services-tárolók leegyszerűsítik a biztonsági mentési adatok szervezését, miközben minimálisra csökkentik a munkaterhelést.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Összehasonlító Recovery Services-tárolók és a Backup-tárolók
Recovery Services-tárolók az Azure-ban, az Azure Resource Manager modellel alapulnak, mivel a Backup-tárolók az Azure Service Manager-modell alapulnak. Amikor frissít egy biztonsági mentési tárolót egy Recovery Services-tároló, a biztonsági mentési adatok sértetlenek maradnak alatt és a frissítési folyamat után. Recovery Services-tárolók adja meg a funkciók nem érhető el a Backup-tárolók, például:

- **Bővített képességet a biztonsági mentési adatainak védelme**: Recovery Services-tárolók Azure Backup felhőbeli biztonsági mentések védelméhez biztonsági képességeket biztosít. Ezek a biztonsági szolgáltatások biztosítása érdekében, hogy biztonságossá teheti a biztonsági mentések, illetve biztonságosan helyreállítható adatok felhőbeli biztonsági mentések még akkor is, ha az éles és a biztonsági mentés integritása sérül. [További információ](backup-azure-security-feature.md)

- **A hibrid informatikai környezet központi figyelését**: A Recovery Services-tárolók figyelése nem csupán a [Azure IaaS virtuális gépek](backup-azure-manage-vms.md) , hanem a [a helyszíni eszközök](backup-azure-manage-windows-server.md#manage-backup-items) egy központi portálról. [További információ](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Szerepköralapú hozzáférés-vezérlés (RBAC)**: RBAC Itt a hozzáférés részletes felügyeleti vezérlés az Azure-ban. [Az Azure különféle beépített szerepkört biztosít](../role-based-access-control/built-in-roles.md), és az Azure Backup három [beépített szerepkörök kezelése a helyreállítási pontok](backup-rbac-rs-vault.md). Recovery Services-tárolók kompatibilisek az RBAC, amely korlátozza a biztonsági mentés, és állítsa vissza a felhasználói szerepkörök meghatározott készletével való hozzáférést. [További információ](backup-rbac-rs-vault.md)

- **Az összes konfiguráció az Azure Virtual Machines védelme**: Recovery Services-tárolók Resource Manager-alapú virtuális gépet is, beleértve a felügyelt lemezek prémium szintű Managed Disks és titkosított virtuális gépek védelmét. Egy biztonsági mentési tároló frissítése Recovery Services-tároló lehetővé teszi, hogy a Service Manager-alapú virtuális gépek frissítése a Resource Manager-alapú virtuális gépeket. A tárolót a frissítés során a Service Manager-alapú virtuális gép helyreállítási pontjai, és a frissített (Resource Manager-kompatibilis) virtuális gépek védelme nem konfigurálható. [További információ](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **IaaS virtuális gépekhez azonnali helyreállítás**: Recovery Services-tárolók használata esetén visszaállíthatja fájlok és mappák egy IaaS-beli virtuális gép helyreállítása a teljes virtuális Gépet, amely lehetővé teszi a gyorsabb helyreállítás nélkül. Azonnali helyreállítás IaaS virtuális gépekhez Windows és Linux rendszerű virtuális gépeken érhető el. [További információ](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Ha egy Backup-tárolóba a MARS-ügynök korábban regisztrált 2.0.9083.0, mint a cikkekhez [töltse le a legújabb MARS-ügynök]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) verziójára a helyreállítási tár összes funkciójának előnyeit. 
> 

## <a name="managing-your-recovery-services-vaults"></a>A Recovery Services-tárolók kezelése
Az alábbi képernyőn látható egy új Recovery Services-tároló, a Backup-tárolóban az Azure Portal-ről. A frissített tároló kerül be a "Alapértelmezett-RecoveryServices-ResourceGroup-földrajzi" nevű alapértelmezett erőforráscsoport. Példa: Ha a Backup-tárolóban található volt USA nyugati RÉGIÓJA, azt kerülnek egy alapértelmezett nevű alapértelmezett-RecoveryServices-ResourceGroup-westus-RG.
> [!NOTE]
> A CPS szabvány rendelkező ügyfelek esetén az erőforráscsoport nem változik a a tár verziófrissítése, és változatlan marad, a frissítés előtt.

Az első képernyőn látható, amely megjeleníti a tároló kulcsentitások tároló irányítópultján.
![a Backup-tároló frissítése Recovery Services-tároló – példa](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

A második képernyőn látható a Súgó segíti a hivatkozások a Recovery Services-tároló használatának első lépései.

![az első lépések panelen található hivatkozások segítségével](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>A frissítés utáni lépések
Recovery Services-tároló megadását időzóna-információk támogatja a biztonsági mentési szabályzat. Miután a tároló sikeresen frissítve van, nyissa meg a biztonsági mentési házirendek tároló beállítások menüjében, és minden a tároló konfigurált szabályzatok időzóna-információk frissítése. Ez a képernyő már mikor használják, helyi időzóna szerint létrehozott szabályzatot a megadott biztonsági mentési ütemezés időpontot jeleníti meg. 

## <a name="enhanced-security"></a>Fokozott biztonság
Backup-tároló frissítése Recovery Services-tárolóba, amikor a tár biztonsági beállításait a rendszer automatikusan bekapcsol. Ha a biztonsági beállításokat, bizonyos műveletek, például a biztonsági mentések törlésével, vagy módosítása egy hozzáférési kódot egy [Azure multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md) PIN-kódot. A fokozott biztonság további információkért tekintse meg a cikket [biztonsági szolgáltatások hibrid biztonsági mentésekhez védelméhez](backup-azure-security-feature.md). Során a fokozott biztonságot be van kapcsolva, adatokat a rendszer másolatot 14 nap a helyreállítási pont adatainak törlése a tárolóból után megőrzi. Az ügyfeleknek a biztonsági adatok használatáért. Helyreállítási pontok esetében az Azure Backup agent, az Azure Backup Server és System Center Data Protection Manager biztonsági adatok megőrzésére vonatkozik. 

## <a name="gather-data-on-your-vault"></a>Adatgyűjtés a tároló
Egyszer, egy Recovery Services-tárolót a frissítés jelentések konfigurálása az Azure Backup (az IaaS virtuális gépek és a Microsoft Azure Recovery Services agent) és a jelentéseket a Power BI segítségével. Az adatgyűjtés további információkért lásd: a cikk [konfigurálása az Azure Backup-jelentések](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**A frissítési csomag befolyásolja a folyamatban lévő biztonsági másolatok?**</br>
Nem. A folyamatban lévő biztonsági másolatok továbbra is megszakítás nélkül, során és a frissítés után.

**Mire frissítési jelent ez az meglévő eszközállományomra?**</br>
A meglévő automatizálást vagy eszközöket a Resource Manager-alapú üzemi modellbe, győződjön meg arról, hogy továbbra is működni a frissítés után frissíteni kell. Tekintse meg a PowerShell parancsmagok referenciáit a [Resource Manager üzemi modell](backup-client-automation.md).

**Visszafordíthatom a frissítés után?**</br>
Nem. Visszaállítás nem támogatott, miután az erőforrások sikeresen frissítette.

**Megtekintheti a saját utólagos frissítése klasszikus tárolóba?**</br>
Nem. Nem tekinthetők meg és kezelheti a klasszikus tárolóba utólagos frissítése. Csak lesz az új Azure Portalon a tárolóban lévő összes felügyeleti műveletet használhatja.

**Miért nem látom a MARS-ügynök a frissített tároló által védett kiszolgálók?**</br>
A legújabb MARS-ügynök a MARS-ügynök a tároló által védett összes kiszolgálójára megtekintéséhez telepítenie kell. Az ügynököt a legújabb verzióját letöltheti [Itt]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**Nem jelenik meg a frissítés után a MARS-ügynök által védett kiszolgálók biztonsági mentési szabályzat**</br>
Tár biztonsági mentési szabályzat lehet, hogy elavult, és ezért nem szinkronizálható, a frissített tárolóba. Frissítse a házirendet, győződjön meg arról, továbbra is megjelenik a szabályzatok a frissített tárolóban.
Szabályzat frissítésének, ugorjon a MARS-ügynök, és a beállított biztonsági mentési szabályzat frissítése.

**Miért nem tudja frissíteni a frissítés után a biztonsági mentési szabályzatot?**</br>
Ez történik, ha a régi biztonsági mentési ügynök, és válassza ki a minimális megőrzési időtartam az engedélyezett minimális értékének kisebbnek kell lennie. Backup-tároló frissítése Recovery Services-tárolóba, amikor a tár biztonsági beállításait a rendszer automatikusan bekapcsol. Győződjön meg róla, hogy nincsenek mindig egy érvényes helyreállítási pontok száma elérhető, van néhány minimális megőrzési időszak, amelyet fenn kell tartani a biztonsági szolgáltatás megfelelően. További részletekért tekintse meg [Itt](backup-azure-security-feature.md).
Emellett szüksége az Azure Backup-ügynökök frissítése az Azure Backup szolgáltatás a legújabb funkciók előnyeinek legújabb verziójára.

**Szeretnék saját ügynök frissítése, de továbbra sem láthatók a frissítés után akár napokig szinkronizált objektumok**</br>
Ellenőrizze, ha több tárolóra ugyanarra a gépre már regisztrált. Győződjön meg arról, hogy éppen megtekintett ugyanahhoz a tárolóhoz, amely a MARS-ügynök regisztrálva van-e. A MARS-ügynök regisztrálva van, mely tároló nyissa meg a Windows beállításjegyzéket, és ellenőrizze a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config ServiceResourceName kulcs értékét a tárolóban regisztrált jelenik meg, hogy MARS-ügynök nincs. Ha nem látható, a rendszer a ServiceResourceName kulcs, fel velünk a kapcsolatot a következő értékkel: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config az erőforrás-azonosító és Gépazonosító-kulcsokat, és segítünk Önnek a probléma megoldásához.

**Miért nem látom a feladatok információkat az erőforrásoknak a frissítés után?**</br>
A biztonsági mentésekhez (MARS-ügynök és IaaS) figyelési funkciója új esetén a Backup-tároló frissítése Recovery Services-tárolóba. A monitorozási információkhoz szinkronizálását a szolgáltatással akár 12 órát vesz igénybe.

**Hogyan jelenthetem a problémát?**</br>
Ha bármely részének a tár frissítése nem sikerül, vegye figyelembe, az OperationId szerepel a hiba. Microsoft Support proaktív módon fog működni a probléma megoldásához. Forduljon támogatási szolgálatához, vagy küldjön e-mailben rsvaultupgrade@service.microsoft.com az előfizetés-azonosító, a tároló neve és OperationId. Megkíséreljük a lehető leggyorsabban a probléma megoldásához. Próbálja megismételni a műveletet nem, ha explicit módon utasította ehhez a Microsoft által.

## <a name="next-steps"></a>További lépések
Használja az alábbi cikkeket:</br>
[Készítsen biztonsági másolatot egy IaaS-beli virtuális Gépen](backup-azure-arm-vms-prepare.md)</br>
[Készítsen biztonsági másolatot az Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[A Windows Server biztonsági mentése](backup-configure-vault.md)
