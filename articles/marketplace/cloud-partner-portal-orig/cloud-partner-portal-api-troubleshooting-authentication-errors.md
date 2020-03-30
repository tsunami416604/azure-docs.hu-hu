---
title: Gyakori hitelesítési hibák elhárítása | Azure Piactér
description: Segítséget nyújt a felhőpartneri portál API-k használatakor a gyakori hitelesítési hibákhoz.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 1da9bbd1ed4bc4abea0699e56d8adc397086d6e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280422"
---
# <a name="troubleshooting-common-authentication-errors"></a>Gyakori hitelesítési hibák elhárítása

Ez a cikk segítséget nyújt a felhőbeli partnerportál API-k használatakor a gyakori hitelesítési hibákhoz.

## <a name="unauthorized-error"></a>Nem engedélyezett hiba

Ha következetesen `401 unauthorized` hibákat kap, ellenőrizze, hogy érvényes hozzáférési jogkivonatot tartalmaz-e.  Ha még nem tette meg, hozzon létre egy alapszintű Azure Active Directory (Azure AD) alkalmazást és egy egyszerű szolgáltatásszolgáltatást a Portal használata című részben leírtak szerint [egy Azure Active Directory-alkalmazás és egyszerű szolgáltatás létrehozásához, amely képes hozzáférni az erőforrásokhoz.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) Ezután használja az alkalmazást, vagy egy egyszerű HTTP POST kérés, hogy ellenőrizze a hozzáférést.  A bérlői azonosítót, az alkalmazásazonosítót, az objektumazonosítót és a titkos kulcsot a hozzáférési jogkivonat beszerzéséhez az alábbi képen látható módon fogja megadni:

![A 401-es hiba elhárítása](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Tiltott hibaüzenet

Ha hibaüzenetet `403 forbidden` kap, győződjön meg arról, hogy a megfelelő egyszerű szolgáltatás lett hozzáadva a közzétevői fiókjához a Cloud Partner Portalon.
Kövesse az Előfeltételek lap [lépéseit](./cloud-partner-portal-api-prerequisites.md) a szolgáltatásnév hozzáadása a portálhoz.

Ha a megfelelő egyszerű szolgáltatás lett hozzáadva, majd ellenőrizze az összes többi információt. Fordítson különös figyelmet a portálon megadott objektumazonosítóra. Az Azure Active Directory alkalmazás regisztrációs lapján két objektumazonosító található, és a helyi objektumazonosítót kell használnia. A megfelelő értéket megtalálhatja, ha megkeresi az alkalmazás **regisztrációi lapját,** és rákattint az alkalmazás nevére a **Helyi címtárban a Felügyelt alkalmazás csoportban.** Ezzel az alkalmazás helyi tulajdonságaihoz vezet, ahol megtalálhatja a megfelelő objektumazonosítót a **Tulajdonságok** lapon, az alábbi ábrán látható módon. Győződjön meg arról is, hogy a megfelelő közzétevő-azonosítót használja, amikor hozzáadja az egyszerű szolgáltatást, és kezdeményezi az API-hívást.

![A 403-as hiba elhárítása](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
