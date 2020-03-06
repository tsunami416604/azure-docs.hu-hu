---
title: Tintaadatok küldése az Ink Recognizer API-nak
titleSuffix: Azure Cognitive Services
description: További információ a tintapatron Analyzer API különböző alkalmazásokhoz való meghívásáról
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ad961495d44f13522a3c02224a5612aaedaf076
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393101"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Tintaadatok küldése az Ink Recognizer API-nak 

A digitális szabadkézi műveletek olyan technológiák, amelyek lehetővé teszik a bemenetek, például a kézírás és a rajzok digitális ábrázolását. Ez általában olyan digitalizáló használatával érhető el, amely rögzíti a bemeneti eszközök, például a toll mozgását. Mivel az eszközök folyamatosan bővítik a digitális szabadkézi műveletek használati körét, a mesterséges intelligencia és a gépi tanulás bármilyen környezetben lehetővé teszi az írott alakok és szövegek felismerését. A tinta-felismerő API lehetővé teszi a szabadkézi ecsetvonások küldését, és részletes információkat kaphat róluk. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>A Ink-felismerő API és az OCR-szolgáltatások

A tinta-felismerő API nem használ optikai karakterfelismerést (OCR). Az OCR-szolgáltatások feldolgozzák a képek képpont-adatait, hogy kézírás-és szöveges felismerést nyújtsanak. Ezt időnként offline felismerésnek is nevezik. Ehelyett a tinta-felismerő API-nak a bemeneti eszközként rögzített digitális tollvonási adatokat kell használnia. A digitális tinta adatainak feldolgozása így az OCR-szolgáltatásokhoz képest pontosabb felismerési eredményeket eredményezhet. 

## <a name="sending-ink-data"></a>Szabadkézi adatok küldése

A tinta-felismerő API-nak az X és az Y koordinátákat kell megadnia, amelyek a bemeneti eszköz által létrehozott szabadkézi ecsetvonásokat jelölik, az észlelési felületet a felemelt állapothoz képest. Az egyes ecsetvonások pontjainak vesszővel tagolt értékeket tartalmazó sztringnek kell lenniük, és a JSON-ban kell formázni, például az alábbi példában látható módon. Emellett minden egyes tollvonásnak egyedi AZONOSÍTÓval kell rendelkeznie minden kérelemben. Ha az azonosítót ugyanazzal a kéréssel ismétli meg, az API hibaüzenetet ad vissza. A legpontosabb felismerési eredményekhez legalább nyolc számjegynek kell lennie a tizedesvessző után. A vászon kezdőpontja (0, 0) a szabadkézi vászon bal felső sarkát feltételezi.

> [!NOTE]
> A következő példa nem érvényes JSON. A [githubon](https://go.microsoft.com/fwlink/?linkid=2089909)egy teljes kézírás-felismerő JSON-kérés található.
 
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

## <a name="ink-recognizer-response"></a>Kézírás-felismerő válasza

A tinta-felismerő API egy elemzési választ ad vissza a szabadkézi tartalomból felismerhető objektumokról. A válasz olyan felismerési egységeket tartalmaz, amelyek leírják a különböző szabadkézi ecsetvonások közötti kapcsolatokat. Például a különálló alakzatokat létrehozó ecsetvonások különböző egységekben lesznek tárolva. Mindegyik egység részletes információkat tartalmaz a szabadkézi ecsetvonásokról, beleértve a felismerhető objektumot, annak koordinátáit és egyéb rajzolási attribútumait.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>A tinta-felismerő API által felismert alakzatok

A kézírás-felismerő API képes azonosítani a leggyakrabban használt alakzatokat a megjegyzésekben. Az alábbi képen néhány alapvető példa látható. Az API által felismert alakzatok és egyéb szabadkézi tartalmak teljes listáját az [API-referenciát ismertető cikkben](https://go.microsoft.com/fwlink/?linkid=2089907)találja. 

![A tinta-felismerő API által felismert alakzatok listája](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Ajánlott hívási minták

Az alkalmazásnak megfelelően különböző mintákban hívhatja a tinta Felismerőjét REST API. 

### <a name="user-initiated-api-calls"></a>Felhasználó által kezdeményezett API-hívások

Ha olyan alkalmazást hoz létre, amely felhasználói adatbevitelt végez (például egy Megjegyzés készítése vagy jegyzet alkalmazása), érdemes megadnia, hogy mikor és melyik tintát küldje el a rendszer a tinta-felismerő API-nak. Ez a funkció különösen akkor hasznos, ha a szöveg és az alakzatok egyaránt jelen vannak a vásznon, és a felhasználók különböző műveleteket kívánnak végrehajtani mindegyikhez. Vegye fontolóra a kiválasztási funkciók (például a lasszó vagy más geometriai kijelölési eszköz) hozzáadását, amelyekkel a felhasználók kiválaszthatják, hogy mit küld az API-nak.  

### <a name="app-initiated-api-calls"></a>Alkalmazás által kezdeményezett API-hívások

Azt is megteheti, hogy az alkalmazás egy időtúllépés után meghívja a tinta felismerő API-ját. Az aktuális szabadkézi ecsetvonásoknak az API-hoz való rendszeres küldésével a rendszer a felismerési eredményeket a létrehozásuk során tárolja, miközben az API válaszideje is javul. Küldhet például egy kézzel írt szöveget tartalmazó sort az API-nak, miután észlelte, hogy a felhasználó elvégezte azt. 

Az elismerési eredmények előzetesen információt adnak a szabadkézi ecsetvonások jellemzőiről, amelyek egymáshoz kapcsolódnak. Például, hogy mely ecsetvonások vannak csoportosítva, hogy ugyanazt a szót, sort, listát, bekezdést vagy alakzatot alkotják. Ez az információ növelheti az alkalmazás tinta-kiválasztási funkcióit azáltal, hogy egyszerre kiválaszthatja az ecsetvonások csoportjait, például a következőt:.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>A tinta-felismerő API integrálása Windows-tintával

A [Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) olyan eszközöket és technológiákat biztosít, amelyekkel számos különböző eszközön engedélyezhető a digitális és a többkirályos élmény. A tinta-felismerő API-val kombinálhatja a Windows tinta platformot a digitális tollvonásokat megjelenítő és értelmező alkalmazások létrehozásához.

## <a name="next-steps"></a>Következő lépések

* [Mi a Ink-felismerő API?](../overview.md)
* [Kézírás-felismerő REST API referenciája](https://go.microsoft.com/fwlink/?linkid=2089907)

* Digitális tinta ecsetvonás-adatok küldésének megkezdése a használatával:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)
