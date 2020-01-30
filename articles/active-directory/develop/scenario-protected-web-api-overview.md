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
ms.openlocfilehash: abe4ecf77e7a65251830ff822b15f79291471202
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773339"
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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-protected-web-api-app-registration.md)
