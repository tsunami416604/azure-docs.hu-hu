---
title: Az Apache Tez felhasználói felület használata a Windows-alapú HDInsight – Azure
description: Ismerje meg, hogyan használhatja az Apache Tez felhasználói felület Windows-alapú HDInsight HDInsight a Tez-feladatok hibakereséséhez.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: ecb613dd2f624b9e0910a0ddcdf931f4b266aa96
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036580"
---
# <a name="use-the-apache-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Az Apache Tez felhasználói felület használata a Windows-alapú HDInsight a Tez-feladatok hibakereséséhez
A Tez felhasználói felület használata a Tez végrehajtómotor Hive-feladatok hibakereséséhez használható. A Tez felhasználói felület megjeleníti a feladat, egy grafikont a csatlakoztatott elemek részletesen az egyes elemeket, és statisztikák és a naplózási információk lekéréséhez.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések szükség egy HDInsight-fürt által használt Windows. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Előfeltételek
* Egy Windows-alapú HDInsight-fürt. Az új fürt létrehozásának lépéseiért lásd: [Windows-alapú HDInsight használatának első lépései](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > Az Apache Tez felhasználói felület a Windows-alapú HDInsight-fürtök 2016. február 8. után létrehozott csak érhető el.
  >
  >
* Egy Windows-alapú távoli asztali ügyfél.

## <a name="understanding-apache-tez"></a>Az Apache Tez ismertetése
Tez egy bővíthető keretrendszer adatfeldolgozás az Apache Hadoop, és nagyobb sebességre képes, mint a hagyományos MapReduce-feldolgozást kínál. Tez engedélyezéséhez többek között az alábbi szöveget egy Hive-lekérdezés részeként:

    set hive.execution.engine=tez;

Tez létrehoz egy irányított aciklikus Graph (DAG), amely leírja a műveletek a feladat a végrehajtás sorrendje. Egyes műveleteket hívják csúcspontokat, és hajtsa végre a teljes feladat egy részét. A tényleges végrehajtása a csúcspont által leírt munka feladat neve, és előfordulhat, hogy legyen elosztva a fürtben több csomóponton.

### <a name="understanding-the-tez-ui"></a>A Tez felhasználói felület ismertetése
A Tez felhasználói felület egy weblap információt nyújt az Tez használó folyamatok. Felajánlhatja, hogy a következő esetekben hasznos információkat:

* Figyelés hosszú ideig futó folyamatok, a térkép állapotának megtekintése és csökkentési feladatokat.
* Megtudhatja, hogyan lehet javítani, feldolgozás vagy miért volt sikertelen sikeres vagy sikertelen folyamatok előzményadatok elemzése.

## <a name="generate-a-dag"></a>Hozzon létre egy DAG
A Tez felhasználói felület adatokat tartalmaz, ha egy feladatot, használja a Tez-motor fut, vagy lett korábban futtatott. Egyszerű Hive-lekérdezések általában feloldható Tez használata nélkül. Összetettebb lekérdezéseket, amelyeket a szűrési, csoportosítás, rendezése, összekapcsolásokat és az egyéb Tez van szükség.

Az alábbi lépések segítségével által használt Tez Hive-lekérdezést futtathat.

1. A böngészőben navigáljon a https://CLUSTERNAME.azurehdinsight.net, ahol **CLUSTERNAME** a HDInsight-fürt neve.
2. Az oldal tetején lévő menüben válassza a **Hive szerkesztő**. Ez megjeleníti a következő példalekérdezés tartalmazó oldalt.

        Select * from hivesampletable

    A példa lekérdezés törléséhez, és cserélje le a következő.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Válassza ki a **küldés** gombra. A **feladat munkamenet** a lap alján található szakasz a lekérdezés állapotát jeleníti meg. Miután állapotra vált, **befejezve**, jelölje be a **részleteinek megtekintése** hivatkozásra kattintva az eredményeket. A **Feladatkimenet** az alábbihoz hasonló lesz:

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>A Tez felhasználói felület használata
> [!NOTE]
> A Tez felhasználói felület csak akkor használható a fürt átjárócsomópontjaiból asztaláról, így az átjárócsomópontokat kapcsolódni a távoli asztal kell használni.
>
>

1. Az a [az Azure portal](https://portal.azure.com), válassza ki a HDInsight-fürtöt. A HDInsight panel tetején válassza a **a távoli asztal** ikonra. Ez a hivatkozás a távoli asztali panel jeleníti meg.

    ![Távoli asztali ikonra](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. A távoli asztal panelről válassza ki a **Connect** , a fürt átjárócsomópontjához csatlakozik. Amikor a rendszer kéri, használja a fürt távoli asztali felhasználónév és jelszó a kapcsolat hitelesítéséhez.

    ![Távoli asztali kapcsolat ikon](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Ha nem engedélyezte a távoli asztali kapcsolat, adjon meg egy felhasználónevet, jelszót és lejárati dátum, majd válassza **engedélyezése** távoli asztal engedélyezése. Ha ez engedélyezve van, használja az előző lépésekben való csatlakozáshoz.
   >
   >
3. A csatlakozás után nyissa meg az Internet Explorer, a távoli asztalon, kattintson a fogaskerék ikonra a jobb felső sarkában a böngészőben, és válassza **kompatibilitási nézet beállításai**.
4. Alján **kompatibilitási nézet beállításai**, törölje a jelet a jelölőnégyzetből a **kompatibilitási nézetet megjeleníteni az intranetes helyek** és **használja a Microsoft kompatibilitási lista**, és Válassza ki **Bezárás**.
5. Az Internet Explorerben lépjen http://headnodehost:8188/tezui/#/. Ez megjeleníti a Tez felhasználói felület

    ![Tez felhasználói felület](./media/hdinsight-debug-tez-ui/tezui.png)

    A Tez felhasználói felület betöltésekor láthatja az adatbázis-elérhetőségi csoportot, amely jelenleg fut, vagy listáját a fürtön futottak. Az alapértelmezett nézet tartalmazza a DAG nevét, azonosítóját, küldője, állapota, Kezdés időpontja, befejezési időpontja, időtartama, Alkalmazásazonosítót, és várólista. További oszlopok is hozzáadhatók a használatával a fogaskerék ikonra az oldal jobb oldalán.

    Ha csak egy bejegyzést, akkor a lekérdezéshez, amely az előző szakaszban futtatta. Ha több bejegyzés, is keresés alapján keresési feltételek megadása a mezőket a fenti adatbázis-elérhetőségi csoportot, majd nyomja le **Enter**.
6. Válassza ki a **Dag neve** a legutóbbi DAG-bejegyzést. Ez a hivatkozás a DAG kapcsolatos információkat, valamint arra, hogy töltse le a zip, a DAG információkat tartalmazó JSON-fájlokat jeleníti meg.

    ![DAG-részletek](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Fent a **DAG részletek** több hivatkozás a DAG információ megjelenítéséhez használható.

   * **DAG-számlálók** a DAG teljesítményszámlálók adatait jeleníti meg.
   * **Grafikus nézetet** a DAG grafikus ábrázolását jeleníti meg.
   * **Az összes csúcsot** a a DAG a csúcspontok listáját jeleníti meg.
   * **Minden feladat** a DAG az összes csúcspontok a tevékenységek listáját jeleníti meg.
   * **Az összes TaskAttempts** a feladatok futtatása a DAG megpróbálja információit jeleníti meg.

     > [!NOTE]
     > Oszlop jelenítse meg a csúcspontok, feladatok és TaskAttempts görgetve láthatja, hogy nincsenek-e megtekintésére szolgáló hivatkozások **számlálók** és **megtekintése és letöltése a naplók** minden egyes sorára.
     >
     >

     Hiba történt a feladat meghiúsul, ha a DAG részletek állapota sikertelen, és a sikertelen feladat kapcsolatos információkra mutató hivatkozásokat. Diagnosztikai adatokat a rendszer a DAG Részletek alatt jelennek meg.
8. Válassza ki **grafikus nézetet**. Ez megjeleníti a DAG grafikus ábrázolását. Az egér, információit jeleníti meg a nézetben minden csúcspont keresztül helyezheti.

    ![Grafikus nézetet](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Kattintson a csúcspont betölti a **csúcspont részletek** , a cikk. Kattintson a **térkép 1** csúcspont ezen elem részleteinek megjelenítéséhez. Válassza ki **megerősítése** a navigációs megerősítéséhez.

    ![Csomópont részletei](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Vegye figyelembe, hogy most már rendelkezik az oldal tetején lévő hivatkozások csúcspontok és-feladatokhoz kapcsolódó.

    > [!NOTE]
    > Is is érkezik erre a lapra visszatérve által **DAG részletei**és **csúcspont részletei**, és válassza a **térkép 1** csúcspont.
    >
    >

    * **Csúcspont számlálók** a csúcspont számláló adatait jeleníti meg.
    * **Feladatok** a csúcspont számára feladatokat jeleníti meg.
    * **Tevékenység-kísérletek** tett kísérleteket a csúcspont feladatok információit jeleníti meg.
    * **Adatforrások és a fogadók** adatforrásokat jeleníti meg, és a fogadók a csúcspont számára.

      > [!NOTE]
      > Mint az előző menüben görgetve oszlop jelenítse meg a feladatok, feladat kísérletek, és források & Sinks__ az egyes elemekhez további információk megjelenítéséhez.
      >
      >
11. Válassza ki **feladatok**, majd válassza a nevű elem **00_000000**. Ez a hivatkozás megjeleníti a **feladat részletei** erre a célra. Ezen a képernyőn megtekintheti **feladat számlálók** és **feladat kísérletek**.

    ![Feladat részletei](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>További lépések
Most, hogy megtanulhatta, hogyan használható a Tez nézet, tudjon meg többet [a HDInsight használatával Hive](hadoop/hdinsight-use-hive.md).

Részletes technikai információ a Tez, tekintse meg a [Tez lapot a Hortonworks](http://hortonworks.com/hadoop/tez/).
