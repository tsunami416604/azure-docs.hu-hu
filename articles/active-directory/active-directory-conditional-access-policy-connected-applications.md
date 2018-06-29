---
title: Hogyan - szükség által kezelt eszköz alkalmazás-hozzáférési felhőalapú Azure Active Directory feltételes hozzáférésű |} Microsoft Docs
description: Megtudhatja, hogyan konfigurálja az Azure Active Directory (Azure AD) eszközalapú feltételes hozzáférési szabályzatok, amely felügyelt eszközök igényel a felhőbeli alkalmazás eléréséhez.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 066d25e8953a2be4bd64cdd1af79b7f2a25dd5f9
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036069"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Útmutató: Szükséges által kezelt eszköz alkalmazás-hozzáférési felhő feltételes hozzáféréssel

Mobileszköz-first, a felhő-első világában Azure Active Directory (Azure AD) lehetővé teszi, hogy az egyszeri bejelentkezés alkalmazásokhoz és szolgáltatásokhoz bárhonnan. Hitelesített felhasználók érhetik el a felhőalkalmazások eszközt, beleértve a mobil, és személyes eszközök széles skáláját. Azonban a legtöbb környezetben legalább néhány olyan alkalmazásokat, amelyek csak legyenek elérhetők a biztonsági és megfelelőségi szabványoknak megfelelő eszközök rendelkeznek. Ezek az eszközök felügyelt eszközökön is ismertek. 

Ez a cikk azt ismerteti, hogyan konfigurálhat feltételes hozzáférési szabályzatok, amelyek igénylik a felügyelt eszközök férhessenek hozzá az egyes felhőalapú alkalmazásokat a környezetben. 


## <a name="prerequisites"></a>Előfeltételek

Felügyelt eszközök megkövetelése a cloud app hozzáférés ties **Azure AD feltételes hozzáférésével** és **az Azure AD Eszközkezelés** együtt. Ha még nem ismeri a következő területeken, olvassa el a következő témakörök először:

- **[Feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md)**  – Ez a cikk a feltételes hozzáférés és a kapcsolódó terminológia áttekintést biztosít.

- **[Bevezetés az Azure Active Directoryban eszközfelügyeletre](device-management-introduction.md)**  – Ez a cikk áttekintést nyújt a különböző lehetőségek eszközök szervezeti vezérlése alatt van. 


## <a name="scenario-description"></a>Forgatókönyv leírása

Biztonság és a termelékenység közötti egyensúly elsajátítása kihívást jelent. A támogatott eszközök a felhőben lévő erőforrások eléréséhez elterjedése segítségével a felhasználók a termelékenység növelése érdekében. A tükrözés oldalon nem érdemes bizonyos erőforrásoknál a környezetében, amelyet egy ismeretlen védelmi szint eszközök érhet el. Az érintett erőforrások érdemes beállítani, hogy felhasználók csak érhetik el azokat a felügyelt eszközök használata. 

A feltételes hozzáférés az Azure AD meg lehet oldani ezt a követelményt, amely hozzáférést biztosít egy házirendnek:

- Kijelölt felhőalapú alkalmazásokhoz

- A kijelölt felhasználók és csoportok

- A felügyelt eszközök megkövetelése


## <a name="managed-devices"></a>Felügyelt eszköz  

Egyszerű feltételeket, a felügyelt eszközök alatt állnak, amelyek eszközök *valamilyen* szervezeti vezérlő. Az Azure ad-ben egy felügyelt eszközt előfeltételeivel, hogy az regisztrálva van az Azure ad-val. Eszköz regisztrálása az eszköz identitásának hoz létre egy eszközobjektumot formája. Ez az objektum az Azure-ban nyomon követésére szolgál egy eszköz vonatkozó állapotadatokat. Az Azure AD a rendszergazdák már használhatja ezt az objektumot a váltógomb (engedélyezése vagy letiltása) az eszköz állapotát.
  
![Eszköz-alapú feltételek](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Ahhoz, hogy az Azure ad-vel regisztrált egy eszközt, három lehetőség közül választhat:

- **[Az Azure AD regisztrált eszközök](device-management-introduction.md#azure-ad-registered-devices)**  - személyes eszköz regisztrálva az Azure ad szolgáltatással

- **[Az Azure AD csatlakoztatott eszközök](device-management-introduction.md#azure-ad-joined-devices)**  – egy szervezeti Windows 10-eszköz, amely nem csatlakozik egy helyszíni AD az Azure ad-vel regisztrált. 

- **[Az Azure AD hibrid csatlakoztatott eszközök](device-management-introduction.md#hybrid-azure-ad-joined-devices)**  – egy helyszíni tartományhoz csatlakoztatott Windows 10-es eszköz az Azure ad-vel regisztrált AD.

Felügyelt eszközzé válik, egy regisztrált eszközre kell lennie vagy a **az Azure AD hibrid csatlakoztatott eszközök** vagy egy **jelölt megfelelőnek eszköz**.  

![Eszköz-alapú feltételek](./media/active-directory-conditional-access-policy-connected-applications/47.png)

 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Szükséges a hibrid Azure AD csatlakoztatott eszközök

A feltételes hozzáférési házirend kiválaszthatja **megkövetelése az Azure AD hibrid csatlakoztatott eszközről** állapotról, hogy a kijelölt felhőalapú alkalmazásokat csak érhető el egy kezelt eszközt használ. 

![Eszköz-alapú feltételek](./media/active-directory-conditional-access-policy-connected-applications/10.png)

Ez a beállítás csak egy helyszíni tartományhoz csatlakoztatott Windows 10-eszközökre érvényes AD. Ezek az eszközök csak regisztrálhatja az Azure AD használatával a Azure AD hibrid csatlakozzon, amely egy [automatikus folyamat](device-management-hybrid-azuread-joined-devices-setup.md) regisztrált Windows 10-eszköz eléréséhez. 

![Eszköz-alapú feltételek](./media/active-directory-conditional-access-policy-connected-applications/45.png)

Az Azure AD hibrid hasznossá csatlakoztatott eszközről egy felügyelt eszközt?  Egy helyszíni tartományhoz csatlakoztatott eszközök AD, feltételezzük, hogy a a felügyelheti kényszerítve legyen-e megoldásokat, mint segítségével **System Center Configuration Manager (SCCM)** vagy **csoportházirend (GP)** kezelhetők. Nincs módszer az Azure AD annak meghatározásához, hogy az alábbi módszereket telepítve van egy eszközt, mert az Azure AD hibrid csatlakoztatott eszközről használata viszonylag gyenge eljárást, amely egy felügyelt eszközt igényelnek. Célszerű mennyire, hogy a módszereket, amelyek érvényesek a helyi tartományhoz csatlakozó eszközök megfelelőek-e egy felügyelt eszközt jelent, ha egy ilyen eszköz is az Azure AD hibrid csatlakoztatott eszközről rendszergazdaként.


## <a name="require-device-to-be-marked-as-compliant"></a>Eszköz megfelelőként való megjelölésének megkövetelése

A beállítással *szükséges az eszköz megfelelőnek kell megjelölni* kérjen egy felügyelt eszközt legerősebb formája, amely.

![Eszköz-alapú feltételek](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Ez a beállítás használatához egy eszközt az Azure AD-regisztráció és által megfelelőnek kell megjelölni:
         
- Intune-ban.
- Egy harmadik fél mobileszköz-felügyelet (MDM) rendszer, amely a Windows 10-eszközöket az Azure AD-integrációs kezeli. Külső MDM rendszerek eltérő Windows 10-es eszköz operációs rendszere típusok nem támogatottak.
 
![Eszköz-alapú feltételek](./media/active-directory-conditional-access-policy-connected-applications/46.png)



Egy eszköz, amely kompatibilis van megjelölve akkor feltételezhető, hogy: 

- A dolgozók számára a vállalati adatok elérésére használt mobileszközök kezelési
- Kezelt mobilalkalmazások a munkaerő használ
- A vállalati adatok védik a munkaerő fér hozzá, és megosztja módjának elősegítése
- Az eszköz és az alkalmazások nem kompatibilis a vállalat biztonsági követelményeinek importálása




## <a name="next-steps"></a>További lépések

Eszközalapú feltételes hozzáférési házirend konfigurálása a környezetben, előtt meg kell vessen egy pillantást a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md).

