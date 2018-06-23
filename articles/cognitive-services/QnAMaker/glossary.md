---
title: Szószedet - Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: Szószedet
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e28cddec005cb6ba99b9f60d8b03a11f1bc97062
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348690"
---
# <a name="glossary"></a>Szószedet

## <a name="qna-maker-service"></a>Kérdések és válaszok készítő szolgáltatás
A kérdések és válaszok készítő "szolgáltatás" előfeltételként kérdések és válaszok készítő elindítására. A kérdések és válaszok készítő réteghez megvásárlásáról beállítja az erőforrások létrehozására és kezelésére a Tudásbázis Azure-előfizetése. Kérdések és válaszok Maker felhasználói fiókokat hozhat létre az Azure-előfizetés több készítő kérdések és válaszok szolgáltatás.

## <a name="knowledge-base"></a>Tudásbázis
A Tudásbázis a tárház kérdések, és felveszi a létrehozott, kezelt és kérdések és válaszok készítő használt. Minden kérdés-válasz készítő réteg több Tudásbázis esetében használható.

## <a name="endpoint"></a>Végpont
A REST-alapú HTTP-végpont a Tudásbázis tartalmat, amely integrálható az alkalmazásba, általában Csevegés bot karbantartása. 

## <a name="test-knowledge-base"></a>Tudásbázis tesztelése
A Tudásbázis két állapota - teszt van, és közzé. A teszt Tudásbázis verziója szerkesztéséhez, mentett és tesztelt, a pontosság és a válaszok teljességéről folyamatban van. A teszt Tudásbázis végzett módosítások nem befolyásolják a végfelhasználó az alkalmazást vagy csevegés botot.

## <a name="published-knowledge-base"></a>Közzétett Tudásbázis
A Tudásbázis két állapota - a vizsgálati és egy közzétett van.  A közzétett Tudásbázis verziója, amely a Csevegés botot/alkalmazásban szereplő szolgál. A művelet a Tudásbázis közzétételének a teszt Tudásbázis tartalmát a Tudásbázis közzétett verziójának helyezi. Mivel a közzétett Tudásbázis a verziója, az alkalmazás által használt keresztül a végpont, annak érdekében, hogy a tartalom-e a megfelelő és tesztelt figyelmet kell fordítani.

## <a name="query"></a>Lekérdezés
A felhasználó lekérdezése a Tudásbázis kérő a végfelhasználói vagy tester kérdése. A lekérdezés legtöbbször a természetes nyelvű formátumban vagy néhány kulcsszót, amelyek megfelelnek a kérdést.

## <a name="response"></a>Válasz
A rendszer a választ, a válasz veszi át a Tudásbázis következő, a legmegfelelőbb egy adott felhasználóhoz lekérdezés alapján.

## <a name="confidence-score"></a>Megbízhatósági pontszám
A válasz az vetett bizalmat pontszám egy numerikus értéket 0 és 100, 100 alatt egy felhasználói lekérdezés és válasz szolgáltatott Tudásbázis fel kérdést pontos lekérdezés egyezését között egy adott felhasználóhoz lekérdezés helyes, a megfelelő választ. Válaszok általában rangsora az vetett bizalmat pontszám szerint, és a másikat a magasabb abban, hogy pontszám kiszolgált az alapértelmezett válaszként.
