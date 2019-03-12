---
title: Egy Backup tároló frissítése Recovery Services-tároló
description: Útmutató és támogatási információk az Azure Backup-tároló frissítése Recovery Services-tárolóba.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: sogup
ms.openlocfilehash: b4ecebc6bef7f49a23455c7a85f25680df087a95
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530756"
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Egy Backup tároló frissítése Recovery Services-tároló

Ez a cikk ismerteti a Backup-tároló frissítése Recovery Services-tárolóba. A frissítési folyamat nem érinti a biztonsági mentési feladatok, és a biztonsági mentési adatok nem elveszett. A Backup-tároló frissítése Recovery Services-tárolót a fő oka:
- Backup-tároló összes funkciójának megmaradnak a Recovery Services-tárolóba.
- Helyreállítási tárak rendelkeznek, mint a biztonsági mentési tároló, beleértve a további funkciók: nagyobb biztonságot, integrált figyelés, a gyorsabb visszaállítás, és elemszintű helyreállítást.
- Biztonsági másolati elemek egy továbbfejlesztett, egyszerűsített Portalon lehet kezelni.
- Új funkciók csak a Recovery Services-tárolókra vonatkoznak.

## <a name="impact-to-operations-during-upgrade"></a>A frissítés során a műveletek hatással

Backup tároló frissítése Recovery Services-tárolóra, ha ez nincs hatással az adatsík műveletekhez. Az összes biztonsági mentési feladatok továbbra is a szokásos módon, és minden aktív helyreállítási feladatok továbbra is megszakítás nélkül. A frissítés során felügyeleti műveletek egy rövid állásidőt merülnek fel, és nem lehet új elemek védelme vagy ad hoc ad hoc biztonsági mentések feladatok létrehozása. IaaS virtuális gépekhez a visszaállítási feladat nem futnak a frissítés során. A tár verziófrissítése vesz igénybe egy óra alatt befejeződik. Ha elkészült, a Recovery Services-tárolót a Backup-tároló váltja fel.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Az automatizálás és az eszköz alkalmazásra történő verziófrissítés utáni módosításai

A tár verziófrissítése, az infrastruktúra előkészítése során a meglévő automatizálást vagy eszközöket annak biztosítására, hogy továbbra is működni a frissítés után kell frissíteni.
Tekintse meg a PowerShell parancsmagok referenciáit a [Resource Manager üzemi modell](backup-client-automation.md).


## <a name="before-you-upgrade"></a>A frissítés előtt

Ellenőrizze a következő problémák, a Backup-tárolók frissítése Recovery Service-tárolókra előtt.

- **Ügynök verziója**: A tár frissítése, ellenőrizze, hogy a Microsoft Azure Recovery Services-(MARS) ügynök legalább egy 2.0.9083.0 verzió. Ha a MARS-ügynök régebbi, mint 2.0.9083.0, frissítse az ügynököt a frissítési folyamat megkezdése előtt.
- **Példány-alapú számlázási modell**: Csak a Recovery Service-tárolók támogatják a példány-alapú számlázási modell. Ha rendelkezik egy biztonsági mentési tárat. a régebbi Storage-alapú számlázási modellt használ, konvertálja a számlázási modellt a frissítés során.
- **Nincsenek folyamatban lévő biztonsági mentési konfiguráció műveletek**: A frissítés során, a felügyeleti sík hozzáférés korlátozva. Végezze el az összes felügyeleti sík műveleteket, és indítsa el a frissítést.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>PowerShell-szkriptek használata a tárolók frissítése

PowerShell-parancsfájlok segítségével a Backup-tárolók frissítése Recovery Services-tárolóra. Ellenőrizze, hogy rendelkezik-e a szükséges PowerShell-összetevők a tár verziófrissítése aktiválásához. PowerShell-parancsprogramok a Backup-tárolók nem működnek a Recovery Services-tárolók. Frissítse a tárolók a környezet előkészítése:

1. Telepítse vagy frissítse [Windows Management Framework (WMF) 5-ös verzió,](https://www.microsoft.com/download/details.aspx?id=50395) vagy újabb.
2. [Az Azure PowerShell MSI telepítése](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Töltse le a [PowerShell-parancsprogram](https://aka.ms/vaultupgradescript2) frissítése a tárolók.

### <a name="run-the-powershell-script"></a>A PowerShell-parancsprogram futtatása

A következő parancsfájl használatával frissítse a tárolók. Az alábbi parancsprogram rendelkezik a paraméterek találja.

RecoveryServicesVaultUpgrade-1.0.2.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`

**SubscriptionID** – a tároló, amely frissítés alatt áll előfizetési azonosító száma.<br/>
**VaultName** -nevét a Backup-tároló, amely frissítés alatt áll.<br/>
**Hely** -frissítés alatt áll a tároló helyét.<br/>
**Erőforrástípus** – biztonsági mentési tár használata.<br/>
**TargetResourceGroupName** – mivel a tárolóhoz szeretne frissíteni egy Resource Manager-alapú üzembe helyezési adjon meg egy erőforráscsoportot. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egyet, új név megadásával. Ha helytelenül az erőforráscsoport nevét, létrehozhat egy új erőforráscsoportot. Az erőforráscsoportokkal kapcsolatos további tudnivalókért olvassa el ezt [erőforráscsoportok áttekintését](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Erőforráscsoport neve megkötések rendelkezik. Ügyeljen arra, hogy kövessék; Ezt okozhatja a tároló frissítése sikertelen.
>
>**Az Azure US Government** ügyfeleknek be kell állítania a környezet "AzureUSGovernment" a parancsprogram futtatása közben.
>**Az Azure China** ügyfeleknek be kell állítania a környezet "AzureChinaCloud" a parancsprogram futtatása közben.

A következő kódrészletet a példából láthatja, hogy milyen a PowerShell-paranccsal hasonlóan kell kinéznie:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

A parancsfájl paraméterek nélkül is futtathatja, és bemenetei között minden szükséges paraméterek megadására kéri.

A PowerShell-parancsfájlt a hitelesítő adatok megadását kéri. Kétszer adja meg hitelesítő adatait: a Service Manager-fiókot, és a Resource Manager-fiók másodszor egyszer.

### <a name="pre-requisites-checking"></a>Előfeltételek ellenőrzése
Adta meg Azure hitelesítő adatait, ha a Azure ellenőrzi, hogy a környezet megfelel-e a következő előfeltételeknek:

- **Ügynök verziója** – Backup-tárolók frissítése Recovery Services-tárolóra van szükség a MARS-ügynök nem lehet rövidebb 2.0.9083.0 verzió. Ha regisztrált egy Backup-tárolóba rendszernél korábbi 2.0.9083.0 ügynök elemeket tartalmaz, az előfeltétel-ellenőrzés sikertelen lesz. Ha az előfeltétel-ellenőrzés sikertelen, frissítse az ügynököt, és próbálja meg újra frissíteni a tárolóban. Az ügynököt a legújabb verzióját letöltheti [ https://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe ](https://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **A folyamatban lévő konfigurációs feladatok**: Ha valaki frissítendő konfigurálása a Backup-tároló feladat beállítása, vagy egy elemet felvette, az előfeltétel-ellenőrzés sikertelen lesz. A konfigurálás befejezéséhez, illetve befejezéséhez regisztrálja az elem, majd indítsa el a tároló frissítési folyamat.
- **Storage-alapú számlázási modell**: Recovery Services-tárolók támogatják a példány-alapú számlázási modell. Ha futtatja a tár verziófrissítése Backup-tároló, amely a Storage-alapú számlázási modellt használ, a számlázási modellt, valamint a tároló frissítésére kéri. Ellenkező esetben frissítheti a számlázási modellt, majd futtassa a tár frissítését.
- Egy erőforráscsoport, a Recovery Services-tároló azonosítása. Kihasználhatja a Resource Manager üzembe helyezési szolgáltatások, egy Recovery Services-tárolót kell helyezzen egy erőforráscsoportban. Ha nem tudja használni, adjon meg egy nevet, hogy melyik erőforráscsoportot, és a frissítési folyamat az erőforráscsoport jön létre az Ön számára. A frissítési folyamat is társítja a tárolót az új erőforráscsoportot.

Ha a frissítési folyamat befejezte az előfeltételek ellenőrzését, a folyamat kéri indítsa el a tár frissítését. Miután megerősítette, a frissítési folyamat végrehajtásához, a tároló méretétől függően általában tart körülbelül 15 – 20 percet. Ha egy nagy méretű tároló, akár 90 percet frissítése eltarthat.

## <a name="managing-your-recovery-services-vaults"></a>A Recovery Services-tárolók kezelése

Az alábbi képernyőn látható egy új Recovery Services-tároló, a Backup-tárolóban az Azure Portal-ről. Az első képernyőn látható, amely megjeleníti a tároló kulcsentitások tároló irányítópultján.

![a Backup-tároló frissítése Recovery Services-tároló – példa](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

A második képernyőn látható a Súgó segíti a hivatkozások a Recovery Services-tároló használatának első lépései.

![az első lépések panelen található hivatkozások segítségével](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>A frissítés utáni lépések
Recovery Services-tároló megadását időzóna-információk támogatja a biztonsági mentési szabályzat. Miután a tároló sikeresen frissítve van, nyissa meg a biztonsági mentési házirendek tároló beállítások menüjében, és minden a tároló konfigurált szabályzatok időzóna-információk frissítése. Ez a képernyő már mikor használják, helyi időzóna szerint létrehozott szabályzatot a megadott biztonsági mentési ütemezés időpontot jeleníti meg.

## <a name="enhanced-security"></a>Fokozott biztonság

Backup-tároló frissítése Recovery Services-tárolóba, amikor a tár biztonsági beállításait a rendszer automatikusan bekapcsol. Ha a biztonsági beállításokat, bizonyos műveletek, például a biztonsági mentések törlésével, vagy módosítása egy hozzáférési kódot egy [Azure multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md) PIN-kódot. A fokozott biztonság további információkért tekintse meg a cikket [biztonsági szolgáltatások hibrid biztonsági mentésekhez védelméhez](backup-azure-security-feature.md).

Során a fokozott biztonságot be van kapcsolva, adatokat a rendszer másolatot 14 nap a helyreállítási pont adatainak törlése a tárolóból után megőrzi. Az ügyfeleknek a biztonsági adatok használatáért. Helyreállítási pontok esetében az Azure Backup agent, az Azure Backup Server és System Center Data Protection Manager biztonsági adatok megőrzésére vonatkozik.

## <a name="gather-data-on-your-vault"></a>Adatgyűjtés a tároló

Miután frissít egy Recovery Services-tárolót, a jelentések konfigurálása az Azure Backup (az IaaS virtuális gépek és a Microsoft Azure Recovery Services-(MARS)), és használata a Power bi-ban a jelentések eléréséhez. Az adatgyűjtés további információkért lásd: a cikk [konfigurálása az Azure Backup-jelentések](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-the-upgrade-plan-affect-my-ongoing-backups"></a>A frissítési csomag befolyásolja a folyamatban lévő biztonsági másolatok?
Nem. A folyamatban lévő biztonsági másolatok továbbra is megszakítás nélkül, során és a frissítés után.

### <a name="if-i-dont-plan-on-upgrading-soon-what-happens-to-my-vaults"></a>Ha nem tervezek frissíteni leggyorsabban, mi történik a tárolóra?
Mivel az összes új funkciót csak a Recovery Services-tárolókra vonatkoznak, nyomatékosan javasoljuk, hogy a tárolók frissítése. 2017. szeptember 1-én kezdve a Microsoft elkezdi az automatikus frissítése a backup-tárolók a Recovery Services-tárolókra. November után 30,2017, már nem Backup-tároló hozható létre PowerShell használatával. A tároló automatikusan frissíthetők a között bármikor. A Microsoft javasolja, hogy minél hamarabb frissítse a tárolót.

### <a name="what-does-this-upgrade-mean-for-my-existing-tooling"></a>Mire frissítési jelent ez az meglévő eszközállományomra?
Frissítse az eszközállomány a Resource Manager-alapú üzemi modellbe. A Recovery Services-tárolók létrehozott használata a Resource Manager-alapú üzemi modellben. A Resource Manager üzemi modell tervezési és a különbség a tárolókban elszámolása fontos.

### <a name="during-the-upgrade-is-there-much-downtime"></a>A frissítés során van mennyi állásidő?
Attól függ, az erőforrások száma, frissít. Kisebb környezetekhez (néhány tucat védett példányok) a teljes frissítés kevesebb mint 20 percet vesz igénybe. Nagyobb telepítések esetén ez egy legfeljebb egy órát vesz igénybe.

### <a name="can-i-roll-back-after-upgrading"></a>Visszafordíthatom a frissítés után?
Nem. Visszaállítás nem támogatott, miután az erőforrások sikeresen frissítette.

### <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-upgrade"></a>Az előfizetésem vagy megtekintéséhez, ha azok már képes a frissítés is ellenőrizni?
Igen. Az első lépés a frissítést azt ellenőrzi, hogy az erőforrások képesek a frissítés. Abban az esetben, ha az előfeltételeket az érvényesítés meghiúsul, értesítést kap a frissítés nem hajtható végre okok miatt.

### <a name="can-i-upgrade-my-csp-based-backup-vault"></a>Frissíthetem a CSP-alapú biztonsági mentési tár?
Nem. Jelenleg nem tudja frissíteni a backup-tárolók CSP-alapú. Hozzáadjuk a CSP-alapú biztonsági mentési tárak a következő kiadásokban frissítésének támogatását.

### <a name="can-i-view-my-classic-vault-post-upgrade"></a>Megtekintheti a saját utólagos frissítése klasszikus tárolóba?
Nem. Nem tekinthetők meg és kezelheti a klasszikus tárolóba utólagos frissítése. Csak lesz az új Azure Portalon a tárolóban lévő összes felügyeleti műveletet használhatja.

### <a name="my-upgrade-failed-but-the-machine-that-held-the-agent-requiring-updating-doesnt-exist-anymore-what-do-i-do-in-such-a-case"></a>A frissítés sikertelen volt, de a gép, amely az ügynök igénylő tárolt már frissíti, nem létezik. Mi a teendő ebben az esetben?
Ha szeretné használni a tárolóban, a biztonsági másolatok hosszú távú adatmegőrzés, majd a gépet nem lehet frissíteni a tárolóban. A későbbi kiadásokban hozzáadjuk az ilyen egy tárolót a frissítés támogatása.
Ha nem kell többé a számítógép biztonsági másolatok tárolására, majd adjon regisztrációjának törlése a tárolóból a gép és próbálja újra a frissítést.

### <a name="why-cant-i-see-the-jobs-information-for-my-resources-after-upgrade"></a>Miért nem látom a feladatok információkat az erőforrásoknak a frissítés után?
A biztonsági mentésekhez (MARS-ügynök és IaaS) figyelési funkciója új esetén a Backup-tároló frissítése Recovery Services-tárolóba. A monitorozási információkhoz szinkronizálását a szolgáltatással akár 12 órát vesz igénybe.

### <a name="how-do-i-report-an-issue"></a>Hogyan jelenthetem be a hibákat?
Ha bármely részének a tár frissítése nem sikerül, vegye figyelembe, az OperationId szerepel a hiba. Microsoft Support proaktív módon fog működni a probléma megoldásához. Forduljon támogatási szolgálatához, vagy küldjön e-mailben rsvaultupgrade@service.microsoft.com az előfizetés-azonosító, a tároló neve és OperationId. Megkíséreljük a lehető leggyorsabban a probléma megoldásához. Próbálja megismételni a műveletet nem, ha explicit módon utasította ehhez a Microsoft által.


## <a name="next-steps"></a>További lépések
Használja a következő cikket:</br>
[Készítsen biztonsági másolatot egy IaaS-beli virtuális Gépen](backup-azure-arm-vms-prepare.md)</br>
[Készítsen biztonsági másolatot az Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[A Windows Server biztonsági mentése](backup-configure-vault.md).
