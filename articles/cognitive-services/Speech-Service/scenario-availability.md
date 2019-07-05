---
title: A forgatókönyv rendelkezésre állása – beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás régiók referenciája.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: 7aa2c72a01f1887ea9680f8d5706b825a49039a1
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561292"
---
# <a name="scenario-availability"></a>A forgatókönyv rendelkezésre állása

A Speech service SDK számos forgatókönyv funkciókat, számos programozási nyelvet és a környezetek között.  Nem minden esetben még jelenleg az összes programozási nyelvet, vagy minden környezetben érhető el.  Az alábbiakban van az egyes forgatókönyvek rendelkezésre állását.

- **Beszédfelismerés (SR), kifejezéslista, szándék, fordítás és a helyszíni tárolók**
  - Minden programozási nyelvet/környezetben, ahol a nyílra kattintva <img src="media/index/link.jpg" height="15" width="15"></img> a rövid útmutató tábla [Itt](https://aka.ms/csspeech).
- **Szövegfelolvasás**
  - C++/ Windows és Linux-környezetekkel
  - C#/ Windows & UWP & Unity
  - Szövegfelolvasás – REST API-t minden helyzetben használható.
- **Hálózati ébresztési Word (kulcsszó Spotter/KWS)**
  - C++/ Windows és Linux-környezetekkel
  - C#/ Windows és Linux-környezetekkel
  - Python/Windows & Linux
  - Java/Windows és Linux & Android (beszéd eszközök SDK)
  - Ébresztési Word (kulcsszó Spotter/KWS) funkció működik együtt a mikrofon bármilyen, hivatalos KWS támogatja, azonban jelenleg kizárólag a a mikrofon tömbök megtalálható az Azure Kinect DK hardverekre, sem a Speech Devices SDK-val
- **Voice-First Virtual Assistant**
  - C++/ Windows és Linux és macOS
  - C#/Windows
  - Java/Windows & Linux, macOS & Android (beszéd Devices SDK-val)
- **Beszélgetés Beszédátírási**
  - C++/ Windows és Linux-környezetekkel
  - C#(Keretrendszer és a .NET Core) vagy Windows- és UWP- és Linux-környezetekkel
  - Java/Windows és Linux & Android (beszéd eszközök SDK)
- **Call Center Beszédátírási**
  - REST API- és bármilyen helyzetben használható
- **Kodek tömörített hangbemenet**
  - C++/Linux
  - C#/Linux
  - Java-/ Linux- és Android rendszerhez
