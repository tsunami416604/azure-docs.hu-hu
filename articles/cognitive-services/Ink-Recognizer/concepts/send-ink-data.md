---
title: Tintaadatok küldése az Ink Recognizer API-nak
titleSuffix: Azure Cognitive Services
description: További információ a különböző alkalmazások esetében az Ink-elemző API hívása
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 23431a6171f9ce4d2550ee62ac84679ce36126de
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721760"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Tintaadatok küldése az Ink Recognizer API-nak 

A digitális szabadkézi műveletek olyan technológiák, amelyek lehetővé teszik a bemenetek, például a kézírás és a rajzok digitális ábrázolását. Ez általában úgy valósul meg, egy digitalizáló, amely rögzíti a bemeneti eszközökről, például a toll típusú áthelyezések használatával. Mivel az eszközök folyamatosan bővítik a digitális szabadkézi műveletek használati körét, a mesterséges intelligencia és a gépi tanulás bármilyen környezetben lehetővé teszi az írott alakok és szövegek felismerését. A tinta felismerő API segítségével, amellyel elküldhetők tollvonások, és a velük kapcsolatos részletes információkhoz juthat. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>A tinta felismerő API vs. Optikai Karakterfelismerés szolgáltatások

A tinta felismerő API-t nem használja a optikai karakter Recognition(OCR). Optikai Karakterfelismerés szolgáltatások adja meg a kézírás-felismerés és szöveg felismerése képekből képpontot feldolgozni. A kapcsolat nélküli felismerés is nevezik. Ehelyett a szabadkézi felismerő API megköveteli a digitális ink körvonal adatokat rögzített, a bemeneti eszközt használja. Ezzel a módszerrel digitális ink adatfeldolgozás OCR szolgáltatások képest pontosabb felismerési eredményeket hozhat létre. 

## <a name="sending-ink-data"></a>Szabadkézi adatküldés

A tinta felismerő API szükséges, amelyek egy bemeneti eszközzel, abban a pillanatban, amikor szüntetni az észlelési felület érint által létrehozott szabadkézi X és Y koordinátái. Az egyes körvonal pontokat vesszővel tagolt értékek karakterláncnak kell lennie, és formázni a JSON-ban az alábbi példához hasonlóan. Emellett minden egyes tollvonások egy egyedi Azonosítóval kell rendelkeznie az egyes kérelmek. Ha az azonosító ismétlődik a kérésben belül, az API hibát adnak vissza. A legpontosabb eredmények felismerés számjegyeket tartalmazhat legalább nyolc a tizedesjel után. A forrás (0,0) a vászon bal felső sarokban a szabadkézi vászon adatforrásmérete.

> [!NOTE]
> A következő példában nem érvényes JSON. Szabadkézi felismerő JSON-kérelem teljes találhat [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909).
 
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

## <a name="ink-recognizer-response"></a>Szabadkézi felismerő válasz

A tinta felismerő API felismerte az objektumokkal kapcsolatos elemzési választ a szabadkézi tartalmat adja vissza. A válasz tartalmazza a felismerés egységek, amelyek ismertetik a különböző tollvonások közötti kapcsolatokat. Például hozzon létre különböző, különálló alakzatok vonások szereplő különböző egységeket. Minden egység a szabadkézi, beleértve a felismert objektum, a koordinátái és más rajzolási attribútumai részletes információkat tartalmaz.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Az alakzatok ismeri fel a szabadkézi felismerő API

A szabadkézi felismerő API azonosíthatja a leggyakrabban használt alakzatokra vegye figyelembe véve. Az alábbi képen néhány alapszintű példa látható. Alakzatok és más ink tartalom ismeri fel az API teljes listáját lásd: a [API áttekintésével foglalkozó cikkben](https://go.microsoft.com/fwlink/?linkid=2089907). 

![Az alakzatok ismeri fel a szabadkézi felismerő API listája](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Ajánlott hívó minták

Az alkalmazás megfelelően a szabadkézi felismerő REST API segítségével meghívhatja a különböző minták. 

### <a name="user-initiated-api-calls"></a>Felhasználó által kezdeményezett API-hívások

Fejleszt egy alkalmazást, amely a felhasználói bevitel (például egy vegye figyelembe véve vagy jegyzet alkalmazásokat), ha lehetővé teszik számukra mikor érdemes, és mely ink a szabadkézi felismerő API számára küldi el. Ez a funkció különösen akkor hasznos, ha a szöveg és alakzatok is megtalálható a vásznon, és különböző műveleteket hajt végre az egyes felhasználók igényeit. Fontolja meg kijelölés funkciók (például egy szabadkézi, vagy más geometriai Kijelölőeszköz), amelyek lehetővé teszik a felhasználók kiválaszthatják, hogy mit küld el a rendszer az API-t.  

### <a name="app-initiated-api-calls"></a>Alkalmazás által kezdeményezett API-hívások

Az alkalmazás hívja Ink felismerő API-t is, ha az időtúllépést követően. A jelenlegi festék vonások rendszeresen az API-t küld, felismerési eredményeket tárolhatja, miközben az API-válaszidő jönnek létre. Például küldhet egy sor a felhasználó annak észlelése után, az API-hoz írt szöveg felismerése képekből, befejeződött. 

Az előzetesen kellene a felismerési eredményeket ad információt tollvonások jellemzőit valamelyikéhez kapcsolódnak egymáshoz. Melyik körvonalak például szerint vannak csoportosítva, az ugyanazon szó, vonal, list, bekezdés vagy alakzat kialakításához. Ezt az információt képes arra, hogy válassza ki a csoportokat az körvonalak egyszerre, például az alkalmazás a Szabadkézi kijelölés szolgáltatások is növelheti.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Windows Ink a szabadkézi felismerő API integrálása

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) eszközök és technológiák az eszközök széles skálája digitális szabadkézi bővítsék biztosít. A Windows Ink platform egyesíthetők a szabadkézi Recognition API megjelenítése és a digitális tollvonások értelmezése alkalmazásokat hozhat létre.

## <a name="next-steps"></a>További lépések

* [Mi az a szabadkézi felismerő API-t?](../overview.md)
* [Szabadkézi felismerő REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2089907)

* Digitális ink körvonal adatok küldésének megkezdése:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)