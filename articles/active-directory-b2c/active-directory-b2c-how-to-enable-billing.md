---
title: "Azure-előfizetés csatolása az Azure AD B2C |} Microsoft Docs"
description: "Részletes útmutató az Azure-előfizetések az Azure AD B2C-bérlő számlázási engedélyezéséhez."
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: joroja
ms.openlocfilehash: 5b9955b2af7f20a79981315fa33a0eb5380a5465
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="linking-an-azure-subscription-to-an-azure-b2c-tenant-to-pay-for-usage-charges"></a>Azure-előfizetéssel összekapcsolása egy Azure B2C-bérlő használati díjak díj ellenében

A folyamatban lévő használati díjak, az Azure Active Directory B2C (vagy az Azure AD B2C) Azure-előfizetéshez vannak számlázva. A bérlői rendszergazda kifejezetten az Azure AD B2C bérlő összekapcsolása egy Azure-előfizetés létrehozása a B2C-bérlő maga után szükség.  Ez a hivatkozás érhető el, hozzon létre egy Azure AD "B2C-bérlő" erőforrás a cél Azure-előfizetés. Sok B2C bérlő kapcsolható egy Azure-előfizetéssel együtt más Azure-erőforrások (például virtuális gépeket, adatokat tároló, LogicApps)


> [!IMPORTANT]
> A legfrissebb információkat, a számlázással és a díjszabással B2C érték a következő oldalon használati: [az Azure AD B2C árazás](
https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="step-1---create-an-azure-ad-b2c-tenant"></a>1. lépés – az Azure AD B2C bérlő létrehozása
B2C bérlő létrehozásakor először el kell végezni. Hagyja ki ezt a lépést, ha már létrehozta a cél B2C-bérlő. [Ismerkedés az Azure AD B2C-vel](active-directory-b2c-get-started.md)

## <a name="step-2---open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Nyissa meg Azure-portálon az Azure AD-bérlő bemutató az Azure-előfizetéshez a, 2 -. lépés
Lépjen az [Azure Portalra](https://portal.azure.com). Váltás az Azure AD-bérlő bemutató az Azure-előfizetéshez szeretne használni. Az Azure AD-bérlő eltér a B2C-bérlő. Az Azure portálon kattintson a jobb felső sarkában az irányítópulton, jelölje be az Azure AD-bérlő meg a fiók nevét. Azure-előfizetésre van szükség a folytatáshoz. [Azure-előfizetés igénylése](https://account.windowsazure.com/signup?showCatalog=True)

![Az Azure AD-bérlő Váltás](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="step-3---create-a-b2c-tenant-resource-in-azure-marketplace"></a>3. lépés - a B2C-bérlő erőforrás létrehozása az Azure piactéren
Nyissa meg a piactér piactér ikonra kattintva, vagy válassza a zöld "+" az irányítópult bal felső sarokban.  Keresse meg és jelölje ki az Azure Active Directory B2C. Válassza ki a létrehozása.

![Válassza ki a piactér](./media/active-directory-b2c-how-to-enable-billing/marketplace.png)

![Keresési AD B2C](./media/active-directory-b2c-how-to-enable-billing/searchb2c.png)

Az Azure AD B2C-erőforrás létrehozása párbeszédpanel magában foglalja az alábbi paraméterekkel:

1. Az Azure AD B2C-bérlő – a legördülő listából válassza ki az Azure AD B2C-bérlő.  Csak jogosult az Azure AD B2C-bérlők megjelenítése.  Jogosult B2C-bérlők ezen feltételeknek megfelelő: a B2C bérlő globális rendszergazdája, és a B2C-bérlő nincs jelenleg társított Azure-előfizetéshez

2. Az Azure AD B2C erőforrásnév - van megadva a B2C-bérlő tartománynevét kereséséhez

3. Előfizetés - rendszergazdaként vagy társadminisztrátorának aktív Azure-előfizetéssel.  Több Azure AD B2C bérlő fel egy Azure-előfizetéssel

4. Erőforráscsoport és az erőforráscsoport helye – Ez az összetevő segítségével több Azure-erőforrások.  Ez a beállítás nincs hatással van a B2C bérlő helyét, a teljesítmény vagy a számlázási állapota

5. Rögzítés az irányítópulton a B2C bérlő számlázási adatokat és a B2C bérlő beállítások legegyszerűbb elérésére ![B2C erőforrás létrehozása](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="step-4---manage-your-b2c-tenant-resources-optional"></a>4. lépés - a B2C-bérlő (nem kötelező) erőforrások kezelése
Központi telepítés befejezése után a egy új "B2C-bérlő" erőforrás a célként megadott erőforráscsoportja jön létre, és a kapcsolódó Azure-előfizetés.  Meg kell jelennie egy új erőforrást "B2C-bérlő" hozzáadott típusú mellett az egyéb Azure-erőforrások.

![B2C-erőforrás létrehozása](./media/active-directory-b2c-how-to-enable-billing/b2cresourcedashboard.png)

A B2C bérlő erőforrás kattint, le is tudja
- Kattintson az előfizetés nevét, hogy tekintse át a számlázási adatokat. Tekintse meg a számlázási és használat.
- Kattintson az Azure AD B2C beállítások gombra egy új böngészőlapon közvetlenül a a B2C-bérlő beállítások panel
- Támogatási kérés küldése
- Egy másik Azure-előfizetést, vagy egy másik erőforráscsoportban, helyezze át a B2C bérlő erőforrás.  A választott módosításokat, mely Azure-előfizetés kap hálózathasználati költségeket.

![B2C erőforrás-beállítások](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Ismert problémák
- B2C-bérlő törlését. A B2C-bérlő jön létre, ha törli, és újra létre ilyen nevű tartomány, is törölje és hozza létre újból az "Linking" erőforrás a ugyanazon tartomány nevét.  Az "Összes erőforrás" a "Linking" erőforrás megtalálja az Azure-portálon az előfizetés-bérlőben.
- Önálló megállapított korlátozás regionális erőforrás helye.  Ritka esetekben a felhasználók létrehozott Azure-erőforrás létrehozása regionális korlátozás.  Ez a korlátozás előfordulhat, hogy az Azure-előfizetés és a B2C-bérlő közötti kapcsolat létrehozását. A mérséklése érdekében enyhíteni a ezt a korlátozást.

## <a name="next-steps"></a>Következő lépések
Ha ezeket a lépéseket az egyes a B2C-bérlők befejeződött, az Azure-előfizetéshez az Azure közvetlen vagy a nagyvállalati szerződés részletei megfelelően lesz számlázva.
- Tekintse át a használati és elszámolási belül a kijelölt Azure-előfizetés
- Tekintse át a részletes--napi használati jelentések használata a [használati Reporting API](active-directory-b2c-reference-usage-reporting-api.md)
