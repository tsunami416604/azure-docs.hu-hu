---
title: A Speech Devices SDK – Speech szolgáltatás hibáinak megoldása
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Speech Devices SDK használatakor felmerülő problémák megoldásához nyújt segítséget.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: 9a757402360b4b69e760315a809482be9b4e04d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558910"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>A Beszédeszközök SDK hibaelhárítása

Ez a cikk a Speech Devices SDK használatakor felmerülő problémák megoldásához nyújt segítséget.

## <a name="certificate-failures"></a>Tanúsítványhibák

Ha a beszédfelismerési szolgáltatások használatakor hibaüzenetet kap, győződjön meg arról, hogy az eszköz rendelkezik a megfelelő dátummal és időponttal:

1. Lépjen a **beállítások**. A **rendszer**válassza **dátum és idő**.

    ![A beállítások területen válassza ki a dátum és idő](media/speech-devices-sdk/qsg-12.png)

1. Tartsa a **automatikus dátum és idő** lehetőség van kijelölve. A **válassza időzóna**, válassza ki az aktuális időzóna.

    ![Válassza ki a dátum és időzóna-beállításai](media/speech-devices-sdk/qsg-13.png)

    Ha látja, hogy a fejlesztői csomag idő megegyezik-e a számítógépen lévő idő, a fejlesztői csomag csatlakozik az internethez.

## <a name="next-steps"></a>További lépések

* [Tekintse át a kibocsátási megjegyzések](devices-sdk-release-notes.md)
