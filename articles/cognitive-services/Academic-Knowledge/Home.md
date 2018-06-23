---
title: Academic Knowledge API Microsoft Academic diagramhoz |} Microsoft Docs
description: Academic Knowledge API segítségével felhasználói lekérdezések értelmezi, és a Academic diagramhoz kognitív Microsoft-szolgáltatásokban gazdag adatok lekérését.
services: cognitive-services
author: mvorvoreanu
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: mivorvor
ms.openlocfilehash: e241f9a87cd58b62eafd754bd3cb4283aa0a1e92
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347514"
---
# <a name="academic-knowledge-api"></a>Academic Knowledge API

Üdvözli a Academic Knowledge API. E szolgáltatás lehetővé teszi a kutatási célú felhasználói lekérdezések értelmezését és gazdag információt hív le a Microsoft Academic Graph-ból (MAG). A súly Tudásbázis a webalkalmazás skálázása heterogén entitás diagramhoz tudományos tevékenységek modell entitások áll: tanulmány, Szerző, intézmény, helyszínére, és az esemény mezőjét. 

A súly adatokat a Bing webes index, valamint egy belső fejlesztésű Tudásbázis a Bing szolgáltatástól származó van bányásszák. Eredményeképpen a további folyamatos indexelő Bing, az API a következő felderítési és a Bing által indexelő webes friss adatait fogja tartalmazni. Ez az adatkészlet alapján, a Academic Knowledge API-k lehetővé teszi, hogy zökkenőmentesen reaktív keresési proaktív javaslat feladatait, a gazdag kutatási papír graph találatok és a hisztogram disztribúciók Tudásbázis-vezérelt, interaktív párbeszédpanel a attribútum értékei által írt cikkeket és a kapcsolódó entitásokból.

A Microsoft Academic grafikonon további információkért lásd: [ http://aka.ms/academicgraph ](http://aka.ms/academicgraph).

Academic Knowledge API kognitív szolgáltatások laborokhoz kognitív Services előzetes verzió tért át. Az új kezdőlapja a projekt van: [ https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge ](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). A meglévő API-kulcs 2018. május 24. amíg továbbra is működnek. Ezt követően hozzon létre egy új API-kulcsot. Vegye figyelembe, hogy fizetős preview többé nem érhető el a meglévő kulcs lejárta után. Lépjen kapcsolatba a csapat, ha az ingyenes csomagot az API nem elegendő a célokra. 

## <a name="features"></a>Szolgáltatások
Academic Knowledge API négy kapcsolódó többi végpontokból áll:  
  1. **értelmezhetők** – természetes nyelvű felhasználói lekérdezési karakterláncként értelmezi. Annotált értelmezések, amelyek lehetővé teszik a keresőmezők automatikus kiegészítését, mintegy előre látva, hogy a felhasználó mit kíván írni.  
  2. **értékelje ki** – egy lekérdezési kifejezésben kiértékeli, és Academic Knowledge entitás eredményeket ad vissza.  
  3. **calchistogram** – a terjesztés az attribútumértékek egy lekérdezési kifejezésben, például a terjesztése idézetei év által egy adott szerző által visszaadott academic entitások hisztogram számítja ki.  
  4. **Graph keresési** – mintát, a megfelelő entitás eredményeket ad vissza a megadott gráffal keres.

Együtt használja, ezek API módszerek használatával hozhat létre egy gazdag szemantikai keresési élményt biztosít. A felhasználó lekérdezési karakterlánc, adott a **értelmezhetők** módszer lehetővé teszi a lekérdezés és a strukturált lekérdezési kifejezésben, a mögöttes academic szemantikáját alapján a felhasználó lekérdezése opcionálisan végrehajtásánál megjegyzésekkel ellátott verziója adatok. Például, ha a felhasználó a karakterlánc *rejtett s*, a **értelmezhetők** módszer is kínálnak rangsorolt értelmezéseket, amelyek arra utalnak, hogy a felhasználó előfordulhat, hogy keresése a vizsgálat mező  *rejtett szemantikai elemzés*, a dokumentum *rejtett struktúra elemzése*, vagy más entitás kifejezések kezdve *rejtett s*. Ez az információ segítségével gyorsan útmutató a felhasználó a megfelelő keresési eredmények között.

A **kiértékelése** módszer használható álló papír entitásokat a academic Tudásbázis beolvasása és a **calchistogram** kiszámításához az attribútumértékek a telepítési módszer használható azon papír entitások, amelyek segítségével további az eredmények szűréséhez.        

A **graph keresési** metódusnak két módja van: *json* és *lambda*. A *json* mód végezheti graph jogcímértékekhez a mintaegyezési funkciót a JSON-objektum által megadott graph mintáknak megfelelően. A *lambda* mód végezheti kiszolgálóoldali számítások alapján a felhasználó által megadott lambda kifejezések graph traversals során.

## <a name="getting-started"></a>Első lépések 
Tekintse meg a részletes dokumentációt a bal oldali altémakörei.  Vegye figyelembe, hogy a példák olvashatóságának, a REST API-hívások karakterek (például a tárolóhelyek), amely nem rendelkezik URL-kódolású.  A kódot kell alkalmazni a megfelelő URL-kódolású.
