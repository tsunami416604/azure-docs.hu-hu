---
title: 'Gyors útmutató: beszédfelismerési szolgáltatás szintézise'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan szintetizálhat beszédet a Speech SDK használatával
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503079"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával konvertálja a szöveget a szintetizált beszédbe. Néhány előfeltétel kielégítése után a szintetizált beszéd az alapértelmezett hangszórókra való renderelése csak négy lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy ````SpeechConfig```` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy ````SpeechSynthesizer```` objektumot a fenti ````SpeechConfig```` objektum használatával.
> * Az ````SpeechSynthesizer```` objektum használatával beszéljen a szövegről.
> * Győződjön meg arról, hogy a hibákat a ````SpeechSynthesisResult```` visszaadja.
