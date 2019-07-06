---
title: SDK - beszédszolgáltatások beszédfelismerési eszközök hibaelhárítása
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Speech eszközök SDK használata során esetlegesen jelentkező problémák megoldásához információkat nyújt.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: f55171a177dfcbebb9bc6df5ce125a8f29494946
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606299"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>A Beszédeszközök SDK hibaelhárítása

Ez a cikk a Speech eszközök SDK használata során esetlegesen jelentkező problémák megoldásához információkat nyújt.

## <a name="certificate-failures"></a>Tanúsítványhibák

Ha tanúsítványhibák a Speech Services használata közben, győződjön meg arról, hogy az eszköz rendelkezik-e a helyes dátum és idő:

1. Lépjen a **beállítások**. A **rendszer**válassza **dátum és idő**.

    ![A beállítások területen válassza ki a dátum és idő](media/speech-devices-sdk/qsg-12.png)

1. Tartsa a **automatikus dátum és idő** lehetőség van kijelölve. A **válassza időzóna**, válassza ki az aktuális időzóna.

    ![Válassza ki a dátum és időzóna-beállításai](media/speech-devices-sdk/qsg-13.png)

    Ha látja, hogy a fejlesztői csomag idő megegyezik-e a számítógépen lévő idő, a fejlesztői csomag csatlakozik az internethez.

## <a name="next-steps"></a>További lépések

* [Tekintse át a kibocsátási megjegyzések](devices-sdk-release-notes.md)
