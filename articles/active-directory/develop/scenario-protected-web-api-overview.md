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
ms.openlocfilehash: c9ff9ae811a29685937b922f04a277e663e26f1f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257364"
---
# <a name="scenario-protected-web-api"></a>Forgatókönyv: védett webes API

Ebből a forgatókönyvből megtudhatja, hogyan teheti elérhetővé a webes API-t. Azt is megtudhatja, hogyan védhető a webes API, hogy csak a hitelesített felhasználók férhessenek hozzá.

A webes API használatához engedélyeznie kell a hitelesített felhasználókat a munkahelyi és az iskolai fiókkal, vagy engedélyeznie kell a személyes Microsoft-fiókokat.

## <a name="specifics"></a>Sajátosságai

Itt találja a webes API-k elleni védelemhez szükséges információkat:

- Az alkalmazás regisztrációjának legalább egy *hatókört* vagy egy *alkalmazás-szerepkört*ki kell mutatnia.
  - A hatóköröket a felhasználók nevében meghívó webes API-k teszik közzé.
  - Az alkalmazási szerepköröket Daemon-alkalmazások által hívott webes API-k teszik közzé (amelyek a webes API-t a saját nevükön meghívja).
- Ha új webes API-alkalmazás regisztrációját hozza létre, válassza ki a webes API által elfogadott [hozzáférési jogkivonat verzióját](reference-app-manifest.md#accesstokenacceptedversion-attribute) a következőre: `2` . Az örökölt webes API-k esetében az elfogadott jogkivonat verziója lehet `null` , ez az érték azonban csak a szervezetek számára korlátozza a bejelentkezési célközönséget, és a személyes Microsoft-fiókokat (MSA) nem támogatja a rendszer.
- A webes API-kód konfigurációjának ellenőriznie kell a webes API meghívásakor használt jogkivonatot.
- A vezérlő műveleteiben szereplő kódnak ellenőriznie kell a tokenben lévő szerepköröket vagy hatóköröket.

## <a name="recommended-reading"></a>Ajánlott olvasás

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-protected-web-api-app-registration.md)
