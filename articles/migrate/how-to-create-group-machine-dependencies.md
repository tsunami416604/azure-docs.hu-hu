---
title: Gép függőségek használata Azure áttelepítése gépek |} Microsoft Docs
description: Útmutatás a gép függőségek használata az Azure áttelepítése szolgáltatásával értékelését létrehozásához.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: a9850044266ec05cee5e32c6825609bcf969351d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Csoport gépek gép függőségi leképezés használata

Ez a cikk ismerteti a gépcsoport létrehozása [Azure áttelepítése](migrate-overview.md) értékelés szerint függőségeinek gépek megjelenítése. Általában ezt a módszert, ha a virtuális gépek abban, hogy magasabb szintű csoportok gép függőségekkel ellenőrzése értékelését futtatása előtt ellenőrizni szeretné. Függőség képi megjelenítés segítségével tervezze az áttelepítést, az Azure-ba, hatékony. Ennek segítségével győződjön meg arról, hogy semmi sem hátrahagyott és jelzés nélküli valamilyen okból kimaradás lép fel, ha telepít át az Azure-bA. Minden egymástól kölcsönösen függnek, áttelepítése együtt, és adja meg, hogy a futó rendszer van még kiszolgáló felhasználók, vagy egy jelölt leszerelése áttelepítési helyett a rendszer felderíthetők. 


## <a name="prepare-machines-for-dependency-mapping"></a>A függőség leképezést gépek előkészítése
Gépek függőségeinek megtekintése, meg kell töltse le és telepítse az ügynököt minden a helyszíni gépen, amelyet ki kell számítani. Emellett, ha internetkapcsolat nélküli gépek, kell letöltéséhez és telepítéséhez [OMS átjáró](../log-analytics/log-analytics-oms-gateway.md) rajtuk.

### <a name="download-and-install-the-vm-agents"></a>A virtuálisgép-ügynökök letöltése és telepítése
1. A **áttekintése**, kattintson a **kezelése** > **gépek**, és jelölje ki a szükséges gépet.
2. Az a **függőségek** oszlopban kattintson **ügynökök telepítése**. 
3. Az a **függőségek** lapon, töltse le és telepítse a Microsoft Monitoring Agent (MMA) és a függőségi ügynök egyes virtuális gépek ki kell számítani.
4. Másolja ki a munkaterület-azonosítót és -kulcsot. Szükség van ezekre az MMA a helyi számítógépen való telepítésekor.

### <a name="install-the-mma"></a>Az MMA telepítése

Az ügynök telepítése Windows-gépen:

1. Kattintson duplán a letöltött ügynökre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra. A **Licencfeltételek** oldalon kattintson az **Elfogadom** gombra a feltételek elfogadásához.
3. A **célmappa**, hagyja, vagy módosítsa az alapértelmezett telepítési mappa > **következő**. 
4. A **ügynök telepítésének beállításai**, jelölje be **Azure Naplóelemzés** > **következő**. 
5. Kattintson a **Hozzáadás** hozzáadása egy új munkaterületet. Illessze be a munkaterület azonosítója és a portál fájlból másolt kulcsot. Kattintson a **Tovább** gombra.


Az ügynök telepítése Linux gépen:

1. Vigye át a megfelelő csomagot (x86 vagy x64) a Linux-számítógép scp/sftp használatával.
2. A csomag telepítéséhez használja a--telepítés argumentum.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése
1. A függőségi ügynök telepíthető a Windows-számítógép, kattintson duplán a telepítőfájlra, és kövesse a varázsló utasításait.
2. A függőségi ügynök telepíthető egy Linux-számítógép, telepítse a legfelső szintű a következő parancsot:

    ```sh InstallDependencyAgent-Linux64.bin```

[További](../monitoring/monitoring-service-map-configure.md#supported-operating-systems) a függőségi ügynök által támogatott operációs rendszerekkel kapcsolatban. 

## <a name="create-a-group"></a>Hozzon létre egy csoportot

1. Miután telepítette az ügynököt, nyissa meg a portálon, majd kattintson **kezelése** > **gépek**.
2. Keresse meg a számítógép, amelyre telepítette az ügynököt.
3. A **függőségek** oszlop a gép most meg kell jelennie **függőségeinek megtekintése**. Kattintson az oszlop, a gép függőségeinek megtekintése.
4. A függőségi térkép, a gép a következő részleteit jeleníti meg:
    - Bejövő (ügyfelek) és a gép onnan kimenő (kiszolgálók) TCP-kapcsolatok
        - A függő gépek, amelyeken nincs telepítve az MMA és függőségi ügynök által használt portszámok vannak csoportosítva.
        - Külön mezőkbe jelennek meg a dependenct gépeken, amelyek a MMA és a függőségi ügynök van telepítve 
    - A gépen futó folyamatok, minden gép mezőben folyamatokat bővítheti
    - Minden számítógépen, ha azt szeretné, hogy a részletek megjelenítéséhez kattintson például a teljesen minősített tartománynevét, operációs rendszer, az egyes gépek MAC-cím stb tulajdonságok,

 ![Gépek függőségeinek megtekintése](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Vessen egy pillantást függőségek különböző idő időtartamra az időtartamig, idő tartomány feliratában kattintva. Alapértelmezés szerint a tartománya egy óra. Módosítsa az időtartományt, vagy adja meg a kezdő és záró dátumát, és időtartama.
5. Miután függő csoportba a kívánt gépek állapította meg, a Ctrl + kattintással jelölje ki a térképen több gép, majd kattintson **gépek**.
6. Adjon meg egy felügyeleticsoport-nevet. Győződjön meg arról, hogy a függő gépeket Azure áttelepítése által felderített. 

    > [!NOTE]
    > Ha egy függő gép alkalmazás nem észlelt Azure át, meg nem adható a csoporthoz. Ilyen gépek felvétele a csoportba, akkor kell futtassa újra a felderítési folyamat a megfelelő hatókörben található vCenter-kiszolgáló, és győződjön meg arról, hogy a gép észlel Azure áttelepíteni.  

7. Ha szeretne létrehozni ehhez a csoporthoz értékelését, jelölje be a jelölőnégyzetet, a csoport egy új assessment létrehozásához.
8. Kattintson a **OK** kell elmentenie a csoportot.

A csoport létrehozása után javasoljuk, hogy az ügynökök telepítésekor a csoport minden számítógépen, és a csoport pontosítás megjelenítése a teljes csoport függőségi.

## <a name="next-steps"></a>További lépések

- [Megtudhatja, hogyan](how-to-create-group-dependencies.md) pontosítsa a csoport által Függőségek megjelenítése
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
