---
title: Azure-előfizetés összekapcsolása – Azure Active Directory B2C | Microsoft Docs
description: Lépésenkénti útmutató, amellyel egy Azure-előfizetésben helyezi az Azure AD B2C-bérlőt számlázás.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 01/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 892f47b6acf22c62ce2290e2ede9d0bcd21eefc8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065896"
---
# <a name="link-an-azure-subscription-to-an-azure-active-directory-b2c-tenant"></a>Azure-előfizetés összekapcsolása egy Azure Active Directory B2C Bérlővel

> [!IMPORTANT]
> A Azure Active Directory B2C (Azure AD B2C) használati számlázásával és díjszabásával kapcsolatos legfrissebb információkért tekintse meg a [Azure ad B2C díjszabását](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

Azure-előfizetéshez az Azure AD B2C használati díjakat számítjuk fel. Azure AD B2C-bérlő jön létre, ha a bérlői rendszergazda explicit módon az Azure AD B2C-bérlő összekapcsolása egy Azure-előfizetést kell. Ez a cikk bemutatja, hogyan.

> [!NOTE]
> Egy előfizetéshez társított Azure AD B2C-bérlő a számlázás az Azure AD B2C használatát vagy más Azure-erőforrások további Azure AD B2C-erőforrással együtt is használható.  Más Azure-licenc-alapú szolgáltatások vagy az Office 365-licencek az Azure AD B2C-bérlő belül hozzáadandó nem használható.

Hozzon létre egy Azure AD B2C-vel "erőforrás" belül a cél Azure-előfizetés az előfizetési hivatkozást érhető el. Számos Azure AD B2C-vel "resources" is létrehozható egy Azure-előfizetéssel, valamint más Azure-erőforrások (például virtuális gépeket, adattárolás, LogicApps) belül. Az erőforrások az előfizetésen belüli összes láthatja a az Azure AD-bérlővel, amely az előfizetés van társítva.

A Azure AD B2C támogatja az Azure Cloud Solution Providers (CSP) előfizetéseket. A funkció API-kkal vagy a Azure AD B2Choz és az összes Azure-erőforráshoz Azure Portal érhető el. A CSP-előfizetések rendszergazdái ugyanúgy kapcsolhatják össze, helyezhetik el és törölhetik a kapcsolatokat a Azure AD B2Cekkel, mint az összes Azure-erőforráshoz. A szerepköralapú hozzáférés-vezérlést használó Azure AD B2C felügyeletét a Azure AD B2C bérlő és az Azure CSP-előfizetés közötti társítás nem érinti. A szerepköralapú hozzáférés-vezérlést a bérlői Alapszerepkörök, nem előfizetés-alapú szerepkörök használatával érik el.

A folytatáshoz érvényes Azure-előfizetéssel van szükség.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

Első lépés [létrehozása az Azure AD B2C-bérlő](active-directory-b2c-get-started.md) , hogy szeretné-e az egy előfizetés összekapcsolása. Hagyja ki ezt a lépést, ha már létrehozott egy Azure AD B2C-bérlőben.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Az Azure AD-bérlővel, amely bemutatja az Azure-előfizetéshez az Azure portál megnyitása

Keresse meg az Azure AD-bérlővel, amely bemutatja az Azure-előfizetésében. Nyissa meg a [az Azure portal](https://portal.azure.com), és váltson át az Azure AD-bérlővel, amely bemutatja az Azure-előfizetéshez szeretne használni.

![Váltás az Azure AD-bérlő](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Keresse meg az Azure AD B2C az Azure Marketplace-en

Kattintson a **erőforrás létrehozása** gombra. A **Keresés a piactéren** mezőbe írja be `Active Directory B2C`a értéket.

![A "Active Directory B2C" nevű portál képernyőképe a piactéren – keresés](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Az eredmények listájában válassza ki a **Azure AD B2C-vel**.

![Az Azure AD B2C az eredménylistában kiválasztva](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Az Azure AD B2C-vel kapcsolatos részletek jelennek meg. Az új Azure Active Directory B2C-bérlő konfigurálásának megkezdéséhez kattintson a **Létrehozás** gombra.

Az erőforrás létrehozás képernyőn válassza ki a **hivatkozásra egy meglévő Azure AD B2C-bérlőt szeretne aktiválni az Azure-előfizetésem**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Hozzon létre egy Azure AD B2C-erőforrás Azure-előfizetésen belül

Erőforrás létrehozása párbeszédpanelen válassza ki az Azure AD B2C-bérlő a legördülő listából. Megjelenik a bérlők, amelynek Ön a globális rendszergazdája és azokkal, amelyek nem kapcsolódnak már egy előfizetéshez.

Az Azure AD B2C-erőforrás nevét a rendszer megfelelően az Azure AD B2C-bérlő tartományneve előre kiválasztott.

Az előfizetéshez válassza ki az aktív Azure-előfizetésre, amelynek Ön a rendszergazdája.

Válasszon egy erőforráscsoportot és az erőforráscsoport helyét. A kijelölt itt nem befolyásolja az Azure AD B2C-bérlő földrajzi, a teljesítmény vagy a számlázási állapota.

![A Azure Portal Azure AD B2C erőforrás-létrehozási lapja](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Az Azure AD B2C-bérlői erőforrások kezelése

Az Azure AD B2C-erőforrás Azure-előfizetésen belül sikeres létrehozása után megtekintheti a típus "B2C-bérlő" mellett más Azure-erőforrások hozzá egy új erőforrást.

Ehhez az erőforráshoz is használhatja:

- Keresse meg az előfizetés számlázási információk áttekintéséhez.
- Nyissa meg az Azure AD B2C-bérlő
- Támogatási kérés küldése
- Helyezze át az Azure AD B2C-bérlő erőforrás, egy másik Azure-előfizetésre, vagy egy másik erőforráscsoportba.

![A B2C erőforrás-beállítások lapja a Azure Portal](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.PNG)

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Azure AD B2C bérlő számlázási előfizetésének módosítása

Azure AD B2C bérlők áthelyezhetők egy másik előfizetésbe, ha a forrás-és a cél-előfizetések ugyanabban a Azure Active Directory-bérlőn belül vannak.

Ha szeretné megismerni, hogyan helyezhetők át az Azure-erőforrások, például a Azure AD B2C bérlője egy másik előfizetésbe, tekintse meg az [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md)

Mielőtt elkezdené az áthelyezést, olvassa el a teljes cikket, amely részletesen ismerteti az ilyen áthelyezésre vonatkozó korlátozásokat és követelményeket. Az erőforrások áthelyezésére vonatkozó utasítások mellett olyan kritikus információkat is tartalmaz, mint például az áthelyezés előtti ellenőrzőlista, valamint a mozgatási művelet ellenőrzése.

## <a name="known-issues"></a>Ismert problémák

### <a name="self-imposed-restrictions"></a>Önálló kivetett korlátozásokat

A felhasználó létrehozott egy regionális korlátozást az Azure-erőforrások létrehozásához. Ez a korlátozás előfordulhat, hogy az Azure AD B2C-erőforrás létrehozása. Megoldás érdekében adjon enyhíteni ezt a korlátozást.

## <a name="next-steps"></a>További lépések

Ha ezeket a lépéseket az Azure AD B2C-bérlők mindegyike befejeződött, az Azure-előfizetés elszámolási megfelelően az Azure Direct és a nagyvállalati szerződés adatait.

A használati és számlázási adatait tekintheti meg a kiválasztott Azure-előfizetésen belül. Emellett áttekintheti a részletes-– napi használati jelentések használata a [reporting API használati](active-directory-b2c-reference-usage-reporting-api.md).
