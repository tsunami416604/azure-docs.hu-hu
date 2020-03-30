---
title: Azure-beli virtuális gépek biztonsági mentésének támogatási mátrixa
description: A támogatási beállítások és korlátozások összefoglalása az Azure-beli virtuális gépek nek az Azure Backup szolgáltatással való biztonsági mentésekor.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: d86ce94c62ec9f25b364e9fdc963e3043b274722
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389290"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure-beli virtuális gépek biztonsági mentésének támogatási mátrixa

Az Azure [Backup szolgáltatás](backup-overview.md) segítségével biztonsági másolatot készíthet a helyszíni gépekről és számítási feladatokról, valamint az Azure virtuális gépeiről (VM-ek). Ez a cikk összefoglalja a támogatási beállításokat és korlátozásokat, amikor az Azure-beli virtuális gépekről biztonsági másolatot készít az Azure Backup szolgáltatással.

Egyéb támogatási mátrixok:

- [Általános támogatási mátrix](backup-support-matrix.md) az Azure Backup hoz
- [Támogatási mátrix](backup-support-matrix-mabs-dpm.md) az Azure Backup server/System Center Data Protection Manager (DPM) biztonsági mentéséhez
- [Támogatási mátrix](backup-support-matrix-mars-agent.md) a Microsoft Azure Recovery Services (MARS) ügynökkel való biztonsági mentéshez

## <a name="supported-scenarios"></a>Támogatott esetek

Így készíthet biztonsági másolatot és állíthat vissza azure-beli virtuális gépekről az Azure Backup szolgáltatással.

**Forgatókönyv** | **Biztonsági mentés** | **Ügynök** |**Visszaállítás**
--- | --- | --- | ---
Az Azure virtuális gépeinek közvetlen biztonsági mentése  | Biztonsági másolatot a teljes virtuális gépről.  | Nincs szükség további ügynökre az Azure virtuális gép. Az Azure Backup telepíti és használja az [Azure Virtuálisgép-ügynök,](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) amely a virtuális gép fut egy bővítményt. | Visszaállítás az alábbiak szerint:<br/><br/> - **Hozzon létre egy egyszerű virtuális gép.** Ez akkor hasznos, ha a virtuális gép nem rendelkezik speciális konfigurációval, például több IP-címmel.<br/><br/> - **Állítsa vissza a virtuális gép lemezét**. Állítsa vissza a lemezt. Ezután csatolja egy meglévő virtuális géphez, vagy hozzon létre egy új virtuális gép a lemezről a PowerShell használatával.<br/><br/> - **VM-lemez cseréje**. Ha egy virtuális gép létezik, és felügyelt lemezeket használ (titkosítatlan), visszaállíthatja a lemezt, és a virtuális gép meglévő lemezének cseréjére használhatja.<br/><br/> - **Adott fájlok/mappák visszaállítása.** A fájlokat/mappákat a virtuális géphelyett a virtuális gépről állíthatja vissza.
Az Azure virtuális gépek közvetlen biztonsági mentése (csak Windows)  | Adott fájlok/mappák/kötetek biztonsági másolatot kell kapnia. | Telepítse az [Azure Recovery Services ügynökét.](backup-azure-file-folder-backup-faq.md)<br/><br/> Futtathatja a MARS-ügynök mellett a biztonsági mentési bővítmény az Azure virtuálisgép-ügynök biztonsági másolatot készíteni a virtuális gép fájl/mappa szintjén. | Adott mappák/fájlok visszaállítása.
Az Azure Virtuális gép biztonsági mentési kiszolgálóra való biztonsági mentése  | Fájlok/mappák/kötetek biztonsági és biztonsági és biztonsági üzemben tartó biztonsági üzemének felelmeg. rendszerállapot/csupasz fémfájlok; az alkalmazásadatokat a System Center DPM vagy a Microsoft Azure Backup Server (MABS) rendszerbe.<br/><br/> A DPM/MABS ezt követően biztonsági másolatot készít a biztonsági mentési tárolóba. | Telepítse a DPM/MABS védelmi ügynököt a virtuális gépre. A MARS-ügynök telepítve van a DPM/MABS-en.| Fájlok/mappák/kötetek visszaállítása; rendszerállapot/csupasz fémfájlok; alkalmazásadatokat.

További információ a [biztonsági mentésről a biztonsági mentés kiszolgálóhasználatával](backup-architecture.md#architecture-back-up-to-dpmmabs) és a [támogatási követelményekről.](backup-support-matrix-mabs-dpm.md)

>[!NOTE]
> **Az Azure Backup mostantól támogatja a szelektív lemezbiztonsági mentést és visszaállítást az Azure virtuális gép biztonsági mentési megoldásával.**
>
>Az Azure Backup ma támogatja az összes lemez (operációs rendszer és adatok) biztonsági mentését a virtuális gépben a virtuális gép biztonsági mentési megoldásának használatával. A exclude-disk funkcióval lehetőséget kap arra, hogy egy vagy néhány biztonsági másolatot készítsen a virtuális gép számos adatlemezéről. Ez hatékony és költséghatékony megoldást nyújt a biztonsági mentési és visszaállítási igényekhez. Minden helyreállítási pont a biztonsági mentési műveletben szereplő lemezek adatait tartalmazza, ami lehetővé teszi, hogy a lemezek egy részhalmaza visszaálljon az adott helyreállítási pontról a visszaállítási művelet során. Ez a pillanatképből és a tárolóból történő visszaállításra vonatkozik.
>
>**Az előnézetre való feliratkozáshoz írjon nekünk aAskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>Támogatott biztonsági mentési műveletek

**Művelet** | **Támogatás**
--- | ---
Leállítási/offline módban elérhető virtuális gép biztonsági és biztonsági őle | Támogatott.<br/><br/> A pillanatkép csak összeomlás-konzisztens, nem alkalmazáskonzisztens.
Lemezek biztonsági és biztonsági és biztonsági másolatot készíteni a felügyelt lemezekre való áttelepítés után | Támogatott.<br/><br/> A biztonsági mentés továbbra is működni fog. Semmit nem kell tenni.
Felügyelt lemezek biztonsági és biztonsági másolatot készíteni az erőforráscsoport zárolásának engedélyezése után | Nem támogatott.<br/><br/> Az Azure Backup nem tudja törölni a régebbi visszaállítási pontokat, és a biztonsági mentések sikertelenlesz, ha eléri a maximális visszaállítási pontok at.
Virtuális gép biztonsági mentési házirendjének módosítása | Támogatott.<br/><br/> A virtuális gép biztonsági másolatot kap az ütemezési és megőrzési beállítások használatával az új szabályzatban. Ha a megőrzési beállítások meg vannak hosszabbodva, a meglévő helyreállítási pontok meg vannak jelölve és megmaradnak. Ha csökkennek, a meglévő helyreállítási pontok a következő tisztítási feladatban lesznek kimetszésben, és végül törlődnek.
Biztonsági mentési feladat megszakítása| A pillanatkép folyamata során támogatott.<br/><br/> Nem támogatott, ha a pillanatkép átvitele a tárolóba.
A virtuális gép biztonsági és biztonsági másolatot kell adnia egy másik régióba vagy előfizetésbe |Nem támogatott.
Biztonsági mentések naponta (az Azure VM-bővítményen keresztül) | Naponta egy ütemezett biztonsági mentés.<br/><br/>Az Azure Backup szolgáltatás naponta legfeljebb kilenc igény szerinti biztonsági mentést támogat, de a Microsoft legfeljebb négy napi igény szerinti biztonsági mentést javasol a legjobb teljesítmény biztosítása érdekében.
Napi biztonsági mentések (a MARS-ügynökön keresztül) | Három ütemezett biztonsági mentés naponta.
Napi biztonsági mentések (DPM/MABS-en keresztül) | Naponta két ütemezett biztonsági mentés.
Havi/éves biztonsági mentés| Nem támogatott, ha az Azure VM-bővítmény biztonsági mentése. Csak naponta és hetente támogatott.<br/><br/> Beállíthatja a szabályzatot a napi/heti biztonsági mentések megtartásához a havi/éves megőrzési időszakra.
Automatikus órajelbeállítás | Nem támogatott.<br/><br/> Az Azure Backup nem módosítja automatikusan a nyári időszámítás változásait a virtuális gépek biztonsági mentésekor.<br/><br/>  Szükség szerint manuálisan módosítsa a házirendet.
[Biztonsági funkciók a hibrid biztonsági mentéshez](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |A biztonsági szolgáltatások letiltása nem támogatott.
Biztonsági másolatot kell kapnia a gépidővel módosító virtuális gépről | Nem támogatott.<br/><br/> Ha a gép idő változik egy jövőbeli dátum-idő után, amely lehetővé teszi a biztonsági mentést, hogy a virtuális gép; Azonban még akkor is, ha az időváltozás visszaáll, a sikeres biztonsági mentés nem garantált.  

## <a name="operating-system-support-windows"></a>Az operációs rendszer támogatása (Windows)

Az alábbi táblázat összefoglalja a windows Azure virtuális gépek biztonsági mentésekor a támogatott operációs rendszereket.

**Forgatókönyv** | **Operációs rendszer támogatása**
--- | ---
Biztonsági másolatot az Azure Virtuálisgép-ügynök bővítményével | - Windows 10 ügyfél (csak 64 bites) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Adatközpont/Standard) <br/><br/> - Windows Server 2008 R2 (RTM és SP1 szabvány)  <br/><br/> - Windows Server 2008 (csak 64 bit)
Biztonsági másolatot a MARS-ügynökkel | [Támogatott](backup-support-matrix-mars-agent.md#supported-operating-systems) operációs rendszerek.
Biztonsági másolatot a DPM/MABS-szel | Támogatott operációs rendszerek a [MABS](backup-mabs-protection-matrix.md) és a DPM biztonsági [mentéséhez.](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807)

Az Azure Backup nem támogatja a 32 bites operációs rendszereket.

## <a name="support-for-linux-backup"></a>Linux biztonsági mentés támogatása

Itt van, mi támogatott, ha azt szeretné, hogy biztonsági másolatot a Linux-gépek.

**Művelet** | **Támogatás**
--- | ---
Linuxos Azure-beli virtuális gépek biztonsági és biztonsági másolatot ( | Fájlkonzisztens biztonsági mentés.<br/><br/> Alkalmazáskonzisztens biztonsági mentés [egyéni parancsfájlok](backup-azure-linux-app-consistent.md)használatával.<br/><br/> A visszaállítás során létrehozhat egy új virtuális gép, visszaállíthatja a lemezt, és használja a virtuális gép létrehozásához, vagy visszaállítani egy lemezt, és használja a lemez cseréje egy meglévő virtuális gép. Az egyes fájlokat és mappákat is visszaállíthatja.
Linuxos Azure-virtuális gépek biztonsági és biztonsági másolatot ( MARS-ügynök) | Nem támogatott.<br/><br/> A MARS-ügynök csak Windows-gépekre telepíthető.
Linuxos Azure-virtuális gépek biztonsági és biztonsági másolatot ( DPM/MABS) használatával | Nem támogatott.

## <a name="operating-system-support-linux"></a>Operációs rendszer támogatása (Linux)

Az Azure VM Linux biztonsági mentések esetén az Azure Backup támogatja az [Azure által jóváhagyott Linux-disztribúciók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)listáját. Vegye figyelembe a következőket:

- Az Azure Backup nem támogatja a Core OS Linuxot.
- Az Azure Backup nem támogatja a 32 bites operációs rendszereket.
- Más bring-your-own Linux disztribúciók működhet, amíg az [Azure VM ügynök Linux elérhető](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) a virtuális gép, és mindaddig, amíg a Python támogatott.
- Az Azure Backup nem támogatja a proxy által konfigurált Linux virtuális gép, ha nem rendelkezik python 2.7-es verzió telepítve.

## <a name="backup-frequency-and-retention"></a>Biztonsági mentés gyakorisága és megőrzése

**Beállítás** | **Korlátok**
--- | ---
Maximális helyreállítási pontok védett példányonként (gép/munkaterhelés) | 9999.
A helyreállítási pont maximális lejárati ideje | Nincs határ.
A tároló maximális biztonsági mentési gyakorisága (Azure VM-bővítmény) | Naponta egyszer.
Maximális tartalék gyakoriság a tárolóba (MARS ügynök) | Három biztonsági mentés naponta.
Maximális biztonsági mentési gyakoriság A DPM/MABS | 15 percenként az SQL Server esetében.<br/><br/> Óránként egyszer más számítási feladatok.
Helyreállítási pont megőrzése | Napi, heti, havi és éves.
Maximális megőrzési időtartam | A biztonsági mentés gyakoriságától függ.
Helyreállítási pontok A DPM/MABS lemezen | 64 fájlkiszolgálók esetén, 448 pedig alkalmazáskiszolgálók esetén.<br/><br/> A szalagos helyreállítási pontok korlátlanok a helyszíni DPM esetén.

## <a name="supported-restore-methods"></a>Támogatott visszaállítási módszerek

**Visszaállítási beállítás** | **Részletek**
--- | ---
**Hozzon létre egy új virtuális gépet** | Gyorsan létrehoz egy egyszerű virtuális gép, és fut egy visszaállítási pontról.<br/><br/> Megadhatja a virtuális gép nevét, kiválaszthatja azt az erőforráscsoportot és virtuális hálózatot (VNet), amelyben elhelyezi, és megadhat egy tárfiókot a visszaállított virtuális géphez. Az új virtuális gép kell létrehozni ugyanabban a régióban, mint a forrás virtuális gép.
**Lemez visszaállítása** | Visszaállít egy virtuális gép lemez, amely ezután egy új virtuális gép létrehozásához használható.<br/><br/> Az Azure Backup egy sablont biztosít a virtuális gép testreszabásához és létrehozásához. <br/><br> A visszaállítási feladat létrehoz egy sablont, amely letöltheti és használhatja az egyéni virtuális gép beállításainak megadásához, és hozzon létre egy virtuális gép.<br/><br/> A lemezek et a program a megadott erőforráscsoportba másolja.<br/><br/> Másik lehetőségként csatolja a lemezt egy meglévő virtuális géphez, vagy hozzon létre egy új virtuális gép powershell használatával.<br/><br/> Ez a beállítás akkor hasznos, ha testre szeretné szabni a virtuális gép, adja meg a konfigurációs beállításokat, amelyek nem voltak ott a biztonsági mentés idején, vagy adja hozzá a beállításokat, amelyeket a sablon vagy a PowerShell használatával kell konfigurálni.
**Meglévő cseréje** | Visszaállíthatja a lemezt, és a meglévő virtuális gép en lévő lemez lecserélésére használhatja.<br/><br/> Az aktuális virtuális gépnek léteznie kell. Ha törölték, ez a beállítás nem használható.<br/><br/> Az Azure Backup pillanatképet készít a meglévő virtuális gépről a lemez cseréje előtt, és a megadott átmeneti helyen tárolja. A virtuális géphez csatlakoztatott meglévő lemezeket a kijelölt visszaállítási pont váltja fel.<br/><br/> A pillanatkép a tárolóba kerül, és az adatmegőrzési szabályzatnak megfelelően marad meg. <br/><br/> A lemez csereművelete után az eredeti lemez megmarad az erőforráscsoportban. Ha nincs rájuk szükség, manuálisan törölheti az eredeti lemezeket. <br/><br/>A meglévő csere a titkosítatlan felügyelt virtuális gépek számára támogatott. Nem felügyelt lemezek, [általános gép-menésű virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)vagy egyéni [lemezképek használatával létrehozott](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)virtuális gépek esetén nem támogatott.<br/><br/> Ha a visszaállítási pont több vagy kevesebb lemezt, mint az aktuális virtuális gép, majd a lemezek száma a visszaállítási pont csak a virtuális gép konfigurációját.<br><br> A meglévő csere nem támogatott a csatolt erőforrásokkal (például [a felhasználó által hozzárendelt felügyelt identitásvagy](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) [a Key Vault)](https://docs.microsoft.com/azure/key-vault/key-vault-overview)rendelkező virtuális gépek esetében, mert a biztonsági mentési ügyfélalkalmazás nem rendelkezik engedélyekkel ezekhez az erőforrásokhoz a visszaállítás végrehajtása közben.
**Régióközi (másodlagos régió)** | A régiók közötti visszaállítás segítségével visszaállíthatja az Azure virtuális gépeket a másodlagos régióban, amely egy [Azure-ban párosított régió.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> Visszaállíthatja az összes Azure virtuális gépek a kiválasztott helyreállítási pont, ha a biztonsági mentés a másodlagos régióban történik.<br><br> Ez a funkció az alábbi lehetőségekhez érhető el:<br> * [Virtuális gép létrehozása](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Lemezek visszaállítása](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Jelenleg nem támogatjuk a [Meglévő lemezek cseréje](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) lehetőséget.<br><br> Engedélyek<br> A másodlagos régióvisszaállítási műveletet a biztonsági mentés i. rendszergazdák és az alkalmazásadminisztrátorok végezhetik el.

## <a name="support-for-file-level-restore"></a>Fájlszintű visszaállítás támogatása

**Visszaállítás** | **Támogatott**
--- | ---
Fájlok visszaállítása operációs rendszerek között | A fájlok at bármely olyan gépen, amely rendelkezik az azonos (vagy kompatibilis) operációs rendszerrel, mint a biztonsági másolatot készített virtuális gép. Lásd a [Kompatibilis operációs rendszer táblázatot](backup-azure-restore-files-from-vm.md#system-requirements).
Fájlok visszaállítása titkosított virtuális gépekről | Nem támogatott.
Fájlok visszaállítása a korlátozott hálózati tárfiókokból | Nem támogatott.
Fájlok visszaállítása virtuális gépeken a Windows tárolóhelyek használatával | Visszaállítás nem támogatott ugyanazon a virtuális gépen.<br/><br/> Ehelyett állítsa vissza a fájlokat egy kompatibilis virtuális gépen.
Fájlok visszaállítása Linux os virtuális gépen LVM/raid tömbök használatával | Visszaállítás nem támogatott ugyanazon a virtuális gépen.<br/><br/> Visszaállítás kompatibilis virtuális számítógépen.
Fájlok visszaállítása speciális hálózati beállításokkal | Visszaállítás nem támogatott ugyanazon a virtuális gépen. <br/><br/> Visszaállítás kompatibilis virtuális számítógépen.

## <a name="support-for-vm-management"></a>Virtuálisgép-kezelés támogatása

Az alábbi táblázat összefoglalja a virtuális gép felügyeleti feladatok során a biztonsági mentés támogatását, például a virtuális gép lemezek hozzáadása vagy cseréje során.

**Visszaállítás** | **Támogatott**
--- | ---
Visszaállítás előfizetés/régió/zóna között. | Nem támogatott.
Visszaállítás meglévő virtuális gépre | Használja a cserelemez beállítást.
Lemez visszaállítása az Azure Storage Service Encryption (SSE) számára engedélyezett tárfiókkal | Nem támogatott.<br/><br/> Visszaállítás olyan fiókra, amelyen nincs engedélyezve az SSE.
Visszaállítás vegyes tárfiókokra |Nem támogatott.<br/><br/> A tárfiók típusa alapján az összes visszaállított lemez prémium vagy normál lesz, és nem kevert.
Virtuális gép visszaállítása közvetlenül egy rendelkezésre állási csoportba | Felügyelt lemezek esetén visszaállíthatja a lemezt, és használhatja a sablon rendelkezésre állási készletbeállítását.<br/><br/> Nem felügyelt lemezek esetén nem támogatott. Nem felügyelt lemezek esetén állítsa vissza a lemezt, majd hozzon létre egy virtuális gép a rendelkezésre állási csoportban.
A felügyelt virtuális gépre való frissítés után a nem felügyelt virtuális gépek biztonsági másolatának visszaállítása| Támogatott.<br/><br/> Visszaállíthatja a lemezeket, majd létrehozhat egy felügyelt virtuális gép.
Virtuális gép visszaállítása a virtuális gép felügyelt lemezekre való áttelepítése előtt | Támogatott.<br/><br/> Visszaáll a nem felügyelt lemezekre (alapértelmezett), konvertálja a visszaállított lemezeket felügyelt lemezre, és hozzon létre egy virtuális gép a felügyelt lemezek.
A törölt virtuális gép visszaállítása. | Támogatott.<br/><br/> A virtuális gép visszaállítható egy helyreállítási pontból.
Többtartományvezérlős konfiguráció részét használó tartományvezérlő (DC) virtuális gép visszaállítása portálon keresztül | Támogatott, ha visszaállítja a lemezt, és hozzon létre egy virtuális gép segítségével PowerShell.
Virtuális gép visszaállítása különböző virtuális hálózatban |Támogatott.<br/><br/> A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie.

## <a name="vm-compute-support"></a>Virtuális gép számítási támogatása

**Compute** | **Támogatás**
--- | ---
Virtuális gép mérete |Bármilyen Azure virtuális gép mérete legalább 2 CPU-maggal és 1 GB RAM-mal.<br/><br/> [További információ.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Virtuális gépek biztonsági és biztonsági másolatot [kell adni a rendelkezésre állási készletekben](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Támogatott.<br/><br/> A virtuális gép nem állítható vissza egy elérhető készletben a virtuális gép gyors létrehozásához. Ehelyett a virtuális gép visszaállításakor állítsa vissza a lemezt, és használja a virtuális gép központi telepítéséhez, vagy egy lemez visszaállításához, és egy meglévő lemez cseréjéhez.
Hibrid [használati előnyökkel (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) üzembe helyezett virtuális gépek biztonsági és biztonsági őre | Támogatott.
Méretezési készletben üzembe helyezett virtuális gépek biztonsági [üzemelése](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Nem támogatott.
Az [Azure Marketplace-ről](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images) üzembe helyezett virtuális gépek biztonsági és biztonsági őrei<br/><br/> (Megjelent a Microsoft, harmadik fél) |Támogatott.<br/><br/> A virtuális gépnek támogatott operációs rendszert kell futtatnia.<br/><br/> A virtuális gépen lévő fájlok helyreállításakor csak kompatibilis operációs rendszerre állíthatja vissza (nem egy korábbi vagy későbbi operációs rendszerre). Nem állítja vissza az Azure Marketplace virtuális gépek virtuális gépként támogatott, mivel ezek vásárlási információkat igényelnek, de csak lemezekként.
Egyéni lemezképből (harmadik féltől származó) telepített virtuális gépek biztonsági és biztonsági őle |Támogatott.<br/><br/> A virtuális gépnek támogatott operációs rendszert kell futtatnia.<br/><br/> A virtuális gépen lévő fájlok helyreállításakor csak kompatibilis operációs rendszerre állíthatja vissza (nem egy korábbi vagy későbbi operációs rendszerre).
Az Azure-ba áttelepített virtuális gépek biztonsági és biztonsági őrei| Támogatott.<br/><br/> A virtuális gép biztonsági és biztonsági másolatot kell, a virtuális gép ügynöktelepítve kell lennie az áttelepített gépen.
Több virtuális gép konzisztenciájának biztonsági és biztonsági biztosa | Az Azure Backup nem biztosít adatokat és alkalmazáskonzisztenciát több virtuális gép között.
Biztonsági mentés [diagnosztikai beállításokkal](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Támogatott. <br/><br/> Ha az Azure virtuális gép visszaállítása diagnosztikai beállításokkal az [Új létrehozása](backup-azure-arm-restore-vms.md#create-a-vm) beállítással aktiválódik, majd a visszaállítás sikertelen lesz.
Zóna által rögzített virtuális gépek visszaállítása | Támogatott (2019 januárja után biztonsági másolatot követő virtuális gép esetén, ahol [rendelkezésre állási zóna](https://azure.microsoft.com/global-infrastructure/availability-zones/) érhető el).<br/><br/>Jelenleg támogatjuk a virtuális gépekben rögzített zónába való visszaállítást. Ha azonban a zóna nem érhető el, a visszaállítás sikertelen lesz.
Gen2 virtuális gépek | Támogatott <br> Az Azure Backup támogatja a Gen2 virtuális gépek biztonsági mentését és [visszaállítását.](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/) Amikor ezeket a virtuális gépeket visszaállítja a helyreállítási pontból, a rendszer [gen2 virtuális gépként](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)állítja vissza őket.

## <a name="vm-storage-support"></a>Virtuális gép tárolási támogatása

**Összetevő** | **Támogatás**
--- | ---
Azure virtuálisgép-adatlemezek | 16 vagy kevesebb adatlemezzel rendelkező virtuális gép biztonsági másolatot készíteni.<BR> Ha regisztrálni szeretne a 16-nál több (legfeljebb 32) adatlemezzel rendelkező virtuális gépek privát előzetes verziójára, írjon nekünk a következő címre: AskAzureBackupTeam@microsoft.com
Adatlemez mérete | Az egyes lemezek mérete legfeljebb 32 TB és legfeljebb 256 TB kombinált összes lemez egy virtuális gép.
Tárolási típus | Standard HDD, standard SSD, prémium SSD.
Felügyelt lemezek | Támogatott.
Titkosított lemezek | Támogatott.<br/><br/> Az Azure Disk Encryption szolgáltatással engedélyezett Azure virtuális gépek (az Azure AD alkalmazással vagy anélkül) biztonsági másolatot lehet készíteni.<br/><br/> A titkosított virtuális gépek nem helyreállnak a fájl/mappa szintjén. Vissza kell állítania a teljes virtuális gép.<br/><br/> Engedélyezheti a titkosítást az Azure Backup által már védett virtuális gépeken.
Olyan lemezek, amelyeken engedélyezve van az írásgyorsító | Nem támogatott.<br/><br/> Az Azure biztonsági mentés automatikusan kizárja a lemezek írásgyorsító engedélyezve van a biztonsági mentés során. Mivel nem készült biztonsági mentés, nem tudja visszaállítani ezeket a lemezeket a virtuális gép helyreállítási pontjairól.
Biztonsági másolat készítése & deduplicated virtuális gépek/lemezek visszaállítása | Az Azure Backup nem támogatja a deduplikációt. További információt ebben a [cikkben](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) talál. <br/> <br/>  - Az Azure Backup nem deduplikál a virtuális gépek között a Recovery Services tárolójában <br/> <br/>  - Ha vannak virtuális gépek deduplikációs állapotban a visszaállítás során, a fájlok nem állíthatók vissza, mivel a tároló nem érti a formátumot. Azonban képes lesz sikeresen végrehajtani a teljes virtuális gép visszaállítása.
Lemez hozzáadása a védett virtuális géphez | Támogatott.
Lemez átméretezése védett virtuális gépen | Támogatott.
Megosztott tároló| A fürt megosztott kötetével (CSV) vagy a kibővített fájlkiszolgálóval nem támogatott a virtuális gépek biztonsági mentése. CSV írók valószínűleg nem a biztonsági mentés során. Visszaállításkor előfordulhat, hogy a CSV-köteteket tartalmazó lemezek nem jelennek meg.
[Megosztott lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | Nem támogatott.

## <a name="vm-network-support"></a>Virtuális gép hálózati támogatása

**Összetevő** | **Támogatás**
--- | ---
Hálózati adapterek száma | Egy adott Azure virtuális gép méretéhez támogatott hálózati adapterek maximális száma.<br/><br/> Hálózati adapterek jönnek létre, amikor a virtuális gép jön létre a visszaállítási folyamat során.<br/><br/> A visszaállított virtuális gép hálózati adaptereinek száma tükrözi a virtuális gép hálózati adaptereinek számát, ha engedélyezte a védelmet. A hálózati adapterek eltávolítása a védelem engedélyezése után nincs hatással a számlálóra.
Külső/belső terheléselosztó |Támogatott. <br/><br/> [További információ](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) a virtuális gépek speciális hálózati beállításokkal történő visszaállításáról.
Több fenntartott IP-cím |Támogatott. <br/><br/> [További információ](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) a virtuális gépek speciális hálózati beállításokkal történő visszaállításáról.
Több hálózati adapterrel rendelkező virtuális gépek| Támogatott. <br/><br/> [További információ](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) a virtuális gépek speciális hálózati beállításokkal történő visszaállításáról.
Nyilvános IP-címmel rendelkező virtuális gépek| Támogatott.<br/><br/> Társítson egy meglévő nyilvános IP-címet a hálózati adapterhez, vagy hozzon létre egy címet, és társítsa azt a hálózati adapterhez a visszaállítás befejezése után.
Hálózati biztonsági csoport (NSG) a hálózati adapteren/alhálózaton. |Támogatott.
Statikus IP-cím | Nem támogatott.<br/><br/> Egy új virtuális gép, amely egy visszaállítási pontról létrehozott egy dinamikus IP-címet kap.<br/><br/> A klasszikus virtuális gépek, nem lehet biztonsági másolatot egy fenntartott IP-címmel rendelkező virtuális gép, és nincs meghatározott végpont.
Dinamikus IP-cím |Támogatott.<br/><br/> Ha a hálózati adapter a forrás virtuális gép dinamikus IP-címzést használ, alapértelmezés szerint a hálózati adapter a visszaállított virtuális gép fogja használni is.
Azure Traffic Manager| Támogatott.<br/><br/>Ha a biztonsági másolatot tett virtuális gép a Traffic Manager, manuálisan adja hozzá a visszaállított virtuális gép ugyanahhoz a Traffic Manager-példány.
Azure DNS |Támogatott.
Egyéni DNS |Támogatott.
Kimenő kapcsolat HTTP-proxyn keresztül | Támogatott.<br/><br/> A hitelesített proxy nem támogatott.
Virtuális hálózati szolgáltatásvégpontok| Támogatott.<br/><br/> A tűzfal és a virtuális hálózati tárfiók beállításainak minden hálózatról hozzáférést kell biztosítaniuk.

## <a name="vm-security-and-encryption-support"></a>Virtuális gép biztonságának és titkosításának támogatása

Az Azure Backup támogatja a titkosítást az átvitel közbeni és az inaktív adatokhoz:

Hálózati forgalom az Azure-ba:

- A kiszolgálókról a Recovery Services tárolóba irányuló biztonsági mentési forgalmat a Speciális titkosítási szabvány 256 használatával titkosítja.
- A biztonsági mentési adatok küldése biztonságos HTTPS-kapcsolaton keresztül történik.
- A biztonsági mentési adatok a Recovery Services tárolójában titkosított formában tárolódnak.
- Csak önnek van meg a jelmondata az adatok feloldásához. A Microsoft nem tudja visszafejteni a biztonsági mentési adatokat.

  > [!WARNING]
  > A tároló beállítása után csak ön férhet hozzá a titkosítási kulcshoz. A Microsoft soha nem tart fenn másolatot, és nem fér hozzá a kulcshoz. Ha a kulcs rossz helyre került, a Microsoft nem tudja helyreállítani a biztonsági mentési adatokat.

Az adatbiztonságot:

- Az Azure-beli virtuális gépek biztonsági mentésekor titkosítást kell beállítania a virtuális gépen *belül.*
- Az Azure Backup támogatja az Azure Disk Encryption szolgáltatást, amely a BitLocker szolgáltatást windowsos virtuális gépeken, a mi **dm-crypt-et pedig Linux-os** virtuális gépeken használja.
- Az Azure Backup a háttérben az [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) használatával biztosít védelmet az inaktív adatok számára.

**Gép** | **Szállítás közben** | **Nyugalmi**
--- | --- | ---
Helyszíni Windows-gépek DPM/MABS nélkül | ![Igen][green] | ![Igen][green]
Azure-beli virtuális gépek | ![Igen][green] | ![Igen][green]
Helyszíni/Azure-beli virtuális gépek DPM-mel | ![Igen][green] | ![Igen][green]
Helyszíni/Azure-beli virtuális gépek MABS-szel | ![Igen][green] | ![Igen][green]

## <a name="vm-compression-support"></a>Virtuális gép tömörítésének támogatása

A biztonsági mentés támogatja a biztonsági mentési forgalom tömörítését az alábbi táblázatban összefoglalva. Vegye figyelembe a következőket:

- Az Azure virtuális gépek, a virtuális gép bővítmény beolvassa az adatokat közvetlenül az Azure storage-fiók a tárolóhálózaton keresztül. Nem szükséges tömöríteni ezt a forgalmat.
- Ha DPM-et vagy MABS-t használ, az adatok DPM/MABS-re való biztonsági mentése előtt csökkentheti a sávszélességet.

**Gép** | **Tömörítés MABS-be/DPM-be (TCP)** | **Tömörítés a tárolóba (HTTPS)**
--- | --- | ---
Helyszíni Windows-gépek DPM/MABS nélkül | NA | ![Igen][green]
Azure-beli virtuális gépek | NA | NA
Helyszíni/Azure-beli virtuális gépek DPM-mel | ![Igen][green] | ![Igen][green]
Helyszíni/Azure-beli virtuális gépek MABS-szel | ![Igen][green] | ![Igen][green]

## <a name="next-steps"></a>További lépések

- [Az Azure-beli virtuális gépek biztonsági másolatot kell kapnia.](backup-azure-arm-vms-prepare.md)
- [Készítsen biztonsági másolatot közvetlenül a Windows-gépekről,](tutorial-backup-windows-server-to-azure.md)tartalék kiszolgáló nélkül.
- Állítsa be a [MABS-t](backup-azure-microsoft-azure-backup.md) az Azure-ba való biztonsági mentéshez, majd készítsen biztonsági másolatot a számítási feladatokról a MABS-re.
- Állítsa be a [DPM-et](backup-azure-dpm-introduction.md) az Azure-ba való biztonsági mentéshez, majd készítsen biztonsági másolatot a számítási feladatokról a DPM-re.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
