---
title: Azure-előfizetés összekapcsolása az Azure Active Directory B2C |} A Microsoft Docs
description: Lépésenkénti útmutató, amellyel egy Azure-előfizetésben helyezi az Azure AD B2C-bérlőt számlázás.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/05/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 80ba42d7eab1726c7add6c4c9426b7dde3b55480
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445941"
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Egy Azure-előfizetés Azure AD B2C-bérlő csatolása

> [!IMPORTANT]
> A legfrissebb információkat, a számlázással és a díjszabás az Azure AD B2C-vel van, a következő használat: [Azure AD B2C-díjszabás](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

Azure-előfizetéshez az Azure AD B2C használati díjakat számítjuk fel. Azure AD B2C-bérlő jön létre, ha a bérlői rendszergazda explicit módon az Azure AD B2C-bérlő összekapcsolása egy Azure-előfizetést kell. Ez a cikk bemutatja, hogyan.

> [!NOTE]
> Egy előfizetéshez társított Azure AD B2C-bérlő csak akkor használható, az Azure AD B2C használatát a számlázását. Az előfizetés nem használható más Azure-szolgáltatások vagy az Office 365-licencek hozzáadásához *belül az Azure AD B2C-bérlő*.

 Hozzon létre egy Azure AD B2C-vel "erőforrás" belül a cél Azure-előfizetés az előfizetési hivatkozást érhető el. Számos Azure AD B2C-vel "resources" is létrehozható egy Azure-előfizetéssel, valamint más Azure-erőforrások (például virtuális gépeket, adattárolás, LogicApps) belül. Az erőforrások az előfizetésen belüli összes láthatja a az Azure AD-bérlővel, amely az előfizetés van társítva.

A folytatáshoz érvényes Azure-előfizetéssel van szükség.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

Első lépés [létrehozása az Azure AD B2C-bérlő](active-directory-b2c-get-started.md) , hogy szeretné-e az egy előfizetés összekapcsolása. Hagyja ki ezt a lépést, ha már létrehozott egy Azure AD B2C-bérlőben.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Az Azure AD-bérlővel, amely bemutatja az Azure-előfizetéshez az Azure portál megnyitása

Keresse meg az Azure AD-bérlővel, amely bemutatja az Azure-előfizetésében. Nyissa meg a [az Azure portal](https://portal.azure.com), és váltson át az Azure AD-bérlővel, amely bemutatja az Azure-előfizetéshez szeretne használni.

![Váltás az Azure AD-bérlő](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Keresse meg az Azure AD B2C az Azure Marketplace-en

Kattintson a **erőforrás létrehozása** gombra. A **Keresés a piactéren** mezőbe írja be a következőt: `B2C`.

![Kiemelt Hozzáadás gomb és az Azure AD B2C szöveg a Keresés a piactéren mezőben](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Az eredmények listájában válassza ki a **Azure AD B2C-vel**.

![Az Azure AD B2C az eredménylistában kiválasztva](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Az Azure AD B2C-vel kapcsolatos részletek jelennek meg. Az új Azure Active Directory B2C-bérlő konfigurálásának megkezdéséhez kattintson a **Létrehozás** gombra.

Az erőforrás létrehozás képernyőn válassza ki a **hivatkozásra egy meglévő Azure AD B2C-bérlőt szeretne aktiválni az Azure-előfizetésem**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Hozzon létre egy Azure AD B2C-erőforrás Azure-előfizetésen belül

Erőforrás létrehozása párbeszédpanelen válassza ki az Azure AD B2C-bérlő a legördülő listából. Megjelenik a bérlők, amelynek Ön a globális rendszergazdája és azokkal, amelyek nem kapcsolódnak már egy előfizetéshez.

Az Azure AD B2C-erőforrás nevét a rendszer megfelelően az Azure AD B2C-bérlő tartományneve előre kiválasztott.

Az előfizetéshez válassza ki az aktív Azure-előfizetésre, amelynek Ön a rendszergazdája.

Válasszon egy erőforráscsoportot és az erőforráscsoport helyét. A kijelölt itt nem befolyásolja az Azure AD B2C-bérlő földrajzi, a teljesítmény vagy a számlázási állapota.

![B2C-erőforrás létrehozása](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Az Azure AD B2C-bérlői erőforrások kezelése

Az Azure AD B2C-erőforrás Azure-előfizetésen belül sikeres létrehozása után megtekintheti a típus "B2C-bérlő" mellett más Azure-erőforrások hozzá egy új erőforrást.

Ehhez az erőforráshoz is használhatja:

- Keresse meg az előfizetés számlázási információk áttekintéséhez.
- Nyissa meg az Azure AD B2C-bérlő
- Támogatási kérés küldése
- Helyezze át az Azure AD B2C-bérlő erőforrás, egy másik Azure-előfizetésre, vagy egy másik erőforráscsoportba.

![B2C-erőforrás beállítások](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Ismert problémák

### <a name="csp-subscriptions"></a>CSP-előfizetésekben

Jelenleg az Azure AD B2C-bérlő **nem** CSP-előfizetésekben mutató hivatkozást.

### <a name="self-imposed-restrictions"></a>Önálló kivetett korlátozásokat

A felhasználó létrehozott egy regionális korlátozást az Azure-erőforrások létrehozásához. Ez a korlátozás előfordulhat, hogy az Azure AD B2C-erőforrás létrehozása. Megoldás érdekében adjon enyhíteni ezt a korlátozást.

## <a name="next-steps"></a>További lépések

Ha ezeket a lépéseket az Azure AD B2C-bérlők mindegyike befejeződött, az Azure-előfizetés elszámolási megfelelően az Azure Direct és a nagyvállalati szerződés adatait.

A használati és számlázási adatait tekintheti meg a kiválasztott Azure-előfizetésen belül. Emellett áttekintheti a részletes-– napi használati jelentések használata a [reporting API használati](active-directory-b2c-reference-usage-reporting-api.md).
