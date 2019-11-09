---
title: Első lépések – Translator Text API
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan regisztrálhat az Azure Cognitive Services Translator Text API, és hogyan szerezhet be előfizetési kulcsot.
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
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835692"
---
# <a name="how-to-sign-up-for-the-translator-text-api"></a>Regisztráció a Translator Text APIra

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

- Még nincs fiókja? A költségvonzat nélküli kísérletezéshez létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
- Van már fiókja? [bejelentkezés](https://ms.portal.azure.com/)

## <a name="create-a-subscription-to-the-translator-text-api"></a>Előfizetés létrehozása a Translator Text API

Miután bejelentkezett a portálra, létrehozhat egy előfizetést a Translator Text API a következőképpen:

1. Válassza a **+ Erőforrás létrehozása** lehetőséget.
1. A **Keresés a piactéren** mezőbe írja be **Translator Text** , majd válassza ki az eredmények közül.
1. Válassza a **Létrehozás** lehetőséget az előfizetés részleteinek megadásához.
1. A **díjszabási** csomag listából válassza ki az igényeinek leginkább megfelelő árképzési szintet.
    1. Az egyes előfizetések ingyenes szintűek. Az ingyenes csomag ugyanazokkal a szolgáltatásokkal és funkciókkal rendelkezik, mint a fizetős csomagok, és nem jár le.
    1. A fiókjához csak egy ingyenes előfizetés tartozhat.
1. Válassza a **Létrehozás** lehetőséget az előfizetés létrehozásának befejezéséhez.

## <a name="authentication-key"></a>Hitelesítési kulcs

Translator Textra való regisztrációkor egy személyre szabott hozzáférési kulcsot kap, amely egyedi az előfizetésében. Ezt a kulcsot kötelező megadni a Translator Text API minden egyes hívásakor.

1. Kérje le a hitelesítési kulcsot úgy, hogy először kiválasztja a megfelelő előfizetést.
1. Az előfizetés részleteinek **Erőforrás-kezelés** szakaszában válassza a **kulcsok** lehetőséget.
1. Másolja a felsorolt kulcsok valamelyikét az előfizetéséhez.

## <a name="learn-test-and-get-support"></a>Tanulás, tesztelés és támogatás kérése

- [Példák a GitHubra](https://github.com/MicrosoftTranslator)
- [Microsoft Translator támogatási fórum](https://www.aka.ms/TranslatorForum)

A Microsoft Translator általában lehetővé teszi az első néhány kérelem továbbítását, mielőtt ellenőrizte az előfizetési fiók állapotát. Ha az első néhány Microsoft Translator API-kérelem sikeres, akkor a hívások sikertelenek lesznek, a hiba válasza a problémát jelzi. Jelentkezzen be az API-válaszba, hogy láthassa az okát.

## <a name="pricing-options"></a>Árképzési lehetőségek

- [Translator Text API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>Testreszabás

Az egyéni fordító segítségével testre szabhatja a fordításokat, és létrehozhatja a saját terminológiája és stílusa alapján létrehozott fordítási rendszert, amely az általános Microsoft Translator neurális gépi fordítási rendszerekből indul. [További információ](customization.md)

## <a name="additional-resources"></a>További források

- [Ismerkedés az Azure-ban (3 perces videó)](https://azure.microsoft.com/get-started/?b=16.24)
- [Fizetés számlával](https://azure.microsoft.com/pricing/invoicing/)
