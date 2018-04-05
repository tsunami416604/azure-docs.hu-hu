---
title: A DPM-munkaterhelések biztonsági mentése az Azure portál használatával |} Microsoft Docs
description: Az Azure Backup szolgáltatás használatával a DPM-kiszolgálók biztonsági másolatának bemutatása
services: backup
documentationcenter: ''
author: adigan
manager: nkolli
editor: ''
keywords: A System Center Data Protection Manager, a data protection manager, a dpm biztonsági mentése
ms.assetid: c8c322cf-f5eb-422c-a34c-04a4801bfec7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: adigan;giridham;jimpark;markgal;trinadhk
ms.openlocfilehash: 05917705264965afda873fb32a28b70e4da87aa3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Számítási feladatok Azure-ba való biztonsági mentésének előkészítése a DPM-mel
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Ez a cikk bemutatja azokat a Microsoft Azure Backup segítségével a System Center Data Protection Manager (DPM) kiszolgálók és munkaterhelések védelme érdekében. Az olvasásával lesz tisztában:

* Hogyan működik a Azure DPM-kiszolgáló biztonsági mentése
* Az Előfeltételek a zökkenőmentes biztonsági mentés élmény eléréséhez
* A szokásos hibákat észlelt, és azokat kezelése
* Támogatott esetek

> [!NOTE]
> Azure az erőforrások létrehozására és kezelésére két üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti, a telepített, a Resource Manager modellt használó virtuális gépek visszaállítására.
>
>

[A System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) készít biztonsági másolatot fájl-és alkalmazásadatok. További információ a támogatott munkaterhelésekkel található [Itt](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix). Adatok biztonsági mentése a DPM a lemezen, szalagon tárolja, vagy biztonsági mentése az Azure-ba, a Microsoft Azure Backup szolgáltatásnál. A DPM a következőképpen együttműködik az Azure Backup szolgáltatással:

* **Fizikai kiszolgáló vagy a helyszíni virtuális gépként telepített DPM** – Ha a DPM fizikai kiszolgálóként vagy helyszíni Hyper-V virtuális gépként is adatok biztonsági mentését a Recovery Services-tároló lemez és szalag mellett a rendszer biztonsági mentés.
* **Az Azure virtuális gépként telepített DPM** – a System Center 2012 R2 Update 3, a DPM telepíthető Azure virtuális géphez. Ha a DPM Azure virtuális gépként van telepítve, adatokról biztonsági másolatot a virtuális géphez csatolt Azure lemezekre, vagy az adattárolási kiszervezése biztonsági mentést a Recovery Services-tároló által.

## <a name="why-back-up-dpm-to-azure"></a>Miért érdemes biztonsági másolatot készíteni a DPM az Azure-bA?
A biztonsági másolatot a DPM-kiszolgálók Azure az üzleti előnyei a következők:

* A helyszíni DPM-telepítés használata Azure szalagra hosszú távú központi telepítés helyett.
* Egy Azure-ban a DPM telepítéséhez, a lemezt az Azure storage-kiszervezés. Régebbi adatok tárolását a Recovery Services-tároló lehetővé teszi az üzleti növelheti a lemezre új adatokat tárol.

## <a name="prerequisites"></a>Előfeltételek
Készítse elő az Azure biztonsági mentési adatainak biztonsági mentése a DPM az alábbiak szerint:

1. **Recovery Services-tároló létrehozása** – Azure-portálon hozzon létre egy tárolót.
2. **Töltse le a tárolói hitelesítő adatokat** – töltse le a hitelesítő adatokkal kell a DPM-kiszolgáló regisztrálása a Recovery Services-tároló.
3. **Az Azure Backup szolgáltatás ügynökének telepítése** – az ügynök telepítése minden DPM-kiszolgálón.
4. **A kiszolgáló regisztrálásához** – a DPM-kiszolgáló regisztrálása a Recovery Services-tároló.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="key-definitions"></a>Fontos definíciókat
Az alábbiakban néhány fontos definíciókat az Azure biztonsági mentés a DPM:

1. **Hitelesítő adatok tároló** – tárolói hitelesítő adatok szükségesek, a gép biztonsági mentési adatokat küldhet egy meghatározott tárolóban, az Azure Backup szolgáltatásban hitelesítést. Letölthető a tárolóból, és 48 órán keresztül érvényes.
2. **Jelszó** – a felhőbe biztonsági másolatok titkosításához használt jelszót. Mentse a fájlt egy biztonságos helyre, szükség van egy helyreállítási művelet során.
3. **PIN-kód biztonsági** – Ha engedélyezte a [biztonsági beállítások](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) tároló, biztonsági PIN-kód szükséges kritikus fontosságú biztonsági mentési műveletek végrehajtása. A többtényezős hitelesítés újabb biztonsági réteget ad hozzá. 
4. **A helyreállítási mappa** – a kifejezést a biztonsági mentések a felhőből ideiglenesen a letöltött felhő helyreállítások alatt. Méretének körülbelül a biztonsági mentési elemek párhuzamosan helyreállítani kívánt egyenlőnek kell lennie.


### <a name="1-create-a-recovery-services-vault"></a>1. Recovery Services-tároló létrehozása
Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben kattintson a **Tallózás** elemre, majd az erőforrások listájába írja be a következőt: **Recovery Services**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Recovery Services-tároló** elemre.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-dpm-introduction/open-recovery-services-vault.png)

    A Recovery Services-tárolók listája jelenik meg.
3. A **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** elemre.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-azure-dpm-introduction/rs-vault-menu.png)

    A Recovery Services tároló menü megnyitása, amely felszólítja, hogy adja meg a **neve**, **előfizetés**, **erőforráscsoport**, és **hely**.

    ![Recovery Services-tároló létrehozása – 5. lépés](./media/backup-azure-dpm-introduction/rs-vault-attributes.png)
4. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetéshez. Írjon be egy 2–50 karakter hosszúságú nevet. Ennek egy betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.
5. Kattintson az **Előfizetés** elemre az elérhető előfizetések listájának megtekintéséhez. Ha nem biztos benne, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (vagy javasolt) előfizetést. Csak akkor lesz több választási lehetőség, ha a szervezetéhez tartozó fiók több Azure-előfizetéssel van összekötve.
6. Kattintson az **Erőforráscsoport** elemre az elérhető erőforráscsoportok listájának megtekintéséhez, vagy kattintson az **Új** elemre egy új erőforráscsoport létrehozásához. Átfogó információk az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).
7. Kattintson a **Hely** elemre a tárolóhoz tartozó földrajzi régió kiválasztásához.
8. Kattintson a **Create** (Létrehozás) gombra. A Recovery Services-tároló létrehozása eltarthat egy ideig. Figyelje az állapotértesítéseket a portál jobb felső területén.
   A tároló létrehozása után megjelenik a portálon.

### <a name="set-storage-replication"></a>Tárreplikáció beállítása
A tárreplikáció lehetősége lehetővé teszi, hogy georedundáns tárolás és helyileg redundáns tárolás között válasszon. Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha ez az elsődleges biztonsági mentési tároló, hagyja a beállítást georedundáns tároláson. Ha egy olcsóbb, rövidebb élettartamú megoldást szeretne, válassza a helyileg redundáns tárolást. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

1. Válassza ki a tároló, a tároló irányítópult és a beállítások menü megnyitásához. Ha a **beállítások** menü nem nyitható meg **összes beállítás** a tároló irányítópultjának.
2. A a **beállítások** menüben kattintson a **biztonsági infrastruktúra** > **biztonsági mentési konfigurációhoz** megnyitásához a **biztonsági mentési konfigurációhoz**menü. Az a **biztonsági mentési konfigurációhoz** menüben válassza ki a tárolási replikációs beállítás a tároló számára.

    ![A Backup-tárolók listája](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Miután kiválasztotta a tárolási beállítást a tároló számára, készen áll, hogy hozzárendelje a virtuális gépet a tárolóhoz. A hozzárendelés megkezdéséhez fel kell fedezni és regisztrálni kell az Azure virtuális gépeket.

### <a name="2-download-vault-credentials"></a>2. A tároló hitelesítő adatainak letöltése
A tároló hitelesítőadat-fájlja a portál által az egyes Backup-tárolókhoz létrehozott tanúsítvány. A portál ezután feltölti a nyilvános kulcsot az Access Control Service (ACS) szolgáltatásba. A tanúsítvány titkos kulcsát a munkafolyamatot, amely meg van adva a gép regisztrációs munkafolyamat bemenetként részeként a felhasználó számára legyen elérhető. Ez hitelesíti a gép biztonsági mentési adatokat küldhet egy meghatározott tárolóban, az Azure Backup szolgáltatásban.

A tároló hitelesítőadat-fájlja csak a regisztrációs munkafolyamat során használható. A felhasználó felelőssége, hogy győződjön meg arról, hogy a tároló hitelesítési adatait tartalmazó fájlt ne legyenek veszélyben. Ha rosszindulatú felhasználó kezébe kerül, a tároló hitelesítőadat-fájljával más gépek is regisztrálhatók ugyanabban a tárolóban. Azonban mivel a biztonsági mentési adatok titkosítása a egy hozzáférési kódot, amely az ügyfél tartozik, meglévő biztonsági mentési adatok nem sérült. Ezen probléma mérséklése érdekében tárolói hitelesítő adatok beállítása az 48hrs múlva lejár. Letöltheti a tárolói hitelesítő adatokat a helyreállítási szolgáltatás tetszőleges számú alkalommal – azonban csak a legújabb tárolói hitelesítő adatok fájlját alkalmazható a regisztrációs munkamenet során.

A tárolói hitelesítő adatok fájlját az Azure-portálról egy biztonságos csatornán keresztül letöltődik. Az Azure Backup szolgáltatás nem érzékeli a tanúsítványhoz tartozó titkos kulcsot, és a titkos kulcs nem őrzi meg a portál vagy a szolgáltatás. Az alábbi lépések segítségével töltse le a tárolói hitelesítő adatok fájlját a helyi számítógépen.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg a Recovery Services-tároló, amelyet szeretne regisztrálni a DPM-számítógépről.
3. Alapértelmezés szerint megnyílik beállítások menüjében. Ha be van zárva, kattintson a **beállítások** tároló irányítópult a beállítások menü megnyitásához. A beállítások menüben kattintson a **tulajdonságok**.

    ![A tárolók menüjének megnyitása](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
4. Kattintson a Tulajdonságok lap **letöltése** alatt **biztonsági hitelesítő adatok**. A portál létrehozza a tárolói hitelesítő adatok fájlját, amely letölthető.

    ![Letöltés](./media/backup-azure-dpm-introduction/vault-credentials.png)

A portál a tároló hitelesítő adatait, a tároló neve és az aktuális dátum kombinációja használatával hoz létre. Kattintson a **mentése** töltse le a tárolói hitelesítő adatokat a helyi fiók Letöltések mappába, vagy adjon meg egy helyet a tárolói hitelesítő adatokat a Mentés menüjéből válassza a Mentés másként. Tart egy perc a fájlhoz.

### <a name="note"></a>Megjegyzés
* Győződjön meg arról, hogy a tároló hitelesítési adatait tartalmazó fájlt is elérhetők, a gép helyre menti. Ha a megosztás/SMB fájl tárolja, ellenőrizze, hogy a hozzáférési engedélyeket.
* A tároló hitelesítési adatait tartalmazó fájlt csak a regisztrációs munkamenet során használt.
* A tároló hitelesítési adatait tartalmazó fájlt 48hrs után lejár, és a portálról tölthető le.

### <a name="3-install-backup-agent"></a>3. Biztonsági mentési ügynök telepítése
Miután létrehozta az Azure Backup-tárolóban, az ügynök telepíthető mindegyik a Windows gép (Windows Server, a Windows ügyfél, a System Center Data Protection Manager-kiszolgáló vagy Azure biztonsági mentési kiszolgálóként működő számítógép), amely lehetővé teszi, hogy készítsen biztonsági másolatot az adat és alkalmazás az Azure-bA.

1. Nyissa meg a Recovery Services-tároló, amelyet szeretne regisztrálni a DPM-számítógépről.
2. Alapértelmezés szerint megnyílik beállítások menüjében. Ha be van zárva, kattintson a **beállítások** a beállítások menü megnyitásához. A beállítások menüben kattintson a **tulajdonságok**.

    ![A tárolók menüjének megnyitása](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Kattintson a beállítások lap **letöltése** alatt **Azure Backup szolgáltatás ügynökének**.

    ![Letöltés](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   Ha az ügynök le, futtassa a MARSAgentInstaller.exe az Azure Backup szolgáltatás ügynökének telepítésének indításakor. Válassza ki a telepítési mappa és az ügynök számára szükséges ideiglenes mappa. A gyorsítótár helyére szabad területet, amely a biztonsági mentési adatok legalább 5 %-át kell rendelkeznie.
4. Ha csatlakozni az internethez, a proxykiszolgálót használni a **proxykonfigurációt** képernyőn, írja be a proxy-kiszolgáló adatait. Ha egy hitelesített proxykiszolgálót használ, adja meg a felhasználói nevet és jelszót adatokat ezen a képernyőn.
5. Az Azure Backup szolgáltatás ügynökének telepíti a .NET-keretrendszer 4.5 és Windows PowerShell (Ha még nem érhető el) a telepítés befejezéséhez.
6. Az ügynök telepítése után **Bezárás** az ablak.

   ![Bezárás](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)
7. A **a DPM-kiszolgáló** a tárolóba, a a **felügyeleti** lapra, kattintson a **Online**. Ezt követően válassza **regisztrálása**. Az regisztrálása a telepítő varázsló nyílik meg.
8. Ha csatlakozni az internethez, a proxykiszolgálót használni a **proxykonfigurációt** képernyőn, írja be a proxy-kiszolgáló adatait. Ha egy hitelesített proxykiszolgálót használ, adja meg a felhasználói nevet és jelszót adatokat ezen a képernyőn.

    ![Proxykiszolgáló-konfiguráció](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. A tároló hitelesítő adatait képernyőn keresse meg és válassza ki a korábban letöltött tárolói hitelesítő adatok fájlt.

    ![Tárolói hitelesítő adatokat](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    A tároló hitelesítési adatait tartalmazó fájlt csak esetén érvényes 48 óra (után a portálról letöltés). Ha ez a képernyő (például "tárolói hitelesítő adatok megadott fájlja lejárt"), jelentkezzen be az Azure portál és a tárolói hitelesítő adatokat újra fájl letöltési hibákat.

    Győződjön meg arról, hogy a tároló hitelesítési adatait tartalmazó fájlt elérhető egy helyen, a telepítő alkalmazás is elérhetők. Ha eléréséhez kapcsolódó hibák, másolja a tároló hitelesítési adatait tartalmazó fájlt egy ideiglenes helyre erre a gépre, és próbálja megismételni a műveletet.

    Ha érvénytelen tárolói hitelesítő adatok hibát észlel (például "érvénytelen tárolóban megadott hitelesítő adatok") a fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatokat a helyreállítási szolgáltatáshoz tartozó. Próbálja megismételni a műveletet, miután egy új tárolói hitelesítő adatok fájlját letöltése a portálról. Ez a hiba általában látható, ha a felhasználó a hivatkozásra kattint a **letöltési tárolóhitelesítő adatokat** az Azure portálon, gyors egymásutánban lehetőséget. Ebben az esetben csak a második tárolói hitelesítő adatok fájlját esetén érvényes.
10. A hálózati sávszélesség használatát és a munka, munkaidőn kívüli órákra, lévő vezérlése érdekében a **sávszélesség-szabályozás beállítása** képernyő, beállíthatja a sávszélesség-használati határértékeken és adja meg a munkahelyi és a munkaszüneti óra.

    ![Szabályozó beállítás](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)
11. Az a **helyreállítási mappa beállítás** képernyőn, keresse meg az a mappa, ahol a fájlokat az Azure-ból letöltött ideiglenesen átmenetileg tárolva lesz.

    ![A helyreállítási mappa beállítás](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)
12. Az a **titkosítási beállítás** képernyő, megadhat egy hozzáférési kódot létrehozni, vagy adjon meg egy jelszót (minimum 16 karakter). Ne felejtse el a hozzáférési kód mentse egy biztonságos helyre.

    ![Titkosítás](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Ha a jelszó elvesztése vagy elfelejtése; A Microsoft nem megkönnyíti a biztonsági mentési adatok helyreállítása. A felhasználó tulajdonában van a titkosítás jelszava és a Microsoft nem látnak bele a végfelhasználó által használt jelszót. Mentse a fájlt egy biztonságos helyre az előírt módon a helyreállítási művelet során.
    >
    >
13. Miután rákattintott a **regisztrálása** gombra, a gép sikeresen regisztrálva van a tárolóba, és most már készen indítsa el a biztonsági mentés a Microsoft Azure.
14. A Data Protection Manager használata esetén a során megadott beállítások a regisztrációs munkafolyamat kattintva módosíthatja a **konfigurálása** lehetőség kiválasztásával **Online** alatt a **kezelése**  Fülre.

## <a name="requirements-and-limitations"></a>Követelmények (és korlátozások)
* DPM fizikai kiszolgálóként vagy a System Center 2012 SP1 vagy a System Center 2012 R2 rendszeren telepített Hyper-V virtuális gép futtathat. Is futhat egy Azure virtuális gépként legalább fut a System Center 2012 R2 DPM 2012 R2 3. kumulatív frissítéssel vagy egy Windows virtuális gépként VMWare legalább fut a System Center 2012 R2 kumulatív frissítés 5.
* Ha a System Center 2012 SP1 DPM használ telepítenie kell frissítés visszaállítani a 2 a System Center Data Protection Manager SP1. Erre azért szükség, az Azure Backup szolgáltatás ügynökének telepítése előtt.
* A DPM-kiszolgálóval kell rendelkeznie a Windows PowerShell és a .net Framework 4.5 telepítve.
* A DPM biztonsági másolatot készíthet a munkaterhelések többségéhez az Azure Backup szolgáltatásba. Teljes listáját lásd: Mi van támogatott az Azure Backup támogatja az alábbi elemek.
* Az Azure Backup tárolt adatokat nem lehet helyreállítani, a "Másolás szalagra" beállítással.
* Az Azure Backup szolgáltatás engedélyezve van az Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ a [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/).
* Az Azure Backup használatához telepíteni kell a kiszolgálókat szeretne biztonsági másolatot készíteni az Azure Backup szolgáltatás ügynöke. Minden olyan kiszolgálón kell rendelkeznie az adatokat, amelyek biztonsági mentése van folyamatban, elérhető szabad helyi tárterület mérete legalább 5 %-át. Például 5 GB szabad hely az ideiglenes helyen legalább 100 GB adat másolatának igényel.
* A tároló Azure storage adatokat fog tárolni. Nincs korlátozva az adatok akkor is biztonsági mentés az Azure biztonsági mentési tároló van, de egy adatforrás (például egy virtuális géphez vagy adatbázis) mérete nem haladhatja meg a 54400 GB.

Ilyen típusú biztonsági mentés Azure támogatottak:

* Titkosított (csak teljes biztonsági mentések)
* Tömörített (növekményes biztonsági mentések támogatva)
* Ritka (növekményes biztonsági mentések támogatva)
* Tömörített és ritka (Ritkaként kezelve)

És ezek nem támogatottak:

* A kis-és nagybetűket fájlrendszerek kiszolgálók nem támogatottak.
* A rögzített hivatkozások (kimaradnak)
* Újraelemzési pontok (kimaradnak)
* Titkosított és tömörített (kimaradnak)
* Titkosított és ritka (kimaradnak)
* Tömörített adatfolyam
* Ritka adatfolyam

> [!NOTE]
> A System Center 2012 DPM SP1 és újabb verziók esetében a készíthet biztonsági másolatot az Azure-bA Microsoft Azure Backup szolgáltatás használatával a DPM által védett munkaterhelések fel.
>
>
