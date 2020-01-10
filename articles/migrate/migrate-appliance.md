---
title: Azure Migrate-berendezés
description: Áttekintést nyújt a kiszolgálók értékeléséhez és áttelepítéséhez használt Azure Migrate készülékről.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: c3ac39759cc096bb27535877084e14f4ed50cea9
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75719579"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-berendezés

Ez a cikk a Azure Migrate berendezést ismerteti. Ha Azure Migrate felmérési és áttelepítési eszközt használ az alkalmazások, az infrastruktúra és a munkaterhelések Microsoft Azure való felderítéséhez, értékeléséhez és áttelepítéséhez, akkor a készüléket üzembe helyezi. 

[Azure Migrate](migrate-services-overview.md) egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint a magán-és nyilvános Felhőbeli virtuális gépek felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz.



## <a name="appliance-overview"></a>Berendezések áttekintése

A Azure Migrate berendezés típusai és használata a következő.

**Forgatókönyv** | **Eszköz** | **Használatban** 
--- | --- 
VMware virtuális gép | Azure Migrate: kiszolgáló értékelése; Azure Migrate: kiszolgáló áttelepítése | VMware virtuális gépek felderítése<br/><br/> Alkalmazások és függőségek felderítése<br/><br/> A számítógép metaadatainak és a teljesítmény metaadatainak gyűjtése az értékelésekhez.<br/><br/> VMware virtuális gépek replikálása ügynök nélküli áttelepítéssel.
Hyper-V virtuális gép | Azure Migrate: kiszolgáló értékelése | Hyper-V virtuális gépek felderítése<br/><br/> A számítógép metaadatainak és a teljesítmény metaadatainak gyűjtése az értékelésekhez.
Fizikai gép |  Azure Migrate: értékelési eszköz |  Fizikai kiszolgálók felderítése<br/><br/> A számítógép metaadatainak és a teljesítmény metaadatainak gyűjtése az értékelésekhez.

## <a name="appliance---vmware"></a>Készülék – VMware 

**Követelmény** | **VMware** 
--- | ---
**Letöltési formátum** | . OVA 
**Letöltési hivatkozás** | https://aka.ms/migrate/appliance/vmware 
**Letöltési méret** | 11,2 GB
**Licenc** | A letöltött Appliance-sablon egy Windows Server 2016 próbaverziós licenccel rendelkezik, amely 180 napig érvényes. Ha a próbaidőszak le van zárva, javasoljuk, hogy töltsön le és helyezzen üzembe egy új készüléket, vagy aktiválja a készülék virtuális gépe operációs rendszerének licencét.
**Hardver** | A vCenter erőforrásai a 32 GB-os RAM 8 vCPU, a 80 GB és egy külső virtuális kapcsoló használatával foglalják el a virtuális gépeket. 
**Kivonat értéke** | MD5: c06ac2a2c0f870d3b274a0b7a73b78b1<br/><br/> SHA256:4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c
**vCenter-kiszolgáló/-gazdagép** | A berendezés virtuális gépnek a 5,5-es vagy újabb verzióját futtató ESXi-gazdagépre kell telepítenie.<br/><br/> vCenter Server fut 5,5, 6,0, 6,5 vagy 6,7.
**Azure Migrate projekt** | Egy készülék egyetlen projekthez is társítható. <br/> Tetszőleges számú berendezés társítható egyetlen projekthez.<br/> 
**Felderítés** | A készülékek akár 10 000 VMware virtuális gépet is felfedezhetnek vCenter Serveron.<br/> Egy berendezés egyetlen vCenter Serverhoz tud csatlakozni.
**Berendezés összetevői** | Felügyeleti alkalmazás: webalkalmazás a berendezésben felhasználói bevitelhez a telepítés során.<br/> Felderítési ügynök: a számítógép konfigurációs adatainak összegyűjtése.<br/> Assessment Agent: teljesítményadatokat gyűjt.<br/> DRA: a virtuális gépek replikálását és a számítógépek/Azure közötti kommunikáció koordinálását koordinálja.<br/> Átjáró: replikált adatokat küld az Azure-nak.<br/> Automatikus frissítési szolgáltatás: összetevők frissítése (24 óránként fut).


## <a name="appliance---hyper-v"></a>Készülék – Hyper-V

**Követelmény** | **Hyper-V** 
--- | ---
**Letöltési formátum** | Tömörített mappa (VHD-vel)
**Letöltési hivatkozás** | https://aka.ms/migrate/appliance/hyperv 
**Letöltési méret** | 10 GB
**Licenc** | A letöltött Appliance-sablon egy Windows Server 2016 próbaverziós licenccel rendelkezik, amely 180 napig érvényes. Ha a próbaidőszak le van zárva, javasoljuk, hogy töltsön le és helyezzen üzembe egy új készüléket, vagy aktiválja a készülék virtuális gépe operációs rendszerének licencét.
**Hardver** | A Hyper-V-gazdagépen található erőforrások 16 GB RAM-ot, 8 vCPU, körülbelül 80 GB tárterületet és külső kapcsolót foglalnak le a készülék virtuális géphez.
**Kivonat értéke** | MD5:29a7531f32bcf69f32d964fa5ae950bc<br/><br/> SHA256:37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31
**Hyper-V gazdagép** | Windows Server 2012 R2 vagy újabb operációs rendszer fut.
**Azure Migrate projekt** | Egy készülék egyetlen projekthez is társítható. <br/> Tetszőleges számú berendezés társítható egyetlen projekthez.<br/> 
**Felderítés** | A készülékek akár 5000 VMware virtuális gépet is felfedezhetnek vCenter Serveron.<br/> Egy készülék legfeljebb 300 Hyper-V gazdagéphez tud csatlakozni.
**Berendezés összetevői** | Felügyeleti alkalmazás: webalkalmazás a berendezésben felhasználói bevitelhez a telepítés során.<br/> Felderítési ügynök: a számítógép konfigurációs adatainak összegyűjtése.<br/> Assessment Agent: teljesítményadatokat gyűjt.<br/>  Automatikus frissítési szolgáltatás: összetevők frissítése (24 óránként fut)


## <a name="appliance---physical"></a>Berendezés – fizikai

**Követelmény** | **Fizikai** 
--- | ---
**Letöltési formátum** | Tömörített mappa (PowerShell-telepítő parancsfájllal)
**Letöltési hivatkozás** | [Letöltési hivatkozás](https://go.microsoft.com/fwlink/?linkid=2105112)
**Letöltési méret** | 59,7 MB
**Hardver** | A készüléket futtató gépnek 16 GB RAM-mal, 8 vCPU, körülbelül 80 GB tárhellyel kell rendelkeznie.
**Kivonat értéke** | MD5:96fd99581072c400aa605ab036a0a7c0<br/><br/> SHA256: f5454beef510c0aa38ac1c6be6346207c351d5361afa0c9cea4772d566fcdc36
**Szoftver** | A készülék számítógépének a Windows Server 2016-et kell futtatnia. A kiszolgálónak dedikált fizikai kiszolgálónak vagy virtuális gépnek kell lennie.
**Azure Migrate projekt** | Egy készülék egyetlen projekthez is társítható. <br/> Tetszőleges számú berendezés társítható egyetlen projekthez.<br/> 
**Felderítés** | A berendezések akár 250 fizikai kiszolgálót is felfedezhetnek.
**Berendezés összetevői** | Felügyeleti alkalmazás: webalkalmazás a berendezésben felhasználói bevitelhez a telepítés során.<br/> Felderítési ügynök: a számítógép konfigurációs adatainak összegyűjtése.<br/> Assessment Agent: teljesítményadatokat gyűjt.<br/>  Automatikus frissítési szolgáltatás: összetevők frissítése (24 óránként fut).
**Hozzáférés/portok** | Miután konfigurálta a készüléket, a 3389-as TCP-porton bejövő kapcsolatok használatával engedélyezheti a távoli asztali kapcsolatokat a készülékhez.<br/><br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez a következő URL-címen keresztül: "https://< Appliance-IP-vagy-Name >: 44368.<br/><br/> A 443-es, 5671-as és 5672-es porton kimenő kapcsolatok a felderítési és a teljesítménybeli metaadatok küldésére Azure Migrate.

## <a name="url-access"></a>URL-hozzáférés

Az Azure Migrate berendezésnek internetkapcsolatra van szüksége.

- A berendezés telepítésekor a Azure Migrate az alábbi táblázatban összefoglalt URL-címek kapcsolatát.
- Ha URL-alapú proxyt használ az internethez való csatlakozáshoz, engedélyezze a hozzáférést ezekhez az URL-címekhez, és győződjön meg arról, hogy a proxy feloldja az URL-címek keresésekor fogadott CNAME-rekordokat.

**URL-cím** | **Részletek**  
--- | --- |
*.portal.azure.com  | Lépjen az Azure Portalra.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *. live.com | Jelentkezzen be az Azure-előfizetésbe.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Hozzon létre Active Directory alkalmazásokat, hogy a készülék kommunikáljon a Azure Migrateokkal.
management.azure.com | Hozzon létre Active Directory alkalmazásokat a készülékhez a Azure Migrate szolgáltatással való kommunikációhoz.
dc.services.visualstudio.com | A belső figyeléshez használt alkalmazás-naplók feltöltése.
*.vault.azure.net | A Azure Key Vault titkainak kezelése.
aka.ms/* | Hozzáférés engedélyezése az aka hivatkozásokhoz.
download.microsoft.com/download | Letöltés engedélyezése a Microsoft letöltéséről.
*.servicebus.windows.net | A készülék és a Azure Migrate szolgáltatás közötti kommunikáció.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*.blob.core.windows.net | Adatok feltöltése a Storage-fiókba.


## <a name="collected-data---vmware"></a>Összegyűjtött adatok – VMware

### <a name="collected-performance-data-vmware"></a>Összegyűjtött teljesítményadatok – VMware

Itt látható a VMware virtuális gép teljesítményadatokat, amelyet a készülék az Azure-ba gyűjt és küld.

**Adatok** | **Számláló** | **Értékelés hatása**
--- | --- | ---
CPU-kihasználtság | CPU. használat. átlag | Ajánlott virtuális gép mérete/díja
Memória kihasználtsága | mem. használat. átlag | Ajánlott virtuális gép mérete/díja
Lemez olvasási sebessége (MB/s) | virtualDisk. Read. Average | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Lemez írási sebessége (MB/s) | virtualDisk. Write. Average | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Lemez olvasási műveletei másodpercenként | virtualDisk. numberReadAveraged. Average | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Lemez írási műveletei másodpercenként | virtualDisk. numberWriteAveraged. Average  | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Hálózati adapter olvasási sebessége (MB/s) | net. Received. Average | A virtuális gép méretének kiszámítása
Hálózati adapter írási sebessége (MB/s) | net. továbbítandó. Average  |A virtuális gép méretének kiszámítása


### <a name="collected-metadata-vmware"></a>Összegyűjtött metaadatok – VMware

> [!NOTE]
> A Azure Migrate készülék által felderített metaadatok segítségével az alkalmazások az Azure-ba való áttelepítéskor, az Azure megfelelőségi elemzés, az alkalmazás-függőségi elemzés és a költséghatékonyság megtervezése révén könnyebben méretezhető alkalmazásait. A Microsoft nem használja ezeket az adattípusokat a licencek megfelelőségi ellenőrzéséhez képest.

Itt találja a készülék által gyűjtött és az Azure-ba küldött VMware VM-metaadatok teljes listáját.

**Adatok** | **Számláló**
--- | --- 
**Gép részletei** | 
A virtuális gép azonosítója | VM. Config. értékű 
a virtuális gép neve | VM. Config.Name
vCenter Server azonosítója | VMwareClient. instance. UUID
Virtuális gép leírása | VM. Summary. config. Megjegyzés
Licenc terméknév | VM. Client. ServiceContent. about. LicenseProductName
Operációs rendszer típusa | VM. SummaryConfig.GuestFullName
Rendszerindítás típusa | VM. Config. firmware
Magok száma | VM. Config. Hardware. NumCPU
Memória (MB) | VM. Config. Hardware. MemoryMB
Lemezek száma | VM. Config. Hardware. Device. ToList (). FindAll (x = > VirtualDisk). darabszám
Lemez mérete lista | VM. Config. Hardware. Device. ToList (). FindAll (x = > VirtualDisk)
Hálózati adapterek listája | VM. Config. Hardware. Device. ToList (). FindAll (x = > VirtualEthernet). darabszám
CPU-kihasználtság | CPU. használat. átlag
Memória kihasználtsága |mem. használat. átlag
**/Lemez adatai** | 
Lemez kulcsának értéke | lemez. Kulcs
Dikunit száma | lemez. UnitNumber
Lemezvezérlő-kulcs értéke | lemez. ControllerKey. Value
Kiépített gigabájt | virtualDisk. DeviceInfo. Summary
Lemez neve | A lemez használatával generált érték. UnitNumber, lemez. Kulcs, lemez. ControllerKey. VAlue
Olvasási műveletek másodpercenként | virtualDisk. numberReadAveraged. Average
Írási műveletek másodpercenként | virtualDisk. numberWriteAveraged. Average
Olvasási sebesség (MB/s) | virtualDisk. Read. Average
Írási sebesség (MB/s) | virtualDisk. Write. Average
**Hálózati adapter adatai** | 
Hálózati adapter neve | hálózati. Kulcs
MAC-cím | ((VirtualEthernetCard) NIC). MacAddress
IPv4-címek | VM. Guest.Net
IPv6-címek | VM. Guest.Net
Olvasási sebesség (MB/s) | net. Received. Average
Írási sebesség (MB/s) | net. továbbítandó. Average
**Leltár elérési útja – részletek** | 
Név | tároló. GetType (). név
Gyermekobjektum típusa | tároló. ChildType
Hivatkozás részletei | tároló. MoRef
Szülő részletei | Container. Parent
Mappa részletei virtuális gépenként | ((Mappa) tároló). ChildEntity. Type
Adatközpont részletei virtuális gépenként | (Datacenter) tároló. VmFolder
Az adatközpont részletei egy gazdagép mappájában | (Datacenter) tároló. HostFolder
Fürt adatai egy gazdagépen | ((ClusterComputeResource) tároló). Gazdagép
Gazdagép adatai virtuális gépenként | ((HostSystem) tároló). VM

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
Virtuális merevlemez típusa | Msvm_VirtualHardDiskSettingData | Type (Típus)
Virtuális merevlemez mérete | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Virtuális merevlemez szülőjének | Msvm_VirtualHardDiskSettingData | ParentPath
**Hálózati adapter adatai** | 
IP-címek (szintetikus hálózati adapterek) | Msvm_GuestNetworkAdapterConfiguration | Erőforrásrekordjaiba
DHCP engedélyezve (szintetikus hálózati adapterek) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-azonosító (szintetikus hálózati adapterek) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Hálózati adapter MAC-címe (szintetikus hálózati adapterek) | Msvm_SyntheticEthernetPortSettingData | Cím
NIC-azonosító (örökölt hálózati adapterek) | MsvmEmulatedEthernetPortSetting-adathalmazok | InstanceID
NIC MAC-azonosító (örökölt hálózati adapterek) | MsvmEmulatedEthernetPortSetting-adathalmazok | Cím




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

