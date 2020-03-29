---
title: Első lépések – Fordítószöveg API
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan regisztrálhatja magát az Azure Cognitive Services Translator Text API-ra, és hogyan szerezhet be egy előfizetési kulcsot.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: cfae5318213e8af164c8d0cc9ed378705a5644b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73835692"
---
# <a name="how-to-sign-up-for-the-translator-text-api"></a>Hogyan lehet feliratkozni a Translator Text API-ra?

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

- Nincs fiókja? A költségvonzat nélküli kísérletezéshez létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
- Már van fiókja? [Bejelentkezés](https://ms.portal.azure.com/)

## <a name="create-a-subscription-to-the-translator-text-api"></a>Előfizetés létrehozása a Translator Text API-ra

Miután bejelentkezett a portálra, az alábbiak szerint hozhat létre előfizetést a Translator Text API-hoz:

1. Válassza a **+ Erőforrás létrehozása** lehetőséget.
1. A **Keresés a Piactéren** keresőmezőbe írja be a **Fordító szövegét,** majd jelölje ki az eredmények közül.
1. Válassza a **Létrehozás** lehetőséget az előfizetés részleteinek meghatározásához.
1. A **tarifacsomag-listából** válassza ki azt a tarifacsomagot, amely a legjobban megfelel az igényeinek.
    1. Minden előfizetéshez ingyenes szint van. Az ingyenes szint ugyanazokat a funkciókat és funkciókat tartalmazza, mint a fizetős csomagok, és nem jár le.
    1. Csak egy ingyenes előfizetése lehet a fiókjához.
1. Az előfizetés létrehozásának befejezéséhez válassza a **Létrehozás** lehetőséget.

## <a name="authentication-key"></a>Hitelesítési kulcs

Amikor feliratkozik a Fordító szövegére, az előfizetésére vonatkozó egyedi, személyre szabott hozzáférési kulcsot kap. Ez a kulcs szükséges a Translator Text API minden egyes hívásához.

1. A hitelesítési kulcs lekéréséhez először válassza ki a megfelelő előfizetést.
1. Válassza a **Kulcsok lehetőséget** az előfizetés **részleteinek Erőforrás-kezelés** szakaszában.
1. Másolja az előfizetéshez felsorolt kulcsok egyikét.

## <a name="learn-test-and-get-support"></a>Tanulás, tesztelés és támogatás

- [Példakódpéldák a GitHubon](https://github.com/MicrosoftTranslator)
- [Microsoft Fordító támogatási fóruma](https://www.aka.ms/TranslatorForum)

A Microsoft Translator általában hagyja, hogy az első néhány kérelem át, mielőtt ellenőrizte az előfizetési fiók állapotát. Ha az első néhány Microsoft Translator API-kérelem sikeres, akkor a hívások sikertelenek lesznek, a hibaválasz jelzi a problémát. Naplózza az API-választ, hogy láthassa az okát.

## <a name="pricing-options"></a>Árképzési lehetőségek

- [Translator Text API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>Testreszabás

Az Egyéni fordító segítségével testreszabhatja a fordításokat, és létrehozhat egy fordítási rendszert a saját terminológiájához és stílusához, kezdve az általános Microsoft Translator neurális gépi fordítási rendszerekből. [További információ](customization.md)

## <a name="additional-resources"></a>További források

- [Első lépések az Azure-ral (3 perces videó)](https://azure.microsoft.com/get-started/?b=16.24)
- [Hogyan kell fizetni egy számla](https://azure.microsoft.com/pricing/invoicing/)
