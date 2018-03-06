---
title: "Bővítse a felderítés és az értékelés Azure áttelepítése használatával |} Microsoft Docs"
description: "Ismerteti, hogyan lehet az Azure áttelepítése szolgáltatás használatával nagy számú helyszíni gépet felmérésére."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/08/2018
ms.author: raynew
ms.openlocfilehash: 9d9ebef66be269c63a62d393eda76254946b13e7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Nagy méretű VMware-környezet felderítése és értékelése

Ez a cikk ismerteti, hogyan használatával a helyszíni virtuális gépek (VM) nagy számú felmérésére [Azure áttelepítése](migrate-overview.md). Az Azure áttelepítése értékelésére gépek ellenőrizze, hogy fontosságúak alkalmas áttelepítése az Azure-bA. A szolgáltatás biztosítja a méretezési és költségű becsléseket futó a gépeket az Azure-ban.

## <a name="prerequisites"></a>Előfeltételek

- **VMware**: A virtuális gépek, amelyek az áttelepíteni kívánt vCenter Server 5.5, 6.0 vagy 6.5 kell kezelnie. Emellett kell egy ESXi gazdagépen futó verziójával 5.0-s vagy újabb, a gyűjtő VM telepítése.
- **vCenter fiók**: olvasási fiók vCenter-kiszolgáló eléréséhez szükséges. Az Azure Migrate ezt a fiókot használja a helyszíni virtuális gépek felderítéséhez.
- **Engedélyek**: A vCenter Server, hozzon létre egy virtuális Gépet petesejtek formátumú fájl importálásával engedélyekre van szükség.
- **Statisztika beállítások**: A statisztika vcenter Server kell beállítás 3. szint telepítés megkezdése előtt. Ha a szintje alacsonyabb, mint 3, a frissítésfelmérő működni fog, de a tárolási és hálózati teljesítményadatok nem gyűjthetők. A méret javaslatok ebben az esetben alapjául Processzor- és teljesítményadatokat és a lemezek és a hálózati adapterek konfigurációs adatait.

## <a name="plan-azure-migrate-projects"></a>Tervezze meg a projektek Azure áttelepítése

Tervezze meg a felderítések és a vizsgálatok során a következő korlátozások:

| **Entitás** | **Számítógépek korlátja** |
| ---------- | ----------------- |
| Project    | 1,500              | 
| Detektálás  | 1,500              |
| Értékelés | 1,500               |

<!-- 
- If you have fewer than 400 machines to discover and assess, you need a single project and a single discovery. Depending on your requirements, you can either assess all the machines in a single assessment or split the machines into multiple assessments. 
- If you have 400 to 1,000 machines to discover, you need a single project with a single discovery. But you will need multiple assessments to assess these machines, because a single assessment can hold up to 400 machines.
- If you have 1,001 to 1,500 machines, you need a single project with two discoveries in it.
- If you have more than 1,500 machines, you need to create multiple projects, and perform multiple discoveries, according to your requirements. For example:
    - If you have 3,000 machines, you can set up two projects with two discoveries, or three projects with a single discovery.
    - If you have 5,000 machines, you can set up four projects: three with a discovery of 1,500 machines, and one with a discovery of 500 machines. Alternatively, you can set up five projects with a single discovery in each one. 
-->

## <a name="plan-multiple-discoveries"></a>Több felderítések megtervezése

Az azonos Azure áttelepítése gyűjtő segítségével egy vagy több projektek több felderítések tegye. Ezeket a tervezési szempontokat vegye figyelembe:
 
- A felderítés az Azure áttelepítése gyűjtő úgy teheti meg, amikor a felderítési hatókör is beállíthatja a vCenter Server mappa, a datacenter, a fürt vagy a gazdagép.
- Ehhez az egynél több felderítési, ellenőrizze a vCenter-kiszolgáló által a virtuális gépek felderíteni kívánt mappák, az adatközpontok, a fürtök vagy a gazdagépekhez, amelyek támogatják a korlátozás 1500 gépek.
- Azt javasoljuk, értékelési célokra, akkor ne gépek egymástól függő szolgáltatásainak ugyanabban a projektben és értékelési belül. A vCenter Server ellenőrizze, hogy a függő gépek ugyanazon a mappa, datacenter vagy fürt értékeléséhez.


## <a name="create-a-project"></a>Projekt létrehozása

A követelményeknek megfelelően Azure áttelepítése projekt létrehozása:

1. Válassza ki az Azure-portálon **hozzon létre egy erőforrást**.
2. Keressen az **Azure Migrate** kifejezésre, és válassza ki az **Azure Migrate (előzetes verzió)** elemet a keresési eredmények közül. Ezután kattintson a **Létrehozás** elemre.
3. Adja meg a projekt nevét és az Azure-előfizetés a projekthez.
4. Hozzon létre egy új erőforráscsoportot.
5. Adja meg a helyet, ahol a projekt létrehozásához, majd válassza ki szeretné **létrehozása**. Vegye figyelembe, hogy a virtuális gépeket egy másik célhelyet a továbbra is felmérheti. A projekt helyére a metaadatok összegyűjtött a helyszíni virtuális gépek tárolására szolgál.

## <a name="set-up-the-collector-appliance"></a>A gyűjtő készülék beállítása

Az Azure Migrate létrehoz egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet. A virtuális gép deríti fel a helyszíni VMware virtuális gépek, és a rájuk vonatkozó metaadatok küld az Azure áttelepítése szolgáltatás. A gyűjtő készülék beállításához petesejtek fájl letöltésére, és importálja azt a helyszíni vCenter Server-példányhoz.

### <a name="download-the-collector-appliance"></a>A gyűjtőberendezés letöltése

Ha több projekt van, akkor töltse le az adatgyűjtő készülék csak egyszer vCenter-kiszolgáló. Töltse le, és állítsa be a készülék, után kell futtatnia minden olyan projekthez, és a projekt egyedi Azonosítóját és kulcsát meg.

1. Azure áttelepítése projekt kijelölni **bevezetés** > **felderítési & felmérési** > **gépek felderítése**.
2. A **gépek észlelése**, jelölje be **letöltése**, a petesejtek fájl letöltéséhez.
3. A **projekt hitelesítő adatok másolása**, másolja le az Azonosítót, és a projekt kulcsát. Ezekre a gyűjtő konfigurálásához lesz szüksége.

   
### <a name="verify-the-collector-appliance"></a>A gyűjtőberendezés ellenőrzése

Ellenőrizze, hogy a petesejtek fájlt biztonságos telepítése előtt:

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot az OVA kivonatának létrehozásához:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Győződjön meg arról, hogy a létrehozott kivonatoló megegyezik-e a következő beállításokat.

    1.0.9.5 petesejtek verziójához

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  

    1.0.9.2 petesejtek verziójához

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  

    Az OVA 1.0.8.59-es verziója esetén

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | 71139e24a532ca67669260b3062c3dad
    SHA1 | 1bdf0666b3c9c9a97a07255743d7c4a2f06d665e
    SHA256 | 6b886d23b24c543f8fc92ff8426cd782a77efb37750afac397591bda1eab8656  
 
    Az OVA 1.0.8.49-es verziója esetében

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | cefd96394198b92870d650c975dbf3b8
    SHA1 | 4367a1801cf79104b8cd801e4d17b70596481d6f
    SHA256 | fda59f076f1d7bd3ebf53c53d1691cc140c7ed54261d0dc4ed0b14d7efef0ed9

    Az OVA 1.0.8.40-es verziója esetében:

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 |afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>A gyűjtő virtuális gép létrehozása

A letöltött fájlt a vCenter-kiszolgáló importálása:

1. Válassza ki a vSphere ügyfélkonzol **fájl** > **OVF-sablon telepítése**.

    ![Az OVF telepítése](./media/how-to-scale-assessment/vcenter-wizard.png)

2. Az OVF-sablon központi telepítése varázslóban > **forrás**, adja meg a petesejtek fájl helyét.
3. A **Name** (Név) és a **Location** (Hely) mezőben adjon meg egy rövid nevet a gyűjtő virtuális gépnek, valamint az azt futtató leltárobjektumnak.
5. A **Host/Cluster** (Gazdagép/fürt) mezőben adja meg a gazdagépet vagy fürtöt, amelyen a gyűjtő virtuális gép futni fog.
7. A tárolóban adja meg a célhelyet a gyűjtő virtuális gép tárolásához.
8. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
9. A **Network Mapping** (Hálózatleképezés) mezőben adja meg a hálózatot, amelyhez a gyűjtő virtuális gép kapcsolódni fog. A hálózati metaadatok küldésére Azure internetkapcsolat szükséges. 
10. Tekintse át és hagyja jóvá a beállításokat, majd válassza ki **Befejezés**.

## <a name="identify-the-id-and-key-for-each-project"></a>Azonosítsa az ID és a kulcsok minden olyan projekthez

Ha több projektet, ügyeljen arra, hogy azonosítsa a azonosítója és a kulcsok minden egyes számára. Be kell a kulcsot, a virtuális gépek felderítése a gyűjtő futtatásakor.

1. Válassza ki a projekt **bevezetés** > **felderítési & felmérési** > **gépek felderítése**.
2. A **projekt hitelesítő adatok másolása**, másolja le az Azonosítót, és a projekt kulcsát. 
    ![Projekt hitelesítő adatok másolása](./media/how-to-scale-assessment/copy-project-credentials.png)

## <a name="set-the-vcenter-statistics-level"></a>A vCenter statisztika szintjének beállítása
A felderítés során gyűjtött teljesítményszámlálók listája látható. A számlálók a rendszer a vCenter Server különböző szinteken elérhető alapértelmezés szerint. 

Azt javasoljuk, hogy a számlálók megfelelően vannak összegyűjtött legnagyobb közös szintjének (3) a statisztika szint beállítása. Ha vCenter egy alacsonyabb szinten adja meg, csak néhány számlálók gyűjtik be teljesen, és a többi értéke 0. Az értékelés majd előfordulhat, hogy megjelenítése nem teljes adatokat. 

A következő táblázatban a értékelési eredmények, amelyek befolyásolják, ha a számláló nem gyűjtött a program.

|A számláló                                  |Szint    |Eszközönkénti szint  |Értékelés gyakorolt hatás                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|CPU.Usage.average                        | 1       |NA                |Ajánlott Virtuálisgép-méretet és költség                    |
|mem.usage.average                        | 1       |NA                |Ajánlott Virtuálisgép-méretet és költség                    |
|virtualDisk.read.average                 | 2       |2                 |Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet         |
|virtualDisk.write.average                | 2       |2                 |Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet         |
|NET.Received.average                     | 2       |3                 |Virtuális gép mérete és a hálózati költség                        |
|NET.transmitted.average                  | 2       |3                 |Virtuális gép mérete és a hálózati költség                        |

> [!WARNING]
> Ha csak magasabb szintű statisztika, tart naponta a teljesítményszámlálók létrehozásához. Igen azt javasoljuk, hogy a felderítés egy nap után.

## <a name="run-the-collector-to-discover-vms"></a>A gyűjtő futtatása a virtuális gépek felderítéséhez

Minden felderítés, végre kell hajtania a gyűjtő felderítéséhez szükséges hatókörében virtuális gépek futnak. Futtassa a másikat a másik után. Egyidejű felderítések nem támogatottak, és mindegyik felderítés rendelkeznie kell egy másik hatókört.

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gépre, majd kattintson az **Open Console** (Konzol megnyitása) elemre.
2. Adja meg a berendezés nyelv-, időzóna- és jelszóbeállításait.
3. Az asztalon, válassza ki a **futtassa a gyűjtő** helyi.
4. Nyissa meg az Azure áttelepítése gyűjtő **előfeltétel** , majd:

   a. Fogadja el a licencfeltételeket, és olvassa el a külső szolgáltatóval kapcsolatos információkat.

   A gyűjtő ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel.
   
   b. Ha a virtuális gép fér hozzá az internethez olyan proxyn keresztül, válassza ki a **proxybeállítások**, adja meg a proxykiszolgáló címét és a figyelő portja. Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.

   A gyűjtő ellenőrzi, hogy fut-e a gyűjtőszolgáltatás. A szolgáltatás alapértelmezés szerint telepítve van a gyűjtő virtuális gépen.

   c. Töltse le és telepítse a VMware PowerCLI.

5. A **Specify vCenter Server details** (vCenter Server adatainak megadása) területen tegye a következőket:
    - Adja meg a név (FQDN) vagy a vCenter-kiszolgáló IP-címét.
    - A **felhasználónév** és **jelszó**, adja meg a csak olvasható fiók hitelesítő adatait, amelyet a gyűjtő virtuális gépek felderítése a vCenter Server fog használni.
    - A **hatókörét**, válassza ki a virtuális gép felderítési hatókörét. A gyűjtő képes felderíteni csak az adott hatókörben lévő virtuális gépek. A hatókör egy adott mappára, adatközpontra vagy fürtre állítható be. Hogy 1000-nél több virtuális gép nem tartalmaz. 

6. A **megadása áttelepítési projekt**, adja meg az Azonosítót, és a projekt kulcsát. Nem másolja őket, ha a gyűjtő VM nyissa meg az Azure-portálon. A projekt **áttekintése** lapon jelölje be **gépek felderítése** , és másolja az értékeket.  
7. A **gyűjtemény folyamatjelző**, a felderítési folyamat figyelésére, és győződjön meg arról, hogy a virtuális gépek gyűjtött metaadatai a hatókörben. Az adatgyűjtő mutatja a felderítés hozzávetőleges időtartamát.


### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítési idő a felderített virtuális gépek számától függ. Általában 100 virtuális gépekhez, felderítési befejezi a gyűjtő követően egy óra körül. 

1. Válassza ki az áttelepítési Planner projektben **kezelése** > **gépek**.
2. Ellenőrizze, hogy a felderíteni kívánt virtuális gépek megjelennek-e a portálon.


## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [hozzon létre egy csoportot](how-to-create-a-group.md) értékeléséhez.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
