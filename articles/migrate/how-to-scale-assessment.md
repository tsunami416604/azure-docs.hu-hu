---
title: Felderítés és értékelés méretezése az Azure Migrate használatával |} A Microsoft Docs
description: Ismerteti, hogyan értékelheti a helyszíni gépek nagy számú az Azure Migrate szolgáltatás használatával.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: raynew
ms.openlocfilehash: 4bcb6734c33d70e4045860a2c0f0acfedfa06eff
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215179"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Nagy méretű VMware-környezet felderítése és értékelése

Az Azure Migrate rendelkezik a maximális hossza 1500 gépet projektenként, ez a cikk azt ismerteti, hogyan értékelheti a helyszíni virtuális gépek (VM) nagy számú használatával [Azure Migrate](migrate-overview.md).   

## <a name="prerequisites"></a>Előfeltételek

- **VMware**: A migrálni kívánt virtuális gépeket egy 5.5-ös, 6.0-s vagy 6.5-ös verziójú vCenter Servernek kell felügyelnie. Emellett szükség egy ESXi gazdagépet verziójú 5.0-s vagy újabb, a gyűjtő virtuális gép üzembe helyezéséhez.
- **vCenter-fióknak**: egy csak olvasható fiókot vCenter-kiszolgáló eléréséhez szükséges. Az Azure Migrate ezt a fiókot használja a helyszíni virtuális gépek felderítéséhez.
- **Engedélyek**: A vCenter Serverben, a virtuális gép létrehozása az OVA formátumú fájl importálásával engedélyek szükségesek.
- **Statisztikai beállítások**: A vCenter Server statisztikai beállításait kell állítani a 3. szintre telepítés megkezdése előtt. Ha a szint alacsonyabb, mint 3, a kiértékelés működni fog, de a tárolási és hálózati teljesítményadatok nem gyűjthetők. A javaslatok a méretekkel kapcsolatban ebben az esetben alapjául Processzor- és teljesítményadatokat, és a lemez és a hálózati adapterek konfigurációs adatait.


### <a name="set-up-permissions"></a>Engedélyek beállítása

Az Azure Migrate hozzá kell férnie a VMware-kiszolgálókhoz a virtuális gépek felderítéséhez kiértékelés céljából. A VMware-fiókot a következő engedélyekre van szüksége:

- Felhasználó típusa: Legalább egy, csak olvasási jogosultsággal rendelkező felhasználó.
- Engedélyek: Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható.
- Részletek: A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.
- A hozzáférés korlátozásához rendelje a Gyermekobjektumba propagálás objektummal rendelkező Nincs hozzáférés szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).

Ha a bérlő környezetben telepíti, a következő beállítására egyik módja:

1.  Hozzon létre egy felhasználó / bérlő és a használatával [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), csak olvasási engedélyek hozzárendelése a virtuális gép összes tartozó, egy adott bérlőbe. Ezután használja ezeket a hitelesítő adatokat a felderítéshez. RBAC biztosítja, hogy a megfelelő vCenter-felhasználó hozzáférhet, csak az adott virtuális gép bérlői.
2. RBAC beállítását másik bérlőben felhasználók felhasználói 1 és 2. felhasználói esetében az alábbi példában ismertetett módon:

    - A **felhasználónév** és **jelszó**, adja meg a csak olvasható fiók hitelesítő adatait, amelynek használatával a gyűjtő a virtuális gépek felderítése
    - Datacenter1 - felhasználó 1 és 2. felhasználói írásvédett engedélyeket biztosíthat. Ilyen engedéllyel, hogy az összes gyermekobjektum nem propagálása, mert fog engedélyeket állít be egyéni virtuális Gépet.

      - A VM1 (bérlő #1) (olvasás csak engedély a felhasználó 1)
      - VM2 (bérlő #1) (olvasás csak engedély a felhasználó 1)
      - Vm3 virtuális gép (bérlő #2) (olvasási felhasználói 2 csak engedélyt)
      - VM4 (bérlő #2) (olvasási felhasználói 2 csak engedélyt)

   - Ha 1 felhasználói hitelesítő adatok használatával felderítést hajt végre, majd csak a VM1 és a VM2 lesz felderítve.

## <a name="plan-your-migration-projects-and-discoveries"></a>A migrálási projektek és a felderítések megtervezése

Egyetlen Azure Migrate collector támogatja a több vCenter-kiszolgálók felderítése (egymás után), és szintén támogatja a felderítést, hogy több áttelepítési projektet (egymás után). A gyűjtő egy működik, és a végrehajtásuk modell, miután megtörtént a felderítést, használhatja ugyanazt a gyűjtő adatokat gyűjteni a különböző vCenter-kiszolgáló, vagy elküldheti azt egy másik migrálási projekt.

Tervezze meg a felderítések és értékelések az alábbi korlátok alapján:

| **Entitás** | **Gép korlátja** |
| ---------- | ----------------- |
| Projekt    | 1,500             |
| Detektálás  | 1,500             |
| Értékelés | 1,500             |

Ezeket a tervezési szempontokat tartsa szem előtt:

- Amikor egy felderítési az Azure Migrate collector úgy teheti meg, a felderítési hatókörben egy vCenter-kiszolgáló mappája, adatközpont, fürt vagy gazdagép is beállíthatja.
- Ehhez az egynél több felderítési, ellenőrizze a vCenter Serverben, amely a felderíteni kívánt virtuális gépek szerepelnek a mappákat, adatközpontok, fürtök vagy gazdagépekhez, amelyek támogatják a korlátozás 1500 gépet.
- Azt javasoljuk, hogy értékelési célokra tartani ezeket a köztük fennálló függőségek értékelése és ugyanazon a projekten belül. A vCenter Serverben ellenőrizze, hogy a függő gépek ugyanazon a mappa, datacenter vagy-fürt létrehozása az értékelést.

A forgatókönyvtől függően akkor is feloszthatja a felfedezett összefüggéseket az előírt az alábbi módon:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>Több vCenter-kiszolgálók legfeljebb 1500 virtuális gépekkel

Ha több vCenter-kiszolgálók a környezetében rendelkezik, és kevesebb mint 1500 virtuális gépek teljes száma, egyetlen gyűjtő és a egy migrálási projekt használhatja a virtuális gépek felderítése minden vCenter-kiszolgáló között. A gyűjtő egy vCenter-kiszolgáló egyszerre deríti fel, mert az azonos gyűjtő futtatása ellen a vCenter-kiszolgálók egymás után, és a gyűjtő pontjára migrálás ugyanazon a projekten. Ha az összes felderítések befejeződött, majd létrehozhat értékeléseket a gépek.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>Több vCenter-kiszolgálók legfeljebb 1500 virtuális gépekkel

Ha több vCenter-kiszolgálók a vCenter-kiszolgáló legfeljebb 1500 virtuális gépet, de több mint 1500 virtuális gépet minden vCenter proxykiszolgálójaként között, szeretne létrehozni (egy migrálási projekt csak 1500 virtuális gépet tartalmazhat) több migrálási projektet. Ez a migrálási projekt / vCenter-kiszolgáló létrehozásával, és a felderítések felosztás érheti el. Egyetlen gyűjtő használatával minden vCenter-kiszolgáló felderítése (egymás után). Ha azt szeretné, hogy egy időben elindítani a felderítéseket, is több berendezések üzembe helyezése és a felderítések párhuzamosan futnak.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Több mint 1500 gépek egyetlen vCenter-kiszolgáló

Ha egy vCenter-kiszolgáló 1500-nál több virtuális gép is van, a felderítés felosztása több áttelepítési projektet szeretné. Osztott felderítések, használja ki a készüléket a hatókör mezőt, és adja meg a gazdagép, fürt, mappa vagy adatközpontot, amelyben szeretné felderíteni. Például, ha a vCenter-kiszolgáló, egy 1000 két mappa (mappa1) virtuális gépek és a másik a 800 virtuális gép (mappa2), hajtsa végre a két felderítések és egyetlen gyűjtő használhatja. Az első felderítés, adja meg a hatókör mappa1 majd mutasson az első migrálási projektet, hogy az első felderítés befejeződése után is az azonos gyűjtő használja, módosítsa a hatókört, a második migrálási projekt mappa2 és a migrálási projekt részletei és Ezt a második felderítés.

### <a name="multi-tenant-environment"></a>Több-bérlős környezet

Ha a bérlők között megosztott környezet, és nem szeretné, hogy egy bérlő a bérlő egy másik előfizetésben található virtuális gépek felderítése, a hatókör mezője a gyűjtőberendezés segítségével a felderítés hatókörét. Ha a bérlők gazdagépek, csak a virtuális gépek az adott bérlőbe történő tartozó csak olvasási hozzáféréssel rendelkező hitelesítő adatok létrehozása, majd ezeket a hitelesítő adatokat használni a gyűjtőberendezés, és a gazdagépként ehhez a felderítési hatókör megadása. Azt is megteheti a virtuális gépek tenant1 mappa1, valamint tenant2 mappákat is létrehozhat a vCenter Server (tegyük a tenant1 mappa1 és a tenant2 mappa2), a megosztott gazdagép alatt helyezhetik át mappa2 és majd ennek megfelelően az a felderítéseket, a gyűjtő a hatókör Adja meg a megfelelő mappát.

## <a name="discover-on-premises-environment"></a>A helyszíni környezet felderítése

Ha készen áll a csomagban, el is indíthatja a helyszíni virtuális gépek felderítése:

### <a name="create-a-project"></a>Projekt létrehozása

Az Azure Migrate-projekt létrehozása a követelményeinek megfelelően:

1. Az Azure Portalon válassza ki a **erőforrás létrehozása**.
2. Keressen az **Azure Migrate** kifejezésre, és válassza ki az **Azure Migrate (előzetes verzió)** elemet a keresési eredmények közül. Ezután kattintson a **Létrehozás** elemre.
3. Adja meg a projekt nevét és az Azure-előfizetést a projekthez.
4. Hozzon létre egy új erőforráscsoportot.
5. Adja meg a helyét, amelyben a projekt létrehozásához, és válassza ki a kívánt **létrehozás**. Vegye figyelembe, hogy egy másik célhelyet a virtuális gépek továbbra is értékelhet. A projekt számára megadott helyen a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.

### <a name="set-up-the-collector-appliance"></a>A gyűjtőberendezés beállításához

Az Azure Migrate létrehoz egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet. Ez a virtuális gép felderíti a helyszíni VMware virtuális gépeket, és a velük kapcsolatos metaadatok küld az Azure Migrate szolgáltatás. A gyűjtőberendezés beállításához egy OVA-fájl letöltéséhez, és importálni kell a helyszíni vCenter Server-példányt.

#### <a name="download-the-collector-appliance"></a>A gyűjtőberendezés letöltése

Ha több projektet, a vCenter Serverhez csak egyszer a gyűjtőberendezés letöltése szeretne. Után töltse le és állítsa be a készülék, az egyes projektek futtatásához, és azt adja meg a projekt egyedi Azonosítóját és kulcsát.

1. Válassza ki az Azure Migrate-projektben **bevezetés** > **felderítés és értékelés** > **gépek felderítése**.
2. A **gépek felderítése**válassza **letöltése**, az OVA-fájl letöltéséhez.
3. A **projekt hitelesítő adatainak másolása**, másolja az Azonosítót, és a projekt kulcsát. Ezekre a gyűjtő konfigurálásához lesz szüksége.


#### <a name="verify-the-collector-appliance"></a>A gyűjtőberendezés ellenőrzése

Ellenőrizze, hogy az OVA-fájl biztonságos-e az üzembe helyezés előtt:

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.

2. Futtassa a következő parancsot az OVA kivonatának létrehozásához:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Győződjön meg arról, hogy a létrehozott kivonatnak megegyezik-e a következő beállításokat.

    Az OVA 1.0.9.12-es verziója esetén

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | d0363e5d1b377a8eb08843cf034ac28a
    SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
    SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

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

Importálja a letöltött fájlt a vcenter-kiszolgálóhoz:

1. Válassza ki a vSphere Client-konzolon **fájl** > **OVF-sablon telepítése**.

    ![Az OVF telepítése](./media/how-to-scale-assessment/vcenter-wizard.png)

2. Az OVF-sablon üzembe helyezése varázsló > **forrás**, adja meg az OVA-fájl helyét.
3. A **Name** (Név) és a **Location** (Hely) mezőben adjon meg egy rövid nevet a gyűjtő virtuális gépnek, valamint az azt futtató leltárobjektumnak.
4. A **Host/Cluster** (Gazdagép/fürt) mezőben adja meg a gazdagépet vagy fürtöt, amelyen a gyűjtő virtuális gép futni fog.
5. A tárolóban adja meg a célhelyet a gyűjtő virtuális gép tárolásához.
6. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
7. A **Network Mapping** (Hálózatleképezés) mezőben adja meg a hálózatot, amelyhez a gyűjtő virtuális gép kapcsolódni fog. A hálózati metaadatokat küldhet az Azure-bA internetkapcsolattal kell rendelkeznie.
8. Tekintse át és hagyja jóvá a beállításokat, és válassza ki **Befejezés**.

### <a name="identify-the-id-and-key-for-each-project"></a>Az azonosító azonosításához, és a kulcs az egyes projektek

Ha több projektet, mindenképpen azonosíthatja az azonosítója, és minden egyes kulcs. A kulcs kell futtatásakor a gyűjtő a virtuális gépek felderítéséhez.

1. Válassza ki a projekt **bevezetés** > **felderítés és értékelés** > **gépek felderítése**.
2. A **projekt hitelesítő adatainak másolása**, másolja az Azonosítót, és a projekt kulcsát.
    ![Projekt hitelesítő adatainak másolása](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="set-the-vcenter-statistics-level"></a>Állítsa be a vCenter statisztikai szintje
Következő látható a felderítés során gyűjtött teljesítményszámlálók listája. A számlálók a vCenter-kiszolgáló különböző szinteken érhető el alapértelmezés szerint is.

Azt javasoljuk, hogy a legnagyobb közös szint (3) a statisztikai szint beállítása úgy, hogy az összes számláló gyűjtött megfelelően. VCenter egy alacsonyabb szinten van, ha csak néhány számlálókat a többi állítsa 0-ra teljesen, előfordulhat, hogy gyűjti. Az értékelés majd előfordulhat, hogy megjelenítése nem teljes adatokat.

Az alábbi táblázatban is megjeleníti az értékelések eredményeinek, ha egy adott teljesítményszámláló gyűjtése nem érinti.

| Számláló                                 | Szint | Az eszközszintű szint | Értékelés gyakorolt hatás                    |
| --------------------------------------- | ----- | ---------------- | ------------------------------------ |
| CPU.Usage.average                       | 1.     | n/a               | Javasolt Virtuálisgép-méretet és költség         |
| mem.usage.average                       | 1.     | n/a               | Javasolt Virtuálisgép-méretet és költség         |
| virtualDisk.read.average                | 2.     | 2.                | Lemez mérete, a tárolási költségek és a Virtuálisgép-méret |
| virtualDisk.write.average               | 2.     | 2.                | Lemez mérete, a tárolási költségek és a Virtuálisgép-méret |
| virtualDisk.numberReadAveraged.average  | 1.     | 3.                | Lemez mérete, a tárolási költségek és a Virtuálisgép-méret |
| virtualDisk.numberWriteAveraged.average | 1.     | 3.                | Lemez mérete, a tárolási költségek és a Virtuálisgép-méret |
| NET.Received.average                    | 2.     | 3.                | Virtuális gép mérete és a hálózati költség             |
| NET.transmitted.average                 | 2.     | 3.                | Virtuális gép mérete és a hálózati költség             |

> [!WARNING]
> Csak statisztikai magasabb szintű állított be, ha tart naponta létrehozni a teljesítményszámlálókat. Ezért javasoljuk, hogy a felderítés futtatásakor egy nap elteltével.

### <a name="run-the-collector-to-discover-vms"></a>A gyűjtő futtatása a virtuális gépek felderítéséhez

Kell végrehajtania minden felderítéshez, futtassa a gyűjtő a szükséges hatókörében virtuális gépek felderítéséhez. Futtassa a felderítések egy egymás után. Egyidejű felderítések nem támogatottak, és mindegyik felderítés rendelkeznie kell egy másik hatókört.

1.  A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gépre, majd kattintson az **Open Console** (Konzol megnyitása) elemre.
2.  Adja meg a berendezés nyelv-, időzóna- és jelszóbeállításait.
3.  Az asztalon, válassza ki a **gyűjtő futtatása** parancsikon.
4.  Az Azure Migrate collectorban nyissa meg **Előfeltételek beállítása** , majd:

    a. Fogadja el a licencfeltételeket, és olvassa el a külső szolgáltatóval kapcsolatos információkat.

    A gyűjtő ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel.

    b. Ha a virtuális gép az interneten egy proxyn keresztül fér hozzá, válassza ki a **proxybeállítások**, és adja meg a proxykiszolgáló címét és a figyelő portját. Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.

    A gyűjtő ellenőrzi, hogy fut-e a gyűjtőszolgáltatás. A szolgáltatás alapértelmezés szerint telepítve van a gyűjtő virtuális gépen.

    c. Töltse le és telepítse a VMware PowerCLI-t.

5.  A **Specify vCenter Server details** (vCenter Server adatainak megadása) területen tegye a következőket:
    - Adja meg a nevét (FQDN) vagy a vCenter-kiszolgáló IP-címét.
    - A **felhasználónév** és **jelszó**, adja meg a csak olvasható fiók hitelesítő adatait, amelynek használatával a gyűjtő virtuális gépek felderítése a vCenter Serverben.
    - A **Select scope** (Hatókör kiválasztása) mezőben válassza ki a virtuális gépek felderítésének hatókörét. A gyűjtő csak a megadott hatókörön belüli virtuális gépek képes felderíteni. A hatókör egy adott mappára, adatközpontra vagy fürtre állítható be. Nem tartalmazhat több mint 1000 virtuális gépet.

6.  A **adja meg a migrálási projekt**, adja meg az Azonosítót, és a projekt kulcsát. Ha nem másolja őket, a gyűjtő virtuális Gépről nyissa meg az Azure Portalon. A projekt **áttekintése** lapon jelölje be **gépek felderítése** és az értékek másolásához.  
7.  A **adatgyűjtési folyamat megtekintése**, a felderítési folyamat figyelésére, és ellenőrizze, hogy a virtuális gépekről gyűjtött metaadatok hatókörében van. Az adatgyűjtő mutatja a felderítés hozzávetőleges időtartamát.


#### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítési idő a felderített virtuális gépek számától függ. Általában 100 virtuális gép, a felderítés befejeződik egy órát, miután a gyűjtő a lejáratot követően újrainduljon.

1. Válassza ki az áttelepítési Planner-projektben **kezelés** > **gépek**.
2. Ellenőrizze, hogy a felderíteni kívánt virtuális gépek megjelennek-e a portálon.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozzon létre egy csoportot](how-to-create-a-group.md) értékeléshez.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
