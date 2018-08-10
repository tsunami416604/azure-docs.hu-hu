---
title: Az Azure Active Directory, alkalmazásalapú feltételes hozzáférés |} A Microsoft Docs
description: Ismerje meg az Azure Active Directory alapján az alkalmazásalapú feltételes hozzáférés működését.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, az Azure AD feltételes hozzáférés, biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: f34fc4c41094292db9bed1294ee7b26ec04c96c6
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630602"
---
# <a name="azure-active-directory-app-based-conditional-access"></a>Az Azure Active Directory, alkalmazásalapú feltételes hozzáférés  

Az alkalmazottak mobileszközöket használnak a személyes és munkahelyi feladatokhoz. Gondoskodik róla, hogy az alkalmazottak számára hatékony munkavégzést, miközben is szeretné megakadályozni az adatvesztést. Az Azure Active Directory (Azure AD) alapján az alkalmazásalapú feltételes hozzáférés a felhőalkalmazásokhoz, az ügyfélalkalmazások, amelyek segítenek a vállalati adatok védelmében korlátozhatja a hozzáférést.  

Ez a témakör ismerteti az Azure AD alkalmazásalapú feltételes hozzáférés konfigurálása.

## <a name="overview"></a>Áttekintés

A [Azure AD feltételes hozzáférés](overview.md), finomhangolhatja hogyan engedéllyel rendelkező felhasználók férhetnek hozzá az erőforrásokat. Például korlátozhatja a hozzáférést a felhőalkalmazásokhoz, megbízható eszközökre.

Használhat [az Intune alkalmazásvédelmi szabályzatai](https://docs.microsoft.com/intune/app-protection-policy) a vállalati adatok védelme érdekében. Az Intune alkalmazásvédelmi szabályzatai nem igénylik a mobileszköz-kezelési (MDM) megoldás, amely lehetővé teszi egy Eszközkezelési megoldás az eszközök regisztrációja nélkül a vállalati adatok védelmét.

Az Azure Active Directory alkalmazásalapú feltételes hozzáférés lehetővé teszi, hogy korlátozni az ügyfélalkalmazások, amelyek támogatják az Intune alkalmazásvédelmi szabályzatai felhőalapú alkalmazásait. Például korlátozhatja hozzáférést az Exchange online-hoz az Outlook alkalmazást.

A feltételes hozzáférés-terminológia, az ügyfél ezeket az alkalmazásokat nevezzük **jóváhagyott ügyfélalkalmazások**.  


![Feltételes hozzáférés](./media/app-based-conditional-access/05.png)


A jóváhagyott ügyfélalkalmazások listájának megtekintéséhez lásd: [jóváhagyott alkalmazás megkövetelése ügyfél](technical-reference.md#approved-client-app-requirement).


Például egyéb szabályzatok alapján az alkalmazásalapú feltételes hozzáférési szabályzatokat kombinálhatja [eszközalapú feltételes hozzáférési szabályzatok](require-managed-devices.md) adatokat a személyes és vállalati eszközök védelme a rugalmasságot biztosít.

 


## <a name="before-you-begin"></a>Előkészületek

Ez a témakör azt feltételezi, hogy Ön ismeri a:

- A [jóváhagyott alkalmazás megkövetelése ügyfél](technical-reference.md#approved-client-app-requirement) műszaki útmutatója.


- Az alapvető fogalmait [feltételes hozzáférés az Azure Active Directory](overview.md).

- Hogyan [feltételes hozzáférési házirend konfigurálása](app-based-mfa.md).

- A [feltételes hozzáférési szabályzatok migrálása](best-practices.md#policy-migration).
 

## <a name="prerequisites"></a>Előfeltételek

Alkalmazásalapú feltételes hozzáférési szabályzat létrehozása, az Enterprise Mobility + Security vagy egy Azure Active Directory premium előfizetéssel kell rendelkeznie, és a felhasználóknak licenccel kell rendelkezniük az EMS-hez vagy az Azure ad-ben. 


## <a name="exchange-online-policy"></a>Az Exchange Online-szabályzat 

Ez a forgatókönyv tartalmaz egy alkalmazásalapú feltételes hozzáférési szabályzat Exchange online-hoz való hozzáféréshez.


### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy egy felhasználó:

- Konfigurálja az iOS vagy Android rendszeren natív mail alkalmazás használatával csatlakozni az Exchange e-mailben

- Kap egy e-mailt, amely azt jelzi, hogy hozzáférést csak elérhető Outlook alkalmazás használata

- Az alkalmazás a hivatkozást tartalmazó letöltések

- Az Outlook alkalmazás megnyílik, és jelentkezik be az Azure AD hitelesítő adatait

- Kéri, hogy telepítse az Authenticator (iOS) vagy a céges portál (Android) a folytatáshoz

- Telepíti az alkalmazást és is tud térjen vissza az Outlook-alkalmazás

- Kéri, hogy az eszköz regisztrálása

- Képes-e-mailek elérése

Minden olyan Intune alkalmazásvédelmi szabályzatok olyan időben aktiválta a hozzáférést vállalati adatokat, és kérheti a felhasználót, hogy indítsa újra az alkalmazást, egy további PIN-kód stb használata (Ha be van állítva, az alkalmazás és a platform).

### <a name="configuration"></a>Konfiguráció 

**1. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-based-conditional-access/01.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **Felhőalkalmazások:** felhőalapú alkalmazásokat, ki kell választania **Office 365 Exchange Online**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/07.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **eszközplatformok** és **ügyfélalkalmazás**:

    a. Mint **eszközplatformok**válassza **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/03.png)

    b. Mint **ügyfélalkalmazás**válassza **mobilalkalmazások és asztali alkalmazások**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/04.png)

5. Mint **hozzáférés-vezérlés**, rendelkeznie kell **jóváhagyott ügyfélalkalmazás (előzetes verzió) megkövetelése** kiválasztott.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/05.png)
 

**2. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-Active Sync (EAS)**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-based-conditional-access/06.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.


3. **Felhőalkalmazások:** felhőalapú alkalmazásokat, ki kell választania **Office 365 Exchange Online**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/07.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **ügyfélalkalmazás**. 

    a. Mint **ügyfélalkalmazás**válassza **Exchange Active Sync**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/08.png)

    b. Mint **hozzáférés-vezérlés**, rendelkeznie kell **jóváhagyott ügyfélalkalmazás (előzetes verzió) megkövetelése** kiválasztott.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/05.png)


**3. lépés – az Intune alkalmazásvédelmi szabályzatot az iOS és Android ügyfélalkalmazások konfigurálása**


![Feltételes hozzáférés](./media/app-based-conditional-access/09.png)

Lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) további információt.


## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange online-hoz és a SharePoint Online-szabályzat

Ebben a forgatókönyvben áll egy feltételes hozzáférési mobilalkalmazás-felügyeleti szabályzat, a jóváhagyott alkalmazások Exchange online-hoz és a SharePoint online-hoz való hozzáféréshez.

### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy egy felhasználó:

- Próbálja meg használni a SharePoint-alkalmazás való csatlakozáshoz és a vállalati webhelyek megtekintése

- Jelentkezzen be ugyanazokkal a hitelesítő adatokkal, mint az Outlook alkalmazás használt kísérlet

- Nem kell újból a regisztrálást és kezdheti az erőforrásokhoz való hozzáférés


### <a name="configuration"></a>Konfiguráció

**1. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online és SharePoint online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-based-conditional-access/71.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.


3. **Felhőalkalmazások:** felhőalapú alkalmazásokat, ki kell választania **Office 365 Exchange Online** és **Office 365 SharePoint Online**. 

    ![Feltételes hozzáférés](./media/app-based-conditional-access/02.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **eszközplatformok** és **ügyfélalkalmazás**:

    a. Mint **eszközplatformok**válassza **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/03.png)

    b. Mint **ügyfélalkalmazás**válassza **mobilalkalmazások és asztali alkalmazások**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/04.png)

5. Mint **hozzáférés-vezérlés**, rendelkeznie kell **jóváhagyott ügyfélalkalmazás (előzetes verzió) megkövetelése** kiválasztott.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/05.png)




**2. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-Active Sync (EAS)**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-based-conditional-access/06.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **Felhőalkalmazások:** felhőalapú alkalmazásokat, ki kell választania **Office 365 Exchange Online**. Online 

    ![Feltételes hozzáférés](./media/app-based-conditional-access/07.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **ügyfélalkalmazás**:

    a. Mint **ügyfélalkalmazás**válassza **Exchange Active Sync**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/08.png)

    b. Mint **hozzáférés-vezérlés**, rendelkeznie kell **jóváhagyott ügyfélalkalmazás (előzetes verzió) megkövetelése** kiválasztott.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/05.png)




**3. lépés – az Intune alkalmazásvédelmi szabályzatot az iOS és Android ügyfélalkalmazások konfigurálása**


![Feltételes hozzáférés](./media/app-based-conditional-access/09.png)

Lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) további információt.


## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Az Exchange online-hoz és a SharePoint Online alkalmazásalapú vagy megfelelő eszközszabályzat

Ez a forgatókönyv áll egy alkalmazás vagy a megfelelő eszköz feltételes hozzáférési szabályzat Exchange online-hoz való hozzáféréshez.


### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy:
 
- Néhány felhasználó már regisztrált (a vagy vállalati eszközök nélkül)

- Felhasználók, akik nem regisztrált, és az Azure AD-alkalmazással regisztrált alkalmazás hogy védett erőforrások eléréséhez az eszköz regisztrálása

- Az alkalmazás által védett alkalmazás használatával beléptetett felhasználóknak nem kell újra regisztrálni az eszközt


### <a name="configuration"></a>Konfiguráció

**1. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online és SharePoint online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-based-conditional-access/62.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **Felhőalkalmazások:** felhőalapú alkalmazásokat, ki kell választania **Office 365 Exchange Online** és **Office 365 SharePoint Online**. 

     ![Feltételes hozzáférés](./media/app-based-conditional-access/02.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **eszközplatformok** és **ügyfélalkalmazás**. 
 
    a. Mint **eszközplatformok**válassza **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/03.png)

    b. Mint **ügyfélalkalmazás**válassza **mobilalkalmazások és asztali alkalmazások**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/04.png)

5. Mint **hozzáférés-vezérlés**, szüksége lesz kiválasztva a következőket:

    - **Megfelelőként megjelölt eszköz megkövetelése**

    - **Jóváhagyott ügyfélalkalmazás (előzetes verzió) megkövetelése**

    - **A kijelölt feltételek egyikének megkövetelése**   
 
    ![Feltételes hozzáférés](./media/app-based-conditional-access/11.png)



**2. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-Active Sync (EAS)**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-based-conditional-access/61.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **Felhőalkalmazások:** felhőalapú alkalmazásokat, ki kell választania **Office 365 Exchange Online**. 

    ![Feltételes hozzáférés](./media/app-based-conditional-access/07.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **ügyfélalkalmazás**. 

    Mint **ügyfélalkalmazás*válassza **Exchange Active Sync**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/08.png)

5. Mint **hozzáférés-vezérlés**, rendelkeznie kell **jóváhagyott ügyfélalkalmazás (előzetes verzió) megkövetelése** kiválasztott.
 
    ![Feltételes hozzáférés](./media/app-based-conditional-access/11.png)




**3. lépés – az Intune alkalmazásvédelmi szabályzatot az iOS és Android ügyfélalkalmazások konfigurálása**


![Feltételes hozzáférés](./media/app-based-conditional-access/09.png)

Lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) további információt.





## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Az Exchange online-hoz és a SharePoint Online alkalmazásalapú és megfelelő eszközzel házirend

Ez a forgatókönyv áll egy alkalmazás-alapú és a megfelelő eszköz feltételes hozzáférési szabályzat Exchange online-hoz való hozzáféréshez.


### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy egy felhasználó:
 
-   Konfigurálja az iOS vagy Android rendszeren natív mail alkalmazás használatával csatlakozni az Exchange e-mailben
-   Kap egy e-mailt, amely azt jelzi, hogy hozzáférést igényel a regisztrálni kívánt eszközön
-   Letölti a céges portálon, és bejelentkezik a vállalati portál
-   Mail ellenőrzi, és felkéri, hogy az Outlook alkalmazás használata
-   Az Outlook alkalmazás letöltése
-   Megnyitja az Outlook alkalmazást, és megadja az a regisztráció során használt hitelesítő adatok
-   Felhasználó érhessék el az e-mailben

Minden olyan Intune alkalmazásvédelmi szabályzatai aktiválódnak az időben, a vállalati adatokhoz való hozzáférést, és kérheti a felhasználót, hogy indítsa újra az alkalmazást, egy további PIN-kód stb. használata (Ha be van állítva, az alkalmazás és a platform)


### <a name="configuration"></a>Konfiguráció

**1. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online és SharePoint online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-based-conditional-access/62.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **Felhőalkalmazások:** felhőalapú alkalmazásokat, ki kell választania **Office 365 Exchange Online** és **Office 365 SharePoint Online**. 

     ![Feltételes hozzáférés](./media/app-based-conditional-access/02.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **eszközplatformok** és **ügyfélalkalmazás**. 
 
    a. Mint **eszközplatformok**válassza **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/03.png)

    b. Mint **ügyfélalkalmazás**válassza **mobilalkalmazások és asztali alkalmazások**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/04.png)

5. Mint **hozzáférés-vezérlés**, szüksége lesz kiválasztva a következőket:

    - **Megfelelőként megjelölt eszköz megkövetelése**

    - **Jóváhagyott ügyfélalkalmazás (előzetes verzió) megkövetelése**

    - **Az összes kijelölt vezérlő megkövetelése**   
 
    ![Feltételes hozzáférés](./media/app-based-conditional-access/13.png)



**2. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-Active Sync (EAS)**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-based-conditional-access/61.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **Felhőalkalmazások:** felhőalapú alkalmazásokat, ki kell választania **Office 365 Exchange Online**. 

    ![Feltételes hozzáférés](./media/app-based-conditional-access/07.png)

4. **Feltételek:** , **feltételek**, konfigurálnia kell **ügyfélalkalmazás**. 

    Mint **ügyfélalkalmazás**válassza **Exchange Active Sync**.

    ![Feltételes hozzáférés](./media/app-based-conditional-access/08.png)

5. Mint **hozzáférés-vezérlés**, szüksége lesz kiválasztva a következőket:

    - **Megfelelőként megjelölt eszköz megkövetelése**

    - **Jóváhagyott ügyfélalkalmazás (előzetes verzió) megkövetelése**

    - **Az összes kijelölt vezérlő megkövetelése**   
 
    ![Feltételes hozzáférés](./media/app-based-conditional-access/64.png)




**3. lépés – az Intune alkalmazásvédelmi szabályzatot az iOS és Android ügyfélalkalmazások konfigurálása**


![Feltételes hozzáférés](./media/app-based-conditional-access/09.png)

Lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) további információt.






## <a name="next-steps"></a>További lépések

Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md).

Ha készen áll a környezetre vonatkozó feltételes hozzáférési szabályzatok konfigurálására, tekintse meg a [ajánlott eljárások az Azure Active Directory feltételes hozzáférés](best-practices.md). 
