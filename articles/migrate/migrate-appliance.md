---
title: Azure Migrate-berendezés
description: Áttekintést nyújt az Azure Áttelepítési készülék kiszolgáló felmérésés áttelepítése során használt.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: bccf4738d46b65f2d149eafc8e69591141d7d073
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437592"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-berendezés

Ez a cikk összegzi az Azure Migrate-berendezés előfeltételeit és támogatási követelményeit. 

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

Az Azure Migrate készülék a következő forgatókönyvekben használatos.

**Forgatókönyv** | **Eszköz** | **Alkalmazási cél** 
--- | --- | ---
**VMware VM értékelés** | Azure Áttelepítés:Kiszolgálófelmérés | Fedezze fel a VMware virtuális gépeket<br/><br/> Gépi alkalmazások és függőségek felderítése<br/><br/> A gépek metaadatait és teljesítménymetaadatait az értékelésekhez gyűjtheti.
**VMware VM ügynök nélküli áttelepítés** | Azure áttelepítése:Kiszolgáló áttelepítése | Fedezze fel a VMware virtuális gépeket <br/><br/> VMware virtuális gépek replikálása ügynök nélküli áttelepítéssel.
**Hyper-V VM értékelés** | Azure Áttelepítés:Kiszolgálófelmérés | Hyper-V virtuális gépek felfedezése<br/><br/> A gépek metaadatait és teljesítménymetaadatait az értékelésekhez gyűjtheti.
**Fizikai gép felmérése** |  Azure Áttelepítés:Kiszolgálófelmérés |  Fedezze fel a fizikai kiszolgálókat (vagy a fizikai kiszolgálóként kezelt virtuális gépeket).<br/><br/> A gépek metaadatait és teljesítménymetaadatait az értékelésekhez gyűjtheti.

## <a name="appliance---vmware"></a>Készülék - VMware 

Az alábbi táblázat összefoglalja az Azure Migrate appliance követelményeinek VMware.

**Követelmény** | **Vmware** 
--- | ---
**A készülék alkatrészei** | A készülék a következő összetevőkből áll:<br/><br/> - **Felügyeleti alkalmazás:** Ez egy webes alkalmazás a felhasználói bevitel hez a berendezés üzembe helyezése során. Az Azure-ba való áttelepítéshez szükséges gépek értékelésekor használatos.<br/> - **Felderítési ügynök**: Az ügynök összegyűjti a gép konfigurációs adatait. Az Azure-ba való áttelepítéshez szükséges gépek értékelésekor használatos.<br/>- **Értékelő ügynök**: Az ügynök teljesítményadatokat gyűjt. Az Azure-ba való áttelepítéshez szükséges gépek értékelésekor használatos.<br/>- **Automatikus frissítési szolgáltatás**: Frissíti a készülék összetevőit (24 óránként fut).<br/>- **DRA-ügynök:** VM-replikáció vezénylése, és koordinálja a replikált gépek és az Azure közötti kommunikációt. Csak akkor használatos, ha a VMware virtuális gépek replikálása az Azure-ba ügynök nélküli áttelepítés használatával.<br/>- **Átjáró**: Replikált adatokküldése az Azure-ba. Csak akkor használatos, ha a VMware virtuális gépek replikálása az Azure-ba ügynök nélküli áttelepítés használatával.
**Támogatott telepítés** | Üzembe helyezése VMware VM-ként OVA sablon használatával.<br/><br/> Telepítse vmware virtuális gépként vagy fizikai gépként a PowerShell telepítési parancsfájl használatával.
**Projekttámogatás** |  Egy készülék egyetlen projekthez társítható. <br/> Egyetlen projekthez tetszőleges számú készülék társítható.<br/> 
**Felderítési korlátok** | Egy készülék legfeljebb 10 000 VMware virtuális gépet fedezhet fel egy vCenter-kiszolgálón.<br/> Egy készülék egyetlen vCenter-kiszolgálóhoz csatlakozhat.
**OVA sablon** | Töltse le a https://aka.ms/migrate/appliance/vmwareportálról vagy a ból.<br/><br/> A letöltés imérete 11,2 GB.<br/><br/> A letöltött készüléksablon windows Server 2016 kiértékelési licenccel rendelkezik, amely 180 napig érvényes. Ha a kiértékelési időszak közel áll a lejárathoz, javasoljuk, hogy töltsön le és telepítsen egy új berendezést, vagy aktiválja a készülék virtuális gépének operációs rendszerlicencét.
**PowerShell-szkript** | Script [letöltés](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Szoftver/hardver** |  A készüléknek Windows Server 2016, 32 GB RAM, 8 vCPU-k, körülbelül 80 GB lemezes tárhely és külső virtuális kapcsoló segítségével kell futnia.<br/> A készülék internet-hozzáférésre van szüksége, akár közvetlenül, akár proxyn keresztül.<br/><br/> Ha a készüléket egy VMware virtuális gép, elegendő erőforrásra van szüksége a vCenter Server lefoglalni egy virtuális gép, amely megfelel a követelményeknek.<br/><br/> Ha a készüléket fizikai számítógépen futtatja, győződjön meg arról, hogy Windows Server 2016 rendszert futtat, és megfelel a hardverkövetelményeknek. 
**VMware követelmények** | Ha a készüléket VMware virtuális gépként telepíti, azt az 5.5-ös vagy újabb verziójú ESXi-gazdagépen kell telepíteni.<br/><br/> Az 5.5, 6.0, 6.5 vagy 6.7 rendszert futtató vCenter Server.
**VDDK (ügynök nélküli áttelepítés)** | Ha a készüléket VMware vm-ként telepíti, és ügynök nélküli áttelepítést futtat, a VMware vSphere VDDK-t telepíteni kell a készülék virtuális gépére.
**Kivonatérték-OVA** | [Ellenőrizze](tutorial-assess-vmware.md#verify-security) az OVA sablon kivonatértékeit.
**Kivonatérték-PowerShell-parancsfájl** | [Ellenőrizze](deploy-appliance-script.md#verify-file-security) a PowerShell-parancsfájl kivonatértékeit.




## <a name="appliance---hyper-v"></a>Készülék - Hyper-V

**Követelmény** | **Hyper-V** 
--- | ---
**A készülék alkatrészei** | A készülék a következő összetevőkből áll:<br/><br/>- **Felügyeleti alkalmazás:** Ez egy webes alkalmazás a felhasználói bevitel hez a berendezés üzembe helyezése során. Az Azure-ba való áttelepítéshez szükséges gépek értékelésekor használatos.<br/> - **Felderítési ügynök**: Az ügynök összegyűjti a gép konfigurációs adatait. Az Azure-ba való áttelepítéshez szükséges gépek értékelésekor használatos.<br/>- **Értékelő ügynök**: Az ügynök teljesítményadatokat gyűjt. Az Azure-ba való áttelepítéshez szükséges gépek értékelésekor használatos.<br/>- **Automatikus frissítési szolgáltatás**: Frissíti a készülék összetevőit (24 óránként fut).
**Támogatott telepítés** | Üzembe helyezése Hyper-V virtuális gépként egy VHD-sablon használatával.<br/><br/> Telepítse egy Hyper-V virtuális gép vagy fizikai gép egy PowerShell telepítőparancsfájl használatával.
**Projekttámogatás** |  Egy készülék egyetlen projekthez társítható. <br/> Egyetlen projekthez tetszőleges számú készülék társítható.<br/> 
**Felderítési korlátok** | Egy készülék akár 5000 Hyper-V virtuális gépet is felfedezhet.<br/> Egy készülék legfeljebb 300 Hyper-V állomáshoz tud csatlakozni.
**VHD-sablon** | Cipzáras mappa, beleértve a VHD-t is. Töltse le a https://aka.ms/migrate/appliance/hypervportálról vagy a ból.<br/><br/> A letöltés imérete 10 GB.<br/><br/> A letöltött készüléksablon windows Server 2016 kiértékelési licenccel rendelkezik, amely 180 napig érvényes. Ha a kiértékelési időszak közel áll a lejárathoz, javasoljuk, hogy töltsön le és telepítsen egy új berendezést, vagy aktiválja a készülék virtuális gépének operációs rendszerlicencét.
**PowerShell-szkript** | Script [letöltés](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> 
**Szoftver/hardver***   |  A készüléknek Windows Server 2016, 32 GB RAM, 8 vCPU-k, körülbelül 80 GB lemezes tárhely és külső virtuális kapcsoló segítségével kell futnia.<br/> A készüléknek statikus vagy dinamikus IP-címre van szüksége, és internet-hozzáférést igényel, akár közvetlenül, akár proxyn keresztül.<br/><br/> Ha a készüléket Hyper-V virtuális gépként futtatja, elegendő erőforrásra van szüksége a Hyper-V gazdagépen a 16 GB RAM, 8 vCPU- k, a mintegy 80 GB tárhely és a készülék virtuális gépkülső kapcsolójának lefoglalásához.<br/><br/> Ha a készüléket fizikai számítógépen futtatja, győződjön meg arról, hogy Windows Server 2016 rendszert futtat, és megfelel a hardverkövetelményeknek. 
**Hyper-V követelmények** | Ha telepíti a készüléket a Virtuálismerevlemez-sablon, a készülék virtuális gép által biztosított Azure Migrate a Hyper-V VM 5.0-s verziója.<br/><br/> A Hyper-V állomásnak Windows Server 2012 R2 vagy újabb rendszert kell futtatnia. 
**Kivonatérték-VHD** | [Ellenőrizze](tutorial-assess-hyper-v.md#verify-security) a VHD-sablon kivonatértékeit.
**Kivonatérték-PowerShell-parancsfájl** | [Ellenőrizze](deploy-appliance-script.md#verify-file-security) a PowerShell-parancsfájl kivonatértékeit.


## <a name="appliance---physical"></a>Készülék - Fizikai

**Követelmény** | **Fizikai** 
--- | ---
**A készülék alkatrészei** | A készülék a következő összetevőkből áll: <br/><br/> - **Felügyeleti alkalmazás:** Ez egy webes alkalmazás a felhasználói bevitel hez a berendezés üzembe helyezése során. Az Azure-ba való áttelepítéshez szükséges gépek értékelésekor használatos.<br/> - **Felderítési ügynök**: Az ügynök összegyűjti a gép konfigurációs adatait. Az Azure-ba való áttelepítéshez szükséges gépek értékelésekor használatos.<br/>- **Értékelő ügynök**: Az ügynök teljesítményadatokat gyűjt. Az Azure-ba való áttelepítéshez szükséges gépek értékelésekor használatos.<br/>- **Automatikus frissítési szolgáltatás**: Frissíti a készülék összetevőit (24 óránként fut).
**Támogatott telepítés** | Üzembe helyezése dedikált fizikai gépként vagy virtuális gépként egy PowerShell-telepítési parancsfájl használatával.
**Projekttámogatás** |  Egy készülék egyetlen projekthez társítható. <br/> Egyetlen projekthez tetszőleges számú készülék társítható.<br/> 
**Felderítési korlátok** | Egy készülék legfeljebb 250 fizikai kiszolgálót képes felderíteni.
**PowerShell-szkript** | Töltse le a parancsfájlt (AzureMigrateInstaller.ps1) egy tömörített mappában a portálról. [További információ](tutorial-assess-physical.md#set-up-the-appliance). Másik lehetőségként [töltse le közvetlenül](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> A letöltés imérete 59,7 MB.
**Szoftver/hardver** |  A készüléknek Windows Server 2016, 32 GB RAM, 8 vCPU-k, körülbelül 80 GB lemezes tárhely és külső virtuális kapcsoló segítségével kell futnia.<br/> A készüléknek statikus vagy dinamikus IP-címre van szüksége, és internet-hozzáférést igényel, akár közvetlenül, akár proxyn keresztül.<br/><br/> Ha a készüléket fizikai számítógépen futtatja, győződjön meg arról, hogy Windows Server 2016 rendszert futtat, és megfelel a hardverkövetelményeknek. 
**Kivonat értéke** | [Ellenőrizze](deploy-appliance-script.md#verify-file-security) a PowerShell-parancsfájl kivonatértékeit.

## <a name="url-access"></a>URL-hozzáférés

Az Azure Migrate készülék internetkapcsolatra van szüksége.

- A készülék üzembe helyezésekor az Azure Migrate kapcsolódási ellenőrzést végez az alábbi táblázatban összefoglalt URL-címeken.
- Ha URL-alapú proxyt használ az internethez való csatlakozáshoz, engedélyeznie kell a hozzáférést ezekhez az URL-címekhez, ügyelve arra, hogy a proxy feloldja az URL-címek felkeresése közben kapott CNAME rekordokat.

**Url** | **Részletek**  
--- | --- |
*.portal.azure.com  | Lépjen az Azure Portalra.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Jelentkezzen be Azure-előfizetésbe.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Hozzon létre Azure Active Directory (AD) alkalmazásokat a készülék számára az Azure Migrate szolgáltatással való kommunikációhoz.
management.azure.com | Hozzon létre Azure AD-alkalmazásokat a készülék számára az Azure Migrate szolgáltatással való kommunikációhoz.
dc.services.visualstudio.com | A belső figyeléshez használt alkalmazásnaplók feltöltése.
*.vault.azure.net | Az Azure Key Vaultban kezelheti a titkos kulcsokat.
aka.ms/* | Hozzáférés engedélyezése más néven hivatkozásokhoz. Az Azure Migrate készülékfrissítéseinek használt.
download.microsoft.com/download | Letöltések engedélyezése a Microsoft letöltéséből.
*.servicebus.windows.net | Kommunikáció a készülék és az Azure Áttelepítés szolgáltatás között.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Csatlakozzon az Azure Migrate szolgáltatás URL-címeihez.
*.hypervrecoverymanager.windowsazure.com | **VMware ügynök nélküli áttelepítéshez használható**<br/><br/> Csatlakozzon az Azure Migrate szolgáltatás URL-címeihez.
*.blob.core.windows.net |  **VMware ügynök nélküli áttelepítéshez használható**<br/><br/>Adatok feltöltése a tárolóba áttelepítéshez.




## <a name="collected-data---vmware"></a>Összegyűjtött adatok - VMware

A készülék metaadatokat, teljesítményadatokat és függőségelemzési adatokat gyűjt (ügynök nélküli [függőségelemzés](concepts-dependency-visualization.md) esetén).

### <a name="metadata"></a>Metaadatok

Az Azure Migrate készülék által felderített metaadatok segítségével megállapíthatja, hogy a gépek és az alkalmazások készen állnak-e az Azure-ba való migrálásra, a megfelelő méretű gépekre és alkalmazásokra, a csomagok költségeire, és elemezheti-e az alkalmazásfüggőségeket. A Microsoft ezeket az adatokat nem használja fel a licencmegfelelőségi naplózásban.

Íme a VMware VM metaadatainak teljes listája, amelyeket a készülék gyűjt és küld az Azure-nak.

**Adatok** | **Counter**
--- | --- 
**A gép adatai** | 
A virtuális gép azonosítója | Vm. Config.InstanceUuid 
a virtuális gép neve | Vm. Config.Name
vCenter-kiszolgálóazonosító | VMwareClient.Instance.Uuid
Virtuális gép leírása | Vm. Summary.Config.Annotation
Licenctermék neve | Vm. Client.ServiceContent.About.LicenseProductName
Operációs rendszer típusa | Vm. SummaryConfig.GuestFullName
Rendszerindítás típusa | Vm. Config.Firmware
Magok száma | Vm. Config.Hardware.NumCPU
Memória (MB) | Vm. Config.Hardware.MemoryMB
Lemezek száma | Vm. Config.Hardware.Device.ToList(). FindAll(x => is VirtualDisk).count
Lemezméret-lista | Vm. Config.Hardware.Device.ToList(). FindAll(x => is VirtualDisk)
Hálózati adapterek listája | Vm. Config.Hardware.Device.ToList(). FindAll(x => is VirtualEthernet).count
Processzorhasználat | cpu.usage.average
Memóriakihasználtság |mem.usage.average
**Lemezenkénti részletek** | 
Lemezkulcs értéke | Lemez. Kulcs
Dikunit száma | Lemez. UnitNumber (Egységszáma)
Lemezvezérlő kulcsának értéke | Lemez. ControllerKey.Érték
Kiépített gigabájt | virtualDisk.DeviceInfo.Summary
Lemez neve | Lemez segítségével létrehozott érték. UnitNumber, lemez. Kulcs, lemez. ControllerKey.VAlue
Olvasási műveletek másodpercenként | virtualDisk.numberReadAveraged.average
Írási műveletek másodpercenként | virtualDisk.numberWriteAveraged.average
Olvasási átviteli sebesség (MB másodpercenként) | virtualDisk.read.average
Írási átviteli sebesség (MB másodpercenként) | virtualDisk.write.average
**Hálózati adapterenkénti részletek** | 
Hálózati adapter neve | Nic. Kulcs
MAC-cím | ((VirtualEthernetCard)nic). MacAddress cím
IPv4-címek | Vm. Guest.Net
IPv6-címek | Vm. Guest.Net
Olvasási átviteli sebesség (MB másodpercenként) | net.received.average
Írási átviteli sebesség (MB másodpercenként) | net.transmitted.average
**Leltár elérési útja részletei** | 
Név | Konténer. GetType(). név
Gyermekobjektum típusa | Konténer. Gyermektípus
Hivatkozás részletei | Konténer. MoRef között
Szülő adatai | Tároló.Szülő
Mappa részletei virtuális gépenként | ((Mappa)tároló). ChildEntity.Típus
Adatközpont részletei virtuális gépenként | ((Datacenter)container). VmFolder
Az adatközpont adatai gazdamappánként | ((Datacenter)container). HostFolder mappa
Fürt adatai állomásonként | ((ClusterComputeResource)tároló). Fogadó
Állomás részletei virtuális gépenként | ((HostSystem)tároló). Vm

### <a name="performance-data"></a>Teljesítményadatok


Íme a VMware virtuális gép teljesítményadatai, amelyeket a készülék gyűjt és küld az Azure-nak.

**Adatok** | **Counter** | **Az értékelés hatása**
--- | --- | ---
Processzorhasználat | cpu.usage.average | Ajánlott virtuális gép mérete/költség
Memóriakihasználtság | mem.usage.average | Ajánlott virtuális gép mérete/költség
Lemez olvasási átviteli sebessége (MB másodpercenként) | virtualDisk.read.average | A lemezméret, a tárolási költség, a virtuális gép méretének számítása
A lemez áteresztőnyílása (MB másodpercenként) | virtualDisk.write.average | A lemezméret, a tárolási költség, a virtuális gép méretének számítása
Lemezolvasási műveletek másodpercenként | virtualDisk.numberReadAveraged.average | A lemezméret, a tárolási költség, a virtuális gép méretének számítása
A lemez másodpercenkéntírja a műveleteket | virtualDisk.numberWriteAveraged.average  | A lemezméret, a tárolási költség, a virtuális gép méretének számítása
Hálózati adapter ek olvasási átviteli sebessége (MB másodpercenként) | net.received.average | A virtuális gép méretének számítása
A hálózati adapter átviteli sebesség (MB/másodperc) | net.transmitted.average  |A virtuális gép méretének számítása

### <a name="app-dependencies-metadata"></a>Alkalmazásfüggőségek metaadatai

Az ügynök nélküli függőségelemzés kapcsolati és feldolgozási adatokat gyűjt.

#### <a name="connection-data"></a>Csatlakozási adatok

Íme a kapcsolati adatok, amelyeket a készülék gyűjt az egyes virtuális gépek engedélyezve ügynök nélküli függőségelemzés. Ezeket az adatokat a rendszer elküldi az Azure-nak.

**Adatok** | **Használt parancs** 
--- | --- 
Helyi port | Netstat
Helyi IP-cím | Netstat
Távoli port | Netstat
Távoli IP-cím | Netstat
TCP-kapcsolat állapota | Netstat
Folyamatazonosító | Netstat
Nem. aktív kapcsolatok | Netstat

#### <a name="process-data"></a>Adatok feldolgozása
Íme a folyamat adatokat, amelyeket a készülék gyűjt az egyes virtuális gépek engedélyezve ügynök nélküli függőség elemzés. Ezeket az adatokat a rendszer elküldi az Azure-nak.

**Adatok** | **WMI osztály** | **WMI-osztály tulajdonság**
--- | --- | ---
Folyamatnév | Win32_Process | Végrehajtható elérési út
Argumentumok feldolgozása | Win32_Process | Commandline
Alkalmazásnév | Win32_Process | A ExecutablePath tulajdonság VersionInfo.ProductName paramétere

#### <a name="linux-vm-data"></a>Linux virtuális gép adatai

Itt van a kapcsolat és a feldolgozási adatokat, amelyek a készülék gyűjt minden Linux virtuális gép engedélyezve ügynök nélküli függőség elemzés. Ezeket az adatokat a rendszer elküldi az Azure-nak.

**Adatok** | **Használt parancs** 
--- | ---
Helyi port | Netstat 
Helyi IP-cím | Netstat 
Távoli port | Netstat 
Távoli IP-cím | Netstat 
TCP-kapcsolat állapota | Netstat 
Nem. aktív kapcsolatok | Netstat
Folyamatazonosító  | Netstat 
Folyamatnév | Ps
Argumentumok feldolgozása | Ps
Alkalmazásnév | dpkg vagy rpm



## <a name="collected-data---hyper-v"></a>Összegyűjtött adatok - Hyper-V

A készülék metaadatokat, teljesítményadatokat és függőségelemzési adatokat gyűjt (ügynök nélküli [függőségelemzés](concepts-dependency-visualization.md) esetén).

### <a name="metadata"></a>Metaadatok
Az Azure Migrate készülék által felderített metaadatok segítségével megállapíthatja, hogy a gépek és az alkalmazások készen állnak-e az Azure-ba való migrálásra, a megfelelő méretű gépekre és alkalmazásokra, a csomagok költségeire, és elemezheti-e az alkalmazásfüggőségeket. A Microsoft ezeket az adatokat nem használja fel a licencmegfelelőségi naplózásban.

Az alábbiakban a Hyper-V VM metaadatok teljes listáját tartalmazza, amelyeket a készülék gyűjt és küld az Azure-nak.

**Adatok* | **WMI OSZTÁLY** | **WMI OSZTÁLY TULAJDONSÁG**
--- | --- | ---
**A gép adatai** | 
A BIOS sorozatszáma Msvm_BIOSElement | BIOSSerialNumber
Virtuális gép típusa (Gen 1 vagy 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType típus
Virtuális gép megjelenítendő neve | Msvm_VirtualSystemSettingData | ElementName
Virtuális gép verziója | Msvm_ProcessorSettingData | VirtualQuantity (Virtuális mennyiség)
Memória (bájt) | Msvm_MemorySettingData | VirtualQuantity (Virtuális mennyiség)
A virtuális gép által felhasználható maximális memória | Msvm_MemorySettingData | Korlát
Dinamikus memória engedélyezve | Msvm_MemorySettingData | DynamicMemoryEnabled
Az operációs rendszer neve/verziója/Teljes tartományneve | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems névadatai
Virtuális gép energiaellátásának állapota | Msvm_ComputerSystem | EnabledState
**Lemezenkénti részletek** | 
Lemezazonosító | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Virtuális merevlemez típusa | Msvm_VirtualHardDiskSettingData | Típus
Virtuális merevlemez mérete | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Virtuális merevlemez szülője | Msvm_VirtualHardDiskSettingData | ParentPath (Szülőgörbe)
**Hálózati adapterenkénti részletek** | 
IP-címek (szintetikus hálózati adapterek) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP-kompatibilis (szintetikus hálózati adapterek) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-azonosító (szintetikus hálózati adapterek) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC-cím (szintetikus hálózati adapterek) | Msvm_SyntheticEthernetPortSettingData | Cím
NIC-azonosító (örökölt hálózati adapterek) | MsvmEmulatedEthernetPortSetting adatok | InstanceID
NIC MAC-azonosító (örökölt hálózati adapterek) | MsvmEmulatedEthernetPortSetting adatok | Cím

### <a name="performance-data"></a>Teljesítményadatok

Íme a Hyper VM teljesítményadatai, amelyeket a készülék gyűjt és küld az Azure-nak.

**Teljesítményszámláló osztálya** | **Counter** | **Az értékelés hatása**
--- | --- | ---
Hyper-V hipervizor virtuális processzor | %vendég futási idő | Ajánlott virtuális gép mérete/költség
Hyper-V dinamikus memória virtuális gép | Jelenlegi nyomás (%)<br/> Vendég látható fizikai memória (MB) | Ajánlott virtuális gép mérete/költség
Hyper-V virtuális tárolóeszköz | Olvasott bájt/másodperc | A lemezméret, a tárolási költség, a virtuális gép méretének számítása
Hyper-V virtuális tárolóeszköz | Írási bájt/másodperc | A lemezméret, a tárolási költség, a virtuális gép méretének számítása
Hyper-V virtuális hálózati adapter | Fogadott bájtok/másodperc | A virtuális gép méretének számítása
Hyper-V virtuális hálózati adapter | Elküldött bájtok/másodperc | A virtuális gép méretének számítása

- A processzorkihasználtság a virtuális géphez csatlakoztatott összes virtuális processzor összes használatának összege.
- A memória kihasználtsága (Aktuális nyomás * Vendég látható fizikai memória) / 100.
- A lemez- és hálózati kihasználtsági értékeket a felsorolt Hyper-V teljesítményszámlálókból gyűjti a rendszer.

## <a name="appliance-upgrades"></a>A készülék frissítései

A készülék frissítése az Azure Migrate ügynökök a készüléken futó frissítéseket. Ez automatikusan megtörténik, mert az automatikus frissítés alapértelmezés szerint engedélyezve van a készüléken. Az alapértelmezett beállítás módosításával manuálisan frissítheti az ügyintézőket.

- **Az automatikus frissítés kikapcsolása**: A beállításjegyzékben az HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" kulcs 0-ra (DWORD) való beállításával kapcsolja ki az automatikus frissítést. Ha úgy dönt, hogy manuális frissítéseket használ, fontos, hogy a készüléken lévő összes ügynököt egyszerre frissítse, a **frissítés** gombbal a készülék minden elavult ügynökéhez.
- **Manuális frissítés:** Manuális frissítések esetén győződjön meg arról, hogy frissíti a készülék összes ügynökét, a készülék minden egyes elavult ügynökéhez használja a **Frissítés** gombot. A frissítési beállítást bármikor visszakapcsolhatja az automatikus frissítésekre.

![Készülék automatikus frissítése](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>További lépések

- [További információ](how-to-set-up-appliance-vmware.md) a készülék VMware készülékének beállításáról.
- [További információ](how-to-set-up-appliance-hyper-v.md) a Hyper-V készülék beállításáról.
- [További információ](how-to-set-up-appliance-physical.md) a készülék fizikai kiszolgálókra való beállításáról.

