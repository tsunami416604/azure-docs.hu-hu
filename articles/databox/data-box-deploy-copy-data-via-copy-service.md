---
title: 'Oktatóanyag: Adatok másolása az adatok másolása szolgáltatáson keresztül a Microsoft Azure Data Box-eszközre |} A Microsoft Docs'
description: Ebben az oktatóanyagban megismerheti, hogyan másolhat adatokat az Azure Data Box-eszközre, az adatok másolása szolgáltatáson keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: alkohli
ms.openlocfilehash: 3f76721129906b57a05e597aade9f2febb609968
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343527"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>Oktatóanyag: Adatok másolása az Azure Data Box (előzetes verzió) az adatok másolása használatára

Ebben az oktatóanyagban a data az adatok másolása szolgáltatással egy köztes gazdagép anélkül, hogy ismerteti. Az adatok szolgáltatás helyben fut a Microsoft Azure Data Box, a hálózati tárolóeszközök (NAS) eszközt, az SMB-n keresztül csatlakozik, és adatokat másol a Data Box.

Az adatok másolása használatára:

- NAS környezetekben, ahol köztes állomások esetleg nem érhetők el.
- A kisméretű fájlok, amelyek adatfeldolgozást és az adatok feltöltése a hetet igénybe vehet. Az adatok szolgáltatás jelentős mértékben javítja a kisméretű fájlok adatfeldolgozást és a feltöltés ideje.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatok másolása a Data Boxra

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Ez az oktatóanyag befejezése: [Állítsa be az Azure Data Box](data-box-deploy-set-up.md).
2. A Data Box-eszköz kapott, és a rendelés állapota a portálon **kézbesítések**.
3. A hitelesítő adatokat a forrás NAS-eszköz, amely csatlakozni fog az adatok másolása rendelkezik.
4. Nagy sebességű hálózat csatlakozik. Határozottan javasoljuk, hogy legalább egy 10 gigabites Ethernet (GbE) kapcsolatot. 10-GbE kapcsolatot nem érhető el, ha egy 1-GbE adatkapcsolat is használhat, de a másolási sebesség hatással lesz.

## <a name="copy-data-to-data-box"></a>Adatok másolása a Data Boxra

Miután csatlakozott a NAS-eszköz, a következő lépés az adatok másolása az. Mielőtt elkezdené az adatok másolását, tekintse át az alábbiakat:

- Adatok másolása során győződjön meg arról, hogy megfelel-e az adatok mérete a méretbeli korlátokat a cikkben ismertetett [az Azure storage és a Data Box-korlátok](data-box-limits.md).
- Data Box által feltöltött adatok Data Box kívül más alkalmazás egyidejűleg fel, ha sikertelen feltöltés – feladatok és adatsérülést okozhat.
- Ha az adatok módosulnak folyamatban van, az adatok szolgáltatás olvasó, hibák, vagy az adatok sérülése merülhetnek fel.

Adatok másolása az adatok másolása használatával, meg kell hozzon létre egy feladatot:

1. A helyi webes felhasználói felületen, a Data Box-eszköz, lépjen a **kezelés** > **adatmásolás**.
2. Az a **adatmásolás** lapon jelölje be **létrehozás**.

    ![Az "Adatok másolása" oldalon válassza ki a létrehozás](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. Az a **konfigurálása feladat és a kezdési** párbeszédpanelen töltse ki a következő mezőket:
    
    |Mező                          |Érték    |
    |-------------------------------|---------|
    |**Feladat neve**                       |Egy egyedi nevet a kevesebb mint 230 karaktereket a feladathoz. Ezeket a karaktereket a feladat neve nem engedélyezett: \<, \>, \|, \?, \*, \\, \:, \/, és \\\.         |
    |**Forrás helye**                |Adja meg az SMB elérési utat az adatforráshoz, a következő formátumban: `\\<ServerIPAddress>\<ShareName>` vagy `\\<ServerName>\<ShareName>`.        |
    |**Felhasználónév**                       |A felhasználónév `\\<DomainName><UserName>` formátum az adatforrás eléréséhez.        |
    |**Jelszó**                       |A jelszó az adatforrás eléréséhez.           |
    |**Cél tárfiók**    |Válassza ki a listából az adatok feltöltése a célként megadott tárfiók.         |
    |**Cél típusa**       |A listából válassza ki a cél tárolási típusa: **Blokkblob**, **Lapblob**, vagy **az Azure Files**.        |
    |**Cél tároló és megosztási**    |Adja meg a tároló nevét, vagy megoszthatja, hogy szeretné-e a cél tárfiók az adatok feltöltése. A név lehet a megosztási nevet vagy a tároló nevét. Például `myshare` vagy `mycontainer`. A nevét adja meg a következő formátumban `sharename\directory_name` vagy `containername\virtual_directory_name`.        |
    |**Fájlok másolása a tartományegyeztetési minta.**    | A fájlnév egyeztetési minta az alábbi két módon adhat meg:<ul><li>**Helyettesítő kifejezések használata:** Csak `*` és `?` helyettesítő karakteres kifejezés támogatottak. Ha például a kifejezés `*.vhd` megegyezik a fájlokat, amelyek a `.vhd` bővítmény. Ehhez hasonlóan `*.dl?` megegyezik a bővítménnyel a fájlok `.dl` vagy kezdődő `.dl`, mint például `.dll`. Hasonlóképpen `*foo` megfelel az összes fájl nevében végződhet `foo`.<br>Közvetlenül a mezőben adja meg a helyettesítő karakteres kifejezést. Alapértelmezés szerint a mezőbe írt számít helyettesítő karakteres kifejezést.</li><li>**Reguláris kifejezések használata:** A POSIX-alapú reguláris kifejezések használata támogatott. Ha például a reguláris kifejezés `.*\.vhd` egyezni fog a fájlokat, amelyek rendelkeznek a `.vhd` bővítmény. Reguláris kifejezések, adja meg a `<pattern>` közvetlenül `regex(<pattern>)`. Reguláris kifejezésekkel kapcsolatos további információkért látogasson el [reguláris kifejezés language – rövid](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Fájlok optimalizálása**              |Ha ez a funkció engedélyezve van, 1 MB-nál kisebb fájlok során Adatbetöltési rendszer csomagolt. A csomagolási felgyorsítja a kisméretű fájlok esetében az adatok másolását. Azt is menti a jelentős mennyiségű időt, amikor a fájlok száma messze meghaladja a könyvtárak száma.        |
 
4. Válassza ki **Start**. A rendszer érvényesíti a bemeneti adatok, és ha az érvényesítés sikeres, majd a feladat elindul. Indítsa el a feladat néhány percbe is telhet.

    ![A feladat elindításához a "Konfigurálás feladat és start" párbeszédpanelen](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Létrejön egy feladat a megadott beállításokkal. Szüneteltetése, folytatása, megszakítása vagy indítsa újra a feladatot. Válassza ki a feladat neve melletti jelölőnégyzetet, és válassza ki a megfelelő gombra.

    ![Az "Adatok másolása" oldalon feladatok kezelése](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Egy feladat akár szüneteltetheti is, ha alacsony kínálat csúcsidőben azt befolyásolja a NAS-eszköz erőforrások:

        ![Az "Adatok másolása" oldalon egy feladat felfüggesztése](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Később, amikor kevesen elindíthatná a feladatot:

        ![Az "Adatok másolása" oldalon egy feladat folytatása](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Bármikor megszakíthatja a feladatot:

        ![Az "Adatok másolása" oldalon feladatok megszakítása](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Feladatok megszakítása, ha egy megerősítő szükség:

        ![Erősítse meg a feladat törlése](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Ha úgy dönt, hogy a feladat megszakítása, már másolt adatok nem törlődnek. A Data Box-eszköz másolt adatokat törölni, visszaállítani az eszközt.

        ![Egy eszköz alaphelyzetbe állítása](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Ha megszakítja, vagy egy feladat felfüggesztése, nagy méretű fájlok előfordulhat, hogy csak részben másolja. A részlegesen másolt fájlok ugyanazt az állapotot az Azure-bA lesznek feltöltve. Ha megszakítja, vagy egy feladat felfüggesztése, ellenőrizze, hogy a fájlok megfelelően erőforrástármegosztásba megtörtént-e. Érvényesítheti a fájlokat, tekintse meg az SMB-megosztások, vagy töltse le a AJ fájlt.

    - Ha például egy hálózati hiba okozhatta, átmeneti hiba miatt sikertelen volt egy feladat újraindíthatja. Egy feladat nem indítható újra, ha például el nem éri a terminál állapotát, de **sikeres** vagy **hibákkal fejeződött be**. A sikertelen feladatok fájlelnevezési vagy a fájl mérete problémák oka lehet. Ezek a hibák jelentkezett, de a feladat nem indítható, miután elkészült.

        ![A meghiúsult feladat újraindítása](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Ha hibát tapasztal, és a feladat nem indítható újra, töltse le a hibanaplókat, és keresse ki a hibát a naplófájlokban. A probléma kijavítására már hozzon létre egy új feladatot a fájlok másolásához. Emellett [másolja a fájlokat az SMB-n keresztül](data-box-deploy-copy-data.md).
    
    - Ebben a kiadásban a feladat nem törölhető.
    
    - Korlátlan számú feladatot is létrehozhat, de Ön csak legfeljebb 10 feladatok párhuzamosan futtathatók egyszerre.
    - Ha **fájlok optimalizálása** kisméretű fájlok vannak-e csomagolt, betöltési másolási teljesítmény javítása érdekében. Ezekben az esetekben látni fog egy csomagolt fájlt (a fájl nevét, egy GUID lesz kell). Ne törölje ezt a fájlt. Ez lehet kicsomagolt feltöltésekor.

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
    - Az a **fájlok** oszlopban látható a számát és a fájlok másolását teljes méretét.
    - Az a **feldolgozott** oszlopban látható a számát, és a feldolgozott fájlok teljes méretét.
    - Az a **feladat részletei** oszlopában válassza **nézet** a feladat részleteinek megtekintéséhez.
    - Ha bármilyen hiba történik a másolási folyamat során, ahogyan az a **# hibák** oszlop, nyissa meg a **hibanapló** oszlopra, és a hiba hibakeresési naplók letöltése.

Várjon, amíg a másolási feladat befejeződésére. Mivel csak néhány hibákat naplózza a **csatlakozás és másolás** lapon, győződjön meg arról, hogy a másolási feladat befejeződött hiba nélkül, mielőtt továbblép a következő lépéssel.

![Nincsenek hibák, a "Csatlakozás és másolás" lap](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Ahhoz, hogy az adatok integritásának megőrzése, egy ellenőrzőösszege számított beágyazott módon az adatokat másolja. Válassza a másolás után **irányítópult megtekintése** ellenőrizheti a felhasznált terület és szabad terület az eszközön.
    
![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

A másolási feladat befejezése után kiválaszthatja **szállításra való**.

>[!NOTE]
> **Szállításra való** nem futhat, amíg a másolási feladat folyamatban van.

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan elküldje a Microsoftnak a Data Box-eszköze szállításra.

> [!div class="nextstepaction"]
> [A Microsoft Azure Data Box-eszköz szállításra](./data-box-deploy-picked-up.md)

