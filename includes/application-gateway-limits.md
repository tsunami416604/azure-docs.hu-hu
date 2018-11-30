---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 3d66d825306c5183bdd8d8e611d98904eef2022a
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440199"
---
| Erőforrás | Alapértelmezett korlát | Megjegyzés |
| --- | --- | --- |
| Application Gateway |előfizetésenként 1000 | |
| Előtérbeli IP-konfigurációk |2 |1 nyilvános és 1 magán |
| Előtérbeli portok |40 | |
| Háttércímkészletek |40 | |
| Készletenkénti háttérkiszolgálók |1200 | |
| HTTP-figyelők |40 | |
| HTTP-terheléselosztási szabályok |400 |HTTP-figyelők száma * n |
| Háttérbeli HTTP-beállítások |40 | |
| Átjárónkénti példányok száma |75 | |
| SSL-tanúsítványok |40 |HTTP-figyelőnként 1 |
| Hitelesítési tanúsítványok |40 | |
| Kérelem időkorlátja min |1 másodperc | |
| Kérelem maximális időtúllépése |24 óra | |
| Helyek száma |40 |HTTP-figyelőnként 1 |
| Figyelőnkénti URL-címleképezések száma |1 | |
| URL-cím maximális-alapú szabályt leképezése|100|
| Az átirányítási konfigurációk |40| |
| WebSocket egyidejű kapcsolatok |5000| |
|URL-cím maximális hossza|8000|
| Maximális feltöltési méretének Standard |2 GB | |
| Maximális feltöltési méretének WAF |Közepes WAF átjárók – 100 MB-ra<br>Nagy WAF átjárók – 500 MB-tal| |
|WAF méretének korlátja (fájlok) nélkül|128 KB|
