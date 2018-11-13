---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: fccc036a5e0422508f7ebc3370a4b5faa5176dc2
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572222"
---
A Bing támogatja a találatok kiemelése, amely jelöli meg lekérdezési kifejezéseket (vagy más feltételek, a Bing talál megfelelő) az egyes, a megjelenítési karakterláncot. Ha például egy weboldal `name`, `displayUrl`, és `snippet` mezők lehet megjelölni a lekérdezési kifejezéseket.

Alapértelmezés szerint a Bing nem tartalmazza a megjelenített karakterláncok jelölők kiemelése. Adja meg a jelölők, adjon meg a `textDecorations` lekérdezési paraméter a kérelemből, és állítsa be **igaz**. A Bing jelöli meg a lekérdezési feltételek használatával a E000 és E001 Unicode karaktereket és az előfizetési időszak vége. Például ha a lekérdezési kifejezés induló utazó Dinghy, és mindkét kifejezés szerepel a mezőben, az előfizetési időszak van idézőjelek közé a találatok kiemelését karakterek az alábbi példában látható módon:  
  
![Találatok kiemelése](./media/cognitive-services-bing-hit-highlighting/bing-hit-highlighting.PNG) 

A karakterlánc megjelenítése a felhasználói felületen, mielőtt a Unicode-karaktereket szeretné cserélje a megjelenítési formátum megfelelő karakterek. Például, ha a szöveg HTML formátumban, előfordulhat, hogy kiemeli azokat a lekérdezési kifejezés a E000 lecserélésével < b\> és a E001 < /b\>. Ha nem kívánja alkalmazni a formázást, távolítsa el a jelölők a karakterláncból. 

A Bing, Unicode-karaktereket vagy a HTML-címkéket jelölők használata lehetővé teszi. Annak megadásához, mely jelölők használata, például a `textFormat` lekérdezési paraméter. Jelölje meg a tartalom Unicode-karaktereket, állítsa `textFormat` RAW (alapértelmezett), és jelölje meg a tartalmat a HTML-címkéket, állítsa be `textFormat` HTML-re. 
  
Ha `textDecorations` van **igaz**, a Bing tartalmazhatnak a következő jelölők a megjelenített karakterláncok válasz. Ha nincs HTML megfelelője, a HTML-táblázat cella je prázdná.

|Unicode|HTML|Leírás
|-|-|-
|U + E000|\<b >|A lekérdezési kifejezés (találatok kiemelése) kezdetét jelöli
|U + E001|\</b >|A lekérdezési időszak végét jelöli
|U + E002|\<e >|Dőlt betűvel tartalom kezdetét jelöli 
|U + E003|\</i >|Dőlt betűvel tartalom végét jelöli
|U + E004|\<br / >|Sortörés jelöli meg
|U + E005||Telefonszám kezdetét jelöli
|U + E006||Telefonszám végét jelöli
|U + E007||Egy cím kezdetét jelöli
|U + E008||Egy cím végét jelöli
|U + E009|\&nbsp;|Jelöli meg a nem törhető szóköz
|U + E00C|\<strong >|Félkövér tartalom kezdetét jelöli
|U + E00D|\</ strong >|Félkövér tartalom végét jelöli
|U + E00E||A tartalom, amelynek háttér kell lennie, mint a környező háttér világosabb kezdetét jelöli
|U + E00F||Amelynek háttér kell lennie, mint a környező háttér világosabb tartalom végét jelöli
|U + E010||A tartalom, amelynek háttér kell lennie a környező háttér sötétebb kezdetét jelöli
|U + E011||Amelyek hátterét kell lennie a környező háttér sötétebb tartalom végét jelöli
|U + E012|\<del >|Át kell húzni tartalom kezdetét jelöli
|U + E013|\</ del >|Át kell húzni tartalom végét jelöli
|U + E016|\<Sub >|Index tartalom kezdetét jelöli
|U + E017|\</ sub >|Index tartalom végét jelöli
|U + E018|\<sup >|Felső tartalom kezdetét jelöli
|U + E019|\</ sup >|Felső tartalom végét jelöli

A következő példa bemutatja egy `Computation` választ, amely tartalmazza az index jelölők log(2) lekérdezés időszakra. A `expression` mező tartalmazza a jelölők, csak ha `textDecoration` van **igaz**.

![számítási jelölők](./media/cognitive-services-bing-hit-highlighting/bing-markers-computation.PNG) 

Ha a kérés nem kért dekorációkat, a kifejezés log10(2) lesz. 
  
