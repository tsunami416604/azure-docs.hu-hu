---
title: Védett webes API – áttekintés |} Az Azure
description: Ismerje meg, hogyan hozhat létre a védett webes API-k (áttekintés).
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074890"
---
# <a name="scenario-protected-web-api"></a>Forgatókönyv: Védett webes API-k

Ebben a forgatókönyvben megtudhatja, hogyan tehetők közzé a webes API-k és az API védelméről, így csak a hitelesített felhasználók hozzáférhetnek. Érdemes mindkét munkahelyi és iskolai fiókokhoz vagy személyes személyes Microsoft-fiókok a hitelesített felhasználók a webes API-t használja.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Tulajdonságairól

Az alábbiakban néhány adatai védelme érdekében a webes API-k ismernie kell:

- Az alkalmazásregisztráció közzé kell tennie, hogy legalább egy hatókör. Fogadja el a webes API a jogkivonat verzió a célközönség bejelentkezési függ.
- A webes API kódját konfigurációjától ellenőrizni kell a webes API hívása során használt jogkivonat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-protected-web-api-app-registration.md)
