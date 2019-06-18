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
ms.openlocfilehash: d844b171ff99dc97e5d1107bcb745f9e8d5b3e9d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65519829"
---
# <a name="scenario-availability"></a>A forgatókönyv rendelkezésre állása

A Speech service SDK számos forgatókönyv funkciókat, számos programozási nyelvet és a környezetek között.  Nem minden esetben még jelenleg az összes programozási nyelvet, vagy minden környezetben érhető el.  Az alábbiakban van az egyes forgatókönyvek rendelkezésre állását.

- **Beszédfelismerés (SR), kifejezéslista, szándék, fordítás és a helyszíni tárolók**
  - Minden programozási nyelvet/környezetben, ahol a nyílra kattintva <img src="media/index/link.jpg" height="15" width="15"></img> a rövid útmutató tábla [Itt](https://aka.ms/csspeech).
- **Szövegfelolvasás**
  - C++/ Windows és Linux-környezetekkel
  - C#/Windows
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
