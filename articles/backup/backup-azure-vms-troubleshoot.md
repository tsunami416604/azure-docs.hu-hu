---
title: Az Azure virtuális gép biztonsági mentési hibák elhárítása
description: Biztonsági mentés és visszaállítás Azure virtuális gépek hibaelhárítása
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 8/7/2018
ms.author: trinadhk
ms.openlocfilehash: 8ef8241e9f0f6223b29fa29f7a5803f57f4d6203
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414998"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Azure-beli virtuális gépek biztonsági mentésének hibaelhárítása
Az alábbi táblázatban szereplő információk az Azure Backup használatával észlelt hibák elhárítását.

| A hiba részletei | Áthidaló megoldás |
| --- | --- |
| Nem sikerült elvégezni a műveletet, mert a virtuális gép már nem létezik. – Állítsa le a virtuális gép védelmét a biztonsági mentési adatok törlése nélkül. További részletek: http://go.microsoft.com/fwlink/?LinkId=808124 |Ez akkor történik, ha az elsődleges virtuális gép törlődik, de a biztonsági mentési szabályzathoz, továbbra is keres a virtuális gép biztonsági mentése. Ez a hiba elhárításához: <ol><li> Hozza létre újra a virtuális gépet az azonos nevű és azonos erőforráscsoport neve [felhőszolgáltatás neve],<br>(VAGY)</li><li> Állítsa le a virtuális gép vagy a biztonsági mentési adatok törlése nélkül. [További részletek](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Pillanatkép-készítési művelet sikertelen volt, mert nincs hálózati kapcsolat a virtuális gépen – győződjön meg arról, hogy a virtuális gép hálózati hozzáféréssel rendelkezik. A pillanatfelvétel sikeres legyen vagy engedélyezett az Azure datacenter IP-cím-tartományait beállítása hálózati hozzáféréshez proxykiszolgálót. További információkért tekintse meg http://go.microsoft.com/fwlink/?LinkId=800034. Ha már használ proxykiszolgálót, ellenőrizze, hogy a proxykiszolgáló beállításai megfelelően vannak-e konfigurálva | Ha megtagadja a kimenő internetkapcsolat a virtuális gépen történik. A VM snapshot bővítményt az alapul szolgáló lemez pillanatfelvételének internetkapcsolatra van szükség. [Letiltott hálózati hozzáférés miatt pillanatkép hibák kijavítása beállításait ismertető szakaszban](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine). |
| A Virtuálisgép-ügynök nem tud kommunikálni az Azure Backup szolgáltatással. – Győződjön meg arról, a virtuális gép rendelkezik hálózati kapcsolattal, és a Virtuálisgép-ügynök legújabb és futnak. További információkért tekintse meg a cikket http://go.microsoft.com/fwlink/?LinkId=800034. |Ez a hiba fordul elő, ha probléma adódott a Virtuálisgép-ügynök vagy az Azure-infrastruktúra való hálózati hozzáférés valamilyen módon le van tiltva. [További](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup) virtuális Gépet is hibakeresésről pillanatkép-problémák.<br> Ha a Virtuálisgép-ügynök nem okozza a hibát, indítsa újra a virtuális Gépet. Egy nem megfelelő virtuális gép állapota problémákat okozhat, és a virtuális gép újraindítása alaphelyzetbe állítja az állapotát. |
| Virtuális gép sikertelenül kiépített állapotban – indítsa újra a virtuális Gépet, és győződjön meg arról, hogy a virtuális gép fut-e, vagy állítsa le. | Ez a hiba akkor fordul elő, amikor a bővítmények hibák egyike az érdeklődők sikertelen kiépítési állapotban kell lennie a virtuális gép állapota. Ugrás a kiterjesztések listája, és e sikertelen bővítmény, távolítsa el, és próbálja meg újraindítani a virtuális gép. Ha az összes bővítmény futó állapotban, ellenőrizze, hogy a Virtuálisgép-ügynök szolgáltatás fut-e. Ha nem, indítsa újra a virtuális gép-ügynök szolgáltatást. |
| A VMSnapshot bővítmény művelete sikertelen volt a felügyelt lemezek – próbálja megismételni a biztonsági mentési műveletet. Ha a probléma továbbra is fennáll, kövesse az utasításokat, "http://go.microsoft.com/fwlink/?LinkId=800034". Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatához | Ez a hiba akkor fordul elő, ha a Backup szolgáltatás nem sikerült elindítani a pillanatképet. [További](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#ExtentionOperationFailed-vmsnapshot-extension-operation-failed) VM hibakeresésről pillanatkép-problémák. |
| Sikerült a pillanatkép a virtuális gép, mert nincs elég szabad hely a tárfiók - példány nem győződjön meg arról, hogy a storage-fiók rendelkezik-e a virtuális géphez csatlakoztatott premium storage-lemezeken lévő adatok megfelelő szabad terület | Prémium szintű virtuális gépek a virtuális gép biztonsági másolat verem V1 esetén tárfiók a pillanatkép másolja azt. Ez a győződjön meg arról, hogy a biztonságimásolat-kezelési forgalom, amely pillanatkép működik, nem korlátozza a prémium lemezek segítségével az alkalmazáshoz rendelkezésre álló IOPS számát. A Microsoft javasolja csak 50 %-os (17.5 TB) foglal le a teljes tárterületet fiók, az Azure Backup szolgáltatás másolhatja a pillanatkép tárolási fiók és az átviteli adatok a storage-fiókban a tárolóba a másolt helyről. | 
| Nem sikerült végrehajtani a műveletet, mert a Virtuálisgép-ügynök nem válaszol |Ez a hiba fordul elő, ha probléma adódott a Virtuálisgép-ügynök vagy az Azure-infrastruktúra való hálózati hozzáférés valamilyen módon le van tiltva. Windows virtuális gépek esetén ellenőrizze a szolgáltatások, valamint hogy az ügynök szerepel-e a Vezérlőpult Programok telepítése Virtuálisgép-ügynök szolgáltatás állapotát. Próbálja meg eltávolítani a program a vezérlő panelen, és újból az ügynök telepítése, amint már említettük [alább](#vm-agent). Után újból telepíti az ügynököt, győződjön meg arról, hogy egy ad hoc biztonsági mentés indítása. |
| Recovery services-bővítmény műveletét nem sikerült. – Ellenőrizze, hogy a legújabb virtuálisgép-ügynök jelen a virtuális gépen, és az ügynök szolgáltatás fut. Ismételje meg a biztonsági mentési műveletet. Ha a biztonsági mentési művelet nem sikerül, forduljon a Microsoft ügyfélszolgálatához. |Ez a hiba fordul elő, ha a Virtuálisgép-ügynök nem naprakész. Tekintse meg a Virtuálisgép-ügynök frissítéséhez az alábbi "Frissítése a Virtuálisgép-ügynök" szakaszban. |
| Virtuális gép nem létezik. – Győződjön meg arról, hogy a virtuális gép valóban létezik, vagy jelölje be egy másik virtuális gépet. |Akkor következik be, amikor az elsődleges virtuális gép törlődik, de a biztonsági mentési szabályzat továbbra is fennáll, és tekintse meg a virtuális gép biztonsági mentése. Ez a hiba elhárításához: <ol><li> Hozza létre újra a virtuális gépet az azonos nevű és azonos erőforráscsoport neve [felhőszolgáltatás neve],<br>(VAGY)<br></li><li>Állítsa le a virtuális gép védelmét a biztonsági mentési adatok törlése nélkül. [További részletek](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| A parancs végrehajtása nem sikerült. – Egy másik művelet van folyamatban ezen az elemen. Várjon, amíg az előző művelet befejeződik, és próbálkozzon újra a művelettel. |Egy meglévő biztonsági mentési feladat fut, és a egy új feladat nem indítható el, a jelenlegi feladat befejezéséig. |
| VHD-k átmásolása a Recovery Services vault időkorlátja lejárt – próbálja megismételni a műveletet néhány perc múlva. Ha a probléma nem szűnik meg, forduljon a Microsoft ügyfélszolgálatához. | Akkor következik be, ha átmeneti hiba van a storage ügyféloldali, vagy ha a biztonsági mentési szolgáltatás nem kap megfelelő tárfiók IOPS való adatátvitel a tárolóhoz, a határidőn belül. Kövesse a [ajánlott eljárásokat, a virtuális gépek konfigurálásakor](backup-azure-vms-introduction.md#best-practices). A virtuális gép áthelyezése egy másik tárfiókot, amely nincs betöltve, és próbálkozzon újra a biztonsági mentési feladat.|
| Biztonsági mentés belső hiba miatt meghiúsult – próbálja megismételni a műveletet néhány perc múlva. Ha a probléma tartósan fennáll, forduljon a Microsoft Support |Ez a hibaüzenet két oka: <ol><li> A Virtuálisgép-tárolók elérése során egy átmeneti probléma van. Ellenőrizze a [Azure állapotlapján hely](https://azure.microsoft.com/status/) megtekintéséhez, ha a számítási, tárolási vagy hálózati problémák vannak-e a régióban. A probléma megoldása után próbálja megismételni a biztonsági mentési feladat. <li>Az eredeti virtuális gép törölve lett, és a helyreállítási pont nem végezhető. A törölt virtuális gép biztonsági mentési adatok megtartása, de a biztonsági mentési hibák eltávolítása: a virtuális gép védelmének, és válassza ki a lehetőséget az adatok megőrzéséhez. Ez a művelet leállítja az ütemezett biztonsági mentési feladat, és az ismétlődő hibaüzenetek. |
| A kijelölt elem – az Azure Recovery Services-bővítmény telepítése nem sikerült a Virtuálisgép-ügynök nem az Azure Recovery Services-bővítmény előfeltétele. Az Azure-beli Virtuálisgép-ügynök telepítése, majd indítsa újra a regisztrációs művelet |<ol> <li>Ellenőrizze, hogy ha a Virtuálisgép-ügynök megfelelően telepítve. <li>Győződjön meg arról, megfelelően van-e beállítva a virtuális gép konfigurációs jelölőjét.</ol> [További információ](#validating-vm-agent-installation) telepítéséről, a Virtuálisgép-ügynök és a Virtuálisgép-ügynök telepítésének ellenőrzése. |
| A bővítmény telepítése nem sikerült a "COM + nem tudta felvenni a kapcsolatot, a Microsoft Distributed Transaction Coordinator |Ez általában azt jelenti, hogy a COM + szolgáltatás nem fut. A probléma javítása, segítségért forduljon a Microsoft ügyfélszolgálatához. |
| Pillanatkép-készítési művelet sikertelen volt a VSS-művelet hibája: "a meghajtó BitLocker meghajtótitkosítással zárolta. Meg kell oldja fel a zárolást a Vezérlőpulton. |Kapcsolja ki a BitLocker az összes meghajtón a virtuális gépen, és tekintse meg az megoldódott-e a VSS-hiba |
| Virtuális gép olyan állapotban, amely lehetővé teszi a biztonsági mentések nem szerepel. |<ul><li>Ha a virtuális gép átmeneti állapotban közötti **futó** és **Leállítás**, és várjon, amíg az állapot módosítása, majd aktiválja a biztonsági mentési feladatot. <li> Ha a virtuális gép Linux rendszerű virtuális gép, és a biztonsági fokozott Linux-kernel modult használja, a kivétel a Linux-ügynök elérési útja (_/var/lib/waagent_) a biztonsági házirendet, és ellenőrizze, hogy a Backup bővítmény telepítve van.  |
| Az Azure virtuális gép nem található. |A hibák akkor fordul elő, amikor az elsődleges virtuális gép törlődik, de a biztonsági mentési szabályzat továbbra is fennáll, a törölt virtuális gép keres. Ez a hiba elhárításához: <ol><li>Hozza létre újra a virtuális gépet az azonos nevű és azonos erőforráscsoport neve [felhőszolgáltatás neve], <br>(VAGY) <li> Tiltsa le a virtuális gép védelmét, így a biztonsági mentési feladatok nem hozható létre. </ol> |
| Virtuálisgép-ügynököt a virtuális gép nem áll rendelkezésre – telepítse az összes előfeltételt és a Virtuálisgép-ügynök, és indítsa újra a műveletet. |[További információ](#vm-agent) Virtuálisgép-ügynök telepítése és a Virtuálisgép-ügynök telepítésének ellenőrzése. |
| A VSS-írók rossz állapota miatt nem sikerült elkészíteni a pillanatképet |Indítsa újra a VSS (kötet árnyékmásolata szolgáltatás) írók rossz állapotban lévő kell. Ennek eléréséhez, egy rendszergazda jogú parancssorból futtassa ```vssadmin list writers```. Kimenet tartalmazza az összes VSS-író és az állapotuk. Az összes VSS-író amelynek állapota nem nem "[1] Stable" indítsa újra a VSS-író futtassa a következő parancsokat egy rendszergazda jogú parancssorból:<br> ```net stop serviceName``` <br> ```net start serviceName```|
| A konfiguráció elemzési hiba miatt nem sikerült elkészíteni a pillanatképet |Ez történik, az MachineKeys címtár módosított engedélyek miatt: _%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br>Futtassa az alábbi parancsot, és győződjön meg róla, hogy MachineKeys könyvtár alapértelmezett:<br>_icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> Alapértelmezett engedélyek a következők:<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>Ha az alapértelmezettől eltérő MachineKeys directory engedélyek látható,. kövesse az alábbi lépéseket a megfelelő engedélyekkel, törölje a tanúsítványt, és aktiválja a biztonsági mentés.<ol><li>Javítsa ki az engedélyek MachineKeys könyvtárban.<br>Használja a Explorer biztonsági tulajdonságait és a speciális biztonsági beállítások arra a címtárra, visszaállítása az engedélyek az alapértelmezett értékeket. Távolítsa el az összes felhasználói objektum (kivéve az alapértelmezett érték) a címtárból, és győződjön meg, hogy a **mindenki** engedéllyel rendelkezik a speciális hozzáféréshez:<br>-Mappa listázása / adatok olvasása <br>-Attribútumok olvasása <br>– Olvassa el a kiterjesztett attribútumok <br>-Fájlok létrehozása / adatok írása <br>-Mappák létrehozása / adatok hozzáfűzése<br>-Attribútumok írása<br>-A kiterjesztett attribútumok írása<br>-Engedélyek olvasása<br><br><li>Törölje az összes tanúsítvány ahol **tulajdonos** a klasszikus üzemi modell vagy **Windows Azure CRP-tanúsítványkészítő**.<ul><li>[Tanúsítványok (helyi számítógép) konzol megnyitása](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>A **személyes** > **tanúsítványok**, törölje az összes tanúsítvány ahol **tulajdonos** a klasszikus üzemi modell vagy **Windows Azure CRP Tanúsítvány-generátor**.</ul><li>Virtuális gép biztonsági mentési feladat aktiválása. </ol>|
| Az Azure Backup szolgáltatás nem rendelkezik megfelelő engedélyekkel a Key Vault a biztonsági mentés a titkosított virtuális gépek. |A biztonsági mentési szolgáltatás meg kell adni ezeket az engedélyeket a PowerShellben található lépések végrehajtásával **biztonsági mentés engedélyezése** szakaszában [PowerShell-dokumentáció](backup-azure-vms-automation.md). |
|Telepítése pillanatkép bővítményt nem sikerült – a COM + nem tudta felvenni a kapcsolatot a Microsoft Distributed Transaction Coordinator | Egy rendszergazda jogú parancssort a Windows szolgáltatás indítása "COM + System Application", például _net start COMSysApp_. <br>Ha a szolgáltatás nem indul el, majd:<ol><li> Ellenőrizze, hogy a napló be a szolgáltatás "Distributed Transaction Coordinator" a "Hálózati szolgáltatás". Ha nem, a bejelentkezési fiók módosítása "Hálózati szolgáltatás", indítsa újra a szolgáltatást, és próbálja meg elindítani a "COM + System Application". "<li>Ha a "COM + System Application nem indítsa el, az alábbi lépéseket követve szolgáltatás"Distributed Transaction Coordinator"távolítsa el/telepítse:<br> -Az MSDTC szolgáltatás leállítása<br> – Nyisson meg egy parancssort (cmd) <br> -Parancs futtatása ```msdtc -uninstall``` <br> -Parancs futtatása ```msdtc -install``` <br> -Az MSDTC szolgáltatás elindítása<li>Indítsa el a "COM + System Application" windows-szolgáltatás. Egyszer "a COM + System Application elindításakor a trigger egy biztonsági mentési feladat az Azure Portalról.</ol> |
|  Pillanatkép-készítési művelet a COM + hiba miatt nem sikerült | A javasolt művelet az "a COM + System Application" windows szolgáltatás újraindítása (egy rendszergazda jogú parancssorból – _net start COMSysApp_). Ha a probléma tartósan fennáll, indítsa újra a virtuális Gépet. Ha a virtuális gép újraindítása nem segít, próbálja meg [a VMSnapshot-bővítmény eltávolítása](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load) és a biztonsági mentés manuális aktiválása. |
| Nem sikerült egy vagy több csatlakozási pontját a fájlrendszer konzisztens pillanatképének elkészítéséhez virtuális gép rögzítése | Ehhez a következő lépések szükségesek: <ol><li>Ellenőrizze a fájlrendszer állapotát az összes csatlakoztatott eszközök _"tune2fs"_ parancsot.<br> Például: tune2fs -l/dev/sdb1 \| grep "Fájlrendszer állapota" <li>Válassza le az eszközöket, mely FileSystem állapota nem tiszta használatával _"umount"_ parancs <li> Ezeket az eszközöket a FileSystemConsistency-ellenőrzés futtatása _"fsck"_ parancs <li> Újra csatlakoztatni az eszközöket, és próbálja meg a biztonsági mentés.</ol> |
| Pillanatkép-készítési művelet sikertelen volt, mert hiba történt a biztonságos hálózati kommunikációs csatorna létrehozása | <ol><Li> Nyissa meg a Beállításszerkesztőt a regedit.exe egy emelt jogosultságszintű módban való futtatásával. <li> Azonosítsa a rendszerben lévő .NET-keretrendszer összes verziójához. Ezek meg adva a hierarchia "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft" beállításkulcs alatt <li> Az egyes .net Framework jelen beállításkulcs, adja hozzá az alábbi kulcs: <br> "SchUseStrongCrypto"=dword:00000001 </ol>|
| Pillanatkép-készítési művelet sikertelen volt, mert hiba történt a Visual C++ terjeszthető változata, a Visual Studio 2012 | Navigáljon a C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion, és telepítse a vcredist2012_x64. Győződjön meg arról, hogy a szolgáltatás telepítése lehetővé teszi a beállításkulcs-érték értéke helyes értéket, azaz beállításkulcs értékét _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_ 3. és 4 nem értékre van állítva. Ha továbbra is problémákba ütközik, indítsa újra a telepítési szolgáltatásának futtatásával _MSIEXEC /UNREGISTER_ követ _MSIEXEC /REGISTER_ egy rendszergazda jogú parancssorból.  |


## <a name="jobs"></a>Feladatok
| A hiba részletei | Áthidaló megoldás |
| --- | --- |
| Megszakítás esetén ez a feladattípus nem támogatott – kis türelmet, amíg a feladat befejeződik. |None |
| A feladat nem visszavonható állapotban – kis türelmet, amíg a feladat befejeződik. <br>VAGY<br> A kiválasztott feladat nem visszavonható állapotban – Várjon, amíg a feladat befejeződik. |A feladat minden valószínűség szerint, majdnem befejeződött. Várjon, amíg a feladat befejeződött.|
| A feladat nem szakítható meg, mert azt nem folyamat – törlés csak a támogatott feladatok, amelyek folyamatban vannak. Adjon a kísérlet megszakítása folyamatban lévő feladat. |Ez egy átmeneti állapot miatt fordul elő. Várjon egy percet, és próbálja megismételni a megszakítási műveletet. |
| Nem sikerült megszakítani a feladatot – kérjük, várjon befejeződik a feladat. |None |

## <a name="restore"></a>Visszaállítás
| A hiba részletei | Áthidaló megoldás |
| --- | --- |
| Felhőalapú belső hiba miatt nem sikerült visszaállítani |<ol><li>A felhőalapú szolgáltatás, amelyhez visszaállítani kívánt DNS-beállításokkal van konfigurálva. Ellenőrizheti <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Ha nincs konfigurált címet, ez azt jelenti, hogy a DNS-beállítások vannak konfigurálva.<br> <li>Felhőalapú szolgáltatás, amelyhez a visszaállítani kívánt a fenntartott IP-cím van beállítva, és leállított állapotban van, a felhőszolgáltatás virtuális gépeinek konfigurációjától.<br>Cloud service lefoglalt IP a következő powershell-parancsmagok használatával ellenőrizheti:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>Állítsa vissza a következő speciális hálózati konfigurációval rendelkező virtuális gép ugyanazon a felhőszolgáltatáson kívánt. <br>– Virtuális gépek a terheléselosztó-konfiguráció (külső és belső)<br>– Virtuális gépek több fenntartott IP-címmel<br>-A virtuális gépek több hálózati adapterrel<br>Válasszon ki egy új felhőszolgáltatást a felhasználói felületen, vagy tekintse meg [helyreállítási szempontjai](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) speciális hálózati konfigurációval rendelkező virtuális gépek számára.</ol> |
| A kiválasztott DNS-név már használatban van – adjon meg egy másik DNS-nevet, és próbálkozzon újra. |Itt a DNS-név hivatkozik a felhőszolgáltatás neve (általában végződő. cloudapp.net). Ennek egyedinek kell lennie. Ha ezt a hibát tapasztal, válasszon egy másik Virtuálisgép-nevet visszaállítás során van szükség. <br><br> Ez a hiba csak a felhasználók számára az Azure Portalon látható. PowerShell-lel a visszaállítási művelet sikeres lesz, mert csak visszaállítja a lemezeket, és nem hoz létre a virtuális gép. A hiba lesz tapasztalható, a virtuális gép létrehozásakor explicit módon, a lemez a visszaállítási művelet után. |
| A megadott virtuális hálózati konfiguráció nem megfelelő – adjon meg egy másik virtuális hálózati konfiguráció és próbálkozzon újra. |None |
| A megadott felhőszolgáltatás fenntartott IP-cím, amely nem felel meg a visszaállítandó virtuális gép konfigurációja – adjon meg egy másik felhőalapú szolgáltatás, amely nem használ fenntartott IP-címet, vagy válasszon másik helyreállítási pontot a visszaállítandó használ. |None |
| A felhőalapú szolgáltatás elérte a bemeneti végpontok számának korlátját – adjon meg egy másik felhőszolgáltatást vagy egy meglévő végpontot, próbálja megismételni a műveletet. |None |
| A Recovery Services-tár és a céloldali tárfiók van két különböző régióban – győződjön meg arról, a storage-fiók a visszaállítási művelethez megadott, a ugyanabban a régióban az Azure és a Recovery Services-tárolónak. |None |
| A visszaállítási művelethez megadott Tárfiók támogatott – a storage-fiókok csak alapszintű/Standard helyileg redundáns vagy földrajzi adhatók meg replikálási beállítások támogatottak. Válasszon ki egy támogatott tárfiókot |None |
| A visszaállításhoz megadott tárfióktípus nem érhető el – győződjön meg arról, hogy online állapotban-e a visszaállítási műveletben megadott tárfiók |Ez egy átmeneti hiba az Azure Storage-ban, vagy leállás miatt miatt fordulhat elő. Válasszon egy másik tárfiókba. |
| Elérte az erőforráscsoport-kvótát –. Töröljön néhány erőforráscsoportot az Azure Portalról, vagy kérje az Azure ügyfélszolgálatától a határértékek növelését. |None |
| Kiválasztott alhálózat nem létezik – válasszon ki egy alhálózatot, amely létezik |None |
| A Backup szolgáltatás nem jogosult az Ön előfizetésében biztosított erőforrások elérésére. |A probléma megoldásához, első lemezek visszaállítása című részben leírt lépéseket követve **visszaállítási biztonsági másolat lemezek** a [kiválasztása Virtuálisgép-konfiguráció visszaállítása](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Ezt követően használja a PowerShell-lépések említett [hozzon létre egy virtuális gép helyreállított lemezekből](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) teljes virtuális gép létrehozásához a helyreállított lemezek alapján. |

## <a name="backup-or-restore-taking-time"></a>Biztonsági mentési vagy visszaállítási időt vesz igénybe
Ha megjelenik a backup(>12 hours) vagy visszaállítási állapotba time(>6 hours):
* Megismerheti [biztonsági mentés idejéhez hozzájáruló tényezők](backup-azure-vms-introduction.md#total-vm-backup-time) és [a helyreállítás időtartamát befolyásoló tényezők](backup-azure-vms-introduction.md#total-restore-time).
* Győződjön meg arról, hogy kövesse [biztonsági mentés ajánlott eljárások](backup-azure-vms-introduction.md#best-practices).

## <a name="vm-agent"></a>A Virtuálisgép-ügynök
### <a name="setting-up-the-vm-agent"></a>A Virtuálisgép-ügynök beállítása
Általában a Virtuálisgép-ügynök már szerepel az Azure katalógusból létrehozott virtuális gépeket. A helyszíni adatközpontokból áttelepített virtuális gépek azonban nem kell a Virtuálisgép-ügynök. Az ilyen virtuális gépek esetén a virtuális gép ügynökét explicit módon kell telepíteni.

Windows virtuális gépek:

* Töltse le és telepítse az [ügynök MSI-t](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek.
* Klasszikus virtuális gépek esetében [frissítse a virtuális gép tulajdonságát](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) jelzi, hogy az ügynök telepítve van-e. Ez a lépés nem kötelező a Resource Manager virtuális gépeken.

Linux rendszerű virtuális gépekhez:

* Az ügynök legújabb verziójának telepítése a terjesztésipont-adattárból. A csomag nevét a részletekért lásd: a [Linux-ügynök tárház](https://github.com/Azure/WALinuxAgent).
* A klasszikus virtuális gépek esetében [blogbejegyzésben használatával frissítse a virtuális gép tulajdonságát](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), és ellenőrzés az ügynök telepítve van. Ez a lépés nem szükséges a Resource Manager virtuális gépeken.

### <a name="updating-the-vm-agent"></a>A virtuálisgép-ügynök frissítése
Windows virtuális gépek:

* A Virtuálisgép-ügynök frissítéséhez újra kell telepítenie a [Virtuálisgép-ügynök bináris fájljainak](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Az ügynök frissítése, előtt ellenőrizze, nincs biztonsági mentési műveleteket a rendszer a Virtuálisgép-ügynök frissítése közben.

Linux rendszerű virtuális gépekhez:

* A Linux rendszerű Virtuálisgép-ügynök frissítéséhez kövesse a cikk a [Linuxos Virtuálisgép-ügynök frissítése](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
**Mindig használja az ügynök frissítése a terjesztési tárház**. Az ügynökkód ne töltse le a Githubról. Ha a legújabb ügynök nem érhető el a terjesztés, forduljon a terjesztési támogatási szolgálatához vonatkozó utasításokat a legújabb ügynököt beszerzése. Ellenőrizheti a legújabb [Windows Azure Linux-ügynök](https://github.com/Azure/WALinuxAgent/releases) a GitHub-adattárban lévő információkat.

### <a name="validating-vm-agent-installation"></a>Virtuálisgép-ügynök telepítésének ellenőrzése

Annak ellenőrzése, a Windows virtuális gépek Virtuálisgép-ügynök verziója:

1. Jelentkezzen be az Azure virtuális gépen, és lépjen abba a mappába *C:\WindowsAzure\Packages*. Itt találja a WaAppAgent.exe fájlt.
2. Kattintson jobb gombbal a fájlra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Részletek** lapot. A termék verziószáma mezőben 2.6.1198.718 kell lennie. vagy újabb

## <a name="troubleshoot-vm-snapshot-issues"></a>Virtuális gép Pillanatfelvételeivel kapcsolatos problémák elhárítása
Virtuális gép biztonsági mentése a pillanatkép-parancsok kiadása az alapul szolgáló tárolóról támaszkodik. Nem tárolási vagy az késleltetések hozzáférést egy pillanatkép-feladat végrehajtása a okozhat a biztonsági mentési feladat sikertelen lesz. A következő pillanatkép-feladat hibája okozhatja.

1. Tárhely hálózati elérhetőségét le van tiltva, NSG-vel<br>
    További információ a [hálózati hozzáférés engedélyezése](backup-azure-arm-vms-prepare.md#establish-network-connectivity) Storage-ban vagy IP-címek engedélyezési vagy proxykiszolgálón keresztül.
2. Az Sql Server biztonsági másolat konfigurált virtuális gépek okozhat a pillanatkép-feladat késleltetése <br>
   Alapértelmezés szerint a virtuális gép biztonsági mentésének létrehoz egy VSS teljes biztonsági mentés Windows virtuális gépeken. SQL Servert futtató virtuális gépeket, az SQL Server biztonsági mentés konfigurálva, pillanatkép késések tapasztalhatnak. Pillanatkép késések a biztonsági mentési hibákhoz vezethet, ha állítsa be a következő beállításkulcsot.

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

3. Virtuális gép állapota helytelenül jelenteni, mert a virtuális gép leállt az RDP.  <br>
   Ha a távoli asztal állítsa le a virtuális gépet használt, ellenőrizze a portálon a virtuális gép állapota helyes-e. Az állapota nem megfelelő, ha a **leállítási** a virtuális gép leállítása a beállítás a virtuális gép portal irányítópultján.
4. Ha több mint négy virtuális gépet ugyanazon a felhőszolgáltatáson, helyezkednek el a virtuális gépek több biztonsági mentési szabályzat. Eltolja abban az esetben a biztonsági mentés időpontjai így nincs négynél több virtuális gép biztonsági mentései kezdenek egyszerre. Próbálja meg külön a kezdési idejének legalább egy órával azokban a házirendekben.
5. Virtuális gép fut, magas CPU/memória.<br>
   Ha a virtuális gép fut magas memória-és CPU usage(>90%), a pillanatkép-feladat várólistára van állítva, késleltetett és végül időtúllépés következik. Ha ez történik, próbálja meg egy igény szerinti biztonsági mentést.

<br>

## <a name="networking"></a>Hálózat
Az összes bővítmény, például a Backup bővítmény működjön a nyilvános interneten hozzá kell férniük. Nem férhető hozzá a nyilvános interneten is manifest maga különböző módokon:

* A bővítmény telepítése sikertelen lehet
* A biztonsági mentési műveletek (mint például a lemez-pillanatképből) sikertelen lehet
* A biztonsági mentési művelet állapotának megjelenítésével sikertelen lehet

A nyilvános internet-címek feloldása szükségességét rendelkezik lett csuklós [Itt](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Ellenőrizze a virtuális hálózat DNS-konfigurációját, és győződjön meg arról, hogy URI-azonosítókat Azure feloldható kell.

Miután a névfeloldás megfelelően megtörtént, az Azure IP-címek elérését is kell megadni. Az Azure-infrastruktúra való hozzáférés feloldása, kövesse az alábbi lépéseket:

1. Az Azure-adatközpont engedélyezett IP-címtartományok.
   * Listájának [Azure datacenter IP-címek](https://www.microsoft.com/download/details.aspx?id=41653) az engedélyezéshez.
   * A segítségével IP-címek feloldása a [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) parancsmagot. Ez a parancsmag az Azure virtuális Gépen belül futtassa egy emelt szintű PowerShell-ablakban (Futtatás rendszergazdaként).
   * Adjon hozzá szabályokat az NSG-hez (ha van érvényben) az IP-címek hozzáférésének engedélyezéséhez.
2. A HTTP-forgalom áramlását elérési utat hoz létre
   * Ha rendelkezik bizonyos hálózati korlátozás helyen (egy hálózati biztonsági csoportot, a példában) a forgalom a HTTP-proxykiszolgáló üzembe helyezése. Egy HTTP-Proxy kiszolgáló telepítése is található [Itt](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   * Adjon hozzá szabályokat az NSG-hez (ha van érvényben) a HTTP-Proxy az internetes hozzáférést.

> [!NOTE]
> A Vendég az IaaS virtuális gépek biztonsági mentését működéséhez engedélyezni kell a DHCP.  Ha statikus magánhálózati IP-cím van szüksége, konfigurálnia kell azt a platformról. A virtuális gép DHCP-beállítást engedélyezni kell a bal oldalon.
> További információk megtekintéséhez kapcsolatos [statikus belső magánhálózati IP-cím beállítása](../virtual-network/virtual-networks-reserved-private-ip.md).
>
>
