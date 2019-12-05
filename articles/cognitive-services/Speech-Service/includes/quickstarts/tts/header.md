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
ms.openlocfilehash: d19f779f67cc0dea8cc7f06aa275885d75c3092e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818354"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával konvertálja a szöveget a szintetizált beszédbe. Néhány előfeltétel kielégítése után a szintetizált beszéd az alapértelmezett hangszórókra való renderelése csak négy lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy ````SpeechConfig```` objektumot az előfizetési kulcsból és régióból.
> * Hozzon létre egy ````SpeechSynthesizer```` objektumot a fenti ````SpeechConfig```` objektum használatával.
> * Az ````SpeechSynthesizer```` objektum használatával beszéljen a szövegről.
> * Győződjön meg arról, hogy a hibákat a ````SpeechSynthesisResult```` visszaadja.
