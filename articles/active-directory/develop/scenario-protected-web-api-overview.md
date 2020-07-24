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
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4e530f76c8301dc74f73b675befa6f0710aedab7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026628"
---
# <a name="scenario-protected-web-api"></a>Forgatókönyv: védett webes API

Ebből a forgatókönyvből megtudhatja, hogyan teheti elérhetővé a webes API-t. Azt is megtudhatja, hogyan védhető a webes API, hogy csak a hitelesített felhasználók férhessenek hozzá.

A webes API használatához engedélyeznie kell a hitelesített felhasználókat a munkahelyi és az iskolai fiókkal, vagy engedélyeznie kell a személyes Microsoft-fiókokat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Sajátosságai

Itt találja a webes API-k elleni védelemhez szükséges információkat:

- Az alkalmazás regisztrációjának legalább egy *hatókört* vagy egy *alkalmazás-szerepkört*ki kell mutatnia.
  - A hatóköröket a felhasználók nevében meghívó webes API-k teszik közzé.
  - Az alkalmazási szerepköröket Daemon-alkalmazások által hívott webes API-k teszik közzé (amelyek a webes API-t a saját nevükön meghívja).
- Ha új webes API-alkalmazás regisztrációját hozza létre, válassza ki a webes API által elfogadott [hozzáférési jogkivonat verzióját](reference-app-manifest.md#accesstokenacceptedversion-attribute) a következőre: `2` . Az örökölt webes API-k esetében az elfogadott jogkivonat verziója lehet `null` , ez az érték azonban csak a szervezetek számára korlátozza a bejelentkezési célközönséget, és a személyes Microsoft-fiókokat (MSA) nem támogatja a rendszer.
- A webes API-kód konfigurációjának ellenőriznie kell a webes API meghívásakor használt jogkivonatot.
- A vezérlő műveleteiben szereplő kódnak ellenőriznie kell a tokenben lévő szerepköröket vagy hatóköröket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-protected-web-api-app-registration.md)
