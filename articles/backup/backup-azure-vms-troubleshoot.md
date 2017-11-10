---
title: "A virtuális gép az Azure biztonsági mentési hibák elhárítása |} Microsoft Docs"
description: "Biztonsági mentés és visszaállítás az Azure virtuális gépek hibaelhárítása"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 73214212-57a4-4b57-a2e2-eaf9d7fde67f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: trinadhk;markgal;jpallavi;
ms.openlocfilehash: 5c4ea3e3714f6a3989a260937c2c67815a6dd6f7
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Azure-beli virtuális gépek biztonsági mentésének hibaelhárítása
> [!div class="op_single_selector"]
> * [Recovery services-tároló](backup-azure-vms-troubleshoot.md)
> * [Mentési tároló](backup-azure-vms-troubleshoot-classic.md)
>
>

Észlelt, miközben az Azure Backup segítségével információkat az alábbi táblázatban szereplő hibák is elháríthatók.

## <a name="backup"></a>Biztonsági mentés

### <a name="error-the-specified-disk-configuration-is-not-supported"></a>Hiba: A megadott lemezkonfiguráció nem támogatott

> [!NOTE]
> Biztonsági mentések rendelkező virtuális gépek támogatásához a private Preview verziójára kell > nem felügyelt 1 TB-os lemezeken. A részletekért tekintse meg [nagy virtuális gép biztonsági mentési támogatása a Private Preview verziójára](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a)
>
>

Azure Backup szolgáltatás jelenleg nem támogatja lemezméret [1023GB-nál nagyobb](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm). 
- Ha a lemez 1 TB-nál nagyobb [új lemez csatolása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) 1 Terabájtnál kisebb ezek <br>
- Ezután másolja újonnan létrehozott lemez(ek) 1 TB-nál kisebb méretű lemez 1TB-nál nagyobb az adatokat. <br>
- Győződjön meg arról, hogy minden adat másolta, és távolítsa el a nagyobb, mint 1 TB-os lemezeken
- Indítsa el a biztonsági mentés.

| Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- |
| Nem sikerült elvégezni a műveletet, mert a virtuális gép már nem létezik. – Állítsa le a virtuális gép védelmét a biztonsági mentési adatok törlése nélkül. További részletekért http://go.microsoft.com/fwlink/?LinkId=808124: |Ez akkor fordul elő, ha az elsődleges virtuális gép törlődik, de a biztonsági mentési házirend továbbra is fennáll, készítsen biztonsági másolatot a virtuális gépek keres. Ez a hiba elhárításához: <ol><li> Hozza létre újra a virtuális gépet az azonos nevű és ugyanazon erőforráscsoport neve [cloud service name]<br>(VAGY)</li><li> Állítsa le, vagy a biztonsági mentési adatok törlése nélkül a virtuális gép védelmét. [További részletekért](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Pillanatkép művelet sikertelen volt, mert nincs hálózati kapcsolat a virtuális gépen – győződjön meg arról, hogy a virtuális gép hálózati hozzáféréssel rendelkezik. Pillanatkép sikeres legyen vagy engedélyezett Azure datacenter IP-cím címtartományok, vagy állítsa be a hálózati hozzáféréshez proxykiszolgálót. További részletekért tekintse meg a http://go.microsoft.com/fwlink/?LinkId=800034. Ha a proxykiszolgáló már használ, győződjön meg arról, hogy helyesen vannak-e konfigurálva a proxykiszolgáló beállításai | Ez a hiba fordul elő, amikor a virtuális gépen a kimenő internetkapcsolat megtagadja. Internetkapcsolat szükség a Virtuálisgép-pillanatképét bővítmény alapul szolgáló lemez a virtuális gép pillanatképének elkészítéséhez. [További](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine) a letiltott hálózati hozzáférés pillanatkép-visszavonások megoldásával. |
| Virtuálisgép-ügynök nem tud kommunikálni az Azure Backup szolgáltatás. -A virtuális Gépnek legyen hálózati kapcsolata, és a Virtuálisgép-ügynök legfrissebb, és fut. Győződjön meg arról. További információkért tekintse meg http://go.microsoft.com/fwlink/?LinkId=800034 |Ez a hiba fordul elő, ha a probléma oka a Virtuálisgép-ügynök vagy az Azure-infrastruktúra hálózati hozzáférését blokkolja a valamilyen módon. [További](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vm-agent-unable-to-communicate-with-azure-backup) kapcsolatos hibakeresés virtuális gépet pillanatkép-problémák.<br> Ha a Virtuálisgép-ügynök nem okoz probléma merül fel, majd indítsa újra a virtuális Gépet. Esetenként a virtuális gép állapota nem megfelelő problémákat okozhatnak, és a virtuális gép újraindítása alaphelyzetbe állítja a "hibás állapot". |
| A virtuális gép van kiépítési állapota sikertelen – indítsa újra a virtuális Gépet, és győződjön meg arról, hogy a virtuális gép biztonsági mentés futó vagy leállítási állapotban van-e | Ez akkor fordul elő, amikor egy, a bővítmény hibák részletes útmutatást a VM állapotot üzembe helyezési állapota sikertelen. Ugrás a kiterjesztések listája, és van-e sikertelen bővítmény, távolítsa el, és indítsa újra a virtuális gép. Ha az összes bővítmény futó állapotban van, ellenőrizze, hogy fut-e a virtuális gép agent szolgáltatást. Ha nem, indítsa újra a virtuális gép agent szolgáltatást. | 
| VMSnapshot bővítmény művelete sikertelen volt, a felügyelt lemez – próbálja megismételni a biztonsági mentési műveletet. Ha a probléma megismétlődik, kövesse "http://go.microsoft.com/fwlink/?LinkId=800034". Ha további sikertelen, forduljon a Microsoft támogatási szolgálatához | A hiba, ha Azure Backup szolgáltatás nem indítható el, egy pillanatkép. [További](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vmsnapshot-extension-operation-failed) kapcsolatos hibakeresés a virtuális gép pillanatkép-problémák. |
| Sikerült másolása a virtuális gép, mert a tárfiók - nincs elég szabad hely a pillanatkép győződjön meg arról, hogy a tárfiók a virtuális géphez csatolt a prémium szintű lemezein levő adatok egyenértékű szabad helye van | Prémium szintű virtuális gépeket, esetén tárfiók a pillanatkép másolja azt. Ez a, győződjön meg arról, hogy a biztonságimásolat-felügyeleti forgalmat, amely akkor működik a pillanatkép, nem a premium lemezek használó alkalmazások számára elérhető IOPS számának korlátozása. A Microsoft azt javasolja, hogy a teljes tárolóhelynek fiók csak 50 %-át foglal le, az Azure Backup szolgáltatás át tudja másolni a pillanatkép tárolási fiók és átviteli adatokat erről a helyről másolt tárfiókban a tárolóba. | 
| Nem sikerült végrehajtani a műveletet, a Virtuálisgép-ügynök nem válaszol |Ez a hiba fordul elő, ha a probléma oka a Virtuálisgép-ügynök vagy az Azure-infrastruktúra hálózati hozzáférését blokkolja a valamilyen módon. Windows virtuális gépek ellenőrizze a VM-ügynökszolgáltatás állapotát a szolgáltatások, és hogy az ügynök jelenik meg, a Vezérlőpult Programok telepítése. Távolítsa el a program vezérlőből panel és újratelepítése az ügynököt, ahogy azt korábban említettük [alatt](#vm-agent). Az ügynök újbóli telepítése után indul el, győződjön meg arról, hogy egy ad hoc biztonsági mentés. |
| Helyreállítási szolgáltatások művelet sikertelen volt. -Ellenőrizze, hogy, hogy a legújabb virtuálisgép-ügynök-e a virtuális gép, és ügynökszolgáltatás fut. Próbálja megismételni a biztonsági mentési műveletet, és ha a hiba, forduljon a Microsoft támogatási szolgálatához. |Ez a hiba fordul elő, ha Virtuálisgép-ügynök elavultak. Tekintse meg a "Frissítése a Virtuálisgép-ügynök" szakaszban látható a virtuális gép ügynökének frissítése. |
| Virtuális gép nem létezik. -Ellenőrizze, hogy a virtuális gép létezik-e, vagy válasszon másik virtuális gépet. |Ez akkor fordul elő, ha az elsődleges virtuális gép törlődik, de a biztonsági mentési házirend továbbra is fennáll, készítsen biztonsági mentést a virtuális gépek kereséséhez. Ez a hiba elhárításához: <ol><li> Hozza létre újra a virtuális gépet az azonos nevű és ugyanazon erőforráscsoport neve [cloud service name]<br>(VAGY)<br></li><li>Állítsa le a virtuális gép védelmét a biztonsági mentési adatok törlése nélkül. [További részletekért](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| A parancs végrehajtása sikertelen volt. – Egy másik művelet jelenleg folyamatban van erre az elemre. Várjon, amíg az előző művelet befejeződik, majd próbálkozzon újra |Meglévő biztonsági másolat a virtuális Gépen fut, és egy új feladat nem indítható el a meglévő feladat futása közben. |
| Másolja a VHD-k biztonsági mentési tárolóból túllépte az időkorlátot, mert a próbálja megismételni a műveletet, néhány perc múlva. Ha a probléma nem szűnik meg, forduljon a Microsoft ügyfélszolgálatához. | Ez akkor fordul elő, ha átmeneti hiba van a tárolási oldalon, vagy ha biztonsági mentési szolgáltatás nem kap elegendő IOPS a tárfiók a virtuális Gépet üzemeltető ahhoz, hogy az adatok átviteléhez a tároló időkorláton belül. Győződjön meg arról, hogy követte [ajánlott eljárások](backup-azure-vms-introduction.md#best-practices) beállítása a biztonsági mentés közben. Próbálja áthelyezni a virtuális gép egy másik tárhelyre fiók ami nincs betöltve, és próbálja meg újra biztonsági mentése.|
| Biztonsági mentés egy belső hiba miatt nem sikerült – a művelet néhány perc múlva próbálkozzon újra. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support |Ez a hibaüzenet 2 lehetnek az okai: <ol><li> Átmeneti jellegű probléma van a Virtuálisgép-tároló elérése közben. Győződjön meg róla [Azure állapot](https://azure.microsoft.com/en-us/status/) meg, egyetlen folyamatos számítási kapcsolatos problémát, a tárolási és a hálózat régióban. A probléma megoldása után próbálja újra a biztonsági mentési feladatot. <li>Az eredeti virtuális gép törölve lett, és ezért a helyreállítási pont nem végezhető. A biztonsági mentési adatok törölt virtuális gépeknél, de távolítsa el a biztonsági mentési hibák: oldja fel a virtuális Gépet, majd az adatok megőrzéséhez lehetőséget. Ez a művelet leáll az ütemezett biztonsági mentési feladatot, és az ismétlődő hibaüzenetek. |
| A kijelölt elem - az Azure Recovery Services bővítmény telepítése nem sikerült a virtuális gépi ügynöke előfeltétele az Azure Recovery Services-bővítmény. Az Azure Virtuálisgép-ügynök telepítése, és indítsa újra a regisztrációs műveletet |<ol> <li>Ellenőrizze, hogy ha a Virtuálisgép-ügynök megfelelően van telepítve. <li>Gondoskodjon arról, hogy megfelelően van-e beállítva a virtuális gép konfigurációs jelölőjét.</ol> [További](#validating-vm-agent-installation) szeretné telepíteni az ügynököt, és hogyan a Virtuálisgép-ügynök telepítésének ellenőrzése. |
| Bővítmény telepítése nem sikerült a "COM + nem tudta kérdezze meg a Microsoft Distributed Transaction Coordinator |Ez általában azt jelenti, hogy nem fut a COM + szolgáltatást. A probléma megoldásán segítségért forduljon a Microsoft támogatási szolgálatához. |
| Pillanatkép művelet nem sikerült a VSS művelet "ezen a meghajtón a BitLocker meghajtótitkosítás zárolta. Ezen a meghajtón, a Vezérlőpult kell zárolásának feloldásához. |Kapcsolja ki a BitLocker összes merevlemezén a virtuális Gépre, és figyelje meg, ha a VSS probléma megoldódott-e. |
| Virtuális gép nincs olyan állapotban, amely lehetővé teszi, hogy a biztonsági mentéseket. |<ul><li>Ellenőrizze, hogy virtuális gép átmeneti állapotban fut, valamint a leállítási között le. Ha igen, várjon, amíg a virtuális gép állapotát és indítás egyik biztonsági mentés ismét. <li> Ha a virtuális gép egy Linux virtuális gép és a használt [biztonsági fokozott Linux] kernel modul, szeretné-e kivétel a Linux-ügynök elérési útja (_/var/lib/waagent_) a biztonsági házirend számára, győződjön meg arról, hogy tartalék mellék telepíti.  |
| Az Azure virtuális gép nem található. |Ez akkor fordul elő, ha az elsődleges virtuális gép törlődik, de a biztonsági mentési házirend továbbra is tekintse meg a virtuális gépek biztonsági mentése végrehajtásához. Ez a hiba elhárításához: <ol><li>Hozza létre újra a virtuális gépet az azonos nevű és ugyanazon erőforráscsoport neve [cloud service name] <br>(VAGY) <li> Tiltsa le a védelmet a virtuális gép, ezért a biztonsági mentési feladatok nem lesz létrehozva. </ol> |
| Virtuálisgép-ügynök nincs jelen a virtuális gépen – telepítse az Előfeltételek és a Virtuálisgép-ügynök, és indítsa újra a műveletet. |[További](#vm-agent) Virtuálisgép-ügynök telepítése, és a Virtuálisgép-ügynök telepítésének ellenőrzése. |
| Pillanatkép-művelet meghiúsult, mert a VSS-író megfelelő állapotban |Újra kell indítania (kötet árnyékmásolata szolgáltatást) VSS-író, hibás állapotú. Ennek érdekében, egy rendszergazda jogú parancssorból futtassa _vssadmin lista írók_. Kimenet tartalmazza az összes VSS-író és állapotát. Minden VSS-író amelynek állapota nem "[1] stabil" indítsa újra VSS-író a következő parancsokat egy rendszergazda jogú parancssorból történő futtatásával:<br> _net stop szolgáltatásnév_ <br> _a net start szolgáltatásnév_|
| A konfiguráció elemzési hiba miatt nem sikerült pillanatképet művelet |Ez akkor történik meg az MachineKeys címtár módosított engedélyek miatt: _%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br>Futtassa az alábbi parancsot, és győződjön meg róla, hogy MachineKeys könyvtár alapértelmezett:<br>_Icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> Az alapértelmezett engedélyek a következők:<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>Ha az alapértelmezettől eltérő MachineKeys directory engedélyek látja, adjon hajtsa végre a következő lépések végrehajtásával megfelelő engedélyekkel, törölje a tanúsítványt, és a biztonsági mentés.<ol><li>Javítsa ki az engedélyek MachineKeys könyvtárban.<br>Explorer biztonsági tulajdonságait és a speciális biztonsági beállítások használata az adott könyvtár engedélyek állítsa vissza az alapértelmezett értékeket, távolítsa el a felesleges (alapértelmezettnél) felhasználói objektum a könyvtárból, és győződjön meg arról, hogy a "mindenki" engedélyek volna a speciális hozzáféréshez:<br>-Mappa listázása / adatok olvasása <br>-Attribútumok olvasása <br>– Olvassa el a kiterjesztett attribútumok <br>-Fájlok létrehozása / adatok írása <br>-Mappák létrehozása / adatok hozzáfűzése<br>-Attribútumok írása<br>-A kiterjesztett attribútumok írása<br>-Engedélyek olvasása<br><br><li>Törölje az összes tanúsítvány "Kiállítva a következőnek" mező = "A Windows Azure felügyeleti a bővítmények" vagy "Windows Azure KSZT tanúsítvány létrehozója".<ul><li>[Nyissa meg a tanúsítványok (helyi számítógép)](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>Minden tanúsítvány törlése (személyi -> tanúsítványok) és "Kiállítva a következőnek" mező = "A Windows Azure felügyeleti a bővítmények" vagy "Windows Azure KSZT tanúsítvány létrehozója".</ul><li>Virtuális gép a biztonsági mentés elindítása. </ol>|
| Érvényesítése sikertelen volt, mert a virtuális gépet önmagában BEK van titkosítva. Biztonsági másolatok csak virtuális gépek BEK és KEK is titkosítani engedélyezhető. |Virtuális gép titkosítani kell a BitLocker titkosítási kulcs, mind a kiszolgáló fő titkosítási kulcs használatával. Ezt követően engedélyezni kell a biztonsági mentés. |
| Az Azure Backup szolgáltatás nincs elegendő jogosultsága Key Vault a biztonsági mentés a titkosított virtuális gépek. |A biztonsági mentési szolgáltatás meg kell adni ezeket az engedélyeket a PowerShellben említett lépéseket követve **biztonsági mentés engedélyezése** szakasza [PowerShell dokumentációs](backup-azure-vms-automation.md). |
|Telepítésének pillanatkép-bővítményt nem sikerült – a COM + nem tudta felvenni a Microsoft Distributed Transaction Coordinator | Próbálja meg elindítani a windows-szolgáltatás "COM + rendszer alkalmazás" (egy emelt szintű parancssorból - _net start COMSysApp_). <br>Indítása közben nem sikerül, ha adjon kövesse az alábbi lépéseket:<ol><li> Ellenőrizze, hogy a bejelentkezési fiók az "Elosztott tranzakciók koordinátora" szolgáltatás "Hálózati szolgáltatás". Ha nem, változtassa meg a "Hálózati szolgáltatás", indítsa újra a szolgáltatást, és próbálja meg elindítani a szolgáltatást "COM + rendszer alkalmazás". "<li>Ha az még nem indul el, távolítsa el/telepítése "Distributed Transaction Coordinator" szolgáltatás által a következő lépések a következők:<br> -Az MSDTC szolgáltatás leállítása<br> -Nyisson meg egy parancssort (cmd) <br> -Parancs futtatása "msdtc-eltávolítása" <br> -Parancs futtatása "msdtc-telepítés" <br> -Az MSDTC szolgáltatás elindítása<li>"COM + rendszer alkalmazás" windows szolgáltatás elindítása és az elindítása után indul el, biztonsági mentés a portálról.</ol> |
|  COM + hiba miatt nem sikerült pillanatképet művelet | A javasolt műveletet az "COM + rendszer alkalmazás" windows szolgáltatás újraindítása (egy emelt szintű parancssorból - _net start COMSysApp_). Ha a probléma továbbra is fennáll, indítsa újra a virtuális Gépet. Ha a virtuális gép újraindítása nem oldották meg [eltávolítása a VMSnapshot bővítmény](https://docs.microsoft.com/en-us/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load) és manuálisan kezdeményezi a biztonsági mentés. |
| Nem sikerült egy vagy több csatlakoztatási pont a fájlrendszer konzisztens pillanatképének készítése a virtuális gép rögzítése | Ehhez a következő lépések szükségesek: <ol><li>Ellenőrizze a fájlrendszer, az összes csatlakoztatott eszközök _"tune2fs"_ parancsot.<br> Példa: tune2fs -l/dev/sdb1 \| GREP "Filesystem állapot" <li>Válassza le az eszközöket, mely FileSystem állapota nem tiszta használatával _"umount"_ parancs <li> Az ilyen eszközök használatával FileSystemConsistency-ellenőrzés futtatása _"fsck"_ parancs <li> Csatlakoztassa újra az eszközök, és próbálja meg a biztonsági mentés.</ol> |
| Pillanatkép-művelet meghiúsult, mert hiba történt a biztonságos hálózati kommunikációs csatorna létrehozása | <ol><Li> Nyissa meg a Beállításszerkesztőt a regedit.exe futtassa egy emelt jogosultságszintű módban. <li> Azonosítsa az összes verzióját. A NetFramework rendszerben. Ezek meg adva a hierarchia "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft" beállításkulcs alatt <li> Az egyes. Beállításkulcs megtalálható NetFramework kulcsot következő hozzáadása: <br> "SchUseStrongCrypto" = dword: 00000001 </ol>|
| Pillanatkép-művelet meghiúsult, mert hiba történt a Visual Studio 2012-es Visual C++ újraterjeszthető csomag telepítése | A C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion és vcredist2012_x64 telepítése. Győződjön meg arról, hogy a beállításkulcs a használatának engedélyezése a szolgáltatás telepítési azaz beállításkulcs értékét helyes értékre van-e beállítva _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_ 3. és 4 nem értékre van állítva. Ha továbbra is telepítési problémák felől elérhető, indítsa újra telepítési szolgáltatásával futtatásával _MSIEXEC /UNREGISTER_ követ _MSIEXEC /REGISTER_ parancsot egy emelt szintű parancssorból.  |


## <a name="jobs"></a>Feladatok
| Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- |
| Megszakítása nem támogatott ilyen típusú feladatokat - Várjon, amíg a feladat befejeződik. |None |
| A feladat nem törölhető állapotban van,-várjon, amíg a feladat befejeződik. <br>VAGY<br> A kiválasztott feladat nem törölhető állapotban van,-várjon a feladat végrehajtásához. |A feladat minden valószínűség szerint, majdnem kész. Várjon, amíg a feladat befejeződött.|
| A feladatot nem lehet megszakítani, mert nincs folyamatban – törlését csak a támogatott feladatok, amely jelenleg folyamatban vannak. Adjon kísérlet megszakítása az egy folyamatban lévő feladat. |Ez akkor fordul elő egy átmeneti állapota miatt. Várjon egy percet, majd próbálja megismételni a visszavonási művelet. |
| Nem sikerült visszavonni a feladatot - Várjon, amíg a feladat befejeződik. |None |

## <a name="restore"></a>Visszaállítás
| Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- |
| Visszaállítás felhő belső hiba miatt sikertelen volt |<ol><li>A felhőalapú szolgáltatás, amelyhez visszaállítani kívánt DNS-beállításokkal van konfigurálva. Ellenőrizheti a <br>$deployment get-AzureDeployment - szolgáltatásnév "ServiceName" =-tárolóhely "Éles" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Ha nincs konfigurált címet, ez azt jelenti, hogy a DNS-beállítások vannak konfigurálva.<br> <li>Felhőalapú szolgáltatás, amelyre szeretné visszaállítani kívánt foglalt IP-cím van konfigurálva, és a meglévő virtuális gépek által a felhőalapú szolgáltatás leállított állapotban van.<br>Ellenőrizheti a felhőszolgáltatás van foglalt IP-cím a következő powershell-parancsmagok használatával:<br>$deployment = get-AzureDeployment - szolgáltatásnév "servicename"-"Éles" $tárolóhely DEP ReservedIPName <br><li>Állítsa vissza a következő speciális hálózati beállításokat a virtuális gép ugyanazon a felhőalapú szolgáltatás kívánt. <br>– Virtuális gépek a terheléselosztó-konfigurációja (külső és belső)<br>-A virtuális gépek a több foglalt IP-cím<br>-A virtuális gépek több hálózati adapterrel rendelkező<br>Válasszon ki egy új felhőalapú szolgáltatást a felhasználói felületen, vagy tekintse meg [szempontok visszaállítása](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) speciális hálózati konfigurációk rendelkező virtuális gépek.</ol> |
| A kijelölt DNS-név már használatban van – adjon meg egy másik DNS-nevet, és próbálkozzon újra. |A DNS-neve itt hivatkozik a felhőszolgáltatás neve (általában végződő. cloudapp.net). Ennek egyedinek kell lennie. Ha ezt a hibát észlel, akkor válasszon egy másik Virtuálisgép-nevet a visszaállítás során. <br><br> Ez a hiba csak az Azure-portálon a felhasználók számára látható. A PowerShell segítségével a visszaállítási művelet fogja sikertelen, mert csak visszaállítja a lemezeket, és nem hoz létre a virtuális gép. A hiba akkor lesz tapasztalt, a virtuális gép létrehozásakor explicit módon Ön által a lemezre visszaállítási művelet után. |
| Érvénytelen a megadott virtuális hálózati konfiguráció – adjon meg egy másik virtuális hálózati konfiguráció, és próbálkozzon újra. |None |
| A megadott felhőszolgáltatás használ egy fenntartott IP-cím, amelyek nem felelnek meg a visszaállítandó virtuális gép konfigurációs – adjon meg egy másik felhőalapú szolgáltatás, amely fenntartott IP-címet nem használja, vagy visszaállíthatja őket egy másik helyreállítási pontot válasszon. |None |
| A felhőalapú szolgáltatás elérte a bemeneti végpontok számára vonatkozó korlátozást, mert a másik felhőalapú szolgáltatást, vagy egy meglévő végpont használatával, próbálja megismételni a műveletet. |None |
| Biztonsági mentési tároló és a cél tárfiók két különböző régiókban – győződjön meg arról, hogy a visszaállítási művelet a megadott tárfiók ugyanabban a régióban Azure a biztonsági mentési tárolóval. |None |
| Tárfiók megadott a visszaállítási művelet nem támogatott – a storage-fiókok csak Basic vagy Standard helyileg redundáns vagy földrajzi redundancia replikációs beállítások támogatottak. Válasszon egy támogatott tárfiókot |None |
| A visszaállítási művelet a megadott Tárfiók típus nem online – győződjön meg arról, hogy online állapotban-e a visszaállítási művelet a megadott tárfiók |Ez azért fordulhat elő, az Azure Storage, illetve a nem tervezett kimaradás átmeneti hiba miatt. Válasszon másik tárolási fiókot. |
| Erőforráscsoport kvóta elérve - töröljön egyes erőforráscsoportok Azure-portálon, vagy lépjen kapcsolatba az Azure támogatási szolgálatától a korlátok növelését. |None |
| Nem létezik a kijelölt alhálózat – válasszon ki egy alhálózatot, amely létezik |None |
| A Backup szolgáltatás nem jogosult az Ön előfizetésében biztosított erőforrások elérésére. |A probléma megoldásához, részben leírt lépéseket követve első visszaállítása lemezek **visszaállítási biztonsági másolatba mentett lemezek** a [kiválasztása a virtuális gép visszaállítási konfigurációs](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Ezt követően lépésekkel PowerShell említett [hozzon létre egy virtuális Gépet visszaállított lemezekből](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) visszaállított lemezekből teljes virtuális gép létrehozásához. |

## <a name="backup-or-restore-taking-time"></a>Biztonsági mentési vagy helyreállítási időt vesz igénybe
Ha megjelenik a backup(>12 hours) vagy visszaállítási véve time(>6 hours):
* Megértéséhez [biztonságimásolat-készítési időpont tényezők](backup-azure-vms-introduction.md#total-vm-backup-time) és [idő visszaállításához tényezők](backup-azure-vms-introduction.md#total-restore-time).
* Győződjön meg arról, hogy kövesse [biztonsági mentéshez ajánlott eljárások](backup-azure-vms-introduction.md#best-practices). 

## <a name="vm-agent"></a>Virtuálisgép-ügynök
### <a name="setting-up-the-vm-agent"></a>A Virtuálisgép-ügynök beállítása
A Virtuálisgép-ügynök általában már szerepel az Azure katalógusában a létrehozott virtuális gépeket. A helyszíni adatközpontját a áttelepített virtuális gépek azonban nem áll a Virtuálisgép-ügynök van telepítve. Ilyen virtuális gépek a Virtuálisgép-ügynök telepítve kell lennie explicit módon.

Windows virtuális gépek:

* Töltse le és telepítse az [ügynök MSI-t](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek.
* Klasszikus virtuális gépekhez [frissítse a virtuális gép tulajdonságát](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) annak jelzésére, hogy az ügynök telepítve van-e. Ez a lépés nincs szükség a virtuális gépek erőforrás-kezelő.

Linux virtuális gépek:

* A telepítés legújabb terjesztési tárházból. A Microsoft **erősen ajánlott** csak a tárház terjesztési ügynök telepítése. A csomag neve információkért tekintse meg [Linux ügynök-tárház](https://github.com/Azure/WALinuxAgent) 
* Klasszikus virtuális gépekhez [frissítse a virtuális gép tulajdonságát](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) annak jelzésére, hogy az ügynök telepítve van-e. Ez a lépés nincs szükség a virtuális gépek erőforrás-kezelő.

### <a name="updating-the-vm-agent"></a>A virtuálisgép-ügynök frissítése
Windows virtuális gépek:

* A virtuálisgép-ügynök frissítése a [virtuálisgép-ügynök bináris fájljainak](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) újratelepítéséből áll. Azonban meg kell győződjön meg arról, hogy nincs biztonsági mentési művelet fut. a Virtuálisgép-ügynök frissítése közben.

Linux virtuális gépek:

* Kövesse az utasításokat [frissítése Linux Virtuálisgép-ügynök](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
A Microsoft **erősen ajánlott** frissítési ügynök csak terjesztési tárház keresztül. Nem ajánlott a közvetlenül a githubból a kód letöltése és frissítése. Ha a terjesztési ügynök legújabb verziójára nem érhető el, lépjen kapcsolatba terjesztési támogatási legújabb ügynök telepítésével kapcsolatos utasításokat. Ellenőrizheti a legújabb [Windows Azure Linux ügynök](https://github.com/Azure/WALinuxAgent/releases) github-tárházban található információk.

### <a name="validating-vm-agent-installation"></a>Virtuálisgép-ügynök telepítésének ellenőrzése
A Virtuálisgép-ügynök verziója, a Windows virtuális gépek ellenőrzésének módja:

1. Jelentkezzen be az Azure virtuális géphez, és lépjen abba a mappába *C:\WindowsAzure\Packages*. Itt találja a WaAppAgent.exe fájlt.
2. Kattintson jobb gombbal a fájlra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Részletek** lapot. A termék verziószáma mező lehet 2.6.1198.718 vagy újabb

## <a name="troubleshoot-vm-snapshot-issues"></a>Virtuális gép pillanatkép problémák elhárítása
Virtuális gép biztonsági mentése támaszkodik a mögöttes tároló pillanatkép parancsok kiadása. Nem tárolási vagy késést a hozzáférést egy pillanatkép-feladat végrehajtása okozhat a biztonsági mentési feladat sikertelen lesz. A következő pillanatkép-feladat hibát okozhat.

1. Tárhely hálózati elérhetőségét le van tiltva, NSG-t használ<br>
    További információ arról, hogy további [hálózati hozzáférés engedélyezése](backup-azure-vms-prepare.md#network-connectivity) tárolóhely-vagy engedélyezett IP-címek vagy proxykiszolgálón keresztül.
2. Sql Server biztonsági másolat konfigurált virtuális gépek okozhat a pillanatkép tevékenység késleltetés <br>
   Virtuális gép alapértelmezés szerint a biztonsági mentés problémák VSS teljes biztonsági mentés a Windows virtuális gépeken. A virtuális gépeken futó Sql Server-kiszolgálók és az Sql Server biztonsági másolat úgy van konfigurálva ez a késleltetés a pillanatkép végrehajtása okozhat. Ha biztonsági mentési hibák pillanatkép problémák miatt merülnek állítsa be a következő beállításkulcsot.

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```
3. Virtuális gép állapotának jelentés nem megfelelően, mert a virtuális gép RDP a leállítása.  <br>
   Ha leállítja a virtuális gép RDP, ellenőrizze újra a portálon a virtuális gép állapotának megfelelően megjelenik-e. Ha nem, akkor állítsa le a virtuális gép portálon VM irányítópulton "Leállítás" lehetőség használatával.
4. Ha négynél több virtuális gépek ugyanazt a felhőalapú szolgáltatás, állítsa be a előkészítése a biztonsági mentés idejének tehát nincs négynél több virtuális gép biztonsági mentések indulnak el egyszerre több biztonsági mentési házirend. Próbálja meg a biztonsági mentési indítási idő egy órával közötti házirendek egymástól terjednek.
5. Virtuális gép fut, magas Processzor/memória.<br>
   Ha a virtuális gép fut, a magas CPU usage(>90%) vagy a memória, pillanatkép-feladat várólistára, késleltetett, és végül fog lekérdezi az időtúllépés miatt megszakadt. Próbálja meg igény szerinti biztonsági mentést az ilyen helyzetekben.

<br>

## <a name="networking"></a>Hálózat
Az összes bővítmény, például a biztonsági mentés bővítmény a nyilvános internethez hozzáférés szükséges. Nem rendelkezik nyilvános internet-hozzáférés is manifest maga különböző módokon:

* A bővítmény telepítése sikertelen lehet
* A biztonsági mentési műveletek (például a lemez pillanatkép) sikertelen lehet
* A biztonsági mentési művelet állapotának megjelenítése sikertelen lehet

Nyilvános internetcímek feloldása szükség van lett csuklós [Itt](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Ellenőrizze a vnet DNS-konfigurációját, és győződjön meg arról, hogy az Azure URI hozzárendelhető kell.

Után a névfeloldás helyesen végezték el, az Azure IP-címek elérését is meg kell adni. Hozzáférés az Azure-infrastruktúra feloldásához kövesse az alábbi lépések egyikét:

1. Engedélyezett Azure datacenter IP-címtartományok.
   * Listájának [Azure datacenter IP-címek](https://www.microsoft.com/download/details.aspx?id=41653) kell szerepel az engedélyezési listán.
   * Az IP-címek használatával feloldása a [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) parancsmag. Futtassa ezt a parancsmagot, az Azure virtuális Gépen belül, egy emelt szintű PowerShell-ablakban (Futtatás rendszergazdaként).
   * Vegye fel szabályok az NSG-t (ha van érvényben) az IP-címek eléréséhez.
2. A HTTP-forgalom áramlását az elérési utat hoz létre
   * Ha rendelkezik néhány helyen (hálózati biztonsági csoport, például) a hálózati korlátozás a forgalmat a HTTP-proxy kiszolgáló központi telepítése. Egy HTTP-Proxy kiszolgáló telepítése is található [Itt](backup-azure-vms-prepare.md#network-connectivity).
   * Vegye fel szabályok az NSG-t (ha van érvényben) számára engedélyezi az INTERNET elérését a HTTP-Proxy.

> [!NOTE]
> A Vendég típusú infrastruktúra-szolgáltatási virtuális gép biztonsági mentés működéséhez engedélyezni kell a DHCP.  Ha statikus magánhálózati IP-címe van szüksége, úgy kell beállítania, a platform keresztül. A DHCP-beállítást a virtuális Gépen belül balra engedélyezni kell.
> További információk megjelenítéséhez kapcsolatos [egy statikus belső privát IP-cím beállítása](../virtual-network/virtual-networks-reserved-private-ip.md).
>
>
