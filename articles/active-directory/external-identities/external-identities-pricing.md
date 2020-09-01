---
title: MAU számlázási modell az Azure AD külső identitásokhoz
description: Ismerje meg az Azure AD külső identitások havi aktív felhasználók (MAU) számlázási modelljét a vendég felhasználói együttműködéshez (B2B) az Azure AD-ben. Ismerje meg, hogyan kapcsolhat Azure AD-bérlőt egy Azure-előfizetéshez.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12aa400d6ca44043d3d90e78a93ae49d97a927e8
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270150"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Számlázási modell az Azure AD külső identitásokhoz

Azure Active Directory (Azure AD) külső identitások díjszabása havi aktív felhasználók (MAU) alapján történik, amely a naptári hónapon belül a hitelesítési tevékenységgel rendelkező egyedi felhasználók száma. Ez a számlázási modell az Azure AD vendég felhasználói együttműködésre (B2B) és [Azure ad B2C bérlőre](https://docs.microsoft.com/azure/active-directory-b2c/billing)is vonatkozik. A MAU számlázási szolgáltatással csökkentheti a költségeket, és az ingyenes szintet, valamint a rugalmas és kiszámítható díjszabást kínál. Ebben a cikkben megismerheti a MAU-számlázást és az Azure AD-bérlők előfizetéshez való összekapcsolását.

> [!IMPORTANT]
> Ez a cikk nem tartalmazza a díjszabás részleteit. A használati számlázással és a díjszabással kapcsolatos legfrissebb információkért tekintse meg a [Azure Active Directory díjszabását](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-i-need-to-do"></a>Mit kell tennem?

A MAU-számlázás kihasználása érdekében az Azure AD-bérlőt egy Azure-előfizetéshez kell kapcsolni.

|Ha a bérlő:  |A következőket kell tennie:  |
|---------|---------|
| Egy előfizetéshez már csatolva van egy Azure AD-bérlő     | Nem kell tenni semmit. Ha külső identitási funkciókat használ a vendég felhasználókkal való együttműködésre, akkor a MAU-modell használatával automatikusan számlázunk.        |
| Egy előfizetéshez még nem csatolt Azure AD-bérlő     | [Kapcsolja össze az Azure ad-bérlőt egy előfizetéssel](#link-your-azure-ad-tenant-to-a-subscription) a Mau-számlázás aktiválásához.        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>A havi aktív felhasználók (MAU) számlázása

Az Azure AD-bérlőben a vendég felhasználói együttműködés használatáról a naptári hónapon belül a hitelesítési tevékenységgel rendelkező egyedi vendég felhasználók száma alapján számítunk fel díjat. Ez a modell a 1:5 arányú számlázási modellt váltja fel, amely legfeljebb öt vendéget engedélyez a bérlő minden prémium szintű Azure AD licence számára. Ha a bérlő egy előfizetéshez van csatolva, és külső identitási funkciókat használ a vendég felhasználókkal való együttműködésre, akkor a MAU-alapú számlázási modell alapján automatikusan számlázunk.
  
A vendég felhasználóinak díjszabási szintje az Azure AD-bérlőhöz rendelt legmagasabb díjszabási szinten alapul. Ha például a bérlő legmagasabb díjszabási szintje prémium szintű Azure AD P1, a prémium P1 díjszabási szint a vendég felhasználóra is érvényes. Ha a legmagasabb díjszabás ingyenes Azure AD, a rendszer arra kéri, hogy frissítsen egy prémium szintű díjszabási szintre, amikor a vendég felhasználói számára prémium szintű szolgáltatásokat próbál használni.

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>Az Azure AD-bérlő összekapcsolása egy előfizetéssel

Egy Azure AD-bérlőt egy Azure-előfizetéshez kell kapcsolni a megfelelő számlázáshoz és a funkciókhoz való hozzáféréshez.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) egy olyan Azure-fiókkal, amely legalább a [közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) szerepkörhöz van rendelve az előfizetésben vagy az előfizetésben található erőforráscsoporthoz.

2. Válassza ki az előfizetést tartalmazó könyvtárat: a Azure Portal eszköztáron válassza ki a **címtár + előfizetés** ikont, majd válassza ki az előfizetést tartalmazó könyvtárat.

    ![Válassza ki a címtár + előfizetés ikont](media/external-identities-pricing/portal-mau-pick-directory.png)

3. Az **Azure-szolgáltatások**területen válassza a **Azure Active Directory**lehetőséget.

4. A bal oldali menüben válassza a **külső identitások**lehetőséget.

5. Az **előfizetések**területen válassza a **társított előfizetések**elemet.

6. A bérlő listában jelölje be a bérlő melletti jelölőnégyzetet, majd válassza az **előfizetés csatolása**lehetőséget.

    ![Válassza ki a bérlőt, és csatolja az előfizetést](media/external-identities-pricing/linked-subscriptions.png)

7. Az előfizetés csatolása ablaktáblán válassza ki az **előfizetést** és az **erőforráscsoportot**. Ezután válassza az **Alkalmaz** lehetőséget.

    ![Előfizetés és erőforráscsoport kiválasztása](media/external-identities-pricing/link-subscription-resource.png)

A lépések elvégzése után az Azure-előfizetését az Azure Direct vagy a Nagyvállalati Szerződés részletei alapján számítjuk fel, ha van ilyen.

## <a name="next-steps"></a>További lépések

A legfrissebb díjszabási információkért lásd: [Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/).
