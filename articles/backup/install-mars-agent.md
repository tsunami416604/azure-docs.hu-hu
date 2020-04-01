---
title: A Microsoft Azure Recovery Services (MARS) ügynök telepítése
description: Megtudhatja, hogy miként telepítheti a Microsoft Azure Recovery Services (MARS) ügynököt a Windows-gépek biztonsági mentéséhez.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: d3932b66dbc41ff2631e2cccbe716c0877a509d3
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422931"
---
# <a name="install-the-azure-backup-mars-agent"></a>Az Azure Backup MARS ügynök telepítése

Ez a cikk a Microsoft Azure Recovery Services (MARS) ügynök telepítését ismerteti. Mars is ismert, mint az Azure Backup ügynök.

## <a name="about-the-mars-agent"></a>A MARS-ügynökről

Az Azure Backup a MARS-ügynök segítségével biztonsági másolatot készít a fájlokról, mappákról és rendszerállapotról a helyszíni gépekről és az Azure-beli virtuális gépekről. Ezeket a biztonsági másolatokat az Azure-ban egy Recovery Services-tároló tárolja. Futtathatja az ügynököt:

* Közvetlenül a helyszíni Windows-gépeken. Ezek a gépek közvetlenül az Azure-ban a Recovery Services-tárolóba is biztonsági mentést kaphatnak.
* Az Azure virtuális gépek, amelyek a Windows fut egymás mellett az Azure virtuális gép biztonsági mentési bővítmény. Az ügynök biztonsági másolatot ad a virtuális gép adott fájljairól és mappáiról.
* Microsoft Azure Backup Server (MABS) példányon vagy System Center Data Protection Manager (DPM) kiszolgálón. Ebben a forgatókönyvben a gépek és a számítási feladatok biztonsági másolatot a MABS vagy az Adatvédelmi manager. Ezután a MABS vagy az Adatvédelmi kezelő a MARS-ügynök segítségével biztonsági másolatot ad egy azure-beli tárolóba.

A biztonsági mentéshez rendelkezésre álló adatok attól függnek, hogy az ügynök hol van telepítve.

> [!NOTE]
> Általában egy Azure virtuális gép biztonsági mentését egy Azure Backup bővítmény használatával a virtuális gép használatával. Ez a módszer biztonsági másolatot ad a teljes virtuális gépről. Ha a virtuális gép bizonyos fájljairól és mappáiról szeretne biztonsági másolatot fésülni, telepítse és használja a MARS-ügynököt a bővítmény mellett. További információ: [Egy beépített Azure virtuális gép biztonsági mentése.](backup-architecture.md#architecture-built-in-azure-vm-backup)

![Biztonsági mentési folyamat lépései](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Előkészületek

* Ismerje meg, hogy az Azure Backup hogyan [használja a MARS-ügynököt a Windows-gépek biztonsági mentéséhez.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Ismerje meg a MARS-ügynököt másodlagos MABS vagy Data Protection Manager kiszolgálón futó [biztonsági mentési architektúrát.](backup-architecture.md#architecture-back-up-to-dpmmabs)
* Tekintse [át, hogy mit támogat, és mit tud a](backup-support-matrix-mars-agent.md) MARS-ügynök.
* Győződjön meg arról, hogy rendelkezik egy Azure-fiókkal, ha biztonsági másolatot kell készítenie egy kiszolgálóról vagy ügyfélről az Azure-ba. Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/free/)
* Ellenőrizze az internet-hozzáférést azokon a gépeken, amelyekről biztonsági másolatot szeretne tenni.
* Győződjön meg arról, hogy a MARS-ügynök telepítését és konfigurálását végző felhasználó helyi rendszergazdai jogosultságokkal rendelkezik a védendő kiszolgálón.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Tárolóreplikáció módosítása

Alapértelmezés szerint a tárolók [georedundáns tárolást (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)használnak.

* Ha a tároló az elsődleges biztonsági mentési mechanizmus, azt javasoljuk, hogy grs használata.
* A [helyileg redundáns tárolás (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) használatával csökkentheti az Azure tárolási költségeit.

A tárolóreplikáció típusának módosítása:

1. Az új tárolóban válassza a **Tulajdonságok lehetőséget** a **Beállítások** szakaszban.

1. A **Tulajdonságok** lap **Biztonsági másolat beállítása területén**válassza a **Frissítés**lehetőséget.

1. Jelölje ki a tárolóreplikáció típusát, majd válassza a **Mentés gombot.**

    ![Biztonsági másolat konfigurációjának frissítése](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> A tároló beállítása után nem módosíthatja a tároló replikációs típusát, és biztonsági mentési elemeket tartalmaz. Ha ezt szeretné tenni, újra létre kell hoznia a tárolót.
>

### <a name="verify-internet-access"></a>Internet-hozzáférés ellenőrzése

Ha a számítógép korlátozott internet-hozzáféréssel rendelkezik, győződjön meg arról, hogy a tűzfal beállításai a számítógépen vagy a proxyn a következő URL-címeket és IP-címeket engedélyezik:

*  URL-címek
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* IP-címek
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Az Azure ExpressRoute használata

Az Azure ExpressRoute-on keresztül is biztonsági másolatot kaphat adatairól nyilvános társviszony-létesítés (a régi áramkörök számára elérhető) és a Microsoft-társviszony-létesítés használatával. A privát társviszony-létesítés biztonsági mentése nem támogatott.

A nyilvános társviszony-létesítés használatához először biztosítsa a hozzáférést a következő tartományokhoz és címekhez:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

A Microsoft társviszony-létesítésének használatához válassza ki a következő szolgáltatásokat, régiókat és a megfelelő közösségi értékeket:

* Azure Active Directory (12076:5060)
* Azure-régió, a Recovery Services-tároló helyének megfelelően
* Az Azure Storage, a Recovery Services-tároló helyének megfelelően

További információ: [ExpressRoute útválasztási követelmények](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

> [!NOTE]
> A nyilvános társviszony-létesítés elavult az új áramkörök esetében.

Az összes előző URL-cím és IP-cím a 443-as porton lévő HTTPS protokollt használja.

### <a name="private-endpoints"></a>Privát végpontok

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>A MARS-ügynök letöltése

Töltse le a MARS-ügynököt, hogy telepíthesse azon gépekre, amelyekről biztonsági másolatot szeretne.

Ha már telepítette az ügynököt bármelyik gépre, győződjön meg arról, hogy az ügynök legújabb verzióját futtatja. Keresse meg a legújabb verziót a portálon, vagy lépjen közvetlenül a [letöltés .](https://aka.ms/azurebackup_agent)

1. A tárolóelső **lépések**csoportban válassza a **Biztonsági másolat lehetőséget.**

    ![A biztonsági mentési cél megnyitása](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. A **Hol fut a számítási feladatok?** csoportban válassza a Helyszíni **lehetőséget.** Akkor is válassza ezt a lehetőséget, ha telepíteni szeretné a MARS-ügynököt egy Azure-beli virtuális gépre.
1. **A Milyen biztonsági másolatot szeretne tenni a biztonsági másolatok között,** válassza a Fájlok és mappák **lehetőséget.** Kiválaszthatja **a Rendszerállapot**lehetőséget is. Számos más lehetőség is rendelkezésre áll, de ezek a beállítások csak másodlagos biztonsági mentési kiszolgáló futtatása esetén támogatottak. Válassza **az Infrastruktúra előkészítése**lehetőséget.

    ![Fájlok és mappák konfigurálása](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Az **Infrastruktúra előkészítése**területen a Helyreállítási szolgáltatások telepítése **ügynök**csoportban töltse le a MARS-ügynököt.

    ![Az infrastruktúra előkészítése](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. A letöltési menüben válassza a **Mentés parancsot.** Alapértelmezés szerint az *MARSagentinstaller.exe* fájlt a rendszer a Downloads mappába menti.

1. Válassza a **Már töltse le vagy használja a legújabb Helyreállítási szolgáltatások ügynökét,** majd töltse le a tároló hitelesítő adatait.

    ![A tároló hitelesítő adatainak letöltése](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Kattintson a **Mentés** gombra. A fájl a Letöltések mappába töltődik le. A tároló hitelesítő adatait nem tudja megnyitni.

## <a name="install-and-register-the-agent"></a>Az ügynök telepítése és regisztrálása

1. Futtassa a *MARSagentinstaller.exe* fájlt azokon a gépeken, amelyekről biztonsági másolatot szeretne tenni.
1. A MARS ügynök telepítővarázslójában válassza a **Telepítési beállítások lehetőséget.** Itt válassza ki, hogy hol szeretné telepíteni az ügynököt, és válassza ki a gyorsítótár helyét. Ezután válassza a **Tovább**gombot.
   * Az Azure Backup a gyorsítótár segítségével tárolja az adatok pillanatképeit, mielőtt elküldené őket az Azure-ba.
   * A gyorsítótár helyének a biztonsági másolatot lehetővé tenni kívánt adatok méretének legalább 5 százalékával kell rendelkeznie.

    ![A TELEPÍTÉSI beállítások megadása a MARS Ügynök telepítővarázslójában](./media/backup-configure-vault/mars1.png)

1. A **Proxykonfiguráció csoportban**adja meg, hogy a Windows rendszerű számítógépen futó ügynök hogyan csatlakozzon az internethez. Ezután válassza a **Tovább**gombot.

   * Ha egyéni proxyt használ, adja meg a szükséges proxybeállításokat és hitelesítő adatokat.
   * Ne feledje, hogy az ügyintézőnek [bizonyos URL-címekhez kell hozzáférnie.](#before-you-start)

    ![Internet-hozzáférés beállítása a MARS varázslóban](./media/backup-configure-vault/mars2.png)

1. **Telepítés esetén**tekintse át az előfeltételeket, és válassza a **Telepítés**lehetőséget.
1. Az ügynök telepítése után válassza a **Folytatás a regisztrációhoz**lehetőséget.
1. A Kiszolgáló varázsló > **tárolójának azonosítása regisztrálása**csoportban keresse meg és jelölje ki a letöltött hitelesítő adatokat tartalmazó fájlt. **Register Server Wizard** Ezután válassza a **Tovább**gombot.

    ![Tároló hitelesítő adatainak hozzáadása a Kiszolgáló regisztrálása varázslóval](./media/backup-configure-vault/register1.png)

1. A **Titkosítás beállítása** lapon adja meg a számítógép biztonsági másolatainak titkosítására és visszafejtésére használt jelszót.

    * Mentse a jelszót biztonságos helyre. Szüksége van rá a biztonsági mentés visszaállításához.
    * Ha elveszíti vagy elfelejti a jelszót, a Microsoft nem tud segíteni a biztonsági mentési adatok helyreállításában.

1. Válassza a **Finish** (Befejezés) elemet. Az ügynök telepítve van, és a gép regisztrálva van a tárolóba. Készen áll a biztonsági mentés konfigurálására és ütemezésére.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [készíthet biztonsági másolatot a Windows-gépekről az Azure Backup MARS ügynök használatával](backup-windows-with-mars-agent.md)
