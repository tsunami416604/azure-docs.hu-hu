---
title: Felhőalapú alkalmazás-hozzáférés az Azure Active Directory feltételes hozzáférés az alkalmazásvédelmi szabályzat igénylése |} A Microsoft Docs
description: Ismerje meg, hogyan alkalmazásvédelmi szabályzatot a feltételes hozzáférés az Azure Active Directory felhőalapú alkalmazás-hozzáférés szükséges.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, feltételes hozzáférés az Azure AD-vel, biztonságos hozzáférés a vállalati erőforrásokhoz, feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: f695d50e251d0104cf9f0d38fe4489a0e66dfe15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288501"
---
# <a name="how-to-require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>kézikönyv: Alkalmazásvédelmi szabályzat szükséges a cloud app hozzáféréshez a feltételes hozzáférés (előzetes verzió)

Az alkalmazottak mobileszközöket használnak a személyes és munkahelyi feladatokhoz. Gondoskodik róla, hogy az alkalmazottak számára hatékony munkavégzést, miközben is szeretné megakadályozni az adatvesztést. Azure Active Directory (Azure AD) feltételes hozzáféréssel védheti a vállalati adatok korlátozza a hozzáférést az ügyfélalkalmazások, amelyek egy alkalmazásvédelmi szabályzattal rendelkeznek az első felhőalapú alkalmazásait.

Ez a témakör bemutatja, hogyan konfigurálhatja a feltételes hozzáférési szabályzatok is előírhatja az alkalmazásvédelmi szabályzat adatokhoz való hozzáférés előtt.

## <a name="overview"></a>Áttekintés

A [Azure AD feltételes hozzáférés](overview.md), finomhangolhatja hogyan engedéllyel rendelkező felhasználók férhetnek hozzá az erőforrásokat. Például korlátozhatja a hozzáférést a felhőalkalmazásokhoz, megbízható eszközökre.

Használhat [az Intune alkalmazásvédelmi szabályzatai](https://docs.microsoft.com/intune/app-protection-policy) a vállalati adatok védelme érdekében. Az Intune alkalmazásvédelmi szabályzatai nem igénylik a mobileszköz-kezelési (MDM) megoldás, amely lehetővé teszi egy Eszközkezelési megoldás az eszközök regisztrációja nélkül a vállalati adatok védelmét.

Az Azure Active Directory feltételes hozzáférés Intune-ban az Azure AD szolgáltatásba az alkalmazásvédelmi szabályzat fogadása jelentett ügyfélalkalmazások számára a felhőalkalmazások korlátozza a hozzáférést. Például korlátozhatja hozzáférést az Exchange online-hoz az Outlook alkalmazást, amely rendelkezik az Intune alkalmazásvédelmi szabályzat.

A feltételes hozzáférés terminológia ügyfél alkalmazások ismert házirend által védett lehet egy **alkalmazásvédelmi szabályzat**.  

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/05.png)

Szabályzat védett ügyfélalkalmazások, lásd: [alkalmazás alkalmazásvédelmi szabályzat követelménye úgy](technical-reference.md#approved-client-app-requirement).

Például app-protection-alapú feltételes hozzáférési szabályzatok az egyéb szabályzatokat kombinálhatja [eszközalapú feltételes hozzáférési szabályzatok](require-managed-devices.md) adatokat a személyes és vállalati eszközök védelme a rugalmasságot biztosít.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>App protection-alapú feltételes hozzáférés követelmény előnyei

Megfelelőségi hasonló iOS és Android rendszerhez, az Intune által jelentett felügyelt eszköz, mostantól az Intune-ban az Azure AD-jelentések alkalmazásvédelmi szabályzat alkalmazása esetén a, hogy a feltételes hozzáférés ezzel, hozzáférés-ellenőrzést. Az új feltételes hozzáférési szabályzat **alkalmazásvédelmi szabályzat** növeli a biztonságot a felügyeleti hibák ellen védelmet biztosító például:

- Intune-licenccel nem rendelkező felhasználók
- felhasználók, az Intune alkalmazásvédelmi szabályzat nem tud fogadni.
- Intune app protection-szabályzat alkalmazások, amelyek nem lett konfigurálva a szabályzat fogadásához


## <a name="before-you-begin"></a>Előkészületek

Ez a témakör azt feltételezi, hogy Ön ismeri a:

- A [alkalmazás alkalmazásvédelmi szabályzat követelménye úgy](technical-reference.md#app-protection-policy-requirement) műszaki útmutatója.

- A [jóváhagyott alkalmazás megkövetelése ügyfél](technical-reference.md#approved-client-app-requirement) műszaki útmutatója.

- Az alapvető fogalmait [feltételes hozzáférés az Azure Active Directory](overview.md).

- Hogyan [feltételes hozzáférési házirend konfigurálása](app-based-mfa.md).


## <a name="prerequisites"></a>Előfeltételek

Be kell a védelmi alkalmazásalapú feltételes hozzáférési szabályzat létrehozása
- Enterprise Mobility + Security vagy egy Azure Active Directory premium előfizetéssel + Intune rendelkezik
- A felhasználók az EMS vagy az Azure AD-licencek biztosítása és az Intune-ban
- Győződjön meg arról, az ügyfélalkalmazás az Intune-ban alkalmazásvédelmi szabályzatokkal fogadására van konfigurálva
- Győződjön meg arról, a felhasználók úgy vannak konfigurálva, az Intune alkalmazásvédelmi szabályzatának fogadásához az Intune-ban

## <a name="app-protection-based-policy-for-exchange-online"></a>App protection-alapú szabályzat Exchange online-hoz

Ez a forgatókönyv áll egy védelmi alkalmazásalapú feltételes hozzáférési szabályzatot az Exchange online-hoz való hozzáféréshez.

### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy egy felhasználó:

- Konfigurálja az iOS vagy Android rendszeren natív mail alkalmazás használatával csatlakozni az Exchange e-mailben

- Kap egy e-mailt, amely azt jelzi, hogy hozzáférést csak elérhető Outlook alkalmazás használata

- Az alkalmazás a hivatkozást tartalmazó letöltések

- Az Outlook alkalmazás megnyílik, és jelentkezik be az Azure AD hitelesítő adatait

- Kéri, hogy telepítse az Authenticator (iOS) vagy a céges portál (Android) a folytatáshoz

- Telepíti az alkalmazást és is tud térjen vissza az Outlook-alkalmazás

- Kéri, hogy az eszköz regisztrálása

- Képes az Intune alkalmazásvédelmi szabályzatának fogadásához

- Képes-e-mailek elérése

Minden olyan Intune alkalmazásvédelmi szabályzatai az alkalmazás vállalati adatokhoz való hozzáférését a kell lennie, és kérheti a felhasználót, hogy indítsa újra az alkalmazást, egy további PIN-kód stb. használata (Ha be van állítva, az alkalmazás és a platform esetében).

### <a name="configuration"></a>Konfiguráció

**1. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/01.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: Minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **Felhőalapú alkalmazások:** Felhőbeli alkalmazásokat, ki kell választania **Office 365 Exchange Online**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

4. **Feltételek:** Mint **feltételek**, konfigurálnia kell **eszközplatformok** és **ügyfélalkalmazás**:

    a. Mint **eszközplatformok**válassza **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/03.png)

    b. Mint **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali alkalmazások** és **Modern hitelesítési ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/91.png)

5. Mint **hozzáférés-vezérlés**, rendelkeznie kell **megkövetelése alkalmazásvédelmi szabályzat (előzetes verzió)** kiválasztott.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/05.png)
 

**2. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-Active Sync (EAS)**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/06.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: Minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.


3. **Felhőalapú alkalmazások:** Felhőbeli alkalmazásokat, ki kell választania **Office 365 Exchange Online**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

4. **Feltételek:** Mint **feltételek**, konfigurálnia kell **ügyfélalkalmazások (előzetes verzió)**. 

    a. Mint **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali ügyfelek** és **Exchange ActiveSync-ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/92.png)

    b. Mint **hozzáférés-vezérlés**, rendelkeznie kell **megkövetelése alkalmazásvédelmi szabályzat (előzetes verzió)** kiválasztott.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/05.png)


**3. lépés – az Intune alkalmazásvédelmi szabályzatot az iOS és Android ügyfélalkalmazások konfigurálása**


![Feltételes hozzáférés](./media/app-protection-based-conditional-access/09.png)

Lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) további információt.



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Az Exchange Online protection vagy a megfelelő eszköz alkalmazásszabályzat

Ez a forgatókönyv áll egy app protection-alapú vagy a megfelelő eszköz feltételes hozzáférési szabályzat Exchange online-hoz való hozzáféréshez.


### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy:
 
- Néhány felhasználó már regisztrálva lett (a vagy vállalati eszközök nélkül)

- Felhasználók, akik nem regisztrált, és az Azure AD-alkalmazással regisztrált alkalmazás hogy védett erőforrások eléréséhez az eszköz regisztrálása

- Az alkalmazás által védett alkalmazás használatával beléptetett felhasználóknak nem kell újra regisztrálni az eszközt

- Felhasználói fogadhat alkalmazásvédelmi szabályzatot az Intune Ha nem regisztrált

- Felhasználói hozzáférhetnek e-mailt az Outlook és a egy az Intune alkalmazásvédelmi szabályzatot, ha nem regisztrált

- Felhasználó hozzáférhessen e-mailt az Outlookban, ha az eszköz regisztrálva van


### <a name="configuration"></a>Konfiguráció

**1. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/62.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: Minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **Felhőalapú alkalmazások:** Felhőbeli alkalmazásokat, ki kell választania **Office 365 Exchange Online**. 

     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

4. **Feltételek:** Mint **feltételek**, konfigurálnia kell **eszközplatformok** és **ügyfélalkalmazás**. 
 
    a. Mint **eszközplatformok**válassza **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/03.png)

    b. Mint **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali ügyfelek** és **Modern hitelesítési ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/91.png)

5. Mint **hozzáférés-vezérlés**, szüksége lesz kiválasztva a következőket:

   - **Eszköz megfelelőként való megjelölésének megkövetelése**

   - **Alkalmazásvédelmi szabályzat megkövetelése (előzetes verzió)**

   - **Az egyik kijelölt vezérlő megkövetelése**   
 
     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/11.png)



**2. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-Active Sync (EAS)**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/06.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: Minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **Felhőalapú alkalmazások:** Felhőbeli alkalmazásokat, ki kell választania **Office 365 Exchange Online**. 

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

4. **Feltételek:** Mint **feltételek**, konfigurálnia kell **ügyfélalkalmazás**. 

    Mint **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali ügyfelek** és **Exchange ActiveSync-ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/92.png)

5. Mint **hozzáférés-vezérlés**, szüksége lesz kiválasztva a következőket:

   - **Eszköz megfelelőként való megjelölésének megkövetelése**

   - **Alkalmazásvédelmi szabályzat megkövetelése (előzetes verzió)**

   - **Az egyik kijelölt vezérlő megkövetelése**   
    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/11.png)



**3. lépés – az Intune alkalmazásvédelmi szabályzatot az iOS és Android ügyfélalkalmazások konfigurálása**


![Feltételes hozzáférés](./media/app-protection-based-conditional-access/09.png)

Lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) további információt.





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Az Exchange Online protection és a megfelelő eszköz alkalmazásszabályzat

Ez a forgatókönyv áll az app-protection-alapú és a megfelelő eszköz feltételes hozzáférési szabályzat Exchange online-hoz való hozzáféréshez.


### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy egy felhasználó:
 
-   Konfigurálja az iOS vagy Android rendszeren natív mail alkalmazás használatával csatlakozni az Exchange e-mailben
-   Kap egy e-mailt, amely azt jelzi, hogy hozzáférést igényel a regisztrálni kívánt eszközön
-   Letölti a céges portálon, és bejelentkezik a vállalati portál
-   Mail ellenőrzi, és felkéri, hogy az Outlook alkalmazás használata
-   Az Outlook alkalmazás letöltése
-   Megnyitja az Outlook alkalmazást, és megadja az a regisztráció során használt hitelesítő adatok
-   Képes fogadni az Intune alkalmazásvédelmi szabályzat fogadása
-   Férhet hozzá az e-mailt, az Outlook és az Intune alkalmazásvédelmi szabályzat

Minden olyan Intune alkalmazásvédelmi szabályzatai a céges adatokhoz való hozzáférés előtt aktiválódnak, és kérheti a felhasználót, hogy indítsa újra az alkalmazást, egy további PIN-kód stb. használata (Ha be van állítva, az alkalmazás és a platform)


### <a name="configuration"></a>Konfiguráció

**1. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/01.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: Minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **Felhőalapú alkalmazások:** Felhőbeli alkalmazásokat, ki kell választania **Office 365 Exchange Online**. 

     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

4. **Feltételek:** Mint **feltételek**, konfigurálnia kell **eszközplatformok** és **ügyfélalkalmazás**. 
 
    a. Mint **eszközplatformok**válassza **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/03.png)

    b. Mint **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali alkalmazások** és **Modern hitelesítési ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/91.png)

5. Mint **hozzáférés-vezérlés**, szüksége lesz kiválasztva a következőket:

   - **Eszköz megfelelőként való megjelölésének megkövetelése**

   - **Alkalmazásvédelmi szabályzat megkövetelése (előzetes verzió)**

   - **Az összes kijelölt vezérlő megkövetelése**   
 
     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/13.png)



**2. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-Active Sync (EAS)**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/06.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: Minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **Felhőalapú alkalmazások:** Felhőbeli alkalmazásokat, ki kell választania **Office 365 Exchange Online**. 

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

4. **Feltételek:** Mint **feltételek**, konfigurálnia kell **ügyfélalkalmazások (előzetes verzió)**. 

    Mint **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali ügyfelek** és **Exchange ActiveSync-ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/92.png)

5. Mint **hozzáférés-vezérlés**, szüksége lesz kiválasztva a következőket:

   - **Eszköz megfelelőként való megjelölésének megkövetelése**

   - **Jóváhagyott ügyfélalkalmazás (előzetes verzió) megkövetelése**

   - **Az összes kijelölt vezérlő megkövetelése**   
 
     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/13.png)




**3. lépés – az Intune alkalmazásvédelmi szabályzatot az iOS és Android ügyfélalkalmazások konfigurálása**


![Feltételes hozzáférés](./media/app-protection-based-conditional-access/09.png)

Lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) további információt.


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>App protection- és alkalmazás-alapú szabályzat Exchange Online és SharePoint online-hoz

Ebben a forgatókönyvben egy Exchange Online és SharePoint online-hoz való hozzáférés védelme vagy a jóváhagyott alkalmazások szabályzatának áll.


### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy egy felhasználó:

- Konfigurálja vagy ügyfélalkalmazások, amelyek vagy az alkalmazás alkalmazásvédelmi szabályzat követelménye úgy vagy a jóváhagyott alkalmazások követelmény támogató alkalmazások listájában.  
- Felhasználó használja ügyfélalkalmazások, amelyek megfelelnek az alkalmazás alkalmazásvédelmi szabályzat követelménye úgy fogadhat az Intune alkalmazásvédelmi szabályzat
- Felhasználó használja az ügyfélalkalmazások, amelyek megfelelnek a jóváhagyott alkalmazások házirend követelmény, amely támogatja az Intune alkalmazásvédelmi szabályzata
- Az alkalmazás hozzáférését a dokumentumok vagy e-mailek megnyitása
- Az Outlook alkalmazás megnyílik, és jelentkezik be az Azure AD hitelesítő adatait
- Az Authenticator (iOS) vagy a céges portál (Android) a folytatáshoz telepítésére kéri (Ha nincs telepítve)
- Telepíti az alkalmazást és is tud térjen vissza az Outlook-alkalmazás
- Kéri, hogy az eszköz regisztrálása
- Képes az Intune alkalmazásvédelmi szabályzatának fogadásához
- Férhet hozzá az e-mailt, az Outlook és az Intune alkalmazásvédelmi szabályzat
- Nem érhetik el, hogy egy alkalmazás nem az alkalmazás alkalmazásvédelmi szabályzat követelménye úgy és helyek vagy dokumentumok, de szerepel a jóváhagyott alkalmazás megkövetelése az.

Minden olyan Intune alkalmazásvédelmi szabályzatai a céges adatokhoz való hozzáférés előtt meg kell adni, és kérheti a felhasználót, hogy indítsa újra az alkalmazást, egy további PIN-kód stb. használata (Ha be van állítva, az alkalmazás és a platform)

**Megjegyzések**

- Ebben a forgatókönyvben is használhatja, ha azt szeretné, mindkét védelmi-alapú és alkalmazásalapú feltételes hozzáférési szabályzatok támogatásához.

- A jelen *vagy* házirendet, az alkalmazások **alkalmazásvédelmi szabályzat** követelmény értékeli ki a hozzáférés előtt **jóváhagyott ügyfélalkalmazások** követelmény.

### <a name="configuration"></a>Konfiguráció

**1. lépés – az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzat konfigurálja a következő összetevőket kell:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/62.png)

1. A **neve** a feltételes hozzáférési szabályzat.

2. **Felhasználók és csoportok**: Minden egyes feltételes hozzáférési szabályzat rendelkeznie kell legalább egy kiválasztott felhasználó vagy csoport.

3. **Felhőalapú alkalmazások:** Felhőbeli alkalmazásokat, ki kell választania **Office 365 Exchange Online**. 

     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/02.png)

4. **Feltételek:** Mint **feltételek**, konfigurálnia kell **eszközplatformok** és **ügyfélalkalmazás**. 
 
    a. Mint **eszközplatformok**válassza **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/03.png)

    b. Mint **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali alkalmazások** és **Modern hitelesítési ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/91.png)

5. Mint **hozzáférés-vezérlés**, szüksége lesz kiválasztva a következőket:

   - **Jóváhagyott ügyfélalkalmazás megkövetelése**

   - **Alkalmazásvédelmi szabályzat megkövetelése (előzetes verzió)**

   - **Az egyik kijelölt vezérlő megkövetelése**   
 
     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/12.png)


**2. lépés – az Intune alkalmazásvédelmi szabályzatot az iOS és Android ügyfélalkalmazások konfigurálása**


![Feltételes hozzáférés](./media/app-protection-based-conditional-access/09.png)

Lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) további információt.




## <a name="next-steps"></a>További lépések

Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md).

Ha kész feltételes hozzáférési szabályzatokat konfigurálni a környezetében, tekintse át [az Azure Active Directory feltételes hozzáféréssel kapcsolatos ajánlott eljárásait](best-practices.md). 