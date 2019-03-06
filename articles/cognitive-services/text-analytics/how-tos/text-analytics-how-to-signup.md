---
title: Iratkozzon fel a szövegelemzési API
titleSuffix: Azure Cognitive Services
description: Utasítások a regisztráció és a Text Analytics szolgáltatással.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 53532a19482a33f8727e71d44ae169225b5b1c98
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449957"
---
# <a name="how-to-sign-up-for-the-text-analytics-api"></a>Regisztráció a szövegelemzési API

A Text Analytics erőforrásai folyamatosan elérhetők a felhőben. Tartalmainak elemzésre való feltöltése előtt regisztrálnia kell a hozzáférési kulcs beszerzéséhez. Az API minden egyes hívásához hozzáférési kulcsot kell csatolni a kéréshez.

+ Először is egy Azure-előfizetésre lesz szüksége. A költségvonzat nélküli kísérletezéshez létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

+ A **Text Analytics API** kiválasztásával létrehozhat egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). A kulcsot a rendszer a regisztráció során állítja elő.

A Text Analytics esetében a megismeréshez és kiértékeléshez elérhető egy ingyenes szint, az éles számítási feladatokhoz tartozó szintek viszont fizetősek. Az egyes előfizetésekben rendelkezhet több feliratkozások: egy ingyenes, egy fizetős, és így tovább. Ha a kérések száma megnő, átválthat egy több tranzakciót biztosító szintre.

Az előzetes verziójú szolgáltatásokhoz és az ingyenes szinthez nem tartozik szolgáltatói szerződés. További információt a [Cognitive Services szolgáltatói szerződését](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/) ismertető szakaszban talál.

## <a name="how-to-change-tiers"></a>Szint módosítása

Az első lépésekhez használjon ingyenes szintet, majd az éles számítási feladatok esetében váltson át fizetős szintre. A fizetős szolgáltatás különböző szintekre osztva érhető el. Szintváltáskor megtarthatja a végpontot és a hozzáférési kulcsokat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és [keresse meg a szolgáltatást](text-analytics-how-to-access-key.md).

2. Kattintson az **Árkategória** elemre.

   ![Árkategória parancs a bal oldali navigációs menüben](../media/portal-pricing-tier.png)

3. Válasszon ki egy szintet, majd kattintson a **Kiválasztás** elemre.  Az új korlátok a választás feldolgozását követően azonnal érvénybe lépnek. 

   ![A szintválasztási oldal csempéi és a Kiválasztás gomb](../media/portal-choose-tier.png)

## <a name="how-billing-works"></a>A számlázás működése

A számlázás a tranzakciószám alapján történik. Havi számlázással vásárolhat egy tranzakcióblokkot az adott szinten, majd ennek felhasználása után tranzakciókként egy kisebb többletköltséggel kell számolnia. Ha rendszeresen túllépi a felső korlátot, akkor érdemes lehet magasabb szintre váltania.

További információkat a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) talál.

### <a name="what-constitutes-a-transaction-in-the-text-analytics-api"></a>Mi minősül tranzakciónak a Text Analytics API-ban?
A dokumentumokhoz készített minden egyes annotáció tranzakciónak minősül. A kötegelt kiértékelési hívások esetében a rendszer az adott tranzakcióban kiértékelendő dokumentumok számát is figyelembe veszi. 1000 dokumentum egyetlen API-hívásban hangulatelemzésre való elküldése például 1000 tranzakciónak számít.

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzáférési kulcs lekérése](text-analytics-how-to-access-key.md)
