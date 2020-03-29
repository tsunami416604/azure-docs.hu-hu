---
title: Képbélyegképek átméretezése és körülvágása – Bing Web Search API
titleSuffix: Azure Cognitive Services
description: A Bing Keresési API-k néhány válasza tartalmazza a Bing által kiszolgált miniatűr képek URL-címeit, amelyeket átméretezhet és körülvághat, és lekérdezési paramétereket tartalmazhatnak.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 630b86f55a537d109c851cb585cfccc34d229f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110638"
---
# <a name="resize-and-crop-thumbnail-images"></a>Miniatűrképek átméretezése és körülvágása

A Bing Keresési API-k néhány válasza tartalmazza a Bing által kiszolgált miniatűr képek URL-címeit, amelyeket átméretezhet és körülvághat, és lekérdezési paramétereket tartalmazhatnak. Példa:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Ha a miniatűrök egy részét megjeleníti, adja meg a többi kép megtekintésének lehetőségét.

> [!NOTE]
> Győződjön meg arról, hogy a miniatűrképek körülvágása és átméretezése olyan keresési forgatókönyvet biztosít, amely tiszteletben tartja a harmadik felek jogait, ahogy azt a Bing Search API [használati és megjelenítési követelményei](use-display-requirements.md)megkövetelik.

## <a name="resize-a-thumbnail"></a>Bélyegkép átméretezése 

Miniatűr átméretezéséhez a Bing azt javasolja, `w` hogy csak `h` egy (szélesség) vagy (magasság) lekérdezési paramétert adjon meg a bélyegkép URL-címében. Ha csak a magasságot vagy a szélességet adja meg, a Bing megőrizheti a kép eredeti aspektusát. Adja meg a szélességet és a magasságot képpontban. 

Ha például az eredeti miniatűr 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

És csökkenteni szeretné a méretét, `w` állítsa be a paramétert egy új értékre (például `336`), és távolítsa el a `h` paramétert:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Ha csak a miniatűr magasságát vagy szélességét adja meg, a kép eredeti méretaránya megmarad. Ha mindkét paramétert megadja, és a méretarány nem marad meg, a Bing fehér kitöltést ad a kép szegélyéhez.

Ha például egy 480x359-es képet vágás nélkül 200x200 képre méretez át, a teljes szélesség a képet tartalmazza, de a magasság 25 képpont fehér kitöltést tartalmaz a kép tetején és alján. Ha a kép 359x480, a bal és a jobb oldali szegély fehér kitöltést tartalmaz. A kép körülvágásakor a program nem ad hozzá fehér kitöltést.  

Az alábbi képen a miniatűrkép eredeti mérete látható (480x300).  
  
![Eredeti fekvő kép](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Az alábbi képen a kép 200x200-ra méretezett. A képarány megmarad, és a felső és alsó szegélyek fehérrel vannak kipárnázva (a fekete szegély itt a párnázás megjelenítéséhez szerepel).  
  
![Átméretezett fekvő kép](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Ha a kép eredeti szélességénél és magasságától nagyobb méreteket ad meg, a Bing fehér kitöltést ad a bal és a felső szegélyhez.  

## <a name="request-different-thumbnail-sizes"></a>Különböző miniatűrök kérése

Ha más miniatűrképméretet szeretne kérni, távolítsa el az összes `id` lekérdezési paramétert a miniatűr URL-címéről, kivéve a paramétereket. `pid` Ezután adja `&w` hozzá a `&h` (szélesség) vagy (magasság) lekérdezési paramétert a kívánt képmérettel képpontban, de mindkettőt ne. A Bing megtartja a kép eredeti képarányát. 

Ha a fenti URL-cím által megadott kép szélességét 165 képpontra szeretné növelni, a következő URL-címet kell használnia:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Ha a kép eredeti méreténél nagyobb képet kér, a Bing szükség szerint fehér kitöltést ad a képhez. Ha például a kép eredeti mérete 474x316, `&w` és 500-ra van állítva, a Bing 500x3333-as képet ad vissza. A kép felső és alsó szélementén 8,5 képpont fehér, a bal és a jobb szélen pedig 13 képpont nyi fehér kitöltés látható.

Ha meg szeretné akadályozni, hogy a Bing fehér kitöltést adjon hozzá, ha a kért méret nagyobb, mint a kép eredeti mérete, állítsa a `&p` lekérdezési paramétert 0-ra. Ha például a `&p=0` fenti URL-címbe adja meg a paramétert, a Bing 500x333-as kép helyett 474x316-os képet ad vissza:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Ha mindkettőt `&w` `&h` és a lekérdezési paramétereket is megadja, a Bing megtartja a kép méretarányát, és szükség szerint fehér kitöltést ad hozzá. Ha például a kép eredeti mérete 474x316, és a szélességi és magassági paramétereket`&w=200&h=200`200x200 ( ) értékre állítja, a Bing olyan képet ad vissza, amely 33 képpont fehér kitöltést tartalmaz felül és alul. Ha a `&p` lekérdezési paramétert adja meg, a Bing egy 200x134-es lemezképet ad vissza.

## <a name="crop-a-thumbnail"></a>Bélyegkép körülvágása 

Kép körülvágásához adja `c` meg a (körülvágási) lekérdezési paramétert. A következő értékeket használhatja:
  
- `4`&mdash; Vak arány  
- `7`&mdash; Intelligens arány  

### <a name="smart-ratio-cropping"></a>Intelligens arány körülvágása

Ha az Intelligens arány körülvágását `c` kéri `7`(a paraméter beállításával), a Bing kifelé vágja a képet az érdeklődési terület középpontjából, miközben megtartja a kép képarányát. Az érdeklődési terület a bing által meghatározott lemezkép területe, amely a legtöbb importálási részt tartalmazza. Az alábbi képen egy érdekes régió tmutat.  
  
![Érdekes régió](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Ha átméretezi a képet, és intelligens arány körülvágást kér, a Bing a méretarány megtartása mellett a kívánt méretre csökkenti a képet. A Bing ezután az átméretezett méretek alapján vágja le a képet. Ha például az átméretezett szélesség kisebb vagy egyenlő a magassággal, a Bing a képet az érdeklődési terület középpontjától balra és jobbra vágja le. Ellenkező esetben a Bing az érdeklődési régió középpontjának tetejére és aljára vágja.  
  
 
Az alábbi ábrán a kép 200x200-ra csökkent a Smart Ratio vágással. Mivel a Bing a bal felső sarokból méri a képet, a rendszer levágja a kép alsó részét. 
  
![Fekvő kép körülvágott 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Az alábbi ábrán a kép 200x100-ra csökkent a Smart Ratio vágással. Mivel a Bing a bal felső sarokból méri a képet, a rendszer levágja a kép alsó részét. 
   
![Fekvő kép körülvágott 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Az alábbi ábrán a kép 100x200-ra csökkent a Smart Ratio vágással. Mivel a Bing középről méri a képet, a rendszer levágja a kép bal és jobb részeit.
  
![100x200-ra levágott fekvőkép](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Ha a Bing nem tudja meghatározni a rendszerkép érdeklődési régióját, a szolgáltatás a Vakarány körülvágását fogja használni.  

### <a name="blind-ratio-cropping"></a>Vakarány körülvágása

Ha a Vakarány körülvágását `c` kéri `4`(a paraméter beállításával a bing a következő szabályokat használja a kép körülvágásához.  
  
- Ha `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`a program a képet a bal felső sarokból méri, alul vágja le.  
- Ha `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`a program a képet középről méri, majd balra és jobbra vágja.  

Az alábbi képen egy 225x300 képből álló kép látható.  
  
![Eredeti napraforgó kép](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Az alábbi ábrán a kép 200x200-ra csökkent a Vakarány vágásával. A kép mérése a bal felső sarokban történik, így a program a kép alsó részét vágja le.  
  
![Napraforgó kép levágott 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Az alábbi ábrán a kép 200x100-ra csökkent a Vakarány vágásával. A kép mérése a bal felső sarokban történik, így a program a kép alsó részét vágja le.  
  
![Napraforgó kép levágott 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Az alábbi ábrán a kép 100x200-ra csökkent a Vakarány vágásával. A kép középponttól kezdve méri a program, így a program a kép bal és jobb részeit vágja le.  
  
![Napraforgó kép vágva 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>További lépések

* [Mik azok a Bing Search API-k?](bing-api-comparison.md)
* [Bing Search API – követelmények használata és megjelenítése](use-display-requirements.md)
