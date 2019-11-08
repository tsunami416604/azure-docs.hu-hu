---
title: Gyakori hitelesítési hibák elhárítása | Azure piactér
description: A Cloud Partner Portal API-k használatakor a gyakori hitelesítési hibákkal kapcsolatos segítséget nyújt.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 0990e9aedf17f6d4ad01e4911e47efd60001f3d7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827371"
---
# <a name="troubleshooting-common-authentication-errors"></a>Gyakori hitelesítési hibák elhárítása

Ez a cikk a Cloud Partner Portal API-k használatakor a gyakori hitelesítési hibákkal kapcsolatos segítséget nyújt.

## <a name="unauthorized-error"></a>Jogosulatlan hiba

Ha folyamatosan `401 unauthorized` hibákat kap, ellenőrizze, hogy rendelkezik-e érvényes hozzáférési jogkivonattal.  Ha még nem tette meg, hozzon létre egy alapszintű Azure Active Directory (Azure AD) alkalmazást és egy egyszerű szolgáltatásnevet a következő témakörben ismertetett módon: a [portál használatával hozzon létre egy Azure Active Directory alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Ezután használja az alkalmazást vagy egy egyszerű HTTP POST-kérelmet a hozzáférés ellenőrzéséhez.  A hozzáférési jogkivonat beszerzéséhez a bérlő AZONOSÍTÓját, az alkalmazás AZONOSÍTÓját, az objektumazonosító és a titkos kulcsot kell megadnia, ahogy az a következő képen látható:

![Az 401-es hiba elhárítása](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Tiltott hiba

Ha `403 forbidden` hibát kap, győződjön meg arról, hogy a megfelelő egyszerű szolgáltatásnév hozzá lett adva a közzétevő fiókjához a Cloud Partner Portal.
Az [Előfeltételek](./cloud-partner-portal-api-prerequisites.md) lapon szereplő lépések végrehajtásával adja hozzá a szolgáltatásnevet a portálhoz.

Ha a megfelelő szolgáltatásnevet adta hozzá, akkor ellenőrizze az összes többi információt. Ügyeljen arra, hogy a portálon megjelenő objektumazonosító kiemelten legyen. A Azure Active Directory-alkalmazás regisztrációja lapon két objektumazonosító szerepel, és a helyi objektumazonosítót kell használnia. Az alkalmazás **Alkalmazásregisztrációk** lapjára kattintva megtalálhatja a megfelelő értéket, és az alkalmazás nevére kattinthat a **helyi címtárban található felügyelt alkalmazás**területen. Ekkor megjelenik az alkalmazás helyi tulajdonságai, ahol az alábbi ábrán látható módon megtalálhatja a megfelelő objektumazonosítót a **Tulajdonságok** lapon. Továbbá győződjön meg arról, hogy a megfelelő közzétevő AZONOSÍTÓját használja az egyszerű szolgáltatás hozzáadásakor, és az API-hívást.

![Az 403-es hiba elhárítása](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
