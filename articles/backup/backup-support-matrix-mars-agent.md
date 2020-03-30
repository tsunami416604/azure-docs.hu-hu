---
title: A MARS-ügynök támogatási mátrixa
description: Ez a cikk összefoglalja az Azure Backup támogatását a Microsoft Azure Recovery Services (MARS) ügynököt futtató gépek biztonsági mentésekor.
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 6085bc647c06b5907282460a2d8706b8549e1bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247864"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Támogatási mátrix a Microsoft Azure Recovery Services (MARS) ügynökkel való biztonsági mentéshez

Az Azure [Backup szolgáltatás](backup-overview.md) segítségével biztonsági másolatot készíthet a helyszíni gépekről és alkalmazásokról, valamint az Azure virtuális gépek (VM-ek) biztonsági mentéséhez. Ez a cikk összefoglalja a támogatási beállításokat és korlátozásokat, amikor a Microsoft Azure Recovery Services (MARS) ügynökkel biztonsági másolatot agépekről.

## <a name="the-mars-agent"></a>A MARS ügynök

Az Azure Backup a MARS-ügynök segítségével biztonsági másolatot készít a helyszíni gépekről és az Azure-beli virtuális gépekről az Azure biztonsági mentési helyreállítási szolgáltatás-tárolójából származó adatokról. A MARS-ügynök:

- A helyszíni Windows-gépeken futtatva közvetlenül az Azure-beli helyreállítási szolgáltatások tárolójának biztonsági mentéséhez készíthetők.
- Futtassa a Windows virtuális gépeken, hogy azok biztonsági másolatot közvetlenül a tárolóba.
- Futtassa a Microsoft Azure Backup Server (MABS) vagy a System Center Data Protection Manager (DPM) kiszolgálón. Ebben az esetben a gépek és a számítási feladatok biztonsági másolatot a MABS vagy a DPM-kiszolgáló. A MARS-ügynök ezután biztonsági másolatot akiszolgálóról egy azure-beli tárolóba.

> [!NOTE]
>Az Azure Backup nem támogatja az óra automatikus beállítását a nyári időszámításhoz (DST). Módosítsa a házirendet annak érdekében, hogy a rendszer figyelembe vegye a nyári időszámítást a tényleges idő és az ütemezett biztonsági mentési idő közötti eltérések elkerülése érdekében.

A biztonsági mentési beállítások attól függnek, hogy az ügynök hol van telepítve. További információ: [Azure Backup architektúra a MARS-ügynök használatával.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) A MABS és a DPM biztonsági mentési architektúrájáról a [DPM vagy az MABS biztonsági mentése](backup-architecture.md#architecture-back-up-to-dpmmabs)című témakörben talál további információt. Lásd még a biztonsági mentési architektúra [követelményeit.](backup-support-matrix-mabs-dpm.md)

**Telepítés** | **Részletek**
--- | ---
Töltse le a legújabb MARS-ügynököt | Letöltheti az ügynök legújabb verzióját a tárolóból, vagy [közvetlenül letöltheti.](https://aka.ms/azurebackup_agent)
Telepítés közvetlenül a gépre | A MARS-ügynököt közvetlenül telepítheti egy helyszíni Windows-kiszolgálóra vagy egy olyan Windows virtuális gépre, amely a [támogatott operációs rendszerek](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)bármelyikét futtatja.
Telepítés biztonsági másolat készítő kiszolgálón | Amikor úgy állítja be a DPM-et vagy a MABS-t, hogy biztonsági másolatot hozzon az Azure-ról, töltse le és telepítse a MARS-ügynököt a kiszolgálón. Az ügynököt a biztonsági másolat kiszolgáló támogatási mátrixában [lévő támogatott operációs rendszerekre](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) telepítheti.

> [!NOTE]
> Alapértelmezés szerint a biztonsági mentéshez engedélyezett Azure-virtuális gépek rendelkeznek egy Azure Backup bővítmény telepítésével. Ez a bővítmény biztonsági másolatot ad a teljes virtuális gépről. Telepítheti és futtathatja a MARS-ügynököt egy Azure virtuális gépen a bővítmény mellett, ha a teljes virtuális gép helyett bizonyos mappákról és fájlokról szeretne biztonsági másolatot fésülni.
> Amikor a MARS-ügynököt egy Azure-beli virtuális gépen futtatja, biztonsági másolatot avirtuális gép ideiglenes tárolójában lévő fájlokról vagy mappákról. A biztonsági mentések sikertelenek, ha a fájlokat vagy mappákat eltávolítják az ideiglenes tárolóból, vagy ha az ideiglenes tárolót eltávolítják.

## <a name="cache-folder-support"></a>Gyorsítótármappa támogatása

Amikor a MARS-ügynök segítségével biztonsági másolatot készít az adatokról, az ügynök pillanatképet készít az adatokról, és egy helyi gyorsítótármappában tárolja azadatokat, mielőtt az adatokat az Azure-ba küldenék. A gyorsítótármappának (scratch) több követelménye is van:

**Gyorsítótár** | **Részletek**
--- | ---
Méret |  A gyorsítótár-mappában a tartalékadatok teljes méretének legalább 5–10 százalékának kell lennie.
Hely | A gyorsítótár mappát helyileg kell tárolni azon a számítógépen, amelyről biztonsági másolatot kell tárolni, és online állapotban kell lennie. A gyorsítótármappa nem lehet hálózati megosztáson, cserélhető adathordozón vagy kapcsolat nélküli köteten.
Mappa | A gyorsítótármappát nem szabad duplikált köteten vagy tömörített, ritka vagy újraelemzési ponttal rendelkező mappában titkosítani.
Helyváltozások | A gyorsítótár helyét úgy módosíthatja,`net stop bengine`hogy leállítja a biztonsági mentési motort ( ) és a gyorsítótármappát egy új meghajtóra másolja. (Győződjön meg arról, hogy az új meghajtón elegendő hely áll rendelkezésre.) Ezután frissítsen két rendszerleíró bejegyzést a **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** és **Config/CloudBackupProvider/ScratchLocation**) alatt az új helyre, majd indítsa újra a motort.

## <a name="networking-and-access-support"></a>Hálózati és hozzáférési támogatás

### <a name="url-and-ip-access"></a>URL- és IP-hozzáférés

A MARS-ügynöknek hozzá kell férnie ezekhez az URL-címekhez:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net

És ezekre az IP címekre:

- 20.190.128.0/18
- 40.126.0.0/18

A fent felsorolt összes URL-címhez és IP-címhez való hozzáférés a 443-as porton lévő HTTPS protokollt használja.

### <a name="azure-expressroute-support"></a>Az Azure ExpressRoute támogatása

Az Azure ExpressRoute-on keresztül is biztonsági másolatot kaphat az adatokról nyilvános társviszony-létesítéssel (a régi áramkörök számára elérhető) és a Microsoft-társviszony-létesítéssel. A privát társviszony-létesítés biztonsági mentése nem támogatott.

Nyilvános társviszony-létesítés: Hozzáférés biztosítása a következő tartományokhoz/címekhez:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

A Microsoft társviszony-létesítésével válassza ki a következő szolgáltatásokat/régiókat és a megfelelő közösségi értékeket:

- Azure Active Directory (12076:5060)
- Microsoft Azure-régió (a Recovery Services-tároló helyének megfelelően)
- Azure Storage (a Recovery Services-tároló helyének megfelelően)

További információt az [ExpressRoute útválasztási követelményei című témakörben talál.](https://docs.microsoft.com/azure/expressroute/expressroute-routing)

>[!NOTE]
>A nyilvános társviszony-létesítés elavult az új áramkörök esetében.

### <a name="throttling-support"></a>Szabályozás támogatása

**Szolgáltatás** | **Részletek**
--- | ---
Sávszélesség-szabályozás | Támogatott. A MARS-ügynökben a **Tulajdonságok módosítása** segítségével állítsa be a sávszélességet.
Hálózati sávszélesség-szabályozás | Windows Server 2008 R2, Windows Server 2008 SP2 vagy Windows 7 rendszert futtató biztonsági másolatokon.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

>[!NOTE]
> A MARS-ügynök nem támogatja a Windows Server Core suk-okat.

A MARS-ügynök segítségével közvetlenül az Azure-ba biztonsági másolatot kaphat az alább felsorolt operációs rendszereken, amelyek a következőkön futnak:

1. Helyszíni Windows-kiszolgálók
2. Windows t futtató Azure-beli virtuális gépek

Az operációs rendszereknek 64 bitesnek kell lenniük, és a legújabb szolgáltatási csomagokat és frissítéseket kell futtatniuk. Az alábbi táblázat a következő operációs rendszereket foglalja össze:

**Operációs rendszer** | **Fájlok/mappák** | **Rendszerállapota** | **Szoftver/modul ra vonatkozó követelmények**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Igen | Nem |  Ellenőrizze a megfelelő kiszolgálói verziót a szoftver/modul követelményekmiatt
Windows 8.1 (Enterprise, Pro)| Igen |Nem | Ellenőrizze a megfelelő kiszolgálói verziót a szoftver/modul követelményekmiatt
Windows 8 (Enterprise, Pro) | Igen | Nem | Ellenőrizze a megfelelő kiszolgálói verziót a szoftver/modul követelményekmiatt
Windows Server 2016 (Normál, Adatközpont, Essentials) | Igen | Igen | - .NET 4.5 <br> - Windows PowerShell <br> - Legújabb kompatibilis Microsoft VC ++ terjeszthető <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (Normál, Adatközpont, Alaprendszer, Essentials) | Igen | Igen | - .NET 4.5 <br> - Windows PowerShell <br> - Legújabb kompatibilis Microsoft VC ++ terjeszthető <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Igen | Igen |- .NET 4.5 <br> -Windows PowerShell <br> - Legújabb kompatibilis Microsoft VC ++ terjeszthető <br> - Microsoft Management Console (MMC) 3.0 <br> - Telepítési lemezképek karbantartása és kezelése (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (normál, munkacsoport) | Igen | Nem | - .NET 4.5 <br> - Windows PowerShell <br> - Legújabb kompatibilis Microsoft VC ++ terjeszthető <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2019 (Normál, Adatközpont, Essentials) | Igen | Igen | - .NET 4.5 <br> - Windows PowerShell <br> - Legújabb kompatibilis Microsoft VC ++ terjeszthető <br> - Microsoft Management Console (MMC) 3.0

További információ: [Supported MABS and DPM operating systems](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

### <a name="operating-systems-at-end-of-support"></a>Operációs rendszerek a támogatás végén

A következő operációs rendszerek a támogatás végén vannak, és erősen ajánlott az operációs rendszer frissítése, hogy továbbra is védett ek maradjanak.

Ha a meglévő kötelezettségvállalások megakadályozzák az operációs rendszer frissítését, fontolja meg a Windows-kiszolgálók áttelepítését az Azure virtuális gépekre, és az Azure virtuális gépek biztonsági másolatainak kihasználásával továbbra is védett marad. Látogasson el az [áttelepítési lapra itt,](https://azure.microsoft.com/migration/windows-server/) ahol további információt talál a Windows-kiszolgáló áttelepítéséről.

A helyszíni vagy üzemeltetett környezetekben, ahol nem lehet frissíteni az operációs rendszert, vagy áttérni az Azure-ba, aktiválja a kiterjesztett biztonsági frissítéseket a gépek továbbra is védett és támogatott marad. Figyelje meg, hogy csak bizonyos kiadások jogosultak a kiterjesztett biztonsági frissítésekre. További információért látogasson el a [GYIK oldalra.](https://www.microsoft.com/cloud-platform/extended-security-updates)

| **Operációs rendszer**                                       | **Fájlok/mappák** | **Rendszerállapota** | **Szoftver/modul ra vonatkozó követelmények**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Végső, Nagyvállalati, Pro, Home Premium / Basic, Starter) | Igen               | Nem                 | Ellenőrizze a megfelelő kiszolgálói verziót a szoftver/modul követelményekmiatt |
| Windows Server 2008 R2 (Normál, Nagyvállalati, Adatközpont, Alaprendszer) | Igen               | Igen                | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Kompatibilis Microsoft VC ++ terjeszthető <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Telepítési lemezképek karbantartása és kezelése (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, Foundation)  | Igen               | Nem                 | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Kompatibilis Microsoft VC ++ terjeszthető <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Telepítési lemezképek karbantartása és kezelése (DISM.exe) <br>  - Virtuális szerver 2005 bázis + KB948515 |

## <a name="backup-limits"></a>Biztonsági mentési korlátok

### <a name="size-limits"></a>Méretkorlátok

Az Azure Backup korlátozza a fájl vagy mappa adatforrásának méretét, amelyről biztonsági másolat készíthető. Azok az elemek, amelyekről egyetlen kötetről biztonsági másolatot készül, nem haladhatják meg a táblázatban összesített méretet:

**Operációs rendszer** | **Méretkorlát**
--- | ---
Windows Server 2012 vagy újabb |54 400 GB
Windows Server 2008 R2 SP1 |1700 GB
Windows Server 2008 SP2| 1700 GB
Windows 8 vagy újabb| 54 400 GB
Windows 7| 1700 GB

### <a name="other-limitations"></a>Egyéb korlátozások

- A MARS nem támogatja több azonos nevű gép védelmét egyetlen tárolóban.

## <a name="supported-file-types-for-backup"></a>Támogatott fájltípusok biztonsági mentéshez

**Típus** | **Támogatás**
--- | ---
Titkosított<sup>*</sup>| Támogatott.
Tömörített | Támogatott.
Ritka | Támogatott.
Sűrített és ritka |Támogatott.
Rögzített hivatkozások| Nem támogatott. Kimarad.
Újraelemzési pont| Nem támogatott. Kimarad.
Titkosított és ritka |Nem támogatott. Kimarad.
Tömörített adatfolyam| Nem támogatott. Kimarad.
Ritka patak| Nem támogatott. Kimarad.
OneDrive (a szinkronizált fájlok ritka adatfolyamok)| Nem támogatott.
Olyan mappák, amelyeknél engedélyezve van az elosztott fájlrendszer replikációs szolgáltatása | Nem támogatott.

\*Győződjön meg arról, hogy a MARS-ügynök hozzáfér a titkosított fájlok eléréséhez szükséges tanúsítványokhoz. A nem elérhető fájlok at kimaradnak.

## <a name="supported-drives-or-volumes-for-backup"></a>Támogatott meghajtók vagy kötetek biztonsági mentéshez

**Meghajtó/hangerő** | **Támogatás** | **Részletek**
--- | --- | ---
Írásvédett kötetek| Nem támogatott | A Volume Copy Shadow Service (VSS) csak akkor működik, ha a kötet írható.
Kapcsolat nélküli kötetek| Nem támogatott |A VSS csak akkor működik, ha a kötet online állapotban van.
Hálózati megosztás| Nem támogatott |A kötetnek helyinek kell lennie a kiszolgálón.
BitLocker-zárolt kötetek| Nem támogatott |A biztonsági mentés megkezdése előtt fel kell oldani a kötet zárolását.
Fájlrendszer azonosítása| Nem támogatott |Csak az NTFS támogatott.
Cserélhető adathordozók| Nem támogatott |Minden biztonságimásolat-készítési elemforrásnak *rögzített* állapotúnak kell lennie.
Deduplikált meghajtók | Támogatott | Az Azure Backup konvertálja a duplikált adatokat normál adatokká. Optimalizálja, titkosítja, tárolja, és elküldi az adatokat a tárolóba.

## <a name="support-for-initial-offline-backup"></a>A kezdeti offline biztonsági mentés támogatása

Az Azure Backup támogatja az *offline vetés* t a kezdeti biztonsági mentési adatok átviteléhez az Azure lemezek használatával. Ez a támogatás akkor hasznos, ha a kezdeti biztonsági mentés valószínűleg a terabájtok (TB) mérettartományában van. Az offline biztonsági mentés a következő célokra támogatott:

- A MARS-ügynököt futtató helyszíni gépeken lévő fájlok és mappák közvetlen biztonsági mentése.
- Számítási feladatok és fájlok biztonsági mentése DPM-kiszolgálóról vagy MABS-ből.

Az offline biztonsági mentés nem használható rendszerállapot-fájlokhoz.

## <a name="support-for-data-restoration"></a>Az adatok visszaállításának támogatása

Az Azure Backup [azonnali visszaállítása](backup-instant-restore-capability.md) funkció használatával visszaállíthatja az adatokat, mielőtt azok a tárolóba másolnák. A biztonsági mentésre készülő gépnek a .NET Framework 4.5.2 vagy újabb rendszert kell futtatnia.

A biztonsági mentések nem állíthatók vissza olyan célszámítógépre, amely az operációs rendszer egy korábbi verzióját futtatja. A Windows 7 rendszert futtató számítógépről készített biztonsági másolat például visszaállítható Windows 8 vagy újabb rendszeren. A Windows 8 rendszert futtató számítógépről készített biztonsági másolat azonban nem állítható vissza windows 7 rendszert futtató számítógépen.

## <a name="next-steps"></a>További lépések

- További információ [a MARS-ügynököt használó biztonsági mentési architektúráról.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
- Ismerje meg, hogy mi támogatott, ha [a MARS-ügynököt MABS-en vagy DPM-kiszolgálón futtatja.](backup-support-matrix-mabs-dpm.md)
