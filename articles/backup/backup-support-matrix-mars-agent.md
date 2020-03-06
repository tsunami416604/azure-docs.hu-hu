---
title: A MARS-ügynök támogatási mátrixa
description: Ez a cikk a Microsoft Azure Recovery Services (MARS) ügynököt futtató gépek biztonsági mentésének Azure Backup támogatását foglalja össze.
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: ef57688dd7b5ccee4e71ac0a54138ac567320aa2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392764"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Támogatási mátrix a Microsoft Azure Recovery Services-(MARS-) ügynökkel való biztonsági mentéshez

A [Azure Backup szolgáltatás](backup-overview.md) segítségével biztonsági mentést készíthet a helyszíni gépekről és alkalmazásokról, valamint biztonsági mentést készíthet az Azure-beli virtuális gépekről. Ez a cikk összefoglalja a támogatási beállításokat és korlátozásokat, amikor a Microsoft Azure Recovery Services (MARS) ügynököt használja a gépek biztonsági mentéséhez.

## <a name="the-mars-agent"></a>A MARS-ügynök

Azure Backup a MARS-ügynök használatával készít biztonsági másolatot a helyszíni gépekről és az Azure-beli virtuális gépekről az Azure-beli biztonsági mentési Recovery Services-tárolóba. A MARS-ügynök a következőket teheti:

- A helyszíni Windows rendszerű gépeken futtathatók, így közvetlenül biztonsági mentést készíthetnek az Azure Backup Recovery Services-tárolójába.
- Futtassa a Windows rendszerű virtuális gépeken, hogy közvetlenül a tárolóba lehessen biztonsági mentést készíteni.
- Futtatás Microsoft Azure Backup-kiszolgálón (MABS) vagy a System Center Data Protection Manager (DPM) kiszolgálón. Ebben a forgatókönyvben a gépek és a számítási feladatok biztonsági mentést készítenek a MABS vagy a DPM-kiszolgálóra. A MARS-ügynök ezután biztonsági másolatot készít a kiszolgálóról egy Azure-tárolóba.

> [!NOTE]
>A Azure Backup nem támogatja az óra automatikus beállítását a nyári időszámításhoz (DST). Módosítsa úgy a szabályzatot, hogy a nyári időmegtakarítást figyelembe vegyék a tényleges idő és az ütemezett biztonsági mentés időbeli eltérésének megelőzése érdekében.

A biztonsági mentési lehetőségek attól függnek, hogy hol van telepítve az ügynök. További információ: [Azure Backup architektúra a Mars-ügynök használatával](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). A MABS és a DPM biztonsági mentési architektúrával kapcsolatos információkért lásd: [biztonsági mentés DPM vagy MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Lásd még a biztonsági mentési architektúra [követelményeit](backup-support-matrix-mabs-dpm.md) is.

**Telepítés** | **Részletek**
--- | ---
A legújabb MARS-ügynök letöltése | Az ügynök legújabb verzióját letöltheti a tárolóból, vagy [közvetlenül is letöltheti](https://aka.ms/azurebackup_agent).
Telepítés közvetlenül a gépen | A MARS-ügynököt közvetlenül egy helyszíni Windows Serverre vagy egy [támogatott operációs rendszert](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)futtató Windows rendszerű virtuális gépre is telepítheti.
Telepítés biztonsági mentési kiszolgálóra | A DPM vagy a MABS Azure-ba való biztonsági mentésének beállításakor letöltheti és telepítheti a MARS-ügynököt a kiszolgálón. A biztonsági mentési kiszolgáló támogatási mátrixában az ügynököt a [támogatott operációs rendszerekre](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) is telepítheti.

> [!NOTE]
> Alapértelmezés szerint a biztonsági mentésre engedélyezett Azure-beli virtuális gépekhez Azure Backup bővítmény telepítése szükséges. Ez a bővítmény biztonsági mentést készít a teljes virtuális gépről. A MARS-ügynököt telepítheti és futtathatja egy Azure-beli virtuális gépen a bővítmény mellett, ha a teljes virtuális gép helyett konkrét mappákról és fájlokról szeretne biztonsági mentést készíteni.
> Ha egy Azure-beli virtuális gépen futtatja a MARS-ügynököt, a biztonsági mentést készít a virtuális gépen lévő ideiglenes tárolóban található fájlokról vagy mappákról. A biztonsági mentések sikertelenek, ha a fájlok vagy mappák törlődnek az ideiglenes tárolóból, vagy ha az ideiglenes tároló el lett távolítva.

## <a name="cache-folder-support"></a>Gyorsítótár-mappák támogatása

Ha a MARS-ügynököt használja az adatok biztonsági mentésére, az ügynök pillanatképet készít az adatokról, és egy helyi gyorsítótár-mappában tárolja azt, mielőtt elküldi az adatokat az Azure-ba. A gyorsítótár (Scratch) mappája több követelményt is tartalmaz:

**Gyorsítótár** | **Részletek**
--- | ---
Méret |  A gyorsítótár mappában lévő szabad területnek legalább 5 – 10%-ának kell lennie a biztonsági mentési adatai teljes méretének.
Hely | A gyorsítótár mappájának helyileg kell lennie a biztonsági mentés alatt álló gépen, és online állapotban kell lennie. A gyorsítótár mappája nem lehet hálózati megosztáson, cserélhető adathordozón vagy offline köteten.
Mappa | A gyorsítótár mappája nem titkosítható deduplikált köteten, vagy egy tömörített mappában, amely ritka, vagy újraelemzési ponttal rendelkezik.
Hely változásai | A gyorsítótár helyét úgy módosíthatja, hogy leállítja a biztonsági mentési motort (`net stop bengine`), és átmásolja a gyorsítótár-mappát egy új meghajtóra. (Győződjön meg arról, hogy az új meghajtó elegendő lemezterülettel rendelkezik.) Ezután frissítsen két beállításjegyzékbeli bejegyzést a **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**config/ScratchLocation** és **config/CloudBackupProvider/ScratchLocation**) elemre az új helyre, és indítsa újra a motort.

## <a name="networking-and-access-support"></a>Hálózatkezelés és hozzáférés támogatása

### <a name="url-and-ip-access"></a>URL-cím és IP-hozzáférés

A MARS-ügynöknek hozzá kell férnie az alábbi URL-címekhez:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

És az alábbi IP-címekre:

- 20.190.128.0/18
- 40.126.0.0/18

A fentiekben felsorolt URL-címek és IP-címek elérése a HTTPS protokollt használja a 443-es porton.

### <a name="azure-expressroute-support"></a>Azure ExpressRoute-támogatás

Az Azure ExpressRoute-on keresztül biztonsági mentést készíthet az adatairól a nyilvános (a régi áramkörökhöz elérhető) és a Microsoft-partnerek számára. A privát kapcsolaton keresztüli biztonsági mentés nem támogatott.

Nyilvános társítás esetén: a következő tartományokhoz/címekhez való hozzáférés biztosítása:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

A Microsoft-partnerekkel válassza ki a következő szolgáltatásokat/régiókat és a vonatkozó közösségi értékeket:

- Azure Active Directory (12076:5060)
- Microsoft Azure régió (az Recovery Services-tároló helyének megfelelően)
- Azure Storage (az Recovery Services-tároló helyének megfelelően)

További részletekért tekintse meg a [ExpressRoute útválasztási követelményeit](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>Az új áramkörök esetében a nyilvános társítás elavult.

### <a name="throttling-support"></a>Szabályozás támogatása

**Funkció** | **Részletek**
--- | ---
Sávszélesség-vezérlés | Támogatott. A MARS-ügynökben a sávszélesség módosításához használja a **Tulajdonságok módosítása** lehetőséget.
Hálózati sávszélesség-szabályozás | Nem érhető el a Windows Server 2008 R2, a Windows Server 2008 SP2 vagy a Windows 7 rendszerű biztonsági másolattal rendelkező gépekhez.

## <a name="support-for-direct-backups"></a>Közvetlen biztonsági másolatok támogatása

>[!NOTE]
> A MARS-ügynök nem támogatja a Windows Server Core SKU-t.

A MARS-ügynök használatával biztonsági mentést készíthet közvetlenül az Azure-ra az alább felsorolt operációs rendszereken:

1. Helyszíni Windows-kiszolgálók
2. Windows rendszerű Azure-beli virtuális gépek

Az operációs rendszernek 64 bitesnek kell lennie, és a legújabb szervizcsomagokat és frissítéseket kell futtatnia. A következő táblázat összefoglalja ezeket az operációs rendszereket:

**Operációs rendszer** | **Fájlok/mappák** | **Rendszerállapot** | **Szoftver/modulra vonatkozó követelmények**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Igen | Nem |  A szoftver/modul követelményeinek megfelelő kiszolgálói verzió keresése
Windows 8,1 (Enterprise, Pro)| Igen |Nem | A szoftver/modul követelményeinek megfelelő kiszolgálói verzió keresése
Windows 8 (Enterprise, Pro) | Igen | Nem | A szoftver/modul követelményeinek megfelelő kiszolgálói verzió keresése
Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Igen | Nem | A szoftver/modul követelményeinek megfelelő kiszolgálói verzió keresése
Windows Server 2016 (Standard, Datacenter, Essentials) | Igen | Igen | -.NET 4,5 <br> – Windows PowerShell <br> – Legújabb kompatibilis Microsoft VC + + terjeszthető csomag <br> – Microsoft Management Console (MMC) 3,0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Igen | Igen | -.NET 4,5 <br> – Windows PowerShell <br> – Legújabb kompatibilis Microsoft VC + + terjeszthető csomag <br> – Microsoft Management Console (MMC) 3,0
Windows Server 2012 (Standard, Datacenter, Foundation) | Igen | Igen |-.NET 4,5 <br> – Windows PowerShell <br> – Legújabb kompatibilis Microsoft VC + + terjeszthető csomag <br> – Microsoft Management Console (MMC) 3,0 <br> -Telepítési lemezképek karbantartása és kezelése (DISM. exe)
Windows Storage Server 2016/2012 R2/2012 (standard, munkacsoport) | Igen | Nem | -.NET 4,5 <br> – Windows PowerShell <br> – Legújabb kompatibilis Microsoft VC + + terjeszthető csomag <br> – Microsoft Management Console (MMC) 3,0
Windows Server 2019 (standard, Datacenter, Essentials) | Igen | Igen | -.NET 4,5 <br> – Windows PowerShell <br> – Legújabb kompatibilis Microsoft VC + + terjeszthető csomag <br> – Microsoft Management Console (MMC) 3,0

További információ: [támogatott MABS és DPM operációs rendszerek](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Biztonsági mentési korlátok

### <a name="size-limits"></a>Blobméretének korlátjai

Azure Backup korlátozza a biztonsági mentésre használható fájl vagy mappa adatforrásának méretét. Az egyetlen kötetről készített biztonsági mentés nem lépheti túl az ebben a táblázatban összefoglalt méreteket:

**Operációs rendszer** | **Méretkorlát**
--- | ---
Windows Server 2012 vagy újabb |54 400 GB
Windows Server 2008 R2 SP1 |1 700 GB
Windows Server 2008 SP2| 1 700 GB
Windows 8 vagy újabb| 54 400 GB
Windows 7| 1 700 GB

### <a name="other-limitations"></a>Egyéb korlátozások

- A MARS nem támogatja több azonos nevű gép védelmét egyetlen tárolóban.

## <a name="supported-file-types-for-backup"></a>A biztonsági mentéshez támogatott fájltípusok

**Típus** | **Támogatás**
--- | ---
Titkosított| Támogatott.
Tömörített | Támogatott.
Ritka | Támogatott.
Tömörített és ritka |Támogatott.
Rögzített hivatkozások| Nem támogatott. Kimarad.
Újraelemzési pont| Nem támogatott. Kimarad.
Titkosított és ritka |Nem támogatott. Kimarad.
Tömörített adatfolyam| Nem támogatott. Kimarad.
Ritka adatfolyam| Nem támogatott. Kimarad.
OneDrive (a szinkronizált fájlok ritka adatfolyamok)| Nem támogatott.
Elosztott fájlrendszer replikációs szolgáltatása engedélyezett mappák | Nem támogatott.

## <a name="supported-drives-or-volumes-for-backup"></a>A biztonsági mentéshez támogatott meghajtók vagy kötetek

**Meghajtó/kötet** | **Támogatás** | **Részletek**
--- | --- | ---
Írásvédett kötetek| Nem támogatott | A kötet másolási árnyékmásolata szolgáltatás (VSS) csak akkor működik, ha a kötet írható.
Offline kötetek| Nem támogatott |A VSS csak akkor működik, ha a kötet online állapotban van.
Hálózati megosztás| Nem támogatott |A kötetnek helyinek kell lennie a kiszolgálón.
BitLocker által zárolt kötetek| Nem támogatott |A biztonsági mentés megkezdése előtt a kötetet fel kell oldani.
Fájlrendszer azonosítása| Nem támogatott |Csak az NTFS használata támogatott.
Cserélhető adathordozó| Nem támogatott |Az összes biztonságimásolat-tétel forrásának *rögzített* állapottal kell rendelkeznie.
Deduplikált meghajtók | Támogatott | Azure Backup a deduplikált adatmennyiséget a normál adatértékre konvertálja. Optimalizálja, titkosítja, tárolja és elküldi az adatokat a tárolónak.

## <a name="support-for-initial-offline-backup"></a>A kezdeti offline biztonsági mentés támogatása

Azure Backup támogatja az *Offline előkészítést* a kezdeti biztonsági mentési adatoknak az Azure-ba történő átviteléhez lemezek használatával. Ez a támogatás akkor hasznos, ha a kezdeti biztonsági mentés valószínűleg terabájt (TBs) tartományba esik. Az offline biztonsági mentés a következő módon támogatott:

- A fájlok és mappák közvetlen biztonsági mentése a MARS-ügynököt futtató helyszíni gépeken.
- Munkaterhelések és fájlok biztonsági mentése DPM-kiszolgálóról vagy MABS.

Az offline biztonsági mentés nem használható rendszerállapot-fájlokhoz.

## <a name="support-for-data-restoration"></a>Az Adathelyreállítás támogatása

Azure Backup [azonnali visszaállítási](backup-instant-restore-capability.md) funkciójával visszaállíthatja az adataikat a tárolóba való másolás előtt. A számítógépnek, amelyről biztonsági másolatot készít, .NET-keretrendszer 4.5.2-es vagy újabb verzióját kell futtatnia.

A biztonsági mentések nem állíthatók vissza olyan célszámítógépre, amely az operációs rendszer korábbi verzióját futtatja. Például egy Windows 7 rendszerű számítógépről készített biztonsági mentés visszaállítható a Windows 8 vagy újabb verzióra. A Windows 8 rendszert futtató számítógépekről azonban nem lehet visszaállítani a Windows 7 rendszert futtató számítógépeken.

## <a name="next-steps"></a>Következő lépések

- További információ [a Mars-ügynököt használó biztonsági mentési architektúráról](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Ismerje meg, hogy mi támogatott [a Mars-ügynök MABS vagy DPM-kiszolgálón való futtatásakor](backup-support-matrix-mabs-dpm.md).
