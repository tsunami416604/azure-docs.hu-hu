---
title: Felderítés és értékelés méretezése az Azure Migrate használatával |} A Microsoft Docs
description: Ismerteti, hogyan értékelheti a helyszíni gépek nagy számú az Azure Migrate szolgáltatás használatával.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: raynew
ms.openlocfilehash: f289e9745eb220e735cc5c58ce8006c9fea7e61e
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086658"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Nagy méretű VMware-környezet felderítése és értékelése

Az Azure Migrate rendelkezik a maximális hossza 1500 gépet projektenként, ez a cikk azt ismerteti, hogyan értékelheti a helyszíni virtuális gépek (VM) nagy számú használatával [Azure Migrate](migrate-overview.md).   

## <a name="prerequisites"></a>Előfeltételek

- **VMware**: A migrálni kívánt virtuális gépeket egy 5.5-ös, 6.0-s vagy 6.5-ös verziójú vCenter Servernek kell felügyelnie. Emellett szükség egy ESXi gazdagépet verziójú 5.0-s vagy újabb, a gyűjtő virtuális gép üzembe helyezéséhez.
- **vCenter-fióknak**: egy csak olvasható fiókot vCenter-kiszolgáló eléréséhez szükséges. Az Azure Migrate ezt a fiókot használja a helyszíni virtuális gépek felderítéséhez.
- **Engedélyek**: A vCenter Serverben, a virtuális gép létrehozása az OVA formátumú fájl importálásával engedélyek szükségesek.
- **Statisztikai beállítások**: Ez a követelmény nem csak a alkalmazni a [felderítés egyszeri felderítés modell](https://docs.microsoft.com/azure/migrate/concepts-collector#discovery-methods). Felderítés egyszeri felderítés modell a vCenter Server statisztikai beállításait meg kell a 3. szintre történő telepítés megkezdése előtt. A statisztikai szint, hogy a nap, heti és havi adatgyűjtés minden állítható be a 3. Ha bármely három az adatgyűjtés szintje alacsonyabb, mint 3, a kiértékelés működni fog, de a tárolási és hálózati teljesítményadatok nem gyűjthetők. A javaslatok a méretekkel kapcsolatban alapjául a Processzor- és teljesítményadatokat, és a lemez és a hálózati adapterek konfigurációs adatait.

### <a name="set-up-permissions"></a>Engedélyek beállítása

Az Azure Migrate hozzá kell férnie a VMware-kiszolgálókhoz a virtuális gépek felderítéséhez kiértékelés céljából. A VMware-fiókot a következő engedélyekre van szüksége:

- Felhasználó típusa: Legalább egy, csak olvasási jogosultsággal rendelkező felhasználó.
- Engedélyek: Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható.
- Részletek: A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.
- A hozzáférés korlátozásához rendelje a Gyermekobjektumba propagálás objektummal rendelkező Nincs hozzáférés szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).

Ha a bérlő környezetben telepíti, a következő beállítására egyik módja:

1.  Hozzon létre egy felhasználó / bérlő és a használatával [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), csak olvasási engedélyek hozzárendelése a következőhöz tartozó, egy adott bérlő összes virtuális gép. Ezután használja ezeket a hitelesítő adatokat a felderítéshez. RBAC biztosítja, hogy csak a bérlőspecifikus virtuális gépek a megfelelő vCenter-felhasználó hozzáférhet.
2. RBAC beállítását másik bérlőben felhasználók felhasználói 1 és 2. felhasználói esetében az alábbi példában ismertetett módon:

    - A **felhasználónév** és **jelszó**, adja meg a csak olvasható fiók hitelesítő adatait, amelynek használatával a gyűjtő a virtuális gépek felderítése
    - Datacenter1 - felhasználó 1 és 2. felhasználói írásvédett engedélyeket biztosíthat. Ilyen engedéllyel, hogy az összes gyermekobjektum nem propagálása, mert az egyes virtuális gépekhez engedélyek értékre állítjuk.

      - A VM1 (bérlő #1) (csak olvasási engedély a felhasználó 1)
      - VM2 (bérlő #1) (csak olvasási engedély a felhasználó 1)
      - Vm3 virtuális gép (bérlő #2) (csak olvasási engedély felhasználói 2)
      - VM4 (bérlő #2) (csak olvasási engedély felhasználói 2)

   - Ha 1 felhasználói hitelesítő adatok használatával felderítést hajt végre, majd csak a VM1 és a VM2 lesz felderítve.

## <a name="plan-your-migration-projects-and-discoveries"></a>A migrálási projektek és a felderítések megtervezése

Egyetlen Azure Migrate collector támogatja a több vCenter-kiszolgálók felderítése (egymás után), és szintén támogatja a felderítést, hogy több áttelepítési projektet (egymás után).

A gyűjtő esetén a felderítés egyszeri felderítés, tűz működik, és a végrehajtásuk modell, miután megtörtént a felderítést, használhatja ugyanazt a gyűjtő adatokat gyűjteni a különböző vCenter-kiszolgáló, vagy elküldheti azt egy másik migrálási projekt. Folyamatos felderítés esetén egy berendezés van csatlakoztatva, csak, egyetlen projekt így nem használhat ugyanabban a gyűjtő egy második felderítés indításához.

Tervezze meg a felderítések és értékelések az alábbi korlátok alapján:

| **Entitás** | **Gép korlátja** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Detektálás  | 1,500             |
| Értékelés | 1,500             |

Ezeket a tervezési szempontokat tartsa szem előtt:

- Amikor egy felderítési az Azure Migrate collector úgy teheti meg, a felderítési hatókörben egy vCenter-kiszolgáló mappája, adatközpont, fürt vagy gazdagép is beállíthatja.
- Ehhez az egynél több felderítési, ellenőrizze a vCenter Serverben, amely a felderíteni kívánt virtuális gépek szerepelnek a mappákat, adatközpontok, fürtök vagy gazdagépekhez, amelyek támogatják a korlátozás 1500 gépet.
- Azt javasoljuk, hogy értékelési célokra tartani ezeket a köztük fennálló függőségek értékelése és ugyanazon a projekten belül. A vCenter Serverben ellenőrizze, hogy a függő gépek ugyanazon a mappa, datacenter vagy-fürt létrehozása az értékelést.

A forgatókönyvtől függően akkor is feloszthatja a felfedezett összefüggéseket az előírt az alábbi módon:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>Több vCenter-kiszolgálók legfeljebb 1500 virtuális gépekkel
Ha több vCenter-kiszolgálók a környezetében rendelkezik, és kevesebb mint 1500 virtuális gépek teljes száma, a forgatókönyv az alábbi megközelítéseket is használhatja:

**Felderítés egyszeri felderítés:** egyetlen gyűjtő és a egy migrálási projekt használhatja az összes virtuális gép felderítése minden vCenter-kiszolgáló között. Mivel a felderítés egyszeri felderítés gyűjtő egy vCenter-kiszolgáló egyszerre deríti fel, az azonos gyűjtő futtatása ellen a vCenter-kiszolgálók egymás után, és a gyűjtő pontjára migrálás ugyanazon a projekten. Ha az összes felderítések befejeződött, majd létrehozhat értékeléseket a gépek.

**Folyamatos felderítési:** folyamatos felderítés esetén csak egyetlen projekt egy készülék csatlakoztatható. Ezért meg kell üzembe helyezése egy berendezést a vCenter-kiszolgálók, majd ennek megfelelően hozzon létre egy projektet az egyes készülék és a felderítések eseményindító.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>Több vCenter-kiszolgálók legfeljebb 1500 virtuális gépekkel

Ha több vCenter-kiszolgálók a vCenter-kiszolgáló legfeljebb 1500 virtuális gépet, de több mint 1500 virtuális gépet minden vCenter-kiszolgáló között, szeretne létrehozni (egy migrálási projekt csak 1500 virtuális gépet tartalmazhat) több migrálási projektet. Ez a migrálási projekt / vCenter-kiszolgáló létrehozásával, és a felderítések felosztás érheti el.

**Felderítés egyszeri felderítés:** egyetlen gyűjtő használatával minden vCenter-kiszolgáló felderítése (egymás után). Ha azt szeretné, hogy egy időben elindítani a felderítéseket, is több berendezések üzembe helyezése és a felderítések párhuzamosan futnak.

**Folyamatos felderítési:** több gyűjtő berendezések (egy mindegyik vCenter-kiszolgáló) létrehozása és csatlakozás minden készülék egy projektet és az eseményindító felderítése ennek megfelelően kell.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Több mint 1500 gépek egyetlen vCenter-kiszolgáló

Ha egy vCenter-kiszolgáló 1500-nál több virtuális gép is van, a felderítés felosztása több áttelepítési projektet szeretné. Osztott felderítések, használja ki a készüléket a hatókör mezőt, és adja meg a gazdagép, fürt, mappa vagy adatközpontot, amelyben szeretné felderíteni. Például, ha a vCenter-kiszolgáló, egy 1000 két mappa (mappa1) virtuális gépek és a másik a 800 virtuális gép (mappa2), a hatókör mező használható felosztása a felderítéseket, ezek a mappák között.

**Felderítés egyszeri felderítés:** az azonos gyűjtő használatával aktiválásához mindkét a felderítések. Az első felderítés, adja meg a hatókör mappa1 majd mutasson az első migrálási projektet, hogy az első felderítés befejeződése után is az azonos gyűjtő használja, módosítsa a hatókört, a második migrálási projekt mappa2 és a migrálási projekt részletei és Ezt a második felderítés.

**Folyamatos felderítési:** ebben az esetben kell az első gyűjtő hozzon létre két collector berendezést, adja meg a hatókör mappa1, és csatlakoztassa az első migrálási projektet. A párhuzamos mappa2 felderítéséhez a második gyűjtőberendezés segítségével, és csatlakoztassa a második migrálási projektet.

### <a name="multi-tenant-environment"></a>Több-bérlős környezet

Ha a bérlők között megosztott környezet, és nem szeretné, hogy egy bérlő a bérlő egy másik előfizetésben található virtuális gépek felderítése, a hatókör mezője a gyűjtőberendezés segítségével a felderítés hatókörét. Ha a bérlők gazdagépek, csak a virtuális gépek az adott bérlőbe történő tartozó csak olvasási hozzáféréssel rendelkező hitelesítő adatok létrehozása, majd ezeket a hitelesítő adatokat használni a gyűjtőberendezés, és a gazdagépként ehhez a felderítési hatókör megadása.

## <a name="discover-on-premises-environment"></a>A helyszíni környezet felderítése

Ha készen áll a csomagban, el is indíthatja a helyszíni virtuális gépek felderítése:

### <a name="create-a-project"></a>Projekt létrehozása

Az Azure Migrate-projekt létrehozása a követelményeinek megfelelően:

1. Az Azure Portalon válassza az **Erőforrás létrehozása** lehetőséget.
2. Keressen az **Azure Migrate** kifejezésre, és válassza ki az **Azure Migrate** elemet a keresési eredmények közül. Ezután kattintson a **Létrehozás** elemre.
3. Adja meg a projekt nevét és az Azure-előfizetést a projekthez.
4. Hozzon létre egy új erőforráscsoportot.
5. Adja meg a helyét, amelyben a projekt létrehozásához, és válassza ki a kívánt **létrehozás**. Vegye figyelembe, hogy egy másik célhelyet a virtuális gépek továbbra is értékelhet. A projekt számára megadott helyen a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.

### <a name="set-up-the-collector-appliance"></a>A gyűjtőberendezés beállításához

Az Azure Migrate létrehoz egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet. Ez a virtuális gép felderíti a helyszíni VMware virtuális gépeket, és a velük kapcsolatos metaadatok küld az Azure Migrate szolgáltatás. A gyűjtőberendezés beállításához egy OVA-fájl letöltéséhez, és importálni kell a helyszíni vCenter Server-példányt.

#### <a name="download-the-collector-appliance"></a>A gyűjtőberendezés letöltése

Ha több projektet, a vCenter Serverhez csak egyszer a gyűjtőberendezés letöltése szeretne. Után töltse le és állítsa be a készülék, az egyes projektek futtatásához, és azt adja meg a projekt egyedi Azonosítóját és kulcsát.

1. Az Azure Migrate projektben kattintson a **Bevezetés** > **Felderítés és értékelés** > **Gépek felderítése** elemre.
2. A **Gépek felderítése** területen kétféle berendezés közül választhat. Kattintson a **Letöltés** gombra az igényeinek megfelelő berendezés letöltéséhez.

    a. **Egyszeri felderítés:** Az ilyen modellű berendezés a vCenter Serverrel kommunikálva gyűjt metaadatokat a virtuális gépekről. A virtuális gépek teljesítményadatainak gyűjtése esetén a teljesítményadatoknak a vCenter Serveren tárolt előzményeit veszi figyelembe, és az előző hónap teljesítményelőzményeit gyűjti össze. Ebben a modellben az Azure Migrate gyűjt mindegyik metrikát (vagy maximális számláló) átlagos számláló [további](https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected). Mivel a felderítés egyszeri felderítés, a helyszíni környezet változásai nem jelennek meg, a felderítés befejeződése után. Ha szeretné, hogy megjelenjenek ezek a változások, végre kell hajtania egy ismételt felderítést ugyanennek a projektnek ugyanezen környezetén.

    b. **Folyamatos felderítés:** Az ilyen modellű berendezés folyamatosan profilkészítést végez a helyszíni környezeten, így valós idejű használati adatokat gyűjt az egyes virtuális gépekről. Ez a modell a metrikák (processzorhasználat, memóriahasználat stb.) maximumszámlálóit gyűjti össze. Ez a modell a teljesítményadatok gyűjtése során nem függ a vCenter Server statisztikai beállításaitól. A berendezésben bármikor leállítható a folyamatos profilkészítés.

    > [!NOTE]
    > A folyamatos felderítés funkciója jelenleg előzetes verzióban érhető el.

3. A **projekt hitelesítő adatainak másolása**, másolja az Azonosítót, és a projekt kulcsát. Ezekre a gyűjtő konfigurálásához lesz szüksége.


#### <a name="verify-the-collector-appliance"></a>A gyűjtőberendezés ellenőrzése

Ellenőrizze, hogy az OVA-fájl biztonságos-e az üzembe helyezés előtt:

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.

2. Futtassa a következő parancsot az OVA kivonatának létrehozásához:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Győződjön meg arról, hogy a létrehozott kivonatnak megegyezik-e a következő beállításokat.

#### <a name="one-time-discovery"></a>Egyszeri felderítés

Az OVA verziója 1.0.9.15 (10/23/2018 engedélyezett)

**Algoritmus** | **kivonat értéke**
>>>>>>> 20dc93529e7c0a4d17f2f4524752b5e2bead4e37---|} ---MD5-TEL |} e9ef16b0c837638c506b5fc0ef75ebfa SHA1 |} 37b4b1e92b3c6ac2782ff5258450df6686c89864 SHA256 |} 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

Az OVA verziója 1.0.9.14 (8/24/2018 engedélyezett)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

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

#### <a name="continuous-discovery"></a>Folyamatos felderítés

Az OVA 1.0.10.4-es verziója esetén

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be

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

A gyűjtőberendezés deríti fel a kiválasztott virtuális gépek következő statikus metaadatait.

1. Virtuális gép megjelenített neve (a vCenter)
2. Virtuális gép szoftverleltár elérési útvonala (gazdagép/mappa a vCenter)
3. IP-cím
4. MAC-cím
5. Operációs rendszer
5. Magok, lemezek, hálózati adapterek száma
6. Memória mérete, a lemezméretek
7. És a virtuális gép, lemez és az alábbi táblázatban felsorolt hálózati teljesítményszámlálók.

A felderítés egyszeri felderítés az alábbi táblázat a pontos teljesítmény számlálókat legyenek gyűjtve, valamint az értékelési eredmények is érint, ha egy adott számlálóra nem gyűjtötte a program.

A folyamatos felderítése számlálókat gyűjtött valós időben (20-másodperces intervallumban), így nincs függőség a vCenter statisztikai szintje. A berendezés majd tekercsben mentési egy adatpont 15 percenként válassza ki a maximális érték 20 másodperces minták létrehozása 20 másodperces minták és elküldi azokat az Azure-bA.

|Számláló                                  |Szint    |Az eszközszintű szint  |Értékelés gyakorolt hatás                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|CPU.Usage.average                        | 1       |NA                |Javasolt Virtuálisgép-méretet és költség                    |
|mem.usage.average                        | 1       |NA                |Javasolt Virtuálisgép-méretet és költség                    |
|virtualDisk.read.average                 | 2       |2                 |Lemez mérete, a tárolási költségek és a Virtuálisgép-méret         |
|virtualDisk.write.average                | 2       |2                 |Lemez mérete, a tárolási költségek és a Virtuálisgép-méret         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Lemez mérete, a tárolási költségek és a Virtuálisgép-méret         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Lemez mérete, a tárolási költségek és a Virtuálisgép-méret         |
|NET.Received.average                     | 2       |3                 |Virtuális gép mérete és a hálózati költség                        |
|NET.transmitted.average                  | 2       |3                 |Virtuális gép mérete és a hálózati költség                        |

> [!WARNING]
> A felderítés egyszeri felderítés Ha csak statisztikai magasabb szintű, tart naponta létrehozni a teljesítményszámlálókat. Ezért javasoljuk, hogy a felderítés futtatásakor egy nap elteltével. Folyamatos felderítési modell várja meg a profil a környezetben, és hozzon létre értékelések berendezés felderítés indítása után legalább egy napot.

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

Egyszeri felderítés esetén a felderítés ideje a felderíteni kívánt virtuális gépek számától függ. Általában a 100 virtuális gép lefutása a gyűjtő futtatását, miután egy órát vesz igénybe az adatgyűjtés és végrehajtásához. Az felderítés végeztével azonnal létrehozhat értékeléseket (teljesítményalapú és helyszíni értékeléseket egyaránt).

Folyamatos felderítés (előzetes verzió) esetén a gyűjtő folyamatos profilkészítést fog végezni a helyszíni környezeten, és a teljesítményadatokat folyamatosan, egyórás időközönként fogja küldeni. Egy órával a felderítés indítását követően tekintheti át a gépeket a portálon. Javasoljuk, hogy várjon legalább egy napot, és csak utána hozza létre a virtuális gépek teljesítményalapú értékeléseit.

1. A migrálási projektben kattintson a **Kezelés** > **Gépek** elemre.
2. Ellenőrizze, hogy a felderíteni kívánt virtuális gépek megjelennek-e a portálon.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozzon létre egy csoportot](how-to-create-a-group.md) értékeléshez.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
