---
title: A Microsoft Azure StorSimple Data Manager felhasználói felületén |} A Microsoft Docs
description: A StorSimple Data Manager szolgáltatás felhasználói felületének használata
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: fa897b4b77f7f5869eab2ba2e7db9afbd84febfa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121502"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Az Azure Portalon a StorSimple Data Manager szolgáltatás kezelése

Ez a cikk bemutatja, hogyan használhatja a StorSimple Data Manager felhasználói felületén a StorSimple 8000 sorozatú eszközök levő adatok átalakításához. Az átalakított adatok ezután más Azure-szolgáltatások például az Azure Media Services, Azure HDInsight, Azure Machine Learning és az Azure Search képes használni.


## <a name="use-storsimple-data-transformation"></a>A StorSimple Data átalakítást

A StorSimple Data Manager, hogy az erőforrás belül mely adatok átalakítása példányosítása. Az Adatátalakítási szolgáltatás alakíthat át adatokat a StorSimple-formátumból, natív formátumban, a blobok vagy az Azure Files lehetővé teszi. A StorSimple natív formátumban adatok átalakítása, adja meg a részletes adatait a StorSimple 8000 sorozatú eszköz és az Önt érdeklő átalakítása kívánt adatok kell.

### <a name="create-a-storsimple-data-manager-service"></a>A StorSimple Data Manager szolgáltatás létrehozása

A következő lépésekkel hozzon létre egy StorSimple Data Manager szolgáltatást.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a Microsoft-fiók hitelesítő adataival.

2. Kattintson a **+ erőforrás létrehozása** , és keresse a StorSimple Data Manager.

    ![Hozzon létre egy StorSimple Data Manager szolgáltatás 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. A StorSimple Data Manager elemre majd **létrehozás**.
    
    ![Hozzon létre egy StorSimple Data Manager szolgáltatás 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. Az új szolgáltatás adja meg a következőket:

   1. Adjon meg egy egyedi **szolgáltatásnév** a StorSimple Data Manager. Ez a szolgáltatás azonosítására szolgáló rövid név. A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. A névnek betűvel vagy számmal kell kezdődnie és végződnie.

   2. Válasszon egy **előfizetés** a legördülő listából. Az előfizetés az Ön számlázási fiókjához lesz társítva. A mező automatikusan ki van töltve (és nem választható) Ha csak egy előfizetéssel rendelkezik.

   3. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új csoportot. További információk: [Azure-erőforráscsoportok](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

   4. Adja meg a **hely** környezetet tároló, a storage-fiókok és a StorSimple Data Manager szolgáltatás a szolgáltatás. A StorSimple-Eszközkezelő szolgáltatás Data Manager szolgáltatás és a társított storage-fiókban kell lenniük a támogatott régióban.
    
   5. Ez a szolgáltatás hivatkozás beszerzése az irányítópulton, válassza a **rögzítés az irányítópulton**.
    
   6. Kattintson a **Create** (Létrehozás) gombra.

      ![3. a StorSimple Data Manager szolgáltatás létrehozása](./media/storsimple-data-manager-ui/create-service-4.png)

A szolgáltatás létrehozása eltarthat néhány percig. A szolgáltatás sikeres létrehozása, és megjelenik az új szolgáltatás után megjelenik egy értesítés.

### <a name="create-a-data-transformation-job-definition"></a>Adatok átalakítása feladat definíciójának létrehozása

Egy StorSimple Data Manager szolgáltatásban szeretne létrehozni egy adatok átalakítási feladat definíciója. Feladatdefiníció Megadja, hogy az Önt érdeklő áthelyezése a storage-fiókra a natív formátumban, a StorSimple adatok részleteit. Miután létrehozta a feladatdefiníciók, majd a feladat ismét futtathatja a különböző futásidejű beállításokat.

A következő lépésekkel hozzon létre egy feladat definíciója.

1. Keresse meg a létrehozott szolgáltatásra. Lépjen a **felügyeleti > Feladatdefiníciók**.

2. Kattintson a **+ feladat definíciójának**.

    ![Kattintson a kívánt feladat definíciója](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Adjon meg egy nevet a feladatdefiníció. A név 3 és 63 karakter közötti lehet. Neve a kis-és nagybetűket, számokat és kötőjeleket tartalmazhat.

4. Adjon meg egy helyet, ahol a feladat fut-e. Ezen a helyen, mint a hely, ahol a szolgáltatás üzembe helyezése eltérő lehet.

5. Kattintson a **forrás** forrás adattár megadásához.

    ![Forrás adatainak adattár konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Mivel ez egy új Data Manager szolgáltatás, sem az adattárak vannak konfigurálva. A **konfigurálása adatforrás**, adja meg a StorSimple 8000 sorozatú eszköz részleteit és az Önt érdeklő adatok.

   A StorSimple-Eszközkezelő, adattár hozzáadásához kattintson **új hozzáadása** az adatok tárház legördülő listából, majd kattintson a **adattár hozzáadása**.

    ![Új adat-adattár hozzáadása](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Válasszon **a StorSimple 8000 sorozat Manager** a tárház adattípusú értékként.
    
   2. Adjon meg egy rövid nevet a forrás adattár.
    
   3. A legördülő listából válasszon egy előfizetést, a StorSimple-Eszközkezelő szolgáltatással kapcsolatos.
    
   4. Adja meg a nevét, a StorSimple Device Manager számára a **erőforrás**.

   5. Adja meg a **szolgáltatásadat-titkosítási** kulcsfontosságú a StorSimple-Eszközkezelő szolgáltatáshoz. 

      ![Forrás adatainak tárház 1 konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Kattintson a **OK** végeztével. Ez menti az adattárat. A StorSimple-Eszközkezelő más definíciók újból ezeket a paramétereket újbóli megadása nélkül. Kattintás után néhány másodpercet vesz igénybe **OK** a legördülő listában jelenik meg az újonnan létrehozott forrás adattár számára.

7. A legördülő listából a **adattár**, válassza ki a létrehozott adattár. 

   1. Adja meg a StorSimple 8000 sorozatú eszköz, amely tartalmazza az Önt érdeklő adatok nevét.

   2. Adja meg, amely rendelkezik az Önt érdeklő adatok StorSimple eszközön levő kötet nevével.

   3. Az a **szűrő** alszakaszt, adja meg a gyökérkönyvtárban, amely tartalmazza a az Önt érdeklő adatok _\MyRootDirectory\Data_ formátumban. Meghajtó-betűjelek, például _\C:\Data_ nem támogatottak. Bármely fájlszűrők is hozzáadhat.

   4. A data transformation szolgáltatás működik, az adatokat az Azure-ban közzétett, a pillanatképek keresztül. Ez a feladat futtatásakor lehet váltani, készítsen biztonsági mentést, minden alkalommal, amikor a feladat fut (a legfrissebb adatokat dolgozhat) vagy a felhőben használja a legutóbbi meglévő biztonsági mentés (ha dolgozik, az archivált adatokat).

   5. Kattintson az **OK** gombra.

      ![Forrás adatainak tárház 2 konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Ezután a céladatbázisban adatokat kell megadni. Válassza ki az adott fiókban blobokba helyezi a fájlokat a storage-fiókok. A legördülő listában válassza ki a **új hozzáadása** , majd **beállításainak konfigurálása**.

9. Válassza ki a hozzáadni kívánt cél-tárházat és az adattár társítva a többi paraméter típusát.

    Ha a tárolási fiók céljának választja, megadhatja egy rövid nevet, az előfizetés (Válasszon azonos-e a szolgáltatás, vagy más), és a egy storage-fiókot.
        ![Konfigurálja a céladattárban 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    A feladat futtatásakor jön létre egy tárolási sorba. A sorba az átalakított blobokkal kapcsolatos üzenetek kerülnek, amint a blobok elkészültek. A sor neve megegyezik a feladatdefiníció nevével.
    
10. Miután hozzáadta az adattár, várjon néhány percig.
    
    1. Válassza ki a létrehozott adattárat a legördülő listából a célként a **célfiók neve**.

    2. Válassza ki az storage BLOB vagy-fájlokat. Adja meg a nevét, a storage-tároló, ahol az átalakított adatok találhatók. Kattintson az **OK** gombra.

        ![Adatok tárház céltárfiók konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Feladat időtartama a feladat futtatása előtt becslést jelentenek lehetőséget is ellenőrizheti. Kattintson a **OK** a feladatdefiníció létrehozása. A feladat definíciója már befejeződött. Ezt a feladatdefiníciót, a felhasználói felületen keresztül többször is használhatja a különböző futásidejű beállításokat.

    ![Teljes feladat definíciója](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Az újonnan létrehozott feladatdefiníció hozzáadódik ehhez a szolgáltatáshoz a feladatdefiníciók listáját.

### <a name="run-the-job-definition"></a>A feladatfuttatási definíció

Adatok áthelyezése a storage-fiókba, a feladat-definícióban megadott StorSimple van szüksége, akkor kell futtatnia. Futásidőben néhány paraméter eltérően adható meg. A lépések a következők:

1. Válassza ki a StorSimple Data Manager szolgáltatást, és nyissa meg **felügyeleti > Feladatdefiníciók**. Válassza ki, majd kattintson a futtatni kívánt feladat definíciója.
     
     ![Futtatás 1 feladat indítása](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Kattintson a **Futtatás most**.
     
     ![Futtassa a 2 feladat indítása](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Kattintson a **futtatási beállítások** módosíthatja azokat a beállításokat, előfordulhat, hogy módosítani szeretné a feladat futtatásához. Kattintson a **OK** majd **futtatása** a feladat elindításához.

    ![Indítsa el a feladatok futtatásához 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Ez a feladat figyeléséhez nyissa meg **feladatok** a StorSimple Data Manager. A megfigyelési funkciókon túl a **feladatok** panelen is hallgathatja meg a tárolási üzenetsort, amikor egy üzenet bekerül minden alkalommal, amikor egy fájl átkerül a StorSimple-ből a storage-fiókot.

    ![Indítsa el a feladatok futtatásához 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>További lépések

[.NET SDK használatával indítsa el a StorSimple Data Manager-feladatok](storsimple-data-manager-dotnet-jobs.md).