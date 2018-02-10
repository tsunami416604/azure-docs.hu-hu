---
title: "Azure Active Directory eszközalapú feltételes hozzáférési szabályzatok konfigurálására |} Microsoft Docs"
description: "Útmutató: Azure Active Directory eszközalapú feltételes hozzáférési házirendek konfigurálása."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 2354a8bf81189f70bb8d0d63c3df3236403c11fc
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Azure Active Directory eszközalapú feltételes hozzáférési házirendek konfigurálása

A [Azure Active Directory (Azure AD) feltételes hozzáférés](active-directory-conditional-access-azure-portal.md), úgy finomhangolhatja, hogyan engedéllyel rendelkező felhasználók férhetnek hozzá az erőforrásokat. Például korlátozhatja a hozzáférést bizonyos erőforrások megbízható eszközökre. Egy feltételes hozzáférési szabályzatot, amely egy megbízható eszközt eszközalapú feltételes hozzáférési szabályzat is nevezik.

Ez a témakör az Azure AD-kompatibilis alkalmazásokat az eszközalapú feltételes hozzáférési házirendek konfigurálásával kapcsolatos információkat nyújt. 


## <a name="before-you-begin"></a>Előkészületek

Eszközalapú feltételes hozzáférési ties **Azure AD feltételes hozzáférésével** és **együtt az Azure AD Eszközkezelés**. Ha még nem ismeri a következő területeken, olvassa el a következő témakörök először:

- **[Feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md)**  – Ez a témakör a feltételes hozzáférés és a kapcsolódó terminológia elméleti áttekintését.

- **[Bevezetés az Azure Active Directoryban eszközfelügyeletre](device-management-introduction.md)**  – Ez a témakör áttekintést nyújt az eszközök csatlakoztatása az Azure ad-vel, hogy különböző lehetőségek közül. 


## <a name="trusted-devices"></a>Megbízható eszközök

Mobileszköz-first, a felhő-első világában Azure Active Directory lehetővé teszi, hogy az egyszeri bejelentkezés eszközök, alkalmazások és szolgáltatások bárhonnan. Az egyes erőforrásoknak a környezetben, és hozzáférést biztosít az arra jogosult felhasználók előfordulhat elég helyes. Az arra jogosult felhasználók mellett is szüksége lehet a megbízható eszközök erőforrások eléréséhez használt. A környezetben, megadhatja, mi megbízható eszköz alapján a következő összetevőket:

- A [eszközplatformok](active-directory-conditional-access-conditions.md#device-platforms) az eszközön
- Egy eszköz-e megfelelő
- Egy eszköz-e a tartományhoz 

A [eszközplatformok](active-directory-conditional-access-conditions.md#device-platforms) jellemzőek, az eszközön futó operációs rendszer. Az eszközalapú feltételes hozzáférési házirendben korlátozhatja az egyes erőforrásokhoz adott eszközplatformhoz való hozzáférést.



Eszközalapú feltételes hozzáférési szabályzatot, a megfelelőnek kell megjelölni megbízható eszközökkel lehet szükség.

![Felhőalkalmazások](./media/active-directory-conditional-access-policy-connected-applications/24.png)

A címtár által megfelelőnek eszközök jelölhető ki:

- Intune 
- Egy harmadik fél mobileszköz felügyelete rendszer, amely kezeli a Windows 10-eszközöket az Azure AD-integrációs 
 
  

Csak az Azure AD szolgáltatáshoz kapcsolódó eszközök megfelelőnek jelölhető. Egy eszköz csatlakozni az Azure Active Directory, az alábbi lehetőségei vannak: 

- Regisztrálva az Azure AD
- Az Azure AD-tartományhoz
- Az Azure AD hibrid csatlakoztatva

    ![Felhőalkalmazások](./media/active-directory-conditional-access-policy-connected-applications/26.png)

Ha egy a helyszíni Active Directory (AD) kezdjen, érdemes lehet eszközökön nem kapcsolódik az Azure AD, de a megbízható AD csatlakozik.

![Felhőalkalmazások](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>További lépések

Eszközalapú feltételes hozzáférési házirend konfigurálása a környezetben, előtt meg kell vessen egy pillantást a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md).

