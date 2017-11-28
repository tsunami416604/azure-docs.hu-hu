---
title: "Felderítési és a kiértékelés Azure áttelepítése méretezhető |} Microsoft Docs"
description: "A helyszíni gépeket az Azure áttelepítése szolgáltatással nagy számú értékeléséhez ismerteti."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dde0d07f-94b7-4b6a-a158-a89aa9324a35
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 930ec182cf329e7dda072dc49bd7f70abb413f2d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Fedezze fel és nagy VMware-környezet felmérése

Ez a cikk ismerteti, hogyan annak ellenőrzéséhez, a helyszíni gépeket nagy mennyiségű [Azure áttelepítése](migrate-overview.md). Az Azure áttelepítése ellenőrizze, hogy azok alkalmas áttelepítése az Azure-ba, és biztosít az Azure-beli a gép méretezési és költségű becsléseket gépek értékelésére.

## <a name="prerequisites"></a>Előfeltételek

- **VMware**: szükség van legalább egy VMware virtuális gép található egy ESXi-állomáson, vagy 5.0-s vagy újabb verzióját futtató fürtre. A gazdagép vagy fürt egy vCenter server 5.5 vagy 6.0-s verzióját futtató kell kezelnie.
- **vCenter fiók**: olvasási fiók rendszergazdai hitelesítő adatokkal a vCenter-kiszolgáló szükséges. Azure áttelepítése a virtuális gépek felderítése ezt a fiókot használja.
- **Engedélyek**: a vCenter-kiszolgáló, a virtuális gép létrehozása a fájl importálásával engedélyekre van szükség. PETESEJTEK formátumban.
- **Statisztika beállítások**: a vCenter-kiszolgáló statisztikák beállításait kell megadni szint 2-es vagy újabb verzióját, mielőtt elkezdené a telepítést.

## <a name="plan-azure-migrate-projects"></a>Tervezze meg a projektek Azure áttelepítése

Azure áttelepítése projektben legfeljebb 1500 gépek felmérheti. Egy adott felderítési projektben legfeljebb 1000 gépek képes felderíteni.

- Ha 1000-nél kevesebb számítógépek felderítésére, az adott felderítési egyetlen projekt szüksége.
- Ha 1000 és 1500 gépek között, egyetlen projekttel ezen két felderítések azt kell.
- Ha több mint 1500 gépekkel rendelkezik, meg kell hozzon létre több projektet, és hajtsa végre több felderítések, a követelményeknek megfelelően. Példa:
    - Ha 3000 gépekkel rendelkezik, akkor beállíthat két felderítések-projektek két vagy három projektek egy adott felderítési.
    - Ha 5000 gépekkel rendelkezik, akkor beállíthat négy projektek. Két 1500 gépek felderítést, és a felderítés 500 gépek másikat. Azt is megteheti akkor beállíthat egy adott felderítési egyes öt projektek. 
- Ha így tesz, a felderítés az Azure át, a felderítési hatókör egy VMware mappa, a datacenter vagy a fürt is beállíthatja.
- Ehhez az egynél több felderítési, ellenőrizze a vCenter, hogy a virtuális gépeket szeretné felderíteni mappák, adatközpontok vagy fürt, amely támogatja a korlátozás 1000 gépek.
- Azt javasoljuk, értékelési célokra, akkor ne gépek közti függőségek ugyanabban a projektben és értékelési belül. Ezért a vCenter, ellenőrizze, hogy függő gépek ugyanazt a mappában, datacenter vagy fürt assessment céljából.


## <a name="create-a-project"></a>Projekt létrehozása

Hozzon létre egy saját igényei szerint hajtsa Azure áttelepítése projektet.

1. Az Azure portálon kattintson **hozzon létre egy erőforrást**.
2. Keresse meg **Azure áttelepítése**, és válassza ki a szolgáltatást (**Azure áttelepítése (előzetes verzió)** a keresési eredmények között. Ezt követően kattintson a **Create** (Létrehozás) gombra.
3. Adja meg a projekt nevét, és az Azure-előfizetés a projekthez.
4. Hozzon létre egy új erőforráscsoportot.
5. Adja meg, a régiót, amelyben a projekt létrehozásához, majd kattintson a **létrehozása**. A helyszíni virtuális gépek összegyűjtött metaadatok tárolódnak, ebben a régióban.

## <a name="set-up-the-collector-appliance"></a>A gyűjtő készülék beállítása

Azure áttelepítése létrehoz egy a helyszíni virtuális Gépre, a gyűjtő készülék néven ismert. A virtuális gép deríti fel a helyszíni VMware virtuális gépeket, és elküldi a metaadatokat róluk az Azure áttelepítése szolgáltatáshoz. A gyűjtő készülék beállításához töltse le egy. PETESEJTEK fájlt, és importálja a helyszíni vCenter-kiszolgáló a virtuális gép létrehozásához.

### <a name="download-the-collector-appliance"></a>Töltse le az adatgyűjtő-készülék

Ha több projektet, csak töltse le az adatgyűjtő készülék egyszer a vCenter-kiszolgáló szükséges. Letöltötte és a készülék beállítása, futtatja azt minden olyan projekthez, és adja meg a projekt egyedi azonosítója és kulcsa.

1. Az Azure áttelepítése projektben kattintson **bevezetés** > **felderítési & felmérési** > **gépek felderítése**.
2. A **gépek észlelése**, kattintson a **letöltése**, hogy töltse le a. PETESEJTEK fájlt.
3. A **projekt hitelesítő adatok másolása**, másolja le az Azonosítót, és a projekt kulcsát. Szükség van ezekre a gyűjtő konfigurálásakor.

   
### <a name="verify-the-collector-appliance"></a>Ellenőrizze az adatgyűjtő-készülék

Ellenőrizze, hogy a. PETESEJTEK fájl biztonságos, csak telepítheti azt.

1. A számítógépen, amelyre a fájlt letöltötte nyissa meg egy rendszergazdai parancsablakot.
2. A következő parancsot a kivonat létrehozásához a petesejtjének:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Példa használati:```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A generált kivonatoló meg kell felelnie, hogy ezeket a beállításokat.

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | c283f00f46484bf673dc8383b01d0741 
    SHA1 | 8f49b47f53d051af1780bbc725659ce64e717ed4
    SHA256 | 7aecdbdb2aea712efe99d0c1444503f52d16de5768e783465e226fbbca71501d

## <a name="create-the-collector-vm"></a>A gyűjtő virtuális gép létrehozása

A letöltött fájlt importálja a vCenter-kiszolgáló.

1. Kattintson a vSphere Client konzolon **fájl** > **OVF-sablon telepítése**.

    ![OVF telepítése](./media/how-to-scale-assessment/vcenter-wizard.png)

2. Az OVF-sablon központi telepítése varázslóban > **forrás**, adja meg a .ova fájl helyét.
3. A **neve** és **hely**, adjon meg egy rövid nevet a gyűjtő virtuális gép számára, és a készlet objektum található, amely a virtuális gép üzemelni fog.
5. A **állomás/fürt**, adja meg a gazdagép vagy fürt a gyűjtő virtuális gép elindul.
7. A tároló adja meg a célhelyet, a gyűjtő virtuális gép számára.
8. A **lemezformátum**, adja meg a lemez típusát és méretét.
9. A **Hálózatleképezés**, adja meg a hálózatot, amelyhez a gyűjtő Virtuálisgép kapcsolódik. A hálózati kell internetkapcsolattal, a metaadatok küldése az Azure-bA. 
10. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Befejezés**.

## <a name="identify-the-key-and-id-for-each-project"></a>A kulcs és az azonosító minden olyan projekthez azonosítása

Ha több projektet, győződjön meg arról, azonosítsa az azonosítója, és mindegyik kulcs. Be kell a kulcsot, a virtuális gépek felderítése a gyűjtő futtatásakor.

1. A projektben kattintson **bevezetés** > **felderítési & felmérési** > **gépek felderítése**.
2. A **projekt hitelesítő adatok másolása**, másolja le az Azonosítót, és a projekt kulcsát. 
    ![Projekt azonosítója](./media/how-to-scale-assessment/project-id.png)

## <a name="vcenter-statistics-level-to-collect-the-performance-counters"></a>vCenter statisztika szintű a teljesítményszámlálók adatainak összegyűjtése
A felderítés során gyűjtött teljesítményszámlálók listája látható. A számlálók a rendszer a vCenter-kiszolgáló különböző szinten elérhető alapértelmezés szerint. Azt javasoljuk, hogy a számlálók megfelelően vannak összegyűjtött állítsa be a statisztika szint legnagyobb közös szintjét (3. szint). Ha egy alacsonyabb szinten vCenter, csak néhány előfordulhat, hogy beolvasása gyűjtött adatait teljesen, és a többi értéke 0. Az értékelés ezért előfordulhat, hogy megjelenítése nem teljes adatokat. Az alábbi táblázat az értékelési eredmények, amelyek csökkenhet, ha a számláló nem gyűjtött a program listája is.

|A számláló                                  |Szint    |Eszköz szintenként  |Értékelés gyakorolt hatás                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|CPU.Usage.average                        | 1       |NA                |Ajánlott Virtuálisgép-méretet és költség                    |
|mem.Usage.average                        | 1       |NA                |Ajánlott Virtuálisgép-méretet és költség                    |
|virtualDisk.read.average                 | 2       |2                 |Lemez méretét, tárolási költségek és a Virtuálisgép-méretet         |
|virtualDisk.write.average                | 2       |2                 |Lemez méretét, tárolási költségek és a Virtuálisgép-méretet         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Lemez méretét, tárolási költségek és a Virtuálisgép-méretet         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Lemez méretét, tárolási költségek és a Virtuálisgép-méretet         |
|NET.Received.average                     | 2       |3                 |Virtuálisgép-méretet és hálózati költsége                        |
|NET.transmitted.average                  | 2       |3                 |Virtuálisgép-méretet és hálózati költsége                        |

> [!WARNING]
> Ha csak magasabb szintű statisztika, a teljesítményszámlálók létrehozni naponta legfeljebb fog tartani. Ezért ajánlott egy nap után a felderítés végrehajtásakor.

## <a name="run-the-collector-to-discover-vms"></a>Futtassa a gyűjtő virtuális gépek felderítése

Minden felderítés kell elvégezni futtassa a gyűjtő felderítéssel virtuális gépek a szükséges hatókörében. Futtassa a másikat a másik után. Egyidejű felderítések nem támogatottak, és mindegyik felderítés rendelkeznie kell egy másik hatókört.

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gép > **nyissa meg a konzolt**.
2. Adja meg a nyelvet, időzóna és a készülék jelszó beállításai.
3. Az asztalon kattintson a **futtassa a gyűjtő** helyi.
4. Nyissa meg az Azure áttelepítése gyűjtő **Előfeltételek beállítása**.
    - Fogadja el a licencfeltételeket, és a külső adatokat olvasni.
    - A gyűjtő ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel.
    - Ha a virtuális gép fér hozzá az internethez olyan proxyn keresztül, kattintson a **proxybeállítások**, és adja meg a proxykiszolgáló címét és a figyelő portja. Adjon meg hitelesítő adatokat, ha a proxy hitelesítést igényel.
    - A gyűjtő ellenőrzi, hogy a Windows Profilkészítő szolgáltatás fut-e. A szolgáltatás a gyűjtő virtuális gép alapértelmezés szerint telepítve van.
    - Töltse le és telepítse a VMware PowerCLI.
. A **gépek felderítése**, tegye a következőket:
    - Adja meg a név (FQDN) vagy a vCenter-kiszolgáló IP-címét.
    - A **felhasználónév** és **jelszó**, adja meg a csak olvasható fiók hitelesítő adatait, amelyet a gyűjtő virtuális gépek felderítése a vCenter Server fog használni.
    - A **gyűjtemény hatókör**, válassza ki a virtuális gép felderítési hatókörét. A gyűjtő csak a megadott hatókörön belüli virtuális gépek képes felderíteni. Egy adott mappában, a datacenter vagy a fürt állítható be hatókör. Hogy 1000-nél több virtuális gép nem tartalmaz. 
    - n **Címke kategória csoportosításhoz**, jelölje be **nincs**.

        ![Hatókör kijelölése](./media/how-to-scale-assessment/select-scope.png)

1. A **projekt kiválasztása**, adja meg az Azonosítót, és a projekt kulcsát. Ha nem másolja át a fájlokat, az Azure-portál megnyitása a gyűjtő virtuális gép. A projekt **áttekintése** kattintson **gépek felderítése**, és másolja az értékeket.  
A **teljes felderítési**, a felderítési folyamat figyelésére, és győződjön meg arról, hogy a virtuális gépek gyűjtött metaadatai a hatókörben. A gyűjtő megadja egy hozzávetőleges felderítés időtartamát.


### <a name="verify-vms-in-the-portal"></a>Ellenőrizze a virtuális gépek a portálon

Felderítési idő függ VMs hány derít fel. Általában 100 virtuális gépen is fut a gyűjtő végeztével időt vesz igénybe egy órát a felderítés befejezéséhez körül. 

1. Az áttelepítés Planner projektben kattintson **kezelése** > **gépek**.
2. Ellenőrizze, hogy a portálon jelennek meg a felderíteni kívánt virtuális gépeket.


## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hozzon létre egy csoportot](how-to-create-a-group.md) értékeléséhez.
- [További](concepts-assessment-calculation.md) kapcsolatos értékelések kiszámítási módját.