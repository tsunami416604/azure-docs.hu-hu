---
title: "Azure-előfizetések és a fiókok aktiválásához |} Microsoft Docs"
description: "Engedélyezze a hozzáférést az Azure Resource Manager API-k használatával új és meglévő fiókok és közös fiók problémák megoldásához."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 03/01/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: a0dc2ee201c1729b10cd363553cdf5d61ec87748
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="activate-azure-subscriptions-and-accounts-with-azure-cost-management"></a>Azure-előfizetések és a fiókok az Azure költség Management aktiválása

Létrehozásától vagy frissítésétől egészen a hitelesítő adatait Azure Resource Manager lehetővé teszi az Azure költség kezelését az összes a fiókok és -előfizetések az Azure-bérlő belül. Ha az Azure Diagnostics bővítmény engedélyezve van a virtuális gépeken is van, majd Azure költség felügyeleti is gyűjtéséhez kiterjesztett például CPU és memória. Ez a cikk ismerteti az Azure Resource Manager API-k használatával új és meglévő fiókok hozzáférés engedélyezése. Azt is ismerteti, hogyan közös fiók problémák megoldásához.

Azure költség felügyeleti nem tud hozzáférni az Azure-előfizetés adatok többségét, ha az előfizetés _aktivált_. Szerkesztenie kell _aktivált_ fiókok, hogy az Azure költség felügyeleti érhetik el azokat.

## <a name="required-azure-permissions"></a>Az Azure szükséges engedélyek

Hajtsa végre a cikkben ismertetett adott engedélyekre van szükség. Ön vagy a bérlői rendszergazda egyaránt a következő engedélyekkel kell rendelkeznie:

- A CloudynCollector alkalmazás regisztrálása az Azure AD-bérlő engedély.
- Az alkalmazást az Azure-előfizetések a szerepkör lehetővé teszi.

Az Azure-előfizetését, rendelkeznie kell a fiókok `Microsoft.Authorization/*/Write` rendelje hozzá a CloudynCollector alkalmazás eléréséhez. Ez a művelet biztosítja a [tulajdonos](../active-directory/role-based-access-built-in-roles.md#owner) szerepkör vagy [felhasználói hozzáférés adminisztrátora](../active-directory/role-based-access-built-in-roles.md#user-access-administrator) szerepkör.

Ha a fiók hozzá van rendelve a **közreműködő** szerepkör, Önnek nincs megfelelő engedélye arra, hogy az alkalmazást. Arra vonatkozó hibaüzenetet kap, az Azure-előfizetése CloudynCollector alkalmazás hozzárendelésekor.

### <a name="check-azure-active-directory-permissions"></a>Azure Active Directory-engedélyek ellenőrzése

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com).
2. Válassza ki az Azure-portálon **Azure Active Directory**.
3. Válassza ki az Azure Active Directoryban, **felhasználói beállítások**.
4. Ellenőrizze a **App regisztrációk** lehetőséget.
    - Ha az érték **Igen**, akkor a nem rendszergazda felhasználók regisztrálhatják AD alkalmazásaiban. Ez a beállítás azt jelenti, hogy egyetlen felhasználóhoz sem az Azure AD-bérlő regisztrálhatja az alkalmazást. Szükséges Azure-előfizetése engedélyei lépne.  
    ![Alkalmazás-regisztráció](./media/activate-subs-accounts/app-register.png)
    - Ha a **App regisztrációk** beállítás **nem**, akkor csak a bérlői rendszergazda felhasználók regisztrálhatja az Azure Active Directory-alkalmazásokat. A bérlői rendszergazda regisztrálnia kell az CloudynCollector alkalmazást.


## <a name="add-an-account-or-update-a-subscription"></a>Vegyen fel egy fiókot, vagy egy előfizetés frissítése

Fiók frissítés előfizetés hozzáadásakor Azure költség felügyeleti hozzáférés engedélyezése az Azure adatait.

### <a name="add-a-new-account-subscription"></a>Adja hozzá egy új fiókot (előfizetés)

1. Az Azure költség felügyeleti portálon, kattintson a jobb felső fogaskerék szimbólum, és válassza ki **felhő fiókok**.
2. Kattintson a **új fiókot felveszi** és a **új fiókot felveszi** mezőben jelenik meg. Adja meg a szükséges adatokat.  
    ![Adja hozzá az új fiók használata](./media/activate-subs-accounts//add-new-account.png)

### <a name="update-a-subscription"></a>Előfizetés frissítése

1. Ha frissíteni szeretne egy _aktivált_ -előfizetéssel, amely már létezik Azure költség felügyeleti a fiókok kezelése, kattintson a Szerkesztés ceruza szimbólumra jobb oldalán a szülő _GUID bérlői_. Előfizetések a szülő bérlői szerint vannak csoportosítva, ezért kerülje az előfizetések aktiválásával külön-külön.
    ![Az előfizetések újbóli felderítése](./media/activate-subs-accounts/existing-sub.png)
2. Ha szükséges, adja meg a bérlő azonosítóját. Ha nem ismeri a bérlő azonosítója, az alábbi lépések segítségével találja meg:
    1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com).
    2. Válassza ki az Azure-portálon **Azure Active Directory**.
    3. Válassza ki ahhoz, hogy a bérlő azonosítója, **tulajdonságok** az Azure AD-bérlő.
    4. Másolja a Directory azonosító GUID. Ez az érték az a bérlő azonosítója.
    További információkért lásd: [-bérlőazonosító beszerzése](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
3. Ha szükséges, válassza ki a sebesség azonosítóját. Ha nem ismeri a sebesség Azonosítóját, a következő lépések segítségével találja meg.
    1. A jobb felső az Azure portál, kattintson a felhasználói adatokat, és kattintson a **megtekintése a számlázási**.
    2. A **számlázási**, kattintson a **előfizetések**.
    3. A **a saját előfizetések**, válassza ki az előfizetést.
    4. Az azonosító alatt látható arány **kínálnak azonosító**. Másolja az ajánlat Azonosítót az előfizetéshez.
4. Az új fiók hozzáadása (vagy előfizetés szerkesztése) párbeszédpanelen kattintson **mentése** (vagy **következő**). A program átirányítja az Azure-portálon.
5. Jelentkezzen be a portálra. Kattintson a **elfogadás** engedélyezése Azure költség felügyeleti adatgyűjtő elérni az Azure-fiókjával.

    Program átirányítja az Azure költség felügyeleti fiókok kezelése lap, és az előfizetésben **aktív** Fiókállapot. Az erőforrás-kezelő oszlopban egy zöld pipa szimbólumot jeleníti.

    Ha egy vagy több előfizetés nem egy zöld pipa szimbólumot lát, azt jelenti, hogy Önnek nincs engedélye az előfizetés (CloudynCollector) reader alkalmazás létrehozásához. Az előfizetés magasabb szintű engedélyekkel rendelkező felhasználó kell ismételje meg a folyamatot.

Tekintse meg a [csatlakozás az Azure Resource Manager Azure költség felügyeleti](https://youtu.be/oCIwvfBB6kk) videót, amely végigvezeti a folyamatot.

>[!VIDEO https://www.youtube.com/embed/oCIwvfBB6kk?ecver=1]

## <a name="resolve-common-indirect-enterprise-set-up-problems"></a>Közvetett vállalati telepítés kapcsolatos gyakori problémák megoldásához

Ha először az Azure költség felügyeleti portál, láthatja az alábbi üzenetek, egy nagyvállalati szerződés vagy a Cloud Solution Provider (CSP) felhasználók:

- *A megadott API-kulcs az nem a legfelső szintű regisztrációs kulcs* jelenik meg a **beállítva fel Azure költség felügyeleti** varázsló.
- *Közvetlen regisztráció – nem* jelenik meg a nagyvállalati szerződés portálon.
- *Az elmúlt 30 napban nem használati adatok található. Lépjen kapcsolatba a terjesztőt, hogy ellenőrizze, hogy engedélyezték a jelölés során az Azure-fiókjával* az Azure költség felügyeleti portál megjeleníti.

Az előző üzenetekből kiderül, hogy őnála Azure nagyvállalati szerződéssel vagy CSP vásárolt. Engedélyeznie kell a viszonteladóhoz vagy a CSP _markup_ az az Azure fiók, hogy az adatok Azure költség felügyeleti megtekintheti.

A problémák megoldásával kapcsolatban itt található:

1. Engedélyeznie kell a viszonteladó tud _markup_ a fiókjához. Útmutatásért lásd: a [közvetett ügyfél bevezetési útmutató](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).
2. Létrehozhat az Azure nagyvállalati szerződéssel kulcs Azure költség felügyeleti való használatra. Útmutatásért lásd: [regisztrálni egy Azure nagyvállalati szerződéssel és adatok megtekintése](https://docs.microsoft.com/en-us/azure/cost-management/quick-register-ea).

Csak egy Azure szolgáltatás-rendszergazda engedélyezheti költség felügyeletet. Közös rendszergazdai engedélyek nem elegendők.

Mielőtt Azure költség kezelő Azure nagyvállalati szerződés API-kulcsot is létrehozhat, engedélyeznie kell az Azure számlázási API által megadott utasítások:

- [A vállalati ügyfelek a Reporting API-k – áttekintés](../billing/billing-enterprise-api.md)
- [A Microsoft Azure enterprise portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI) alatt **adataihoz a API-val való hozzáférés engedélyezése**

Is szükség lehet ahhoz, hogy megkapja a részleg, a fiókhoz tulajdonosainak és a vállalati rendszergazdák engedélyeket _díjak megtekintése_ számlázási API-val.

## <a name="next-steps"></a>További lépések

- Ha még nem fejezte első oktatóanyaga, amely költség-kezelésre, olvassa el a [tekintse át a használati és költségek](tutorial-review-usage.md).
