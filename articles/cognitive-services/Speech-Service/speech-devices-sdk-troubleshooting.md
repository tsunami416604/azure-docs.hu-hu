---
title: A beszédfelismerési eszközök SDK – Beszédfelismerési szolgáltatás hibáinak elhárítása
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Beszédeszközök SDK használata során felmerülő problémák megoldásához nyújt segítséget.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815562"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>A Beszédeszközök SDK hibaelhárítása

Ez a cikk a Beszédeszközök SDK használata során felmerülő problémák megoldásához nyújt segítséget.

## <a name="certificate-failures"></a>Tanúsítványhibák

Ha tanúsítványhibákat kap a beszédfelismerési szolgáltatás használatakor, győződjön meg arról, hogy az eszköz a megfelelő dátummal és idővel rendelkezik:

1. Nyissa meg a **Beállítások lapot.** A **Rendszer csoportban**válassza **a Dátum & idő**lehetőséget.

    ![A Beállítások csoportban válassza a Dátum & idő lehetőséget.](media/speech-devices-sdk/qsg-12.png)

1. Tartsa be **az Automatikus dátum & idő** beállítást. Az **Időzóna kiválasztása**csoportban válassza ki az aktuális időzónát.

    ![Dátum- és időzóna-beállítások megadása](media/speech-devices-sdk/qsg-13.png)

    Amikor azt látja, hogy a fejlesztői készlet ideje megegyezik a számítógépen töltött idővel, a fejlesztői készlet csatlakozik az internethez.

## <a name="next-steps"></a>További lépések

* [A kibocsátási megjegyzések áttekintése](devices-sdk-release-notes.md)
