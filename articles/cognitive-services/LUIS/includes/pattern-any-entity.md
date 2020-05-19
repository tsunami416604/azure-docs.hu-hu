---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 8aef4a5eeeec27bfb2ca3edf9ff6823b88431319
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591835"
---
A pattern.any entitás lehetővé teszi szabad formátumú adatok keresését olyankor, amikor az entitás szövegezése nehézzé teszi az entitás végének a kimondott szöveg fennmaradó részétől való elkülönítését.

A humánerőforrás-alkalmazás segíti az alkalmazottakat A vállalati űrlapok megtalálásában.

|Kimondott szöveg|
|--|
|Hol található a **HRF-123456**?|
|Ki a szerzője ennek: **HRF-123234**?|
|A **HRF-456098** űrlap francia nyelven lett közzétéve?|

Ugyanakkor minden űrlapnak két neve van: egyrészt az előző táblázatban használt formázott neve, másrészt egy könnyebb elnevezése, mint például `Request relocation from employee new to the company 2018 version 5`.

A könnyebb űrlapnévvel rendelkező kimondott szöveg a következőképpen néz ki:

|Kimondott szöveg|
|--|
|Hol található a **2018-ban a vállalathoz került alkalmazott áthelyezési kérelme, 5. verzió**?|
|Ki a szerzője ennek: **2018-ban a vállalathoz került alkalmazott áthelyezési kérelme, 5. verzió**?|
|A **2018-ban a vállalathoz került alkalmazott áthelyezési kérelme, 5. verzió** űrlap francia nyelven lett közzétéve?|

A változó hosszúságú szöveg olyan szavakat tartalmaz, amelyek megnehezítik a LUIS szolgáltatás számára az entitás végének meghatározását. A Pattern.any entitás mintában való használata lehetővé teszi az űrlapnév elejének és végének megadását, így a LUIS helyesen nyeri ki az űrlapnevet.

|Példa kimondott sablonszövegre|
|--|
|Hol található a {FormName}[?]|
|Ki a szerzője ennek: {FormName}[?]|
|A {FormName} űrlap francia nyelven lett közzétéve[?]|

Tekintse át a mintát. bármilyen [hivatkozási](../reference-entity-pattern-any.md) információ