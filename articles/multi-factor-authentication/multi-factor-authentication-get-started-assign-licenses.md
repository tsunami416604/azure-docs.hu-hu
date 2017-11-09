---
title: "Azure MFA-licencek kiosztása | Microsoft Docs"
description: "Megtudhatja, hogyan rendelhet felhasználói licenceket a Microsoft Azure Multi-Factor Authentication hitelesítéshez."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 514ef423-8ee6-4987-8a4e-80d5dc394cf9
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/13/2017
ms.author: joflore
ms.reviewer: yossib
ms.custom: it-pro
ROBOTS: NOINDEX
ms.openlocfilehash: 86e8e5ce183d11a76fb679da0e6c707383ce86d0
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>Azure MFA, Azure AD Prémium vagy Nagyvállalati mobilitási licenc hozzárendelése felhasználókhoz
Ha Azure MFA, Azure AD Prémium vagy Nagyvállalati mobilitási csomag licenceket vásárolt, nem kell létrehoznia Multi-Factor Auth szolgáltatót. Miután hozzárendelte a licenceket a felhasználókhoz, elkezdheti az engedélyezésüket az MFA-hoz.

## <a name="to-assign-a-license"></a>Licenc hozzárendelése
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. Az Active Directory lapon kattintson duplán arra a könyvtárra, amelyben az engedélyezni kívánt felhasználók találhatók.
4. A könyvtárlap tetején válassza a **Licencek** elemet.
   ![Licencek hozzárendelése](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. A Licencek lapon válassza az **Azure Multi-Factor Authentication**, a **Prémium szintű Active Directory** vagy a **Nagyvállalati mobilitási csomag** lehetőséget.  Ha csak eggyel rendelkezik, akkor azt a rendszer automatikusan bejelöli.
6. Kattintson a lap alján található **Hozzárendelés** gombra.
   ![Licencek hozzárendelése](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
7. A megjelenő mezőben kattintson azon felhasználók vagy csoportok mellé, amelyekhez licenceket szeretne rendelni.  Egy zöld pipának kell megjelennie.
8. Kattintson a pipa ikonra a módosítások mentéséhez.
   ![Licencek hozzárendelése](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
9. Ekkor egy üzenet jelenik meg amely tartalmazza, hány licenc lett hozzárendelve, és hány hozzárendelése sikertelen.  Kattintson az **OK** gombra.
   ![Licencek hozzárendelése](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)

## <a name="next-steps"></a>Következő lépések

- További információkért lásd [a Microsoft Azure Active Directory-licencelést](../active-directory/active-directory-licensing-what-is.md) ismertető témakört.
