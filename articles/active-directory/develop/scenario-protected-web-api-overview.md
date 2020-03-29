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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773339"
---
# <a name="scenario-protected-web-api"></a>Eset: Védett webes API

Ebben a forgatókönyvben megtudhatja, hogyan teheti elérhetővé a webes API-t. Azt is megtudhatja, hogyan védheti meg a webes API-t, hogy csak a hitelesített felhasználók férhessenek hozzá.

A webes API használatához engedélyeznie kell a hitelesített felhasználókat munkahelyi és iskolai fiókkal, vagy engedélyeznie kell a Microsoft személyes fiókjait.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Sajátosságai

Itt vannak olyan konkrét információk, amelyeket ismernie kell a webes API-k védelme érdekében:

- Az alkalmazásregisztrációnak legalább egy hatókört meg kell tennie. A webes API által elfogadott token verzió a bejelentkezési közönségtől függ.
- A webes API kódkonfigurációjának ellenőriznie kell a webes API meghívásakor használt jogkivonatot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-protected-web-api-app-registration.md)
