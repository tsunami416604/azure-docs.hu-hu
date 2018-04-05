---
title: Azure Active Directory, alkalmazás-alapú feltételes hozzáférés |} Microsoft Docs
description: Ismerje meg, hogyan működik a Azure Active Directory alkalmazás-alapú feltételes hozzáférés.
services: active-directory
keywords: alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/11/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: b35aed3583307c34eadbed3dd13d1ffd51494927
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="azure-active-directory-app-based-conditional-access"></a>Azure Active Directory, alkalmazás-alapú feltételes hozzáférés  

Az alkalmazottak mobileszközöket használnak a személyes és munkahelyi feladatokhoz. Meggyőződött arról, hogy az alkalmazottak produktív munkavégzést, szeretné az adatvesztés elkerülése érdekében. Az Azure Active Directory (Azure AD) alkalmazás-alapú feltételes hozzáférés korlátozhatja a felhőalkalmazások ügyfél alkalmazásokat értünk, amelyek a vállalati adatok védelméről is hozzáférést.  

Ez a témakör ismerteti, hogyan konfigurálhatja az Azure AD alkalmazás-alapú feltételes hozzáférés.

## <a name="overview"></a>Áttekintés

A [Azure AD feltételes hozzáférésével](active-directory-conditional-access-azure-portal.md), úgy finomhangolhatja, hogyan engedéllyel rendelkező felhasználók férhetnek hozzá az erőforrásokat. Például a hozzáférés korlátozható a felhőalkalmazások megbízható eszközökre.

Használhat [Intune alkalmazás adatvédelmi szabályzatok](https://docs.microsoft.com/intune/app-protection-policy) a vállalati adatok védelme érdekében. Intune app adatvédelmi szabályzatok nincs szükség a mobileszköz-kezelési (MDM) megoldás, amely lehetővé teszi a vállalati adatok védelmét egy Eszközkezelési megoldás az eszközök regisztrációja nélkül.

Az Azure Active Directory alkalmazás-alapú feltételes hozzáférés lehetővé teszi, hogy az ügyfélalkalmazások, amelyek támogatják az Intune app adatvédelmi szabályzatok a felhőalapú alkalmazások korlátozhatják. Például korlátozhatja hozzáférés az Exchange Online az Outlook alkalmazást.

A feltételes hozzáférés terminológiát, az ügyfél alkalmazások nevezzük **ügyfélalkalmazások jóváhagyott**.  


![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/05.png)


A jóváhagyott alkalmazások listáját lásd: [ügyfél app követelmény jóváhagyott](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


Alkalmazás-alapú feltételes hozzáférési házirendek egyéb házirendek például kombinálhatja [eszközalapú feltételes hozzáférési házirendek](active-directory-conditional-access-policy-connected-applications.md) adatok személyes és vállalati eszközök védelme a rugalmasságot biztosít.

 


## <a name="before-you-begin"></a>Előkészületek

Ez a témakör feltételezi, hogy Ön ismeri a:

- A [ügyfél app követelmény jóváhagyott](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) műszaki útmutatója.


- Az alapvető fogalmait [feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md).

- Hogyan [feltételes hozzáférési házirend konfigurálása](active-directory-conditional-access-azure-portal-get-started.md).

- A [feltételes hozzáférési házirendek áttelepítésének](active-directory-conditional-access-best-practices.md#policy-migration).
 

## <a name="prerequisites"></a>Előfeltételek

Alkalmazás-alapú feltételes hozzáférési szabályzat létrehozása egy nagyvállalati mobilitási + biztonsági vagy egy Azure Active Directory premium előfizetéssel kell rendelkeznie, és a felhasználóknak licenccel kell rendelkezniük az EMS, illetve az Azure AD. 


## <a name="exchange-online-policy"></a>Exchange Online-szabályzat 

Ebben a forgatókönyvben egy alkalmazás-alapú feltételes hozzáférési házirendet az Exchange Online-hozzáférésének áll.


### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy a felhasználó:

- Konfigurálja az e-mailek a natív e-mail alkalmazás iOS vagy Android kapcsolódni az Exchange-hez

- Kap egy e-mailt, amely azt jelzi, hogy hozzáférést csak elérhető Outlook alkalmazás használata

- Letölti az alkalmazást a hivatkozás

- Megnyitja az Outlook alkalmazás és jelentkezik be az Azure AD hitelesítő adatait

- A rendszer kéri-hitelesítő (iOS) vagy a vállalati portál (Android) a folytatáshoz telepítse

- Térjen vissza az alkalmazás és a is telepíti az Outlook alkalmazás

- A rendszer kéri-eszköz regisztrálása

- Képes-e-mailek elérése

A Intune app védelmi házirendek időpontjában aktív a hozzáférés vállalati adatok, és előfordulhat, hogy a felhasználótól a az alkalmazás újraindítása (Ha be van állítva, az alkalmazás és platform), használja a egy további PIN stb.

### <a name="configuration"></a>Konfiguráció 

**1. lépés – az Azure AD feltételes hozzáférési házirend konfigurálása az Exchange online-hoz**

A feltételes hozzáférési házirend ebben a lépésben kell a következő összetevők konfigurálása:

![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/01.png)

1. A **neve** a feltételes hozzáférési szabályzatának.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési házirend rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **A felhőalapú alkalmazások:** felhőalapú alkalmazások, mint kell kiválasztania **Office 365 Exchange Online**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/07.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **eszközplatformok** és **ügyfélalkalmazások**:

    a. Mint **eszközplatformok**, jelölje be **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/03.png)

    b. Mint **ügyfélalkalmazások**, jelölje be **hordozható és asztali alkalmazások**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/04.png)

5. Mint **hozzáférés-szabályozási**, telepíteni kell **jóváhagyott alkalmazás (előnézet) szükséges** kijelölt.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/05.png)
 

**2. lépés - az Azure AD feltételes hozzáférési házirend konfigurálása az Exchange online-hoz az Active Sync (EAS)**

A feltételes hozzáférési házirend ebben a lépésben kell a következő összetevők konfigurálása:

![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/06.png)

1. A **neve** a feltételes hozzáférési szabályzatának.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési házirend rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.


3. **A felhőalapú alkalmazások:** felhőalapú alkalmazások, mint kell kiválasztania **Office 365 Exchange Online**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/07.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **ügyfélalkalmazások**. 

    a. Mint **ügyfélalkalmazások**, jelölje be **az Exchange Active Sync**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/08.png)

    b. Mint **hozzáférés-szabályozási**, telepíteni kell **jóváhagyott alkalmazás (előnézet) szükséges** kijelölt.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/05.png)


**3. lépés - az Intune app védelmi házirend konfigurálásában az iOS és Android ügyfélalkalmazások**


![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/09.png)

Lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) további információt.


## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online és SharePoint Online-szabályzat

Ebben a forgatókönyvben áll egy feltételes hozzáférési mobilalkalmazás-kezelési házirenddel jóváhagyott alkalmazások Exchange Online és SharePoint online-hoz való hozzáféréshez.

### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy a felhasználó:

- Próbálja használni a SharePoint-alkalmazás való csatlakozáshoz és a vállalati hely megtekintése

- Kísérlet történt jelentkezzen be ugyanazokkal a hitelesítő adatokkal, az Outlook alkalmazás hitelesítő adatként

- Nem kell újból a regisztrálást és ingyenesen juthatnak az erőforrások


### <a name="configuration"></a>Konfiguráció

**1. lépés – az Azure AD feltételes hozzáférési házirend konfigurálása az Exchange Online és SharePoint online-hoz**

A feltételes hozzáférési házirend ebben a lépésben kell a következő összetevők konfigurálása:

![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/71.png)

1. A **neve** a feltételes hozzáférési szabályzatának.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési házirend rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.


3. **A felhőalapú alkalmazások:** felhőalapú alkalmazások, mint kell kiválasztania **Office 365 Exchange Online** és **Office 365 SharePoint Online**. 

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/02.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **eszközplatformok** és **ügyfélalkalmazások**:

    a. Mint **eszközplatformok**, jelölje be **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/03.png)

    b. Mint **ügyfélalkalmazások**, jelölje be **hordozható és asztali alkalmazások**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/04.png)

5. Mint **hozzáférés-szabályozási**, telepíteni kell **jóváhagyott alkalmazás (előnézet) szükséges** kijelölt.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/05.png)




**2. lépés - az Azure AD feltételes hozzáférési házirend konfigurálása az Exchange online-hoz az Active Sync (EAS)**

A feltételes hozzáférési házirend ebben a lépésben kell a következő összetevők konfigurálása:

![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/06.png)

1. A **neve** a feltételes hozzáférési szabályzatának.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési házirend rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **A felhőalapú alkalmazások:** felhőalapú alkalmazások, mint kell kiválasztania **Office 365 Exchange Online**. Online 

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/07.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **ügyfélalkalmazások**:

    a. Mint **ügyfélalkalmazások**, jelölje be **az Exchange Active Sync**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/08.png)

    b. Mint **hozzáférés-szabályozási**, telepíteni kell **jóváhagyott alkalmazás (előnézet) szükséges** kijelölt.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/05.png)




**3. lépés - az Intune app védelmi házirend konfigurálásában az iOS és Android ügyfélalkalmazások**


![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/09.png)

Lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) további információt.


## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online és SharePoint Online alkalmazás-alapú vagy nem megfelelőek eszközszabályzat

Ebben a forgatókönyvben egy alkalmazás vagy a megfelelő eszköz feltételes hozzáférési házirendet az Exchange Online-hozzáférésének áll.


### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy:
 
- Néhány felhasználó a már regisztrált (vagy anélkül vállalati eszközök)

- Felhasználók, akik nem regisztrálták és regisztrálva az Azure AD egy adott alkalmazás használatát a védett alkalmazás hozzáférését az erőforrásokhoz az eszköz regisztrálása

- A védett app alkalmazással beléptetett felhasználóknak nem kell újra regisztrálni az eszközt


### <a name="configuration"></a>Konfiguráció

**1. lépés – az Azure AD feltételes hozzáférési házirend konfigurálása az Exchange Online és SharePoint online-hoz**

A feltételes hozzáférési házirend ebben a lépésben kell a következő összetevők konfigurálása:

![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/62.png)

1. A **neve** a feltételes hozzáférési szabályzatának.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési házirend rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **A felhőalapú alkalmazások:** felhőalapú alkalmazások, mint kell kiválasztania **Office 365 Exchange Online** és **Office 365 SharePoint Online**. 

     ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/02.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **eszközplatformok** és **ügyfélalkalmazások**. 
 
    a. Mint **eszközplatformok**, jelölje be **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/03.png)

    b. Mint **ügyfélalkalmazások**, jelölje be **hordozható és asztali alkalmazások**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/04.png)

5. Mint **hozzáférés-szabályozási**, a következő kijelölt van szükség:

    - **Az eszköz megfelelőnek kell megjelölni megkövetelése**

    - **Jóváhagyott alkalmazás (előnézet) megkövetelése**

    - **Jelszó használata kötelezővé tehető a kijelölt vezérlők**   
 
    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/11.png)



**2. lépés - az Azure AD feltételes hozzáférési házirend konfigurálása az Exchange online-hoz az Active Sync (EAS)**

A feltételes hozzáférési házirend ebben a lépésben kell a következő összetevők konfigurálása:

![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/61.png)

1. A **neve** a feltételes hozzáférési szabályzatának.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési házirend rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **A felhőalapú alkalmazások:** felhőalapú alkalmazások, mint kell kiválasztania **Office 365 Exchange Online**. 

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/07.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **ügyfélalkalmazások**. 

    Mint **ügyfélalkalmazások*, jelölje be **az Exchange Active Sync**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/08.png)

5. Mint **hozzáférés-szabályozási**, telepíteni kell **jóváhagyott alkalmazás (előnézet) szükséges** kijelölt.
 
    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/11.png)




**3. lépés - az Intune app védelmi házirend konfigurálásában az iOS és Android ügyfélalkalmazások**


![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/09.png)

Lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) további információt.





## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online és SharePoint Online alkalmazás- és a megfelelő eszköz házirend

Ebben a forgatókönyvben egy alkalmazás- és a megfelelő eszköz feltételes hozzáférési házirendet az Exchange Online-hozzáférésének áll.


### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy a felhasználó:
 
-   Konfigurálja az e-mailek a natív e-mail alkalmazás iOS vagy Android kapcsolódni az Exchange-hez
-   Kap egy e-mailt, amely jelzi, hogy hozzáférést igényel a regisztrálni kívánt eszközön
-   A vállalati portál letölti és jelentkezik be a vállalati portál
-   Mail ellenőrzi, és felkéri, hogy az Outlook alkalmazás használata
-   Az Outlook alkalmazás letöltése
-   Megnyitja az Outlook alkalmazást, és megadja a regisztrációt a használt hitelesítő adatok
-   Felhasználó érhessék el az e-mailek

Intune app protection-házirendeket a vállalati adatokhoz való hozzáférés időpontjában aktív, és kérheti a felhasználót, hogy indítsa újra az alkalmazást, egy további PIN stb használata (Ha be van állítva, az alkalmazás és platform)


### <a name="configuration"></a>Konfiguráció

**1. lépés – az Azure AD feltételes hozzáférési házirend konfigurálása az Exchange Online és SharePoint online-hoz**

A feltételes hozzáférési házirend ebben a lépésben kell a következő összetevők konfigurálása:

![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/62.png)

1. A **neve** a feltételes hozzáférési szabályzatának.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési házirend rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **A felhőalapú alkalmazások:** felhőalapú alkalmazások, mint kell kiválasztania **Office 365 Exchange Online** és **Office 365 SharePoint Online**. 

     ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/02.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **eszközplatformok** és **ügyfélalkalmazások**. 
 
    a. Mint **eszközplatformok**, jelölje be **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/03.png)

    b. Mint **ügyfélalkalmazások**, jelölje be **hordozható és asztali alkalmazások**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/04.png)

5. Mint **hozzáférés-szabályozási**, a következő kijelölt van szükség:

    - **Az eszköz megfelelőnek kell megjelölni megkövetelése**

    - **Jóváhagyott alkalmazás (előnézet) megkövetelése**

    - **A kijelölt vezérlők megkövetelése**   
 
    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/13.png)



**2. lépés - az Azure AD feltételes hozzáférési házirend konfigurálása az Exchange online-hoz az Active Sync (EAS)**

A feltételes hozzáférési házirend ebben a lépésben kell a következő összetevők konfigurálása:

![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/61.png)

1. A **neve** a feltételes hozzáférési szabályzatának.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési házirend rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **A felhőalapú alkalmazások:** felhőalapú alkalmazások, mint kell kiválasztania **Office 365 Exchange Online**. 

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/07.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **ügyfélalkalmazások**. 

    Mint **ügyfélalkalmazások**, jelölje be **az Exchange Active Sync**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/08.png)

5. Mint **hozzáférés-szabályozási**, a következő kijelölt van szükség:

    - **Az eszköz megfelelőnek kell megjelölni megkövetelése**

    - **Jóváhagyott alkalmazás (előnézet) megkövetelése**

    - **A kijelölt vezérlők megkövetelése**   
 
    ![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/64.png)




**3. lépés - az Intune app védelmi házirend konfigurálásában az iOS és Android ügyfélalkalmazások**


![Feltételes hozzáférés](./media/active-directory-conditional-access-mam/09.png)

Lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) további információt.






## <a name="next-steps"></a>További lépések

Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).

Ha készen áll a környezet feltételes hozzáférési házirend-beállításokkal, tekintse meg a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md). 
