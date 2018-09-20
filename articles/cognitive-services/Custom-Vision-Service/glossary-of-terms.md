---
title: Kifejezések jegyzéke – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: A Custom Vision Service szószedet.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 05/08/2017
ms.author: anroth
ms.openlocfilehash: ae68db3de5d1f7eaacbe355133b9b7b61f145f04
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363411"
---
# <a name="glossary-of-terms-for-custom-vision-service"></a>A Custom Vision Service kifejezések jegyzéke

Az alábbiakban néhány kifejezések a Custom Vision Service, és azok jelentését.

## <a name="classifier"></a>Az osztályozó

Besorolás a Custom Vision Service segítségével néhány betanító kép használatával létrehozhat egy modellt. Miután ezzel végzett, egy új osztályozó által igénybe vett képzés, kap egy értékelési végpontot (HTTPS) adhat hozzá az alkalmazáshoz. Minden egyes osztályozó hoz létre a saját project, és megtekintheti az összes projekt, miután bejelentkezett.

## <a name="domain"></a>Domain

Egy projekt létrehozásakor ki kell választania, hogy a projekt "tartomány". A tartomány egy adott típusú objektum a képeken besorolás optimalizálja. Például ha a forgatókönyv közötti apple torta sárgarépa tortaszelet lemezképek és a képek osztályozásához, majd válassza ki a "Food" tartományt. Ha biztos benne, hogy melyik tartománya, válassza ki, majd válassza az "Általános" tartomány.

- **Az élelmiszer-tartomány.** Optimalizált csészéket jelennének meg egy étterem menüben. Az egyes zöldségekből vagy FELISMERVE nem lett optimalizálva. Ha szeretné besorolni különálló vagy zöldségek fényképeket, használja az általános tartomány erre a célra.
- **A környezet tartományhoz.** Könnyen felismerhető névre arcrész természetes és mesterséges optimalizálva. Ebben a tartományban akkor működik a legjobban, ha a környezet a fényképet és a fókuszjelzőknek jól láthatóknak akkor is, ha a környezet valamivel probléma egyik oka egy csoportnak jelentő takarja.
- **A kiskereskedelmi tartományhoz.** Optimalizált Írisz képek egy vásárlási katalógus vagy a vásárlási webhelyén. Ha nagy pontosságú ruha besorolásakor, pants, ing, stb., majd a kiskereskedelmi következő tartomány használata.
- **A felnőtt tartományhoz.** Optimalizált pontosabb meghatározásához felnőtt tartalom, és nem felnőtt tartalom között. Például ha azt szeretné, lemezképek emberek fürdés csomagok blokkolására, ebben a tartományban lehetővé teszi egyéni tartalombesoroló létrehozása valósítható meg.
- **Az általános tartomány.** Olyan széles kép fájlosztályozási feladatokhoz.

A modell által létrehozott **tartományok tömörítése** exportálható legyen az iteráció exportálási funkciókkal. A korlátozások a mobileszközökön a valós idejű besorolás vannak optimalizálva. Kompakt tartomány beépített deklarációkkal némileg kevésbé pontos lehet a betanítási adatok akkora standard tartománnyal. Az egyensúlyt a rendszer, hogy azok kellően kicsire közel valós idejű helyben kell futtatni. 

## <a name="training-image"></a>Képzési kép

Hozzon létre nagy pontosságú besorolás, a Custom Vision Service számos betanító kép kell rendelkeznie. A képzési lemezkép egy fényképet, a kép azt szeretné, hogy a Custom Vision Service besorolásához. Például narancs osztályozására, meg kellene narancs több képek feltöltése a Custom Vision Service, hogy a szolgáltatás, amely képes felismerni a narancs besorolás létrehozása. Azt javasoljuk, hogy legalább 30 képenként címke.

## <a name="iteration"></a>Iteráció

Minden alkalommal, amikor a vonat vagy újra betaníthatja az, létrehozhat egy új példányaiban a modellben. Mindent megteszünk, hogy idővel az előrehaladást összehasonlítása több korábbi ismétlések. Már nem hasznosak az Ön ismétlések törölheti. Ne feledje, hogy egy iterációját törlése nem vonható vissza, és minden képet és a módosításokat, amelyek korábban csak az adott iteráció is törli. 

## <a name="workspace"></a>Munkaterület

A munkaterület összes a betanítási lemezképet tartalmaz, és tükrözi a legfrissebb verzió továbbfejlesztésében az összes módosítást például hozzáadott vagy eltávolított lemezképek. Betaníthatja az, amikor használatával hoz létre egy új ismétlését a besorolás, a képeket a munkaterület megtalálható.

## <a name="tags"></a>Címkék

Címkék használatával az objektumokat a betanító kép címkézését. Létrehozásakor egy osztályozó kutyák és pónikat azonosításához, akkor célszerű helyezni pónikat, és mind a "kutya" tartalmazó lemezképek "pony" címke és a egy kutya és a egy pony is tartalmazó lemezképek "pony" címke kutyájával lenni, tartalmazó lemezképek "kutya" címkét.

## <a name="evaluation"></a>Próbaverzió

Miután az osztályozó által igénybe vett van tanítva, az automatikusan létrehozott https-végpont használatával küldhet tesztelési képet. Az osztályozó által igénybe vett előre jelzett címkék készletét magabiztosan sorrendben adja vissza.

## <a name="predictions"></a>Előrejelzések

Az osztályozó által igénybe vett új képek osztályozásához fogad, mint a lemezképeket tárolja az Ön számára. Ezek a lemezképek használatával javíthatja a besorolás pontossága megfelelően megtekintett a rosszul előre jelzett lemezképeket. Ezek a lemezképek használatával, majd újra betaníthatja az.

## <a name="precision"></a>Pontosság

A besorolás, a lemezkép megfelelően besorolni mikor besorolni, kép, hogy mennyire valószínű? Kívül az összes rendszerkép (kutyák és pónikat) a besorolás betanításához használja milyen % volt a modell be megfelelő? 100 kép kívül 99 megfelelő címkéket 99 %-os pontossága biztosít.

## <a name="recall"></a>Visszahívás

Ki kell besorolásuk megfelelően összes rendszerkép hány volt az osztályozó által igénybe vett azonosítása megfelelően? 100 %-os visszahívását Ez azt jelenti, ha az osztályozó által igénybe vett betanításához használja a képeken 38 kutya képek, 38 kutyájával lenni az osztályozó által találhatók.

## <a name="settings"></a>Beállítások

Projekt szintű beállítások és a felhasználói szintű beállításokat két típusa van.

- Projekt szintű beállításokat: 
  
  Egy projekt vagy az osztályozó által igénybe vett projektszintű beállítások érvényesek. Ezek a következők:

   - Project-tartomány
   - Projekt neve
   - Projekt leírása
   - Használat
      - Betanító kép száma
      - Létrehozott címkék száma
      - Létrehozott ismétlések száma

- A felhasználói szintű beállításokat: 
   - Előfizetői azonosítók: egy a betanítása és a egy az értékelés/előrejelzéssel töltött.
   - Használat
      - Projektek létrehozása száma
      - Értékelés/előrejelzési API-hívások száma.
