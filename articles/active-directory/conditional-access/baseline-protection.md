---
title: Mi egy alapvető védelmet az Azure Active Directory feltételes hozzáférés? – előzetes verzió |} A Microsoft Docs
description: Ismerje meg, hogy alapvető védelmet biztosítja, hogy legalább engedélyezve van az Azure Active Directory-környezetet a biztonsági alapkonfiguráció szintjét.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, feltételes hozzáférés az Azure AD-vel, biztonságos hozzáférés a vállalati erőforrásokhoz, feltételes hozzáférési szabályzatok
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
ms.date: 08/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 57fef112186834ead76f6223e32cb358e4d6d053
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024073"
---
# <a name="what-is-baseline-protection-preview"></a>Mi az alapvető védelmet (előzetes verzió)?  

Az elmúlt évben identitás-támadások 300 %-kal nőtt. A környezet védelmét biztosíthatja a folyamatosan növekvő konkurenciával kell támadások ellen, hogy az Azure Active Directory (Azure AD) alapvető védelmet nevű új szolgáltatást vezet be. Alapvető védelmet egy olyan előre definiált [feltételes hozzáférési szabályzatok](../active-directory-conditional-access-azure-portal.md). Ezek a házirendek az a célja, hogy gondoskodjon arról, hogy legalább engedélyezve van az Azure AD minden kiadása a biztonsági alapkonfiguráció szintjét. 

Ez a cikk áttekintést az Azure Active Directory alapvető védelmet biztosít.


 
## <a name="require-mfa-for-admins"></a>Többtényezős hitelesítés a rendszergazdák számára

Kiemelt jogosultságú fiókok hozzáféréssel rendelkező felhasználó ugyanolyan korlátlan hozzáféréssel rendelkezik a környezetben. A teljesítmény, a fiókokra, mert kezelje őket gondosan. Egy közös módszerrel történő kiemelt jogosultságú fiókok védelméhez, hogy egy fiókhitelesítés erősebb formája kérése, amikor használhassák jelentkezhetnek be. Az Azure Active Directoryban azzal, hogy a többtényezős hitelesítés (MFA) egy erősebb fiókhitelesítés kaphat.  

**Többtényezős hitelesítés a rendszergazdák számára** egy alapvető házirendet, amely többtényezős Hitelesítést követel meg a következő címtárszerepkörökre van: 

- Globális rendszergazda  

- SharePoint-rendszergazda  

- Exchange-rendszergazda  

- Feltételes hozzáférésű rendszergazda  

- Biztonsági rendszergazda  


![Azure Active Directory](./media/baseline-protection/01.png)

Ez a alapvető házirend biztosít arra, hogy felhasználók és csoportok kizárása. Előfordulhat, hogy szeretne zárni egy *[válságkezelési hozzáférés rendszergazdai fiók](../users-groups-roles/directory-emergency-access.md)* , győződjön meg arról, hogy nem záródik ki a bérlő.


## <a name="enable-a-baseline-policy"></a>Egy alapkonfiguráció házirend engedélyezése 

Alapkonfiguráció szabályzatok vannak előzetes verzióban érhető el, amíg nincs aktiválva alapértelmezés szerint vannak. A szabályzatot manuálisan engedélyezze, ha szeretné aktiválni kell. Amint ez a szolgáltatás elérte a általánosan elérhető, házirend-beállítások aktiválva alapértelmezés szerint. A tervezett viselkedést változás, miért van továbbá, hogy aktiválják vagy inaktiválják egy harmadik lehetőség egy szabályzat állapotának beállításához az OK: **szabályzat automatikus engedélyezés a jövőben**. A beállítás bejelölésével engedélyezi a Microsoft dönt egy házirendet aktiválja.      


**Egy alapkonfiguráció-házirend engedélyezése:**  

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) globális rendszergazdai, biztonsági rendszergazdai vagy feltételes hozzáférésű rendszergazda.

2. Az a **az Azure portal**, a bal oldali navigációs sávon kattintson **Azure Active Directory**.

    ![Azure Active Directory](./media/baseline-protection/02.png)

3. Az a **Azure Active Directory** lap a **biztonsági** területén kattintson **feltételes hozzáférési**.

    ![Feltételes hozzáférés](./media/baseline-protection/05.png)

4. A szabályzatok listájában kattintson egy szabályzatot, amely kezdődik **alapvető házirendet:**. 

5. A szabályzat engedélyezéséhez kattintson **a házirend azonnal használható**.

6. Kattintson a **Save** (Mentés) gombra. 
 
  
 

## <a name="what-you-should-know"></a>Tudnivalók 

Egyéni feltételes hozzáférési szabályzatok kezelése egy prémium szintű Azure AD-licenc szükséges, miközben alapkonfiguráció házirendek az Azure AD összes kiadásokban érhető el.     

A referenciakonfiguráció-házirendben telepítésben a címtárbeli szerepkörök a jogosultsági Azure AD-szerepkörök. 

Ha a parancsfájlok privilegizált használt fiókok, kell cserélni, azokat a [Felügyeltszolgáltatás-identitás (MSI)](../managed-identities-azure-resources/overview.md) vagy [tanúsítványokkal rendelkező egyszerű szolgáltatás](../../azure-resource-manager/resource-group-authenticate-service-principal.md). Ideiglenes Áthidaló megoldásként a referenciakonfiguráció házirendből zárhat ki egyedi felhasználói fiókokhoz. 

Alapkonfiguráció házirendek vonatkoznak az örökölt hitelesítési folyamatok például a POP, IMAP, régebbi Office asztali ügyfelet. 




## <a name="next-steps"></a>További lépések

További információkért lásd:

- [Öt lépést a személyazonosság-infrastruktúra védelme](https://docs.microsoft.com/azure/security/azure-ad-secure-steps)

- [Mi az az Azure Active Directory feltételes hozzáférés?](overview.md) 

