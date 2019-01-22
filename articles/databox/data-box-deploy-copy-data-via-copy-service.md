---
title: Adatok másolása az SMB-n keresztül a Microsoft Azure Data Box |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure Data Box adatok másolása szolgáltatáson keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: alkohli
ms.openlocfilehash: a71635abd036bb89546dd3421af97cd9b88f4327
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440199"
---
# <a name="tutorial-use-data-copy-service-to-directly-ingest-data-into-azure-data-box-preview"></a>Oktatóanyag: Adatok másolása szolgáltatás segítségével közvetlenül betölteni az adatokat az Azure Data Box (előzetes verzió)

Ez az oktatóanyag leírja, hogyan gyűjthet adatokat, az adatok másolása szolgáltatás nincs szükség egy köztes gazdagép használatával. Az adatszolgáltatás másolási helyileg futtatja a Data Box, az SMB-n keresztül NAS-eszköz csatlakozik, és másolja az adatokat a Data Box.

Adatok másolása használatára:

- A hálózati tárolóeszközök (NAS) környezetekben, ahol a közvetítő gazdagépek nem érhető el.
- A kisméretű fájlok, amelyek adatfeldolgozást és az adatok feltöltése a hetet igénybe vehet. Ez a szolgáltatás jelentős mértékben javítja az Adatbetöltési és -feltöltést idő.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatok másolása a Data Boxra
> * Data Box szállításának előkészítése

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Ön teljesítette a [oktatóanyag: Állítsa be az Azure Data Box](data-box-deploy-set-up.md).
2. A Data Box kapott, és a rendelés állapota a portálon **kézbesítések**.
3. A hitelesítő adatokat a forrás NAS-eszköz csatlakozik az adatok másolása rendelkezik.
4. Nagy sebességű hálózat csatlakozik. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. 10-GbE kapcsolatot nem érhető el, ha 1-GbE adatok hivatkozás használata, de a másolási sebességek érinti.

## <a name="copy-data-to-data-box"></a>Adatok másolása a Data Boxra

Miután csatlakozott a NAS-nak, a következő lépés az adatok másolása. Mielőtt elkezdené az adatok másolását, tekintse át az alábbiakat:

- Adatok másolása során győződjön meg arról, hogy megfelel-e az adatok mérete a méretbeli korlátokat ismertetett a [az Azure storage és a Data Box-korlátok](data-box-limits.md).
- Ha a Data Box, a feltöltött adatok Data Box-en kívül más alkalmazás egyidejűleg fel, majd emiatt feltöltési feladat hibák és az adatok sérülését.
- Ha az adatokat, az adatok szolgáltatás olvasó folyamatban előforduló, hibák, vagy az adatok sérülése sikerült láthatja.

Adatok másolása szolgáltatás használata az adatok másolásához kell hozzon létre egy feladatot. Hozzon létre egy feladatot, amely az adatok másolása az alábbi lépéseket követve.

1. A helyi webes felületén a Data Box, lépjen a **kezelés > adatmásolás**.
2. Az a **adatmásolás** kattintson **létrehozás**.

    ![Kattintson a ** létrehozás ** az ** adatok ** oldal](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. Az a **beállítása és elindítása** párbeszédpanelen adja meg az alábbi ráfordítások.
    
    |Mező                          |Érték    |
    |-------------------------------|---------|
    |Feladat neve                       |Egy egyedi nevet a kevesebb mint 230 karaktereket a feladathoz. Ezeket a karaktereket a feladat neve – nem engedélyezett \<, \>, \|, \?, \*, \\, \:, \/, és \\\.         |
    |Forráshely                |Adja meg az SMB elérési utat az adatforráshoz, a következő formátumban: `\\<ServerIPAddress>\<ShareName>` vagy `\\<ServerName>\<ShareName>`.        |
    |Felhasználónév                       |A felhasználónév az adatforrás eléréséhez.        |
    |Jelszó                       |A jelszó az adatforrás eléréséhez.           |
    |Céloldali tárfiók    |Válassza ki a céloldali tárfiók feltölteni az adatokat, a legördülő listából.         |
    |Cél tárolási típus       |Válassza ki a cél tárolási típust blokkblob, lapblob vagy az Azure Files.        |
    |Céltároló vagy -megosztás    |Adja meg a tároló nevét, vagy ossza feltölteni az adatokat a rendeltetési tárfiókja. A név lehet a megosztási nevet vagy a tároló nevét. Ha például `myshare` vagy `mycontainer`. A következő formátumban is megadhat `sharename\directory_name` vagy `containername\virtual_directory_name` a felhőben.        |
    |A mintával egyező fájlok másolása    | Adja meg a fájl nevét a tartományegyeztetési minta. a következő két módon.<ul><li>**Helyettesítő kifejezések használata** csak `*` és `?` helyettesítő karakteres kifejezés támogatottak. Ha például a kifejezés `*.vhd` megegyezik a .vhd kiterjesztésű fájlokat. Ehhez hasonlóan `*.dl?` megfelel az összes fájl, amelynek futtatására szolgáló bővítmény pedig akár `.dl` vagy `.dll`. Ezenkívül `*foo` egyezését az összes fájl nevében végződhet `foo`.<br>Közvetlenül a mezőben adhat meg helyettesítő karakteres kifejezést. Alapértelmezés szerint a mezőbe beírt értéket számít helyettesítő karakteres kifejezést.</li><li>**Reguláris kifejezések használata** -POSIX-alapú reguláris kifejezések használata támogatott. Ha például egy reguláris kifejezést `.*\.vhd` egyezni fog a fájlokat, amelyek `.vhd` bővítmény. Reguláris kifejezés, adja meg a `<pattern>` közvetlenül `regex(<pattern>)`. <li>További információk a reguláris kifejezések, [reguláris kifejezés language – rövid](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |Fájlok optimalizálása              |Ha engedélyezve van, a fájlokat, a betöltés vannak csomagolt. Ez felgyorsítja a kisméretű fájlok esetében az adatok másolását.        |
 
4. Kattintson az **Indítás**gombra. A rendszer érvényesíti a bemeneti adatok, és ha az érvényesítés sikeres, majd elindul egy feladat. Igénybe vehet néhány percet, amíg a feladat elindításához.

    ![Egy feladat elindítása a konfigurálás feladatból, és indítsa el a párbeszédpanel](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Létrejön egy feladat a megadott beállításokkal. Jelölje be a jelölőnégyzetet és majd szüneteltetése és folytatása, megszakíthatja, vagy indítsa újra a feladatot.

    ![A feladat másolatot adatok lapján kezelheti](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Ha ez negatív hatással van a NAS-erőforrásokat az alacsony kínálat csúcsidőben szüneteltetheti a feladatot.

        ![Egy feladat felfüggesztése](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Kikapcsolt csúcsértéke során később folytathatja a feladat óra.

        ![Egy feladat folytatása](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Bármikor megszakíthatja a feladatot.

        ![Feladatok megszakítása](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png) egy megerősítése kötelező, ha egy feladatot megszakítja.

        ![Erősítse meg a feladat törlése](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Ha úgy dönt, hogy megszakítja a feladatot, a már másolt adatokat nem törlődik. A Data Box másolt adatokat törölni, visszaállítani az eszközt.

        ![Eszköz alaphelyzetbe állítása](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Ha megszakítja, vagy egy feladat felfüggesztése, nagy fájlok másolandó maradhat félig másolt. Ezek a fájlok feltöltése ugyanazt az állapotot az Azure-bA. Megszakítása vagy szüneteltetheti, ellenőrzése közben a rendszer megfelelően másolja a fájlokat. Érvényesítheti a fájlokat, tekintse meg az SMB-megosztások, vagy töltse le a AJ fájlt.

    - Ha, például egy hálózati hiba okozhatta egy átmeneti hiba miatt váratlanul meghiúsult újraindíthatja a feladatot. Egy feladat nem indítható újra, ha elérte a állapotot például sikeresen befejeződött, vagy hibákkal fejeződött be. A hibák fájlelnevezésnél vagy a fájl mérete probléma okozhatja. Ezek a hibák jelentkezett, de a feladat nem indítható el, ha befejeződött.

        ![A meghiúsult feladat újraindítása](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Ha hibát tapasztal, és a feladat nem indítható, töltse le a hibanaplókat, és tekintse meg a hibát a naplófájlokban. A probléma kijavítására a fájlok másolása egy új feladatot is létrehozhat. Emellett [másolja a fájlokat az SMB-n keresztül](data-box-deploy-copy-data.md).
    
    - Ebben a kiadásban a feladat nem törölhető.
    
    - Korlátlan számú feladatok létrehozására, de legfeljebb 10 feladatok párhuzamosan is futtatni egy adott időpontban.
    - Ha a fájlok optimalizálása, a kisméretű fájlok vannak csomagolni a betöltési másolási teljesítményének javítása érdekében. Ezekben az esetekben az alábbi képernyőképen látható módon jelenik meg egy csomagolt fájlt (neveként GUID azonosítója).

        ![A példában egy tömörített fájl](media/data-box-deploy-copy-data-via-copy-service/packed-file-on-ingest.png)

6. Amíg a feladat a folyamatban van. a **adatmásolás** oldalon:

    - Az a **állapot** oszlop, a másolási feladat állapotát is megtekintheti. Az állapot a következő lehet:
        - **Fut**
        - **Nem sikerült**
        - **Succeeded**
        - **Pausing**
        - **Paused**
        - **Megszakítása**
        - **Meg lett szakítva**
        - **Hibákkal fejeződött be**
    - Az a **fájlok** oszlopban látható a száma és mérete éppen másolt fájlok száma.
    - Az a **feldolgozott** oszlopban látható a számát és a feldolgozott fájlok méretét.
    - Az a **részletek** oszlopot, kattintson a **nézet** a feladat részleteinek megtekintéséhez.
    - Ha hibákat a másolási folyamat során, ahogyan a **# hibák** oszlop, lépjen a **hibanapló** oszlopra, és a hiba hibakeresési naplók letöltése.

Várjon, amíg befejeződik a másolási feladatokat. Néhány hiba csak bejelentkezett, a **csatlakozás és másolás** lapon, győződjön meg arról, hogy a másolási feladatokat végzett hiba nélkül, mielőtt továbblép a következő lépéssel.

![Nincsenek hibák, a ** csatlakozás és másolás ** lap](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Az adatok integritásának biztosítása érdekében az ellenőrzőösszeg kiszámítására beágyazva, az adatok másolása közben kerül sor. A másolás befejezése után ellenőrizze, hogy mekkora a felhasznált és a szabad tárhely az eszközén.
    
![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

A másolási feladat befejeződése után nyissa meg **szállításra való**.

>[!NOTE]
> Szállításra való nem futhat, amíg a másolási feladat folyamatban van.

## <a name="prepare-to-ship"></a>A szállítás előkészítése

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Adatok másolása a Data Boxra
> * Data Box szállításának előkészítése

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan tehetnek a Data Box elküldje a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box elküldése a Microsoftnak](./data-box-deploy-picked-up.md)

