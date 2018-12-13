---
title: Gyakori hitelesítési hibák elhárítása |} A Microsoft Docs
description: A gyakori hitelesítési hibák segítségnyújtást kínál a Cloud Partner Portal API-k használata esetén.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 12ed395855b9d870f8f6e3564dc5b3b899b9c6c1
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313870"
---
# <a name="troubleshooting-common-authentication-errors"></a>Gyakori hitelesítési hibák elhárítása

Ez a cikk nyújt segítséget a gyakori hitelesítési hibák, a Cloud Partner Portal API-k használata esetén.

## <a name="unauthorized-error"></a>Jogosulatlan hiba

Ha rendszeresen kap `401 unauthorized` hibát, ellenőrizze, hogy rendelkezik-e egy érvényes hozzáférési jogkivonatot.  Ha még nem tette meg, hozzon létre egy alapszintű Azure Active Directory (Azure AD-) alkalmazás és a egy egyszerű szolgáltatást leírtak szerint [egy Azure Active Directory-alkalmazás és -erőforrásokeléréséhezszolgáltatásnévlétrehozásaaportálon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Ezután használja az alkalmazás vagy egy egyszerű HTTP POST-kérelmet a hozzáférés ellenőrzéséhez.  Akkor tartalmazza a bérlő Azonosítóját, Alkalmazásazonosítót, objektumazonosító: és a titkos kulcs a hozzáférési jogkivonat beszerzése az alábbi képen látható módon:

![A 401-es hiba elhárítása](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Tiltott hibaüzenet

Ha egy `403 forbidden` hiba, győződjön meg arról, hogy a megfelelő szolgáltatásnévhez hozzáadta-e a Cloud Partner portálra a kiadói fiókjában.
Kövesse a [Előfeltételek](./cloud-partner-portal-api-prerequisites.md) lapot, az egyszerű szolgáltatást a portálra.

Ha hozzá lett adva a megfelelő szolgáltatásnévhez, majd ellenőrizze az egyéb információkat. Az Objektumazonosító elolvassa az utólagos adta meg a portálon. Az Azure Active Directory alkalmazásregisztrációs oldalán találhatók két objektum-azonosító, és kell használni a helyi objektum azonosítóját. Annak a megfelelő értéket a a **alkalmazásregisztrációk** lapja az alkalmazás, és kattintson az alkalmazás neve mellett a **felügyelt alkalmazás a helyi címtárban**. Ekkor megjelenik az alkalmazás, ahol megtalálhatja a helyes Objektumazonosítóját a helyi tulajdonságai a **tulajdonságok** lapon, az alábbi ábrán látható módon. Is győződjön meg arról, hogy a megfelelő közzétevő-azonosító hozzáadása a szolgáltatásnévhez, és állítsa be az API-hívás.

![A 403-as hiba elhárítása](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
