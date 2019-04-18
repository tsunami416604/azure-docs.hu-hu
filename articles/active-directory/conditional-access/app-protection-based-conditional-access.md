---
title: Felhőalapú alkalmazás-hozzáférés az Azure Active Directory feltételes hozzáférés szükséges az alkalmazásvédelmi szabályzat |} A Microsoft Docs
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
ms.openlocfilehash: 2250449c0ef342332945b80cb10cb9a02885b259
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496930"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Alkalmazásvédelmi szabályzat szükséges a cloud app hozzáféréshez a feltételes hozzáférés (előzetes verzió)

Az alkalmazottak mobileszközöket használnak a személyes és munkahelyi feladatokhoz. Gondoskodik róla, hogy az alkalmazottak számára hatékony munkavégzést, miközben is szeretné megakadályozni az adatvesztést. Az Azure Active Directory (Azure AD) feltételes hozzáférés a felhőbeli alkalmazásokhoz való hozzáférés korlátozásával is a vállalati adatok védelme. Először az alkalmazásvédelmi szabályzat az ügyfél alkalmazásokat.

Ez a cikk bemutatja, hogyan konfigurálhatja a feltételes hozzáférési szabályzatokat, amelyek férjenek hozzá adatokat egy alkalmazásvédelmi szabályzatot is szükség.

## <a name="overview"></a>Áttekintés

A [Azure AD feltételes hozzáférés](overview.md), finomhangolhatja hogyan engedéllyel rendelkező felhasználók férhetnek hozzá az erőforrásokat. Például korlátozhatja a hozzáférést a felhőalkalmazásokhoz, megbízható eszközökre.

Használhat [az Intune alkalmazásvédelmi szabályzatai](https://docs.microsoft.com/intune/app-protection-policy) a vállalati adatok védelme érdekében. Az Intune alkalmazásvédelmi szabályzatai nem igénylik a mobileszköz-felügyelet (MDM) megoldás. A vállalati adatok vagy egy Eszközkezelési megoldás eszközök regisztrációja nélkül védheti meg.

Az Azure Active Directory feltételes hozzáférés Intune-ban az Azure AD szolgáltatásba az alkalmazásvédelmi szabályzat fogadása jelentett ügyfélalkalmazások számára a felhőalkalmazások korlátozza a hozzáférést. Például korlátozhatja hozzáférést az Exchange online-hoz az Outlook alkalmazást, amely rendelkezik az Intune alkalmazásvédelmi szabályzat.

A feltételes hozzáférés terminológia ügyfél alkalmazások ismert házirend által védett lehet egy *alkalmazásvédelmi szabályzat*.  

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/05.png)

Az ügyfél a házirend által védett alkalmazások listáját lásd: [alkalmazás alkalmazásvédelmi szabályzat követelménye úgy](technical-reference.md#approved-client-app-requirement).

App-protection-alapú feltételes hozzáférési szabályzatok más szabályzatok, például kombinálhatja [eszközalapú feltételes hozzáférési szabályzatok](require-managed-devices.md). Ezzel a módszerrel megadhatja rugalmasságot biztosítanak a személyes és vállalati eszközök esetén az adatok védelme.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>App protection-alapú feltételes hozzáférés követelmény előnyei

Által jelentett Intune iOS és Android rendszerű felügyelt eszköz esetén most már a jelentések az Azure AD, ha egy alkalmazásvédelmi szabályzatot alkalmazza az Intune megfelelőségi hasonló. Feltételes hozzáférés is használhatja ezt a házirendet hozzáférés-ellenőrzést. Az új feltételes hozzáférési házirend, az alkalmazásvédelmi szabályzatot, növeli a biztonságot. Ez tranzakciómentes rendszergazdai hibák, például:

- Felhasználók, akik nem rendelkeznek az Intune-licencet.
- Felhasználók, akik az Intune alkalmazásvédelmi szabályzat nem tud fogadni.
- Intune app protection-szabályzat alkalmazások, amelyek a szabályzat fogadásához levelezésre.


## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy ismeri a:

- A [alkalmazás alkalmazásvédelmi szabályzat követelménye úgy](technical-reference.md#app-protection-policy-requirement) műszaki útmutatója.
- A [jóváhagyott alkalmazás megkövetelése ügyfél](technical-reference.md#approved-client-app-requirement) műszaki útmutatója.
- Az alapvető fogalmait [feltételes hozzáférés az Azure Active Directory](overview.md).
- Hogyan [feltételes hozzáférési házirend konfigurálása](app-based-mfa.md).


## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy védelmi alkalmazásalapú feltételes hozzáférési szabályzatot, a következőket kell tennie:

- Enterprise Mobility + Security vagy egy Azure Active Directory premium előfizetéssel + Intune rendelkezik.
- Győződjön meg arról, hogy a felhasználók az Enterprise Mobility + Security vagy Azure AD-licencek + Intune-ban.
- Győződjön meg arról, hogy az ügyfélalkalmazás az Intune-ban alkalmazásvédelmi szabályzatokkal fogadására van konfigurálva.
- Győződjön meg arról, hogy a felhasználók az Intune alkalmazásvédelmi szabályzatának fogadásához az Intune-ban vannak konfigurálva.

## <a name="app-protection-based-policy-for-exchange-online"></a>App protection-alapú szabályzat Exchange online-hoz

Ez a forgatókönyv áll egy védelmi alkalmazásalapú feltételes hozzáférési szabályzatot az Exchange online-hoz való hozzáféréshez.

### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy egy felhasználó:

- Konfigurálja az e-mail használatával egy natív e-mail alkalmazás iOS vagy Android rendszeren való Exchange-hez való csatlakozáshoz.
- Kap egy e-mailt, amely azt jelzi, hogy hozzáférés csak az Outlook alkalmazás használatával érhető el.
- Letölti az alkalmazást a hivatkozásra.
- Az Outlook alkalmazás megnyílik, és bejelentkezik az Azure AD hitelesítő adatait.
- Az iOS használja a Microsoft Authenticator vagy az Intune vállalati portál Android használatra folytatja telepítésére kéri.
- Telepíti az alkalmazást, és az Outlook alkalmazást, a folytatáshoz adja vissza.
- A kéri, hogy az eszköz regisztrálása.
- Az Intune alkalmazásvédelmi szabályzat kapnak.
- E-mailekhez férhet.

Az alkalmazás hozzáférését a vállalati adatok Intune alkalmazásvédelmi szabályzat sem kell lennie. A házirendek előfordulhat, hogy kérni a felhasználót, indítsa újra az alkalmazást, vagy egy további PIN-kód használatához. Ez a helyzet, ha a házirendek beállítása az alkalmazás és a platform.

### <a name="configuration"></a>Konfiguráció

**1. lépés: Egy Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzat adja meg az alábbi összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/01.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.

2. A **hozzárendelések**, a **felhasználók és csoportok**, válasszon ki legalább egy felhasználót vagy csoportot minden egyes feltételes hozzáférési szabályzathoz.

3. A **Felhőalkalmazások**válassza **Office 365 Exchange Online**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

4. A **feltételek**, konfigurálja **eszközplatformok** és **ügyfélalkalmazások (előzetes verzió)**:

    a. A **eszközplatformok**válassza **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/03.png)

    b. A **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali ügyfelek** és **Modern hitelesítési ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/91.png)

5. A **hozzáférés-vezérlés**válassza **megkövetelése alkalmazásvédelmi szabályzat (előzetes verzió)**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/05.png)
 

**2. lépés: Az Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange online-hoz az ActiveSync (EAS)**

Ebben a lépésben a feltételes hozzáférési szabályzat adja meg az alábbi összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/06.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.

2. A **hozzárendelések**, a **felhasználók és csoportok**, válasszon ki legalább egy felhasználót vagy csoportot minden egyes feltételes hozzáférési szabályzathoz.


3. A **Felhőalkalmazások**válassza **Office 365 Exchange Online**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

4. A **feltételek**, konfigurálja **ügyfélalkalmazások (előzetes verzió)**. 

    a. A **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali ügyfelek** és **Exchange ActiveSync-ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/92.png)

    b. A **hozzáférés-vezérlés**válassza **megkövetelése alkalmazásvédelmi szabályzat (előzetes verzió)**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/05.png)


**3. lépés: Az Intune alkalmazásvédelmi szabályzatot az iOS és Android ügyfélalkalmazások konfigurálása**


![Feltételes hozzáférés](./media/app-protection-based-conditional-access/09.png)

További információkért lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Az Exchange Online protection vagy a megfelelő eszköz alkalmazásszabályzat

Ez a forgatókönyv áll egy app protection-alapú vagy a megfelelő eszköz feltételes hozzáférési szabályzat Exchange online-hoz való hozzáféréshez.


### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy:
 
- A felhasználó már regisztrálva van, vagy a vállalati eszközök nélkül.
- Felhasználók, akik nem regisztrált és a egy alkalmazás használatával az Azure ad-vel regisztrált alkalmazás eszközöket, hogy az erőforrások eléréséhez regisztrálnia kell védeni.
- Regisztrált felhasználók, akik használják az alkalmazás által védett alkalmazást nem kell újra regisztrálni az eszközt.
- A felhasználó az Intune alkalmazásvédelmi szabályzat fogadhat Ha nem regisztrált.
- A felhasználó hozzáférhet az e-mailt az Outlook és a egy az Intune alkalmazásvédelmi szabályzatot, ha nem regisztrált.
- A felhasználó hozzáférhessen e-mailt az Outlookban, ha az eszköz regisztrálva van.


### <a name="configuration"></a>Konfiguráció

**1. lépés: Egy Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzat adja meg az alábbi összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/62.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.

2. A **hozzárendelések**, a **felhasználók és csoportok**, válasszon ki legalább egy felhasználót vagy csoportot minden egyes feltételes hozzáférési szabályzathoz.

3. A **Felhőalkalmazások**válassza **Office 365 Exchange Online**. 

     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

4. A **feltételek**, konfigurálja **eszközplatformok** és **ügyfélalkalmazások (előzetes verzió)**. 
 
    a. A **eszközplatformok**válassza **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/03.png)

    b. A **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali ügyfelek** és **Modern hitelesítési ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/91.png)

5. A **hozzáférés-vezérlés**, válassza ki a következő beállításokat:

   - **Megfelelőként megjelölt eszköz megkövetelése**

   - **Megkövetelése alkalmazásvédelmi szabályzat (előzetes verzió)**

   - **A kijelölt feltételek egyikének megkövetelése**   
 
     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/11.png)



**2. lépés: Egy Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online, az ActiveSync szolgáltatással**

Ebben a lépésben a feltételes hozzáférési szabályzat adja meg az alábbi összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/06.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.

2. A **hozzárendelések**, a **felhasználók és csoportok**, válasszon ki legalább egy felhasználót vagy csoportot minden egyes feltételes hozzáférési szabályzathoz.

3. A **Felhőalkalmazások**válassza **Office 365 Exchange Online**. 

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

4. A **feltételek**, konfigurálja **ügyfélalkalmazások (előzetes verzió)**. 

    A **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali ügyfelek** és **Exchange ActiveSync-ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/92.png)

5. A **hozzáférés-vezérlés**, válassza ki a következő beállításokat:

   - **Megfelelőként megjelölt eszköz megkövetelése**

   - **Megkövetelése alkalmazásvédelmi szabályzat (előzetes verzió)**

   - **A kijelölt feltételek egyikének megkövetelése**

     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/11.png)



**3. lépés: Az Intune alkalmazásvédelmi szabályzatot az iOS és Android ügyfélalkalmazások konfigurálása**


![Feltételes hozzáférés](./media/app-protection-based-conditional-access/09.png)

További információkért lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Az Exchange Online protection és a megfelelő eszköz alkalmazásszabályzat

Ez a forgatókönyv áll az app-protection-alapú és a megfelelő eszköz feltételes hozzáférési szabályzat Exchange online-hoz való hozzáféréshez.


### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy egy felhasználó:
 
-   Konfigurálja az e-mail használatával egy natív e-mail alkalmazás iOS vagy Android rendszeren való Exchange-hez való csatlakozáshoz.
-   Kap egy e-mailt, amely azt jelzi, hogy hozzáférést igényel az eszközt regisztrálni kell.
-   Letölti az Intune vállalati portál, és bejelentkezik a portálra.
-   Mail ellenőrzi, és felkéri, hogy az Outlook alkalmazás használata.
-   Az Outlook alkalmazás letöltése.
-   Megnyitja az Outlook alkalmazást, és beírja az a regisztráció során használt hitelesítő adatok.
-   Az Intune alkalmazásvédelmi szabályzat kapnak.
-   E-mailt, az Outlook és az Intune alkalmazásvédelmi szabályzat férhetnek hozzá.

Minden olyan Intune alkalmazásvédelmi szabályzatai rendszer aktiválja a vállalati adatokhoz való hozzáférés előtt. A házirendek előfordulhat, hogy kérni a felhasználót, indítsa újra az alkalmazást, vagy egy további PIN-kód használatához. Ez a helyzet, ha a házirendek beállítása az alkalmazás és a platform.


### <a name="configuration"></a>Konfiguráció

**1. lépés: Egy Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzat adja meg az alábbi összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/01.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.

2. A **hozzárendelések**, a **felhasználók és csoportok**, válasszon ki legalább egy felhasználót vagy csoportot minden egyes feltételes hozzáférési szabályzathoz.

3. A **Felhőalkalmazások**válassza **Office 365 Exchange Online**. 

     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

4. A **feltételek**, konfigurálja **eszközplatformok** és **ügyfélalkalmazások (előzetes verzió)**. 
 
    a. A **eszközplatformok**válassza **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/03.png)

    b. A **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali ügyfelek** és **Modern hitelesítési ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/91.png)

5. A **hozzáférés-vezérlés**, válassza ki a következő beállításokat:

   - **Megfelelőként megjelölt eszköz megkövetelése**

   - **Megkövetelése alkalmazásvédelmi szabályzat (előzetes verzió)**

   - **Az összes kijelölt vezérlő megkövetelése**   
 
     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/13.png)



**2. lépés: Egy Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online, az ActiveSync szolgáltatással**

Ebben a lépésben a feltételes hozzáférési szabályzat adja meg az alábbi összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/06.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.

2. A **hozzárendelések**, a **felhasználók és csoportok**, válasszon ki legalább egy felhasználót vagy csoportot minden egyes feltételes hozzáférési szabályzathoz.

3. A **Felhőalkalmazások**válassza **Office 365 Exchange Online**. 

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

4. A **feltételek**, konfigurálja **ügyfélalkalmazások (előzetes verzió)**. 

    A **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali ügyfelek** és **Exchange ActiveSync-ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/92.png)

5. A **hozzáférés-vezérlés**, válassza ki a következő beállításokat:

   - **Megfelelőként megjelölt eszköz megkövetelése**

   - **Megkövetelése alkalmazásvédelmi szabályzat (előzetes verzió)**

   - **Az összes kijelölt vezérlő megkövetelése**   
 
     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/13.png)




**3. lépés: Az Intune alkalmazásvédelmi szabályzatot az iOS és Android ügyfélalkalmazások konfigurálása**


![Feltételes hozzáférés](./media/app-protection-based-conditional-access/09.png)

További információkért lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>App protection- és alkalmazás-alapú szabályzat Exchange Online és SharePoint online-hoz

Ebben a forgatókönyvben egy Exchange Online és SharePoint online-hoz való hozzáférés védelme vagy a jóváhagyott alkalmazások szabályzatának áll.


### <a name="scenario-playbook"></a>A forgatókönyv forgatókönyv

Ez a forgatókönyv feltételezi, hogy egy felhasználó:

- Konfigurálja az ügyfélalkalmazások, amelyek vagy az alkalmazás alkalmazásvédelmi szabályzat követelménye úgy vagy a jóváhagyott alkalmazások követelmény támogató alkalmazások listájában.  
- Ügyfélalkalmazások, amelyek megfelelnek az alkalmazás alkalmazásvédelmi szabályzat követelménye úgy, és egy az Intune alkalmazásvédelmi szabályzatának fogadásához is használ.
- Használja az ügyfélalkalmazások, amelyek megfelelnek a jóváhagyott alkalmazások házirend követelmény, amely támogatja az Intune alkalmazásvédelmi szabályzata.
- Megnyílik az alkalmazás dokumentumok vagy e-mailek eléréséhez.
- Az Outlook alkalmazás megnyílik, és bejelentkezik az Azure AD hitelesítő adatait.
- Kéri, hogy telepítse az iOS használja a Microsoft Authenticator vagy az Intune vállalati portál Android használatra, ha Ön még nem telepítette.
- Telepíti az alkalmazást és is tud térjen vissza az Outlookot, a folytatáshoz.
- A kéri, hogy az eszköz regisztrálása.
- Az Intune alkalmazásvédelmi szabályzat kapnak.
- E-mailt, az Outlook és az Intune alkalmazásvédelmi szabályzat férhetnek hozzá.
- Helyek és a egy alkalmazás nem az alkalmazás alkalmazásvédelmi szabályzat követelménye úgy és dokumentumok hozzáférhet, de a jóváhagyott alkalmazás megkövetelése szerepel.

Minden olyan Intune alkalmazásvédelmi szabályzatai szükség, hogy hozzáférést kapjanak a vállalati adatokhoz. A házirendek előfordulhat, hogy kérni a felhasználót, indítsa újra az alkalmazást, vagy egy további PIN-kód használatához. Ez a helyzet, ha a házirendek beállítása az alkalmazás és a platform.

**Megjegyzések**

- Ebben a forgatókönyvben is használhatja, ha azt szeretné, mindkét védelmi-alapú és alkalmazásalapú feltételes hozzáférési szabályzatok támogatásához.
- A jelen *vagy* értékeli ki a szabályzatot, az alkalmazás alkalmazásvédelmi szabályzat követelménye úgy alkalmazások hozzáférés előtt a jóváhagyott alkalmazások követelménynek.

### <a name="configuration"></a>Konfiguráció

**1. lépés: Egy Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzat adja meg az alábbi összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/62.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.

2. A **hozzárendelések**, a **felhasználók és csoportok**, válasszon ki legalább egy felhasználót vagy csoportot minden egyes feltételes hozzáférési szabályzathoz.

3. A **Felhőalkalmazások**válassza **Office 365 Exchange Online**. 

     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/02.png)

4. A **feltételek**, konfigurálja **eszközplatformok** és **ügyfélalkalmazások (előzetes verzió)**. 
 
    a. A **eszközplatformok**válassza **Android** és **iOS**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/03.png)

    b. A **ügyfélalkalmazások (előzetes verzió)** válassza **mobilalkalmazások és asztali ügyfelek** és **Modern hitelesítési ügyfelek**.

    ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/91.png)

5. A **hozzáférés-vezérlés**, válassza ki a következő beállításokat:

   - **Jóváhagyott ügyfélalkalmazás megkövetelése**

   - **Megkövetelése alkalmazásvédelmi szabályzat (előzetes verzió)**

   - **A kijelölt feltételek egyikének megkövetelése**
 
     ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/12.png)


**2. lépés: Az Intune alkalmazásvédelmi szabályzatot az iOS és Android ügyfélalkalmazások konfigurálása**


![Feltételes hozzáférés](./media/app-protection-based-conditional-access/09.png)

További információkért lásd: [alkalmazások és a Microsoft Intune-nal adatainak védelme](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).




## <a name="next-steps"></a>További lépések

- Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md).
- Ha készen áll a környezet feltételes hozzáférési szabályzatainak konfigurálása, lásd: [ajánlott eljárások az Azure Active Directory feltételes hozzáférés](best-practices.md). 