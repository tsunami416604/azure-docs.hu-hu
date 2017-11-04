---
title: "Tez felhasználói felület használata a Windows-alapú HDInsight - Azure |} Microsoft Docs"
description: "Útmutató a Tez felhasználói felület használata a Windows-alapú HDInsight HDInsight-on Tez feladatokhoz."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a55bccb9-7c32-4ff2-b654-213a2354bd5c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 32f6a12544c05dbf4ac65dd386cd9dea18ca79b3
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>A Tez felhasználói felület használata a Windows-alapú HDInsight-on Tez feladatokhoz
A Tez felhasználói felület egy olyan weblap, megértéséhez, valamint a Tez használják a Windows-alapú HDInsight-fürtök-végrehajtó motor feladatok debug használható. A Tez felhasználói felület lehetővé teszi a feladathoz egy grafikonon csatlakoztatott elemek megjelenítése, egyes elemek elemezze, és statisztika és naplózási információk lekérdezéséhez.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy HDInsight-fürt által használt Windows igényelnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Előfeltételek
* Egy Windows-alapú HDInsight-fürtöt. Az új fürt létrehozásának lépései: [Ismerkedés a Windows-alapú HDInsight eszközzel](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > A Tez felhasználói felületén a 2016. február 8. után létrehozott Windows-alapú HDInsight-fürtök csak érhető el.
  >
  >
* A Windows-alapú távoli asztali ügyfél.

## <a name="understanding-tez"></a>Tez ismertetése
Tez egy bővíthető keretrendszer, amely a hagyományos MapReduce feldolgozási-nál nagyobb sebesség biztosítja az adatok feldolgozásához. A Windows-alapú HDInsight-fürtök egy nem kötelező motor, amely a Hive-lekérdezés részeként a következő paranccsal engedélyezheti a Hive esetén:

    set hive.execution.engine=tez;

Amikor munkahelyi Tez, létrehoz egy irányított aciklikus diagramhoz (DAG), amely leírja a feladat által igényelt művelet végrehajtási sorrendjét. Egyes műveletek csúcsban hívják, és egy adat, a teljes feladat végrehajtása. A munkahelyi csúcspont szerint tényleges végrehajtása feladat neve, és előfordulhat, hogy a fürt több csomópontja legyen elosztva.

### <a name="understanding-the-tez-ui"></a>A Tez felhasználói felület ismertetése
A Tez felhasználói felület egy weblap nyújt információkat folyamatok futó, és rendelkezik a korábban futtatott Tez használatával. Lehetővé teszi a Tez, által generált DAG például a feladatok és a csúcsban és a hibainformációk által használt memória hogyan oszlik más fürtökre, teljesítményszámlálók. Felajánlhatja, hogy a következő esetekben hasznos információkat:

* Figyelési hosszan futó dolgozza fel a térkép állapotának megtekintése, és a feladatokat.
* Megtudhatja, hogyan lehet javítani, feldolgozás, vagy sikertelenségének sikeres vagy sikertelen folyamatok előzményadatainak elemzése.

## <a name="generate-a-dag"></a>Egy dag-csoport létrehozása
A Tez felhasználói felület csak fog adatokat tartalmazni, ha egy feladatot használ a Tez motor éppen fut, vagy már futott az elmúlt. Egyszerű Hive-lekérdezések általában feloldható Tez, azonban összetett lekérdezések, amelyek szűrés, csoportosítás, rendezés, illesztéseket, stb. általában szükséges Tez használata nélkül.

A következő lépésekkel futtathat Hive-lekérdezéseket, amely végrehajtja a Tez használatával.

1. Egy böngészőben navigáljon a https://CLUSTERNAME.azurehdinsight.net, ahol **CLUSTERNAME** a HDInsight-fürt neve.
2. Az oldal tetején a menüből válassza ki a **Hive szerkesztő**. Ekkor megjelenik egy oldal, a következő példa lekérdezést.

        Select * from hivesampletable

    A példa lekérdezés törléséhez, és cserélje le a következő.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Válassza ki a **Submit** gombra. A **feladat munkamenet** szakasz a lap alján a lekérdezés állapotát jeleníti meg. Miután állapota **befejezve**, jelölje be a **részleteinek megtekintése** hivatkozásra az eredmények megtekintéséhez. A **Job Output** kell lennie a következőhöz hasonló:

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>A Tez felhasználói felület használata
> [!NOTE]
> A Tez felhasználói felület érhető el csak az asztalról központi fürtcsomópont, ezért az átjárócsomópontokkal csatlakozni kell használnia a távoli asztal.
>
>

1. Az a [Azure-portálon](https://portal.azure.com), válassza ki a HDInsight-fürthöz. A HDInsight panel felső részén válassza ki a **távoli asztal** ikonra. Ez megjeleníti a távoli asztali panel

    ![Távoli asztali ikon](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. A távoli asztal panelen válassza ki **Connect** az átjárócsomóponthoz való kapcsolódáshoz. Amikor a rendszer kéri, használja a fürt távoli asztalhoz tartozó felhasználónév és jelszó a kapcsolat hitelesítéséhez.

    ![Távoli asztali kapcsolat ikon](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Ha nem engedélyezte a távoli asztali kapcsolat, adjon meg egy felhasználónevet, jelszót és a lejárati dátumot, majd válasszon **engedélyezése** távoli asztal engedélyezése. Amennyiben engedélyezve van, használja az előző lépésekben való csatlakozáshoz.
   >
   >
3. A csatlakozás után nyissa meg az Internet Explorer, a távoli asztalon, jelölje ki a fogaskerék ikonra a képernyő jobb felső sarkában a böngészőt, és válassza **kompatibilitási nézet beállításai**.
4. Alján **kompatibilitási nézet beállításai**, törölje a jelölést az **kompatibilitási nézetben jeleníti meg az intranetes helyek** és **használata Microsoft kompatibilitási lista**, majd válassza ki **Bezárás**.
5. Az Internet Explorer programban navigáljon a http://headnodehost:8188/tezui / #/. Ez megjeleníti a Tez felhasználói felület

    ![Tez felhasználói felület](./media/hdinsight-debug-tez-ui/tezui.png)

    A Tez felhasználói felület betöltésekor látni fogja, amely jelenleg fut, vagy dag listáját a fürtön futottak. Az alapértelmezett nézet tartalmazza a Dag nevét, azonosítója, küldő, állapot, Kezdés időpontja, befejezési idő, időtartam, Alkalmazásazonosító, és várólista. További oszlopok is hozzáadhatók a fogaskerék ikonra a lap jobb használatával.

    Ha csak egy bejegyzést, az előző szakaszban futtatott lekérdezés lesz. Ha több bejegyzés, továbbra is beírva a keresési feltételek mezőiben adatbázis-elérhetőségi csoportot, majd kattintson a **Enter**.
6. Válassza ki a **Dag neve** a legutóbbi DAG-bejegyzést. Ez megjeleníti a DAG kapcsolatos információkat, valamint egy zip a DAG információkat tartalmazó JSON-fájlok letöltése beállítás.

    ![DAG-részletek](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Fent a **DAG részletek** több kapcsolat, az adatbázis-elérhetőségi csoport kapcsolatos információk megjelenítéséhez használható.

   * **DAG-számlálók** a DAG számlálók adatait jeleníti meg.
   * **Grafikus nézetének** jeleníti meg az adatbázis-elérhetőségi csoport grafikus ábrázolása.
   * **Minden csúcsban** a DAG a csúcsban listáját jeleníti meg.
   * **Minden feladat** az adatbázis-elérhetőségi csoport az összes csúcsban feladatok listáját jeleníti meg.
   * **Minden TaskAttempts** a feladatok futtatása a DAG megpróbálja információit jeleníti meg.

     > [!NOTE]
     > Ha az oszlop megjelenítési csúcsban, feladatok és TaskAttempts, figyelje meg, hogy vannak-e hivatkozásra kattintva **számlálók** és **megtekintése vagy naplók letöltéséhez** minden egyes sorára.
     >
     >

     Hiba történt a feladathoz, ha a DAG részleteit és a meghiúsult feladat kapcsolatos információkra mutató hivatkozásokat sikertelen, az állapot jelenik meg. Diagnosztikai adatok az adatbázis-elérhetőségi csoport részletek alatt jelenik meg.
8. Válassza ki **grafikus nézetének**. Ez megjeleníti a DAG grafikus ábrázolása. Az egér elhelyezheti az információt szeretne megjeleníteni a nézetben minden csomópont alatt.

    ![Grafikus megtekintése](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Kattintson a csúcspont betölti a **csúcspont részletek** , a cikk. Kattintson a **térkép 1** csúcspont ezt az elemet a részletek megjelenítéséhez. Válassza ki **megerősítése** a navigációs megerősítéséhez.

    ![Csomópont részletei](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Vegye figyelembe, hogy most már rendelkezik csúcsban és-feladatokhoz kapcsolódó hivatkozások az oldal tetején.

    > [!NOTE]
    > Is is megérkezik a ezen a lapon térhet vissza a **DAG részletek**, kiválasztásával **csúcspont részletek**, és jelölje be a **térkép 1** csúcspont.
    >
    >

    * **Csúcspont számlálók** a csúcspont számláló adatait jeleníti meg.
    * **Feladatok** a csúcspont feladatokat jeleníti meg.
    * **Feladat kísérletek** kísérletek a csúcspont feladatok futtatásához információit jeleníti meg.
    * **Adatforrások & fogadók esetében** adatforrások jeleníti meg, és ez a csomópont a fogadók esetében.

      > [!NOTE]
      > Mint az előző menüvel görgetve az oszlop megjelenítési feladatok, feladat kísérletet, és a források & a Sinks__ további információt az egyes elemekhez tartozó mutató hivatkozások megjelenítése.
      >
      >
11. Válassza ki **feladatok**, és válassza ki a nevű elem **00_000000**. Ez megjeleníti **feladat részletei** ehhez a feladathoz. Ez a képernyő megtekintheti **feladat számlálók** és **feladat kísérletek**.

    ![Feladat részletei](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Következő lépések
Most, hogy rendelkezik megtudta, hogyan használja a Tez, további információ [használata a HDInsight Hive](hadoop/hdinsight-use-hive.md).

Részletesebb műszaki információkat Tez, lásd: a [Hortonworks Tez lapon](http://hortonworks.com/hadoop/tez/).
