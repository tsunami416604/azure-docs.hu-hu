---
title: Az állapot-tároló példányának ellenőrzése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ellenőrizheti az állapotfigyelő példány állapotát.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 1a8ce0bc94c61a0cfe6cdad11375763ba954957d
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122513"
---
### <a name="verify-that-a-container-is-running"></a>Annak ellenőrzése, hogy a tároló fut-e

1. Válassza az **Áttekintés** lapot, és másolja ki az IP-címet.
1. Nyisson meg egy új böngésző fület, és adja meg az IP-címet. Írja be például a `http://<IP-address>:5000 (http://55.55.55.55:5000` következőt:). Megjelenik a tároló kezdőlapja, amelyből megtudhatja, hogy fut-e a tároló.

    ![A tároló kezdőlapjának megtekintése annak ellenőrzéséhez, hogy fut-e](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Válassza ki a **szolgáltatás API leírása** hivatkozást, hogy megnyissa a tároló hencegő lapját.

1. Válassza ki a **post** API-k bármelyikét, és válassza a **kipróbálás**lehetőséget. Megjelennek a paraméterek, például a bemenetek.

Több URL-címet is használhat annak ellenőrzéséhez, hogy a tároló fut-e.

|Kérés|Cél|
|--|--|
|`http://localhost:5000/`|A tároló egy kezdőlappal rendelkezik.|
|`http://localhost:5000/ready`|A GET használatával a rendszer ellenőrzi, hogy a tároló készen áll-e a modellre irányuló lekérdezés elfogadására. Ez a kérelem az Kubernetes [és készültségi](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)tesztekhez használható.|
|`http://localhost:5000/status`|A GET, a/Ready végponthoz hasonlóan a rendszer ellenőrzi, hogy a tároló fut-e, és nincs-e lekérdezés a modellen. Ez a kérelem az Kubernetes [és készültségi](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)tesztekhez használható.|
|`http://localhost:5000/swagger`|Ezen az URL-címen a tároló teljes körű dokumentációt biztosít a végpontokhoz és a `Try it now` szolgáltatásokhoz. Ezzel a funkcióval megadhatja a beállításokat egy webalapú HTML-űrlapon, és anélkül teheti meg a lekérdezést, hogy kódot kellene írnia. A lekérdezés visszaadása után egy példa CURL-parancs van megadva a szükséges HTTP-fejlécek és-szövegtörzs bemutatásához. |
|`http://localhost:5000/demo`| Böngészőn keresztül igényelt, ez a funkció interaktív vizualizációt biztosít a bemeneti szöveges minták vagy egy Ön által megadott lekérdezések eredményeiről.  |

A kérelem URL-címének használata – `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health` egy lekérdezés küldése a tárolónak.
