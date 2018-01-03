---
title: "Bővítse a felderítés és az értékelés Azure áttelepítése használatával |} Microsoft Docs"
description: "Ismerteti, hogyan lehet az Azure áttelepítése szolgáltatás használatával nagy számú helyszíni gépet felmérésére."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/19/2017
ms.author: raynew
ms.openlocfilehash: 9b457252fdb7a1ad62b7e6038b341451df2e1590
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Fedezze fel és nagy VMware-környezet felmérése

Ez a cikk ismerteti, hogyan használatával a helyszíni virtuális gépek (VM) nagy számú felmérésére [Azure áttelepítése](migrate-overview.md). Az Azure áttelepítése értékelésére gépek ellenőrizze, hogy fontosságúak alkalmas áttelepítése az Azure-bA. A szolgáltatás biztosítja a méretezési és költségű becsléseket futó a gépeket az Azure-ban.

## <a name="prerequisites"></a>Előfeltételek

- **VMware**: A virtuális gépek, amelyek az áttelepíteni kívánt vCenter Server 5.5, 6.0 vagy 6.5 kell kezelnie. Emellett kell egy ESXi gazdagépen futó verziójával 5.0-s vagy újabb, a gyűjtő VM telepítése.
- **vCenter fiók**: olvasási fiók vCenter-kiszolgáló eléréséhez szükséges. Az Azure áttelepítése ezt a fiókot használja a helyszíni virtuális gépek felderítése.
- **Engedélyek**: A vCenter Server, hozzon létre egy virtuális Gépet petesejtek formátumú fájl importálásával engedélyekre van szükség.
- **Statisztika beállítások**: A statisztika vcenter Server kell beállítás 3. szint telepítés megkezdése előtt. Ha a szintje alacsonyabb, mint 3, a frissítésfelmérő működni fog, de a tárolási és hálózati teljesítményadatok nem gyűjthetők. A méret javaslatok ebben az esetben alapjául Processzor- és teljesítményadatokat és a lemezek és a hálózati adapterek konfigurációs adatait.

## <a name="plan-azure-migrate-projects"></a>Tervezze meg a projektek Azure áttelepítése

Tervezze meg a felderítések és a vizsgálatok során a következő korlátozások:

| **Entitás** | **Számítógépek korlátja** |
| ---------- | ----------------- |
| Project    | 1,500              | 
| Detektálás  | 1,000              |
| Értékelés | 400               |

- Ha kevesebb mint 400 gépek felderítése és értékeléséhez, egyetlen projekt és szükséges egy adott felderítési. A követelményeitől függően a egyetlen értékelése gépeire értékeléséhez, vagy ossza fel a gépek több értékeléseket. 
- Ha 400-1 000 számítógépek felderítésére, az adott felderítési egyetlen projekt szüksége. Azonban szüksége lesz több vizsgálatot, ezek a gépek értékeléséhez, mert egyetlen assessment legfeljebb 400 gépek tárolására képes.
- Ha 1,001 való 1500 gépekkel rendelkezik, két felderítések azt az egyetlen projekt kell.
- Ha több mint 1500 gépekkel rendelkezik, akkor kell hozzon létre több projektet, és hajtsa végre több felderítések, a követelményeknek megfelelően. Példa:
    - Ha 3000 olyan gépeken, állíthat be két felderítések-projektek két vagy három projektek egy adott felderítési.
    - Ha 5000 gépekkel rendelkezik, akkor állíthatja négy projektek: két 1500 gépek felderítést, és a felderítés 500 gépek másikat. Másik lehetőségként állíthat be egy adott felderítési egyes öt projektek. 

## <a name="plan-multiple-discoveries"></a>Több felderítések megtervezése

Az azonos Azure áttelepítése gyűjtő segítségével egy vagy több projektek több felderítések tegye. Ezeket a tervezési szempontokat vegye figyelembe:
 
- A felderítés az Azure áttelepítése gyűjtő úgy teheti meg, amikor a felderítési hatókör is beállíthatja a vCenter Server mappa, a datacenter, a fürt vagy a gazdagép.
- Ehhez az egynél több felderítési, ellenőrizze a vCenter-kiszolgáló által a virtuális gépek felderíteni kívánt mappák, az adatközpontok, a fürtök vagy a gazdagépekhez, amelyek támogatják a korlátozás 1000 gépek.
- Azt javasoljuk, értékelési célokra, akkor ne gépek egymástól függő szolgáltatásainak ugyanabban a projektben és értékelési belül. A vCenter Server ellenőrizze, hogy a függő gépek ugyanazon a mappa, datacenter vagy fürt értékeléséhez.


## <a name="create-a-project"></a>Projekt létrehozása

A követelményeknek megfelelően Azure áttelepítése projekt létrehozása:

1. Válassza ki az Azure-portálon **hozzon létre egy erőforrást**.
2. Keresse meg **Azure áttelepítése**, és válassza ki a szolgáltatást **Azure áttelepítése (előzetes verzió)** a keresési eredmények között. Ezután kattintson a **Létrehozás** elemre.
3. Adja meg a projekt nevét és az Azure-előfizetés a projekthez.
4. Hozzon létre egy új erőforráscsoportot.
5. Adja meg a helyet, ahol a projekt létrehozásához, majd válassza ki szeretné **létrehozása**. Vegye figyelembe, hogy a virtuális gépeket egy másik célhelyet a továbbra is felmérheti. A projekt helyére a metaadatok összegyűjtött a helyszíni virtuális gépek tárolására szolgál.

## <a name="set-up-the-collector-appliance"></a>A gyűjtő készülék beállítása

Azure áttelepítése létrehoz egy a helyszíni virtuális Gépre, a gyűjtő készülék néven ismert. A virtuális gép deríti fel a helyszíni VMware virtuális gépek, és a rájuk vonatkozó metaadatok küld az Azure áttelepítése szolgáltatás. A gyűjtő készülék beállításához petesejtek fájl letöltésére, és importálja azt a helyszíni vCenter Server-példányhoz.

### <a name="download-the-collector-appliance"></a>Töltse le az adatgyűjtő-készülék

Ha több projekt van, akkor töltse le az adatgyűjtő készülék csak egyszer vCenter-kiszolgáló. Töltse le, és állítsa be a készülék, után kell futtatnia minden olyan projekthez, és a projekt egyedi Azonosítóját és kulcsát meg.

1. Azure áttelepítése projekt kijelölni **bevezetés** > **felderítési & felmérési** > **gépek felderítése**.
2. A **gépek észlelése**, jelölje be **letöltése**, a petesejtek fájl letöltéséhez.
3. A **projekt hitelesítő adatok másolása**, másolja le az Azonosítót, és a projekt kulcsát. Szükség van ezekre a gyűjtő konfigurálásakor.

   
### <a name="verify-the-collector-appliance"></a>Ellenőrizze az adatgyűjtő-készülék

Ellenőrizze, hogy a petesejtek fájlt biztonságos telepítése előtt:

1. A számítógépen, amelyre a fájlt letöltötte nyissa meg egy rendszergazdai parancsablakot.
2. A következő parancsot a kivonat létrehozásához a petesejtjének:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Példa használati:```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Győződjön meg arról, hogy a létrehozott kivonatoló megegyezik-e a következő beállításokat.
 
    A petesejtek verziója 1.0.8.38:
    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | dd27dd6ace28f9195a2b5d52a4003067 
    SHA1 | d2349e06a5d4693fc2a1c0619591b9e45c36d695
    SHA256 | 1492a0c6d6ef76e79269d5cd6f6a22f336341e1accbc9e3dfa5dad3049be6798

    A petesejtek verziója 1.0.8.40:
    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad



## <a name="create-the-collector-vm"></a>A gyűjtő virtuális gép létrehozása

A letöltött fájlt a vCenter-kiszolgáló importálása:

1. Válassza ki a vSphere ügyfélkonzol **fájl** > **OVF-sablon telepítése**.

    ![OVF telepítése](./media/how-to-scale-assessment/vcenter-wizard.png)

2. Az OVF-sablon központi telepítése varázslóban > **forrás**, adja meg a petesejtek fájl helyét.
3. A **neve** és **hely**, adjon meg egy rövid nevet a gyűjtő virtuális gép számára, és a készlet objektum található, amely a virtuális gép üzemelni fog.
5. A **állomás/fürt**, adja meg a gazdagép vagy fürt a gyűjtő virtuális gép elindul.
7. A tároló adja meg a célhelyet, a gyűjtő virtuális gép számára.
8. A **lemezformátum**, adja meg a lemez típusát és méretét.
9. A **Hálózatleképezés**, adja meg a hálózatot, amelyhez a gyűjtő Virtuálisgép kapcsolódik. A hálózati metaadatok küldésére Azure internetkapcsolat szükséges. 
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
|mem.Usage.average                        | 1       |NA                |Ajánlott Virtuálisgép-méretet és költség                    |
|virtualDisk.read.average                 | 2       |2                 |Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet         |
|virtualDisk.write.average                | 2       |2                 |Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Lemez méretét, a tárolási költségek és a Virtuálisgép-méretet         |
|NET.Received.average                     | 2       |3                 |Virtuális gép mérete és a hálózati költség                        |
|NET.transmitted.average                  | 2       |3                 |Virtuális gép mérete és a hálózati költség                        |

> [!WARNING]
> Ha csak magasabb szintű statisztika, tart naponta a teljesítményszámlálók létrehozásához. Igen azt javasoljuk, hogy a felderítés egy nap után.

## <a name="run-the-collector-to-discover-vms"></a>Futtassa a gyűjtő virtuális gépek felderítése

Minden felderítés, végre kell hajtania a gyűjtő felderítéséhez szükséges hatókörében virtuális gépek futnak. Futtassa a másikat a másik után. Egyidejű felderítések nem támogatottak, és mindegyik felderítés rendelkeznie kell egy másik hatókört.

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gép > **nyissa meg a konzolt**.
2. Adja meg a nyelvet, időzóna és a készülék jelszó beállításai.
3. Az asztalon, válassza ki a **futtassa a gyűjtő** helyi.
4. Nyissa meg az Azure áttelepítése gyűjtő **előfeltétel** , majd:

   a. Fogadja el a licencfeltételeket, és a külső adatokat olvasni.

   A gyűjtő ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel.
   
   b. Ha a virtuális gép fér hozzá az internethez olyan proxyn keresztül, válassza ki a **proxybeállítások**, adja meg a proxykiszolgáló címét és a figyelő portja. Adjon meg hitelesítő adatokat, ha a proxy hitelesítést igényel.

   A gyűjtő ellenőrzi, hogy a gyűjtő szolgáltatás fut-e. A szolgáltatás a gyűjtő virtuális gép alapértelmezés szerint telepítve van.

   c. Töltse le és telepítse a VMware PowerCLI.

5. A **adja meg a vCenter-kiszolgáló adatait**, tegye a következőket:
    - Adja meg a név (FQDN) vagy a vCenter-kiszolgáló IP-címét.
    - A **felhasználónév** és **jelszó**, adja meg a csak olvasható fiók hitelesítő adatait, amelyet a gyűjtő virtuális gépek felderítése a vCenter Server fog használni.
    - A **hatókörét**, válassza ki a virtuális gép felderítési hatókörét. A gyűjtő képes felderíteni csak az adott hatókörben lévő virtuális gépek. Egy adott mappában, a datacenter vagy a fürt állítható be hatókör. Hogy 1000-nél több virtuális gép nem tartalmaz. 
    - A **vCenter címke kategória csoportosításhoz**, jelölje be **nincs**.

    ![Hatókör kijelölése](./media/how-to-scale-assessment/select-scope.png)

6. A **megadása áttelepítési projekt**, adja meg az Azonosítót, és a projekt kulcsát. Nem másolja őket, ha a gyűjtő VM nyissa meg az Azure-portálon. A projekt **áttekintése** lapon jelölje be **gépek felderítése** , és másolja az értékeket.  
7. A **gyűjtemény folyamatjelző**, a felderítési folyamat figyelésére, és győződjön meg arról, hogy a virtuális gépek gyűjtött metaadatai a hatókörben. A gyűjtő megadja egy hozzávetőleges felderítés időtartamát.


### <a name="verify-vms-in-the-portal"></a>Ellenőrizze a virtuális gépek a portálon

Felderítési idő függ VMs hány derít fel. Általában 100 virtuális gépekhez, felderítési befejezi a gyűjtő követően egy óra körül. 

1. Válassza ki az áttelepítési Planner projektben **kezelése** > **gépek**.
2. Ellenőrizze, hogy a portálon jelennek meg a felderíteni kívánt virtuális gépeket.


## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [hozzon létre egy csoportot](how-to-create-a-group.md) értékeléséhez.
- [További](concepts-assessment-calculation.md) kapcsolatos értékelések kiszámítási módját.
