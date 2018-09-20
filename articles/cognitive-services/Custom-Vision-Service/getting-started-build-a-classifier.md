---
title: Egy osztályozó – Custom Vision Service létrehozásához
titlesuffix: Azure Cognitive Services
description: Megtudhatja, hogyan hozhat létre, amely képes tesz különbséget az adatok a fényképek objektumok besorolás a a Custom Vision Service használatával.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 998900e72511a95336e4a94289c794e2a8e59feb
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364246"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>A Custom Vision besorolás létrehozása

A Custom Vision Service használatához először egy osztályozó kell létrehozni. Ebből a dokumentumból megtudhatja, hogyan hozhat létre egy osztályozó a webböngészőn keresztül.

## <a name="prerequisites"></a>Előfeltételek

Tartalombesoroló létrehozása, előbb rendelkeznie:

- Egy érvényes [Microsoft-fiók](https://account.microsoft.com/account) vagy egy Azure Active Directory OrgID ("munkahelyi vagy iskolai fiók"), így customvision.ai be tud jelentkezni, és a kezdéshez.

    > [!IMPORTANT] 
    > Az Azure Active Directory (Azure AD) származó felhasználók OrgID bejelentkezési [országos felhők](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) jelenleg nem támogatott.

- (A minimum 30 képenként címke) a besorolás betanítására lemezképek sorozata.

- Néhány képet az osztályozó által igénybe vett tesztelése után az osztályozó által igénybe vett be van tanítva.

- Választható lehetőség: Azure-előfizetéssel társított Microsoft-Account vagy OrgID. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt.

    > [!IMPORTANT]
    > Azure-előfizetés nélkül, csak lesz létrehozása __próbaverzió korlátozott__ projektek. Ha rendelkezik Azure-előfizetéssel, kéri a Custom Vision Service betanítási és Predikciós erőforrások létrehozása a [az Azure portal](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) projekt létrehozása során.   

## <a name="create-a-new-project"></a>Új projekt létrehozása

Új projekt létrehozásához használja az alábbi lépéseket:

1. A böngészőben navigáljon a [Custom Vision weblap](https://customvision.ai). Válassza ki __jelentkezzen be a__ a szolgáltatás használatának megkezdéséhez.

    ![A bejelentkezési lap képe](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > Bejelentkezés után a Custom Vision Service, projektek listája jelenik meg. Két "korlátozott próbaverzió" projektek tesztelési kívül a projektek társítva egy Azure-erőforrás. Ha Ön Azure-felhasználó, látni fogja a kapcsolódó összes projektet [Azure-erőforrások](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources) , amely hozzáféréssel rendelkezik. 

2. Az első-projekt létrehozásához válassza **új projekt**. Az első projektet, a rendszer felkéri vállalja, hogy a szolgáltatási feltételeket. Jelölje be a jelölőnégyzetet, és válassza a **elfogadom** gombra. A **új projekt** párbeszédpanel jelenik meg.

    ![Az új projekt párbeszédpanel mezőinek nevét, leírását és tartományok rendelkezik.](./media/getting-started-build-a-classifier/new-project.png)

3. Adjon meg egy nevet és leírást a projekthez. Ezután válassza ki, az elérhető tartományok közül. Minden egyes tartományhoz optimalizálja az osztályozó által igénybe vett képek, bizonyos típusú, az alábbi táblázatban leírtak szerint:

    |Domain|Cél|
    |---|---|
    |__Általános__| Optimalizált széles kép fájlosztályozási feladatokhoz. Ha más tartományok sem megfelelő, vagy Ön nem tudja biztosan, melyik tartománya, válassza ki, válassza ki a általános tartományt. |
    |__Élelmiszer__|Optimalizált csészék fényképeket, látnák őket egy étterem menüben. Ha szeretné besorolni különálló vagy zöldségek fényképek, használja az élelmiszer-tartomány.|
    |__Arcrész__|Könnyen felismerhető névre arcrész természetes és mesterséges optimalizálva. Ez a tartomány akkor működik a legjobban, ha a környezet a fénykép fókuszjelzőknek jól láthatóknak. Ez a tartomány működik, akkor is, ha a környezet némileg fedhetik személyek takarja.|
    |__Kereskedelmi__|Optimalizált rendszerképeket, amelyeket egy vásárlási katalógus vagy a vásárlási webhelyén találhatók. Ha azt szeretné, hogy nagy pontosságú Írisz ruha pants és ing között, használja ezt a tartományt.|
    |__Felnőtt tartalom__|Optimalizált pontosabb meghatározásához a felnőtt tartalom, és nem felnőtt tartalom. Például ha azt szeretné, lemezképek emberek fürdés csomagok blokkolására, ebben a tartományban lehetővé teszi egyéni tartalombesoroló létrehozása valósítható meg.|
    |__Kompakt tartományok__| A korlátozások a mobileszközökön a valós idejű besorolás optimalizálva. A modellek kompakt tartományok által létrehozott helyi futtatásához exportálhatók.|

    Ha azt szeretné, később bármikor válthat a a tartományhoz.

4. Válasszon ki egy erőforráscsoportot. Az erőforráscsoport legördülő is láthatók, az Azure-erőforráscsoportok, amelyek tartalmazzák a Custom Vision Service erőforrás. Válassza ki is létrehozhat __próbaverzió korlátozott__. A korlátozott próbaverziós bejegyzés az egyetlen erőforráscsoportot, egy nem Azure-beli felhasználói tudják közül választhat.

    A projekt létrehozásához válassza __Create project__.

## <a name="upload-and-tag-images"></a>Feltöltés és címke képek

1. Képek hozzáadása az osztályozó által igénybe vett, használja a __lemezképeket__ gombra, majd válassza ki __helyi fájlok tallózása__. Válassza ki __nyílt__ szeretne váltani a címkézést.

    > [!TIP]
    > Miután a rendszerképek kiválasztásáról, fel kell címkéznie, azokat. A csoport lemezképek kiválasztott szeretne feltölteni, így egyszerűbb tölthet fel képeket szeretne használni a címkék alapján lehet érvényes a címke. A kódot a megadott lemezképek címkézett és feltöltése után is módosíthatja.

    > [!TIP]
    > Tölthet fel képeket a különböző kamera beállítások, világító, háttér, típusok, stílusok, csoportok, méretek, stb. Többféle típusú fénykép használatával győződjön meg arról, hogy az osztályozó által igénybe vett nem torzítatlan van, és jól generalize is.

    A Custom Vision Service fogad betanító kép .jpg, .png és .bmp formátum, a kép legfeljebb 6 MB. (Legfeljebb 4 MB / kép előrejelzési lemezképek is lehet.) Azt javasoljuk, hogy képeket lehet-e a legrövidebb peremhálózaton 256 képpont. Rövidebb, mint a legrövidebb peremhálózaton 256 képpont képeket a Custom Vision Service magánkészletekben.

    ![A Hozzáadás lemezképek vezérlőelem bal felső és alsó központról gombként jelenik meg.](./media/getting-started-build-a-classifier/add-images01.png)

    >[!NOTE] 
    > A REST API segítségével betöltése betanító kép URL-címekről.

2. A címke beállítása, adjon meg szöveget a a __saját címkéket__ mezőbe, majd a __+__ gombra. A képek, és azokat, használja a __[száma] fájlok feltöltése__ gombra. Egynél több címkét adhat hozzá a képeket. 

    > [!NOTE]
    > A feltöltés által számát és méretét, a kiválasztott képek függ.

    ![A címke és a feltöltési lap képe](./media/getting-started-build-a-classifier/add-images03.png)

3. Válassza ki __kész__ rendelkezik a képek feltöltése után.

    ![A folyamatjelző mutatja a minden feladat befejeződött.](./media/getting-started-build-a-classifier/add-images04.png)

4. Egy másik hárompéldányos készletet képek feltöltéséhez, térjen vissza 1. lépés. Például ha meg szeretné különböztetni egymástól kutyák és pónikat, töltse fel, és pónikat képcímkézést.

## <a name="train-and-evaluate-the-classifier"></a>Betanítása és kiértékelése az osztályozó által igénybe vett

A besorolás betanítására, válassza ki a **betanításához** gombra.

![A vonat gombra a böngészőablak jobb felső van.](./media/getting-started-build-a-classifier/train01.png)

Csak a besorolás betanítására néhány percet vesz igénybe. Ebben az időszakban a betanítási folyamat jelenik meg.

![A vonat gombra a böngészőablak jobb felső van.](./media/getting-started-build-a-classifier/train02.png)

Képzés, miután a __teljesítmény__ jelenik meg. A pontosság és a visszaírási mutatók megtudhatja, hogyan érdemes az osztályozó által igénybe vett van, az automatikus vizsgálat alapján. A Custom Vision Service használ a betanítási folyamat használatával alapján számítja ki az ezek a számok nevű elküldött képek [k lépésből keresztellenőrzési](https://en.wikipedia.org/wiki/Cross-validation_(statistics)).

![A képzési eredmények megjelenítése a teljes precíziós és visszaírási, és a pontosság, és az egyes címkét az osztályozó által igénybe vett visszaírásához.](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> Minden alkalommal, amikor kiválasztja a **Train** gomb, létrehozhat egy új ismétlését az osztályozó által igénybe vett. Megtekintheti a régi ismétlését a a **teljesítmény** fülre, majd törölheti azokat is, elavult lehet. Ha töröl egy iterációját, hogy végül egyedileg társított, képeket törlése.

Az osztályozó által igénybe vett összes rendszerkép használatával létrehoz egy modellt, amely minden címke azonosítja. Az osztályozó által igénybe vett tesztelésére a modell minőségét, próbálja meg a modell tesztelésével, a modell megkeresi az egyes rendszerképek.

A minőségű az osztályozó által igénybe vett eredmények jelennek meg.

|Időtartam|Meghatározás|
|---|---|
|__Pontosság__|A besorolás, a lemezkép megfelelően besorolni mikor besorolni, kép, hogy mennyire valószínű? (Kutyák és pónikat) a besorolás betanítására által használt összes rendszerképek, ki milyen százalékos volt a modell be megfelelő? 100 kép kívül 99 megfelelő címkéket 99 %-os pontossága biztosít.|
|__Idézze__|Ki kell besorolásuk megfelelően összes rendszerkép hány volt az osztályozó által igénybe vett azonosítása megfelelően? 100 %-os visszahívást, az azt jelenti, hogy ha a képeken, a besorolás betanítására használt 38 kutya képek, az osztályozó által igénybe vett 38 kutyák található.|

## <a name="next-steps"></a>További lépések

[Tesztelje, és a modell újratanítása](test-your-model.md)

