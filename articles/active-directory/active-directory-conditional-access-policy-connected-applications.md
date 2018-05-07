---
title: Azure Active Directory eszközalapú feltételes hozzáférési szabályzatok konfigurálására |} Microsoft Docs
description: 'Útmutató: Azure Active Directory eszközalapú feltételes hozzáférési házirendek konfigurálása.'
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: ede0c7954fdcb27788e4045c08bd6a0b88ae74bc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Azure Active Directory eszközalapú feltételes hozzáférési házirendek konfigurálása

A [Azure Active Directory (Azure AD) feltételes hozzáférés](active-directory-conditional-access-azure-portal.md), szabályozhatja, hogy hogyan engedéllyel rendelkező felhasználók férhetnek hozzá az erőforrásokat. Például korlátozhatja a kezelt eszközökre bizonyos erőforrások elérésére. Egy feltételes hozzáférési szabályzatot, amely egy felügyelt eszközt eszközalapú feltételes hozzáférési szabályzat is nevezik.

Ez a témakör azt ismerteti, hogyan konfigurálhatja az eszközalapú feltételes hozzáférési házirendeket az Azure AD-kompatibilis alkalmazásokat. 


## <a name="before-you-begin"></a>Előkészületek

Eszközalapú feltételes hozzáférési ties **Azure AD feltételes hozzáférésével** és **együtt az Azure AD Eszközkezelés**. Ha még nem ismeri a következő területeken, olvassa el a következő témakörök először:

- **[Feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md)**  – Ez a témakör a feltételes hozzáférés és a kapcsolódó terminológia elméleti áttekintését.

- **[Bevezetés az Azure Active Directoryban eszközfelügyeletre](device-management-introduction.md)**  – Ez a témakör áttekintést nyújt az eszközök csatlakoztatása az Azure ad-vel, hogy különböző lehetőségek közül. 



## <a name="managed-devices"></a>Felügyelt eszköz  

Mobileszköz-first, a felhő-első világában Azure Active Directory lehetővé teszi, hogy az egyszeri bejelentkezés eszközök, alkalmazások és szolgáltatások bárhonnan. Az egyes erőforrásoknak a környezetben, és hozzáférést biztosít az arra jogosult felhasználók előfordulhat elég helyes. Az arra jogosult felhasználók mellett is szüksége lehet, hogy hozzáférések csak hajtható végre egy felügyelt eszközt használ.

Egy felügyelt eszközről, amely megfelel a biztonsági és megfelelőségi előírások. Egyszerű feltételeket, a felügyelt eszközök alatt állnak, amelyek eszközök *valamilyen* szervezeti vezérlő. Az Azure ad-ben egy felügyelt eszközt előfeltételeivel, hogy az regisztrálva van az Azure ad-val. Eszköz regisztrálása az eszköz identitásának hoz létre egy eszközobjektumot formája. Ez az objektum az Azure-ban nyomon követésére szolgál egy eszköz vonatkozó állapotadatokat. Az Azure AD a rendszergazdák már használhatja ezt az objektumot a váltógomb (engedélyezése vagy letiltása) az eszköz állapotát.
  
![Eszköz-alapú feltételek](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Ahhoz, hogy az Azure ad-vel regisztrált egy eszközt, három lehetőség közül választhat:

- **[Az Azure AD regisztrált eszközök](device-management-introduction.md#azure-ad-registered-devices)**  - személyes eszköz regisztrálva az Azure ad szolgáltatással

- **[Az Azure AD csatlakoztatott eszközök](device-management-introduction.md#azure-ad-joined-devices)**  – egy szervezeti Windows 10-eszközön, amely nem csatlakozik egy helyszíni Azure AD regisztrálva az Azure ad-vel regisztrált eléréséhez. 

- **[Az Azure AD hibrid csatlakoztatott eszközök](device-management-introduction.md#hybrid-azure-ad-joined-devices)**  – egy helyszíni tartományhoz csatlakoztatott Windows 10-es eszköz az Azure ad-vel regisztrált AD.

Felügyelt eszközzé válik, a regisztrált egy eszközt az Azure AD csatlakoznia vagy megfelelőnek jelölt eszköz hibrid lehet.  

![Eszköz-alapú feltételek](./media/active-directory-conditional-access-policy-connected-applications/47.png)


 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Szükséges a hibrid Azure AD csatlakoztatott eszközök

A feltételes hozzáférési házirend kiválaszthatja **megkövetelése az Azure AD hibrid csatlakoztatott eszközről** állapotról, hogy a kijelölt felhőalapú alkalmazásokat csak érhető el egy kezelt eszközt használ. 

![Eszköz-alapú feltételek](./media/active-directory-conditional-access-policy-connected-applications/10.png)

Ez a beállítás csak egy helyszíni Azure AD tartományhoz csatlakoztatott Windows 10-eszközökre vonatkozik. Ezek az eszközök csak regisztrálhatja az Azure AD használatával a Azure AD hibrid csatlakozzon, amely egy [automatikus folyamat](device-management-hybrid-azuread-joined-devices-setup.md) regisztrált Windows 10-eszköz eléréséhez. 

![Eszköz-alapú feltételek](./media/active-directory-conditional-access-policy-connected-applications/45.png)

Az Azure AD hibrid hasznossá csatlakoztatott eszközről egy felügyelt eszközt?  Egy helyszíni tartományhoz csatlakoztatott eszközök AD, feltételezzük, hogy a a felügyelheti kényszerítve legyen-e megoldásokat, mint segítségével **System Center Configuration Manager (SCCM)** vagy **csoportházirend (GP)** kezelhetők. Nincs módszer az Azure AD annak meghatározásához, hogy az alábbi módszereket telepítve van egy eszközt, mert az Azure AD hibrid csatlakoztatott eszközről használata viszonylag gyenge eljárást, amely egy felügyelt eszközt igényelnek. Célszerű mennyire, hogy a módszereket, amelyek érvényesek a helyi tartományhoz csatlakozó eszközök megfelelőek-e egy felügyelt eszközt jelent, ha egy ilyen eszköz is az Azure AD hibrid csatlakoztatott eszközről rendszergazdaként.


## <a name="require-device-to-be-marked-as-compliant"></a>Eszköz megfelelőként való megjelölésének megkövetelése

A beállítással *szükséges az eszköz megfelelőnek kell megjelölni* kérjen egy felügyelt eszközt legerősebb formája, amely.

![Eszköz-alapú feltételek](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Ez a beállítás használatához egy eszközt az Azure AD-regisztráció és által megfelelőnek kell megjelölni:
         
- Intune 
- Egy harmadik fél mobileszköz felügyelete rendszer, amely kezeli a Windows 10-eszközöket az Azure AD-integrációs 
 
![Eszköz-alapú feltételek](./media/active-directory-conditional-access-policy-connected-applications/46.png)



Egy eszköz, amely kompatibilis van megjelölve akkor feltételezhető, hogy: 

- A dolgozók számára a vállalati adatok elérésére használt mobileszközök kezelési
- Kezelt mobilalkalmazások a munkaerő használ
- A vállalati adatok védik a munkaerő fér hozzá, és megosztja módjának elősegítése
- Az eszköz és az alkalmazások nem kompatibilis a vállalat biztonsági követelményeinek importálása




## <a name="next-steps"></a>További lépések

Eszközalapú feltételes hozzáférési házirend konfigurálása a környezetben, előtt meg kell vessen egy pillantást a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md).

