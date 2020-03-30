---
title: A DPM-kiszolgáló előkészítése a munkaterhelések biztonsági rendszerének biztonsági készítésére
description: Ebből a cikkből megtudhatja, hogyan készülhet fel a System Center Data Protection Manager (DPM) azure-beli biztonsági mentéseire az Azure Backup szolgáltatás használatával.
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 2119d46ca6102286ca879777058a49938b501ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273461"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Felkészülés a számítási feladatok azure-beli biztonsági készítésére a System Center DPM segítségével

Ez a cikk bemutatja, hogyan készülhet fel a System Center Data Protection Manager (DPM) biztonsági mentései az Azure-ba az Azure Backup szolgáltatás használatával.

A cikk a következőket tartalmazza:

- A DPM azure backup szolgáltatással történő üzembe helyezésének áttekintése.
- Az Azure Backup DPM-mel való használatának előfeltételei és korlátai.
- Az Azure előkészítésének lépései, beleértve a Recovery Services biztonsági mentési tárolójának beállítását, és szükség esetén a tároló Azure-tárolótípusának módosítását.
- A DPM-kiszolgáló előkészítésének lépései, beleértve a tároló hitelesítő adatainak letöltését, az Azure Backup-ügynök telepítését és a DPM-kiszolgáló regisztrálását a tárolóban.
- Hibaelhárítási tippek a gyakori hibákhoz.

## <a name="why-back-up-dpm-to-azure"></a>Miért érdemes biztonsági másolatot adni a DPM-ről az Azure-ba?

[A System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) biztonsági másolatot ad a fájl- és alkalmazásadatokról. A DPM az alábbiak szerint lép kapcsolatba az Azure Backup szolgáltatással:

- **Fizikai kiszolgálón vagy helyszíni virtuális gépen futó DPM** – A lemez- és szalagos biztonsági mentés mellett biztonsági másolatot készíthet az azure-beli biztonsági mentési tárolóba.
- **Az Azure-beli virtuális gépeken futó DPM** – A System Center 2012 R2 3- as vagy újabb frissítéssel rendelkező rendszerközpontjából telepítheti a DPM-et egy Azure virtuális gépen. A virtuális géphez csatlakoztatott Azure-lemezekre biztonsági másolatot készíthet, vagy az Azure Backup segítségével biztonsági másolatot készíthet az adatokról egy biztonsági mentési tárolóba.

A DPM-kiszolgálók Azure-ba való biztonsági mentésének üzleti előnyei a következők:

- A helyszíni DPM, az Azure Backup alternatívát kínál a hosszú távú üzembe helyezés szalagra.
- Az Azure VM-en futó DPM esetében az Azure Backup lehetővé teszi a tárhely azure-lemezről történő kiszervezését. A régebbi adatok biztonsági mentési tárolóban való tárolása lehetővé teszi, hogy az új adatok lemezre tárolásával felskálázhatja a vállalkozást.

## <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

**Beállítás** | **Követelmény**
--- | ---
DPM egy Azure virtuális gép | System Center 2012 R2 dpm 2012 R2 összegző frissítőcsomaggal 3.
DPM fizikai kiszolgálón | System Center 2012 SP1 vagy újabb; System Center 2012 R2.
DPM egy Hyper-V virtuális gépen | System Center 2012 SP1 vagy újabb; System Center 2012 R2.
DPM vmware virtuális gépen | System Center 2012 R2 5-ös vagy újabb összegző frissítőcsomaggal.
Összetevők | A DPM-kiszolgálón telepítve kell lennie a Windows PowerShell és a .NET Framework 4.5 rendszernek.
Támogatott alkalmazások | [További információ arról,](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) hogy a DPM mire képes biztonsági másolatot találni.
Támogatott fájltípusok | Ezekről a fájltípusokról az Azure Backup: Titkosított (csak teljes biztonsági mentések) készíthető biztonsági másolat; Tömörített (növekményes biztonsági mentések támogatottak); Ritka (növekményes biztonsági mentések támogatottak); Sűrített és ritka (gyérként kezelt).
Nem támogatott fájltípusok | A kis- és nagybetűket megkülönböztető fájlrendszerek kiszolgálói; kemény hivatkozások (kimarad); újraelemzési pontok (kimarad); titkosított és tömörített (kimarad); titkosított és ritka (kihagyott); Tömörített adatfolyam; elemzési adatfolyam.
Helyi tárhely | Minden olyan gépnek, amelyről biztonsági másolatot szeretne, helyi szabad tárhellyel kell rendelkeznie, amely legalább 5%-a a biztonsági másolatot alatt álló adatok méretének. A 100 GB-os adatok biztonsági mentése például legalább 5 GB szabad helyet igényel a tervezőpisa helyen.
Tároló tárolása | Nincs korlátozva, hogy az adatok mennyisége készíthet biztonsági másolatot egy Azure Backup-tárolóba, de egy adatforrás (például egy virtuális gép vagy adatbázis) mérete nem haladhatja meg az 54400 GB-ot.
Azure ExpressRoute | Ha az Azure ExpressRoute privát vagy Microsoft-társviszony-létesítéssel van konfigurálva, nem használható az adatok Azure-ba történő biztonsági biztonsági társítására.<br/><br/> Ha az Azure ExpressRoute nyilvános társviszony-létesítéssel van konfigurálva, az adatok Azure-ba történő biztonsági biztonsági másolatot.<br/><br/> **Megjegyzés:** A nyilvános társviszony-létesítés elavult az új áramkörök esetében.
Az Azure Backup ügynöke | Ha a DPM a System Center 2012 SP1 szervizcsomagban fut, telepítse a 2. Ez az ügynök telepítéséhez szükséges.<br/><br/> Ez a cikk ismerteti, hogyan telepítheti az Azure Backup ügynök legújabb verzióját, más néven a Microsoft Azure Recovery Service (MARS) ügynök. Ha egy korábbi verzió telepítve van, frissítse a legújabb verzióra, hogy a biztonsági mentés a várt módon működjön.

Mielőtt elkezdené, szüksége van egy Azure-fiók az Azure Backup funkció engedélyezve van. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. Olvassa el az [Azure Backup díjszabását.](https://azure.microsoft.com/pricing/details/backup/)

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Tárolási beállítások módosítása

Választhat a georedundáns tárolás és a helyileg redundáns tárolás között.

- Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik.
- Ha a tároló az elsődleges biztonsági mentés, hagyja a beállítást georedundáns tárolásra. Ha olcsóbb, nem annyira tartós megoldást szeretne, az alábbi eljárással konfigurálhatja a helyileg redundáns tárolást.
- Ismerje meg [az Azure storage,](../storage/common/storage-redundancy.md)és a [georedundáns](../storage/common/storage-redundancy-grs.md) és [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségeket.
- Módosítsa a tárolási beállításokat a kezdeti biztonsági mentés előtt. Ha már biztonsági másolatot készíteni egy elemről, a tárolóbeállításainak módosítása előtt állítsa le a biztonsági mentést a tárolóban.

A tárreplikációs beállítás szerkesztése:

1. Nyissa meg a páncélterem irányítópultját.

2. A **Kezelés csoportban**kattintson a **Biztonsági másolat infrastruktúra elemre.**

3. A **Biztonsági másolat konfigurációja** menüben válasszon egy tárolási lehetőséget a tárolóhoz.

    ![A Backup-tárolók listája](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>A tároló hitelesítő adatainak letöltése

A tároló hitelesítő adatait használja, amikor regisztrálja a DPM-kiszolgálót a tárolóban.

- A tároló hitelesítőadat-fájlja a portál által az egyes Backup-tárolókhoz létrehozott tanúsítvány.
- A portál ezután feltölti a nyilvános kulcsot az Access Control Service (ACS) szolgáltatásba.
- A gépregisztrációs munkafolyamat során a tanúsítvány személyes kulcsa elérhetővé válik a felhasználó számára, amely hitelesíti a számítógépet.
- A hitelesítés alapján az Azure Backup szolgáltatás adatokat küld az azonosított tárolóba.

### <a name="best-practices-for-vault-credentials"></a>Ajánlott eljárások a tároló hitelesítő adataihoz

A hitelesítő adatok beszerzéséhez töltse le a tároló hitelesítő fájlját egy biztonságos csatornán keresztül az Azure Portalról:

- A tároló hitelesítő adatait csak a regisztrációs munkafolyamat során használják.
- Az Ön felelőssége annak biztosítása, hogy a trezor hitelesítő adatok fájl biztonságos, és nem sérült.
  - Ha a hitelesítő adatok vezérlése elvész, a tároló hitelesítő adatait lehet használni, hogy regisztráljon más gépek a tárolóba.
  - A biztonsági mentési adatok titkosítása azonban az ügyfélhez tartozó jelszóval történik, így a meglévő biztonsági mentési adatok nem sérülhetnek.
- Győződjön meg arról, hogy a fájl olyan helyre kerül, amely a DPM-kiszolgálóról érhető el. Ha fájlmegosztásban/SMB-ben van tárolva, ellenőrizze a hozzáférési engedélyeket.
- A tároló hitelesítő adatai 48 óra elteltével lejárnak. Az új tároló hitelesítő adatait annyiszor töltheti le, ahányszor csak szükséges. Azonban csak a legújabb tároló hitelesítő fájl használható a regisztrációs munkafolyamat során.
- Az Azure Backup szolgáltatás nem ismeri a tanúsítvány személyes kulcsát, és a személyes kulcs nem érhető el a portálon vagy a szolgáltatásban.

Töltse le a tároló hitelesítő adatait egy helyi számítógépre az alábbiak szerint:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Nyissa meg azt a tárolót, amelyben regisztrálni szeretné a DPM-kiszolgálót.
3. A **Beállítások csoportban**kattintson a **Tulajdonságok gombra.**

    ![A tárolók menüjének megnyitása](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. A **Tulajdonságok** > **biztonsági másolatai párbeszédpanelen**kattintson a **Letöltés gombra.** A portál a tároló hitelesítő adatait a tároló nevének és az aktuális dátumnak kombinációjával hozza létre, és lehetővé teszi számára a letöltést.

    ![Letöltés](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Kattintson a **Mentés gombra** a tároló hitelesítő adatainak mappába való letöltéséhez, vagy a **Mentés másként** gombra, és adja meg a helyet. A fájl létrehozása akár egy percet is igénybe vehet.

## <a name="install-the-backup-agent"></a>A biztonságimásolat-készítő ügynök telepítése

Az Azure Backup által biztonsági másolatban található minden gépre telepítve kell lennie a biztonsági mentési ügynöknek (más néven a Microsoft Azure Recovery Service (MARS) ügynöknek). Telepítse az ügynököt a DPM-kiszolgálóra az alábbiak szerint:

1. Nyissa meg azt a tárolót, amelyhez regisztrálni szeretné a DPM-kiszolgálót.
2. A **Beállítások csoportban**kattintson a **Tulajdonságok gombra.**

    ![A tárolók menüjének megnyitása](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. A **Tulajdonságok** lapon töltse le az Azure backup ügynök.

    ![Letöltés](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. A letöltés után futtassa a MARSAgentInstaller.exe programot. az ügynök DPM-számítógépre való telepítéséhez.
5. Jelöljön ki egy telepítési mappát és gyorsítótármappát az ügynökhöz. A gyorsítótár helyének szabad területének a biztonsági mentési adatok legalább 5%-ának kell lennie.
6. Ha proxykiszolgálót használ az internethez való csatlakozáshoz, a **Proxy konfigurációs** képernyőjén adja meg a proxykiszolgáló adatait. Ha hitelesített proxyt használ, ezen a képernyőn adja meg a felhasználónevét és a jelszavát.
7. Az Azure Backup ügynök telepíti a .NET Framework 4.5 és a Windows PowerShell (ha nincsenek telepítve) a telepítés befejezéséhez.
8. Az ügynök telepítése után **zárja be** az ablakot.

    ![Bezárás](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>A DPM-kiszolgáló regisztrálása a tárolóban

1. A DPM-rendszergazda konzolon > **A kezelés**ablakban kattintson az **Online**gombra. Kattintson a **Register** (Regisztrálás) elemre. Megnyílik a Kiszolgáló regisztrálása varázsló.
2. A **Proxykonfiguráció párbeszédpanelen**adja meg a proxybeállításokat.

    ![Proxy konfigurálása](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. A **Biztonsági másolat tárolójában**keresse meg és jelölje ki a letöltött tároló hitelesítő adatait.

    ![A tároló hitelesítő adatai](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. A **Szabályozás beállításban**tetszés szerint engedélyezheti a sávszélesség-szabályozást a biztonsági mentések számára. Beállíthatja a munkaidő és a nap megadására vonatkozó sebességkorlátozásokat.

    ![Szabályozás beállítása](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. A **Helyreállítási mappa beállításában**adja meg az adat-helyreállítás során használható helyet.

    - Az Azure Backup ezt a helyet használja ideiglenes tárolóterületként a helyreállított adatokhoz.
    - Az adatok helyreállításának befejezése után az Azure Backup törli az ezen a területen lévő adatokat.
    - A helynek elegendő hellyel kell rendelkeznie a párhuzamosan behegedt elemek tárolására.

    ![Helyreállítási mappa beállítása](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. A **Titkosítás beállításban**hozzon létre vagy adjon meg egy jelszót.

    - A jelszó a biztonsági mentések felhőbe titkosítására szolgál.
    - Adjon meg legalább 16 karaktert.
    - Mentse a fájlt biztonságos helyre, a helyreállításhoz szükséges.

    ![Titkosítás](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Ön a titkosítási jelszó, és a Microsoft nem rendelkezik rálátással.
    > Ha a jelszó elveszett vagy elfelejtett; A Microsoft nem tud segíteni a biztonsági mentési adatok helyreállításában.

7. Kattintson a **Regisztráció** gombra a DPM-kiszolgáló regisztrálásához a tárolóba.

Miután a kiszolgáló sikeresen regisztrálva van a tárolóba, és most már készen áll a Microsoft Azure-ra való biztonsági mentés megkezdésére. A DPM-konzolon lévő védelmi csoportot konfigurálnia kell az Azure-ba irányuló számítási feladatok biztonsági mentéséhez. [További információ](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019) a védelmi csoportok üzembe helyezéséről.

## <a name="troubleshoot-vault-credentials"></a>Tároló hitelesítő adatainak – problémamegoldás

### <a name="expiration-error"></a>Elévülési hiba

A tároló hitelesítő fájl csak 48 óráig érvényes (miután letöltötte a portálról). Ha bármilyen hibát észlel ezen a képernyőn (például "A vault hitelesítő adatok megadott fájl lejárt"), jelentkezzen be az Azure Portalon, és töltse le újra a tároló hitelesítő fájl.

### <a name="access-error"></a>Hozzáférési hiba

Győződjön meg arról, hogy a tároló hitelesítő fájl érhető el egy helyen, amely elérhető a telepítő alkalmazás által elérhető. Ha hozzáféréssel kapcsolatos hibákat tapasztal, másolja a tároló hitelesítő adatait egy ideiglenes helyre ezen a számítógépen, és próbálja meg újra a műveletet.

### <a name="invalid-credentials-error"></a>Érvénytelen hitelesítő adatok hiba

Ha érvénytelen tárolóhitelesítő hibát észlel (például "Érvénytelen tárolóhitelesítő adatok") a fájl sérült, vagy nem rendelkezik a helyreállítási szolgáltatáshoz társított legújabb hitelesítő adatokkal.

- Próbálkozzon újra a művelettel, miután letöltött egy új tároló hitelesítő fájlját a portálról.
- Ez a hiba általában akkor jelenik meg, ha az Azure Portalon a **Tároló hitelesítő adatainak letöltése** beállításra kattint, kétszer gyors egymásutánban. Ebben az esetben csak a második tároló hitelesítő adatfájl érvényes.
