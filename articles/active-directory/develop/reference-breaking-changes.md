---
title: Az Azure Active Directory használhatatlanná tévő változásai hivatkozás |} A Microsoft Docs
description: Ismerje meg, amely hatással lehet az alkalmazás az Azure AD-protokollok végzett módosítások.
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
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 266d9ff9ceb4aa71429a9afc3056d23d222a9c7b
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247371"
---
# <a name="whats-new-for-authentication"></a>Újdonságok a hitelesítéshez? 

>Ez a lap frissítésével kapcsolatos értesítést kaphat. Adja hozzá a [az URL-cím](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) az RSS-hírcsatorna-olvasó.

A hitelesítési rendszere módosítja, és hozzáadja a szolgáltatások rendszeresen biztonsági és megfelelőségi szabványoknak. A legújabb fejlemények az naprakész információkat, ez a cikk a következő információkat:

- Legújabb funkciókról
- Ismert problémák
- Protokoll módosítások
- Elavult funkciók

> [!TIP] 
> Ez az oldal rendszeresen frissül, így látogat. Ha nincs másképp jelölve, a módosítások csak kerüljenek helyen az újonnan regisztrált alkalmazások.  

## <a name="upcoming-changes"></a>Közelgő változások

### <a name="authorization-codes-can-no-longer-be-reused"></a>Engedélyezési kód már nem használható fel újra

**Hatályba lépés dátuma**: 2018. október 10 **érintett végpontok**: egyaránt 1.0-s és 2.0-s verziójú **érintett protokoll**: [Code folyamat](v2-oauth2-auth-code-flow.md)

2018. október 10., kezdve az Azure AD leáll, az alkalmazások korábban használt hitelesítési kódok elfogadásával. Ez a változás segítséget nyújt ahhoz, hogy az Azure AD az OAuth-specifikációnak megfelelően, és alkalmazza a v1 és v2 végpontokon.

Ha az alkalmazás újból felhasználja a jogkivonatok lekérésére, több erőforrás-engedélyezési kódokat, javasoljuk, hogy a kód használatával egy frissítési jogkivonat lekérése, és a frissítési jogkivonat használatával más erőforrások kiegészítő jogkivonatok beszerzéséhez. Engedélyezési kód csak egyszer használhatók fel, de frissítési biztonsági jogkivonat használható többször több erőforrást. Minden olyan új alkalmazás, amely megpróbálja újból felhasználhatja a hitelesítési kódot az OAuth hitelesítésikód-folyamata során invalid_grant hiba lép fel.

További információ a frissítési biztonsági jogkivonat: [frissítése a hozzáférési jogkivonatok](v1-protocols-oauth-code.md#refreshing-the-access-tokens).

> [!NOTE]
> Annak érdekében, hogy, néhány alkalmazások felosztása, lehetséges, a meglévő alkalmazások által használt, ez a szolgáltatás a kapott kivétel ez alól.  Minden alkalmazás több mint 10 bejelentkezések ezt a mintát a függő napi tekinthető biztosíthasson.  

## <a name="may-2018"></a>2018. május

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID jogkivonatok nem használható a OBO folyamat

**Dátum**: 2018. május 1. **érintett végpontok**: egyaránt 1.0-s és 2.0-s verziójú **érintett protokollok**: Implicit folyamat és [OBO folyamat](v1-oauth2-on-behalf-of-flow.md)

2018. május 1. után id_tokens nem használható a OBO folyamatban a helyességi feltétel alkalmazása új alkalmazások számára. Hozzáférési jogkivonatok helyette használandó API-kat, biztonságossá is között egy ügyfél és a középső réteg ugyanazzal az alkalmazással. Mielőtt 2018. május 1. továbbra is működik, és képes az exchange-hozzáférési jogkivonat helyeként; id_tokens regisztrált alkalmazás Ez azonban nem tekinthető a legjobb.

Ez a változás megkerüléséhez a következőket teheti:

1. Hozzon létre egy webes API-t a középső rétegbeli alkalmazás egy vagy több hatókört. Ez lehetővé teszi, ennél a részletes ellenőrzés és biztonság.
1. Az alkalmazás-jegyzékfájl, az a a [az Azure portal](https://portal.azure.com) vagy a [alkalmazás regisztrációs portál](https://apps.dev.microsoft.com), győződjön meg arról, hogy az alkalmazás engedélyezett az implicit folyamatot keresztül hozzáférési jogkivonatokat kibocsátani. Ennek segítségével lehet szabályozni az `oauth2AllowImplicitFlow` kulcsot.
1. Amikor az ügyfélalkalmazást igényel a keresztül id_token `response_type=id_token`, is kérheti a hozzáférési jogkivonat (`response_type=token`) a fent létrehozott webes API-hoz. Így a v2.0-végpont használata esetén a `scope` paraméter hasonlóan kell kinéznie `api://GUID/SCOPE`. Az 1.0-s verziójú végpont a `resource` paraméternek kell lennie az alkalmazás URI-ját a webes API-t.
1. A középső réteg az id_token helyett adja meg a hozzáférési jogkivonatot.  
