---
title: "Felderítése és a helyszíni VMware virtuális gépek Azure-bA Azure áttelepítése áttelepítési értékeléséhez |} Microsoft Docs"
description: "Ismerteti, hogyan felderítése és a helyszíni VMware virtuális gépek áttelepítése az Azure-bA az Azure áttelepítése szolgáltatás értékeléséhez."
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a2521630-730f-4d8b-b298-e459abdced46
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 1c21364c3ff5cfb61866c912a699b722f2668607
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Fedezze fel és felmérheti a helyszíni VMware virtuális gépek áttelepítése az Azure-bA

A [Azure áttelepítése](migrate-overview.md) szolgáltatások értékelésére a helyszíni munkaterhelések Azure való áttelepítésre.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Azure áttelepítése projektet.
> * Egy helyszíni adatgyűjtő virtuális gép (VM) beállítását a helyszíni VMware virtuális gépek a ellenőrzéséhez.
> * Virtuális gépek csoportnak, és hozzon létre egy értékelést.


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

- **VMware**: szükség van legalább egy VMware virtuális gép található egy ESXi-állomáson, vagy 5.0-s vagy újabb verzióját futtató fürtre. A gazdagép vagy fürt 5.5, 6.0 vagy 6.5-ös verzióját futtató vCenter-kiszolgálót kell kezelnie.
- **vCenter fiók**: olvasási fiók rendszergazdai hitelesítő adatokkal a vCenter-kiszolgáló szükséges. Azure áttelepítése a virtuális gépek felderítése ezt a fiókot használja.
- **Engedélyek**: a vCenter-kiszolgáló, a virtuális gép létrehozása a fájl importálásával engedélyekre van szükség. PETESEJTEK formátumban. 
- **Statisztika beállítások**: A statisztika a vCenter-kiszolgáló beállításait kell megadni 3. szint telepítés megkezdése előtt. Ha alacsonyabb, mint 3 assessment szinten működik, de nem gyűjti a teljesítményadatokat tárolási és hálózati.

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.
Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="create-a-project"></a>Projekt létrehozása

1. Az Azure portálon kattintson **hozzon létre egy erőforrást**.
2. Keresse meg **Azure áttelepítése**, és válassza ki a szolgáltatást (**Azure áttelepítése (előzetes verzió)** a keresési eredmények között. Ezt követően kattintson a **Create** (Létrehozás) gombra.
3. Adja meg a projekt nevét, és az Azure-előfizetés a projekthez.
4. Hozzon létre egy új erőforráscsoportot.
5. Adja meg, a régiót, amelyben a projekt létrehozásához, majd kattintson a **létrehozása**. Ebben a régióban a helyszíni virtuális gépek összegyűjtött metaadatok tárolódnak. A régióban nyugati középső Régiójában az előzetes verzió csak az Azure áttelepítése projektek hozhat létre. Virtuális gépek felmérheti azonban egy másik hely.

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>Töltse le az adatgyűjtő-készülék

Azure áttelepítése létrehoz egy a helyszíni virtuális Gépre, a gyűjtő készülék néven ismert. A virtuális gép deríti fel a helyszíni VMware virtuális gépeket, és elküldi a metaadatokat róluk az Azure áttelepítése szolgáltatáshoz. A gyűjtő készülék beállításához töltse le egy. PETESEJTEK fájlt, és importálja a helyszíni vCenter-kiszolgáló a virtuális gép létrehozásához.

1. Az Azure áttelepítése projektben kattintson **bevezetés** > **felderítési & felmérési** > **gépek felderítése**.
2. A **gépek észlelése**, kattintson a **letöltése**, hogy töltse le a. PETESEJTEK fájlt.
3. A **projekt hitelesítő adatok másolása**, másolja a projekt Azonosítót, és a kulcs. Szükség van ezekre a gyűjtő konfigurálásakor.

    ![.Ova fájl letöltése](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Ellenőrizze az adatgyűjtő-készülék

Ellenőrizze, hogy a. PETESEJTEK fájl biztonságos, csak telepítheti azt.

1. A számítógépen, amelyre a fájlt letöltötte nyissa meg egy rendszergazdai parancsablakot.
2. A következő parancsot a kivonat létrehozásához a petesejtjének:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Példa használati:```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A generált kivonatoló meg kell felelnie, hogy ezeket a beállításokat.
    
    1.0.8.38 petesejtek verziójához
    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | dd27dd6ace28f9195a2b5d52a4003067 
    SHA1 | d2349e06a5d4693fc2a1c0619591b9e45c36d695
    SHA256 | 1492a0c6d6ef76e79269d5cd6f6a22f336341e1accbc9e3dfa5dad3049be6798

    1.0.8.40 petesejtek verziójához
    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>A gyűjtő virtuális gép létrehozása

A letöltött fájlt importálja a vCenter-kiszolgáló.

1. Kattintson a vSphere Client konzolon **fájl** > **OVF-sablon telepítése**.

    ![OVF telepítése](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. Az OVF-sablon központi telepítése varázslóban > **forrás**, adja meg a .ova fájl helyét.
3. A **neve** és **hely**, adjon meg egy rövid nevet a gyűjtő virtuális gép számára, és a készlet objektum található, amely a virtuális gép üzemelni fog.
5. A **állomás/fürt**, adja meg a gazdagép vagy fürt a gyűjtő virtuális gép elindul.
7. A tároló adja meg a célhelyet, a gyűjtő virtuális gép számára.
8. A **lemezformátum**, adja meg a lemez típusát és méretét.
9. A **Hálózatleképezés**, adja meg a hálózatot, amelyhez a gyűjtő Virtuálisgép kapcsolódik. A hálózati kell internetkapcsolattal, a metaadatok küldése az Azure-bA. 
10. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Befejezés**.

## <a name="run-the-collector-to-discover-vms"></a>Futtassa a gyűjtő virtuális gépek felderítése

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gép > **nyissa meg a konzolt**.
2. Adja meg a nyelvet, időzóna és a készülék jelszó beállításait.
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
    - A **címke kategória csoportosításhoz**, jelölje be **nincs**.
1. A **projekt kiválasztása**, adja meg az Azure áttelepítése projekt Azonosítót, és a portálról másolt kulcsot. Ha nem másolja át a fájlokat, az Azure-portál megnyitása a gyűjtő virtuális gép. A projekt **áttekintése** kattintson **gépek felderítése**, és másolja az értékeket.  
2. A **teljes felderítési**felderítési figyelje, és győződjön meg arról, hogy a virtuális gépek gyűjtött metaadatai a hatókörben. A gyűjtő megadja egy hozzávetőleges felderítés időtartamát.

> [!NOTE]
> A gyűjtő csak az "Angol (Egyesült Államok)" az operációs rendszer nyelve és a gyűjtő felület nyelvének támogatja. További nyelvek támogatása hamarosan elérhető.


### <a name="verify-vms-in-the-portal"></a>Ellenőrizze a virtuális gépek a portálon

Felderítési idő függ VMs hány derít fel. Általában 100 virtuális gépen is fut a gyűjtő végeztével időt vesz igénybe egy órát a felderítés befejezéséhez körül. 

1. Az áttelepítés Planner projektben kattintson **kezelése** > **gépek**.
2. Ellenőrizze, hogy a portálon jelennek meg a felderíteni kívánt virtuális gépeket.


## <a name="create-and-view-an-assessment"></a>Hozzon létre és értékelés megtekintése

Virtuális gépek később, akkor csoportosításához, és hozzon létre értékelését. 

1. A projekt **áttekintése** kattintson **+ assessment létrehozása**.
2. Kattintson a **összes** assessment beállításainak ellenőrzéséhez.
3. A csoport létrehozásához, és adjon meg egy felügyeleticsoport-nevet.
4. Válassza ki a gépeket, a csoporthoz hozzáadni kívánt.
5. Kattintson a **létrehozása Assessment**, a csoport és az értékelés létrehozásához.
6. Az értékelés létrehozása után megtekintheti a **áttekintése** > **irányítópult**.
7. Kattintson a **assessment exportálása**, letöltheti az Excel-fájlként.

### <a name="sample-assessment"></a>A minta értékelése

Íme egy példa értékelési jelentést. Az Azure és a becsült havi költségeket kompatibilis csatlakoznak-e virtuális gépek kapcsolatos információkat tartalmaz. 

![Értékelés jelentés](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Az Azure készültségi

Ez a nézet megjeleníti az egyes gépek készültségi állapotát.

- Virtuális gépekhez, amely készen áll Azure telepítse át azt javasolja, hogy a virtuális gép méretét, az Azure-ban.
- Virtuális gépekhez, amelyek nem állnak készen Azure áttelepítése bemutatja ennek okát, valamint javítási lépéseket.
- Az Azure áttelepítése az áttelepítés használható eszközök javasol. Ha a számítógép megfelelő-e a növekedési és shift áttelepítési, ajánlott az Azure Site Recovery szolgáltatásban. Ha egy adatbázis-számítógép, ajánlott az Azure-adatbázis áttelepítési szolgáltatás.

  ![Assessment readiness](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Havi költség becslése

Ez a nézet megjeleníti a számítási és a tárolás, az egyes gépek költségszámítás. Becsült költség kiszámítása a teljesítmény-alapú méretével kapcsolatos megfontolások a gépek és a lemezek és az értékelés tulajdonságok használatával.

Becsült havi költségeket a számítási és tárolási összesítése a csoportban lévő virtuális gép esetében. További részletek a részletezéshez minden számítógépen kattinthat. 

![Értékelés VM költsége](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

Egy adott gép lásd a költségek is lebontva.

![Értékelés VM költsége](./media/tutorial-assessment-vmware/assessment-vm-drill.png) 

## <a name="next-steps"></a>Következő lépések

- [Ismerje meg,](how-to-scale-assessment.md) beállítása a helyszíni gépeket nagy számú értékelését.
- Részletesebb assessment csoportok létrehozásával [gép függőségi leképezése](how-to-create-group-machine-dependencies.md)
- [További](concepts-assessment-calculation.md) kapcsolatos értékelések kiszámítási módját.
