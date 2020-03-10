---
title: Azure Migrate-berendezés
description: Áttekintést nyújt a kiszolgálók értékeléséhez és áttelepítéséhez használt Azure Migrate készülékről.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 1b1e35c3b7a9d98e57ec4261f6f913c370bbb365
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388921"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-berendezés

Ez a cikk a Azure Migrate berendezést ismerteti. A készüléket a [Azure Migrate: kiszolgáló-értékelési](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszköz használatával helyezheti üzembe az alkalmazások, az infrastruktúra és a munkaterhelések Microsoft Azureba való áttelepítésének felderítéséhez és értékeléséhez. A készüléket akkor is használja a rendszer, ha VMware virtuális gépeket telepít át az Azure-ba [Azure Migrate használatával: kiszolgálói értékelés](migrate-services-overview.md#azure-migrate-server-migration-tool) [ügynök nélküli áttelepítéssel](server-migrate-overview.md).

## <a name="appliance-overview"></a>Berendezések áttekintése

A Azure Migrate készüléket a következő esetekben használja a rendszer.

**Forgatókönyv** | **Eszköz** | **Használatban** 
--- | --- | ---
VMware virtuális gép | Azure Migrate: kiszolgáló értékelése<br/><br/> Azure Migrate: kiszolgáló áttelepítése | VMware virtuális gépek felderítése<br/><br/> Gépi alkalmazások és függőségek felderítése<br/><br/> A számítógép metaadatainak és a teljesítmény metaadatainak gyűjtése az értékelésekhez.<br/><br/> VMware virtuális gépek replikálása ügynök nélküli áttelepítéssel.
Hyper-V virtuális gép | Azure Migrate: kiszolgáló értékelése | Hyper-V virtuális gépek felderítése<br/><br/> A számítógép metaadatainak és a teljesítmény metaadatainak gyűjtése az értékelésekhez.
Fizikai gép |  Azure Migrate: kiszolgáló értékelése |  Fizikai kiszolgálók felderítése<br/><br/> A számítógép metaadatainak és a teljesítmény metaadatainak gyűjtése az értékelésekhez.

## <a name="appliance---vmware"></a>Készülék – VMware 

**Követelmény** | **VMware** 
--- | ---
**Letöltési formátum** | . OVA 
**Letöltési hivatkozás** | https://aka.ms/migrate/appliance/vmware 
**Letöltési méret** | 11,2 GB
**Engedély** | A letöltött Appliance-sablon egy Windows Server 2016 próbaverziós licenccel rendelkezik, amely 180 napig érvényes. Ha a próbaidőszak le van zárva, javasoljuk, hogy töltsön le és helyezzen üzembe egy új készüléket, vagy aktiválja a készülék virtuális gépe operációs rendszerének licencét.
**Üzembe helyezés** | A készüléket VMware virtuális gépként kell üzembe helyezni. Elegendő erőforrásra van szüksége a vCenter Server egy virtuális gép lefoglalásához 32 GB RAM-mal, 8 vCPU, körülbelül 80 GB lemezes tárterülettel és külső virtuális kapcsolóval.<br/> A berendezéshez közvetlenül vagy proxyn keresztül kell internet-hozzáférést igényelni.<br/> A készülék egyetlen vCenter Serverhoz tud csatlakozni.
**Hardver** | A vCenter erőforrásai a 32 GB-os RAM 8 vCPU, a 80 GB és egy külső virtuális kapcsoló használatával foglalják el a virtuális gépeket. 
**Kivonat értéke** | [Itt](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security) tekintheti meg
**vCenter-kiszolgáló/-gazdagép** | A berendezés virtuális gépnek a 5,5-es vagy újabb verzióját futtató ESXi-gazdagépre kell telepítenie.<br/><br/> vCenter Server fut 5,5, 6,0, 6,5 vagy 6,7.
**Azure Migrate projekt** | Egy készülék egyetlen projekthez is társítható. <br/> Tetszőleges számú berendezés társítható egyetlen projekthez.<br/> 
**Felfedezés** | A készülékek akár 10 000 VMware virtuális gépet is felfedezhetnek vCenter Serveron.<br/> Egy berendezés egyetlen vCenter Serverhoz tud csatlakozni.
**Berendezés összetevői** | Felügyeleti alkalmazás: webalkalmazás a berendezésben felhasználói bevitelhez a telepítés során.<br/> Felderítési ügynök: a számítógép konfigurációs adatainak összegyűjtése.<br/> Assessment Agent: teljesítményadatokat gyűjt.<br/> DRA: a virtuális gépek replikálását és a számítógépek/Azure közötti kommunikáció koordinálását koordinálja.<br/> Átjáró: replikált adatokat küld az Azure-nak.<br/> Automatikus frissítési szolgáltatás: összetevők frissítése (24 óránként fut).
**VDDK (ügynök nélküli áttelepítés)** | Ha ügynök nélküli áttelepítést futtat Azure Migrate kiszolgáló áttelepítésével, az VMware vSphere VDDK telepítve kell lennie a készülék virtuális gépén.


## <a name="appliance---hyper-v"></a>Készülék – Hyper-V

**Követelmény** | **Hyper-V** 
--- | ---
**Letöltési formátum** | Tömörített mappa (VHD-vel)
**Letöltési hivatkozás** | https://aka.ms/migrate/appliance/hyperv 
**Letöltési méret** | 10 GB
**Engedély** | A letöltött Appliance-sablon egy Windows Server 2016 próbaverziós licenccel rendelkezik, amely 180 napig érvényes. Ha a próbaidőszak le van zárva, javasoljuk, hogy töltsön le és helyezzen üzembe egy új készüléket, vagy aktiválja a készülék virtuális gépe operációs rendszerének licencét.
**Berendezések üzembe helyezése**   |  A berendezést Hyper-V virtuális gépként kell üzembe helyezni.<br/> Az Azure Migrate által biztosított Appliance VM a Hyper-V VM 5,0-es verziója.<br/> A Hyper-V-gazdagépen Windows Server 2012 R2 vagy újabb rendszernek kell futnia.<br/> A gazdagépnek elegendő helyet kell biztosítania 16 GB RAM, 8 vCPU, körülbelül 80 GB tárterület és egy külső kapcsoló lefoglalásához a készülék virtuális gépe számára.<br/> A készüléknek statikus vagy dinamikus IP-címnek, valamint internet-hozzáférésre van szüksége.
**Hardver** | A Hyper-V-gazdagépen található erőforrások 16 GB RAM-ot, 8 vCPU, körülbelül 80 GB tárterületet és külső kapcsolót foglalnak le a készülék virtuális géphez.
**Kivonat értéke** | [Itt](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v#verify-security) tekintheti meg
**Hyper-V gazdagép** | Windows Server 2012 R2 vagy újabb operációs rendszer fut.
**Azure Migrate projekt** | Egy készülék egyetlen projekthez is társítható. <br/> Tetszőleges számú berendezés társítható egyetlen projekthez.<br/> 
**Felfedezés** | Egy készülék legfeljebb 5000 Hyper-V virtuális gépet képes felderíteni.<br/> Egy készülék legfeljebb 300 Hyper-V gazdagéphez tud csatlakozni.
**Berendezés összetevői** | Felügyeleti alkalmazás: webalkalmazás a berendezésben felhasználói bevitelhez a telepítés során.<br/> Felderítési ügynök: a számítógép konfigurációs adatainak összegyűjtése.<br/> Assessment Agent: teljesítményadatokat gyűjt.<br/>  Automatikus frissítési szolgáltatás: összetevők frissítése (24 óránként fut).


## <a name="appliance---physical"></a>Berendezés – fizikai

**Követelmény** | **Fizikai** 
--- | ---
**Letöltési formátum** | Tömörített mappa (PowerShell-alapú telepítő parancsfájllal)
**Letöltési hivatkozás** | [Letöltési hivatkozás](https://go.microsoft.com/fwlink/?linkid=2105112)
**Letöltési méret** | 59,7 MB
**Hardver** | Dedikált fizikai gép vagy virtuális gép használata. A készüléket futtató gépnek 16 GB RAM-mal, 8 vCPU, körülbelül 80 GB tárhellyel és külső kapcsolóval kell rendelkeznie.<br/> A készüléknek statikus vagy dinamikus IP-címnek, valamint internet-hozzáférésre van szüksége.
**Kivonat értéke** | [Itt](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical#verify-security) tekintheti meg
**Operációs rendszer** | A készüléket futtató gépnek Windows Server 2016 rendszernek kell futnia. 
**Berendezések üzembe helyezése**   |  A készülék telepítőjének parancsfájlját a rendszer letölti a portálról (tömörített mappában). <br/> Bontsa ki a mappát, és futtassa a PowerShell-szkriptet (AzureMigrateInstaller. ps1).
**Felfedezés** | A berendezések akár 250 fizikai kiszolgálót is felfedezhetnek.
**Berendezés összetevői** | Felügyeleti alkalmazás: webalkalmazás a berendezésben felhasználói bevitelhez a telepítés során.<br/> Felderítési ügynök: a számítógép konfigurációs adatainak összegyűjtése.<br/> Assessment Agent: teljesítményadatokat gyűjt.<br/>  Automatikus frissítési szolgáltatás: összetevők frissítése (24 óránként fut).


## <a name="url-access"></a>URL-hozzáférés

Az Azure Migrate berendezésnek internetkapcsolatra van szüksége.

- A berendezés telepítésekor a Azure Migrate az alábbi táblázatban összefoglalt URL-címek kapcsolatát.
- Ha URL-alapú proxyt használ az internethez való csatlakozáshoz, engedélyezze a hozzáférést ezekhez az URL-címekhez, és győződjön meg arról, hogy a proxy feloldja az URL-címek keresésekor fogadott CNAME-rekordokat.

**URL-cím** | **Részletek**  
--- | --- |
*.portal.azure.com  | Navigáljon a Azure Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com | Jelentkezzen be az Azure-előfizetésbe.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Hozzon létre Active Directory alkalmazásokat, hogy a készülék kommunikáljon a Azure Migrateokkal.
management.azure.com | Hozzon létre Active Directory alkalmazásokat a készülékhez a Azure Migrate szolgáltatással való kommunikációhoz.
dc.services.visualstudio.com | A belső figyeléshez használt alkalmazás-naplók feltöltése.
*.vault.azure.net | A Azure Key Vault titkainak kezelése.
aka.ms/* | Hozzáférés engedélyezése az aka hivatkozásokhoz. Azure Migrate berendezés frissítéseihez használatos.
download.microsoft.com/download | Letöltés engedélyezése a Microsoft letöltéséről.
*.servicebus.windows.net | A készülék és a Azure Migrate szolgáltatás közötti kommunikáció.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*.hypervrecoverymanager.windowsazure.com | **VMware ügynök nélküli Migrálás esetén használatos**<br/><br/> Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*.blob.core.windows.net |  **VMware ügynök nélküli Migrálás esetén használatos**<br/><br/>Adatok feltöltése a Storage-ba áttelepítéshez.




## <a name="collected-data---vmware"></a>Összegyűjtött adatok – VMware

### <a name="collected-performance-data-vmware"></a>Összegyűjtött teljesítményadatok – VMware

Itt látható a VMware virtuális gép teljesítményadatokat, amelyet a készülék az Azure-ba gyűjt és küld.

**Adatok** | **Számláló** | **Értékelés hatása**
--- | --- | ---
Processzorhasználat | cpu.usage.average | Ajánlott virtuális gép mérete/díja
Memória kihasználtsága | mem.usage.average | Ajánlott virtuális gép mérete/díja
Lemez olvasási sebessége (MB/s) | virtualDisk.read.average | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Lemez írási sebessége (MB/s) | virtualDisk.write.average | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Lemez olvasási műveletei másodpercenként | virtualDisk.numberReadAveraged.average | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Lemez írási műveletei másodpercenként | virtualDisk.numberWriteAveraged.average  | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Hálózati adapter olvasási sebessége (MB/s) | net. Received. Average | A virtuális gép méretének kiszámítása
Hálózati adapter írási sebessége (MB/s) | net. továbbítandó. Average  |A virtuális gép méretének kiszámítása


### <a name="collected-metadata-vmware"></a>Összegyűjtött metaadatok – VMware

> [!NOTE]
> A Azure Migrate készülék által felderített metaadatok segítségével az alkalmazások az Azure-ba való áttelepítéskor, az Azure megfelelőségi elemzés, az alkalmazás-függőségi elemzés és a költséghatékonyság megtervezése révén könnyebben méretezhető alkalmazásait. A Microsoft nem használja ezeket az adattípusokat a licencek megfelelőségi ellenőrzéséhez képest.

Itt találja a készülék által gyűjtött és az Azure-ba küldött VMware VM-metaadatok teljes listáját.

**Adatok** | **Számláló**
--- | --- 
**Gép részletei** | 
VIRTUÁLIS GÉP AZONOSÍTÓJA | vm.Config.InstanceUuid 
a virtuális gép neve | vm.Config.Name
vCenter Server azonosítója | VMwareClient.Instance.Uuid
Virtuális gép leírása | vm.Summary.Config.Annotation
Licenc terméknév | vm.Client.ServiceContent.About.LicenseProductName
Operációs rendszer típusa | vm.SummaryConfig.GuestFullName
Rendszerindítás típusa | vm.Config.Firmware
Magok száma | vm.Config.Hardware.NumCPU
Memória (MB) | vm.Config.Hardware.MemoryMB
Lemezek száma | VM. Config. Hardware. Device. ToList (). FindAll (x = > VirtualDisk). darabszám
Lemez mérete lista | VM. Config. Hardware. Device. ToList (). FindAll (x = > VirtualDisk)
Hálózati adapterek listája | VM. Config. Hardware. Device. ToList (). FindAll (x = > VirtualEthernet). darabszám
Processzorhasználat | cpu.usage.average
Memória kihasználtsága |mem.usage.average
**/Lemez adatai** | 
Lemez kulcsának értéke | lemez. Kulcs
Dikunit száma | disk.UnitNumber
Lemezvezérlő-kulcs értéke | disk.ControllerKey.Value
Kiépített gigabájt | virtualDisk.DeviceInfo.Summary
Lemez neve | A lemez használatával generált érték. UnitNumber, lemez. Kulcs, lemez. ControllerKey. VAlue
Olvasási műveletek másodpercenként | virtualDisk.numberReadAveraged.average
Írási műveletek másodpercenként | virtualDisk.numberWriteAveraged.average
Olvasási sebesség (MB/s) | virtualDisk.read.average
Írási sebesség (MB/s) | virtualDisk.write.average
**Hálózati adapter adatai** | 
Hálózati adapter neve | hálózati. Kulcs
MAC-cím | ((VirtualEthernetCard)nic).MacAddress
IPv4-címek | VM. Guest.Net
IPv6-címek | VM. Guest.Net
Olvasási sebesség (MB/s) | net. Received. Average
Írási sebesség (MB/s) | net. továbbítandó. Average
**Leltár elérési útja – részletek** | 
Name (Név) | container.GetType().Name
Gyermekobjektum típusa | tároló. ChildType
Hivatkozás részletei | tároló. MoRef
Szülő részletei | Container. Parent
Mappa részletei virtuális gépenként | ((Folder)container).ChildEntity.Type
Adatközpont részletei virtuális gépenként | ((Datacenter)container).VmFolder
Az adatközpont részletei egy gazdagép mappájában | ((Datacenter)container).HostFolder
Fürt adatai egy gazdagépen | ((ClusterComputeResource)container).Host
Gazdagép adatai virtuális gépenként | ((HostSystem)container).VM

## <a name="collected-data---hyper-v"></a>Összegyűjtött adatok – Hyper-V

### <a name="collected-performance-data-hyper-v"></a>Összegyűjtött teljesítményadatok – Hyper-V

> [!NOTE]
> A Azure Migrate készülék által felderített metaadatok segítségével az alkalmazások az Azure-ba való áttelepítéskor, az Azure megfelelőségi elemzés, az alkalmazás-függőségi elemzés és a költséghatékonyság megtervezése révén könnyebben méretezhető alkalmazásait. A Microsoft nem használja ezeket az adattípusokat a licencek megfelelőségi ellenőrzéséhez képest.

Itt látható a virtuális gép által összegyűjtött és az Azure-ba küldött teljesítményadatok.

**Teljesítményszámláló osztálya** | **Számláló** | **Értékelés hatása**
--- | --- | ---
Hyper-V hypervisor virtuális processzor | Vendég futási ideje (%) | Ajánlott virtuális gép mérete/díja
Hyper-V dinamikus memória VM | Aktuális nyomás (%)<br/> Vendég látható fizikai memóriája (MB) | Ajánlott virtuális gép mérete/díja
Hyper-V virtuális tárolóeszköz | Olvasási bájt/másodperc | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Hyper-V virtuális tárolóeszköz | Írási bájt/másodperc | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Hyper-V Virtual Network adapter | Fogadott bájtok/másodperc | A virtuális gép méretének kiszámítása
Hyper-V Virtual Network adapter | Elküldett bájtok/másodperc | A virtuális gép méretének kiszámítása

- A CPU-kihasználtság a virtuális GÉPHEZ csatolt összes virtuális processzorhoz tartozó összes használat összege.
- A memória kihasználtsága (aktuális nyomás * vendég látható fizikai memória)/100.
- A rendszer a lemez-és hálózati kihasználtsági értékeket a felsorolt Hyper-V teljesítményszámlálók alapján gyűjti.

### <a name="collected-metadata-hyper-v"></a>Összegyűjtött metaadatok – Hyper-V

Az alábbi lista tartalmazza a Hyper-V VM-metaadatok teljes listáját, amelyeket a készülék az Azure-ba gyűjt és küld.

**Adatok** | **WMI-osztály** | **WMI-osztály tulajdonsága**
--- | --- | ---
**Gép részletei** | 
A BIOS sorozatszáma _ Msvm_BIOSElement | BIOSSerialNumber
Virtuális gép típusa (1. vagy 2. generációs) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Virtuális gép megjelenítendő neve | Msvm_VirtualSystemSettingData | ElementName
VM-verzió | Msvm_ProcessorSettingData | VirtualQuantity
Memória (bájt) | Msvm_MemorySettingData | VirtualQuantity
A virtuális gép által felhasználható maximális memória | Msvm_MemorySettingData | Korlát
Dinamikus memória engedélyezve | Msvm_MemorySettingData | DynamicMemoryEnabled
Operációs rendszer neve/verziója/teljes tartományneve | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems
Virtuális gép energiaellátási állapota | Msvm_ComputerSystem | EnabledState
**/Lemez adatai** | 
Lemez azonosítója | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Virtuális merevlemez típusa | Msvm_VirtualHardDiskSettingData | Típus
Virtuális merevlemez mérete | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Virtuális merevlemez szülőjének | Msvm_VirtualHardDiskSettingData | ParentPath
**Hálózati adapter adatai** | 
IP addresses (synthetic NICs) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP engedélyezve (szintetikus hálózati adapterek) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-azonosító (szintetikus hálózati adapterek) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Hálózati adapter MAC-címe (szintetikus hálózati adapterek) | Msvm_SyntheticEthernetPortSettingData | Cím
NIC-azonosító (örökölt hálózati adapterek) | MsvmEmulatedEthernetPortSetting Data | InstanceID
NIC MAC-azonosító (örökölt hálózati adapterek) | MsvmEmulatedEthernetPortSetting Data | Cím




## <a name="discovery-and-collection-process"></a>Felderítési és gyűjtési folyamat

A készülék a következő eljárással kommunikál a vCenter-kiszolgálókkal és a Hyper-V-gazdagépekkel/-fürtökkel.

1. **Felderítés elindítása**:
    - Amikor elindítja a felderítést a Hyper-V berendezésen, az a 5985-as (HTTP) és a 5986 (HTTPS) WinRM-portok Hyper-V-gazdagépekkel kommunikál.
    - Amikor elindítja a felderítést a VMware készüléken, az a 443-as TCP-porton keresztül kommunikál a vCenter-kiszolgálóval. Ha a vCenter-kiszolgáló egy másik portot figyel, konfigurálhatja azt a berendezés webalkalmazásban.
2. **Metaadatok és teljesítményadatok összegyűjtése**:
    - A készülék egy CIM-(CIM-) munkamenetet használ a Hyper-V virtuális gépek adatainak a Hyper-V-gazdagépről a 5985-es és 5986-es portokon való összegyűjtéséhez.
    - A készülék alapértelmezés szerint az 443-as porttal kommunikál a VMware virtuális gépek adatainak a vCenter Serverból való összegyűjtéséhez.
3. **Adatküldés**: a készülék elküldi az összegyűjtött adatokat Azure Migrate kiszolgáló értékelésére és Azure Migrate kiszolgáló áttelepítésére az 443-as SSL-porton keresztül. A készülék az interneten keresztül tud csatlakozni az Azure-hoz, vagy használhatja a ExpressRoute nyilvános/Microsoft-partneri kapcsolattal is.
    - A teljesítményadatok esetében a készülék valós idejű kihasználtsági adatokat gyűjt.
        - A teljesítményadatokat a rendszer minden egyes teljesítménymutató esetében 20 másodpercenként gyűjti a VMware-hez, és minden 30 másodpercenként a Hyper-V-hez.
        - Az összegyűjtött adatokat a rendszer összesíti, hogy egy adatpontot 10 percen belül hozzon létre.
        - A csúcsérték kihasználtsági értéke a 20/30 másodperces adatpontból van kiválasztva, és az Azure-ba az értékelés kiszámításához lett küldve.
        - Az értékelés tulajdonságaiban (50/90/95./esetek 99%) megadott százalékos érték alapján a tíz perces pont növekvő sorrendbe kerül, és az értékelés kiszámításához a megfelelő percentilis értéket használja a rendszer.
    - A kiszolgáló áttelepítése esetén a készülék elkezdi a virtuális gépekkel kapcsolatos adatok gyűjtését, és replikálja azt az Azure-ba.
4. **Értékelés és Migrálás**: most már létrehozhat értékeléseket a készülék által gyűjtött metaadatokból Azure Migrate Server Assessment használatával. Emellett a VMware virtuális gépek áttelepítését is megkezdheti Azure Migrate kiszolgáló áttelepítésével az ügynök nélküli virtuális gép replikálásának előkészítéséhez.


![Architektúra](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Berendezések frissítése

A készülék frissítve lett, mivel a készüléken futó Azure Migrate-ügynökök frissülnek.

- Ez automatikusan megtörténik, mert alapértelmezés szerint engedélyezve van az automatikus frissítés a készüléken.
- Az alapértelmezett beállítás módosításával manuálisan is frissítheti az ügynököket.
- Az automatikus frissítés letiltásához nyissa meg a Beállításszerkesztőt > HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance, és állítsa az "AutoUpdate" beállításkulcsot 0-ra (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Az ügynökök frissítésének beállítása kézire

Manuális frissítés esetén győződjön meg arról, hogy a készüléken lévő összes ügynököt egy időben frissíti, a készüléken lévő összes elavult ügynök **frissítés** gombjával. A frissítési beállítás bármikor visszaváltható az automatikus frissítésekre.

## <a name="next-steps"></a>Következő lépések

[Ismerje meg, hogyan](tutorial-assess-vmware.md#set-up-the-appliance-vm) állíthatja be a készüléket a VMware rendszerhez.
[Ismerje meg, hogyan](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) állíthatja be a készüléket a Hyper-V-hez.

