---
title: "Egy értékelési csoport csoport függőségi leképezéssel Azure telepítse át a pontosítsa |} Microsoft Docs"
description: "Ismerteti, hogyan pontosítsa csoport függőségi leképezés használata az Azure áttelepítése szolgáltatásban értékelését."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/22/2017
ms.author: raynew
ms.openlocfilehash: 3b10765894501791004e3a9221363f196cc0c91d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Pontosítsa a csoportot a függőségi leképezése

Ez a cikk ismerteti a csoport finomíthatja a csoport összes gép Függőségek megjelenítése. Általában ezt a módszert helyzet egy meglévő csoport tagsági pontosítás ellenőrzése függőségek, felmérés elvégzéséhez futtatása előtt. Egy csoportot a függőségi képi megjelenítés finomítása hatékony tervezéséhez az áttelepítés Azure.You súgó is felderíthetők az egymástól kölcsönösen függnek rendszerek át kell térnie együtt. Ennek segítségével győződjön meg arról, hogy semmi sem hátrahagyott és jelzés nélküli valamilyen okból kimaradás lép fel, ha telepít át az Azure-bA. 


> [!NOTE]
> Csoportokat, amelyekhez Függőségek megjelenítése nem tartalmazhat több mint 10 gépek. Ha több mint 10 gépet a csoportban található, azt javasoljuk, hogy ossza ki a függőségi képi megjelenítés funkció kisebb csoportokat.


# <a name="prepare-the-group-for-dependency-visualization"></a>A csoport függőségi a képi megjelenítéshez tartozó előkészítése
Egy csoport függőségeinek megtekintése, meg kell töltse le és telepítse az ügynököt minden a helyszíni gépen, amely a csoport tagja. Emellett, ha internetkapcsolat nélküli gépek, kell letöltéséhez és telepítéséhez [OMS átjáró](../log-analytics/log-analytics-oms-gateway.md) rajtuk.

### <a name="download-and-install-the-vm-agents"></a>Töltse le és telepítse a virtuális gép ügynökök
1. A **áttekintése**, kattintson a **kezelése** > **csoportok**, keresse fel a szükséges csoportot.
2. Gépek, a listában a a **függőségi ügynök** oszlopban kattintson **telepítése szükséges** letöltése és telepítése az ügynökök vonatkozó lépéseit.
3. Az a **függőségek** lapon töltse le és telepítse a Microsoft Monitoring Agent (MMA), és a függőségi ügynök a csoport részét képező virtuális gépek.
4. Másolja a munkaterület azonosítója és kulcsa. Szükség van ezekre a MMA telepítésekor a helyszíni gépeket.

### <a name="install-the-mma"></a>Telepítse a MMA

Az ügynök telepítése Windows-gépen:

1. Kattintson duplán a letöltött ügynök.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra. Az a **licencfeltételeket** kattintson **elfogadom** elfogadja a licencfeltételeket.
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

## <a name="refine-the-group-based-on-dependency-visualization"></a>A csoportok függőségi képi megjelenítés alapján finomítható
Miután a csoport minden számítógépen telepített ügynökök, a függőségeket, a csoport ábrázolhatja és pontosítás a következő az alábbi lépéseket.

1. Az Azure-ban áttelepítése projekt, a **kezelése**, kattintson a **csoportok**, és válassza ki azt a csoportot.
2. A csoport lapján, kattintson a **függőségeinek megtekintése**kattintva nyissa meg a csoport kapcsolatfüggőségi térképének megjelenítéséhez.
3. A függőségi térkép a csoport a következő részleteit jeleníti meg:
    - Bejövő (ügyfelek) és a csoport részét képező összes gép onnan kimenő (kiszolgálók) TCP-kapcsolatok
        - A függő gépek, amelyeken nincs telepítve az MMA és függőségi ügynök által használt portszámok vannak csoportosítva.
        - Külön mezőkbe jelennek meg a dependenct gépeken, amelyek a MMA és a függőségi ügynök van telepítve 
    - A gépen futó folyamatok, minden gép mezőben folyamatokat bővítheti
    - Minden számítógépen, ha azt szeretné, hogy a részletek megjelenítéséhez kattintson például a teljesen minősített tartománynevét, operációs rendszer, az egyes gépek MAC-cím stb tulajdonságok,

     ![Csoport függőségeinek megtekintése](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Részletesebb függőségek megtekintéséhez kattintson az időtartományt módosítható. Alapértelmezés szerint a tartománya egy óra. Módosítsa az időtartományt, vagy adja meg a kezdő és záró dátumát, és időtartama.
4. Ellenőrizze a függő gépek, a folyamat minden gépen futó, és azonosíthatja a gépek kell hozzáadni vagy eltávolítani a csoportból.
5. Ctrl + kattintás segítségével válassza ki a gépeket a térképen hozzáadása vagy eltávolítása a csoportból.
    - Csak a felderített gépeket adhat hozzá.
    - Hozzáadása és a gép eltávolítása egy csoportból érvényteleníti az értékelések részhez.
    - Egy új assessment másik lehetőségként létrehozhatja, ha a csoport módosítása.
5. Kattintson a **OK** kell elmentenie a csoportot.

    ![Hozzáadni vagy eltávolítani a gépek](./media/how-to-create-group-dependencies/add-remove.png)

Ha egy adott gépen futó, a csoport függőségi térkép függőségeit ellenőrizni kívánja [gép függőségi megfeleltetés beállítása](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>További lépések

[További](concepts-assessment-calculation.md) kapcsolatos értékelések kiszámítási módját.
