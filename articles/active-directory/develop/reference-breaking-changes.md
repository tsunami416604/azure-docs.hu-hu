---
title: Az Azure Active Directory használhatatlanná tévő változásai hivatkozás |} A Microsoft Docs
description: Az Azure AD-protokollok, amely hatással lehet az alkalmazás végzett módosítások.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 14217d03cdb56c5c641ab8f8c3fc0748e8e815a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987767"
---
# <a name="whats-new-for-authentication"></a>Újdonságok a hitelesítéshez? 

|
>Értesítést kaphat arról, hogy nyissa meg újra a frissítések ezen a lapon hozzáadhatja ezt mikor [URL-cím](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) az RSS-hírcsatorna-olvasó.

A hitelesítési rendszere módosítja, és hozzáadja a funkciókat folyamatosan növelheti a biztonságot, és javíthatja a szabványoknak való megfelelés érdekében. Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

- A legújabb funkciók
- Ismert problémák
- Protokoll módosítások
- Elavult funkciók

> [!TIP] 
> Ez az oldal rendszeresen frissül, ezért újból megnyitni a gyakran. Ha nincs másképp jelölve, a módosítások csak kerüljenek helyen az újonnan regisztrált alkalmazások.  

## <a name="upcoming-changes"></a>Közelgő változások

### <a name="authorization-codes-can-no-longer-be-reused"></a>Engedélyezési kód már nem használható fel újra

**Hatályba lépés dátuma**: 2018. október 10 **érintett végpontok**: egyaránt 1.0-s és 2.0-s verziójú **érintett protokoll**: [Code folyamat](v2-oauth2-auth-code-flow.md)

2018. október 10., kezdve az Azure AD leáll, fogadja az előzőleg használt hitelesítési kódok új alkalmazások esetében. 2018. október 10. előtt létrehozott összes alkalmazás továbbra is fel szeretné használni a hitelesítési kódok képesek lesznek. Ez a változás segítséget nyújt ahhoz, hogy az Azure AD az OAuth-specifikációnak megfelelően, és alkalmazza a v1 és v2 végpontokon.

Ha az alkalmazás újból felhasználja a jogkivonatok lekérésére, több erőforrás-engedélyezési kódokat, javasoljuk, hogy a kód használatával egy frissítési jogkivonat lekérése, és a frissítési jogkivonat használatával más erőforrások kiegészítő jogkivonatok beszerzéséhez. Engedélyezési kód csak egyszer használhatók fel, de frissítési biztonsági jogkivonat használható többször több erőforrást. Minden olyan új alkalmazás, amely megpróbálja újból felhasználhatja a hitelesítési kódot az OAuth hitelesítésikód-folyamata során invalid_grant hiba lép fel.

További információ a frissítési biztonsági jogkivonat: [frissítése a hozzáférési jogkivonatok](v1-protocols-oauth-code.md#refreshing-the-access-tokens).

## <a name="may-2018"></a>2018. május

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID jogkivonatok nem használható a OBO folyamat

**Dátum**: 2018. május 1. **érintett végpontok**: egyaránt 1.0-s és 2.0-s verziójú **érintett protokollok**: Implicit folyamat és [OBO folyamat](v1-oauth2-on-behalf-of-flow.md)

2018. május 1. után id_tokens nem használható a OBO folyamatban a helyességi feltétel alkalmazása új alkalmazások számára.  Hozzáférési jogkivonatok helyette használandó API-kat, biztonságossá is között egy ügyfél és a középső réteg ugyanazzal az alkalmazással.  Mielőtt 2018. május 1. továbbra is működik, és képes az exchange-hozzáférési jogkivonat helyeként - id_tokens regisztrált alkalmazások azonban ez nem tekinthető a legjobb.

Annak érdekében, hogy a probléma megkerüléséhez, a következőket teheti:

1. Hozzon létre egy webes API-t a középső rétegbeli alkalmazás egy vagy több hatókört.  Ez lehetővé teszi, ennél a részletes ellenőrzés és biztonság.
1. Az alkalmazás-jegyzékfájl, az a [az Azure Portal](https://portal.azure.com), vagy a [alkalmazás regisztrációs portál](https://apps.dev.microsoft.com), győződjön meg arról, hogy az alkalmazás engedélyezett az implicit folyamatot keresztül hozzáférési jogkivonatokat kibocsátani. Ez védi a `oauth2AllowImplicitFlow` kulcsot.
1. Amikor az ügyfélalkalmazást igényel a keresztül id_token `response_type=id_token`, is kérheti a hozzáférési jogkivonat (`response_type=token`) a fent létrehozott webes API-hoz.  Így a v2.0-végpont használata esetén a `scope` paraméter hasonlóan kell kinéznie `api://GUID/SCOPE`.  Az 1.0-s verziójú végpont a `resource` paraméternek kell lennie az alkalmazás URI-ját a webes API-t.
1. A középső réteg az id_token helyett adja meg a hozzáférési jogkivonatot.  