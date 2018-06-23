---
title: 'Bing egyéni keresés: Keressen egy egyéni nézet |} Microsoft Docs'
description: Ismerteti, hogyan keresse meg a webes egyéni képe
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 75f6c8d299c7eed901dda0631fca74b040f72e30
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347851"
---
# <a name="call-your-custom-search"></a>Az egyéni keresési hívás
Mielőtt meghozná a keresési eredmények lekérése a példányt az egyéni Search API első hívás, kell egy kognitív szolgáltatások előfizetés kulcs beszerzése. A kulcs egyéni keresési API-érdemes megtekinteni [kognitív szolgáltatások próbálja](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).

> [!NOTE]
> Meglévő Bing egyéni keresés ügyfelek rendelkező kiépítve előzetes kulcs, vagy azt megelőzően 2017. október 15 is kulcsokat kíván használni a November 30 2017, vagy amíg azok kimerített engedélyezett lekérdezések maximális számát. Ezt követően kell az Azure-on általánosan elérhető verziójára való áttérést.

## <a name="try-it-out"></a>Próbálja ki!
Miután konfigurálta az egyéni keresési élményt biztosít, tesztelheti a konfigurációt az egyéni keresési portálon. Jelentkezzen be a [egyéni keresés](https://customsearch.ai), kattintson egy egyéni keresési példányát, majd kattintson a **éles** fülre. A **végpontok** lap akkor jelenik meg. Az előfizetés határozza meg, hogy mely végpontokat érhetők el a próbálja című [lapok árképzési](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/). A végpont teszteléséhez a legördülő listából válassza ki azt, és adja meg a társított konfigurációs beállításokat. 

Az elérhető lehetőségek a következők.

- **Lekérdezés**: A keresési kifejezés kereséséhez. Csak Web-, kép, és automatikus javaslatokba végpontok esetén érhető el.
- **Egyéni konfiguráció azonosítója**: a kijelölt egyéni keresési példányát konfiguráció Azonosítóját. Ez a mező csak olvasható.
- **Piaci**: A piacon honnan származnak az eredményeket. Csak Web-, kép, és tárolt felhasználói felület végpontok esetén érhető el.
- **Előfizetés kulcs**: az Előfizetés kulcs tesztelni. Előfordulhat, hogy a legördülő listából válasszon ki egy kulcsot, vagy manuálisan adjon meg egy másikat.
- **Biztonságos keresési**: használatával szűrhetők a weblapok felnőtt tartalom szűrőt. Csak Web-, kép, és tárolt felhasználói felület végpontok esetén érhető el.
- **Count**: A keresési eredmények vissza a válaszban szereplő számát. Csak Web- és képfájlok végpontok esetén érhető el.
- **Eltolás**: A keresési eredmények vissza a válaszban szereplő számát. Csak Web- és képfájlok végpontok esetén érhető el.

A megadott webes, kép vagy automatikus javaslatokba minden szükséges beállításnak, kattintson **hívás** a JSON-válasz megtekintéséhez a jobb oldali ablaktáblán. 

Ha a felhasználói felület üzemeltetett végpont, tesztelheti a keresési élményt biztosít a jobb oldali ablaktáblán.

## <a name="next-steps"></a>További lépések
- [Az egyéni nézetben a C# hívása](./call-endpoint-csharp.md)
- [Az egyéni nézetben Java hívása](./call-endpoint-java.md)
- [Az egyéni nézetben, a NodeJs hívása](./call-endpoint-nodejs.md)
- [Az egyéni nézetben Python hívása](./call-endpoint-python.md)