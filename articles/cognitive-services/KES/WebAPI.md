---
title: Webes API interfész – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: A webes API-felület használatával létrehozhat egy olyan interaktív és szemantikai keresés a a Knowledge Exploration Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 5be39e8dce6aeeef32d20273c56650620d6fe986
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122025"
---
# <a name="web-api-interface"></a>Webes API-felület

A modell fájlokat a Knowledge Exploration Service által készített üzemeltetett, és egy webes API-k készlete változón keresztül érhetők el.  Az API-k is futhat a helyi gép használja a [ `host_service` ](CommandLine.md#host_service-command) parancsot, vagy egy Azure cloud service használatával is üzembe helyezhetők a [ `deploy_service` ](CommandLine.md#deploy_service-command) parancsot.  Mindkét módszer a következő API-végpontokat tesznek elérhetővé:

* [*értelmezése* ](interpretMethod.md) – értelmezi a természetes nyelvű lekérdezési karakterláncot. Annotált értelmezések, amelyek lehetővé teszik a keresőmezők automatikus kiegészítését, mintegy előre látva, hogy a felhasználó mit kíván írni.
* [*kiértékelése* ](evaluateMethod.md) – Evaluates és strukturált lekérdezési kifejezés a kimenetet visszaadja.
* [*calchistogram* ](calchistogramMethod.md) – strukturált a lekérdezés által visszaadott objektumokhoz attribútumértékei hisztogram számítja ki.

Együttes használatuk esetén ezek a metódusok API gazdag szemantikai keresési funkciókat létrehozásának engedélyezése.  A természetes nyelvű lekérdezési karakterláncként megadva a *értelmezése* módszert biztosít a bemeneti lekérdezés strukturált lekérdezési kifejezések, nyelvtani és index alapul szolgáló adatok alapján a jegyzettel ellátott verzióit.  A *kiértékelése* metódus a strukturált lekérdezések kiértékelése és a megjelenítendő egyező index objektumokat adja vissza.  A *calchistogram* metódus kiszámítja az attribútum értéke disztribúciók szűrési és pontosítás engedélyezéséhez.

**Példa**

Oktatási kiadványok tartományban, ha a felhasználó a karakterlánc "segít a rejtett s" a *értelmezése* módszert is biztosítanak rangsorolt értelmezések, amely arra utal, hogy a felhasználó előfordulhat, hogy keresése a kulcsszó "segít a rejtett szemantikai", a cím "segít a rejtett struktúra elemzése", vagy egyéb kifejezések "segít a rejtett s" kezdetű.  Ez az információ segítségével gyorsan végigvezeti a felhasználót a kívánt találatok.

Az ebben a tartományban a *kiértékelése* módszer használható beolvasására az academic indexből kiadványok egyező és a *calchistogram* módszer használható a terjesztési attribútum kiszámítása értékek a megfelelő kiadványok esetén használható, amely tovább szűkítheti használható, és a találatok szűkítése.

Vegye figyelembe, hogy a példák olvashatóbbá, a REST API-hívások, amely nem rendelkezik URL-kódolású karakterek (például a tárolóhelyek) tartalmaznak. A kód megfelelő URL-cím-kódolásai a alkalmazni kell.
