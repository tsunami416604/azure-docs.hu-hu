---
title: Tudnivalók az Azure Migrate a gyűjtőberendezés |} A Microsoft Docs
description: A gyűjtőberendezés az Azure Migrate ismerteti.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 3d9106fb682a370e18fc78d35ca31662bae44524
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310641"
---
# <a name="about-the-collector-appliance"></a>A gyűjtőberendezés kapcsolatban

 Ez a cikk ismerteti az Azure Migrate Collector.

Az Azure Migrate Collector egy egyszerűsített készülék vCenter a helyszíni környezet felderítéséhez kiértékelés céljából használható a [Azure Migrate](migrate-overview.md) szolgáltatástól, az Azure-ba való migrálás előtt.  

## <a name="discovery-method"></a>A felderítési módszer

Korábban voltak a gyűjtőberendezés, a felderítés egyszeri felderítés és a folyamatos felderítési két lehetőséget. A felderítés egyszeri felderítés modell, a vCenter Server statisztikai beállításait (szükséges statisztikai beállításait a 3. szintre kell beállítani) teljesítményadat-gyűjtés támaszkodni, most már elavult, és szabványosította (nem csúcsidőt) átlagos számlálókat is gyűjti Korrigáljuk méretezése. A folyamatos felderítési modell biztosítja a részletes adatok gyűjtése, és pontos méretezése miatt csúcs gyűjtésének eredményez. Az alábbi, a működési elv:

A gyűjtőberendezés folyamatosan csatlakozik az Azure Migrate-projektben, és folyamatosan gyűjti a teljesítményadatokat a virtuális gépek.

- A gyűjtő folyamatosan profilokat a helyszíni környezetben, valós idejű használati adatok gyűjtéséhez 20 másodpercenként.
- A berendezés összesíti a 20 másodperces mintákat, és létrehoz egy adatpont 15 percenként.
- Az adatok létrehozásához a készülék pont csúcsértéke kiválasztja a 20 másodperces mintákat, és elküldi azt az Azure-bA.
- Ez a modell nem függ a vCenter Server statisztikai beállításait teljesítményadatok gyűjtéséhez.
- Folyamatos profilkészítés címen bármikor a gyűjtő is leállíthatja.

**Gyors értékelést:** A folyamatos felderítési berendezéssel, a felderítés befejeződése után (tart néhány óra múlva, virtuális gépek számától függően), itt azonnal létrehozhat értékeléseket. A teljesítményadat-gyűjtés akkor kezdődik, amikor Ön indíthat felderítési, ha a gyors értékelések, mivel az értékelésben, mint a méretezési feltétel teljesítményalapú kiválasztani *helyszíni*. Teljesítmény-alapú értékelések javasolt a legalább egy napot várni megbízható méretezési javaslatokat kaphat a felderítés megkezdése után.

A berendezés csak az folyamatosan teljesítményadatokat gyűjt, semmilyen konfigurálási változást nem észleli a helyszíni környezetben (pl. virtuális gép hozzáadása, törlése, lemez hozzáadása stb.). Ha a helyszíni környezet konfigurációja módosul, a következőket teheti a változások tükrözésére a portálon:

- További elemek (virtuális gépek, lemezek, magok stb.): A változásoknak az Azure Portalon, a felderítés a készülék leállítása és a majd indítsa el újra. Ez biztosítja, hogy a módosítások frissítése megtörténjen az Azure Migrate-projektben.

- Virtuális gépek törlése: Lehet a célja, a készülék virtuális gépek törlése nem tükrözi, akkor is, ha leállítja és elindítja a felderítést. Ennek az oka, hogy a későbbi felderítések adatait a rendszer hozzáfűzi a korábbi felderítések adataihoz, nem pedig felülírja azokat. Ebben az esetben egyszerűen figyelmen kívül hagyhatja a virtuális gépet a portálon. Ehhez távolítsa el a csoportból, és számítsa újra az értékelést.

> [!NOTE]
> A felderítés egyszeri felderítés berendezés elavulttá vált, ez a módszer támaszkodtak a vCenter Server statisztikai beállításait teljesítmény adatok pont rendelkezésre állását és virtuális gépek áttelepítése az Azure-ba való korrigáljuk méretezésének eredményezett átlagos teljesítményszámlálók gyűjtése.

## <a name="deploying-the-collector"></a>A gyűjtő telepítése

A gyűjtőberendezés egy OVF-sablon használatával telepít:

- Az OVF-sablon letöltése az Azure Migrate-projektet az Azure Portalon. A letöltött fájl importálása a vCenter Serverhez, a virtuális gép gyűjtőberendezés beállításához.
- Az OVF, a VMware beállítja 8 magos, 16 GB RAM és 80 GB egy lemezt egy virtuális Gépet. Az operációs rendszer Windows Server 2016 (64 bites).
- Futtassa a gyűjtő, amikor egy előfeltétel-ellenőrzések száma futtatásával győződjön meg arról, hogy a gyűjtő csatlakozhat az Azure Migrate.

- [További](tutorial-assessment-vmware.md#create-the-collector-vm) a gyűjtő létrehozásáról.


## <a name="collector-prerequisites"></a>Gyűjtő Előfeltételek

A gyűjtő át kell adnia néhány előfeltétel-ellenőrzéseket győződjön meg arról, hogy kapcsolódni az Azure Migrate szolgáltatás az interneten keresztül, és a feltöltés felderített adatokat.

- **Ellenőrizze az Azure-felhő**: A gyűjtő tudnia kell, az Azure-felhőben, amelyet át kíván.
    - Válassza ki Azure Government, ha azt tervezi, Azure Government-felhőben való áttelepítéséhez.
    - Válassza a az Azure globális, ha az Azure kereskedelmi felhőben át kíván.
    - A felhőben az itt megadott alapján, a berendezés küld felderített metaadatokat a megfelelő végpontok.
- **Ellenőrizze az internetkapcsolatot**: A gyűjtő kapcsolódhatnak közvetlenül az internethez, vagy egy proxyn keresztül.
    - Az előfeltétel-ellenőrzés ellenőrzi a kapcsolatot a [szükséges és választható URL-címek](#urls-for-connectivity).
    - Ha közvetlenül kapcsolódik az internethez, az adott semmit nem szükséges, eltérő gondoskodik róla, hogy, hogy a gyűjtő elérje a szükséges URL-címek.
    - Ha egy proxyn keresztül csatlakoztatja, vegye figyelembe az alábbi követelményeknek.
- **Időszinkronizálás ellenőrzése**: A gyűjtő kell szinkronizálva az internetes időkiszolgálóval hitelesíti a kérelmeket a szolgáltatás biztosításához.
    - A portal.azure.com URL-cím kell a gyűjtő érhető el, hogy az idő érvényesíthető legyen.
    - Ha a gép nincs szinkronizálva, az idő a gyűjtő virtuális gépen az aktuális idő megfelelően módosítani szeretné. Ehhez a nyissa meg egy rendszergazdai parancssort a virtuális Gépen, futtassa **w32tm /tz** időzóna ellenőrzésére. Futtatás **w32tm/resync** az idő szinkronizálása.
- **Ellenőrizze a gyűjtő szolgáltatás fut**:  Az Azure Migrate Collector szolgáltatás a gyűjtő virtuális gépen kell futnia.
    - Ez a szolgáltatás a számítógép indításakor automatikusan elindul.
    - Ha a szolgáltatás nem fut, indítsa el a Vezérlőpult.
    - A gyűjtő szolgáltatás csatlakozik a vCenter-kiszolgáló, a virtuális gép metaadatainak és a teljesítmény adatokat gyűjti össze és elküldi azokat az Azure Migrate szolgáltatásnak.
- **Ellenőrizze a VMware PowerCLI 6.5 telepítve**: A VMware PowerCLI 6.5-ös PowerShell modulnak telepítve kell lennie a gyűjtő virtuális gépen, hogy a vCenter-kiszolgáló képes kommunikálni.
    - Ha a gyűjtő el tudja érni az URL-címeket, a modul telepítéséhez szükséges, telepítés automatikusan gyűjtő üzembe helyezés során.
    - Ha a gyűjtő nem tudja telepíteni a modul üzembe helyezése során, akkor manuálisan kell telepítenie.
- **Ellenőrizze a vCenter-kiszolgáló kapcsolatot**: A gyűjtő a vCenter-kiszolgálóhoz kapcsolódva kérdezi le a virtuális gépek, a metaadatok és a teljesítményszámlálók képesnek kell lennie. [Előfeltételek ellenőrzése](#connect-to-vcenter-server) kapcsolódáshoz.


### <a name="connect-to-the-internet-via-a-proxy"></a>Csatlakozzon az internethez olyan proxyn keresztül

- Ha a proxykiszolgáló hitelesítést igényel, megadhatja a felhasználónevet és jelszót a gyűjtő beállításakor.
- IP cím vagy teljes Tartománynevét a proxykiszolgálóhoz meg kell *http://IPaddress* vagy *http://FQDN*.
- Csak a HTTP-proxyk használata támogatott. A gyűjtő a HTTPS-alapú proxykiszolgálók nem támogatja.
- Ha a proxykiszolgálóhoz, egy lehallgató proxy importálnia kell a proxy tanúsítvány a gyűjtő virtuális géphez.
    1. A gyűjtő virtuális gép, lépjen a **Start menü** > **számítógép-tanúsítványok kezelése**.
    2. A tanúsítványok eszközben alatt **tanúsítványok – helyi számítógép**, található **megbízható közzétevők** > **tanúsítványok**.

        ![Tanúsítványok eszköz](./media/concepts-intercepting-proxy/certificates-tool.png)

    3. A proxy tanúsítvány másolja a gyűjtő virtuális Gépen. Szüksége lehet szerzi be a hálózati rendszergazdával.
    4. Dupla kattintással nyissa meg a tanúsítványt, és kattintson a **tanúsítvány telepítése**.
    5. A Tanúsítványimportáló varázsló > Store helyét, válassza a **helyi gép**.

    ![Tanúsítványtár helye](./media/concepts-intercepting-proxy/certificate-store-location.png)

    6. Válassza ki **minden tanúsítvány tárolása ebben a tárolóban** > **Tallózás** > **megbízható közzétevők**. Kattintson a **Befejezés** importálhatja a tanúsítványt.

    ![Tanúsítványok tárolójában](./media/concepts-intercepting-proxy/certificate-store.png)

    7. Ellenőrizze, hogy a tanúsítvány importálása várt módon, és ellenőrizze, hogy az internetes kapcsolat az előfeltétel-ellenőrzés működik, a várt.


### <a name="urls-for-connectivity"></a>URL-címeket a hálózati kapcsolatot

A kapcsolat ellenőrzése az URL-listák való csatlakozással érvényességét.

**URL-cím** | **Részletek**  | **Az előfeltétel-ellenőrzés**
--- | --- | ---
*.portal.azure.com | Érvényes globális Azure-bA. Az Azure-szolgáltatás, és időszinkronizálás kapcsolatát ellenőrzi. | Hozzáférés az URL-cím megadása kötelező.<br/><br/> Előfeltételek ellenőrzése sikertelen, ha nincs kapcsolat.
*.portal.azure.us | Csak az Azure Government alkalmazható. Az Azure-szolgáltatás, és időszinkronizálás kapcsolatát ellenőrzi. | Hozzáférés az URL-cím megadása kötelező.<br/><br/> Előfeltételek ellenőrzése sikertelen, ha nincs kapcsolat.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *. powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| Töltse le a vCenter PowerCLI PowerShell modult használja. | URL-címek hozzáférésre szükség.<br/><br/> Előfeltételek ellenőrzése nem sikerül.<br/><br/> A gyűjtő virtuális gép automatikus modul telepítése sikertelen lesz. A modul telepítése manuálisan egy internetkapcsolattal rendelkező gépen, és másolja a modulok a készülék szüksége. [További hibaelhárítási útmutató 4. lépés áttekinthet](https://docs.microsoft.com/azure/migrate/troubleshooting-general#error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception).


### <a name="install-vmware-powercli-module-manually"></a>Telepítse manuálisan a VMware PowerCLI-modul

1. Telepítse a modul használatával [ezeket a lépéseket](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). Ezek a lépések bemutatják, online és offline telepítés.
2. Ha a gyűjtő virtuális gép offline állapotban, és telepítse a modul egy másik gépen internet-hozzáféréssel rendelkező, arról a gépről a VMware.* fájlok másolása a gyűjtő virtuális gép van szükség.
3. A telepítés után indítsa újra az előfeltétel-ellenőrzést annak ellenőrzéséhez, hogy a PowerCLI telepítve van-e.

### <a name="connect-to-vcenter-server"></a>Csatlakozás a vCenter Serverhez

A gyűjtő a vCenter-kiszolgálóhoz csatlakozik, és lekérdezi a virtuális gépek metaadatait és a teljesítményszámlálók. Ez a kapcsolat szükséges.

- Csak vCenter Server 5.5-ös, 6.0-s, 6.5-ös és 6.7 támogatja.
- A felderítés alább összefoglalt engedélyekkel egy csak olvasható fiók szükséges. A felderítés csak a fiók kódjával elérhető adatközpontok érhetők el.
- Alapértelmezés szerint csatlakozik a vCenter Server-kiszolgáló teljes Tartománynevét vagy IP-címmel. Ha a vCenter-kiszolgáló egy másik porton figyel, kapcsolódik hozzá a képernyőn *IPAddress:Port_Number* vagy *FQDN:Port_Number*.
- A gyűjtő rendelkeznie kell egy hálózati üzemel a vCenter-kiszolgálóhoz.

#### <a name="account-permissions"></a>Fiók engedélyei

**Fiók** | **Engedélyek**
--- | ---
Legalább egy írásvédett felhasználói fiók | Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható   


## <a name="collector-communications"></a>Gyűjtő kommunikáció

A gyűjtő kommunikál a következő ábra és táblázat foglalja össze.

![Gyűjtő kommunikációs diagramja](./media/tutorial-assessment-vmware/portdiagram.PNG)


**Gyűjtő kommunikál** | **Port** | **Részletek**
--- | --- | ---
Azure Migrate szolgáltatás | 443-as TCP | Gyűjtő SSL 443-as porton keresztül kommunikál az Azure Migrate szolgáltatással.
vCenter Server | 443-as TCP | A gyűjtő képes kommunikálni a vCenter-kiszolgálóhoz kell lennie.<br/><br/> Alapértelmezés szerint csatlakozik a vCenter a 443-as porton.<br/><br/> Ha a vCenter-kiszolgáló egy másik porton figyel, erre a portra érhető el, a gyűjtő kimenő portként kell lennie.
RDP | TCP 3389 |

## <a name="collected-metadata"></a>Összegyűjtött metaadatok

A gyűjtőberendezés minden virtuális géphez deríti fel a következő konfigurációs metaadatokat. A konfigurációs adatokat a virtuális gépek esetében érhető el egy órán felderítés indítása.

- Virtuális gép megjelenített neve (a vCenter-kiszolgáló)
- Virtuális gép szoftverleltár elérési útvonala (a gazdagép/mappa a vCenter-kiszolgáló)
- IP-cím
- MAC-cím
- Operációs rendszer
- Magok, lemezek, hálózati adapterek száma
- Memória mérete, a lemezméretek
- Teljesítményszámlálók a virtuális gép, a lemez és a hálózat.

### <a name="performance-counters"></a>Teljesítményszámlálók

 A gyűjtőberendezés gyűjti össze a következő teljesítményszámlálókkal minden virtuális géphez az ESXi-gazdagép 20 másodperces időközönként. Ezek a számlálók vCenter teljesítményszámlálók és a terminológia átlagos feliratú, bár a 20 másodperces minták-e a valós idejű számlálókat. A teljesítményadatokat a virtuális gépek indítása váljon a portálon elérhető, rendelkezik kezdődjön a felderítés után két órával. A legalább egy napot vár az értékelések teljesítményalapú pontos megfelelő javaslatokat beolvasni létrehozása előtt erősen ajánlott. Ha a keresett azonnali menni, elkészítheti a méretezési feltétel, *helyszíni* nem veszi, amely megfelelő a teljesítményadatokat.

**A számláló** |  **Értékelés gyakorolt hatás**
--- | ---
cpu.usage.average | Javasolt Virtuálisgép-méretet és költség  
mem.usage.average | Javasolt Virtuálisgép-méretet és költség  
virtualDisk.read.average | Kiszámítja a lemez mérete, a tárolási költségeket, a virtuális gép mérete
virtualDisk.write.average | Kiszámítja a lemez mérete, a tárolási költségeket, a virtuális gép mérete
virtualDisk.numberReadAveraged.average | Kiszámítja a lemez mérete, a tárolási költségeket, a virtuális gép mérete
virtualDisk.numberWriteAveraged.average | Kiszámítja a lemez mérete, a tárolási költségeket, a virtuális gép mérete
NET.Received.average | Kiszámítja a virtuális gép mérete                          
NET.transmitted.average | Kiszámítja a virtuális gép mérete     

Az Azure Migrate által gyűjtött VMware számlálók teljes listáját a alatt érhető el:

**Kategória** |  **Metadata** | **vCenter datapoint**
--- | --- | ---
Gép részletei | VM-azonosító | vm.Config.InstanceUuid
Gép részletei | a virtuális gép neve | vm.Config.Name
Gép részletei | vCenter Server ID | VMwareClient.InstanceUuid
Gép részletei |  Virtuális gép leírása |  vm.Summary.Config.Annotation
Gép részletei | Licenc termék neve | vm.Client.ServiceContent.About.LicenseProductName
Gép részletei | Operációs rendszer típusa | vm.Summary.Config.GuestFullName
Gép részletei | Operációs rendszer verziója | vm.Summary.Config.GuestFullName
Gép részletei | Rendszerindítás típusa | vm.Config.Firmware
Gép részletei | Magok száma | vm.Config.Hardware.NumCPU
Gép részletei | Memória (MB) | vm.Config.Hardware.MemoryMB
Gép részletei | Lemezek száma | a virtuális gép. Config.Hardware.Device.ToList(). FindAll(x => x is VirtualDisk).count
Gép részletei | Lemez mérete lista | a virtuális gép. Config.Hardware.Device.ToList(). FindAll (x = > x VirtualDisk az)
Gép részletei | Hálózati adapterek listáját | vm.Config.Hardware.Device.ToList().FindAll(x => x is VirtualEthernetCard)
Gép részletei | Processzorkihasználtság | cpu.usage.average
Gép részletei | Memóriakihasználtság | mem.usage.average
Lemez adatai (lemezenként) | Lemez kulcs értéke | a lemez. Kulcs
Lemez adatai (lemezenként) | Lemezek száma | disk.UnitNumber
Lemez adatai (lemezenként) | Lemez vezérlő kulcs értéke | disk.ControllerKey.Value
Lemez adatai (lemezenként) | GB kiosztott | virtualDisk.DeviceInfo.Summary
Lemez adatai (lemezenként) | Lemez neve | Ez az érték használatával: lemez jön létre. UnitNumber, a lemez. Kulcs és a lemez. ControllerKey.Value
Lemez adatai (lemezenként) | Olvasási műveletek másodpercenkénti száma | virtualDisk.numberReadAveraged.average
Lemez adatai (lemezenként) | Írási műveletek másodpercenkénti száma | virtualDisk.numberWriteAveraged.average
Lemez adatai (lemezenként) | Az olvasás átviteli sebességét megabájt / másodperc | virtualDisk.read.average
Lemez adatai (lemezenként) | Lemezírás teljesítménye megabájt / másodperc | virtualDisk.write.average
Hálózati Adapter adatai száma (NIC) | Hálózati adapter neve | hálózati adapteren. Kulcs
Hálózati Adapter adatai száma (NIC) | MAC-cím | ((VirtualEthernetCard)nic).MacAddress
Hálózati Adapter adatai száma (NIC) | IPv4-címek | vm.Guest.Net
Hálózati Adapter adatai száma (NIC) | IPv6-címek | vm.Guest.Net
Hálózati Adapter adatai száma (NIC) | Az olvasás átviteli sebességét megabájt / másodperc | NET.Received.average
Hálózati Adapter adatai száma (NIC) | Lemezírás teljesítménye megabájt / másodperc | NET.transmitted.average
Elérési út Leltáradatait | Name (Név) | container.GetType().Name
Elérési út Leltáradatait | Írja be az gyermekobjektum | container.ChildType
Elérési út Leltáradatait | Hivatkozás részletei | a tároló. MoRef
Elérési út Leltáradatait | A készlet teljes elérési útja | a tároló. Nevezze el a teljes elérési útja
Elérési út Leltáradatait | Szülő részletei | Container.Parent
Elérési út Leltáradatait | Mappa adatai az egyes virtuális Gépekhez | ((Folder)container).ChildEntity.Type
Elérési út Leltáradatait | Minden virtuális gép mappa adatközpont részletei | ((Datacenter)container).VmFolder
Elérési út Leltáradatait | Minden gazdagép mappa adatközpont részletei | ((Datacenter)container).HostFolder
Elérési út Leltáradatait | Minden állomás számára a fürt részletes adatai | ((ClusterComputeResource)container).Host)
Elérési út Leltáradatait | Minden virtuális gép gazdagép részletei | ((HostSystem)container).Vm


## <a name="securing-the-collector-appliance"></a>A gyűjtőberendezés biztonságossá tétele

A gyűjtőberendezés biztonságos a következő lépéseket javasoljuk:

- Ne ossza meg, vagy rendszergazdai jelszavak feljegyezte jogosulatlan személyekkel.
- Állítsa le a készülék, amikor nincs használatban.
- Egy biztonságos hálózaton a készülék helyét.
- Áttelepítés befejezése után törölje a berendezés-példány.
- Emellett az áttelepítés után is törölheti a lemezes biztonsági másolat fájljai (vmdk-inak), a lemezeket lehet vCenter hitelesítő adatokat, azokat a gyorsítótárba.

## <a name="os-license-in-the-collector-vm"></a>A gyűjtő virtuális gép operációsrendszer-licencek

A gyűjtő tartalmaz egy Windows Server 2012 R2 kiértékelési licenc, amely 180 napig érvényes. Ha a gyűjtő virtuális Gépnek a próbaverziós időszak lejár, ajánlott egy új OVA letöltéséhez, és hozzon létre egy új készüléket.

## <a name="updating-the-os-of-the-collector-vm"></a>A gyűjtő virtuális gép operációs rendszerének frissítése

Habár a gyűjtőberendezés egy próbalicencre 180 napig, a készülék automatikus – leállítás elkerüléséhez le a készülék az operációs rendszer folyamatosan frissíteni szeretné.

- Ha a gyűjtő 60 napig nem frissül, elindítja a gép automatikus leállítása.
- Ha fut a felderítést, a gép nem kapcsolható ki, akkor is, ha a 60 nap. A számítógép ki lesz kapcsolva a felderítés befejeződése után.
- Ha korábban már használta a gyűjtő több mint 60 napig, javasoljuk, a gép frissítés: minden alkalommal futó Windows Update gondoskodik.

## <a name="upgrading-the-collector-appliance-version"></a>A gyűjtő berendezés verziójának frissítése

Az OVA újra letöltése nélkül frissítheti a gyűjtő a legújabb verzióra.

1. Töltse le a [legújabb frissítési csomagot felsorolt](concepts-collector-upgrade.md)
2. Győződjön meg arról, hogy a letöltött gyorsjavítás biztonságos, nyissa meg a rendszergazdai parancsablakot, és futtassa a következő parancsot a ZIP-fájl kivonatának. A létrehozott kivonatnak egyeznie kell az említett verzió elleni kivonat:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (gyakorlati példa: C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Másolja a zip-fájlt az Azure Migrate gyűjtő virtuális gép (gyűjtőberendezés).
4. Kattintson a jobb gombbal a zip-fájlt, és válassza ki az összes kibontása.
5. Kattintson a jobb gombbal a Setup.ps1, és válassza a Futtatás a PowerShell-lel, és kövesse a képernyőn a frissítés telepítéséhez.

## <a name="discovery-process"></a>Felderítési folyamat

Miután a készülék be van állítva, futtassa a felderítést. Itt látható, hogyan is működik:

- A felderítési hatókör futtatásához. A megadott vCenter-készlet elérési lévő összes virtuális gép fog eredményül adni.
    - Egy hatókör meg egyszerre.
    - A hatókör tartalmazhat 1500 virtuális gépet vagy kisebb.
    - A hatókör lehet egy adatközpontban, mappa vagy ESXi-gazdagépen.
- VCenter-kiszolgálóhoz való csatlakozás után a gyűjtemény migrálási projekt megadása csatlakozik.
- Virtuális gépeket a felderítésüket, és a metaadatok és a teljesítmény adataikat az Azure-bA küldi el. Ezek a műveletek egy feladat részét képezik.
    - A gyűjtőberendezés kap egy adott Gyűjtőazonosító, amely egy adott gép állandó felderítések között.
    - Egy futó feladat kap egy adott munkamenet-azonosítót. Az azonosító gyűjtemény feladatonként módosítja, és a hibaelhárításhoz használható.

## <a name="next-steps"></a>További lépések

[Értékelés beállítása helyszíni VMware virtuális gépekhez](tutorial-assessment-vmware.md)
