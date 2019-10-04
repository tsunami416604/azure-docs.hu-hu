---
title: Azure Migrate berendezés architektúrája | Microsoft Docs
description: Áttekintést nyújt a Azure Migrate készülékről
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: e2faa47a58283623747ae569de22e1c57df1a51f
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231142"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-berendezés

Ez a cikk a Azure Migrate berendezést ismerteti. Ha Azure Migrate felmérési és áttelepítési eszközt használ az alkalmazások, az infrastruktúra és a munkaterhelések Microsoft Azure való felderítéséhez, értékeléséhez és áttelepítéséhez, akkor a készüléket üzembe helyezi. 

[Azure Migrate](migrate-services-overview.md) egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint a magán-és nyilvános Felhőbeli virtuális gépek felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz.



## <a name="appliance-overview"></a>Berendezések áttekintése

A Azure Migrate berendezés típusai és használata a következő.

**Központilag telepítve** | **Használt** | **Részletek**
--- | --- |  ---
VMware virtuális gép | VMware virtuális gép értékelése a Azure Migrate Assessment Tool eszközzel.<br/><br/> VMware VM ügynök nélküli áttelepítés a Azure Migrate Server áttelepítési eszközzel | Töltse le a petesejtek sablonját, és importálja vCenter Serverre a készülék virtuális gépe létrehozásához.
Hyper-V virtuális gép | A Hyper-V virtuális gép értékelése a Azure Migrate Assessment Tool eszközzel. | Töltse le a tömörített VHD-t, és importálja a Hyper-V-be a készülék virtuális gépe létrehozásához.

## <a name="appliance-access"></a>Készülék-hozzáférés

Miután konfigurálta a készüléket, a 3389-es TCP-porton keresztül távolról elérheti a készülék virtuális gépe szolgáltatását. A (z) 44368-as porton keresztül távolról is elérheti a készülék webkezelési `https://<appliance-ip-or-name>:44368`alkalmazását a következő URL-címmel:.

## <a name="appliance-license"></a>Készülék licence
A készülékhez tartozik egy Windows Server 2016 próbaverziós licenc, amely 180 napig érvényes. Ha a próbaidőszak le van zárva, javasoljuk, hogy töltsön le és helyezzen üzembe egy új készüléket, vagy aktiválja a készülék virtuális gépe operációs rendszerének licencét.

## <a name="appliance-agents"></a>Berendezések ügynökei
A készülék telepítette ezeket az ügynököket.

**Ügynök** | **Részletek**
--- | ---
Felderítési ügynök | A helyszíni virtuális gépek konfigurációs adatok összegyűjtése
Értékelési ügynök | A helyszíni környezetet a virtuális gép teljesítményadatait gyűjti.
Áttelepítési adapter | Összehangolja a virtuális gépek replikálását, és koordinálja a virtuális gépek és az Azure közötti kommunikációt.
Áttelepítési átjáró | Replikált virtuálisgép-adatokat küld az Azure-nak.


## <a name="appliance-deployment-requirements"></a>Berendezések telepítési követelményei

- [Tekintse át](migrate-support-matrix-vmware.md#assessment-appliance-requirements) a VMware készülék központi telepítési követelményeit, valamint azokat az URL-címeket, amelyekhez a készüléknek hozzá kell férnie.
- [Tekintse át](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) a Hyper-V berendezés központi telepítési követelményeit, valamint azokat az URL-címeket, amelyekhez a készüléknek hozzá kell férnie.


## <a name="collected-performance-data-vmware"></a>Összegyűjtött teljesítményadatok – VMware

Itt látható a VMware virtuális gép teljesítményadatokat, amelyet a készülék az Azure-ba gyűjt és küld.

**Adatok** | **Számláló** | **Értékelés hatása**
--- | --- | ---
Processzorkihasználtság | cpu.usage.average | Ajánlott virtuális gép mérete/díja
Memóriakihasználtság | mem.usage.average | Ajánlott virtuális gép mérete/díja
Lemez olvasási sebessége (MB/s) | virtualDisk.read.average | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Lemez írási sebessége (MB/s) | virtualDisk.write.average | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Lemez olvasási műveletei másodpercenként | virtualDisk.numberReadAveraged.average | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Lemez írási műveletei másodpercenként | virtualDisk.numberWriteAveraged.average  | A lemez méretére, a tárolási díjakra, a virtuális gépek méretére vonatkozó számítás
Hálózati adapter olvasási sebessége (MB/s) | net. Received. Average | A virtuális gép méretének kiszámítása
Hálózati adapter írási sebessége (MB/s) | net. továbbítandó. Average  |A virtuális gép méretének kiszámítása


## <a name="collected-metadata-vmware"></a>Összegyűjtött metaadatok – VMware

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
Processzorkihasználtság | cpu.usage.average
Memóriakihasználtság |mem.usage.average
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



## <a name="collected-performance-data-hyper-v"></a>Összegyűjtött teljesítményadatok – Hyper-V

> [!NOTE]
> A Azure Migrate készülék által felderített metaadatok segítségével az alkalmazások az Azure-ba való áttelepítéskor, az Azure megfelelőségi elemzés, az alkalmazás-függőségi elemzés és a költséghatékonyság megtervezése révén könnyebben méretezhető alkalmazásait. A Microsoft nem használja ezeket az adattípusokat a licencek megfelelőségi ellenőrzéséhez képest.

Itt látható a VMware virtuális gép teljesítményadatokat, amelyet a készülék az Azure-ba gyűjt és küld.

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

## <a name="collected-metadata-hyper-v"></a>Összegyűjtött metaadatok – Hyper-V

Az alábbi lista tartalmazza a Hyper-V VM-metaadatok teljes listáját, amelyeket a készülék az Azure-ba gyűjt és küld.

**Adatok** | **WMI-osztály** | **WMI-osztály tulajdonsága**
--- | --- | ---
**Gép részletei** | 
BIOS _ Msvm_BIOSElement sorozatszáma | BIOSSerialNumber
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
Virtuális merevlemez típusa | Msvm_VirtualHardDiskSettingData | Type
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
3. Adatküldés: A készülék elküldi az összegyűjtött adatokat Azure Migrate Server Assessment és Azure Migrate Server áttelepítésre az 443-as SSL-porton keresztül.
    - A teljesítményadatok esetében a készülék valós idejű kihasználtsági adatokat gyűjt.
        - A teljesítményadatokat a rendszer minden egyes teljesítménymutató esetében 20 másodpercenként gyűjti a VMware-hez, és minden 30 másodpercenként a Hyper-V-hez.
        - Az összegyűjtött adatokat a rendszer összesíti, hogy egy adatpontot tíz percen belül hozzon létre.
        - A csúcsérték kihasználtsági értéke a 20/30 második adatpontból van kiválasztva, és az Azure-ba az értékelés kiszámításakor lesz elküldve.
        - Az értékelés tulajdonságaiban (50/90/95./esetek 99%) megadott százalékos érték alapján a tíz perces pont növekvő sorrendbe kerül, és az értékelés kiszámításához a megfelelő percentilis értéket használja a rendszer.
    - A kiszolgáló áttelepítése esetén a készülék elkezdi a virtuális gépekkel kapcsolatos adatok gyűjtését, és replikálja azt az Azure-ba.
4. **Értékelés és áttelepítés**: Most már létrehozhat értékeléseket a készülék által gyűjtött metaadatokból Azure Migrate kiszolgáló értékelése alapján. Emellett a VMware virtuális gépek áttelepítését is megkezdheti Azure Migrate kiszolgáló áttelepítésével az ügynök nélküli virtuális gép replikálásának előkészítéséhez.


![Architektúra](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Berendezések frissítése

A készülék frissítve lett, mivel a készüléken futó Azure Migrate-ügynökök frissülnek.

- Ez automatikusan megtörténik, mert alapértelmezés szerint engedélyezve van az automatikus frissítés a készüléken.
- Az alapértelmezett beállítás módosításával manuálisan is frissítheti az ügynököket.
- Az automatikus frissítés letiltásához nyissa meg a Beállításszerkesztőt > HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance, és állítsa az "AutoUpdate" beállításkulcsot 0-ra (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Az ügynökök frissítésének beállítása kézire

Manuális frissítés esetén győződjön meg arról, hogy a készüléken lévő összes ügynököt egy időben frissíti, a készüléken lévő összes elavult ügynök **frissítés** gombjával. A frissítési beállítás bármikor visszaváltható az automatikus frissítésekre.

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan](tutorial-assess-vmware.md#set-up-the-appliance-vm) állíthatja be a készüléket a VMware rendszerhez.
[Ismerje meg, hogyan](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) állíthatja be a készüléket a Hyper-V-hez.

