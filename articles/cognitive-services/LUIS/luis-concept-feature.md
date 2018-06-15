---
title: LUIS alkalmazásokban az Azure-szolgáltatások ismertetése |} Microsoft Docs
description: Ismerje meg a szolgáltatásokat, amelyek a LUIS alkalmazások teljesítményének javítása érdekében. Szolgáltatások közé tartozik a kifejezés listák és minták a reguláris kifejezések ismer fel.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: v-geberr
ms.openlocfilehash: 416fadaf52d7a71dcaab81aab3bf6099213e5af5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35350055"
---
# <a name="phrase-list-features-in-luis"></a>A LUIS kifejezés szolgáltatásai

A machine learning szolgáltatásban egy *szolgáltatás* megkülönböztető tulajdonságra vagy a rendszer betartja adatok attribútum. 

Funkciók hozzáadása ismeri fel a bemeneti címke vagy besorolni azokat a kívánt kapcsolatos javaslatokat tegyen a nyelvi modellhez. Szolgáltatások révén leképezések és a entitások LUIS, de a funkciók nem állnak a leképezések vagy entitások magukat. Ehelyett előfordulhat, hogy funkcióival kapcsolatos használati példák.  

## <a name="what-is-a-phrase-list-feature"></a>Mi az a kifejezés lista szolgáltatás?
A kifejezéslista például egy csoport értékek (szavakat vagy kifejezéseket), amelyek ugyanahhoz az osztályhoz tartozik a tartoznak, hasonlóképpen (például az várost vagy termékek nevének) kell kezelni. Mi LUIS értesül egyik automatikusan alkalmazza a mások számára is. Ez az egy lezárt [entitás listában](luis-concept-entity-types.md#types-of-entities) (pontos szöveg megegyezik) megfelelő szó.

Az alkalmazástartomány szóhasználatának, egy második jel LUIS-e szavakkal kapcsolatos kifejezés listáját hozzá.

## <a name="how-to-use-phrase-lists"></a>Kifejezés listák használata
Egy utazás ügynök alkalmazásban kifejezés lista létrehozása "Városokat" nevű London Párizsi és Kairó értékeket tartalmazó. Ha az egyszerű egységként megjelölésére a következő értékek egyike egy [példa utterance](luis-how-to-add-example-utterances.md#add-simple-entity-label) megjelölésű, a LUIS Tanulja meg a többi felismeréséhez. 

A kifejezéslista cserélhető vagy nem cserélhető lehet. Egy *cserélhető* kifejezéslista van értékek, amelyek szinonimák, és egy *nem cserélhető* kifejezéslista érték már nem, de más módon hasonló számára készült. 

## <a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>Kifejezés tartalmazza a Súgó egyszerű átválthatók entitások azonosítása
Átválthatók kifejezés listák olyan egy jó módszer a LUIS alkalmazás teljesítményét. Ha az alkalmazás előrejelzésére utterances, hogy a helyes leképezés, vagy ismer fel entitások, gondolja át e a utterances szokatlan szavak vagy szavak nem egyértelmű a következő jelentéssel elképzelhető, hogy tartalmaznak-e. Ezeknek a szavaknak esetén a kifejezés listára használható jól.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Kifejezés tartalmazza a Súgó azonosíthatja a leképezések bővebb ismertetése kontextust
Használjon kifejezés ritka, saját és külső szavak jeleníti meg. Lehet, hogy LUIS nem ismeri fel a ritka és saját fejlesztésű szavakat, valamint a külső szavak (kívül a kulturális környezet, az alkalmazás), és ezért kell adni őket kifejezés listáját. A kifejezéslista nem cserélhető, annak jelzésére, hogy a ritka szavak készlete képezi egy osztály, amely LUIS kell bemutatják, hogyan ismeri fel, de nincsenek szinonimák vagy cserélhető egymás mellett kell megjelölni.

Egy kifejezés listája nincs a szigorú egyeztetésre vagy a kötetcímke mindig a kifejezéslista összes feltételeit azok, ugyanúgy LUIS utasítást. Egyszerűen csak egy mutató. Például lehet, amely jelzi, hogy "Patti" és "Selma" nevek kifejezés listáját, de LUIS továbbra is használhatja környezetfüggő adatok ismeri fel, hogy azok jelenti azt egy másik, a "Tegye 2 foglalás a Patti tartozó Diner vacsora a" és "öt me befolyásoló tényezők utasításokat Selma, Georgia". 

A kifejezéslista hozzáadása akkor további példa utterances hozzáadása megjelölésű helyett. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Mikor érdemes használni a kifejezés listák és a lista entitások
Kifejezés listáját és a lista entitások közötti összes leképezések utterances befolyásolhatja, amíg valók ez más módon. Egy kifejezés listával leképezési előrejelzés pontszám hatással. A lista entitás segítségével entitás kibontási pontos szöveg egyeztetést végez hatással. 

### <a name="use-a-phrase-list"></a>Egy kifejezés helyett szerepel a listában
Kifejezés a listáját LUIS is figyelembe kell venni a környezetben, és a generalize lenne azonosítani a hasonló, de nem pontos egyezés, a listában lévő elemeket. Ha módosítania kell a LUIS alkalmazás generalize és kategória új cikkek azonosítására, használja a kifejezés listáját. 

Ha meg szeretné tudni ismeri fel az entitás egy értekezlet Feladatütemező, amely felismeri és új ügyfelek felismeri az új termékekkel készlet alkalmazás neve például új példányok az egy másik típusú entitás gép megtanulta, például egy egyszerű vagy hierarchikus entitás. Ezután létrehozhat egy kifejezést szavak és kifejezések, amelyek segítségével megkeresheti az entitás hasonló más szavakat LUIS. Ez a lista végig is vezeti LUIS ismeri fel az entitás példák további többszörösére felvételével listázzák értékét. 

Kifejezés listák hasonlóak a tartományspecifikus szóhasználatának segítenek a leképezések és entitások ismertetése minőségét javítja. Egy kifejezés listáját általános használatát a városnév például tulajdonnevek. A város neve lehet több szó, beleértve a kötőjelet, vagy aposztrófot.
 
### <a name="dont-use-a-phrase-list"></a>A kifejezéslista ne használjon. 
A lista entitás explicit módon határozza meg minden egyes értékhez entitás is igénybe vehet, és csak az egyező pontosan azonosítja. A lista entitás az olyan alkalmazásoknál, ahol egy entitás összes példánya nevezik, és nem változnak gyakran, például a étele menüben található elemek a egy étterem ritkán változó lehet. Ha egy teljes szöveges egyezik az entitás, ne használjon kifejezés listáját. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg, [ajánlott eljárások](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

Lásd: [szolgáltatások hozzáadása](luis-how-to-add-features.md) funkciók hozzáadása az LUIS alkalmazás tájékozódhat.