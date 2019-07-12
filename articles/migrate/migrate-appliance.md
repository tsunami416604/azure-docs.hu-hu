---
title: Az Azure Migrate készülék architektúrája |} A Microsoft Docs
description: Az Azure Migrate készülék áttekintése
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: c2c9ca3082aa9c2067a63f8d6304e8a229dac14a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811452"
---
# <a name="azure-migrate-appliance"></a>Az Azure Migrate-készülék

Ez a cikk ismerteti az Azure Migrate berendezés. A berendezés üzembe helyezése, Azure Migrate elemzésekhez és Migráláshoz eszközök felderítése és értékelése, valamint alkalmazások, az infrastruktúra és a számítási feladatok migrálása a Microsoft Azure-ba való használatakor. 

[Az Azure Migrate](migrate-services-overview.md) kínál egy központi agyhoz felderítési, felmérési és a helyszíni alkalmazások és a számítási feladatok és a privát és nyilvános felhőbeli VM-eken, az Azure-ba való migrálásának nyomon követéséhez. A hub felmérés és migrálás, valamint a külső független szoftverszállítók (ISV) szállító ajánlatok az Azure Migrate eszközöket biztosít.



## <a name="appliance-overview"></a>Készülék – áttekintés

Az Azure Migrate készülék típusa és a használati az alábbiak szerint.

**Telepített** | **Használt** | **Részletek**
--- | --- |  ---
VMware virtuális gép | A VMware virtuális gépek értékelése az Azure Migrate Assessment eszközzel.<br/><br/> VMware virtuális gép-ügynök nélküli migrálást az Azure Migrate-kiszolgáló áttelepítése eszközzel | OVA-sablon letöltése és importálása a vCenter-kiszolgáló a berendezés virtuális gép létrehozásához.
A Hyper-V virtuális gép | A Hyper-V rendszerű virtuális gépek értékelése az Azure Migrate Assessment eszközzel. | Töltse le a Tömörített VHD, és Hyper-V a berendezés virtuális gép létrehozásához importálja.

## <a name="appliance-access"></a>Készülék-hozzáférés

Miután konfigurálta a készülék, távolról érheti el a virtuális berendezés 3389-es TCP-porton keresztül. Távolról is hozzáférhet a webalkalmazás felügyeleti a berendezés port 44368 URL-címmel: ``` https://<appliance-ip-or-name>:44368 ```.

## <a name="appliance-license"></a>Készülék-licenc
A berendezés tartalmaz egy Windows Server 2016 próbalicencre, amely 180 napig érvényes. Ha a próbaidőszak lejártakor közelében van, ajánlott töltse le, és a egy új berendezések üzembe helyezése vagy aktiválni a készülék virtuális Gépet az operációsrendszer-licenccel.

## <a name="appliance-agents"></a>Készülék-ügynökök
A berendezés ilyen ügynök telepítve van.

**Ügynök** | **Részletek**
--- | ---
Felderítési ügynök | Konfigurációs adatokat gyűjti össze a helyszíni virtuális gépeket.
Értékelés ügynök | Profilt készít a virtuális gép teljesítményadatok gyűjtése a helyszíni környezetben.
Áttelepítési adapter | Hangolja össze a virtuális gép replikációja, és a virtuális gépek és Azure közötti kommunikáció koordinálását.
Áttelepítési átjáró | Küld az Azure-bA replikálja a virtuális gép adatait.


## <a name="appliance-deployment-requirements"></a>Készülék központi telepítésére vonatkozó követelmények

- [Felülvizsgálat](migrate-support-matrix-vmware.md#assessment-appliance-requirements) egy VMware-készülék, és az URL-címeket, amelyek a készülék való hozzáférésre van szüksége a központi telepítésére vonatkozó követelmények.
- [Felülvizsgálat](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) egy Hyper-V-készülék, és az URL-címeket, amelyek a készülék való hozzáférésre van szüksége a központi telepítésére vonatkozó követelmények.


## <a name="collected-performance-data-vmware"></a>Gyűjtött teljesítmény-adatok – VMware

Íme a VMware virtuális gép teljesítményadatok, hogy a berendezés gyűjt, és az Azure-bA küldi.

**Adatok** | **A számláló** | **Értékelés gyakorolt hatás**
--- | --- | ---
Processzorkihasználtság | cpu.usage.average | Javasolt a virtuális gép mérete/költség
Memóriakihasználtság | mem.usage.average | Javasolt a virtuális gép mérete/költség
Lemez sebessége olvasott átviteli sebesség (MB / s) | virtualDisk.read.average | A lemez mérete, a tárolási költségek, a Virtuálisgép-méret kiszámítása
Lemezírás teljesítménye (MB / s) | virtualDisk.write.average | A lemez mérete, a tárolási költségek, a Virtuálisgép-méret kiszámítása
Lemezolvasási műveletek másodpercenkénti száma | virtualDisk.numberReadAveraged.average | A lemez mérete, a tárolási költségek, a Virtuálisgép-méret kiszámítása
Az írási műveletek száma másodpercenként | virtualDisk.numberWriteAveraged.average  | A lemez mérete, a tárolási költségek, a Virtuálisgép-méret kiszámítása
Hálózati adapter, olvassa el az átviteli sebesség (MB / s) | NET.Received.average | Virtuálisgép-méret kiszámítása
Hálózati adapter írási átviteli sebesség (MB / s) | NET.transmitted.average  |Virtuálisgép-méret kiszámítása


## <a name="collected-metadata-vmware"></a>Összegyűjtött metaadatok – VMware

Itt látható a teljes listája megtalálható a VMware virtuális gépek metaadatait, hogy a berendezés gyűjt, és az Azure-bA küldi.

**Adatok** | **A számláló**
--- | --- 
**Gép részletei** | 
VIRTUÁLIS GÉP AZONOSÍTÓJA | vm.Config.InstanceUuid 
a virtuális gép neve | vm.Config.Name
vCenter Server ID | VMwareClient.Instance.Uuid
Virtuális gép leírása | vm.Summary.Config.Annotation
Licenc termék neve | vm.Client.ServiceContent.About.LicenseProductName
Operációs rendszer típusa | vm.SummaryConfig.GuestFullName
Rendszerindítás típusa | vm.Config.Firmware
Magok száma | vm.Config.Hardware.NumCPU
Memória (MB) | vm.Config.Hardware.MemoryMB
Lemezek száma | a virtuális gép. Config.Hardware.Device.ToList(). FindAll(x => is VirtualDisk).count
Lemez mérete lista | a virtuális gép. Config.Hardware.Device.ToList(). FindAll (x = > VirtualDisk van)
Hálózati adapterek listáját | a virtuális gép. Config.Hardware.Device.ToList(). FindAll(x => is VirtualEthernet).count
Processzorkihasználtság | cpu.usage.average
Memóriakihasználtság |mem.usage.average
**Lemez adatai kiszolgálónként** | 
Lemez kulcs értéke | a lemez. Kulcs
Dikunit száma | disk.UnitNumber
Lemez vezérlő kulcs értéke | disk.ControllerKey.Value
GB kiosztott | virtualDisk.DeviceInfo.Summary
Lemez neve | Lemez használatával létrehozott értéket. UnitNumber, a lemez. Kulcs, a lemez. ControllerKey.VAlue
Olvasási műveletek száma másodpercenként | virtualDisk.numberReadAveraged.average
Írási műveletek száma másodpercenként | virtualDisk.numberWriteAveraged.average
Olvassa el az átviteli sebesség (MB / s) | virtualDisk.read.average
Írás az átviteli sebesség (MB / s) | virtualDisk.write.average
**Egy hálózati adapter részletei** | 
Hálózati adapter neve | hálózati adapteren. Kulcs
MAC-cím | ((VirtualEthernetCard)nic).MacAddress
IPv4 addresses | vm.Guest.Net
IPv6-címek | vm.Guest.Net
Olvassa el az átviteli sebesség (MB / s) | NET.Received.average
Írás az átviteli sebesség (MB / s) | NET.transmitted.average
**Elérési út leltáradatait** | 
Name (Név) | container.GetType().Name
Írja be az gyermekobjektum | container.ChildType
Hivatkozás részletei | a tároló. MoRef
Szülő részletei | Container.Parent
Mappa részletei virtuális gépenként | ((Folder)container).ChildEntity.Type
Datacenter részletei virtuális gépenként | ((Datacenter)container).VmFolder
Gazdagép mappa adatközpont részletei | ((Datacenter)container).HostFolder
Fürt részletes adatai gazdagépenként | ((ClusterComputeResource)container).Host
Gazdagép részletei virtuális gépenként | ((HostSystem)container).VM



## <a name="collected-performance-data-hyper-v"></a>Teljesítmény összegyűjtött adatok Hyper-V

Íme a VMware virtuális gép teljesítményadatok, hogy a berendezés gyűjt, és az Azure-bA küldi.

**Teljesítményszámláló osztály** | **A számláló** | **Értékelés gyakorolt hatás**
--- | --- | ---
A Hyper-V Hipervizort virtuális processzor | A(z) % Vendég futási ideje | Javasolt a virtuális gép mérete/költség
A Hyper-V dinamikus memória VM | Jelenlegi nyomás (%)<br/> Vendég látható fizikai memória (MB) | Javasolt a virtuális gép mérete/költség
A Hyper-V virtuális tárolóeszköz | Read Bytes/Second | A lemez mérete, a tárolási költségek, a Virtuálisgép-méret kiszámítása
A Hyper-V virtuális tárolóeszköz | Write Bytes/Second | A lemez mérete, a tárolási költségek, a Virtuálisgép-méret kiszámítása
A Hyper-V virtuális hálózati Adapter | Bájt/mp | Virtuálisgép-méret kiszámítása
A Hyper-V virtuális hálózati Adapter | Bájt küldött/másodperc | Virtuálisgép-méret kiszámítása

- CPU-kihasználtság értéke egyezik meg az összes használat, a virtuális Géphez csatolt minden virtuális processzort.
- Memóriahasználat értéke: (jelenlegi nyomás * Vendég látható fizikai memória) / 100.
- Lemezek és a hálózati kihasználtság értékek a listán szereplő Hyper-V teljesítményszámlálókat gyűjtenek.

## <a name="collected-metadata-hyper-v"></a>Összegyűjtött metaadatok Hyper-V

Itt látható, hogy a berendezés gyűjti és elküldi az Azure-bA Hyper-V virtuális gép metaadatainak teljes listáját.

**Adatok** | **WMI-osztály** | **WMI-osztály tulajdonság**
--- | --- | ---
**Gép részletei** | 
BIOS-_ Msvm_BIOSElement sorozatszáma | BIOSSerialNumber
Virtuális gép típusa (1. vagy 2. generációs) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Virtuális gép megjelenített neve | Msvm_VirtualSystemSettingData | ElementName
Virtuális gép verziója | Msvm_ProcessorSettingData | VirtualQuantity
Memória (bájt) | Msvm_MemorySettingData | VirtualQuantity
Virtuális gép által felhasználható maximális memória | Msvm_MemorySettingData | Korlát
Dinamikus memória engedélyezve | Msvm_MemorySettingData | DynamicMemoryEnabled
Operációs rendszer neve/verziója/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsciExchangeItems Name Data
Virtuális gép energiaállapota | Msvm_ComputerSystem | EnabledState
**Lemez adatai kiszolgálónként** | 
Lemez azonosítója | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Virtuális merevlemez típusa | Msvm_VirtualHardDiskSettingData | Type
Virtuális merevlemez mérete | Msvm_VirtualHardDiskSettingData | MaxInternalSize
A szülő virtuális merevlemezt | Msvm_VirtualHardDiskSettingData | ParentPath
**Egy hálózati adapter részletei** | 
IP addresses (synthetic NICs) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP-kompatibilis (szintetikus hálózati adapterek) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
Hálózati adapter azonosítója (szintetikus hálózati adapterek) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Hálózati adapter MAC-cím (szintetikus hálózati adapterek) | Msvm_SyntheticEthernetPortSettingData | Cím
Hálózati adapter azonosítója (régi NIC-k) | MsvmEmulatedEthernetPortSetting Data | InstanceID
Hálózati adapter MAC-azonosító (örökölt hálózati adapter) | MsvmEmulatedEthernetPortSetting Data | Cím




## <a name="discovery-and-collection-process"></a>Felderítés és az adatgyűjtési folyamat

A berendezés kommunikál a vCenter-kiszolgálók és a Hyper-V gazdagép vagy fürt a következő eljárással.


1. **Felderítés indítása**:
    - Amikor a felderítés a Hyper-V berendezésen, a Hyper-V-gazdagépek a Rendszerfelügyeleti webszolgáltatások portokon 5985-ös (HTTP) és az 5986-os (HTTPS) kommunikál.
    - Amikor felderítési a VMware-készüléket, a vCenter-kiszolgáló alapértelmezés szerint a 443-as TCP-porton kommunikál. Ha a vCenter-kiszolgáló egy másik porton figyel, konfigurálhatja a berendezés-webalkalmazásban.
2. **Metaadat-és teljesítményadatok összegyűjtése**:
    - A berendezés Common Information Model (CIM) munkamenet helyekről történő adatgyűjtéshez Hyper-V virtuális gép a Hyper-V-gazdagép az 5985-ös és az 5986-os portot használja.
    - A berendezés VMware virtuális gép adatainak gyűjtésére a vCenter-kiszolgáló alapértelmezés szerint a 443-as porton kommunikál.
3. **Adatok küldése**: A berendezés küld az összegyűjtött adatokat az Azure Migrate Server Assessment és az Azure Migrate-kiszolgáló áttelepítése 443-as SSL-porton keresztül.
    - A berendezés teljesítményadatokat, valós idejű használati adatokat gyűjti.
        - VMware-ről 20 másodpercenként, és ez lehet 30 másodperc, a Hyper-V, az egyes teljesítmény-mérőszám teljesítményadatok összegyűjtése.
        - A gyűjtött adatokat hozhat létre egy adatpont tíz percre lesz állítva.
        - A kihasználtság Csúcsérték ki van jelölve mind a 20/30 másodperc adatpontok és értékelés kiszámítása az Azure-bA küldött.
        - A PERCENTILIS értékének az értékelésben (az 50/90/95/99th) megadott alapján, a 10 perc pontok emelkedő sorrendben rendezi a rendszer, és a megfelelő PERCENTILIS értékének az értékelés számítási használja
    - A kiszolgáló áttelepítéséhez a készülék elindítja a virtuális gép adatokat gyűjt, és azt az Azure-bA replikálja.
4. **Értékelni és migrálni**: Mostantól létrehozhat értékeléseket az Azure Migrate Server Assessment használatával berendezés által gyűjtött metaadatok alapján. Emellett is elindíthatja a VMware virtuális gépek Azure Migrate-áttelepítés használatával ügynök nélküli kivételfigyelés VM replikációra áttelepítése.


![Architektúra](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Készülék-frissítések

A berendezés verzióra frissíti, az Azure Migrate-ügynökök a készülék futó frissülnek.

- Ez automatikusan történik, mert az automatikus frissítés a készüléken alapértelmezés szerint engedélyezve van.
- Módosíthatja az alapértelmezett beállítás, az ügynökök manuális frissítéséhez.
- Az automatikus frissítések letiltásához állítsa HKLM\SOFTWAREMicrosoft\Azure berendezés az automatikus frissítés, beállításkulcsot.

### <a name="set-agent-updates-to-manual"></a>Az ügynök frissítések manuális beállítása

Manuális frissítések, győződjön meg arról, hogy egyszerre, a készüléken az ügynökök frissítése használatával a **frissítése** gombot a készüléken minden elavult ügynökhöz. Vissza az automatikus frissítések szolgáltatás bármikor válthat a frissítési beállítását.

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan](tutorial-assess-vmware.md#set-up-the-appliance-vm) a berendezés beállítása VMware-ről.
[Ismerje meg, hogyan](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) a berendezés beállítása a Hyper-V.

