---
title: "Azure-előfizetés csatolása az Azure AD B2C |} Microsoft Docs"
description: "Részletes útmutató az Azure-előfizetések az Azure AD B2C-bérlő számlázási engedélyezéséhez."
services: active-directory-b2c
documentationcenter: dev-center-name
author: parakhj
manager: krassk
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2017
ms.author: parja
ms.openlocfilehash: 35fab74abf2c2ba27a8bf99eb93eb53f39b26227
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Azure-előfizetéssel összekapcsolása egy Azure AD B2C bérlő

> [!IMPORTANT]
> A legfrissebb információkat, a számlázással és a díjszabással az Azure AD B2C jelenleg a következő lap használati: [az Azure AD B2C árazás](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

Használati díjak az Azure AD B2C az Azure-előfizetéshez vannak számlázva. Azure AD B2C-bérlő jön létre, amikor a bérlői rendszergazdának kell explicit módon csatolja az Azure AD B2C bérlő Azure-előfizetéssel. Ez a cikk bemutatja, hogyan.

> [!NOTE]
> Csatolva az Azure AD B2C-bérlő előfizetés csak az Azure AD B2C használatának számlázási használható. Az előfizetés nem használható más Azure-szolgáltatások vagy az Office 365 licencek hozzáadásához *belül az Azure AD B2C bérlő*.

 Az előfizetési hivatkozásra az érhető el az Azure AD B2C "erőforrás" a cél Azure-előfizetés létrehozása. Sok Azure AD B2C "források" is létrehozható egy Azure-előfizetéssel, valamint más Azure-erőforrások (a példában a virtuális gépeket, adatokat tároláshoz, LogicApps) belül. Nyissa meg az előfizetéshez tartozó Azure AD-bérlő láthat minden az előfizetésen belüli erőforrásokon.

A folytatáshoz egy érvényes Azure-előfizetés szükséges.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

Először [az Azure AD B2C bérlő létrehozása](active-directory-b2c-get-started.md) , hogy szeretné-e előfizetés hivatkozásra. Hagyja ki ezt a lépést, ha már létrehozott egy Azure AD B2C-bérlő.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Nyissa meg Azure-portál, amely tartalmazza az Azure-előfizetéshez az Azure AD-bérlőben

Nyissa meg az Azure AD-bérlő bemutató az Azure-előfizetéshez. Nyissa meg a [Azure-portálon](https://portal.azure.com), és váltson a bemutató az Azure-előfizetéshez szeretne használni az Azure AD-bérlő.

![Az Azure AD-bérlő Váltás](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Az Azure AD B2C található az Azure piactéren

Kattintson az **Új** gombra. A **Keresés a piactéren** mezőbe írja be a következőt: `B2C`.

![A Keresés a piactér mező gomb és a szöveg az Azure AD B2C hozzáadása](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Az eredmények listájában válassza **az Azure AD B2C**.

![Az Azure AD B2C az eredmények listájában kijelölt](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Az Azure AD B2C adatait jelennek meg. Az új Azure Active Directory B2C-bérlő konfigurálásának megkezdéséhez kattintson a **Létrehozás** gombra.

Az erőforrás létrehozása képernyőn válassza ki a **kapcsolat egy meglévő Azure AD B2C bérlő számára az Azure-előfizetésem**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Az Azure AD B2C erőforrás belül az Azure-előfizetés létrehozása

Az erőforrás létrehozása párbeszédpanelen válassza ki az Azure AD B2C bérlő a legördülő listából. A bérlő globális rendszergazdája, valamint azokat, amelyeket már nem tartozó előfizetés képező összes jelenik meg.

Az Azure AD B2C-erőforrás nevét a rendszer az Azure AD B2C-bérlő tartománynevét megfelelően előre kiválasztott.

Az előfizetéshez válasszon egy aktív Azure-előfizetéssel, amelynek Ön a rendszergazdája.

Válasszon egy erőforráscsoportot és az erőforráscsoport helye. A kijelölés itt nincs hatással az Azure AD B2C bérlő helyét, a teljesítmény vagy a számlázási állapot rendelkezik.

![B2C-erőforrás létrehozása](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenent-resources"></a>Az Azure AD B2C-bérlő erőforrások kezeléséhez

Az Azure AD B2C-erőforrás van sikeres létrehozását követően az Azure-előfizetés belül meg kell jelennie egy új erőforrás típusa "B2C bérlő" hozzáadva az egyéb Azure-erőforrások mellett.

Ez az erőforrás használhatja:

- Nyissa meg az előfizetés számlázási információk áttekintéséhez.
- Nyissa meg az Azure AD B2C-bérlő
- Támogatási kérés küldése
- Egy másik Azure-előfizetésre, vagy egy másik erőforráscsoportban, helyezze át az Azure AD B2C bérlő erőforrás.

![B2C erőforrás-beállítások](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Ismert problémák

### <a name="csp-subscriptions"></a>Kriptográfiai Szolgáltató előfizetések

Jelenleg az Azure AD B2C-bérlő **nem** CSP előfizetések mutató hivatkozást.

### <a name="self-imposed-restrictions"></a>Önálló megállapított korlátozások

A felhasználó létrehozott Azure-erőforrás létrehozása regionális korlátozás. Ez a korlátozás előfordulhat, hogy az Azure AD B2C-erőforrást. A mérséklése érdekében enyhíteni a ezt a korlátozást.

## <a name="next-steps"></a>Következő lépések

Ha ezeket a lépéseket az Azure AD B2C-bérlő minden befejeződött, az Azure-előfizetéshez az Azure közvetlen vagy a nagyvállalati szerződés részletei megfelelően lesz számlázva.

A használati és a számlázási tudnivalókról tekintheti meg a kijelölt Azure-előfizetés belül. Emellett áttekintheti a részletes--napi használati jelentések használata a [reporting API-val használati](active-directory-b2c-reference-usage-reporting-api.md).
