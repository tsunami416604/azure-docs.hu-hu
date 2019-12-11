---
title: Alkalmazás-védelmi szabályzatok feltételes hozzáféréssel – Azure Active Directory
description: Megtudhatja, hogyan igényelhet alkalmazás-védelmi szabályzatot a felhőalapú alkalmazásokhoz való hozzáféréshez Azure Active Directory-ben való feltételes hozzáféréssel.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbbe882acda532a54333ca8013693121d5b677b7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964089"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Alkalmazás-védelmi szabályzat megkövetelése a Cloud app Accesshez feltételes hozzáféréssel (előzetes verzió)

Az alkalmazottak mobileszközöket használnak a személyes és munkahelyi feladatokhoz. Az adatvesztés elkerülése érdekében az alkalmazottak is hatékonyak lehetnek. A Azure Active Directory (Azure AD) feltételes hozzáférés használatával a felhőalapú alkalmazásokhoz való hozzáférés korlátozásával védetté teheti a vállalati adatait. Először használja az ügyfélalkalmazások alkalmazást egy alkalmazás-védelmi szabályzattal.

Ez a cikk azt ismerteti, hogyan konfigurálhat olyan feltételes hozzáférési szabályzatokat, amelyek az alkalmazáshoz való hozzáférés megkövetelése előtt megkövetelhetik az alkalmazások védelmi házirendjét.

## <a name="overview"></a>Áttekintés

Az [Azure ad feltételes hozzáférésével](overview.md)részletesen beállíthatja, hogy a jogosult felhasználók hogyan férhessenek hozzá az erőforrásokhoz. Például korlátozhatja a felhőalapú alkalmazásokhoz való hozzáférést a megbízható eszközökre.

Az [Intune app Protection-szabályzatok](https://docs.microsoft.com/intune/app-protection-policy) segítségével biztosíthatja a vállalat adatainak védelmét. Az Intune app Protection-szabályzatok nem igényelnek mobileszköz-kezelési (MDM) megoldást. A vállalati adatait az eszközök regisztrálása nélkül is biztosíthatja az Eszközkezelő megoldásban.

Azure Active Directory feltételes hozzáférés korlátozza a felhőalapú alkalmazásokhoz való hozzáférést olyan ügyfélalkalmazások számára, amelyeket az Intune jelentett az Azure AD-nek az alkalmazás védelmi házirendjének fogadása során. Például korlátozhatja az Exchange Online-hoz való hozzáférést az Intune app Protection-szabályzattal rendelkező Outlook alkalmazáshoz.

A feltételes hozzáférési terminológiában ezek az ügyfélalkalmazások az *alkalmazás-védelmi szabályzattal*védett házirendek.  

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/05.png)

A szabályzattal védett ügyfélalkalmazások listáját lásd: az [alkalmazás védelmére vonatkozó követelmény](technical-reference.md#approved-client-app-requirement).

Az alkalmazás-védelem alapú feltételes hozzáférési szabályzatokat más házirendekkel kombinálhatja, például az [eszközökön alapuló feltételes hozzáférési szabályzatok](require-managed-devices.md)segítségével. Így rugalmasságot biztosíthat a személyes és a vállalati eszközök adatainak védelme terén is.

> [!NOTE]
> A feltételes hozzáférést biztosító alkalmazás-védelmi szabályzatok nem alkalmazhatók a B2B-felhasználókra, mert a meghívó szervezetnek nincs láthatósága a B2B-felhasználó otthoni szervezetében.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Az App Protection-alapú feltételes hozzáférési követelmények előnyei

Az Intune által az iOS-hez és az Androidhoz egy felügyelt eszközön jelentett megfelelőséghez hasonlóan az Intune mostantól jelentéseket küld az Azure AD-nek, ha alkalmazza az alkalmazás védelmi szabályzatát. A feltételes hozzáférés ezt a házirendet hozzáférés-ellenőrzési ként használhatja. Ez az új feltételes hozzáférési szabályzat, az alkalmazás-védelmi szabályzat, növeli a biztonságot. Védelmet biztosít a rendszergazdai hibák ellen, például:

- Azok a felhasználók, akik nem rendelkeznek Intune-licenccel.
- Azok a felhasználók, akik nem tudnak Intune app Protection-szabályzatot fogadni.
- Az Intune app Protection-szabályzatok nem a szabályzatok fogadására konfigurált alkalmazásai.

## <a name="before-you-begin"></a>Előzetes teendők

Ez a cikk azt feltételezi, hogy már ismeri a következőket:

- Az [alkalmazás védelmi házirendjének követelménye](technical-reference.md#app-protection-policy-requirement) technikai útmutató.
- A [jóváhagyott ügyfélalkalmazás-követelmény](technical-reference.md#approved-client-app-requirement) technikai útmutatója.
- A [Azure Active Directoryban a feltételes hozzáférés](overview.md)alapvető fogalmai.
- [Feltételes hozzáférési szabályzat konfigurálása](app-based-mfa.md).

## <a name="prerequisites"></a>Előfeltételek

Az alkalmazás védelme alapú feltételes hozzáférési szabályzat létrehozásához a következőket kell tennie:

- Enterprise Mobility + Security vagy Azure Active Directory Premium előfizetéssel és Intune-nal rendelkezik.
- Győződjön meg arról, hogy a felhasználók Enterprise Mobility + Security vagy az Azure AD + Intune licenccel rendelkeznek.
- Győződjön meg arról, hogy az ügyfélalkalmazás konfigurálva van az Intune-ban az alkalmazás védelmi házirendjének fogadásához.
- Győződjön meg arról, hogy a felhasználók az Intune-ban vannak konfigurálva az Intune app Protection-szabályzatok fogadásához.

## <a name="app-protection-based-policy-for-exchange-online"></a>App Protection-alapú házirend az Exchange Online-hoz

Ez a forgatókönyv egy app Protection-alapú feltételes hozzáférési szabályzatot tartalmaz az Exchange Online-hoz való hozzáféréshez.

### <a name="scenario-playbook"></a>Forgatókönyv forgatókönyve

Ez a forgatókönyv feltételezi, hogy a felhasználó:

- Az e-mailt az iOS-vagy Android-alapú natív posta alkalmazásával konfigurálhatja az Exchange-hez való kapcsolódáshoz.
- Egy e-mailt kap, amely azt jelzi, hogy a hozzáférés csak az Outlook alkalmazás használatával érhető el.
- Letölti az alkalmazást a hivatkozással.
- Megnyitja az Outlook alkalmazást, és bejelentkezik az Azure AD hitelesítő adataival.
- A folytatáshoz a rendszer a **Microsoft Authenticator alkalmazás** vagy a **Intune céges portál** telepítésére kéri.
- Telepíti az alkalmazást, és visszaadja az Outlook alkalmazásnak a folytatáshoz.
- A rendszer egy eszköz regisztrálását kéri.
- Az Intune app Protection-szabályzatot kaphat.
- Elérheti az e-maileket.

Minden Intune alkalmazás-védelmi szabályzatnak az alkalmazáson kell lennie a vállalati adateléréshez. Előfordulhat, hogy a házirendek arra kérik a felhasználót, hogy indítsa újra az alkalmazást, vagy használjon egy további PIN-kódot. Ebben az esetben, ha a szabályzatok az alkalmazáshoz és a platformhoz vannak konfigurálva.

### <a name="configuration"></a>Konfiguráció

**1. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz**

Az ebben a lépésben megadott feltételes hozzáférési szabályzat esetében konfigurálja a következő összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/01.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.
1. A **hozzárendelések**alatt a **felhasználók és csoportok**területen válasszon ki legalább egy felhasználót vagy csoportot az egyes feltételes hozzáférési házirendekhez.
1. A **Cloud apps**szolgáltatásban válassza az **Office 365 Exchange Online**lehetőséget.

   ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

1. A **feltételek**területen konfigurálja az **eszközök platformját** és az **ügyfélalkalmazások (előzetes verzió)** :
   1. Az **eszközök platformon**válassza az **Android** és az **iOS**lehetőséget.

      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/03.png)

   1. Az **ügyfélalkalmazások (előzetes verzió)** lapon válassza a **Mobile apps és az asztali ügyfelek** és a **modern hitelesítési ügyfelek**lehetőséget.

      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/91.png)

1. A **hozzáférés-vezérlés**területen válassza az **alkalmazás-védelmi házirend megkövetelése (előzetes verzió)** lehetőséget.

   ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/05.png)

**2. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz az ActiveSync (EAS) segítségével**

Az ebben a lépésben megadott feltételes hozzáférési szabályzat esetében konfigurálja a következő összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/06.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.
1. A **hozzárendelések**alatt a **felhasználók és csoportok**területen válasszon ki legalább egy felhasználót vagy csoportot az egyes feltételes hozzáférési házirendekhez.
1. A **Cloud apps**szolgáltatásban válassza az **Office 365 Exchange Online**lehetőséget.

   ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

1. A **feltételek**területen konfigurálja az **ügyfélalkalmazások (előzetes verzió)** szolgáltatást. 

   1. Az **ügyfélalkalmazások (előzetes verzió)** lapon válassza a **Mobile apps és az asztali ügyfelek** és az **Exchange ActiveSync-ügyfelek**lehetőséget.

      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/92.png)

   1. A **hozzáférés-vezérlés**területen válassza az **alkalmazás-védelmi házirend megkövetelése (előzetes verzió)** lehetőséget.

      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/05.png)

**3. lépés: az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára**

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/09.png)

További információ: [alkalmazások és adatok védelme Microsoft Intuneokkal](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>App Protection-alapú vagy megfelelő eszköz házirend az Exchange Online-hoz

Ez a forgatókönyv egy, az Exchange Online-hoz való hozzáférésre vonatkozó, alkalmazás-védelmi vagy megfelelőségi eszközre vonatkozó feltételes hozzáférési szabályzatot tartalmaz.

### <a name="scenario-playbook"></a>Forgatókönyv forgatókönyve

Ez a forgatókönyv feltételezi, hogy:
 
- Egy felhasználó már regisztrálva van, vállalati eszközökkel vagy anélkül.
- Azokat a felhasználókat, akik nem regisztráltak és az Azure AD-ben regisztrálva vannak, egy alkalmazás által védett alkalmazással kell regisztrálniuk az erőforrásokat az erőforrásokhoz való hozzáféréshez.
- Az alkalmazás által védett alkalmazást használó regisztrált felhasználóknak nem kell újra regisztrálniuk az eszközt.
- Ha nincs regisztrálva, a felhasználó egy Intune app Protection-szabályzatot is fogadhat.
- A felhasználó hozzáférhet az Outlookhoz és az Intune app Protection-szabályzathoz, ha nincs regisztrálva.
- A felhasználó az Outlookban elérheti az e-maileket, ha az eszköz regisztrálva van.

### <a name="configuration"></a>Konfiguráció

**1. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz**

Az ebben a lépésben megadott feltételes hozzáférési szabályzat esetében konfigurálja a következő összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/62.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.
1. A **hozzárendelések**alatt a **felhasználók és csoportok**területen válasszon ki legalább egy felhasználót vagy csoportot az egyes feltételes hozzáférési házirendekhez.
1. A **Cloud apps**szolgáltatásban válassza az **Office 365 Exchange Online**lehetőséget. 

   ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

1. A **feltételek**területen konfigurálja az **eszközök platformját** és az **ügyfélalkalmazások (előzetes verzió)** szolgáltatást. 
 
   1. Az **eszközök platformon**válassza az **Android** és az **iOS**lehetőséget.

      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/03.png)

   1. Az **ügyfélalkalmazások (előzetes verzió)** lapon válassza a **Mobile apps és az asztali ügyfelek** és a **modern hitelesítési ügyfelek**lehetőséget.

      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/91.png)

5. A **hozzáférés-vezérlés**területen válassza ki a következő beállításokat:
   - **Eszköz megfelelőként való megjelölésének megkövetelése**
   - **Alkalmazás-védelmi házirend megkövetelése (előzetes verzió)**
   - **A kiválasztott vezérlők egyikének megkövetelése**   
 
      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/11.png)

**2. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz az ActiveSync segítségével**

Az ebben a lépésben megadott feltételes hozzáférési szabályzat esetében konfigurálja a következő összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/06.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.
1. A **hozzárendelések**alatt a **felhasználók és csoportok**területen válasszon ki legalább egy felhasználót vagy csoportot az egyes feltételes hozzáférési házirendekhez.
1. A **Cloud apps**szolgáltatásban válassza az **Office 365 Exchange Online**lehetőséget. 

   ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

1. A **feltételek**területen konfigurálja az **ügyfélalkalmazások (előzetes verzió)** szolgáltatást. 

   Az **ügyfélalkalmazások (előzetes verzió)** lapon válassza a **Mobile apps és az asztali ügyfelek** és az **Exchange ActiveSync-ügyfelek**lehetőséget.

   ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/92.png)

1. A **hozzáférés-vezérlés**területen válassza ki a következő beállításokat:
   - **Eszköz megfelelőként való megjelölésének megkövetelése**
   - **Alkalmazás-védelmi házirend megkövetelése (előzetes verzió)**
   - **A kiválasztott vezérlők egyikének megkövetelése**

      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/11.png)

**3. lépés: az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára**

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/09.png)

További információ: [alkalmazások és adatok védelme Microsoft Intuneokkal](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>App Protection-alapú és megfelelő eszköz házirend az Exchange Online-hoz

Ez a forgatókönyv egy, az Exchange Online-hoz való hozzáférésre vonatkozó, alkalmazásra épülő és megfelelő eszköz feltételes hozzáférési szabályzatot tartalmaz.

### <a name="scenario-playbook"></a>Forgatókönyv forgatókönyve

Ez a forgatókönyv feltételezi, hogy a felhasználó:
 
- Az e-mailt az iOS-vagy Android-alapú natív posta alkalmazásával konfigurálhatja az Exchange-hez való kapcsolódáshoz.
- Egy e-mailt kap, amely azt jelzi, hogy a hozzáféréshez az eszköz regisztrálása szükséges.
- Letölti Intune Céges portál, és bejelentkezik a portálra.
- Ellenőrzi az e-maileket, és kéri az Outlook alkalmazás használatát.
- Letölti az Outlook alkalmazást.
- Megnyitja az Outlook alkalmazást, és megadja a regisztráció során használt hitelesítő adatokat.
- Az Intune app Protection-szabályzatot kaphat.
- Hozzáférhet az e-mailekhez az Outlook és az Intune app Protection-szabályzat segítségével.

Minden Intune app Protection-szabályzat aktiválva van, mielőtt hozzáférést kap a vállalati adatszolgáltatáshoz. Előfordulhat, hogy a házirendek arra kérik a felhasználót, hogy indítsa újra az alkalmazást, vagy használjon egy további PIN-kódot. Ebben az esetben, ha a szabályzatok az alkalmazáshoz és a platformhoz vannak konfigurálva.

### <a name="configuration"></a>Konfiguráció

**1. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz**

Az ebben a lépésben megadott feltételes hozzáférési szabályzat esetében konfigurálja a következő összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/01.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.
1. A **hozzárendelések**alatt a **felhasználók és csoportok**területen válasszon ki legalább egy felhasználót vagy csoportot az egyes feltételes hozzáférési házirendekhez.
1. A **Cloud apps**szolgáltatásban válassza az **Office 365 Exchange Online**lehetőséget. 

   ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

1. A **feltételek**területen konfigurálja az **eszközök platformját** és az **ügyfélalkalmazások (előzetes verzió)** szolgáltatást. 
   1. Az **eszközök platformon**válassza az **Android** és az **iOS**lehetőséget.

      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/03.png)

   1. Az **ügyfélalkalmazások (előzetes verzió)** lapon válassza a **Mobile apps és az asztali ügyfelek** és a **modern hitelesítési ügyfelek**lehetőséget.

      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/91.png)

1. A **hozzáférés-vezérlés**területen válassza ki a következő beállításokat:
   - **Eszköz megfelelőként való megjelölésének megkövetelése**
   - **Alkalmazás-védelmi házirend megkövetelése (előzetes verzió)**
   - **Az összes kijelölt vezérlő megkövetelése**   
 
      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/13.png)

**2. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz az ActiveSync segítségével**

Az ebben a lépésben megadott feltételes hozzáférési szabályzat esetében konfigurálja a következő összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/06.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.
1. A **hozzárendelések**alatt a **felhasználók és csoportok**területen válasszon ki legalább egy felhasználót vagy csoportot az egyes feltételes hozzáférési házirendekhez.
1. A **Cloud apps**szolgáltatásban válassza az **Office 365 Exchange Online**lehetőséget. 

   ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/07.png)

1. A **feltételek**területen konfigurálja az **ügyfélalkalmazások (előzetes verzió)** szolgáltatást. 

   Az **ügyfélalkalmazások (előzetes verzió)** lapon válassza a **Mobile apps és az asztali ügyfelek** és az **Exchange ActiveSync-ügyfelek**lehetőséget.

   ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/92.png)

1. A **hozzáférés-vezérlés**területen válassza ki a következő beállításokat:
   - **Eszköz megfelelőként való megjelölésének megkövetelése**
   - **Alkalmazás-védelmi házirend megkövetelése (előzetes verzió)**
   - **Az összes kijelölt vezérlő megkövetelése**   
 
      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/13.png)

**3. lépés: az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára**

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/09.png)

További információ: [alkalmazások és adatok védelme Microsoft Intuneokkal](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>App Protection-alapú vagy alkalmazáson alapuló házirend az Exchange Online-hoz és a SharePoint Online-hoz

Ez a forgatókönyv egy, az Exchange Online-hoz és a SharePoint Online-hoz való hozzáférést biztosító alkalmazás-védelmi vagy jóváhagyott alkalmazási szabályzatból áll.

### <a name="scenario-playbook"></a>Forgatókönyv forgatókönyve

Ez a forgatókönyv feltételezi, hogy a felhasználó:

- Az alkalmazás-védelmi házirend követelményét vagy a jóváhagyott alkalmazások követelményeit támogató alkalmazások listáján konfigurálja az ügyfélalkalmazások listáját.  
- Az alkalmazás-védelmi házirend követelményének megfelelő ügyfélalkalmazások használatát, és az Intune app Protection-szabályzatot is képes fogadni.
- Olyan ügyfélalkalmazások használatát használja, amelyek megfelelnek az Intune app Protection-szabályzatot támogató, jóváhagyott alkalmazásokra vonatkozó házirendnek.
- Megnyitja az alkalmazást az e-mailek vagy dokumentumok eléréséhez.
- Megnyitja az Outlook alkalmazást, és bejelentkezik az Azure AD hitelesítő adataival.
- A rendszer arra kéri, hogy telepítse a Microsoft Authenticator iOS-használatra, vagy Intune Céges portál Android-használatra, ha még nincsenek telepítve.
- Telepíti az alkalmazást, és a folytatáshoz visszatérhet az Outlook alkalmazáshoz.
- A rendszer egy eszköz regisztrálását kéri.
- Az Intune app Protection-szabályzatot kaphat.
- Hozzáférhet az e-mailekhez az Outlook és az Intune app Protection-szabályzat segítségével.
- Az alkalmazás nem az alkalmazás-védelmi szabályzat követelményeit, hanem a jóváhagyott alkalmazásra vonatkozó követelményben felsorolt alkalmazások számára is elérhetővé teheti a helyeket és a dokumentumokat.

Az Intune app Protection-szabályzatok szükségesek ahhoz, hogy hozzáférjenek a vállalati adatszolgáltatáshoz. Előfordulhat, hogy a házirendek arra kérik a felhasználót, hogy indítsa újra az alkalmazást, vagy használjon egy további PIN-kódot. Ebben az esetben, ha a szabályzatok az alkalmazáshoz és a platformhoz vannak konfigurálva.

**Megjegyzések**

- Ezt a forgatókönyvet akkor használhatja, ha az App Protection-alapú és az alkalmazás-alapú feltételes hozzáférési szabályzatokat is támogatni kívánja.
- Ebben *vagy* a szabályzatban az alkalmazásokra vonatkozó adatvédelmi szabályzattal kapcsolatos követelmények kiértékelése először a jóváhagyott ügyfélalkalmazás-követelmények előtt történik.

### <a name="configuration"></a>Konfiguráció

**1. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz és a SharePoint Online-hoz**

Az ebben a lépésben megadott feltételes hozzáférési szabályzat esetében konfigurálja a következő összetevőket:

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/62.png)

1. Adja meg a feltételes hozzáférési szabályzat nevét.
1. A **hozzárendelések**alatt a **felhasználók és csoportok**területen válasszon ki legalább egy felhasználót vagy csoportot az egyes feltételes hozzáférési házirendekhez.
1. A **Cloud apps**szolgáltatásban válassza az **Office 365 Exchange Online** és az **Office 365 SharePoint Online**lehetőséget. 

   ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/02.png)

1. A **feltételek**területen konfigurálja az **eszközök platformját** és az **ügyfélalkalmazások (előzetes verzió)** szolgáltatást. 
   1. Az **eszközök platformon**válassza az **Android** és az **iOS**lehetőséget.

      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/03.png)

   1. Az **ügyfélalkalmazások (előzetes verzió)** lapon válassza a **Mobile apps és az asztali ügyfelek** és a **modern hitelesítési ügyfelek**lehetőséget.

      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/91.png)

1. A **hozzáférés-vezérlés**területen válassza ki a következő beállításokat:
   - **Jóváhagyott ügyfélalkalmazás megkövetelése**
   - **Alkalmazás-védelmi házirend megkövetelése (előzetes verzió)**
   - **A kiválasztott vezérlők egyikének megkövetelése**
 
      ![Feltételes hozzáférés](./media/app-protection-based-conditional-access/12.png)

**2. lépés: az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára**

![Feltételes hozzáférés](./media/app-protection-based-conditional-access/09.png)

További információ: [alkalmazások és adatok védelme Microsoft Intuneokkal](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Következő lépések

- Ha tudni szeretné, hogyan konfigurálhat egy feltételes hozzáférési szabályzatot, tekintse meg a többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md)című témakört.
- Ha készen áll a környezet feltételes hozzáférési házirendjeinek konfigurálására, tekintse meg az [ajánlott eljárásokat a feltételes hozzáféréshez Azure Active Directory](best-practices.md).
