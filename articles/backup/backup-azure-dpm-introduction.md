---
title: A DPM használata számítási feladatok biztonsági mentéséhez az Azure-bA
description: Bevezetés az adatok biztonsági mentése a DPM az Azure Recovery Services-tárolóba.
services: backup
author: adigan
manager: nkolli
keywords: A System Center Data Protection Manager, a data protection manager, a dpm biztonsági mentése
ms.service: backup
ms.topic: conceptual
ms.date: 8/22/2018
ms.author: adigan
ms.openlocfilehash: 2da5b04f56a5746fb77de6bc954bb5971eb4664b
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885170"
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Számítási feladatok Azure-ba való biztonsági mentésének előkészítése a DPM-mel
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Ez a cikk azt ismerteti, hogyan adatok biztonsági mentéséhez a System Center Data Protection Manager (DPM) az Azure-bA; beleértve a következőket:

* Biztonsági mentése a DPM-kiszolgáló adatainak az Azure-bA
* Az Előfeltételek egy zökkenőmentes biztonsági mentés felhasználói élményt érdekében
* A tipikus hibák és a velük kezelése
* Támogatott esetek

> [!NOTE]
> Az Azure az erőforrások létrehozásához és használatához két üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). A cikk a Resource Manager modellel üzembe helyezett virtuális gépek visszaállításához információkat és eljárásokat ismerteti.
>
>

[A System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) fájlok és alkalmazások adatainak biztonsági mentését. További információ a támogatott számítási feladatok található [Itt](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix). Adatok biztonsági mentése a DPM a lemezen, szalagon tárolt vagy biztonsági mentése az Azure-bA a Microsoft Azure Backup szolgáltatásban is. A DPM a következőképpen kommunikál az Azure Backup szolgáltatással:

* **Fizikai kiszolgáló vagy a helyszíni virtuális gépként telepített DPM** – DPM fizikai kiszolgálóként üzembe helyezett, vagy egy helyszíni Hyper-V virtuális gép biztonsági másolatot készít adatok mellett a lemezes és szalagos helyreállítási tárba történő biztonsági mentés.
* **Az Azure virtuális gépként telepített DPM** – a System Center 2012 R2 Update 3, a DPM telepíthető Azure virtuális géphez. Ha a DPM Azure virtuális gépként van telepítve, adatokról készít biztonsági másolatot a virtuális géphez csatolt Azure lemezeken, vagy az adattárolási kiszervezheti a biztonsági mentés a Recovery Services-tároló.

## <a name="why-back-up-dpm-to-azure"></a>Miért érdemes a DPM biztonsági mentése az Azure-bA?
A DPM-kiszolgálók biztonsági másolatának az Azure-bA üzleti előnyei a következők:

* A helyszíni DPM-telepítés az Azure-t szalagos hosszú távú központi telepítés helyett.
* A DPM-et az Azure-beli virtuális gép üzembe helyezése az Azure disk storage kiürítése. A helyreállítási tárban lévő régebbi adatok tárolásához lehetővé teszi azáltal, hogy az üzleti lemezre új adatok tárolására.

## <a name="prerequisites"></a>Előfeltételek
Készítse elő az Azure Backup az adatok biztonsági mentése a DPM a következő:

1. **Hozzon létre egy Recovery Services-tároló** – hozzon létre egy tárolót az Azure Portalon.
2. **Töltse le a tároló hitelesítő adatainak** – töltse le a hitelesítő adatokat a DPM-kiszolgálón regisztrálja a Recovery Services-tároló használatával.
3. **Az Azure Backup ügynök telepítése** – az ügynök telepítése minden egyes DPM-kiszolgálón.
4. **Regisztrálja a kiszolgálót** – a DPM-kiszolgáló regisztrálása a Recovery Services-tárolóval.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="key-definitions"></a>Fontos definíciókat
Az alábbiakban a DPM az backup Azure néhány fontos definíciókat:

1. **Tárolói hitelesítő adatok** – a tároló hitelesítő adatai szükségesek a gép biztonsági mentési adatokat küldjön egy azonosított tárolóba az Azure Backup szolgáltatásban történő hitelesítéséhez. Letölthető a tárolóból és azt érvényes 48 órán át.
2. **Hozzáférési kód** – a felhőbeli biztonsági mentések titkosításához használt jelszó. Mentse a fájlt egy biztonságos helyre, mivel szükség egy helyreállítási művelet során.
3. **Biztonsági PIN-kód** – Ha engedélyezte a [biztonsági beállítások](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) a tár biztonsági PIN-kód szükséges kritikus fontosságú biztonsági mentési műveletek végrehajtása. A multi-factor Authentication hitelesítés újabb biztonsági réteget ad hozzá. 
4. **Helyreállítási mappa** – legyen az a kifejezés, amely a felhőbeli biztonsági mentések ideiglenesen le felhőalapú helyreállítás közben. A méret nagyjából párhuzamosan helyreállítani kívánt biztonsági mentési elem mérete megegyezik kell lennie.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="edit-storage-replication"></a>Tárreplikáció szerkesztése

Tárreplikáció lehetővé teszi, hogy georedundáns tárolás és helyileg redundáns tárolás között válasszon. Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha a tároló az elsődleges biztonsági mentési, hagyja a beállítást, a georedundáns tárolás. Ha egy olcsóbb, amely tartós, még nem szeretné, a következő eljárással konfigurálhatja a helyileg redundáns tárolás. A [georedundáns](../storage/common/storage-redundancy-grs.md) és a [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.

A tárreplikációs beállítás szerkesztése:

> [!NOTE] 
> A tárreplikáció a kezdeti biztonsági mentés elindítása előtt konfigurálja. Ha úgy dönt, hogy módosítsa a tárolási replikációs konfiguráció után a védett elem biztonsági másolatának, le kell állítania védelem a táron a tárolási konfiguráció váltása előtt.
>  

1. Válassza ki a tárolót, és nyissa meg a tároló irányítópultján.

2. Az a **kezelés** területén kattintson **biztonsági mentési infrastruktúra**.

3. Az a **biztonsági mentés konfigurációja** menüben válassza ki a tárolóreplikációs beállítást a tároló számára.

    ![A Backup-tárolók listája](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

    Miután kiválasztotta a tárolási beállítást a tároló számára, készen áll, hogy hozzárendelje a virtuális gépet a tárolóhoz. A hozzárendelés megkezdéséhez fel kell fedezni és regisztrálni kell az Azure virtuális gépeket.

## <a name="download-vault-credentials"></a>A tároló hitelesítő adatainak letöltése
A tároló hitelesítőadat-fájlja a portál által az egyes Backup-tárolókhoz létrehozott tanúsítvány. A portál ezután feltölti a nyilvános kulcsot az Access Control Service (ACS) szolgáltatásba. A gép regisztrációs munkafolyamat során a tanúsítvány titkos kulcsához szeretné elérhetővé tenni a felhasználónak, amely feljogosítja a gépet. A hitelesítés alapján, az Azure Backup szolgáltatás adatokat küld az azonosított tárolóba.

A tároló hitelesítőadat-fájlja csak a regisztrációs munkafolyamat során használható. A felhasználó felelőssége, hogy győződjön meg arról, hogy a tároló hitelesítőadat-fájlja ne legyenek veszélyben. Vezérlő a hitelesítő adatok nem vesztek el, ha a tároló hitelesítő adatainak a tárba más gépek használható. Biztonsági mentési adatok azonban van titkosítva, így a meglévő biztonsági mentési adatok biztonsága nem sérülhet az ügyfélhez tartozó jelszóval. Ez a probléma megoldásához, a tároló hitelesítő adatai lejárnak 48 óra. Töltse le az új tároló hitelesítő adatait annyiszor szükséges. Azonban csak a legújabb tároló hitelesítőadat-fájlja a regisztrációs munkafolyamat során használható.

A tároló hitelesítőadat-fájlja a rendszer letölti az Azure Portalról biztonságos csatornán keresztül történik. Az Azure Backup szolgáltatás nincs tisztában a tanúsítvány titkos kulcsához, és a titkos kulcs nem érhető el a portálon vagy a szolgáltatás. Az alábbi lépések segítségével a tároló hitelesítőadat-fájljának letöltése a helyi számítógépre.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Nyissa meg a Recovery Services-tárolót, amelyet szeretne regisztrálni a DPM-kiszolgálóhoz.

3. Beállítások menü alapértelmezés szerint megnyílik. Ha be van zárva, kattintson a **beállítások** a tároló irányítópultjának, a menü megnyitásához. Az a **beállítások** menüben kattintson a **tulajdonságok**.

    ![A tárolók menüjének megnyitása](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. A Tulajdonságok lapon alatt **biztonsági hitelesítő adatok** kattintson **letöltése**. A portálon hoz létre a tároló hitelesítőadat-fájlja, amely tettük elérhetővé letöltésre.

    ![Letöltés](./media/backup-azure-dpm-introduction/vault-credentials.png)

A portálon a tároló hitelesítő adatait, a tároló nevére, és az aktuális dátumot együttes használatával hoz létre. Kattintson a **mentése** a tároló hitelesítő adatainak letöltése a helyi fiók Letöltések mappába, és adjon meg egy helyet a tároló hitelesítő adatait, a Mentés menü Mentés másként kiválasztására. Létrejön a fájl egy percig eltarthat.

### <a name="note"></a>Megjegyzés
* Győződjön meg arról, hogy a tároló hitelesítőadat-fájlja a gép számára elérhető helyre menti. Ha egy fájl megosztási/SMB tárolva van, ellenőrizze a hozzáférési engedélyeket.
* A tároló hitelesítőadat-fájlja csak a regisztrációs munkafolyamat során használatos.
* A tároló hitelesítőadat-fájlja 48hrs után lejár, és letölthető a portálon.

## <a name="install-backup-agent"></a>Backup-ügynök telepítése
Miután létrehozta az Azure Backup-tárolót, egy ügynököt telepíteni kell minden, a Windows gépek (Windows Server, Windows-ügyfél, a System Center Data Protection Manager-kiszolgáló vagy az Azure Backup Server gép), amely lehetővé teszi, hogy készítsen biztonsági másolatot az adatokhoz és alkalmazásokhoz az Azure-bA .

1. Nyissa meg a Recovery Services-tárolót, amelyet szeretne regisztrálni a DPM-gépre.
2. Beállítások menü alapértelmezés szerint megnyílik. Ha be van zárva, kattintson a **beállítások** a beállítások menü megnyitásához. A beállítások menüben kattintson a **tulajdonságok**.

    ![A tárolók menüjének megnyitása](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Kattintson a beállítások oldalon **letöltése** alatt **Azure Backup szolgáltatás ügynökének**.

    ![Letöltés](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   Az ügynök letöltését követően futtassa a MARSAgentInstaller.exe az Azure Backup-ügynök telepítésének indításához. Válassza ki a telepítési mappa és az ügynök számára szükséges ideiglenes mappát. A gyorsítótár helyét, a megadott szabad területet, amely a biztonsági mentési adatok legalább 5 %-át kell rendelkeznie.

4. Ha a proxykiszolgáló segítségével csatlakozzon az internethez, az a **proxykonfiguráció** képernyőn, adja meg a proxykiszolgáló adatai. Ha egy hitelesített proxyt használ, adja meg ezen a képernyőn a felhasználó felhasználónevének és jelszavának részleteit.

5. Az Azure Backup ügynököt telepíti a .NET-keretrendszer 4.5 és Windows PowerShell használatával (Ha ez még nem érhető el) a telepítés befejezéséhez.

6. Az ügynök telepítése után **Bezárás** az ablakban.

   ![Bezárás](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

7. A **a DPM-kiszolgáló regisztrálása** a tárolóba, az a **felügyeleti** lapra, kattintson a **Online**. Ezután válassza ki **regisztrálása**. A telepítő regisztrálása varázsló megnyílik.

8. Ha a proxykiszolgáló segítségével csatlakozzon az internethez, az a **proxykonfiguráció** képernyőn, adja meg a proxykiszolgáló adatai. Ha egy hitelesített proxyt használ, adja meg ezen a képernyőn a felhasználó felhasználónevének és jelszavának részleteit.

    ![Proxy konfigurálása](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. A tároló hitelesítő adatok képernyőn keresse meg és válassza ki a tároló hitelesítő adatai fájlt, amelyet korábban letöltött.

    ![A tároló hitelesítő adatai](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    A tároló hitelesítőadat-fájlja csak esetén érvényes 48 óra (nincs letöltve a portálon). Ha ezt a képernyőt (például "tároló hitelesítő adatainak megadott fájlja lejárt"), jelentkezzen be az Azure portal és a tároló hitelesítő adatai fájlt újra letöltési hibákat tapasztal.

    Győződjön meg arról, hogy a tároló hitelesítőadat-fájlja érhető el olyan helyre, ahol a telepítő alkalmazás elérhető lesz. Ha eléréséhez kapcsolódó hibákat, másolja a tároló hitelesítőadat-fájlja egy ideiglenes helyre erre a gépre, és próbálja megismételni a műveletet.

    Ha a tároló hitelesítőadat-hibát észlel (például "érvénytelen tár hitelesítő adatainak megadott") a fájl sérült, vagy nem nem rendelkezik a hozzárendelt legfrissebb hitelesítő adatokkal a szolgáltatásban. Próbálja megismételni a műveletet, miután letöltötte az új tároló hitelesítőadat-fájlja a portálról. Ez a hiba általában látható, ha a felhasználó kattint a **letöltési Táralapú hitelesítőadat** beállítás egymás után gyorsan az Azure Portalon. Ebben az esetben csak a második tároló hitelesítőadat-fájlja érvénytelen.

10. A hálózati sávszélesség használatának szabályozására során a munka és a munkaidőn kívüli órákra, az a **sávszélesség-szabályozás beállítása** képernyő, beállíthatja a sávszélesség használati korlátokat, és adja meg a munkahelyi és a munkaidőn kívüli óra.

    ![Sávszélesség-szabályozás beállítása](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. Az a **helyreállítási mappabeállításának** lapon keresse meg az a mappa, ahol a fájlokat letölteni az Azure-ból ideiglenesen átmenetileg tárolva lesz.

    ![Helyreállítási mappa beállítás](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. Az a **titkosítási beállítás** képernyő, hozzon létre egy hozzáférési kódot, vagy adjon meg egy jelszót (legalább 16 karakter). Ne felejtse el egy biztonságos helyre mentse.

    ![Titkosítás](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Ha a jelszó elvesztése vagy elfelejtése; A Microsoft nem segít a biztonsági mentési adatok helyreállítása. A felhasználó tulajdonában van a titkosítási jelszót, és a Microsoft nem rendelkezik a végfelhasználók által használt jelszót láthatósága. Mentse a fájlt egy biztonságos helyre, mert szükség egy helyreállítási művelet során.
    >
    >

13. Miután rákattint a **regisztrálása** gombra, a gép sikeres regisztrációja esetén a tárolóhoz, és most már készen áll, indítsa el a biztonsági mentés a Microsoft Azure.

14. A Data Protection Manager használata esetén kattintson a regisztrációs munkafolyamat során megadott beállításokat módosíthatja a **konfigurálása** lehetőség kiválasztásával **Online** alatt a **kezelése**  Fülre.

## <a name="requirements-and-limitations"></a>Követelmények (és korlátozások)
* DPM fizikai kiszolgálóként vagy a System Center 2012 SP1 vagy System Center 2012 R2 rendszeren telepített Hyper-V virtuális gép futhat. Azt is futtathat a System Center 2012 R2 vagy újabb rendszert futtató Azure virtuális gépként a DPM 2012 R2 3. kumulatív frissítéssel vagy a VMware környezetben a System Center 2012 R2 vagy újabb rendszerű Windows virtuális gép 5. kumulatív frissítés.
* Ha a System Center 2012 SP1 DPM szoftvert kell telepítenie kumulatív frissítés a 2 System Center Data Protection Manager SP1. Ez azért szükséges, az Azure Backup ügynök telepítése előtt.
* A DPM-kiszolgálóval kell rendelkeznie a Windows PowerShell és a .net keretrendszer 4.5-ös verzióját.
* A DPM biztonsági másolatot készíthet a legtöbb számítási feladatokat az Azure Backup szolgáltatásban. Lásd még támogatott műveletek teljes listáját az Azure Backup támogatja az alábbi cikkeket.
* Az Azure Backup tárolt adatok a "Másolás szalagra" beállítás nem állítható helyre.
* Az Azure Backup szolgáltatás engedélyezve van az Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/).
* Az Azure Backup használatához telepíteni kell a kiszolgálók biztonsági az Azure Backup-ügynök. Minden kiszolgálónak rendelkeznie kell legalább 5 %-a másolat készül, szabad helyi tárterület elérhető adatok. Például 100 GB adat biztonsági mentésével legalább 5 GB szabad hely az ideiglenes hely szükséges.
* Adatokat a tároló Azure storage-ban kell tárolni. Nem, akkor képes biztonsági mentést az Azure Backup-tároló adatok mennyisége korlátozott, de egy adatforráshoz (például egy virtuális gép vagy adatbázis) mérete nem haladhatja meg a 54400 GB.

Ilyen típusú biztonsági mentést Azure támogatottak:

* Titkosított (csak teljes biztonsági mentések)
* Tömörített (növekményes biztonsági mentések támogatva)
* Ritka (növekményes biztonsági mentések támogatva)
* Tömörített és ritka (Ritkaként kezelve)

És nem támogatott az alábbiak:

* A kis-és nagybetűket fájlrendszerek kiszolgálók nem támogatottak.
* A rögzített hivatkozások (kimaradnak)
* Újraelemzési pontok (kimaradnak)
* Titkosított és tömörített (kimaradnak)
* Titkosított és ritka (kimaradnak)
* Tömörített adatfolyam
* Ritka adatfolyam

> [!NOTE]
> A System Center 2012 DPM SP1 és újabb verziók, akkor is védett munkaterhelések biztonsági mentését az Azure-bA.
>
>
