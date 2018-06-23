---
title: Az egyéni kifejezések stratégiai szolgáltatás – Azure kognitív szolgáltatások |} Microsoft Docs
description: Szószedet egyéni stratégiai szolgáltatáshoz.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/08/2017
ms.author: anroth
ms.openlocfilehash: 871617ce3c1c5a84df746c0c7d87c113b3a6f354
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347875"
---
# <a name="glossary-of-terms-for-custom-vision-service"></a>Egyéni stratégiai szolgáltatás szószedet

Az alábbiakban néhány kifejezés, amely egyéni stratégiai szolgáltatás, és azok jelentését.

## <a name="classifier"></a>Osztályozó

Besorolás egy olyan modell, létrehozására egyéni stratégiai szolgáltatást, néhány képzési lemezképek használatával. Amint befejezte egy új osztályozó betanítása, kiértékelése a végpont (HTTPS) adhat hozzá az alkalmazás kapni. Minden egyes osztályozó létrehozása a saját projektben, és miután bejelentkezett az összes projektet, melyekben megtekintheti.

## <a name="domain"></a>Tartomány

Amikor létrehoz egy projekt, jelöljön ki egy "tartományt" projekthez. A tartomány egy adott típusú objektum a képek besorolás optimalizálja. Például ha a forgatókönyv besorolására képeit és lemezképek sárgarépa tortaszelet apple torta között, majd válassza ki a "Étele" tartományt. Ha biztos benne, hogy mely tartomány elemre, majd válassza ki az "Általános" tartományt.

- **A étele tartományhoz.** Egy étterem menü mutatunk be csészéket optimalizálva. Az egyes zöldségekből vagy ismer fel nem lett optimalizálva. Ha szeretné besorolni különálló vagy zöldségek fényképek, használja az általános tartomány erre a célra.
- **A jellegzetes tartományhoz.** Optimalizált felismerhető természetes és mesterséges jellegzetes hely. Ez a tartomány akkor működik a legjobban, ha a környezet egyértelműen látható a fénykép, még akkor is, ha a környezet valamivel probléma egyik oka előtte vajon másokkal.
- **A kereskedelmi tartományhoz.** Egy vásárlásra szolgáló katalógus vagy a vásárlási webhelye sorolásához képek optimalizálva. Magas pontosság ruha besorolásakor, pants, ing, stb., majd használja a kiskereskedelmi tartományhoz.
- **A felnőtt tartományhoz.** Optimalizált közötti felnőtt tartalom és a nem felnőtt tartalom pontosabb meghatározásához. Például fürdés csomagok tagjai képeit blokkolni szeretne, ha a tartomány teszi ehhez az egyéni osztályozó létrehozása.
- **Az általános tartomány.** A kép besorolás feladatok széles választékában megfelel.

A modellek által generált **tartományok tömörítésével** iterációs exportálási funkció exportálhatók. Ezek a mobileszközök valós idejű besorolás korlátai vannak optimalizálva. Kompakt tartománnyal rendelkező beépített osztályozó némileg kevésbé pontos lehet betanítási adatok akkora szabványos tartomány. Mi a fontosabb, hogy azok kicsi helyileg, a közel valós időben kell futtatni. 

## <a name="training-image"></a>Képzési kép

Egy olyan nagy pontosságú osztályozó létrehozása, az egyéni stratégiai szolgáltatásnak kell több képzési lemezképet. A képzési lemezkép a kívánt egyéni stratégiai szolgáltatás besorolása képre fényképe. Például besorolására narancs, akkor kellene egyéni stratégiai szolgáltatás ahhoz, hogy az egy osztályozó létrehozása, amely fel tudja ismerni narancs narancs több képek feltöltése. Azt javasoljuk, hogy legalább 30 kép / címke.

## <a name="iteration"></a>Ismétlés

Minden alkalommal, amikor a vonat vagy újra betanítása a osztályozó, létrehozhat egy új változatát a. Segítségével összehasonlíthatja a folyamat adott idő alatt több múltbeli ismétlés tartsa azt. Már nem hasznosak az Ön ismétlések törölheti. Ne feledje, hogy egy iteráció törlése nem vonható vissza, és bármely képeket vagy módosításokat, amelyek adott iterációs egyedi is törli. 

## <a name="workspace"></a>Munkaterület

A munkaterület minden képzési képet tartalmaz, és tükrözi a legfrissebb iterációs az összes módosítást, mint hozzáadott vagy eltávolított képek. Betanítása a osztályozó, amikor hoz létre egy új munkamenetben a osztályozó a képek a munkaterület szerepel.

## <a name="tags"></a>Címkék

Címkék használata az objektumok a képzés képek megjelölésére. Kutya és pónikat besorolás létrehozásakor, akkor célszerű helyezni egy "kutya" címke kutya, pónikat, és mind a "kutya" tartalmazó lemezképek "póni" címke és lemezképeket, amelyek tartalmaznak egy kutya, mind a póni "póni" címke lemezképeket.

## <a name="evaluation"></a>Próbaverzió

Miután a osztályozó rendelkezik betanítása, elküldheti a tesztelési képet az automatikusan létrehozott https-végpont használatával. A besorolás adja vissza, amely előre jelzett címkék, ahhoz az vetett bizalmat.

## <a name="predictions"></a>Előrejelzések

A osztályozó osztályozására szolgáló új képek fogad el, mert az Ön tárolja a lemezképet. Ezek a lemezképek használatával javíthatja a besorolás pontossága a téves előre jelzett képek megfelelően címkézéssel. Ezeket a lemezképeket újra betanítása a osztályozó használhatja.

## <a name="precision"></a>Pontosság

Ha besorolását, kép, hogy mennyire valószínű a osztályozó a lemezkép megfelelően besorolása? Kívül az osztályozó (kutya és pónikat) betanításához használandó összes lemezkép milyen százalék nem a modell beszerezni megfelelő? 100 kép kívül 99 megfelelő címkék 99 %-os pontossága biztosít.

## <a name="recall"></a>Visszahívás

Kívül minden lemezképbe, amelyek kell besorolásuk megfelelően hogy hány nem a osztályozó azonosítani megfelelően? A visszaírási 100 %-os azt jelenti, hogy ha a képek a osztályozó betanításához használandó 38 kutya képek, 38 kutya az osztályozó által talált.

## <a name="settings"></a>Beállítások

A beállítások, a projektszintű beállításainak és a felhasználószintű beállítások két típusa van.

- Projektszintű beállításai: 
  
  A projekt vagy osztályozó projektszintű beállítások fognak vonatkozni. Ezek a következők:

   - Projekt-tartomány
   - Projekt neve
   - Projekt leírása
   - Használat
      - Képzési csomópontképek száma
      - A címkék létrehozása száma
      - Az létrehozott ismétlések száma

- Felhasználói szintű beállításokat: 
   - Előfizetés kulcsok: egy a képzési, egy értékelési/előrejelzés.
   - Használat
      - Projektek létrehozása száma
      - Kiértékelési/előrejelzés API-hívások száma.
