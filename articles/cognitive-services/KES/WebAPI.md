---
title: A Tudásbázis feltárása Service API webes API felülettel |} Microsoft Docs
description: A webes API felülete segítségével hozzon létre egy sokoldalú, szemantikai keresési élményt biztosít a a Tudásbázis feltárása szolgáltatás (KES) API kognitív szolgáltatásban.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 16c5680eb4f249a5d37e6b90eea92cfff7090eef
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346982"
---
# <a name="web-api-interface"></a>Webes API felület
A szolgáltatásmodell-fájlokból a Tudásbázis feltárása szolgáltatás által birtokolt, és hozzáférést biztosít egy webes API-k készlete.  Az API-kat a helyi számítógép használatával is futhat a [ `host_service` ](CommandLine.md#host_service-command) parancsot, vagy egy Azure cloud service használatával is telepíthető a [ `deploy_service` ](CommandLine.md#deploy_service-command) parancsot.  Mindkét technikát teszi közzé a következő API-végpontok:
* [*értelmezhetők* ](interpretMethod.md) – természetes nyelvű lekérdezési karakterláncként értelmezi. Annotált értelmezések, amelyek lehetővé teszik a keresőmezők automatikus kiegészítését, mintegy előre látva, hogy a felhasználó mit kíván írni.
* [*értékelje ki* ](evaluateMethod.md) – Evaluates és strukturált lekérdezési kifejezés a kimenetet visszaadja.
* [*calchistogram* ](calchistogramMethod.md) – az attribútumértékek strukturált lekérdezési kifejezésben által visszaadott objektumok hisztogram számítja ki.

Együttes használatuk esetén API módszerekhez engedélyezi a gazdag szemantikai keresésekhez létrehozását.  Természetes nyelvű lekérdezés-karakterlánc, adott a *értelmezhetők* módszer lehetővé teszi a megjegyzésekkel ellátott verzióinak a lekérdezési strukturált lekérdezési kifejezések, a mögöttes nyelvtan és index adatok alapján.  A *kiértékelése* módszer a strukturált lekérdezési kifejezés kiértékelése és megjelenítendő egyező index objektumokat adja vissza.  A *calchistogram* metódus kiszámítja attribútum érték azokat a terjesztéseket szűrést és a pontosítás engedélyezéséhez.

**Példa**

Academic kiadványok tartományban, ha a felhasználó a karakterlánc a "rejtett s" a *értelmezhetők* módszer is kínálnak rangsorolt értelmezéseket, amelyek arra utalnak, hogy a felhasználó előfordulhat, hogy keresése a következő kulcsszó: "rejtett szemantikai analysis", a cím "rejtett struktúra elemzés", vagy egyéb kifejezések kezdve a "rejtett s".  Ez az információ segítségével gyorsan útmutató a felhasználó a megfelelő keresési eredmények között.

Az ebben a tartományban a *kiértékelése* módszer használható a academic indexből kiadványok álló beolvasása és a *calchistogram* módszer használható attribútum terjesztési kiszámításához a megfelelő kiadványok, így további szűrő használatával és a keresési eredmények szűkítéséhez értékeit.

Vegye figyelembe, hogy a példák olvashatóságának, a REST API-hívások karakterek (például a tárolóhelyek), amely nem rendelkezik URL-kódolású. A kódot kell alkalmazni a megfelelő URL-kódolású.
