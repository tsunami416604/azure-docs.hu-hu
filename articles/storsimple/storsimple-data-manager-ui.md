---
title: Microsoft Azure StorSimple Data Manager felhasználói felülete
description: A StorSimple Data Manager szolgáltatás felhasználói felületének használata
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 85be49ad88ac62d90235c3da6b89b0da6a11487c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933752"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>A StorSimple Data Manager szolgáltatás kezelése az Azure Portalon

Ez a cikk bemutatja, hogyan használhatja a StorSimple Data Manager felhasználói felületét a StorSimple 8000 sorozatú eszközökön található adatok átalakításához. Az átalakított adatokat ezután más Azure-szolgáltatások, például az Azure Media Services, az Azure HDInsight, az Azure Machine Learning és az Azure Cognitive Search is felhasználhatja.


## <a name="use-storsimple-data-transformation"></a>StorSimple-adatátalakítás használata

A StorSimple-adatkezelő az az erőforrás, amelyen belül az adatok átalakítása példányos. Az adatátalakítási szolgáltatás lehetővé teszi a StorSimple formátumból származó adatok átalakítása natív formátumba blobokban vagy Azure-fájlokban. A StorSimple natív formátumú adatok átalakításához meg kell adnia a StorSimple 8000 sorozatú eszköz részleteit és az átalakítani kívánt adatokat.

### <a name="create-a-storsimple-data-manager-service"></a>StorSimple Adatkezelő szolgáltatás létrehozása

A StorSimple Data Manager szolgáltatás létrehozásához hajtsa végre az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a Microsoft-fiók hitelesítő adataival.

2. Kattintson **a + Erőforrás létrehozása elemre,** és keressen a StorSimple Data Manager kifejezésre.

    ![StorSimple Adatkezelő szolgáltatás létrehozása 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Kattintson a StorSimple-adatkezelő, majd **a Létrehozás gombra.**
    
    ![StorSimple Data Manager szolgáltatás létrehozása 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Az új szolgáltatáshoz adja meg a következőket:

   1. Adjon meg egy egyedi **szolgáltatásnevet** a StorSimple Data Manager számára. Ez a szolgáltatás azonosítására szolgáló rövid név. A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. A névnek betűvel vagy számmal kell kezdődnie és végződnie.

   2. Válasszon **előfizetést** a legördülő listából. Az előfizetés az Ön számlázási fiókjához lesz társítva. Ez a mező automatikusan kitöltődik (és nem választható ki), ha csak egy előfizetéssel rendelkezik.

   3. Jelöljön ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új csoportot. További információk: [Azure-erőforráscsoportok](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

   4. Adja meg a **hely a** szolgáltatás, amely a tárfiókok és a StorSimple Data Manager szolgáltatás. A StorSimple Eszközkezelő szolgáltatás, a Data Manager szolgáltatás és a kapcsolódó tárfiók mind a támogatott régiókban kell lennie.
    
   5. Ha az irányítópulton szeretne hivatkozást kapni erre a szolgáltatásra, válassza **a Rögzítés az irányítópultra**lehetőséget.
    
   6. Kattintson **a Létrehozás gombra.**

      ![StorSimple Data Manager szolgáltatás létrehozása 3](./media/storsimple-data-manager-ui/create-service-4.png)

A szolgáltatás létrehozása eltarthat néhány percig. A szolgáltatás sikeres létrehozása után megjelenik egy értesítés, és megjelenik az új szolgáltatás.

### <a name="create-a-data-transformation-job-definition"></a>Adatátalakítási feladatdefiníció létrehozása

A StorSimple Data Manager szolgáltatáson belül létre kell hoznia egy adatátalakítási feladatdefiníciót. A feladatdefiníció a StorSimple-adatok részleteit adja meg, amelyek ben a natív formátumban szeretne átlépni egy tárfiókba. Miután létrehozott egy feladatdefiníciót, ezt a feladatot újra futtathatja különböző futásidejű beállításokkal.

A feladatdefiníció létrehozásához hajtsa végre az alábbi lépéseket.

1. Nyissa meg a létrehozott szolgáltatást. Nyissa meg **a Felügyeleti > feladatdefiníciókat.**

2. Kattintson **a + Feladatdefiníció gombra.**

    ![Kattintson a +Feladatdefiníció gombra](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Adja meg a feladatdefiníció nevét. A név 3 és 63 karakter között lehet. A név nagy- és kisbetűket, számokat és kötőjeleket tartalmazhat.

4. Adja meg azt a helyet, ahol a feladat fut. Ez a hely eltérhet a szolgáltatás üzembe helyezésének helyétől.

5. Kattintson a **Forrás** elemre a forrásadattár megadásához.

    ![Forrásadat-tártár konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Mivel ez egy új Data Manager-szolgáltatás, nincsenek adattárak konfigurálva. Az **adatforrás konfigurálása**területen adja meg a StorSimple 8000 sorozatú eszköz részleteit és az érdekes adatokat.

   Ha a StorSimple Eszközkezelőt adattárként szeretné hozzáadni, kattintson az **Új hozzáadása** az adattár legördülő menüjében, majd az **Adattár hozzáadása parancsra.**

    ![Új adattár hozzáadása](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Válassza a **StorSimple 8000 sorozatkezelőt** adattártípusként.
    
   2. Adja meg a forrásadattár rövid nevét.
    
   3. A legördülő listából válasszon egy előfizetést a StorSimple Eszközkezelő szolgáltatáshoz társítva.
    
   4. Adja meg az **erőforrás**StorSimple eszközkezelőjének nevét.

   5. Adja meg a StorSimple Eszközkezelő szolgáltatás **szolgáltatásadat-titkosítási** kulcsát. 

      ![1. forrásadat-tártár konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Ha végzett, kattintson **az OK** gombra. Ezzel elmenti az adattárat. Használja fel újra ezt a StorSimple Eszközkezelőt más feladatdefiníciókban anélkül, hogy újra beírna ezeket a paramétereket. Néhány másodpercet vesz igénybe, miután az **újonnan** létrehozott forrásadattár ok gombjára kattint, hogy megjelenjen a legördülő menüben.

7. Az **Adattár**legördülő listájából válassza ki a létrehozott adattárat. 

   1. Adja meg a StorSimple 8000 sorozatú eszköz nevét, amely az érdekes adatokat tartalmazza.

   2. Adja meg a storSimple-eszközön található, az Ön számára fontos adatokat tartalmazó kötet nevét.

   3. A **Szűrő** alszakaszban adja meg a _\MyRootDirectory\Data_ formátumban található fontos adatokat tartalmazó gyökérkönyvtárat. Az ilyen _\C:\Adatok_ meghajtóbetűjelek nem támogatottak. Itt is hozzáadhat fájlszűrőket.

   4. Az adatátalakítási szolgáltatás csak az Azure-ba leküldéses adatok legújabb pillanatképén működik.

   5. Kattintson az **OK** gombra.

      ![2. forrásadat-tártár konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Ezután a céladattárt konfigurálni kell. Válassza ki a tárfiókokat a fájlok blobokba való behelyezéséhez a fiókban. A legördülő menüben válassza az **Új hozzáadása,** majd a **Beállítások megadása lehetőséget.**

9. Válassza ki a hozzáadni kívánt céltárház típusát és a tárházhoz társított egyéb paramétereket.

    Ha a tárfiók típusának cél, megadhatja a rövid nevet, előfizetést (válassza ki a szolgáltatás vagy más), és egy tárfiók.
        ![Céladat-tártár konfigurálása 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    A feladat futtatásakor létrejön egy tárolási várólista. A sorba az átalakított blobokkal kapcsolatos üzenetek kerülnek, amint a blobok elkészültek. A sor neve megegyezik a feladatdefiníció nevével.
    
10. Az adattár hozzáadása után várjon néhány percet.
    
    1. Válassza ki a célként létrehozott tárházat a **Célfiók nevében**lévő legördülő listából.

    2. Válassza ki a tárolási típust blobként vagy fájlként. Adja meg annak a tárolótárolónak a nevét, amelyben az átalakított adatok találhatók. Kattintson az **OK** gombra.

        ![Céladat-tártárfiók konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. A feladat futtatása előtt beis jelölheti a feladat időtartamának becslését. A feladatdefiníció létrehozásához kattintson az **OK** gombra. A feladatdefiníció befejeződött. Ezt a feladatdefiníciót többször is használhatja a felhasználói felületen keresztül, különböző futásidejű beállításokkal.

    ![Teljes feladatdefiníció](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Az újonnan létrehozott feladatdefiníció hozzáadódik a szolgáltatás feladatdefinícióinak listájához.

### <a name="run-the-job-definition"></a>A feladatdefiníció futtatása

Amikor adatokat kell áthelyeznie a StorSimple-ből a feladatdefinícióban megadott tárfiókba, futtatnia kell azt. Futásidőben bizonyos paraméterek másképp is megadhatók. Ennek lépései a következők:

1. Válassza ki a StorSimple Data Manager szolgáltatást, és nyissa meg **a Felügyeleti > feladatdefiníciók at.** Jelölje ki és kattintson a futtatni kívánt feladatdefinícióra.
     
     ![Feladat futtatása 1.](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Kattintson a **Futtatás most gombra.**
     
     ![Feladat futtatása 2.](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Kattintson a **Beállítások futtatása** gombra a feladatfuttatásához esetleg módosítani kívánt beállítások módosításához. Kattintson **az OK,** majd a **Futtatás** gombra a feladat elindításához.

    ![Feladat futtatása 3.](./media/storsimple-data-manager-ui/start-job-run3.png)

4. A feladat figyeléséhez nyissa meg a **Feladatok** a StorSimple Data Manager. A **feladatok** panelfigyelés mellett a tárolási várólistán is figyelheti, ahol egy üzenet kerül minden alkalommal, amikor egy fájl a StorSimple-ről a tárfiókba kerül.

    ![Feladat futtatása 4.](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Naplók megtekintése a feladat befejezése után

A feladat befejezése után megtekintheti a feladat állapotát. A feladat állapota **lehet Sikeres**, **Részben sikeres** és **sikertelen.** Megtekintheti a sikeresen másolt és a nem másolt fájlok listáját. Ezek a listák a céltárfiókon belül **"storsimple-data-manager-joblogs"** nevű tárolóban érhetők el. Ebben a tárolóban kereshet egy mappát, amelynek neve megegyezik a feladatdefiníciójával. Ezen belül egy mappa jön létre minden feladatfuttatáshoz, amely tartalmazza a listákat. Ennek a mappának a neve lesz a feladat GUID azonosítója, amelyet a feladat részletei lapon kaphat meg. Másik lehetőségként a legtöbb esetben megjelenik egy hivatkozás a másolási naplókhoz a feladatok oldalon belül.
Ebben a mappában 2 csv fájl található. Minden fájl, amely **a másolt fájllistával kezdődik,** tartalmazza a sikeresen másolt fájlok listáját. Minden sikertelen **fájllal** kezdődő fájl olyan fájlokat tartalmaz, amelyeket nem lehetett másolni, egy hibaüzenettel együtt.


## <a name="next-steps"></a>További lépések

[A .NET SDK segítségével indítsa el a StorSimple Data Manager-feladatokat.](storsimple-data-manager-dotnet-jobs.md)
