---
title: Az Azure Backup támogatási mátrixa
description: Az Azure Backup szolgáltatás támogatási beállításainak és korlátainak összegzése.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: b87430c9ff7d806fd1fc74d5d2a8270f9db43b53
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537321"
---
# <a name="support-matrix-for-azure-backup"></a>Támogatási mátrix az Azure Backup hoz

Az [Azure Backup](backup-overview.md) segítségével biztonsági másolatot készíthet az adatokról a Microsoft Azure felhőplatformjára. Ez a cikk összegzi az Azure Backup-forgatókönyvek és -telepítések általános támogatási beállításait és korlátait.

Egyéb támogatási mátrixok is rendelkezésre állnak:

- Az [Azure virtuális gép (VM) biztonsági mentésének](backup-support-matrix-iaas.md) támogatási mátrixa
- Biztonsági mentés támogatási mátrixa a [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md) használatával
- Biztonsági mentés támogatási mátrixa a [Microsoft Azure Recovery Services (MARS) ügynök](backup-support-matrix-mars-agent.md) használatával

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>A Vault támogatása

Az Azure Backup a Recovery Services-tárolók at használja a biztonsági mentések koordinálásához és kezeléséhez. Emellett tárolókat is használ a biztonsági mentésben lévő adatok tárolására.

Az alábbi táblázat a Recovery Services-tárolók szolgáltatásait ismerteti:

**Szolgáltatás** | **Részletek**
--- | ---
**Adattraszólók az előfizetésben** | Akár 500 Helyreállítási szolgáltatások tárolók egyetlen előfizetésben.
**Gépek egy boltozatban** | Akár 1000 Azure-beli virtuális gép egyetlen tárolóban.<br/><br/> Legfeljebb 50 MABS-kiszolgáló regisztrálható egyetlen tárolóban.
**Adatforrások** | Az egyes [adatforrások](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined) maximális mérete 54 400 GB. Ez a korlát nem vonatkozik az Azure virtuális gép biztonsági mentések. Nincs korlátozás vonatkozik a teljes mennyiségű adatot lehet biztonsági másolatot a tárolóba.
**Biztonsági mentések a tárolóba** | **Azure virtuális gépek:** Naponta egyszer.<br/><br/>**DPM/MABS által védett gépek:** Naponta kétszer.<br/><br/> **Közvetlenül a MARS-ügynök segítségével biztonsági másolatot készítettek:** Naponta háromszor.
**Biztonsági mentések a tárolók között** | A biztonsági mentés egy régión belül van.<br/><br/> Szüksége van egy tároló minden Azure-régióban, amely tartalmazza a virtuális gépeket szeretne biztonsági másolatot. Nem lehet biztonsági másolatot tartani egy másik régióban.
**Páncéltermek áthelyezése** | A [tárolók között](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) előfizetések vagy erőforráscsoportok ugyanabban az előfizetésben. A tárolók régiók közötti áthelyezése azonban nem támogatott.
**Adatok áthelyezése tárolók között** | A biztonsági másolatok közötti adatok áthelyezése a tárolók között nem támogatott.
**Tároló tárolási típusának módosítása** | A biztonsági mentések tárolása előtt módosíthatja a tároló replikációs típusát (georedundáns vagy helyileg redundáns tárolás). Miután a biztonsági mentések megkezdődtek a tárolóban, a replikáció típusa nem módosítható.

## <a name="on-premises-backup-support"></a>Helyszíni biztonsági mentés támogatása

A helyszíni gépek biztonsági csinálása esetén az alábbiakat támogatja:

**Gép** | **Mi ről van biztonsági másolatom?** | **Helyen** | **Szolgáltatások**
--- | --- | --- | ---
**A Windows-gép közvetlen biztonsági mentése marsügynökkel** | Fájlok, mappák, rendszerállapot | Biztonsági másolatot a Recovery Services tárolójának. | Biztonsági másolatot (biztonsági másolatot) naponta háromszor<br/><br/> Nincs alkalmazásbarát biztonsági mentés<br/><br/> Fájl, mappa, kötet visszaállítása
**A Linux-gép közvetlen biztonsági mentése a MARS ügynökkel** | A biztonsági mentés nem támogatott
**Biztonsági másolatot a DPM-hez** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonsági másolatot a helyi DPM-tárolóhoz. A DPM ezután biztonsági másolatot ad a tárolóba. | Alkalmazásbarát pillanatképek<br/><br/> Teljes részletesség a biztonsági mentéshez és helyreállításhoz<br/><br/> Virtuális gépekhez támogatott Linux (Hyper-V/VMware)<br/><br/> Az Oracle nem támogatott
**Biztonsági másolatot a MABS-hez** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonsági másolatot a MABS helyi tárolójához. A MABS ezután biztonsági másolatot tart a páncélterembe. | Alkalmazásbarát pillanatképek<br/><br/> Teljes részletesség a biztonsági mentéshez és helyreállításhoz<br/><br/> Virtuális gépekhez támogatott Linux (Hyper-V/VMware)<br/><br/> Az Oracle nem támogatott

## <a name="azure-vm-backup-support"></a>Az Azure VM biztonsági mentésének támogatása

### <a name="azure-vm-limits"></a>Az Azure virtuális gép korlátai

**Korlát** | **Részletek**
--- | ---
**Azure virtuálisgép-adatlemezek** | 16-os korlát <br> Ha regisztrálni szeretne a 16-nál több (legfeljebb 32) adatlemezzel rendelkező virtuális gépek privát előzetes verziójára, írjon nekünk a következő címre: AskAzureBackupTeam@microsoft.com
**Az Azure Virtuálisgép-adatlemez mérete** | Az egyes lemezek mérete legfeljebb 32 TB és legfeljebb 256 TB kombinált összes lemez egy virtuális gép.

### <a name="azure-vm-backup-options"></a>Az Azure virtuális gép biztonsági mentési beállításai

Az alábbiakban támogatja az Azure-beli virtuális gépekbiztonsági biztonsági való leépítést:

**Gép** | **Mi ről van biztonsági másolatom?** | **Helyen** | **Szolgáltatások**
--- | --- | --- | ---
**Az Azure virtuálisgép biztonsági mentése a Virtuálisgép-bővítmény használatával** | Teljes virtuális gép | Vissza a páncélterembe. | A bővítmény akkor lett telepítve, ha engedélyezi a virtuális gép biztonsági mentését.<br/><br/> Biztonsági másolatot!<br/><br/> Alkalmazásbarát biztonsági mentés Windows virtuális gépekhez; fájlkonzisztens biztonsági mentés linuxos virtuális gépekhez. A Linux-gépek hez egyéni parancsfájlok használatával konfigurálhatja az alkalmazáskonzisztenciát.<br/><br/> Virtuális gép vagy lemez visszaállítása.<br/><br/> Nem lehet biztonsági másolatot egy Azure-virtuális gép egy helyszíni helyre.
**Az Azure Virtuálisgép biztonsági mentése a MARS-ügynök használatával** | Fájlok, mappák, rendszerállapot | Vissza a páncélterembe. | Biztonsági másolatot egy nap háromszor.<br/><br/> Ha a teljes virtuális gép helyett a teljes virtuális gép helyett bizonyos fájlokról vagy mappákról szeretne biztonsági másolatot, a MARS-ügynök futtathatja a virtuális gép bővítménye mellett.
**Az Azure virtuális gépe A DPM** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonsági másolatot a DPM-et futtató Azure virtuális gép helyi tárhelyéről. A DPM ezután biztonsági másolatot ad a tárolóba. | Alkalmazásbarát pillanatképek.<br/><br/> Teljes részletesség a biztonsági mentéshez és a helyreállításhoz.<br/><br/> A Virtuális gépek (Hyper-V/VMware) által támogatott Linux.<br/><br/> Az Oracle nem támogatott.
**Azure virtuális gép MABS-szel** | Fájlok, mappák, kötetek, rendszerállapot, alkalmazásadatok | Biztonsági másolatot a MABS-t futtató Azure virtuális gép helyi tárhelyéről. A MABS ezután biztonsági másolatot tart a páncélterembe. | Alkalmazásbarát pillanatképek.<br/><br/> Teljes részletesség a biztonsági mentéshez és a helyreállításhoz.<br/><br/> A Virtuális gépek (Hyper-V/VMware) által támogatott Linux.<br/><br/> Az Oracle nem támogatott.

## <a name="linux-backup-support"></a>Linux biztonsági mentés támogatása

A linuxos gépek biztonsági csinálása esetén az alábbiaktámogatottak:

**Biztonsági másolat típusa** | **Linux (az Azure jóváhagyva)**
--- | ---
**A Linuxot futtató helyszíni gép közvetlen biztonsági mentése** | Nem támogatott. A MARS-ügynök csak Windows-gépekre telepíthető.
**Ügynökbővítmény használata a Linuxot futtató Azure Virtuális gép biztonsági rendszerének biztonsági elégítéséhez** | Alkalmazáskonzisztens biztonsági mentés [egyéni parancsfájlok](backup-azure-linux-app-consistent.md)használatával.<br/><br/> Fájlszintű helyreállítás.<br/><br/> Állítsa vissza egy virtuális gép létrehozásával egy helyreállítási pontvagy lemez.
**A DPM használata linuxos helyszíni gépek biztonsági rendszerének biztonsági leéséhez** | A Linux vendég virtuális gépek fájlkonzisztens biztonsági mentése a Hyper-V és a VMWare rendszeren.<br/><br/> A Hyper-V és a VMWare Linux vendégvirtuális gépek virtuális gépének helyreállítása.
**A MABS használata a Linuxot futtató helyszíni gépek biztonsági rendszerének biztonsági leéséhez** | A Linux vendég virtuális gépek fájlkonzisztens biztonsági mentése a Hyper-V és a VMWare rendszeren.<br/><br/> A Hyper-V és a VMWare Linux vendég virtuális gépek virtuális gépeinek helyreállítása.
**Linuxos Azure-virtuális gépek biztonsági másolatot ad az MABS vagy a DPM szolgáltatásról** | Nem támogatott.

## <a name="daylight-saving-time-support"></a>Nyári időszámítás támogatása

Az Azure Backup nem támogatja az automatikus órajel-beállítást az Azure virtuális gépek biztonsági mentéséhez a nyári időszámításhoz. Nem tolja előre vagy hátra a biztonsági mentés óráját. Annak érdekében, hogy a biztonsági mentés a kívánt időben futjon, szükség szerint manuálisan módosítsa a biztonsági mentési házirendeket.

## <a name="disk-deduplication-support"></a>Lemezdeduplikáció támogatása

A lemezdeduplikáció támogatása a következő:

- A lemezdeduplikáció a helyszínen támogatott, ha a DPM vagy az MABS használatával biztonsági másolatot készíteni a Windows rendszert futtató Hyper-V virtuális gépekről. A Windows Server adatdeduplikációt hajt végre (állomásszinten) a virtuális géphez biztonsági másolatként csatlakoztatott virtuális merevlemezeken (VD-k).
- A deduplikáció nem támogatott az Azure-ban egyetlen biztonsági mentési összetevő esetében sem. Amikor a DPM és a MABS telepítve van az Azure-ban, a virtuális géphez csatlakoztatott tárolólemezek nem lehet duplikálni.

## <a name="security-and-encryption-support"></a>Biztonsági és titkosítási támogatás

Az Azure Backup támogatja a titkosítást az átvitel közbeni és az inaktív adatokhoz.

### <a name="network-traffic-to-azure"></a>Hálózati forgalom az Azure-ba

- A kiszolgálókról a Recovery Services tárolóba irányuló biztonsági mentési forgalmat a Speciális titkosítási szabvány 256 használatával titkosítja.
- A biztonsági mentési adatok küldése biztonságos HTTPS-kapcsolaton keresztül történik.
- A biztonsági mentési adatok at a Recovery Services tárolója titkosított formában tárolja.
- Csak önnek van meg a jelmondata az adatok feloldásához. A Microsoft nem tudja visszafejteni a biztonsági mentési adatokat.

    > [!WARNING]
    > A tároló beállítása után csak ön férhet hozzá a titkosítási kulcshoz. A Microsoft soha nem tart fenn másolatot, és nem fér hozzá a kulcshoz. Ha a kulcs rossz helyre került, a Microsoft nem tudja helyreállítani a biztonsági mentési adatokat.

### <a name="data-security"></a>Adatbiztonság

- Amikor az Azure virtuális gépek biztonsági mentésekor a virtuális gépen *belül* titkosítást kell beállítania.
- Az Azure Backup támogatja az Azure Disk Encryption szolgáltatást, amely a BitLockert használja Windows rendszerű virtuális gépeken és a **dm-crypt**-et Linux rendszerű virtuális gépeken.
- A háttérben az Azure Backup az [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md)szolgáltatást használja, amely védi az inaktív adatokat.

**Gép** | **Szállítás közben** | **Nyugalmi**
--- | --- | ---
**Helyszíni Windows-gépek DPM/MABS nélkül** | ![Igen][green] | ![Igen][green]
**Azure-beli virtuális gépek** | ![Igen][green] | ![Igen][green]
**Helyszíni Windows-gépek vagy Azure-beli virtuális gépek DPM-mel** | ![Igen][green] | ![Igen][green]
**Helyszíni Windows-gépek vagy MABS-rel rendelkező Azure-virtuális gépek** | ![Igen][green] | ![Igen][green]

## <a name="compression-support"></a>Tömörítés támogatása

A biztonsági mentés támogatja a biztonsági mentési forgalom tömörítését az alábbi táblázatban összefoglalva.

- Az Azure virtuális gépek, a virtuális gép bővítmény beolvassa az adatokat közvetlenül az Azure storage-fiók a tárolóhálózaton keresztül, így nem szükséges a forgalom tömörítése.
- Ha DPM-et vagy MABS-t használ, az adatok biztonsági mentése előtt csökkentheti a sávszélességet.

**Gép** | **Tömörítés MABS-be/DPM-be (TCP)** | **Tömörítés a tárolóba (HTTPS)**
--- | --- | ---
**A helyszíni Windows-gépek közvetlen biztonsági mentése** | NA | ![Igen][green]
**Az Azure virtuális gépek biztonsági mentése a virtuálisgép-bővítmény használatával** | NA | NA
**Biztonsági mentés helyszíni/Azure-alapú gépeken mabs/DPM használatával** | ![Igen][green] | ![Igen][green]

## <a name="retention-limits"></a>Megőrzési korlátok

**Beállítás** | **Korlátok**
--- | ---
**Maximális helyreállítási pontok védett példányonként (gép vagy munkaterhelés)** | 9,999
**Maximális lejárati idő egy helyreállítási ponthoz** | Nincs korlátozás
**Maximális biztonsági mentési gyakoriság A DPM/MABS** | 15 percenként az SQL Serverhez<br/><br/> Óránként egyszer más számítási feladatok esetén
**Maximális biztonsági mentési gyakoriság a tárolóba** | **Helyszíni Windows-gépek vagy MARS-ot futtató Azure-virtuális gépek:** Három naponta<br/><br/> **DPM/MABS:** Naponta kettő<br/><br/> **Az Azure virtuális gép biztonsági mentése:** Naponta egy
**Helyreállítási pont megőrzése** | Napi, heti, havi, éves
**Maximális megőrzési időtartam** | A biztonsági mentés gyakoriságától függően változik
**Helyreállítási pontok A DPM/MABS lemezen** | 64 fájlkiszolgálók esetén; 448 alkalmazáskiszolgálók esetén <br/><br/>Korlátlan szalagos helyreállítási pontok a helyszíni DPM-hez

## <a name="cross-region-restore"></a>Régiók közötti visszaállítás

Az Azure Backup hozzáadta a Régióközi visszaállítás funkciót az adatok rendelkezésre állásának és rugalmassági képességének megerősítéséhez, így az ügyfelek teljes körű enciklopédiát biztosítanak az adatok másodlagos régióba való visszaállításához. A szolgáltatás konfigurálásához látogasson el [a Régiók közötti visszaállítás beállítása című cikkben.](backup-create-rs-vault.md#set-cross-region-restore). . Ez a szolgáltatás a következő felügyeleti típusok esetén támogatott:

| Biztonsági másolat kezelése típusa | Támogatott                                                    | Támogatott régiók |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Igen.   4 TB-nál kisebb lemezekkel rendelkező titkosított virtuális gépek és virtuális gépek esetén támogatott | Az összes Azure nyilvános régió.  |
| MARS ügynök/Telephelyen | Nem                                                           | N/A               |
| SQL /SAP HANA          | Nem                                                           | N/A               |
| Afs                    | Nem                                                           | N/A               |

## <a name="next-steps"></a>További lépések

- [Tekintse át](backup-support-matrix-iaas.md) az Azure virtuális gépek biztonsági mentésének támogatási mátrixát.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
