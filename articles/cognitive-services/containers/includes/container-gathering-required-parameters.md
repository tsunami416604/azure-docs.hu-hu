---
title: A szükséges paraméterek összegyűjtése
services: cognitive-services
author: aahill
manager: nitinme
description: A Cognitive Services összes tárolójának paraméterei
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875041"
---
## <a name="gathering-required-parameters"></a>A szükséges paraméterek összegyűjtése

A Cognitive Services összes tárolójának három elsődleges paramétere van, amelyek szükségesek. A végfelhasználói licencszerződésnek (EULA) a értékűnek `accept`kell lennie. Emellett mind az Endpoint URL-címre, mind az API-kulcsra szükség van.

### <a name="endpoint-uri-endpoint_uri"></a>Végpont URI-ja`{ENDPOINT_URI}`

Az **Endpoint** URI-értéke elérhető az Azure Portal *áttekintése* lapon a megfelelő Cognitive Service-erőforrás. Nyissa meg az *Áttekintés* lapot, mutasson `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> a Végpontra, és megjelenik egy ikon. Szükség esetén másolja és használja.

![Gyűjtse össze a végpont uri későbbi használatra](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Kulcsok`{API_KEY}`

Ez a kulcs a tároló indításához használható, és elérhető az Azure Portal Keys oldalán a megfelelő Cognitive Service-erőforrás. Keresse meg a *Kulcsok* lapot, `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> és kattintson az ikonra.

![A két billentyű egyikének beszereznie későbbi használatra](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Ezek az előfizetési kulcsok a Cognitive Service API eléréséhez használatosak. Ne ossza meg a kulcsait. Tárolja őket biztonságosan, például az Azure Key Vault használatával. Azt is javasoljuk, hogy rendszeresen regenerálja ezeket a kulcsokat. Csak egy kulcs szükséges egy API-hívás hoz. Az első kulcs újragenerálásakor a második kulccsal folyamatos hozzáférést biztosíthat a szolgáltatáshoz.