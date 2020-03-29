---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77474234"
---
A tárolólekérdezések számlázása a. `ApiKey`

Az Azure Cognitive Services-tárolók nem rendelkeznek a mérési/számlázási végponthoz való csatlakozás nélküli futtatásra. Lehetővé kell tennie, hogy a tárolók mindig kommunikáljanak számlázási adatokat a számlázási végponttal. A Cognitive Services-tárolók nem küldenek ügyféladatokat, például az elemzett képet vagy szöveget a Microsoftnak.

### <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

A tároló futtatásához a számlázási argumentumok értékei nek kell futnia. Ezek az értékek lehetővé teszik, hogy a tároló csatlakozzon a számlázási végponthoz. A tároló 10–15 percenként jelenti a használatot. Ha a tároló nem csatlakozik az Azure-hoz a megengedett időablakon belül, a tároló továbbra is fut, de nem szolgál lekérdezéseket, amíg a számlázási végpont helyre nem áll. A kapcsolat 10-szer próbálkozik 10-15 perces időközzel. Ha nem tud csatlakozni a számlázási végponta a 10 próbálkozások, a tároló leáll a kérelmek kiszolgálása.

### <a name="billing-arguments"></a>Számlázási argumentumok

<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` A <span class="docon docon-navigate-external x-hidden-focus"></span> </a> parancs akkor indítja el a tárolót, ha az alábbi három beállítás érvényes értékkel rendelkezik:

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | A Cognitive Services-erőforrás API-kulcsa, amely a számlázási adatok nyomon követésére szolgál.<br/>Ennek a beállításnak az értékét a kiépített `Billing`erőforráshoz a megadott API-kulcsra kell állítani. |
| `Billing` | A Cognitive Services-erőforrás végpontja, amely a számlázási adatok nyomon követésére szolgál.<br/>Ennek a beállításnak az értékét egy kiépített Azure-erőforrás végpontURI-jára kell beállítani.|
| `Eula` | Azt jelzi, hogy elfogadta a tároló licencét.<br/>Ennek a beállításnak az értékét úgy kell beállítani, hogy elfogadja a **értéket.** |
