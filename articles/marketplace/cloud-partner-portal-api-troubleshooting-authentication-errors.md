---
title: Gyakori hitelesítési hibák elhárítása | Azure piactér
description: A Cloud Partner Portal API-k használatakor a gyakori hitelesítési hibákkal kapcsolatos segítséget nyújt.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 7684e097118fce4dcd471257ec0d9d476ce342be
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964923"
---
# <a name="troubleshooting-common-authentication-errors"></a>Gyakori hitelesítési hibák elhárítása

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a partneri központtal, és továbbra is működni fognak, miután az ajánlatokat áttelepítik a partner központba. Az integráció kis változásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működni fog a partneri központba való Migrálás után.

Ez a cikk a Cloud Partner Portal API-k használatakor a gyakori hitelesítési hibákkal kapcsolatos segítséget nyújt.

## <a name="unauthorized-error"></a>Jogosulatlan hiba

Ha folyamatosan hibákat kap `401 unauthorized` , ellenőrizze, hogy rendelkezik-e érvényes hozzáférési jogkivonattal.  Ha még nem tette meg, hozzon létre egy alapszintű Azure Active Directory (Azure AD) alkalmazást és egy egyszerű szolgáltatásnevet a következő témakörben ismertetett módon: a [portál használatával hozzon létre egy Azure Active Directory alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz](../active-directory/develop/howto-create-service-principal-portal.md). Ezután használja az alkalmazást vagy egy egyszerű HTTP POST-kérelmet a hozzáférés ellenőrzéséhez.  A hozzáférési jogkivonat beszerzéséhez a bérlő AZONOSÍTÓját, az alkalmazás AZONOSÍTÓját, az objektumazonosító és a titkos kulcsot kell megadnia, ahogy az a következő képen látható:

![Az 401-es hiba elhárítása](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Tiltott hibaüzenet

Ha `403 forbidden` hibaüzenetet kap, győződjön meg arról, hogy a megfelelő egyszerű szolgáltatásnév hozzá lett adva a közzétevő fiókjához a Cloud Partner Portal.
Az [Előfeltételek](./cloud-partner-portal-api-prerequisites.md) lapon szereplő lépések végrehajtásával adja hozzá a szolgáltatásnevet a portálhoz.

Ha a megfelelő szolgáltatásnevet adta hozzá, akkor ellenőrizze az összes többi információt. Ügyeljen arra, hogy a portálon megjelenő objektumazonosító kiemelten legyen. A Azure Active Directory-alkalmazás regisztrációja lapon két objektumazonosító szerepel, és a helyi objektumazonosítót kell használnia. Az alkalmazás **Alkalmazásregisztrációk** lapjára kattintva megtalálhatja a megfelelő értéket, és az alkalmazás nevére kattinthat a **helyi címtárban található felügyelt alkalmazás**területen. Ekkor megjelenik az alkalmazás helyi tulajdonságai, ahol az alábbi ábrán látható módon megtalálhatja a megfelelő objektumazonosítót a **Tulajdonságok** lapon. Továbbá győződjön meg arról, hogy a megfelelő közzétevő AZONOSÍTÓját használja az egyszerű szolgáltatás hozzáadásakor, és az API-hívást.

![Az 403-es hiba elhárítása](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
