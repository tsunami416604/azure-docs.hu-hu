---
title: Jóváhagyott ügyfélalkalmazások feltételes hozzáféréssel – Azure Active Directory
description: Megtudhatja, hogyan igényelhet jóváhagyott ügyfélalkalmazások a felhőalapú alkalmazásokhoz való hozzáféréshez Azure Active Directory-ben feltételes hozzáféréssel.
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
ms.openlocfilehash: c173d0e17166911e28fea3d1c5820879d17af4a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381126"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Útmutató: jóváhagyott ügyfélalkalmazások megkövetelése a Cloud app Accesshez feltételes hozzáféréssel 

Az alkalmazottak a személyes és munkahelyi feladatokhoz egyaránt használnak mobileszköz-eszközöket. Az adatvesztés elkerülése érdekében az alkalmazottak is hatékonyak lehetnek. A Azure Active Directory (Azure AD) feltételes hozzáféréssel korlátozhatja a Felhőbeli alkalmazásokhoz való hozzáférést a vállalati adatai védelme érdekében jóváhagyott ügyfélalkalmazások számára.  

Ez a témakör azt ismerteti, hogyan konfigurálhat olyan feltételes hozzáférési szabályzatokat, amelyek jóváhagyott ügyfélalkalmazások használatát igénylik.

## <a name="overview"></a>Áttekintés

Az [Azure ad feltételes hozzáférésével](overview.md)részletesen beállíthatja, hogy a jogosult felhasználók hogyan férhessenek hozzá az erőforrásokhoz. Például korlátozhatja a felhőalapú alkalmazásokhoz való hozzáférést a megbízható eszközökre.

Az [Intune app Protection-szabályzatok](https://docs.microsoft.com/intune/app-protection-policy) segítségével biztosíthatja a vállalat adatainak védelmét. Az Intune app Protection-szabályzatok nem igényelnek mobileszköz-kezelési (MDM) megoldást, amely lehetővé teszi, hogy a vállalati adatait az eszközök regisztrálása nélkül vagy anélkül biztosítsa az Eszközkezelő megoldásban.

Azure Active Directory feltételes hozzáférés lehetővé teszi, hogy korlátozza a Felhőbeli alkalmazásokhoz való hozzáférést az Intune app Protection-szabályzatokat támogató ügyfélalkalmazások számára. Például korlátozhatja az Exchange Online-hoz való hozzáférést az Outlook alkalmazáshoz.

A feltételes hozzáférési terminológiában ezek az ügyfélalkalmazások **jóváhagyott ügyfélalkalmazások**néven ismertek.  

![Feltételes hozzáférés](./media/app-based-conditional-access/05.png)

A jóváhagyott ügyfélalkalmazások listáját itt tekintheti meg: [jóváhagyott ügyfélalkalmazás-követelmény](technical-reference.md#approved-client-app-requirement).

Az alkalmazás-alapú feltételes hozzáférési szabályzatokat más házirendekkel kombinálhatja, például az [eszközökön alapuló feltételes hozzáférési szabályzatok](require-managed-devices.md) segítségével biztosítható, hogy a személyes és a vállalati eszközök adatainak védelme hogyan védhető.

## <a name="before-you-begin"></a>Előkészületek

Ez a témakör azt feltételezi, hogy már ismeri a következőt:

- A [jóváhagyott ügyfélalkalmazás-követelmény](technical-reference.md#approved-client-app-requirement) technikai útmutatója.
- A [Azure Active Directoryban a feltételes hozzáférés](overview.md)alapvető fogalmai.
- [Feltételes hozzáférési szabályzat konfigurálása](app-based-mfa.md).
- [Feltételes hozzáférési szabályzatok áttelepítése](best-practices.md#policy-migration).

## <a name="prerequisites"></a>Előfeltételek

Alkalmazás-alapú feltételes hozzáférési szabályzat létrehozásához Enterprise Mobility + Security vagy Azure Active Directory Premium előfizetéssel kell rendelkeznie, és a felhasználóknak licenccel kell rendelkezniük az EMS-hez vagy az Azure AD-hoz. 

## <a name="exchange-online-policy"></a>Exchange Online-szabályzat 

Ez a forgatókönyv egy alkalmazás-alapú feltételes hozzáférési szabályzatot tartalmaz az Exchange Online-hoz való hozzáféréshez.

### <a name="scenario-playbook"></a>Forgatókönyv forgatókönyve

Ez a forgatókönyv feltételezi, hogy a felhasználó:

- Az e-mailt az iOS-vagy Android-alapú natív posta alkalmazással konfigurálja az Exchange-hez való kapcsolódáshoz
- Egy e-mailt kap, amely azt jelzi, hogy a hozzáférés csak az Outlook alkalmazás használatával érhető el
- Az alkalmazás letöltése a hivatkozással
- Megnyitja az Outlook alkalmazást, és bejelentkezik az Azure AD hitelesítő adataival
- A rendszer a folytatáshoz a hitelesítő (iOS) vagy a Céges portál (Android) telepítését kéri
- Telepíti az alkalmazást, és visszatérhet az Outlook alkalmazásnak a folytatáshoz
- A rendszer egy eszköz regisztrálását kéri
- Képes hozzáférni az e-mailekhez

Minden Intune app Protection-szabályzat aktiválva van a vállalati adathozzáféréskor, és kérheti a felhasználónak, hogy indítsa újra az alkalmazást, használjon egy további PIN-kódot (ha be van állítva az alkalmazáshoz és a platformhoz).

### <a name="configuration"></a>Konfiguráció 

**1. lépés – Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzathoz a következő összetevőket kell konfigurálnia:

1. A feltételes hozzáférési szabályzat **neve** .
1. **Felhasználók és csoportok**: minden feltételes hozzáférési szabályzatnak legalább egy felhasználót vagy csoportot ki kell jelölnie.
1. **Felhőalapú alkalmazások:** A Cloud apps szolgáltatásban az **Office 365 Exchange Online**elemet kell választania.
1. **Feltételek:** A **feltételeknek**megfelelően konfigurálnia kell az **eszköz platformját** és az **ügyfélalkalmazások**:
   1. Az **eszközök platformja**területen válassza az **Android** és az **iOS**lehetőséget.
   1. **Ügyfélalkalmazásokként (előzetes verzió)** válassza a **Mobile apps és az asztali alkalmazások** és a **modern hitelesítési ügyfelek**lehetőséget.
1. A **hozzáférés-vezérléshez**meg kell adnia a **jóváhagyott ügyfélalkalmazás (előzetes verzió)** beállítást.

   ![Feltételes hozzáférés](./media/app-based-conditional-access/05.png)

**2. lépés – Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz az Active Sync (EAS) segítségével**

Ebben a lépésben a feltételes hozzáférési szabályzathoz a következő összetevőket kell konfigurálnia:

1. A feltételes hozzáférési szabályzat **neve** .
1. **Felhasználók és csoportok**: minden feltételes hozzáférési szabályzatnak legalább egy felhasználót vagy csoportot ki kell jelölnie.
1. **Felhőalapú alkalmazások:** A Cloud apps szolgáltatásban az **Office 365 Exchange Online**elemet kell választania.
1. **Feltételek:** **Feltételként**konfigurálnia kell az **ügyfélalkalmazások (előzetes verzió)** szolgáltatást. 
   1. **Ügyfélalkalmazásokként (előzetes verzió)** válassza a **Mobile apps és az asztali ügyfelek** és az **Exchange ActiveSync-ügyfelek**lehetőséget.
   1. A **hozzáférés-vezérléshez**meg kell adnia a **jóváhagyott ügyfélalkalmazás (előzetes verzió)** beállítást.

      ![Feltételes hozzáférés](./media/app-based-conditional-access/05.png)

**3. lépés – az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára**

További információért lásd: [alkalmazások és adatok védelme Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online és SharePoint Online-szabályzat

Ez a forgatókönyv feltételes hozzáférést biztosít a Mobile App Management-házirenddel az Exchange Online-hoz és a SharePoint Online-hoz a jóváhagyott alkalmazásokkal való hozzáféréshez.

### <a name="scenario-playbook"></a>Forgatókönyv forgatókönyve

Ez a forgatókönyv feltételezi, hogy a felhasználó:

- Megpróbál csatlakozni a SharePoint-alkalmazáshoz, és megtekintheti a vállalati webhelyeit is
- Megkísérli a bejelentkezést ugyanazzal a hitelesítő adatokkal, mint az Outlook-alkalmazás hitelesítő adatai
- Nem kell újra regisztrálnia, és hozzá tud férni az erőforrásokhoz

### <a name="configuration"></a>Konfiguráció

**1. lépés – Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz és a SharePoint Online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzathoz a következő összetevőket kell konfigurálnia:

1. A feltételes hozzáférési szabályzat **neve** .
1. **Felhasználók és csoportok**: minden feltételes hozzáférési szabályzatnak legalább egy felhasználót vagy csoportot ki kell jelölnie.
1. **Felhőalapú alkalmazások:** A Cloud apps szolgáltatásban az **office 365 Exchange Online** és az **Office 365 SharePoint Online**lehetőséget kell választania. 
1. **Feltételek:** A **feltételeknek**megfelelően konfigurálnia kell az **eszköz platformját** és az **ügyfélalkalmazások**:
   1. Az **eszközök platformja**területen válassza az **Android** és az **iOS**lehetőséget.
   1. **Ügyfélalkalmazásokként (előzetes verzió)** válassza a **Mobile apps és az asztali ügyfelek** és a **modern hitelesítési ügyfelek**lehetőséget.
1. A **hozzáférés-vezérléshez**meg kell adnia a **jóváhagyott ügyfélalkalmazás (előzetes verzió)** beállítást.

   ![Feltételes hozzáférés](./media/app-based-conditional-access/05.png)

**2. lépés – Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz az Active Sync (EAS) segítségével**

Ebben a lépésben a feltételes hozzáférési szabályzathoz a következő összetevőket kell konfigurálnia:

1. A feltételes hozzáférési szabályzat **neve** .
1. **Felhasználók és csoportok**: minden feltételes hozzáférési szabályzatnak legalább egy felhasználót vagy csoportot ki kell jelölnie.
1. **Felhőalapú alkalmazások:** A Cloud apps szolgáltatásban az **Office 365 Exchange Online**elemet kell választania. Online 
1. **Feltételek:** **Feltételként**konfigurálnia kell az **ügyfélalkalmazások**:
   1. **Ügyfélalkalmazásokként (előzetes verzió)** válassza a **Mobile apps és az asztali ügyfelek** és az **Exchange ActiveSync-ügyfelek**lehetőséget.
   1. A **hozzáférés-vezérléshez**meg kell adnia a **jóváhagyott ügyfélalkalmazás (előzetes verzió)** beállítást.

      ![Feltételes hozzáférés](./media/app-based-conditional-access/05.png)

**3. lépés – az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára**

![Feltételes hozzáférés](./media/app-based-conditional-access/09.png)

További információért lásd: [alkalmazások és adatok védelme Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Alkalmazás-vagy megfelelő eszköz házirend az Exchange Online-hoz és a SharePoint Online-hoz

Ez a forgatókönyv egy alkalmazás-vagy megfelelő feltételes hozzáférési szabályzatot tartalmaz az Exchange Online-hoz való hozzáféréshez.

### <a name="scenario-playbook"></a>Forgatókönyv forgatókönyve

Ez a forgatókönyv feltételezi, hogy:
 
- Néhány felhasználó már regisztrálva van (vállalati eszközökkel vagy anélkül)
- Azok a felhasználók, akik nem regisztráltak és az Azure AD-ben regisztrálnak egy alkalmazás által védett alkalmazás használatával, regisztrálniuk kell az eszközöket az erőforrásokhoz való hozzáféréshez
- Az alkalmazás által védett alkalmazást használó regisztrált felhasználóknak nem kell újra regisztrálniuk az eszközt

### <a name="configuration"></a>Konfiguráció

**1. lépés – Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz és a SharePoint Online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzathoz a következő összetevőket kell konfigurálnia:

1. A feltételes hozzáférési szabályzat **neve** .
1. **Felhasználók és csoportok**: minden feltételes hozzáférési szabályzatnak legalább egy felhasználót vagy csoportot ki kell jelölnie.
1. **Felhőalapú alkalmazások:** A Cloud apps szolgáltatásban az **office 365 Exchange Online** és az **Office 365 SharePoint Online**lehetőséget kell választania. 
1. **Feltételek:** A **feltételeknek**megfelelően kell konfigurálnia az **eszköz platformját** és az **ügyfélalkalmazások**konfigurációját. 
   1. Az **eszközök platformja**területen válassza az **Android** és az **iOS**lehetőséget.
   1. **Ügyfélalkalmazásokként (előzetes verzió)** válassza a **Mobile apps és az asztali ügyfelek** és a **modern hitelesítési ügyfelek**lehetőséget.
1. **Hozzáférés-vezérlésként**a következők közül kell kiválasztania:
   - **Eszköz megfelelőként való megjelölésének megkövetelése**
   - **Jóváhagyott ügyfélalkalmazás megkövetelése (előzetes verzió)**
   - **A kiválasztott vezérlők egyikének megkövetelése**   
 
      ![Feltételes hozzáférés](./media/app-based-conditional-access/11.png)

**2. lépés – Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz az Active Sync (EAS) segítségével**

Ebben a lépésben a feltételes hozzáférési szabályzathoz a következő összetevőket kell konfigurálnia:

1. A feltételes hozzáférési szabályzat **neve** .
1. **Felhasználók és csoportok**: minden feltételes hozzáférési szabályzatnak legalább egy felhasználót vagy csoportot ki kell jelölnie.
1. **Felhőalapú alkalmazások:** A Cloud apps szolgáltatásban az **Office 365 Exchange Online**elemet kell választania. 
1. **Feltételek:** **Feltételként**konfigurálnia kell az **ügyfélalkalmazások**konfigurációját. 
   1. **Ügyfélalkalmazásokként (előzetes verzió)** válassza a **Mobile apps és az asztali ügyfelek** és az **Exchange ActiveSync-ügyfelek**lehetőséget.
1. A **hozzáférés-vezérléshez**meg kell adnia a **jóváhagyott ügyfélalkalmazás (előzetes verzió)** beállítást.
 
   ![Feltételes hozzáférés](./media/app-based-conditional-access/11.png)

**3. lépés – az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára**

![Feltételes hozzáférés](./media/app-based-conditional-access/09.png)

További információért lásd: [alkalmazások és adatok védelme Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Alkalmazás-és megfelelő eszköz-házirend az Exchange Online-hoz és a SharePoint Online-hoz

Ez a forgatókönyv egy, az Exchange Online-hoz való hozzáférésre vonatkozó, alkalmazáson alapuló és megfelelő feltételes hozzáférési szabályzatot tartalmaz.

### <a name="scenario-playbook"></a>Forgatókönyv forgatókönyve

Ez a forgatókönyv feltételezi, hogy a felhasználó:
 
- Az e-mailt az iOS-vagy Android-alapú natív posta alkalmazással konfigurálja az Exchange-hez való kapcsolódáshoz
- Egy e-mailt kap, amely azt jelzi, hogy a hozzáféréshez az eszköz regisztrálása szükséges
- Letölti a vállalati portált, és bejelentkezik a vállalati portálra
- E-mailek ellenőrzése és az Outlook alkalmazás használatának megválaszolása
- Az Outlook alkalmazás letöltése
- Megnyitja az Outlook alkalmazást, és megadja a regisztráció során használt hitelesítő adatokat
- A felhasználó hozzáférhet az e-mailekhez

Minden Intune app Protection-szabályzat aktiválva van a vállalati információkhoz való hozzáféréskor, és kérheti a felhasználót, hogy indítsa újra az alkalmazást, használjon egy további PIN-kódot (ha be van állítva az alkalmazáshoz és a platformhoz).

### <a name="configuration"></a>Konfiguráció

**1. lépés – Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz és a SharePoint Online-hoz**

Ebben a lépésben a feltételes hozzáférési szabályzathoz a következő összetevőket kell konfigurálnia:

1. A feltételes hozzáférési szabályzat **neve** .
1. **Felhasználók és csoportok**: minden feltételes hozzáférési szabályzatnak legalább egy felhasználót vagy csoportot ki kell jelölnie.
1. **Felhőalapú alkalmazások:** A Cloud apps szolgáltatásban az **office 365 Exchange Online** és az **Office 365 SharePoint Online**lehetőséget kell választania. 
1. **Feltételek:** A **feltételeknek**megfelelően kell konfigurálnia az **eszköz platformját** és az **ügyfélalkalmazások**konfigurációját. 
   1. Az **eszközök platformja**területen válassza az **Android** és az **iOS**lehetőséget.
   1. **Ügyfélalkalmazásokként (előzetes verzió)** válassza a **Mobile apps és az asztali alkalmazások** és a **modern hitelesítési ügyfelek**lehetőséget.
1. **Hozzáférés-vezérlésként**a következők közül kell kiválasztania:
   - **Eszköz megfelelőként való megjelölésének megkövetelése**
   - **Jóváhagyott ügyfélalkalmazás megkövetelése (előzetes verzió)**
   - **Az összes kijelölt vezérlő megkövetelése**   
 
      ![Feltételes hozzáférés](./media/app-based-conditional-access/13.png)

**2. lépés – Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz az Active Sync (EAS) segítségével**

Ebben a lépésben a feltételes hozzáférési szabályzathoz a következő összetevőket kell konfigurálnia:

1. A feltételes hozzáférési szabályzat **neve** .
1. **Felhasználók és csoportok**: minden feltételes hozzáférési szabályzatnak legalább egy felhasználót vagy csoportot ki kell jelölnie.
1. **Felhőalapú alkalmazások:** A Cloud apps szolgáltatásban az **Office 365 Exchange Online**elemet kell választania. 
1. **Feltételek:** **Feltételként**konfigurálnia kell az **ügyfélalkalmazások (előzetes verzió)** szolgáltatást. 
   1. **Ügyfélalkalmazásokként (előzetes verzió)** válassza a **Mobile apps és az asztali ügyfelek** és az **Exchange ActiveSync-ügyfelek**lehetőséget.

   ![Feltételes hozzáférés](./media/app-based-conditional-access/92.png)

1. **Hozzáférés-vezérlésként**a következők közül kell kiválasztania:
   - **Eszköz megfelelőként való megjelölésének megkövetelése**
   - **Jóváhagyott ügyfélalkalmazás megkövetelése (előzetes verzió)**
   - **Az összes kijelölt vezérlő megkövetelése**   

**3. lépés – az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára**

![Feltételes hozzáférés](./media/app-based-conditional-access/09.png)

További információért lásd: [alkalmazások és adatok védelme Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) .

## <a name="next-steps"></a>További lépések

Ha tudni szeretné, hogyan konfigurálhat egy feltételes hozzáférési szabályzatot, tekintse meg a többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md)című témakört.

Ha készen áll a környezet feltételes hozzáférési házirendjeinek konfigurálására, tekintse meg az [ajánlott eljárásokat a feltételes hozzáféréshez Azure Active Directory](best-practices.md). 
