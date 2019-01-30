---
title: Webes API interfész – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: A webes API-felület használatával létrehozhat egy olyan interaktív és szemantikai keresés a a Knowledge Exploration Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 131d8d58982b5430063aa3dc7b3d1982cd430148
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211304"
---
# <a name="web-api-interface"></a>Webes API-felület

A modell fájlokat a Knowledge Exploration Service által készített üzemeltetett, és egy webes API-k készlete változón keresztül érhetők el.  Az API-k is futhat a helyi gép használja a [ `host_service` ](CommandLine.md#host_service-command) parancsot, vagy egy Azure cloud service használatával is üzembe helyezhetők a [ `deploy_service` ](CommandLine.md#deploy_service-command) parancsot.  Mindkét módszer a következő API-végpontokat tesznek elérhetővé:

* [*értelmezése* ](interpretMethod.md) – értelmezi a természetes nyelvű lekérdezési karakterláncot. Jegyzettel ellátott értelmezéseket ad vissza, amelyek lehetővé teszik a keresőmezők automatikus kiegészítését, előre jelezve, hogy a felhasználó mit fog írni.
* [*kiértékelése* ](evaluateMethod.md) – Evaluates és strukturált lekérdezési kifejezés a kimenetet visszaadja.
* [*calchistogram* ](calchistogramMethod.md) – strukturált a lekérdezés által visszaadott objektumokhoz attribútumértékei hisztogram számítja ki.

Együttes használatuk esetén ezek a metódusok API gazdag szemantikai keresési funkciókat létrehozásának engedélyezése.  A természetes nyelvű lekérdezési karakterláncként megadva a *értelmezése* módszert biztosít a bemeneti lekérdezés strukturált lekérdezési kifejezések, nyelvtani és index alapul szolgáló adatok alapján a jegyzettel ellátott verzióit.  A *kiértékelése* metódus a strukturált lekérdezések kiértékelése és a megjelenítendő egyező index objektumokat adja vissza.  A *calchistogram* metódus kiszámítja az attribútum értéke disztribúciók szűrési és pontosítás engedélyezéséhez.

**Példa**

Oktatási kiadványok tartományban, ha a felhasználó a karakterlánc "segít a rejtett s" a *értelmezése* módszert is biztosítanak rangsorolt értelmezések, amely arra utal, hogy a felhasználó előfordulhat, hogy keresése a kulcsszó "segít a rejtett szemantikai", a cím "segít a rejtett struktúra elemzése", vagy egyéb kifejezések "segít a rejtett s" kezdetű.  Ezen információk segítségével a felhasználó gyorsan eljuthat a kívánt keresési eredményekhez.

Az ebben a tartományban a *kiértékelése* módszer használható beolvasására az academic indexből kiadványok egyező és a *calchistogram* módszer használható a terjesztési attribútum kiszámítása értékek a megfelelő kiadványok esetén használható, amely tovább szűkítheti használható, és a találatok szűkítése.

Vegye figyelembe, hogy a példák olvashatóbbá tétele érdekében a REST API-hívások olyan karaktereket is tartalmaznak (például szóközöket), amelyek nem URL-kódolásúak. Saját kódjának mindig a megfelelő URL-kódolásokat kell alkalmaznia.
