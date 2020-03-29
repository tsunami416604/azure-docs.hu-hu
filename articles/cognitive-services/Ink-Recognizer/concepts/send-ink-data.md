---
title: Tintaadatok küldése az Ink Recognizer API-nak
titleSuffix: Azure Cognitive Services
description: További információ a tintaelemző API hívásáról a különböző alkalmazásokhoz
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ad961495d44f13522a3c02224a5612aaedaf076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221105"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Tintaadatok küldése az Ink Recognizer API-nak 

A digitális szabadkézi műveletek olyan technológiák, amelyek lehetővé teszik a bemenetek, például a kézírás és a rajzok digitális ábrázolását. Ez általában egy digitalizáló, amely rögzíti a mozgását a bemeneti eszközök, például egy toll. Mivel az eszközök folyamatosan bővítik a digitális szabadkézi műveletek használati körét, a mesterséges intelligencia és a gépi tanulás bármilyen környezetben lehetővé teszi az írott alakok és szövegek felismerését. A Tintafelismerő API lehetővé teszi a tintavonások küldését és a róluk való részletes információ beírását. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>A tintafelismerő API és az OCR-szolgáltatások

A Tintafelismerő API nem használ optikai karakterfelismerést (OCR). Az OCR-szolgáltatások a képek képpontadatait a kézírás és a szöveg felismerése érdekében dolgozzák fel. Ezt néha offline felismerésnek is nevezik. Ehelyett a Tintafelismerő API-hoz olyan digitális tintavonási adatokra van szükség, amelyek a bemeneti eszköz használata során rögzítésre kerülnek. A digitális tintaadatok ily módon történő feldolgozása pontosabb felismerési eredményeket hozhat az OCR-szolgáltatásokhoz képest. 

## <a name="sending-ink-data"></a>Tintaadatok küldése

A Tintafelismerő API-hoz szükség van a bemeneti eszköz által létrehozott tollvonásokat reprezentáló X és Y koordinátákra, attól a pillanattól kezdve, hogy megérinti az észlelési felületet, egészen a felemelésig. Az egyes ecsetvonások pontjainak vesszővel elválasztott értékek sorozatának kell lenniük, és az alábbi példához hasonlóan JSON-ban kell formázni őket. Ezenkívül minden egyes szabadkézi vonásnak egyedi azonosítóval kell rendelkeznie minden kérésben. Ha az azonosító ismétlődik ugyanazon a kérésen belül, az API-t egy hiba adja vissza. A legpontosabb felismerési eredmények érdekében legalább nyolc számjegyet kell megkell adni a tizedesvessző után. A vászon kezdőpontja (0,0) a szabadkézi vászon bal felső sarkának kell lennie.

> [!NOTE]
> A következő példa nem érvényes JSON. A Teljes tintafelismerő JSON-kérés megtalálható a [GitHubon.](https://go.microsoft.com/fwlink/?linkid=2089909)
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Tintafelismerő válasza

A Tintafelismerő API elemzési választ ad vissza a tintatartalomból felismert objektumokról. A válasz olyan felismerési egységeket tartalmaz, amelyek a különböző tintavonások közötti kapcsolatokat írják le. Például a különböző, különálló alakzatokat létrehozó körvonalak különböző egységekben lesznek elszóródva. Minden egység részletes információkat tartalmaz a szabadkézi körvonalairól, beleértve a felismert objektumot, a koordinátákat és más rajzjellemzőket.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>A Tintafelismerő API által felismert alakzatok

A Tintafelismerő API képes azonosítani a jegyzetelésben leggyakrabban használt alakzatokat. Az alábbi képen néhány alapvető példa látható. Az API által felismert alakzatok és egyéb tintatartalom teljes listáját az [API referenciacikkében](https://go.microsoft.com/fwlink/?linkid=2089907)olvashatja. 

![A Tintafelismerő API által felismert alakzatok listája](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Ajánlott hívási minták

A tintafelismerő REST API-t az alkalmazásnak megfelelően különböző mintákban hívhatja meg. 

### <a name="user-initiated-api-calls"></a>Felhasználó által kezdeményezett API-hívások

Ha olyan alkalmazást hoz össze, amely felhasználói bevitelt (például jegyzetelő vagy jegyzetelő alkalmazást) vesz igénybe, érdemes lehet nekik szabályozni, hogy mikor és melyik tinta kerül elküldésre a Tintafelismerő API-ba. Ez a funkció különösen akkor hasznos, ha a szöveg és az alakzatok egyaránt jelen vannak a vásznon, és a felhasználók mindegyikhez különböző műveleteket szeretnének végrehajtani. Fontolja meg a kijelölési funkciók (például egy lasszó vagy más geometriai kijelölési eszköz) hozzáadását, amely lehetővé teszi a felhasználók számára, hogy kiválasszák, mit küld az API-nak.  

### <a name="app-initiated-api-calls"></a>Alkalmazás által kezdeményezett API-hívások

Azt is beállíthatja, hogy az alkalmazás időhosszabbítás után hívja meg a tintafelismerő API-t. Az aktuális tintavonások rendszeres elküldésével az API-ba tárolhatja a felismerési eredményeket, ahogy létrejönnek, miközben javítja az API válaszidejét. Például küldhet egy sor kézzel írt szöveget az API-t, miután észlelte a felhasználó befejezte azt. 

A felismerés előre történő megjelenítésével információt nyújt a tintavonások jellemzőiről, amelyek egymáshoz kapcsolódnak. Például, hogy mely körvonalak vannak csoportosítva, hogy ugyanazt a szót, sort, listát, bekezdést vagy alakzatot alkossák. Ezek az információk javíthatják az alkalmazás tintaválasztó funkcióit, ha például egyszerre választhatják ki a körvonalak csoportjait.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>A Tintafelismerő API integrálása a Windows Ink-mal

[A Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) olyan eszközöket és technológiákat biztosít, amelyekkel a digitális szabadkézi alkalmazások számos eszközön lehetővé teszik a digitális szabadkézi alkalmazások at. A Windows ink platformot a Tintafelismerő API-val kombinálva olyan alkalmazásokat hozhat létre, amelyek digitális tintavonásokat jelenítenek meg és értelmeznek.

## <a name="next-steps"></a>További lépések

* [Mi az Ink Recognizer API?](../overview.md)
* [Tintafelismerő REST API-hivatkozása](https://go.microsoft.com/fwlink/?linkid=2089907)

* Digitális tintavonás-adatok küldésének megkezdése a következő használatával:
    * [C #](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [Javascript](../quickstarts/javascript.md)
