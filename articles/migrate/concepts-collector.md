---
title: Tudnivalók az Azure Migrate a gyűjtőberendezés |} A Microsoft Docs
description: A gyűjtőberendezés az Azure Migrate ismerteti.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 3c40fd97540d8529c95c7d18d2c3155dd37717e9
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945416"
---
# <a name="about-the-collector-appliance"></a>A gyűjtőberendezés kapcsolatban

 Ez a cikk ismerteti az Azure Migrate Collector.

Az Azure Migrate Collector egy egyszerűsített készülék vCenter a helyszíni környezet felderítéséhez kiértékelés céljából használható a [Azure Migrate](migrate-overview.md) szolgáltatástól, az Azure-ba való migrálás előtt.  


## <a name="deploying-the-collector"></a>A gyűjtő telepítése

A gyűjtőberendezés egy OVF-sablon használatával telepít:

- Az OVF-sablon letöltése az Azure Migrate-projektet az Azure Portalon. A letöltött fájl importálása a vCenter Serverhez, a virtuális gép gyűjtőberendezés beállításához.
- Az OVF, a VMware 4 mag, 8 GB RAM és a egy lemezt 80 GB-os virtuális gép beállítása. Az operációs rendszer Windows Server 2012 R2 (64 bites).
- Futtassa a gyűjtő, amikor egy előfeltétel-ellenőrzések száma futtatásával győződjön meg arról, hogy a gyűjtő csatlakozhat az Azure Migrate.

- [További](tutorial-assessment-vmware.md#create-the-collector-vm) a gyűjtő létrehozásáról.


## <a name="collector-prerequisites"></a>Gyűjtő Előfeltételek

A gyűjtő át kell adnia néhány előfeltétel-ellenőrzéseket győződjön meg arról, hogy kapcsolódni az Azure Migrate szolgáltatás az interneten keresztül, és a feltöltés felderített adatokat.

- **Ellenőrizze az internetkapcsolatot**: A gyűjtő csatlakozhat közvetlenül az internethez, vagy egy proxyn keresztül.
    - Az előfeltétel-ellenőrzés ellenőrzi a kapcsolatot a [szükséges és választható URL-címek](#connect-to-urls).
    - Ha közvetlenül kapcsolódik az internethez, az adott semmit nem szükséges, eltérő gondoskodik róla, hogy, hogy a gyűjtő elérje a szükséges URL-címek.
    - Ha egy proxyn keresztül csatlakozik, vegye figyelembe a [kapcsolatos követelményeiről alább](#connect-via-a-proxy).
- **Időszinkronizálás ellenőrzése**: A gyűjtő kell szinkronizálva az internetes időkiszolgálóval hitelesíti a kérelmeket a szolgáltatás biztosításához.
    - A portal.azure.com URL-cím kell a gyűjtő érhető el, hogy az idő érvényesíthető legyen.
    - Ha a gép nincs szinkronizálva, az idő a gyűjtő virtuális gépen az aktuális idő megfelelően módosítani szeretné. Ehhez a nyissa meg egy rendszergazdai parancssort a virtuális Gépen, futtassa **w32tm /tz** időzóna ellenőrzésére. Futtatás **w32tm/resync** az idő szinkronizálása.
- **Ellenőrizze a gyűjtő szolgáltatás fut**: az Azure Migrate Collector szolgáltatás a gyűjtő virtuális gépen kell futnia.
    - Ez a szolgáltatás a számítógép indításakor automatikusan elindul.
    - Ha a szolgáltatás nem fut, indítsa el a Vezérlőpult.
    - A gyűjtő szolgáltatás csatlakozik a vCenter-kiszolgáló, a virtuális gép metaadatainak és a teljesítmény adatokat gyűjti össze és elküldi azokat az Azure Migrate szolgáltatásnak.
- **Ellenőrizze a VMware PowerCLI 6.5 telepítve**: A VMware PowerCLI 6.5-ös PowerShell modulnak telepítve kell lennie a gyűjtő virtuális gépen, hogy a vCenter-kiszolgáló képes kommunikálni.
    - Ha a gyűjtő el tudja érni az URL-címeket, a modul telepítéséhez szükséges, telepítés automatikusan gyűjtő üzembe helyezés során.
    - Ha a gyűjtő nem tudja telepíteni a modul üzembe helyezése során, akkor kell [telepítse manuálisan](#install-vwware-powercli-module-manually).
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




### <a name="connect-to-urls"></a>Csatlakozhat az URL-címek

A kapcsolat ellenőrzése az URL-listák való csatlakozással érvényességét.

**URL-cím** | **Részletek**  | **Az előfeltétel-ellenőrzés**
--- | --- | ---
*.portal.azure.com | Az Azure-szolgáltatás, és időszinkronizálás kapcsolatát ellenőrzi. | Hozzáférés az URL-cím megadása kötelező.<br/><br/> Előfeltételek ellenőrzése sikertelen, ha nincs kapcsolat.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *. powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| Töltse le a vCenter PowerCLI PowerShell modult használja. | Nem kötelező URL-címek elérését.<br/><br/> Előfeltételek ellenőrzése nem sikerül.<br/><br/> A gyűjtő virtuális gép automatikus modul telepítése sikertelen lesz. A modul telepítése manuálisan kell.


### <a name="install-vmware-powercli-module-manually"></a>Telepítse manuálisan a VMware PowerCLI-modul

1. Telepítse a modul használatával [ezeket a lépéseket](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). Ezek a lépések bemutatják, online és offline telepítés.
2. Ha a gyűjtő virtuális gép offline állapotban, és telepítse a modul egy másik gépen internet-hozzáféréssel rendelkező, arról a gépről a VMware.* fájlok másolása a gyűjtő virtuális gép van szükség.
3. A telepítés után indítsa újra az előfeltétel-ellenőrzést annak ellenőrzéséhez, hogy a PowerCLI telepítve van-e.

### <a name="connect-to-vcenter-server"></a>Csatlakozás a vCenter Serverhez

A gyűjtő a vCenter-kiszolgálóhoz csatlakozik, és lekérdezi a virtuális gépek metaadatait és a teljesítményszámlálók. Ez a kapcsolat szükséges.

- Csak vCenter Server 5.5-ös, 6.0-s és 6.5-ös támogatja.
- A felderítés alább összefoglalt engedélyekkel egy csak olvasható fiók szükséges. A felderítés csak a fiók kódjával elérhető adatközpontok érhetők el.
- Alapértelmezés szerint csatlakozik a vCenter Server-kiszolgáló teljes Tartománynevét vagy IP-címmel. Ha a vCenter-kiszolgáló egy másik porton figyel, kapcsolódik hozzá a képernyőn *IPAddress:Port_Number* vagy *FQDN:Port_Number*.
- A tárolási és hálózatkezelési teljesítményadatok gyűjtése, vCenter statisztikai beállításait kiszolgáló kell beállítani a három szintre.
- Ha szintje alacsonyabb, mint három, felderítésének működése, de a teljesítményadatok nem gyűjthetők. Néhány számlálót gyűjthető, de más nulla értékre lesz beállítva.
- Nem gyűjti a teljesítményadatokat tárolási és hálózatkezelési, felmérési javaslatok a méretekkel kapcsolatban-e a CPU és memória és a lemez és a hálózati adapterek konfigurációs adatai alapján ügynökteljesítmény-adatokat.
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
RDP | 3389-ES TCP |


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


## <a name="discovery-methods"></a>A felderítési módszerek

Két módszerrel, a gyűjtőberendezés felderítése, a felderítés egyszeri felderítés vagy a folyamatos felderítési használhat.


### <a name="one-time-discovery"></a>Egyszeri felderítés

A gyűjtő a vCenter Server kérdezze le a virtuális gépek metaadatait egyszeri kommunikál. Ezzel a módszerrel:

- A berendezés folyamatosan nincs csatlakoztatva az Azure Migrate-projekt.
- A helyszíni környezetben nem tükröződnek az Azure Migrate felderítési befejeződése után. Bármely változásainak kell újra felderítése ugyanabban a projektben ugyanabban a környezetben.
- Ez a felderítési módszer esetében a statisztikai beállítások megadása a vCenter-kiszolgáló három szintre kell.
- Biztonsági szint három, miután vesz igénybe naponta létrehozni a teljesítményszámlálók. Ezért javasoljuk, hogy a felderítés futtatásakor egy nap.
- Teljesítményadatok összegyűjtése egy virtuális géphez, a berendezést a vCenter Serverben tárolt korábbi teljesítményadatok támaszkodik. Teljesítményelőzmények összegyűjti az elmúlt hónapban.
- Az Azure Migrate minden metrika, ami korrigáljuk méretezési átlagos számlálók (helyett csúcs számláló) gyűjti.

### <a name="continuous-discovery"></a>Folyamatos felderítés

A gyűjtőberendezés folyamatosan csatlakozik az Azure Migrate-projektben, és folyamatosan gyűjti a teljesítményadatokat a virtuális gépek.

- A gyűjtő folyamatosan profilokat a helyszíni környezetben, valós idejű használati adatok gyűjtéséhez 20 másodpercenként.
- Ez a modell nem függ a vCenter Server statisztikai beállításait teljesítményadatok gyűjtéséhez.
- A berendezés összesíti a 20 másodperces mintákat, és létrehoz egy adatpont 15 percenként.
- Az adatok létrehozásához a készülék pont csúcsértéke kiválasztja a 20 másodperces mintákat, és elküldi azt az Azure-bA.
- Folyamatos profilkészítés címen bármikor a gyűjtő is leállíthatja.

Vegye figyelembe, hogy a berendezés csak az folyamatosan teljesítményadatokat gyűjt, semmilyen konfigurálási változást nem észleli a helyszíni környezetben (pl. virtuális gép hozzáadása, törlése, lemez hozzáadása stb.). Ha egy konfigurációmódosítás a helyszíni környezetben, hogy tükrözzék a változásokat a portál a következőket teheti:

1. További elemek (virtuális gépek, lemezek, magok stb.): A változásoknak az Azure Portalon, a felderítés a készülék leállítása és elindítása azt újra. Ez biztosítja, hogy a változtatások az Azure Migrate-projektben.

2. Virtuális gépek törlése: lehet a célja, a készülék, virtuális gépek törlése nem jelenik meg akkor is, ha leállítja és elindítja a felderítést. Ennek az oka, hogy az ezt követő felderítések adatokat hozzáfűzi korábbi felderítések, és nem bírálja felül. Ebben az esetben egyszerűen figyelmen kívül hagyhatja a portálon, a virtuális gép eltávolítása a csoportból, és az értékelés újraszámításakor.

> [!NOTE]
> Folyamatos felderítési funkciója előzetes verzióban érhető el. Ha a vCenter Server statisztikai beállításait a 3. szintre nem, azt javasoljuk, hogy ezt a módszert használja.


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

### <a name="collected-metadata"></a>Összegyűjtött metaadatok

A gyűjtőberendezés a következő statikus metaadat-beli virtuális gépek deríti fel:

- Virtuális gép megjelenített neve (a vCenter-kiszolgáló)
- Virtuális gép szoftverleltár elérési útvonala (a gazdagép/mappa a vCenter-kiszolgáló)
- IP-cím
- MAC-cím
- Operációs rendszer
- Magok, lemezek, hálózati adapterek száma
- Memória mérete, a lemezméretek
- Teljesítményszámlálók a virtuális gép, a lemez és a hálózat.

#### <a name="performance-counters"></a>Teljesítményszámlálók

- **Felderítés egyszeri felderítés**: amikor a rendszer a felderítés egyszeri felderítés számlálókat gyűjti, vegye figyelembe a következőket:

    - Összegyűjti és elküldi a projekt konfigurációs metaadatok akár 15 percet is igénybe vehet.
    - Konfigurációs adatok begyűjtését követően is igénybe vehet egy órát a portálon érhetők el teljesítményadatok.
    - Miután a metaadatok a portálon, megjelenik a virtuális gépek listája, és elkezdheti a csoportok értékelés létrehozása.
- **Folyamatos felderítési**: folyamatos felderítéshez, vegye figyelembe a következőket:
    - A virtuális gép konfigurációs adatok érhető el egy órán felderítés indítása
    - Teljesítményadatok indítja el, 2 óra után rendelkezésre álló váljon.
    - Után elindítja a felderítést, várjon legalább egy napot a berendezéshez, hogy a profil a környezet értékelés létrehozása előtt.

**A számláló** | **Szint** | **Az eszközszintű szint** | **Értékelés gyakorolt hatás**
--- | --- | --- | ---
CPU.Usage.average | 1 | NA | Javasolt Virtuálisgép-méretet és költség  
mem.usage.average | 1 | NA | Javasolt Virtuálisgép-méretet és költség  
virtualDisk.read.average | 2 | 2 | Kiszámítja a lemez mérete, a tárolási költségeket, a virtuális gép mérete
virtualDisk.write.average | 2 | 2  | Kiszámítja a lemez mérete, a tárolási költségeket, a virtuális gép mérete
virtualDisk.numberReadAveraged.average | 1 | 3 |  Kiszámítja a lemez mérete, a tárolási költségeket, a virtuális gép mérete
virtualDisk.numberWriteAveraged.average | 1 | 3 |   Kiszámítja a lemez mérete, a tárolási költségeket, a virtuális gép mérete
NET.Received.average | 2 | 3 |  Kiszámítja a virtuális gép mérete                          |
NET.transmitted.average | 2 | 3 | Kiszámítja a virtuális gép mérete     

## <a name="next-steps"></a>További lépések

[Értékelés beállítása helyszíni VMware virtuális gépekhez](tutorial-assessment-vmware.md)
