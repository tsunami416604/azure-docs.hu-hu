---
title: A DPM-kiszolgáló számítási feladatainak biztonsági mentéséhez az Azure előkészítése
description: Bevezetés az adatok biztonsági mentése a DPM az Azure Recovery Services-tárolóba.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: bb13e507e7992f4cd4d767a7a18850739b8dccf2
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270198"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Az Azure-ba, a System Center DPM-mel biztonsági másolatot előkészítése

Ez a cikk bemutatja, hogyan készíti elő a System Center Data Protection Manager (DPM) biztonsági másolat az Azure, az Azure Backup szolgáltatással.

A cikk tartalmazza:

- Telepíthető a DPM az Azure Backup áttekintése.
- Előfeltételek és korlátozások vonatkoznak az Azure Backup a dpm-mel.
- Azure-ban, beleértve a Recovery Services Backup-tároló beállításához, és szükség esetén módosítása az Azure storage a tároló típusa előkészítésének lépéseit.
- A DPM-kiszolgálón, beleértve a letöltés előkészítésének lépéseit a tár hitelesítő adatait, az Azure Backup ügynök telepítése, és regisztrálnia kell a DPM-kiszolgáló a tárolóban.
- Hibaelhárítási tippek a gyakori hibák elhárításához.


## <a name="why-back-up-dpm-to-azure"></a>Miért érdemes a DPM biztonsági mentése az Azure-bA?

[A System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) fájlok és alkalmazások adatainak biztonsági mentését. A DPM a következőképpen kommunikál az Azure Backup szolgáltatással:

* **DPM fizikai kiszolgálóként vagy helyszíni virtuális gép futó** – akkor is adatainak biztonsági mentése Backup-tároló az Azure-ban, emellett lemezre és szalagra biztonsági mentés.
* **Egy Azure-beli virtuális gépen futó DPM** – a System Center 2012 R2 Update 3 vagy újabb, a DPM telepíthető Azure rendszerű virtuális gépen. Adatok biztonsági mentése a virtuális géphez csatolt Azure lemezeken, vagy az adatok biztonsági mentésére egy Backup-tárolóba az Azure Backup szolgáltatás használatával.

A DPM-kiszolgálók biztonsági másolatának az Azure-bA üzleti előnyei a következők:

* A helyszíni dpm az Azure Backup biztosít a szalagos hosszú távú központi telepítés helyett.
* A dpm-beli virtuális gépen fut az Azure Backup lehetővé teszi az Azure lemezről tárolást. Backup-tároló régebbi adatok tárolása lehetővé teszi azáltal, hogy az üzleti lemezre új adatok tárolására.

## <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

**Beállítás** | **Követelmény**
--- | ---
A DPM egy Azure virtuális gépen | System Center 2012 R2 DPM 2012 R2 3. kumulatív frissítéssel vagy újabb.
DPM fizikai kiszolgálón | A System Center 2012 SP1 vagy újabb, illetve A System Center 2012 R2.
A DPM a Hyper-V virtuális gépen | A System Center 2012 SP1 vagy újabb, illetve A System Center 2012 R2.
DPM VMware virtuális gépen | System Center 2012 R2 5. kumulatív frissítés vagy újabb.
Összetevők | A DPM-kiszolgálóval kell rendelkeznie a Windows PowerShell és a .net keretrendszer 4.5-ös verzióját.
Támogatott alkalmazások | [Ismerje meg,](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) milyen biztonsági mentéseket.
Támogatott fájltípusok | Az ilyen biztonsági másolat készíthető a Azure Backup szolgáltatással: Titkosított (csak teljes biztonsági mentések); Tömörített (növekményes biztonsági mentések támogatva); Ritka (növekményes biztonsági mentések támogatva); Tömörített és ritka (kezelt, ritka).
Nem támogatott fájltípusok | A kis-és nagybetűket fájlrendszerek; kiszolgálók a rögzített hivatkozások (kimaradnak); újraelemzési pontok (kimaradnak); titkosított és tömörített (kimaradnak); titkosított és ritka (kimaradnak); Tömörített stream; elemezni az adatfolyamot.
Helyi tárhely | Egyes gépek biztonsági helyi szabad tárhely, amely az adatokat, amelyek készül biztonsági másolat méretének legalább 5 %-át kell rendelkeznie.  Például 100 GB adat biztonsági mentésével legalább 5 GB szabad hely az ideiglenes hely szükséges.
Tároló tárolási | Nem a biztonsági másolatot készíthet az Azure Backup-tároló adatok mennyisége korlátozott, de egy adatforráshoz (például egy virtuális gép vagy adatbázis) mérete nem haladhatja meg a 54400 GB.
Az Azure Backup ügynöke | Ha a DPM a System Center 2012 SP1 rendszeren fut, telepítse a 2-es vagy újabb kumulatív DPM SP1. Ez az ügynök telepítéséhez szükséges.<br/><br/> Ez a cikk ismerteti, hogyan helyezhet üzembe az Azure Backup ügynököt, más néven a Microsoft Azure Recovery Service-(MARS-) ügynök legújabb verzióját. Ha egy korábbi verziója telepítve van, győződjön meg arról, hogy a biztonsági mentés a várt módon működik a legújabb verzióra frissíteni.


Mielőtt elkezdené, egy Azure-fiókra van szüksége az Azure Backup szolgáltatás engedélyezve van. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/).


[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]


## <a name="modify-storage-settings"></a>Tárolási beállítások módosítása

Georedundáns tárolás és helyileg redundáns tárolás közül választhat.

- Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik.
- Ha a tároló az elsődleges biztonsági mentési, hagyja a beállítást, a georedundáns tárolás. Ha egy olcsóbb, amely tartós, még nem szeretné, a következő eljárással konfigurálhatja a helyileg redundáns tárolás.
- Ismerje meg [az Azure storage](../storage/common/storage-redundancy.md), és a [georedundáns](../storage/common/storage-redundancy-grs.md) és [helyileg redundáns](../storage/common/storage-redundancy-lrs.md) tárolási lehetőségeket.
- A kezdeti biztonsági mentés előtt tárolási beállítások módosítása. Ha Ön már készített biztonsági másolatot egy elemet, állítsa le a biztonsági mentés a tároló tárolási beállítások módosítása előtt.

A tárreplikációs beállítás szerkesztése:

1. Nyissa meg a tároló irányítópultján.

2. A **kezelés**, kattintson a **biztonsági mentési infrastruktúra**.

3. A **biztonsági mentés konfigurációja** menüben válassza a tárolási beállítást a tárolóhoz.

    ![A Backup-tárolók listája](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)


## <a name="download-vault-credentials"></a>A tároló hitelesítő adatainak letöltése

A tároló hitelesítő adatainak használnia, ha a DPM-kiszolgálón regisztrálja a tárolóban.

- A tároló hitelesítőadat-fájlja a portál által az egyes Backup-tárolókhoz létrehozott tanúsítvány.
- A portál ezután feltölti a nyilvános kulcsot az Access Control Service (ACS) szolgáltatásba.
- A gép regisztrációs munkafolyamat során a tanúsítvány titkos kulcsához szeretné elérhetővé tenni a felhasználónak, amely feljogosítja a gépet.
- A hitelesítés alapján, az Azure Backup szolgáltatás adatokat küld az azonosított tárolóba.

### <a name="best-practices-for-vault-credentials"></a>Ajánlott eljárások a tároló hitelesítő adatai

Szerezze be a hitelesítő adatokat, töltse le a tároló hitelesítő adatait tartalmazó fájlt egy biztonságos csatornán keresztül az Azure Portalról:

- A tároló hitelesítő adatai csak a regisztrációs munkafolyamat során használt.
- A feladata, hogy a tároló hitelesítőadat-fájlja biztonságos, és nem sérült.
    -  Vezérlő a hitelesítő adatok nem vesztek el, ha a tároló hitelesítő adatainak a tárba más gépek használható.
    - Biztonsági mentési adatok azonban van titkosítva, így a meglévő biztonsági mentési adatok biztonsága nem sérülhet az ügyfélhez tartozó jelszóval.
- Győződjön meg arról, hogy a fájl mentése egy olyan helyre, amely a DPM-kiszolgáló elérhető lesz. Ha egy fájl megosztási/SMB tárolva van, ellenőrizze a hozzáférési engedélyeket.
- A tároló hitelesítő adatai lejárnak 48 óra. Új tároló hitelesítő adatait annyiszor szükséges töltheti le. Azonban csak a legújabb tároló hitelesítőadat-fájlja a regisztrációs munkafolyamat során használható.
- Az Azure Backup szolgáltatás nincs tisztában a tanúsítvány titkos kulcsához, és a titkos kulcs nem érhető el a portálon vagy a szolgáltatás.

Töltse le a tároló hitelesítőadat-fájlja a helyi számítógépre a következőképpen:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg a tároló, amelyben a DPM-kiszolgálón regisztrálni szeretne.
3. A **beállítások**, kattintson a **tulajdonságok**.

    ![A tárolók menüjének megnyitása](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. A **tulajdonságok** > **biztonsági hitelesítő adatok**, kattintson a **letöltése**. A portálon a tároló hitelesítőadat-fájlja a tároló neve és az aktuális dátum együttes használatával hoz létre, és elérhetővé letöltésre.

    ![Letöltés](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Kattintson a **mentése** a tároló hitelesítő adatainak letöltése mappába, vagy **Mentés másként** , és adjon meg egy helyet. Létrejön a fájl egy percig eltarthat.


## <a name="install-the-backup-agent"></a>A biztonsági mentési ügynök telepítése

Minden olyan gép, amely biztonsági másolatot készít az Azure Backup a Backup-ügynök (más néven a Microsoft Azure Recovery Service-(MARS-) ügynök) telepítve kell rendelkeznie. Telepítse az ügynököt a DPM-kiszolgálón a következő:

1. Nyissa meg a tárolóban, amelyhez regisztrálja a DPM-kiszolgálót szeretne.
2. A **beállítások**, kattintson a **tulajdonságok**.

    ![A tárolók menüjének megnyitása](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Az a **tulajdonságok** lapon, az Azure Backup szolgáltatás ügynökének letöltése.

    ![Letöltés](./media/backup-azure-dpm-introduction/azure-backup-agent.png)


4. A letöltés után futtassa a MARSAgentInstaller.exe. az ügynök telepítéséhez a DPM-gépen.
5. Válassza ki a telepítési mappát, és a gyorsítótár mappája az ügynök számára. A gyorsítótár helye szabad területet kell lennie a biztonsági mentési adatok legalább 5 %-át.
6. Ha a proxykiszolgáló segítségével csatlakozzon az internethez, az a **proxykonfiguráció** képernyőn, adja meg a proxykiszolgáló adatai. Ha egy hitelesített proxyt használ, adja meg ezen a képernyőn a felhasználó felhasználónevének és jelszavának részleteit.
7. Az Azure Backup ügynököt telepíti a .NET-keretrendszer 4.5 és Windows PowerShell (Ha még nincs telepítve) a telepítés befejezéséhez.
8. Az ügynök telepítése után **Bezárás** az ablakban.

   ![Bezárás](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)


## <a name="register-the-dpm-server-in-the-vault"></a>A DPM-kiszolgálón regisztrálja a tárolóban

1. A DPM felügyeleti konzol > **felügyeleti**, kattintson a **Online**. Kattintson a **Register** (Regisztrálás) elemre. Kiszolgáló regisztrálása varázsló megnyílik.
2. A **proxykonfiguráció**, szükség szerint adja meg a proxybeállításokat.

    ![Proxy konfigurálása](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. A **Backup-tároló**, keresse meg és válassza ki a tároló hitelesítőadat-fájlja letöltött.

    ![A tároló hitelesítő adatai](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

10. A **sávszélesség-szabályozás beállítása**, engedélyezheti a sávszélesség-szabályozási biztonsági mentésekhez. Beállíthatja a sebességkorlátozás adja meg a munkaidőre és a nap.

    ![Sávszélesség-szabályozás beállítása](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. A **helyreállítási mappabeállításának**, adjon meg egy helyet, amely az adat-helyreállítás során is használható.

    - Az Azure Backup ideiglenes elképzelheti, ezt a helyet használja a helyreállított adatokkal.
    - Adat-helyreállítás befejezése után az Azure Backup fogja törölni az adatokat, ezen a területen.
    - A hely elegendő területtel rendelkezik, helyreállítása várhatóan párhuzamosan elemeket kell rendelkeznie.

    ![Helyreállítási mappa beállítás](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. A **titkosítási beállítás** létrehozni, vagy adjon meg egy jelszót.

    - A hozzáférési kódot a felhőbeli biztonsági mentések titkosításához használatos.
    - Adjon meg egy legalább 16 karakter.
    - Mentse a fájlt egy biztonságos helyre szükség esetén a helyreállítás.

    ![Titkosítás](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > A titkosítási jelszó a saját és a Microsoft nem rendelkezik, betekintést.
    > Ha a jelszó elvesztése vagy elfelejtése; A Microsoft nem segít a biztonsági mentési adatok helyreállítása.

13. Kattintson a **regisztrálása** a DPM-kiszolgáló a tárolóban való regisztrálásához.  


A kiszolgáló sikeresen való regisztrálása után a tárolóban, és most már készen áll indítsa el a biztonsági mentés a Microsoft Azure.

## <a name="troubleshoot-vault-credentials"></a>A tároló hitelesítő adatainak hibaelhárítása

### <a name="expiration-error"></a>Lejárati hiba

A tároló hitelesítőadat-fájlja csak esetén érvényes 48 óra (nincs letöltve a portálon). Ha ezt a képernyőt (például "tároló hitelesítő adatainak megadott fájlja lejárt"), jelentkezzen be az Azure portal és a tároló hitelesítő adatai fájlt újra letöltési hibákat tapasztal.

### <a name="access-error"></a>Hozzáférési hiba

Győződjön meg arról, hogy a tároló hitelesítőadat-fájlja érhető el olyan helyre, ahol a telepítő alkalmazás elérhető lesz. Ha eléréséhez kapcsolódó hibákat, másolja a tároló hitelesítőadat-fájlja egy ideiglenes helyre erre a gépre, és próbálja megismételni a műveletet.

### <a name="invalid-credentials-error"></a>Érvénytelen hitelesítő adatok hiba

Ha a tároló hitelesítőadat-hibát észlel (például "érvénytelen tár hitelesítő adatainak megadott") a fájl sérült, vagy nem nem rendelkezik a hozzárendelt legfrissebb hitelesítő adatokkal a szolgáltatásban.

- Próbálja megismételni a műveletet, miután letöltötte az új tároló hitelesítőadat-fájlja a portálról.
- Ez a hiba általában látható elemre a **letöltési Táralapú hitelesítőadat** beállítás kétszer a egymás után gyorsan az Azure Portalon. Ebben az esetben csak a második tároló hitelesítőadat-fájlja érvénytelen.
