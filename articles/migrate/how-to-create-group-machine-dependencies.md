---
title: "Gép függőségek használata Azure áttelepítése gépek |} Microsoft Docs"
description: "Útmutatás a gép függőségek használata az Azure áttelepítése szolgáltatásával értékelését létrehozásához."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 769c05916de4e7ad5b14812c2c8dbcf69e91320c
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Csoport gépek gép függőségi leképezés használata

Ez a cikk ismerteti a gépcsoport létrehozása [Azure áttelepítése](migrate-overview.md) assessment gép függőségi leképezés használata. Általában ezt a módszert, ha a virtuális gépek abban, hogy magasabb szintű csoportok gép függőségekkel ellenőrzése értékelését futtatása előtt ellenőrizni szeretné.



## <a name="prepare-machines-for-dependency-mapping"></a>A függőség leképezést gépek előkészítése
Függőségi leképezésben gépek felvételéhez szükség töltse le és telepítse az ügynököt minden a helyszíni gépen, amelyet ki kell számítani. Emellett, ha internetkapcsolat nélküli gépek, kell letöltéséhez és telepítéséhez [OMS átjáró](../log-analytics/log-analytics-oms-gateway.md) rajtuk.

### <a name="download-and-install-the-vm-agents"></a>Töltse le és telepítse a virtuális gép ügynökök
1. A **áttekintése**, kattintson a **kezelése** > **gépek**, és jelölje ki a szükséges gépet.
2. Az a **függőségek** oszlopban kattintson **ügynökök telepítése**. 
3. Az a **függőségek** lapon, töltse le és telepítse a Microsoft Monitoring Agent (MMA) és a függőségi ügynök egyes virtuális gépek ki kell számítani.
4. Másolja a munkaterület azonosítója és kulcsa. Szükség van ezekre az MMA a helyi számítógépen való telepítésekor.

### <a name="install-the-mma"></a>Telepítse a MMA

Az ügynök telepítése Windows-gépen:

1. Kattintson duplán a letöltött ügynök.
2. Az a **üdvözlő** kattintson **következő**. Az a **licencfeltételeket** kattintson **elfogadom** elfogadja a licencfeltételeket.
3. A **célmappa**, hagyja, vagy módosítsa az alapértelmezett telepítési mappa > **következő**. 
4. A **ügynök telepítésének beállításai**, jelölje be **Azure Naplóelemzés (OMS)** > **következő**. 
5. Kattintson a **Hozzáadás** hozzáadása egy új OMS-munkaterület. Illessze be a munkaterület azonosítója és a portál fájlból másolt kulcsot. Kattintson a **Tovább** gombra.


Az ügynök telepítése Linux gépen:

1. Vigye át a megfelelő csomagot (x86 vagy x64) a Linux-számítógép scp/sftp használatával.
2. A csomag telepítéséhez használja a--telepítés argumentum.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése
1. A függőségi ügynök telepíthető a Windows-számítógép, kattintson duplán a telepítőfájlra, és kövesse a varázsló utasításait.
2. A függőségi ügynök telepíthető egy Linux-számítógép, telepítse a legfelső szintű a következő parancsot:

    ```sh InstallDependencyAgent-Linux64.bin```

[További](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) a függőségi ügynök által támogatott operációs rendszerekkel kapcsolatban. 

## <a name="create-a-group"></a>Hozzon létre egy csoportot

1. Miután telepítette az ügynököt, nyissa meg a portálon, majd kattintson **kezelése** > **gépek**.
2. A **függőségek** oszlop most meg kell jelennie **függőségeinek megtekintése**. Kattintson az oszlop függőségeinek megtekintése.
3. Minden gép ellenőrizheti:
    - Hogy a MMA és a függőségi ügynök telepítve van, és hogy a gép visszaélésre derült fény.
    - A vendég operációs rendszer fut a gépen.
    - Bejövő és kimenő IP-kapcsolatokat és a portok.
    - A gépeken futó folyamatok.
    - Gépek közötti függőségek.

4. Részletesebb függőségek kattintson az időtartományt módosítható. Alapértelmezés szerint a tartománya egy óra. Módosítsa az időtartományt, vagy adja meg a kezdő és záró dátumát, és időtartama.
5. Miután függő csoportba a kívánt gépek állapította meg, válassza ki azokat a térképen gépeket, majd kattintson **gépek**.
6. Adjon meg egy felügyeleticsoport-nevet. Győződjön meg arról, hogy a gép észlel Azure áttelepíteni. Ha a felderítési folyamat a helyszíni újra nem futott. Ha szeretné, azonnal futtathatja értékelését.
7. Kattintson a **OK** kell elmentenie a csoportot.

    ![Hozzon létre egy csoportot a gép függőségek](./media/how-to-create-group-machine-dependencies/create-group.png)

## <a name="next-steps"></a>Következő lépések

- [Megtudhatja, hogyan](how-to-create-group-dependencies.md) pontosítsa a csoport függőségek ellenőrzésével
- [További](concepts-assessment-calculation.md) kapcsolatos értékelések kiszámítási módját.
