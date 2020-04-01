---
title: Microsoft Azure Data Explorer Flow-összekötő (előzetes verzió)
description: Ismerje meg, hogy miként hozhat létre automatikusan ütemezett vagy aktivált feladatok ból a Microsoft Flow-összekötőt.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 198b78d5bab15057fdb6c7f6d4e8fff9f77d496e
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397090"
---
# <a name="microsoft-flow-connector-preview"></a>Microsoft Flow-összekötő (előzetes verzió)

Az Azure Data Explorer flow-összekötő lehetővé teszi, hogy az Azure Data Explorer a [Microsoft Power Automate Flow-képességeivel](https://flow.microsoft.com/) automatikusan futtathassa a Kusto-lekérdezéseket és parancsokat egy ütemezett vagy aktivált feladat részeként.

A gyakori használati forgatókönyvek a következők:

* Táblázatokat és diagramokat tartalmazó napi jelentések küldése
* Értesítések beállítása lekérdezési eredmények alapján
* Vezérlőparancsok ütemezése fürtökön
* Adatok exportálása és importálása az Azure Data Explorer és más adatbázisok között 

További információt a [Microsoft Flow-összekötő használati példái című témakörben talál.](flow-usage.md)

##  <a name="log-in"></a>Bejelentkezés 

1. Jelentkezzen be a [Microsoft Power Automate programba.](https://flow.microsoft.com/)

1. Az első csatlakozáskor a rendszer kéri a bejelentkezést.

1. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait.

![Bejelentkezési párbeszédpanel](./media/flow/flow-signin.png)

## <a name="authentication"></a>Hitelesítés

A hitelesítés felhasználói hitelesítő adatokkal vagy AAD-alkalmazással is hitelesíthető.

> [!Note]
> Győződjön meg arról, hogy az alkalmazás [Egy AAD-alkalmazás,](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-provision-aad-app) és jogosult a lekérdezések végrehajtására a fürtön.

1. Válassza ki a Microsoft Flow-összekötő jobb felső ![részén található három pont lehetőséget: Kapcsolat hozzáadása](./media/flow/flow-addconnection.png)

1. Válassza **az Új kapcsolat hozzáadása,** majd a Csatlakozás egyszerű **szolgáltatással lehetőséget.**
![Bejelentkezési párbeszédpanel](./media/flow/flow-signin.png)

1. Adja meg a szükséges adatokat:
    * Kapcsolat neve: Az új kapcsolat leíró és értelmezhető neve
    * Ügyfélazonosító: Az alkalmazás azonosítója
    * Ügyféltitok: Az alkalmazáskulcs
    * Bérlő: Annak az AAD-könyvtárnak az azonosítója, amelyben létrehozta az alkalmazást. Például a Microsoft bérlői azonosítója: 72f988bf-86f1-41af-91ab-2d7cd011db47

![Alkalmazás hitelesítése](./media/flow/flow-appauth.png)

Amikor a hitelesítés befejeződött, látni fogja, hogy a folyamat az újonnan hozzáadott kapcsolatot használja.

![Befejezett alkalmazáshitelesítés](./media/flow/flow-appauthcomplete.png)

Mostantól ez a folyamat ezekkel az alkalmazáshitelesítő adatokkal fog futni.

## <a name="find-the-azure-kusto-connector"></a>Az Azure Kusto-összekötő megkeresése

A Microsoft Flow-összekötő használatához először hozzá kell adnia egy eseményindítót. Az eseményindító definiálható egy ismétlődő időszak alapján, vagy egy korábbi folyamatműveletre adott válaszként.

1. [Hozzon létre egy új folyamatot,](https://flow.microsoft.com/manage/flows/new) vagy a kezdőlapon válassza a **Saját folyamatok** műveletet, majd válassza a **+ Új**lehetőséget.

    ![Új folyamat létrehozása](./media/flow/flow-newflow.png)

1. Üres ütemezés hozzáadása.

    ![Új ütemezett folyamat](./media/flow/flow-scheduled-from-blank.png)

1. Adja meg a szükséges információkat az Ütemezett folyamat létrehozása lapon.
    ![Ütemezett folyamat létrehozása](./media/flow/flow-build-scheduled-flow.png)
1. Kattintson a **Létrehozás** gombra.
1. Válassza az **+ Új lépés** gombot.
1. A keresőmezőbe írja be a "Kusto" szót.

    ![Folyamatműveletek kiválasztása](./media/flow/flow-actions.png)

1. Válassza az **Azure Data Explorer**lehetőséget.

## <a name="flow-actions"></a>Folyamatműveletek

Amikor megnyitja az Azure Data Explorer-összekötőt, három lehetséges műveletet adhat hozzá a folyamathoz.

Ez a szakasz az egyes Microsoft Flow-műveletek képességeit és paramétereit ismerteti.

![Flow Azure Data Explorer műveletek](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>Az irányítóparancs futtatása és az eredmények megjelenítése

Használja a Vezérlőközpont futtatása parancsot, és jelenítse meg az eredményműveletet a [vezérlőparancs](https://docs.microsoft.com/azure/kusto/management/index)futtatásához.

1. Adja meg a fürt URL-címét. Például: https://clusterName.eastus.kusto.windows.net
1. Adja meg az adatbázis nevét.
1. Adja meg az ellenőrző parancsot:
    * Dinamikus tartalom kiválasztása a folyamatban használt alkalmazásokból és összekötőkből
    * Kifejezés hozzáadása értékek eléréséhez, konvertálásához és összehasonlításához
1. Ha a művelet eredményeit e-mailben szeretné elküldeni táblázatként vagy diagramként, adja meg a diagram típusát, amely a következő lehet:
    * HTML-táblázat
    * Kördiagram
    * Idődiagram
    * Sávdiagram

![A Folyamatvezérlő futtatása parancs](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> A *Fürtnév* mezőbe írja be a fürt URL-címét.

### <a name="run-query-and-list-results"></a>Lekérdezési és listaeredmények futtatása

> [!Note]
> Ha a lekérdezés pontokkal kezdődik (azaz [vezérlőpara),](https://docs.microsoft.com/azure/kusto/management/index)használja a [Vezérlés futtatása parancsot, és jelenítse meg az eredményeket.](#run-control-command-and-visualize-results)

Ez a művelet lekérdezést küld a Kusto-fürtnek. Az ezt követően hozzáadott műveletek a lekérdezés eredményeinek minden sorában iterálni.

A következő példa percenként elindítja a lekérdezést, és e-mailt küld a lekérdezés eredménye alapján. A lekérdezés ellenőrzi az adatbázis sorainak számát, majd csak akkor küld e-mailt, ha a sorok száma 0-nál nagyobb. 

![Lekérdezéslista eredményeinek futtatása](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> Ha az oszlop több sort is használ, az összekötő az oszlop minden egyes sorához futni fog.

### <a name="run-query-and-visualize-results"></a>Lekérdezés futtatása és eredmények megjelenítése
        
> [!Note]
> Ha a lekérdezés pontokkal kezdődik (azaz [vezérlőpara),](https://docs.microsoft.com/azure/kusto/management/index)használja a [Vezérlés futtatása parancsot, és jelenítse meg az eredményeket.](#run-control-command-and-visualize-results)
        
A Lekérdezés futtatása és az eredményművelet megjelenítése segítségével a Kusto-lekérdezés eredményét táblázatként vagy diagramként jelenítheti meg. Ezzel a folyamattal például napi ICM-jelentéseket kaphat e-mailben. 
    
Ebben a példában a lekérdezés eredménye HTML-táblaként jelenik meg.
            
![Lekérdezés futtatása és eredmények megjelenítése](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> A *Fürtnév* mezőbe írja be a fürt URL-címét.

## <a name="email-kusto-query-results"></a>E-mail Kusto lekérdezés eredményei

Bármely folyamatba beilleszthet egy lépést, amellyel e-mailben küldhet jelentéseket bármely e-mail címre. 

1. Válassza a **+ Új lépés lehetőséget,** ha új lépést szeretne hozzáadni a folyamathoz.
1. A keresőmezőbe írja be az Office 365-öt, és válassza az **Office 365 Outlook**lehetőséget.
1. Válassza **az E-mail küldése (V2) lehetőséget.**
1. Adja meg azt az e-mail címet, ahová el szeretné küldeni az e-mail jelentést.
1. Adja meg az e-mail tárgyát.
1. Válassza **a Kód nézet lehetőséget.**
1. Helyezze a kurzort a *Törzs* mezőbe, és válassza **a Dinamikus tartalom hozzáadása**lehetőséget.
1. Válassza **a BodyHtml**lehetőséget.
    ![E-mail küldése](./media/flow/flow-send-email.png)
1. Válassza a **Speciális beállítások megjelenítése** lehetőséget.
1. A *Mellékletek neve -1* mezőben válassza a **Melléklet neve lehetőséget.**
1. A *Mellékletek tartalma* mezőben válassza a **Melléklettartalma**lehetőséget.
1. Ha szükséges, adjon hozzá további mellékleteket. 
1. Szükség esetén állítsa be a fontossági szintet.
1. Kattintson a **Mentés** gombra.

![E-mail küldése](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>Annak ellenőrzése, hogy a folyamat sikeres volt-e

Annak ellenőrzéséhez, hogy a folyamat sikeres volt-e, tekintse meg a folyamat futtatási előzményeit:
1. Nyissa meg a [Microsoft Flow kezdőlapját](https://flow.microsoft.com/).
1. A főmenüben válassza a [Saját folyamatok lehetőséget.](https://flow.microsoft.com/manage/flows)
    ![Saját folyamatok lap](./media/flow/flow-myflows.png)
1. A vizsgálni kívánt folyamatsorban jelölje ki a további parancsok ikont, majd **futtassa az előzményeket**.

    ![Előzmények futtatása menü](./media/flow//flow-runhistory.png)

    Minden folyamatfuttatás a kezdési idővel, az időtartammal és az állapottal van felsorolva.
    ![Előzmények eredményoldalának futtatása](./media/flow/flow-runhistoryresults.png)

    A folyamattal kapcsolatos részletekért a [Saját folyamatok](https://flow.microsoft.com/manage/flows) lapon válassza ki a vizsgálni kívánt folyamatot.

    ![Előzmények futtatása teljes eredményoldal](./media/flow/flow-fulldetails.png) 

Ha meg szeretné tudni, hogy miért nem sikerült a futtatás, válassza ki a futtatás kezdési idejét. Megjelenik a folyamat, és a sikertelen folyamat lépéseit egy piros felkiáltójel jelzi. Bontsa ki a sikertelen lépést a részletek megtekintéséhez. A jobb oldali ablaktábla a hibával kapcsolatos információkat tartalmaz, így hibaelhárítást tehet lehet.

![Folyamathiba](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Idő-eltikivételek

A folyamat sikertelen lehet, és visszaad egy "RequestTimeout" kivételt, ha hét percnél tovább fut.

További információ a [Microsoft Flow korlátairól](#limitations).
    
Ugyanaz a lekérdezés sikeresen futhat az Azure Data Explorerben, ahol az idő nem korlátozott, és módosítható.
            
A "RequestTimeout" kivétel az alábbi képen látható:
    
![Folyamatkérés időkérési kivétele – hiba](./media/flow/flow-requesttimeout.png)
    
Időtúllépési probléma megoldásához próbálja meg hatékonyabbá tenni a lekérdezést, hogy gyorsabban futjon, vagy válassza szét adattömbökre. Minden adattömb a lekérdezés egy másik részén futtatható.

További információt a [Lekérdezésekkel kapcsolatos gyakorlati tanácsok](https://docs.microsoft.com/azure/kusto/query/best-practices)című útmutatóban talál.

## <a name="limitations"></a>Korlátozások

* Az ügyfélnek visszaadott eredmények legfeljebb 500 000 rekordot rögzítenek. A rekordok teljes memóriája nem haladhatja meg a 64 MB-ot és a hétperces végrehajtási időt.
* A csatlakozó nem támogatja a [villa](https://docs.microsoft.com/azure/kusto/query/forkoperator) és [a lapként szolgáló](https://docs.microsoft.com/azure/kusto/query/facetoperator) operátorokat.
* A Flow a Microsoft Edge és a Chrome rendszerén működik a legjobban.

## <a name="next-steps"></a>További lépések

Ismerje meg a [Microsoft Azure Explorer Logic App összekötő,](https://docs.microsoft.com/azure/kusto/tools/logicapps) amely egy másik módja a Kusto-lekérdezések és parancsok automatikus futtatásának részeként egy ütemezett vagy aktivált feladat.
