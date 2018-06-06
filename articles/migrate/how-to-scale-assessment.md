---
title: Bővítse a felderítés és az értékelés Azure áttelepítése használatával |} Microsoft Docs
description: Ismerteti, hogyan lehet az Azure áttelepítése szolgáltatás használatával nagy számú helyszíni gépet felmérésére.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/04/2018
ms.author: raynew
ms.openlocfilehash: 89c9cfd4bdc1c483764983c886ba9f96cc75c69e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736830"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Nagy méretű VMware-környezet felderítése és értékelése

Azure áttelepítése egy legfeljebb 1500 gépek projekt száma, akkor ez a cikk ismerteti, hogyan használatával a helyszíni virtuális gépek (VM) nagy számú felmérésére [Azure áttelepítése](migrate-overview.md).   

## <a name="prerequisites"></a>Előfeltételek

- **VMware**: A virtuális gépek, amelyek az áttelepíteni kívánt vCenter Server 5.5, 6.0 vagy 6.5 kell kezelnie. Emellett kell egy ESXi gazdagépen futó verziójával 5.0-s vagy újabb, a gyűjtő VM telepítése.
- **vCenter fiók**: olvasási fiók vCenter-kiszolgáló eléréséhez szükséges. Az Azure Migrate ezt a fiókot használja a helyszíni virtuális gépek felderítéséhez.
- **Engedélyek**: A vCenter Server, hozzon létre egy virtuális Gépet petesejtek formátumú fájl importálásával engedélyekre van szükség.
- **Statisztika beállítások**: A statisztika vcenter Server kell beállítás 3. szint telepítés megkezdése előtt. Ha a szintje alacsonyabb, mint 3, a frissítésfelmérő működni fog, de a tárolási és hálózati teljesítményadatok nem gyűjthetők. A méret javaslatok ebben az esetben alapjául Processzor- és teljesítményadatokat és a lemezek és a hálózati adapterek konfigurációs adatait.

## <a name="plan-your-migration-projects-and-discoveries"></a>Az áttelepítési projektek és felderítések tervezése

Egyetlen Azure áttelepítése adatgyűjtő több vcenter-kiszolgálók felderítése (egymás után) és az is támogatja több áttelepítési projektek észlelés (egymás után). A gyűjtő tűz is működik, és elfelejti modell, ha a felderítés végzett, segítségével az ugyanazon gyűjtő adatokat gyűjteni a különböző vCenter-kiszolgáló, vagy küldje el a különböző áttelepítési projekt.

Tervezze meg a felderítések és a vizsgálatok során a következő korlátozások:

| **Entitás** | **Számítógépek korlátja** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Detektálás  | 1,500             |
| Értékelés | 1,500             |

Ezeket a tervezési szempontokat vegye figyelembe:

- A felderítés az Azure áttelepítése gyűjtő úgy teheti meg, amikor a felderítési hatókör is beállíthatja a vCenter Server mappa, a datacenter, a fürt vagy a gazdagép.
- Ehhez az egynél több felderítési, ellenőrizze a vCenter-kiszolgáló által a virtuális gépek felderíteni kívánt mappák, az adatközpontok, a fürtök vagy a gazdagépekhez, amelyek támogatják a korlátozás 1500 gépek.
- Azt javasoljuk, értékelési célokra, akkor ne gépek egymástól függő szolgáltatásainak ugyanabban a projektben és értékelési belül. A vCenter Server ellenőrizze, hogy a függő gépek ugyanazon a mappa, datacenter vagy fürt értékeléséhez.

A forgatókönyvtől függően fel a felderítések előírt alatt:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>Több vCenter-kiszolgálók legfeljebb 1500 virtuális gépek

Ha több vCenter-kiszolgálók vannak a környezetében, és a virtuális gépek száma kisebb, mint 1500, egyetlen adatgyűjtő és egyetlen áttelepítési projekt segítségével a virtuális gépek felderítése minden vCenter-kiszolgáló között. A gyűjtő egy vCenter-kiszolgáló egyszerre deríti fel, mert futtatni az azonos gyűjtő összes a vCenter-kiszolgálók, egymás után, és a gyűjtő mutasson áttelepítési ugyanabban a projektben. Ha befejeződött a felderítések, is létrehozhat a gépek értékeléseket.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>Több vCenter-kiszolgálók több mint 1500 virtuális gépek

Ha több vCenter-kiszolgálók legfeljebb 1500 virtuális gépek száma vCenter-kiszolgáló, de több mint 1500 virtuális gépek közötti összes vCenter szolgál, létrehozandó több áttelepítési projektet (egy áttelepítési projekt tárolására képes csak 1500 virtuális gépeken). Ez egy vCenter-kiszolgáló áttelepítési-projekt létrehozása és a felderítések felosztás érhet el. Egyetlen adatgyűjtő segítségével minden vCenter-kiszolgáló felderítése (egymás után). Ha azt szeretné, hogy egy időben elindítani a felderítések, is telepítheti a több készülékek és a felderítések párhuzamosan futnak.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Több mint 1500 gépek egyetlen vCenter Serverrel

Ha több mint 1500 virtuális gépek egy vCenter-kiszolgáló van, a felderítés felosztása több áttelepítési projektek szeretné. Felderítések szétválasztásához használja ki a készüléket a hatókör mező, és adja meg a gazdagép, a fürt, a mappa vagy a adatközpont, amelyen szeretné felderíteni. Például, ha két mappa a vCenter Server, amelyek közül az 1000 (mappa1) virtuális gépek és egyéb 800 virtuális gépek (mappa2), egy egyetlen használják, és hajtsa végre a két felderítések. Az első felderítés hatókörének adja meg a Mappa1 majd mutasson az első áttelepítési projekthez, amikor elkészült, az első felderítési is használják az azonos, módosítsa a hatókört mappa2 és az áttelepítést a második áttelepítési projekthez projekt részleteit, és a második felderítési tegye.

### <a name="multi-tenant-environment"></a>Több-bérlős környezet

Ha a bérlők által megosztott környezet, és nem szeretné, hogy a másik bérlői előfizetéshez egy bérlő virtuális gépeinek felderítése, a gyűjtő készülék hatókör mezője használhatja a felderítés hatókörének. A bérlők osztja gazdagépeken, ha csak a virtuális gépek az adott bérlőhöz tartozó csak olvasási jogosultságokkal rendelkező hitelesítő adatok létrehozása, majd ezeket a hitelesítő adatokat használja a gyűjtő készüléknek, és adja meg a hatókörét a gazdagép, a felderítés elvégzéséhez. Azt is megteheti a virtuális gépeket a Mappa1 tenant1 és tenant2 is létrehozhat mappákat a vCenter Server (Tételezzük fel a tenant1 mappa1 és a tenant2 mappa2), a megosztott gazdagép áthelyezi mappa2 és majd hatókörének megfelelően a gyűjtő a felderítések Megadja a megfelelő mappát.

## <a name="discover-on-premises-environment"></a>A helyszíni környezet felderítése

Ha ezzel elkészült a csomagot, el is indíthatja a helyszíni virtuális gépek észlelése:

### <a name="create-a-project"></a>Projekt létrehozása

A követelményeknek megfelelően Azure áttelepítése projekt létrehozása:

1. Válassza ki az Azure-portálon **hozzon létre egy erőforrást**.
2. Keressen az **Azure Migrate** kifejezésre, és válassza ki az **Azure Migrate (előzetes verzió)** elemet a keresési eredmények közül. Ezután kattintson a **Létrehozás** elemre.
3. Adja meg a projekt nevét és az Azure-előfizetés a projekthez.
4. Hozzon létre egy új erőforráscsoportot.
5. Adja meg a helyet, ahol a projekt létrehozásához, majd válassza ki szeretné **létrehozása**. Vegye figyelembe, hogy a virtuális gépeket egy másik célhelyet a továbbra is felmérheti. A projekt helyére a metaadatok összegyűjtött a helyszíni virtuális gépek tárolására szolgál.

### <a name="set-up-the-collector-appliance"></a>A gyűjtő készülék beállítása

Az Azure Migrate létrehoz egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet. A virtuális gép deríti fel a helyszíni VMware virtuális gépek, és a rájuk vonatkozó metaadatok küld az Azure áttelepítése szolgáltatás. A gyűjtő készülék beállításához petesejtek fájl letöltésére, és importálja azt a helyszíni vCenter Server-példányhoz.

#### <a name="download-the-collector-appliance"></a>A gyűjtőberendezés letöltése

Ha több projekt van, akkor töltse le az adatgyűjtő készülék csak egyszer vCenter-kiszolgáló. Töltse le, és állítsa be a készülék, után kell futtatnia minden olyan projekthez, és a projekt egyedi Azonosítóját és kulcsát meg.

1. Azure áttelepítése projekt kijelölni **bevezetés** > **felderítési & felmérési** > **gépek felderítése**.
2. A **gépek észlelése**, jelölje be **letöltése**, a petesejtek fájl letöltéséhez.
3. A **projekt hitelesítő adatok másolása**, másolja le az Azonosítót, és a projekt kulcsát. Ezekre a gyűjtő konfigurálásához lesz szüksége.


#### <a name="verify-the-collector-appliance"></a>A gyűjtőberendezés ellenőrzése

Ellenőrizze, hogy a petesejtek fájlt biztonságos telepítése előtt:

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.

2. Futtassa a következő parancsot az OVA kivonatának létrehozásához:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Győződjön meg arról, hogy a létrehozott kivonatoló megegyezik-e a következő beállításokat.

    Az OVA 1.0.9.8-as verziója esetében

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | b5d9f0caf15ca357ac0563468c2e6251
    SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
    SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

    Az OVA 1.0.9.7-es verziója esetében

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

    Az OVA 1.0.9.5-ös verziója esetében

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  

    Az OVA 1.0.9.2-es verziója esetén

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  

### <a name="create-the-collector-vm"></a>A gyűjtő virtuális gép létrehozása

A letöltött fájlt a vCenter-kiszolgáló importálása:

1. Válassza ki a vSphere ügyfélkonzol **fájl** > **OVF-sablon telepítése**.

    ![Az OVF telepítése](./media/how-to-scale-assessment/vcenter-wizard.png)

2. Az OVF-sablon központi telepítése varázslóban > **forrás**, adja meg a petesejtek fájl helyét.
3. A **Name** (Név) és a **Location** (Hely) mezőben adjon meg egy rövid nevet a gyűjtő virtuális gépnek, valamint az azt futtató leltárobjektumnak.
4. A **Host/Cluster** (Gazdagép/fürt) mezőben adja meg a gazdagépet vagy fürtöt, amelyen a gyűjtő virtuális gép futni fog.
5. A tárolóban adja meg a célhelyet a gyűjtő virtuális gép tárolásához.
6. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
7. A **Network Mapping** (Hálózatleképezés) mezőben adja meg a hálózatot, amelyhez a gyűjtő virtuális gép kapcsolódni fog. A hálózati metaadatok küldésére Azure internetkapcsolat szükséges.
8. Tekintse át és hagyja jóvá a beállításokat, majd válassza ki **Befejezés**.

### <a name="identify-the-id-and-key-for-each-project"></a>Azonosítsa az ID és a kulcsok minden olyan projekthez

Ha több projektet, ügyeljen arra, hogy azonosítsa a azonosítója és a kulcsok minden egyes számára. Be kell a kulcsot, a virtuális gépek felderítése a gyűjtő futtatásakor.

1. Válassza ki a projekt **bevezetés** > **felderítési & felmérési** > **gépek felderítése**.
2. A **projekt hitelesítő adatok másolása**, másolja le az Azonosítót, és a projekt kulcsát.
    ![Projekt hitelesítő adatok másolása](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="set-the-vcenter-statistics-level"></a>A vCenter statisztika szintjének beállítása
A felderítés során gyűjtött teljesítményszámlálók listája látható. A számlálók a rendszer a vCenter Server különböző szinteken elérhető alapértelmezés szerint.

Azt javasoljuk, hogy a számlálók megfelelően vannak összegyűjtött legnagyobb közös szintjének (3) a statisztika szint beállítása. Ha vCenter egy alacsonyabb szinten adja meg, csak néhány számlálók gyűjtik be teljesen, és a többi értéke 0. Az értékelés majd előfordulhat, hogy megjelenítése nem teljes adatokat.

A következő táblázatban a értékelési eredmények, amelyek befolyásolják, ha a számláló nem gyűjtött a program.

| Számláló                                 | Szint | Eszközönkénti szint | Értékelés gyakorolt hatás                    |
| --------------------------------------- | ----- | ---------------- | ------------------------------------ |
| CPU.Usage.average                       | 1     | NA               | Ajánlott Virtuálisgép-méretet és költség         |
| mem.usage.average                       | 1     | NA               | Ajánlott Virtuálisgép-méretet és költség         |
| virtualDisk.read.average                | 2     | 2                | Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet |
| virtualDisk.write.average               | 2     | 2                | Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet |
| virtualDisk.numberReadAveraged.average  | 1     | 3                | Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet |
| virtualDisk.numberWriteAveraged.average | 1     | 3                | Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet |
| NET.Received.average                    | 2     | 3                | Virtuális gép mérete és a hálózati költség             |
| NET.transmitted.average                 | 2     | 3                | Virtuális gép mérete és a hálózati költség             |

> [!WARNING]
> Ha csak magasabb szintű statisztika, tart naponta a teljesítményszámlálók létrehozásához. Igen azt javasoljuk, hogy a felderítés egy nap után.

### <a name="run-the-collector-to-discover-vms"></a>A gyűjtő futtatása a virtuális gépek felderítéséhez

Minden felderítés, végre kell hajtania a gyűjtő felderítéséhez szükséges hatókörében virtuális gépek futnak. Futtassa a másikat a másik után. Egyidejű felderítések nem támogatottak, és mindegyik felderítés rendelkeznie kell egy másik hatókört.

1.  A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gépre, majd kattintson az **Open Console** (Konzol megnyitása) elemre.
2.  Adja meg a berendezés nyelv-, időzóna- és jelszóbeállításait.
3.  Az asztalon, válassza ki a **futtassa a gyűjtő** helyi.
4.  Nyissa meg az Azure áttelepítése gyűjtő **előfeltétel** , majd:

    a. Fogadja el a licencfeltételeket, és olvassa el a külső szolgáltatóval kapcsolatos információkat.

    A gyűjtő ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel.

    b. Ha a virtuális gép fér hozzá az internethez olyan proxyn keresztül, válassza ki a **proxybeállítások**, adja meg a proxykiszolgáló címét és a figyelő portja. Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.

    A gyűjtő ellenőrzi, hogy fut-e a gyűjtőszolgáltatás. A szolgáltatás alapértelmezés szerint telepítve van a gyűjtő virtuális gépen.

    c. Töltse le és telepítse a VMware PowerCLI-t.

5.  A **Specify vCenter Server details** (vCenter Server adatainak megadása) területen tegye a következőket:
    - Adja meg a név (FQDN) vagy a vCenter-kiszolgáló IP-címét.
    - A **felhasználónév** és **jelszó**, adja meg a csak olvasható fiók hitelesítő adatait, amelyet a gyűjtő virtuális gépek felderítése a vCenter Server fog használni.
    - A **Select scope** (Hatókör kiválasztása) mezőben válassza ki a virtuális gépek felderítésének hatókörét. A gyűjtő képes felderíteni csak az adott hatókörben lévő virtuális gépek. A hatókör egy adott mappára, adatközpontra vagy fürtre állítható be. Hogy 1000-nél több virtuális gép nem tartalmaz.

6.  A **megadása áttelepítési projekt**, adja meg az Azonosítót, és a projekt kulcsát. Nem másolja őket, ha a gyűjtő VM nyissa meg az Azure-portálon. A projekt **áttekintése** lapon jelölje be **gépek felderítése** , és másolja az értékeket.  
7.  A **gyűjtemény folyamatjelző**, a felderítési folyamat figyelésére, és győződjön meg arról, hogy a virtuális gépek gyűjtött metaadatai a hatókörben. Az adatgyűjtő mutatja a felderítés hozzávetőleges időtartamát.


#### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítési idő a felderített virtuális gépek számától függ. Általában 100 virtuális gépekhez, felderítési befejezi a gyűjtő követően egy óra körül.

1. Válassza ki az áttelepítési Planner projektben **kezelése** > **gépek**.
2. Ellenőrizze, hogy a felderíteni kívánt virtuális gépek megjelennek-e a portálon.


## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [hozzon létre egy csoportot](how-to-create-a-group.md) értékeléséhez.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
