---
title: Gyakori hitelesítési hibák elhárítása |} Az Azure Marketplace-en
description: A gyakori hitelesítési hibák segítségnyújtást kínál a Cloud Partner Portal API-k használata esetén.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ddf3c9ce26a1538d91f1e6d6bcc04fd0d18e7936
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935805"
---
# <a name="troubleshooting-common-authentication-errors"></a>Gyakori hitelesítési hibák elhárítása

Ez a cikk nyújt segítséget a gyakori hitelesítési hibák, a Cloud Partner Portal API-k használata esetén.

## <a name="unauthorized-error"></a>Jogosulatlan hiba

Ha rendszeresen kap `401 unauthorized` hibát, ellenőrizze, hogy rendelkezik-e egy érvényes hozzáférési jogkivonatot.  Ha még nem tette meg, hozzon létre egy alapszintű Azure Active Directory (Azure AD-) alkalmazás és a egy egyszerű szolgáltatást leírtak szerint [egy Azure Active Directory-alkalmazás és -erőforrásokeléréséhezszolgáltatásnévlétrehozásaaportálon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Ezután használja az alkalmazás vagy egy egyszerű HTTP POST-kérelmet a hozzáférés ellenőrzéséhez.  Akkor tartalmazza a bérlő Azonosítóját, Alkalmazásazonosítót, objektumazonosító: és a titkos kulcs a hozzáférési jogkivonat beszerzése az alábbi képen látható módon:

![A 401-es hiba elhárítása](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Forbidden error

Ha egy `403 forbidden` hiba, győződjön meg arról, hogy a megfelelő szolgáltatásnévhez hozzáadta-e a Cloud Partner portálra a kiadói fiókjában.
Kövesse a [Előfeltételek](./cloud-partner-portal-api-prerequisites.md) lapot, az egyszerű szolgáltatást a portálra.

Ha hozzá lett adva a megfelelő szolgáltatásnévhez, majd ellenőrizze az egyéb információkat. Az Objektumazonosító elolvassa az utólagos adta meg a portálon. Az Azure Active Directory alkalmazásregisztrációs oldalán találhatók két objektum-azonosító, és kell használni a helyi objektum azonosítóját. Annak a megfelelő értéket a a **alkalmazásregisztrációk** lapja az alkalmazás, és kattintson az alkalmazás neve mellett a **felügyelt alkalmazás a helyi címtárban**. Ekkor megjelenik az alkalmazás, ahol megtalálhatja a helyes Objektumazonosítóját a helyi tulajdonságai a **tulajdonságok** lapon, az alábbi ábrán látható módon. Is győződjön meg arról, hogy a megfelelő közzétevő-azonosító hozzáadása a szolgáltatásnévhez, és állítsa be az API-hívás.

![A 403-as hiba elhárítása](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
