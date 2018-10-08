---
title: A Web Language Model API áttekintése – Azure Cognitive Services | Microsoft Docs
description: A Microsoft Cognitive Services részét képező Web Language Model API a legkorszerűbb eszközöket biztosítja a természetes nyelvek feldolgozásához.
services: cognitive-services
author: piyushbehre
manager: yanbo
ms.service: cognitive-services
ms.component: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: pibehre
ms.openlocfilehash: dc5dc0519e33e024014033ac5260004482b419c2
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096955"
---
# <a name="what-is-the-web-language-model-api-preview"></a>Mi a Web Language Model API? (Előzetes verzió)

A Microsoft Web Language Model API egy REST-alapú felhőszolgáltatás, amely a legkorszerűbb eszközöket biztosítja a természetes nyelvek feldolgozásához. Az API segítségével alkalmazása kihasználhatja a big data előnyeit, mivel olyan nyelvi modelleket használhat, amelyek a Bing által az en-US nyelvi piacon összegyűjtött, webes méretezésű korpuszokból tanultak.

Ezek a visszametszéses simítást alkalmazó N-gram típusú nyelvi modellek, amelyek akár ötödrendű Markov-láncokat is támogatnak, a következő korpuszokból tanulnak:

- Weblapok szövegtörzse
- Weblapok címsorszövege
- Weblapok horgonyszövege
- Webes keresési lekérdezések szövege

A Web Language Model API négy keresési műveletet támogat:

1. Szósorozat összesített (log10) valószínűsége.
2. Olyan szó feltételes (log10) valószínűsége, amelyet egy szósorozat előz meg.
3. Olyan szavak (kiegészítések) listája, amelyek a legnagyobb valószínűséggel követhetnek egy adott szósorozatot.
4. Olyan sztringek szavakra bontása, amelyek nem tartalmaznak szóközöket.

## <a name="getting-started"></a>Első lépések

1. Iratkozzon fel a szolgáltatásra.
2. Töltse le az [SDK](https://www.github.com/microsoft/cognitive-weblm-windows)-t.
3. Futtassa az SDK-mintakódot.
4. A végpontokkal kapcsolatos részletes információkért tekintse meg az [API-referenciát](https://westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104), amely számos nyelven íródott kódrészleteket is tartalmaz.

## <a name="underlying-technology"></a>Alapul szolgáló technológia

A következő tanulmány a nyelvi modellek fejlesztéséről ismertet részleteket, és forrásként megjelölendő az olyan kutatási publikációkban, amelyekhez az említett szolgáltatást felhasználták:

- [A Microsoft Web N-gram-korpusz és alkalmazások áttekintése](http://research.microsoft.com/apps/pubs/default.aspx?id=130762), NAACL-HLT 2010

Kattintson [ide](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0) az erre a munkára hivatkozó tanulmányok aktuális listájának megtekintéséhez.
