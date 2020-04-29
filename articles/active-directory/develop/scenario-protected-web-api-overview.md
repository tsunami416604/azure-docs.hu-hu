---
title: Védett webes API – áttekintés
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre védett webes API-t (áttekintés).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: cf66757d28a3883664aaacd85baad9cc0dea6956
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537202"
---
# <a name="scenario-protected-web-api"></a>Forgatókönyv: védett webes API

Ebből a forgatókönyvből megtudhatja, hogyan teheti elérhetővé a webes API-t. Azt is megtudhatja, hogyan védhető a webes API, hogy csak a hitelesített felhasználók férhessenek hozzá.

A webes API használatához engedélyeznie kell a hitelesített felhasználókat a munkahelyi és az iskolai fiókkal, vagy engedélyeznie kell a személyes Microsoft-fiókokat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Sajátosságai

Itt találja a webes API-k elleni védelemhez szükséges információkat:

- Az alkalmazás regisztrációja legalább egy hatókört ki kell mutatnia. A webes API által elfogadott jogkivonat-verzió a bejelentkezési közönségtől függ.
- A webes API-kód konfigurációjának ellenőriznie kell a webes API meghívásakor használt jogkivonatot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-protected-web-api-app-registration.md)
