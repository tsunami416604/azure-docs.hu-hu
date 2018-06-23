---
title: Egyéni stratégiai szolgáltatással - Azure kognitív szolgáltatások besorolás létrehozása |} Microsoft Docs
description: Útmutató az egyéni stratégiai szolgáltatás egy osztályozó létrehozása, amely megfejteni is a behatolók fényképeket objektumokat.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 6dc271c13f53a445c7d1101f5264d890208bd03c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347890"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Az egyéni stratégiai besorolás létrehozása

Az egyéni stratégiai szolgáltatás használatához először egy osztályozó kell létrehozni. Ebből a dokumentumból megtudhatja, hogyan hozhat létre egy osztályozó webböngésző segítségével.

## <a name="prerequisites"></a>Előfeltételek

Besorolás készítéséhez kell:

- Egy érvényes [Microsoft-fiók](https://account.microsoft.com/account) vagy egy Azure Active Directory, szervezeti azonosítójával ("munkahelyi vagy iskolai fiók"), így customvision.ai tud bejelentkezni, és az első lépések.

    > [!IMPORTANT] 
    > Az Azure Active Directory (Azure AD) felhasználó számára a szervezeti azonosítójával bejelentkezési [nemzeti felhők](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) jelenleg nem támogatott.

- Egy sorozatát kell még betanítani a osztályozó (legalább 30 kép címke /) a lemezképeket.

- Néhány képet a osztályozó tesztelése után a osztályozó be van tanítva.

- Választható lehetőség: Egy Azure-előfizetése társítva van a Microsoft Account vagy a szervezeti azonosítójával. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt.

    > [!IMPORTANT]
    > Egy Azure-előfizetés nélkül csak lesz létrehozásához __próbaverzió korlátozott__ projektek. Ha Azure-előfizetéssel rendelkezik, kérni fogja létrehozni az egyéni stratégiai szolgáltatás képzési és előrejelzés erőforrásokat a [Azure-portálon](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) projekt létrehozása során.   

## <a name="create-a-new-project"></a>Új projekt létrehozása

Új projekt létrehozásához használja az alábbi lépéseket:

1. A böngészőben navigáljon a [egyéni stratégiai weblap](https://customvision.ai). Válassza ki __bejelentkezés__ a szolgáltatás használatának megkezdéséhez.

    ![A bejelentkezési lapjának képe](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > Bejelentkezés után egyéni stratégiai szolgáltatásra, lehetősége lesz projektek listája. Tesztelési két "próbaverzió korlátozott" projektek kívül projektek társított egy Azure-erőforrást. Ha egy Azure felhasználói, látni fogja a társított összes projektek [Azure-erőforrások](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources) , amelyhez hozzáfér. 

2. Az első projektet létrehozásához válassza **új projekt**. Az első projekt a rendszer felkéri vállalja, hogy a szolgáltatási feltételeket. Jelölje be a jelölőnégyzetet, majd válassza ki a **elfogadom** gombra. A **új projekt** párbeszédpanel jelenik meg.

    ![Az új projekt párbeszédpanel neve, leírása és tartományokra vonatkozó mezőt tartalmaz.](./media/getting-started-build-a-classifier/new-project.png)

3. Adjon meg egy nevet és leírást a projekthez. Válassza ki az egyik rendelkezésre álló tartományában. Minden egyes tartományhoz optimalizálja a képek, bizonyos típusú osztályozó, a következő táblázatban ismertetett módon:

    |Tartomány|Cél|
    |---|---|
    |__Általános__| Kép besorolás feladatok széles köre optimalizálva. Ha nincs más tartományok megfelelő, vagy mely tartomány kiválasztása nem, válassza ki az általános tartományt. |
    |__Étele__|Egy étterem menü volna ismertető optimalizált csészék fényképeket. Ha szeretné besorolni különálló vagy zöldségek fényképek, használja a étele tartományhoz.|
    |__Jellegzetes hely__|Optimalizált felismerhető természetes és mesterséges jellegzetes hely. Ez a tartomány akkor működik a legjobban, ha a környezet a fénykép jól látható. Ez a tartomány működik, akkor is, ha a környezet némileg fedhetik személyek takarja el.|
    |__Kereskedelmi__|Képek, amelyek szerepelnek a vásárlási katalógus vagy a vásárlási webhelye optimalizálva. Ha azt szeretné, hogy magas pontosság zárolásának ruha, pants és ing között, ezt a tartományt használják.|
    |__Felnőtt__|Optimalizált pontosabb meghatározásához a felnőtt tartalom és a nem felnőtt tartalom. Például fürdés csomagok tagjai képeit blokkolni szeretne, ha a tartomány teszi ehhez az egyéni osztályozó létrehozása.|
    |__Kompakt tartományok__| A valós idejű besorolás mobileszközökön korlátozásait optimalizálva. A modellek kompakt tartományok által létrehozott helyi futtatásához exportálhatók.|

    Ha azt szeretné, később módosíthatók a tartományhoz.

4. Válasszon egy erőforráscsoportot. Az erőforráscsoport legördülő mutatja meg az Azure erőforráscsoportok, amely tartalmazza az egyéni stratégiai szolgáltatás-erőforrást. Válassza ki is létrehozhat __próbaverzió korlátozott__. A korlátozott próba bejegyzés az-Azure felhasználó fog tudni választhat csak erőforráscsoport.

    A projekt létrehozásához válassza __Create project__.

## <a name="upload-and-tag-images"></a>Feltöltés és címke lemezképek

1. Lemezképek hozzáadása a osztályozó, használja a __lemezképek hozzáadása__ gombra, majd válassza ki __keresse meg a helyi fájlok__. Válassza ki __nyitott__ címkézés áthelyezése.

    > [!TIP]
    > Miután kiválasztotta a képek, címke azokat. A címke a csoport kijelölt feltölteni, így elképzelhető, hogy a képek feltöltése a használni kívánt címkék alapján könnyebben képek vonatkozik. A megadott lemezképek címkéjének címkézett és feltöltése után is módosíthatja.

    > [!TIP]
    > Töltse fel a lemezképek különböző kamera beállítások, megvilágítási, háttér, típusok, stílusok, csoportok, méretét, stb. Fénykép típusok különböző segítségével győződjön meg arról, hogy a osztályozó nincs optimalizálva, és jól generalize is.

    Egyéni stratégiai szolgáltatás fogadja a képzés képek .jpg, .png és .bmp formátum, legfeljebb 6 MB méretű kép. (Legfeljebb 4 MB méretű kép előrejelzés képek is lehet.) Azt javasoljuk, hogy a képek a legrövidebb oldal 256 képpont lehet. Rövidebb, mint a legrövidebb oldal 256 képpont egyéni stratégiai szolgáltatás kiterjesztett képeket.

    ![A Hozzáadás képek vezérlő bal felső és alsó központban gombként jelenik meg.](./media/getting-started-build-a-classifier/add-images01.png)

    ![A helyi fájlok gomb alsó center mellett megjelenik.](./media/getting-started-build-a-classifier/add-images02.png)

    >[!NOTE] 
    > A REST API képzési képek betöltése a következő URL-címeket is használható.

2. A címke beállításához írjon be szöveget a __a címkék__ mezőben, majd a __+__ gombra. A képek feltöltése és címke azokat, használja a __[szám] fájlok feltöltése__ gombra. A képek több címke adhat hozzá. 

    > [!NOTE]
    > A feltöltés idő függ a számát és kijelölt képek méretét.

    ![A címke és feltöltése lapjának képe](./media/getting-started-build-a-classifier/add-images03.png)

3. Válassza ki __végzett__ után a képek fel lett töltve.

    ![A folyamatjelző minden feladat befejeződött.](./media/getting-started-build-a-classifier/add-images04.png)

4. Egy másik készlet képek feltöltéséhez adnak vissza az 1. lépés. Például ha megkülönböztetni kutya és pónikat, töltse fel, és pónikat képeket címkét.

## <a name="train-and-evaluate-the-classifier"></a>Betanítása és kiértékelése a osztályozó

A osztályozó betanítása, válassza ki a **betanítása** gombra.

![A vonat gomb nincs a böngészőablak jobb felső részén.](./media/getting-started-build-a-classifier/train01.png)

Csak a osztályozó betanítása néhány percet vesz igénybe. Ebben az időszakban a képzési vonatkozó információk jelennek meg.

![A vonat gomb nincs a böngészőablak jobb felső részén.](./media/getting-started-build-a-classifier/train02.png)

Képzési, miután a __teljesítmény__ jelenik meg. A pontosság és a visszaírási mutatók jelzi, hogy mennyire jó a osztályozó, automatikus tesztekre alapozva. Egyéni stratégiai szolgáltatás használ a lemezképeket, amelyek ezeket a számokat egy folyamattal kiszámításához betanítása hívása a(z) [k hajtott keresztellenőrzési](https://en.wikipedia.org/wiki/Cross-validation_(statistics)).

![A képzési eredmények megjelenítése a teljes pontosság és a visszaírási, és a pontosság, és az egyes-címke a osztályozó visszahívási.](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> Minden alkalommal, amikor kijelöli a **vonat** gomb, létrehozhat egy új munkamenetben a osztályozó. Megtekintheti a régi közelítés a a **teljesítmény** fülre, majd törölheti azokat, előfordulhat, hogy elavult. Ha töröl egy iteráció, végül azt egyedileg tartozó képeket törlése.

A osztályozó használja a képek modell, amely azonosítja az egyes címkék létrehozásához. A modell minőségét teszteléséhez a osztályozó megpróbál minden kép megtekintéséhez a modell megkeresi a modell.

A minőségi osztályozó eredmények jelennek meg.

|Időtartam|Meghatározás|
|---|---|
|__Pontosság__|Ha besorolását, kép, hogy mennyire valószínű a osztályozó a lemezkép megfelelően besorolása? Ki kell még betanítani a osztályozó (kutya és pónikat) használt összes képek milyen százalék nem a modell beszerezni megfelelő? 100 kép kívül 99 megfelelő címkék 99 %-os pontossága biztosít.|
|__Visszahívása__|Kívül minden lemezképbe, amelyek kell besorolásuk megfelelően hogy hány nem a osztályozó azonosítani megfelelően? A visszaírási 100 %-os azt jelenti, hogy ha a képek a osztályozó betanítása használt 38 kutya lemezképek, a osztályozó 38 kutya található.|

## <a name="next-steps"></a>További lépések

[Tesztelje, és a modell újratanítása](test-your-model.md)

