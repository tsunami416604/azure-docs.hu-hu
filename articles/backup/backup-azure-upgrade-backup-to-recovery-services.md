---
title: "Frissítse a biztonsági másolatok tárolóját a Recovery Services-tároló (előzetes verzió) |} Microsoft Docs"
description: "Útmutatás és támogatási információk frissítése a Recovery Services-tároló az Azure Backup-tárolóban."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/03/2017
ms.author: sogup;markgal;arunak
ms.openlocfilehash: 7347301cd34979df9b3acc59f623ab346dbe41f6
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Frissítse a biztonsági másolatok tárolóját a Recovery Services-tároló

Ez a cikk ismerteti, hogyan frissítheti a biztonsági másolatok tárolóját a Recovery Services-tároló. A frissítési folyamat nem befolyásolja a futó biztonsági mentési feladat, és a biztonsági mentési adatok nem vesztek el. A biztonsági másolatok tárolóját frissítése a Recovery Services-tároló elsődleges ok miatt:
- A mentési tároló összes funkciójának Recovery Services-tároló maradnak.
- Helyreállítási szolgáltatások tárolók mentési tárolókban, beleértve a több szolgáltatással rendelkezik: nagyobb biztonságot, integrált figyelés, gyorsabb visszaállítás és elemszintű visszaállítások.
- Kezelheti a biztonsági másolati elemei egy továbbfejlesztett, egyszerűsített portálról.
- Új funkciók csak a Recovery Services-tárolók vonatkoznak.

## <a name="impact-to-operations-during-upgrade"></a>Frissítés során műveletekre gyakorolt hatás

Amikor frissíti a biztonsági másolatok tárolóját a Recovery Services-tároló, akkor ennek nincs hatása a adatok vezérlősík műveletekhez. Minden biztonsági mentési feladatok folytatása a szokásos módon, és az aktív helyreállítási feladatokat megszakítás nélkül tovább. A frissítés során felügyeleti műveletek fel Önnek egy rövid állásidőt, és nem lehet új elemek védelme vagy ad hoc biztonsági mentés feladatok létrehozása. Infrastruktúra-szolgáltatási virtuális gépek feladatok visszaállításakor ne futtassa a frissítés során. A tároló frissítés befejezéséhez egy óra alatt. Miután befejeződött, a Recovery Services-tároló helyettesíti a mentési tárolóba.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Az automatizálás és a frissítés után az eszköz módosításai

Az infrastruktúra előkészítése a tároló frissítési, során a meglévő automatizációk vagy tooling annak érdekében, hogy az tovább működni a frissítés után kell frissíteni.
Tekintse meg a PowerShell-parancsmagok hivatkozásokat az a [Service Manager telepítési modell](backup-client-automation-classic.md) és a [Resource Manager üzembe helyezési modellben](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Frissítés előtti teendők

Ellenőrizze a következő problémák, a biztonsági mentési tárolók szolgáltatásbeli tárolóhoz való frissítés előtt.

- **Minimális verziója**: a tároló frissítési, győződjön meg arról, a Microsoft Azure Recovery Services (MARS) ügynök legalább 2.0.9083.0 verziója. Ha a MARS agent régebbi, mint 2.0.9083.0, frissítse az ügynököt a frissítési folyamat megkezdése előtt.
- **Számlázási modellt példány-alapú**: helyreállítási szolgáltatás tárolók csak a példány-alapú számlázási modellt támogatja. Ha egy mentési tárolót, amely a régebbi Storage-alapú számlázási modellt használja, alakítsa át a számlázási modellt frissítés során.
- **Nincs folyamatban biztonsági mentési konfigurációhoz műveletek**: a frissítés során a felügyeleti vezérlősík elérése történik. Minden felügyeleti vezérlősík műveletek végrehajtása, és indítsa el a frissítést.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>PowerShell-parancsfájlok használata a tárolók frissítése

PowerShell-parancsfájlok használatával frissítse a biztonsági mentési tárolók Recovery Services-tárolók. Ellenőrizze, hogy rendelkezik-e a szükséges PowerShell-összetevők a tároló-frissítés indításához. Mentési tárolókban PowerShell-parancsfájlok nem működnek a Recovery Services-tárolók. A tárolók frissítése a környezet előkészítése:

1. Telepítéséhez vagy verziófrissítéséhez [Windows Management Framework (WMF) verzióra 5](https://www.microsoft.com/download/details.aspx?id=50395) vagy újabb.
2. [Telepítse az Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Töltse le a [PowerShell-parancsfájl](https://aka.ms/vaultupgradescript2) a tároló frissítésére.

### <a name="run-the-powershell-script"></a>A PowerShell-parancsprogram futtatása

A következő parancsfájl használatával frissíti a tárolók. Az alábbi mintaparancsfájl magyarázatokat paraméter tartozik.

RecoveryServicesVaultUpgrade-1.0.2.ps1 **- SubscriptionID** `<subscriptionID>` **- VaultName** `<vaultname>` **-hely** `<location>` **- ResourceType** `BackupVault` **- TargetResourceGroupName**`<rgname>`

**A SubscriptionID** -előfizetés azonosítója számának a tárolóra, a frissítés alatt áll.<br/>
**VaultName** -nevét a mentési tárolóval, hogy a frissítés alatt áll.<br/>
**Hely** -frissítés alatt a tároló helyét.<br/>
**A ResourceType** -BackupVault használja.<br/>
**TargetResourceGroupName** – mivel frissíti a tároló egy Resource Manager-alapú telepítéshez, adja meg egy erőforráscsoportot. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy új nevet megadásával. Ha az erőforráscsoport neve helytelenül, létrehozhat egy új erőforráscsoportot. További tudnivalók az erőforráscsoportokról, olvassa el ezt [tudnivalók az erőforráscsoportokról áttekintése](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Erőforráscsoport-nevek vannak. Ügyeljen arra, hogy kövesse az útmutatást; Ehhez hibát okozhat tároló frissítése sikertelen.
>
>**Az Azure Amerikai Egyesült államokbeli kormányzati** ügyfelek kell beállítani az "AzureUSGovernment" környezetben a parancsprogram futtatása közben.
>**Az Azure Kína** ügyfelek kell beállítani az "AzureChinaCloud" környezetben a parancsprogram futtatása közben.

A következő kódrészletet példája milyen a PowerShell-parancsot kell hasonlítania:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

A parancsfájl paraméterek nélkül is futtathatja, és a rendszer felkéri az összes szükséges paraméterek megadására, bemeneti adatokat.

A PowerShell-parancsfájlt a hitelesítő adatok megadását kéri. Adja meg a hitelesítő adatok kétszer: egy alkalommal a Service Manager-fiókot, és az erőforrás-kezelő fiók még egyszer.

### <a name="pre-requisites-checking"></a>Előfeltételek ellenőrzése
Azure hitelesítő adatait adta meg, miután a Azure ellenőrzi, hogy a környezet megfelel-e a következő előfeltételek teljesülését:

- **Minimális verziója** -mentési tárolókban a Recovery Services-tárolók frissítéséhez a MARS agent legalább 2.0.9083.0 verziója. Ha regisztrált egy mentési tárolóba rendszernél korábbi 2.0.9083.0 ügynök elemeket tartalmaz, az előfeltétel-ellenőrzés sikertelen lesz. Ha az előfeltétel-ellenőrzés sikertelen, frissítse az ügynököt, majd próbálja meg újra frissíteni a tárolóban. Az ügynök a legújabb verzióját letöltheti [http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **A folyamatban lévő konfigurációs feladatok**: Ha valaki konfigurálását végzi a biztonsági másolatok tárolóját, állítsa be a frissíteni kívánt feladatot, vagy egy elem regisztrálása, az előfeltétel-ellenőrzés sikertelen lesz. A konfigurálás, vagy az elem regisztrálásának befejezése, és indítsa el a tároló frissítési folyamat.
- **Storage-alapú számlázási modellt**: Recovery Services-tárolók a példány-alapú számlázási modellt támogatja. Ha a tároló frissítést futtat egy biztonsági mentési tárolót, hogy a tároló-alapú számlázási modellt használ, a számlázási modellt és a tároló frissítésére kéri. Ellenkező esetben frissítheti a számlázási modellt először, és futtassa a tároló frissítés.
- Azonosítsa a Recovery Services-tárolónak egy erőforráscsoportot. A Resource Manager üzembe helyezési funkcióival kihasználását, a Recovery Services-tároló erőforráscsoportban kell helyezni. Ha nem tudja, melyik erőforráscsoport kíván használni, adjon meg egy nevet, és a frissítési folyamat meg az erőforráscsoportot hoz létre. A frissítési folyamat is hozzárendeli a tárolót az új erőforráscsoport.

Ha a frissítési folyamat befejeződik, a szükséges előfeltételek ellenőrzése, a folyamat kéri indítsa el a tároló frissítését. Miután meggyőződött róla, a frissítési folyamat általában befejezéséhez körülbelül 15 – 20 perc, a tároló méretétől függően. Ha egy nagy tárolóban, frissítése akár 90 percig is tarthat.

## <a name="managing-your-recovery-services-vaults"></a>A Recovery Services-tárolók kezelése

A következő képernyőkön megjelenítése egy új Recovery Services-tároló, a biztonsági mentési tárolóból, az Azure portálon frissítve. Az első képernyőn látható a tároló irányítópultot, amelyen a tároló kulcs entitásokat.

![a biztonsági másolatok tárolóját verzióról frissített Recovery Services-tároló – példa](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

A második képernyőn látható hivatkozások nyújtanak segítséget a Recovery Services-tároló használatának megkezdésében a Súgó.

![az első lépések panelen található hivatkozások segítségével](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>A frissítés utáni lépések
Recovery Services-tároló megadását időzóna-információk támogatja a biztonsági mentési házirend. Tároló sikeres frissítése után nyissa meg a biztonsági mentési házirendek tároló-beállítások menüjében, és az időzóna-adatok a tárolóban lévő konfigurált házirendek frissítése. Ez a képernyő már jeleníti meg a helyi időzónát használják, amikor egy létrehozott házirend megadott biztonsági mentési ütemezést. 

## <a name="enhanced-security"></a>Fokozott biztonság

A biztonsági másolatok tárolóját a Recovery Services-tároló történő frissítésekor a biztonsági bekapcsolja a beállításokat, hogy a tároló automatikusan. Ha a biztonsági beállítások, bizonyos műveletek, például törlés, biztonsági mentések, vagy módosítása egy hozzáférési kódot igényel egy [Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) PIN-kódot. A fokozott biztonsági további információkért lásd: a cikk [funkciókat a hibrid biztonsági másolatának](backup-azure-security-feature.md). 

A fokozott biztonságot be van kapcsolva, amikor adatok megőrződnek fel és 14 nap után a helyreállítási pont adatok törölve lett a tárolóból. Az ügyfelek számlázása a biztonsági adatok tárolására. Biztonsági adatok megőrzése érvényes helyreállítási pontot az Azure Backup ügynök, az Azure Backup Server és a System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>A tároló adatgyűjtés

Miután a Recovery Services-tároló frissít, jelentések konfigurálása az Azure biztonsági mentés (az infrastruktúra-szolgáltatási virtuális gépeket és a Microsoft Azure Recovery Services (MARS)), és a jelentésekhez a Power BI használatával. Az adatgyűjtés további információkért lásd: a cikk [Azure Backup konfigurálása jelentések](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**A frissítési csomag befolyásolja a folyamatban levő biztonsági másolatok?**</br>
Nem. A folyamatban levő biztonsági másolatok megszakításmentes folytatásához során, és a frissítés után.

**Ha szeretnék frissítéséről hamarosan nem tervezi, mi történik a tárolók?**</br>
Mivel az összes új szolgáltatás csak a Recovery Services-tárolók vonatkoznak, javasoljuk, hogy a tároló frissítésére. A Microsoft felé fog érvényteleníthető a klasszikus portálon. 2017. szeptember 1., kezdési Microsoft megkezdődik a mentési tárolók automatikus frissítése a Recovery Services-tárolók. A Microsoft által 2017. November 30. a frissítési folyamat befejeződik. A tároló frissíthetők automatikusan az között bármikor. A Microsoft azt javasolja, hogy minél hamarabb frissítse a tárolóban.

**A meglévő eszközt használunk erre a frissítési középértéket funkciója?**</br>
Frissítse az eszközt használunk erre a Resource Manager üzembe helyezési modellben. Tárolók jelentek meg a helyreállítási szolgáltatások használata a Resource Manager üzembe helyezési modellben. A Resource Manager üzembe helyezési modellel tervezéséről és a különbség a tárolók elszámolása fontos. 

**A frissítés közben nem sok állásidő?**</br>
Az olyan erőforrások száma, amelyek frissítik a függ. Kisebb környezetekhez (védett példányok néhány több tíz) a teljes frissítés kevesebb, mint 20 percet kell végrehajtani. Nagyobb telepítések esetén elméletileg legfeljebb egy óra.

**I vissza lehet vonni a frissítés után?**</br>
Nem. Miután az erőforrások frissítése sikeresen befejeződött a rollback utasítás nem támogatott.

**Az előfizetés vagy az erőforrások megjelenítéséhez, ha fontosságúak képes a frissítés is ellenőrzi?**</br>
Igen. A frissítés első lépése ellenőrzi, hogy az erőforrások frissítés. Abban az esetben, ha a szükséges előfeltételek ellenőrzése sikertelen, üzeneteket fogadni a frissítés nem hajtható végre okok miatt.

**Milyen engedélyekkel kell rendelkeznie elindítható a tároló frissítés?**</br>
A tároló frissítéshez kell hozzáadni, a klasszikus Azure portálon az előfizetés társadminisztrátoraként. Ez elengedhetetlen, még akkor is, ha már fel van sorolva az Azure portálon tulajdonosa. Próbálja meg hozzáadni egy az előfizetés társadminisztrátoraként a klasszikus Azure portálon, ha szeretné tudni, hogy-e az előfizetés társadminisztrátoraként. Ha nem tud társadminisztrátorának hozzáadni, lépjen kapcsolatba a szolgáltatás-rendszergazdai vagy társadminisztrátori az előfizetés, akik felvehesse Önt társadminisztrátoraként.

**Frissíthetem a CSP-alapú biztonsági mentés tárolójának?**</br>
Nem. Mentési tárolók CSP-alapú jelenleg nem frissíthető. A Microsoft támogatni fogják a CSP-alapú biztonsági mentési tárolók a következő kiadásokban az frissítése.

**Megtekintheti a post frissítési klasszikus tárolót?**</br>
Nem. Nem tekinthetők meg és kezelheti a klasszikus tároló utólagos frissítése. Csak kell tudni használni az új Azure-portálon a tárolóban lévő összes felügyeleti műveletet.

**A frissítés nem sikerült, de a gépet, az ügynök igénylő tárolt már frissíti, nem létezik. Mi a teendő ebben az esetben?**</br>
Ha a tároló használatára van szüksége, a hosszú távú megőrzési, majd a gépet nem lesznek a tároló frissítésére. A későbbi kiadásokban azt támogatni fogják a frissítés ilyen egy tárolót.
Ha a számítógép biztonsági másolatait tárolni többé nem kell, majd adja regisztrációt megszüntetni ezen a számítógépen a tárolóból, majd próbálja újra a frissítést.

**Miért nem olvashatom a feladatok információkat az erőforrások a frissítés után?**</br>
(A MARS agent és IaaS) biztonsági mentés figyelése az új szolgáltatása, ha a Recovery Services-tároló frissítenie a mentési tárolóba. A figyelési adatokat a szolgáltatás-vel való szinkronizálásának 12 órát vesz igénybe.

**Hogyan jelenthetem a problémát?**</br>
Ha nem sikerül bármely részét a tároló frissítési, vegye figyelembe a OperationID azonosítójú szerepel a hiba. A probléma megoldásához a Microsoft Support proaktív módon működnek. Támogatási érheti el, vagy e-mail-nekünk az rsvaultupgrade@service.microsoft.com az előfizetés-azonosító, a tároló neve és a OperationID azonosítójú. Megpróbáljuk a lehető leggyorsabban tegye a probléma megoldásához. Próbálja megismételni a műveletet nem kivéve, ha explicit módon kifejezetten kéri a Microsoft által.


## <a name="next-steps"></a>Következő lépések
Használja a következő cikkhez:</br>
[Készítsen biztonsági másolatot az infrastruktúra-szolgáltatási virtuális gép](backup-azure-arm-vms-prepare.md)</br>
[Készítsen biztonsági másolatot az Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[A Windows Server biztonsági mentése](backup-configure-vault.md).
