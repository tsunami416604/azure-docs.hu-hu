---
title: 'Oktatóanyag: Adatok másolása az Azure Data Box Heavy szolgáltatásba adatmásolási szolgáltatáson keresztül'
description: Ebben az oktatóanyagban megtudhatja, hogyan másolhat adatokat az Azure Data Box Heavy eszközre az adatmásolási szolgáltatáson keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 67547db53d2b9ce05838335ffcb5d789b77ecbbe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77560220"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-heavy-preview"></a>Oktatóanyag: Az adatmásolási szolgáltatás használatával adatokat másolhat az Azure Data Box Heavy szolgáltatásba (előzetes verzió)

Ez az oktatóanyag ismerteti, hogyan lehet adatokat befogadni az adatmásolási szolgáltatás köztes állomás nélkül használatával. Az adatmásolási szolgáltatás helyileg fut az Azure Data Box Heavy szolgáltatáson, sMB-n keresztül csatlakozik a hálózathoz csatlakoztatott tároló (NAS) eszközhöz, és adatokat másol a Data Box Heavy-ba.

Használja az adatmásolási szolgáltatást:

- NAS-környezetekben, ahol előfordulhat, hogy a köztes állomások nem érhetők el.
- Kis fájlokkal, amelyek hetekig is eltarthatnak az adatok betöltése és feltöltése esetén. Az adatmásolási szolgáltatás jelentősen javítja a kis fájlok betöltési és feltöltési idejét.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatok másolása a Data Box Heavyre

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Befejezte ezt az oktatóanyagot: [Állítsa be az Azure Data Box Heavy szolgáltatást.](data-box-heavy-deploy-set-up.md)
2. Megkapta a Data Box Heavyt, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Rendelkezik annak a forrásNAS-eszköznek a hitelesítő adataival, amelyhez az adatok másolásához csatlakozni fog.
4. Nagy sebességű hálózathoz csatlakozik. A legnagyobb másolási sebesség érdekében két 40 GbE sebességű kapcsolat (csomópontonként egy) használható egyidejűleg. Ha nem rendelkezik 40 GbE sebességű kapcsolattal, javasoljuk, hogy legalább két 10 GbE sebességű kapcsolattal (csomópontonként eggyel) rendelkezzen. 

## <a name="copy-data-to-data-box-heavy"></a>Adatok másolása a Data Box Heavyre

Miután csatlakozott a NAS-eszközhöz, a következő lépés az adatok másolása. Az adatok másolásának megkezdése előtt tekintse át a következőket:

- Az adatok másolása közben győződjön meg arról, hogy az adatok mérete megfelel az Azure Storage és a [Data Box Heavy korlátokban](data-box-heavy-limits.md)ismertetett méretkorlátoknak.
- Ha a Data Box Heavy által feltöltött adatokat egyidejűleg más alkalmazások töltik fel a Data Box Heavy-on kívül, a feltöltési feladat hibák és az adatok megromlottak.
- Ha az adatok módosítása folyamatban van, miközben az adatmásolási szolgáltatás olvassa, előfordulhat, hogy hibák vagy adatsérülés lép nek ki.

Az adatok adatmásolási szolgáltatással történő másolásához létre kell hoznia egy feladatot:

1. A Data Box Heavy eszköz helyi webes felhasználói felületén nyissa meg az**Adatok másolásának** **kezelése** > című részt.
2. Az **Adatok másolása** lapon válassza a **Létrehozás gombot.**

    ![Válassza a Létrehozás gombot az "Adatok másolása" lapon](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. A **Feladat konfigurálása és indításpárbeszédpanelen** töltse ki a következő mezőket:
    
    |Mező                          |Érték    |
    |-------------------------------|---------|
    |**Feladat neve**                       |A feladathoz 230 karakternél rövidebb egyedi név. Ezek a karakterek nem szerepelhetnek \<a \> \|feladat \? \*nevében: , , , , \\, \:, , \/és\\\.         |
    |**Forrás helye**                |Adja meg az adatforrás SMB elérési `\\<ServerIPAddress>\<ShareName>` útját `\\<ServerName>\<ShareName>`a következő formátumban: vagy .        |
    |**Felhasználónév**                       |Felhasználónév `\\<DomainName><UserName>` formátumban az adatforrás eléréséhez. Ha egy helyi rendszergazda csatlakozik, explicit biztonsági engedélyekre lesz szüksége. Kattintson a jobb gombbal a mappára, válassza a **Tulajdonságok parancsot,** majd válassza **a Biztonság parancsot.** Ezzel hozzá kell adnia a helyi **rendszergazdát** a Biztonság laphoz.       |
    |**Jelszó**                       |Jelszó az adatforrás eléréséhez.           |
    |**Céltárfiók**    |Válassza ki a céltárfiókot, amelybe adatokat szeretne feltölteni a listából.         |
    |**Cél típusa**       |Válassza ki a céltároló típusát a listából: **Blokkblob,** **Lapblob**vagy **Azure-fájlok**.        |
    |**Céltároló/megosztás**    |Adja meg annak a tárolónak vagy megosztásnak a nevét, amelybe adatokat szeretne feltölteni a céltárfiókba. A név lehet megosztási név vagy tárolónév. Például használhatja a következőket: `myshare` vagy `mycontainer`. A nevet a formátumban `sharename\directory_name` vagy `containername\virtual_directory_name`a formátumban is megadhatja.        |
    |**Fájlok másolása egyező minta**    | A fájlnév-egyeztetési mintát a következő két módon adhatja meg:<ul><li>**Helyettesítő karakterkifejezések használata:** Csak `*` `?` helyettesítő kifejezések támogatják, és ezeket helyettesítő kifejezések támogatják. A kifejezés `*.vhd` például megfelel a kiterjesztést `.vhd` tartalmazó összes fájlnak. Hasonlóképpen, `*.dl?` egyezik az összes `.dl` fájlt a `.dl`kiterjesztés `.dll`vagy a kezdő , például . Hasonlóképpen `*foo` megegyezik azokkal a `foo`fájlokkal, amelyek neve a végződésű.<br>Közvetlenül beírhatja a helyettesítő kifejezést a mezőbe. Alapértelmezés szerint a mezőbe beírt értéket a program helyettesítő kifejezésként kezeli.</li><li>**Használjon reguláris kifejezéseket:** A POSIX-alapú reguláris kifejezések támogatottak. A reguláris kifejezés `.*\.vhd` például a `.vhd` kiterjesztéssel rendelkező összes fájlnak megfelel. Reguláris kifejezések esetén `<pattern>` adja `regex(<pattern>)`meg a közvetlenül a . A reguláris kifejezésekkel kapcsolatos további információkért látogasson el [a Reguláris kifejezés nyelve című rövid útmutatóra.](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)</li><ul>|
    |**Fájloptimalizálás**              |Ha ez a szolgáltatás engedélyezve van, az 1 MB-nál kisebb fájlok a betöltés során kerülnek becsomagolva. Ez a csomagolás felgyorsítja a kis fájlok adatmásolatát. Ez is megment egy alapvető összeg -ból idő mikor a fájlok at távoli kimagaslik a szám -ból könyvtárak.        |
 
4. Válassza az **Indítás** elemet. A bemenetek érvényesítése, és ha az érvényesítés sikeres, majd a feladat elindul. Beletelhet pár percbe, amíg a munka elkezdődik.

    ![Feladat indítása a "Feladat konfigurálása és indítás" párbeszédpanelről](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Létrejön egy feladat a megadott beállításokkal. Szüneteltetheti, folytathatja, megszakíthatja vagy újraindíthatja a feladatot. Jelölje be a feladat neve melletti jelölőnégyzetet, majd a megfelelő gombot.

    ![Feladat kezelése az "Adatok másolása" lapon](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Szüneteltetheti a feladatot, ha az hatással van a NAS-eszköz erőforrásaira csúcsidőben:

        ![Feladat szüneteltetése az "Adatok másolása" oldalon](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        A munkát később, csúcsidőn kívül folytathatja:

        ![Feladat folytatása az "Adatok másolása" oldalon](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - A feladatot bármikor visszavonhatja:

        ![Feladat megszakítása az "Adatok másolása" oldalon](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Amikor megszakítja a feladatot, megerősítést kell igazolnia:

        ![Feladat törlésének megerősítése](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Ha úgy dönt, hogy megszakítja a feladatot, a már másolt adatok nem törlődnek. A Data Box-eszközre másolt adatok törléséhez állítsa alaphelyzetbe az eszközt.

        ![Eszköz alaphelyzetbe állítása](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Ha megszakítja vagy szünetelteti a feladatot, előfordulhat, hogy a nagyfájlok csak részben lesznek másolva. Ezek a részben másolt fájlok ugyanabban az állapotban kerülnek feltöltésre az Azure-ba. Amikor megszakítja vagy szünetelteti a feladatot, győződjön meg arról, hogy a fájlok másolása megfelelő en lett. A fájlok érvényesítéséhez tekintse meg az SMB-megosztásokat, vagy töltse le az anyagjegyzékfájlt.

    - Újraindíthatja a feladatot, ha átmeneti hiba, például hálózati hiba miatt meghibásodott. De nem lehet újraindítani a feladatot, ha elérte a terminál állapotát, például **sikeres** vagy **befejezett hibákkal.** A feladathibákat fájlelnevezési vagy fájlméret-problémák okozhatják. Ezeket a hibákat a rendszer naplózza, de a feladat nem indítható újra a befejezése után.

        ![Sikertelen feladat újraindítása](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Ha hibát tapasztal, és nem tudja újraindítani a feladatot, töltse le a hibanaplókat, és keresse meg a hibát a naplófájlokban. Miután javította a problémát, hozzon létre egy új feladatot a fájlok másolásához. A [fájlokat smb-n keresztül](data-box-deploy-copy-data.md)is másolhatja.
    
    - Ebben a kiadásban nem törölhet i.
    
    - Létrehozhat korlátlan feladatokat, de egyszerre legfeljebb 10 feladatot futtathat párhuzamosan.
    - Ha **a fájloptimalizálás** be van kapcsolva, a kis méretű fájlok betöltéskor vannak csomagolva a másolási teljesítmény javítása érdekében. Ezekben az esetekben egy csomagolt fájl jelenik meg (a fájlneve GUID lesz). Ne törölje ezt a fájlt. A feltöltés során kicsomagolják.

6. Amíg a feladat folyamatban van, az **Adatok másolása** lapon:

    - Az **Állapot** oszlopban megtekintheti a másolási feladat állapotát. Az állapot a következő lehet:
        - **Fut**
        - **Nem sikerült**
        - **Sikeres**
        - **Felfüggesztés**
        - **Szüneteltetve**
        - **Megszakítás**
        - **Megszakítva**
        - **Befejeződött, hibákkal**
    - A Fájlok oszlopban **láthatja** a másolt fájlok számát és teljes méretét.
    - A **Feldolgozott** oszlopban láthatja a feldolgozott fájlok számát és teljes méretét.
    - A **Feladat részletei** oszlopban válassza a **Nézet** lehetőséget a feladat részleteinek megtekintéséhez.
    - Ha bármilyen hiba történik a másolási folyamat során, ahogy az a **# Hibák** oszlopban látható, lépjen a **Hibanapló** oszlopba, és töltse le a hibaelhárítási naplót.

Várja meg, amíg a másolási feladat befejeződik. Mivel egyes hibák csak a **Csatlakozás és másolás** lapon kerülnek naplózásra, a következő lépés előtt győződjön meg arról, hogy a másolási feladat hiba nélkül befejeződött.

![Nincs hiba a "Csatlakozás és másolás" oldalon](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Az adatok integritásának biztosítása érdekében az adatok másolásakordában számítja ki az ellenőrzőösszeget. A másolás befejezése után válassza az **Irányítópult megtekintése** lehetőséget az eszközön használt terület és szabad terület ellenőrzéséhez.
    
![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

> [!IMPORTANT]
> Ismételje meg ugyanezt az utasítást az adatok nak a Data Box Heavy második csomópontjára történő másolásához.

Miután a másolási feladat befejeződött, **kiválaszthatja a Felkészülés a szállításhoz**lehetőséget.

>[!NOTE]
> **A szállításra való felkészülés** nem futtatható, amíg a másolási feladatok folyamatban vannak.

## <a name="next-steps"></a>További lépések

A következő oktatóanyagra lépésként megtudhatja, hogyan szállíthatja vissza a Data Box Heavy eszközt a Microsofthoz.

> [!div class="nextstepaction"]
> [Az Azure Data Box Heavy eszköz szállítása a Microsoftnak](./data-box-heavy-deploy-picked-up.md)

