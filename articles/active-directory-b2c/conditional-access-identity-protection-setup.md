---
title: Az Identity Protection és a feltételes hozzáférés beállítása Azure AD B2C
description: Ismerje meg, hogyan konfigurálhatja az Identitáskezelés és a feltételes hozzáférés beállítását Azure AD B2C bérlő számára a kockázatos bejelentkezések és egyéb kockázati események megtekintéséhez, valamint a kockázatok észlelése alapján létrehozott szabályzatok létrehozásához.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5554cfcde9aba1b0e5c9c8b60e2e6a7e9a8ba378
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89271542"
---
# <a name="set-up-identity-protection-and-conditional-access-in-azure-ad-b2c"></a>Az Identity Protection és a feltételes hozzáférés beállítása Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Az Identity Protection folyamatos kockázati észlelést biztosít a Azure AD B2C bérlő számára. Ha a Azure AD B2C bérlői díjszabása prémium P2, a Azure Portalban tekintheti meg a részletes Identity Protection kockázati eseményeit. Ezen kockázati észlelések alapján [feltételes hozzáférési](../active-directory/conditional-access/overview.md) szabályzatokat is alkalmazhat a műveletek meghatározásához és a szervezeti házirendek betartatásához.

## <a name="prerequisites"></a>Előfeltételek

- A Azure AD B2C bérlőt [egy Azure ad-előfizetéshez kell kapcsolni](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).
- Azure AD B2C prémium P2 szükséges a bejelentkezéshez és a felhasználói kockázatos feltételes hozzáférés használatához. Ha szükséges, [módosítsa a Azure ad B2C díjszabási szintjét a prémium P2-](https://aka.ms/exid-pricing-tier)re. 
- A B2C-bérlőben az Identitáskezelés és a feltételes hozzáférés kezeléséhez olyan fiókra van szüksége, amely a globális rendszergazdai szerepkörhöz vagy a biztonsági rendszergazdai szerepkörhöz van rendelve.
- Ahhoz, hogy használhassa ezeket a szolgáltatásokat a bérlőben, először át kell váltania a Azure AD B2C Premium P2 díjszabási szintjére.

## <a name="set-up-identity-protection"></a>Identity Protection beállítása

Az Identity Protection alapértelmezés szerint be van kapcsolva. Ahhoz, hogy meg tudja tekinteni az Identity Protection kockázati eseményeit a Azure AD B2C bérlőben, egyszerűen csatolja a Azure AD B2C bérlőt egy Azure AD-előfizetéshez, és válassza a Azure AD B2C Premium P2 díjszabási szintet. A Azure Portalban tekintheti meg a kockázati események részletes jelentéseit.

### <a name="supported-identity-protection-risk-detections"></a>Támogatott Identity Protection kockázati észlelések

A következő kockázati észlelések jelenleg Azure AD B2C támogatottak:  

|Kockázat észlelésének típusa  |Leírás  |
|---------|---------|
| Szokatlan utazás     | Jelentkezzen be egy atipikus helyről a felhasználó legutóbbi bejelentkezései alapján.        |
|Névtelen IP-cím     | Bejelentkezés névtelen IP-címről (például: Tor-böngésző, névtelen VPN-ek)        |
|Szokatlan bejelentkezési tulajdonságok     | Jelentkezzen be a nemrégiben nem látott tulajdonságokkal az adott felhasználó számára.        |
|Kártevők társított IP-címe     | Bejelentkezés kártevővel összekapcsolt IP-címről         |
|Azure AD-fenyegetések felderítése     | A Microsoft belső és külső veszélyforrások hírszerzési forrásai ismert támadási mintát azonosítottak        |

## <a name="view-risk-events-for-your-azure-ad-b2c-tenant"></a>A Azure AD B2C bérlő kockázati eseményeinek megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

1. A Azure Portal keresse meg és válassza a **Azure ad B2C**lehetőséget.

1. A **Biztonság**területen válassza a **kockázatos felhasználók (előzetes verzió)** lehetőséget.

   ![Kockázatos felhasználók](media/conditional-access-identity-protection-setup/risky-users.png)

1. A **Biztonság**területen válassza a **kockázati észlelések (előzetes verzió)** lehetőséget.

   ![Kockázatészlelések](media/conditional-access-identity-protection-setup/risk-detections.png)

## <a name="add-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat hozzáadása 

Ha feltételes hozzáférési szabályzatot szeretne hozzáadni az Identity Protection kockázati észlelései alapján, győződjön meg arról, hogy a biztonsági alapértékek le vannak tiltva a Azure AD B2C bérlő esetében, majd hozzon létre feltételes hozzáférési házirendeket.

### <a name="to-disable-security-defaults"></a>A biztonsági alapértékek letiltása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

3. A Azure Portal keresse meg és válassza a **Azure Active Directory**lehetőséget.

4. Válassza a **Tulajdonságok**lehetőséget, majd válassza a **biztonsági beállítások kezelése**lehetőséget.

   ![Biztonsági alapértékek letiltása](media/conditional-access-identity-protection-setup/disable-security-defaults.png)

5. A biztonsági Alapértelmezések engedélyezése területen válassza a nem lehetőséget. 

   ![A biztonsági Alapértelmezések engedélyezése váltógomb beállítása nem értékre](media/conditional-access-identity-protection-setup/enable-security-defaults-toggle.png)

### <a name="to-create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

1. A Azure Portal keresse meg és válassza a **Azure ad B2C**lehetőséget.

1. A **Biztonság**területen válassza a **feltételes hozzáférés (előzetes verzió)** lehetőséget. Megnyílik a **feltételes hozzáférési szabályzatok** lap. 

1. Válassza az **új szabályzat** lehetőséget, majd az Azure ad feltételes hozzáférési dokumentációjában hozzon létre egy új szabályzatot. Például:

   - [Bejelentkezési kockázat alapú feltételes hozzáférés: Engedélyezés feltételes hozzáférési házirenddel](../active-directory/conditional-access/howto-conditional-access-policy-risk.md#enable-with-conditional-access-policy)

   > [!IMPORTANT]
   > Amikor kiválasztja azokat a felhasználókat, akikre alkalmazni kívánja a szabályzatot, ne csak az **összes felhasználót** jelölje ki, vagy tiltsa le a bejelentkezést.

## <a name="test-the-conditional-access-policy"></a>A feltételes hozzáférési szabályzat tesztelése

1. Hozzon létre egy feltételes hozzáférési szabályzatot a fentiekben leírtak szerint, a következő beállításokkal:
   
   - **Felhasználók és csoportok**esetében válassza a teszt felhasználót. Ne jelölje ki az **összes felhasználót** , vagy tiltsa le a bejelentkezést.
   - **Felhőalapú alkalmazások vagy műveletek**esetében válassza az **alkalmazások kiválasztása**lehetőséget, majd válassza ki a függő entitás alkalmazását.
   - A feltételek beállításnál válassza a **bejelentkezési kockázat** és a **magas**, **közepes**és **alacsony** kockázati szintek lehetőséget.
   - A **támogatás**mezőben válassza a **hozzáférés letiltása**lehetőséget.

      ![A blokkolási hozzáférés kiválasztása](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

1. A **Létrehozás**lehetőség kiválasztásával engedélyezheti a feltételes hozzáférési szabályzat tesztelését.

1. Kockázatos bejelentkezés szimulálása a [Tor-böngésző](https://www.torproject.org/download/)használatával. 

1. A megkísérelt bejelentkezéshez tartozó jwt.ms dekódolású tokenben látnia kell, hogy a bejelentkezés le lett tiltva:

   ![Blokkolt bejelentkezés tesztelése](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>A feltételes hozzáférés eredményeinek áttekintése a naplózási jelentésben

Feltételes hozzáférési esemény eredményének áttekintése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

3. A Azure Portal keresse meg és válassza a **Azure ad B2C**lehetőséget.

4. A **tevékenységek**területen válassza a **naplók**lehetőséget.

5. A napló szűréséhez állítsa a **Kategória** beállítást a **B2C** értékre, és állítsa be a **tevékenység erőforrástípus** **IdentityProtection**értékre. Ezután válassza az **Alkalmaz** lehetőséget.

6. Tekintse át a naplózási tevékenységet az elmúlt 7 napban. A következő típusú tevékenységek tartoznak ide:

   - **Feltételes hozzáférési szabályzatok kiértékelése**: Ez a naplózási naplóbejegyzés azt jelzi, hogy a rendszer feltételes hozzáférési értékelést hajtott végre egy hitelesítés során.
   - **Felhasználó szervizelése**: Ez a bejegyzés azt jelzi, hogy a végfelhasználó megkapta a feltételes hozzáférési szabályzatok támogatását vagy követelményeit, és ez a tevékenység jelent meg a kockázati motornak, hogy csökkentse a felhasználó kockázatát.

7. Válassza ki a **feltételes hozzáférési szabályzat kiértékelése** naplóbejegyzés a listában a **tevékenység részletei:** napló lap, amely a naplózási napló azonosítóit jeleníti meg, valamint a **További részletek** szakaszban található információkat:

   - ConditionalAccessResult: a feltételes szabályzat kiértékeléséhez szükséges engedély.
   - AppliedPolicies: az összes olyan feltételes hozzáférési szabályzat listája, amelyben teljesülnek a feltételek, és a szabályzatok be vannak kapcsolva.
   - ReportingPolicies: azoknak a feltételes hozzáférési házirendeknek a listája, amelyek a csak jelentés módra lettek beállítva, és a feltételek teljesültek.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés hozzáadása egy felhasználói folyamathoz](conditional-access-user-flow.md).
