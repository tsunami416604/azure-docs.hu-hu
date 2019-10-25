---
title: Védett webes API – áttekintés
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre védett webes API-t (áttekintés).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64418138f9eff61cc94a57be4dc5e2625e5fdebb
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803690"
---
# <a name="scenario-protected-web-api"></a>Forgatókönyv: védett webes API

Ebben a forgatókönyvben bemutatjuk, hogyan teheti elérhetővé a webes API-t, és hogyan védhető meg, hogy csak a hitelesített felhasználók férhessenek hozzá az API-hoz. A webes API használatához a hitelesített felhasználók a munkahelyi és az iskolai fiókkal, illetve a személyes Microsoft személyes fiókjaival is engedélyezhetők.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Sajátosságai

Az alábbiakban néhány, a webes API-k elleni védelemhez szükséges tudnivalókat talál:

- Az alkalmazás regisztrációja legalább egy hatókört ki kell mutatnia. A webes API által elfogadott jogkivonat-verzió a bejelentkezési közönségtől függ.
- A webes API-hoz tartozó kód konfigurációjának ellenőriznie kell a webes API meghívásakor használt jogkivonatot.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-protected-web-api-app-registration.md)
