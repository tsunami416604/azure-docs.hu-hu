---
title: Mi egy alapvető védelmet az Azure Active Directory feltételes hozzáférés? – előzetes verzió | Microsoft Docs
description: Ismerje meg, hogy alapvető védelmet biztosítja, hogy legalább engedélyezve van az Azure Active Directory-környezetet a biztonsági alapkonfiguráció szintjét.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, feltételes hozzáférés az Azure AD-vel, biztonságos hozzáférés a vállalati erőforrásokhoz, feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: daveba
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
ms.openlocfilehash: 67f3299bcf5b852e05308a8e71004fcd613e83cf
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446270"
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

Ez a alapvető házirend nyújt a felhasználók kizárására. Előfordulhat, hogy szeretne zárni egy *[válságkezelési hozzáférés rendszergazdai fiók](../users-groups-roles/directory-emergency-access.md)* , győződjön meg arról, hogy nem záródik ki a bérlő.


## <a name="enable-a-baseline-policy"></a>Egy alapkonfiguráció házirend engedélyezése 

Alapkonfiguráció szabályzatok vannak előzetes verzióban érhető el, amíg nincs aktiválva alapértelmezés szerint vannak. A szabályzatot manuálisan engedélyezze, ha szeretné aktiválni kell. Ha explicit módon engedélyezi a referenciakonfiguráció-házirendeket az előnézeti szakaszban, azok aktív marad, ha ez a funkció eléri elérhetőség. A tervezett viselkedést módosítás okát miért, továbbá hogy aktiválják vagy inaktiválják, van egy harmadik lehetőséget egy szabályzat állapotának beállításához: **Szabályzat automatikus engedélyezés a jövőben**. Ez a beállítás kiválasztásával hagyja meg a házirend le van tiltva az előzetes verzióban, de van a Microsoft automatikusan engedélyezheti őket, amikor ez a funkció eléri elérhetőség. Ha nem kifejezetten engedélyezi a referenciakonfiguráció házirendek most, és ne jelölje be a **szabályzat automatikus engedélyezés a jövőben** lehetőségnél a házirendek letiltott állapotban maradnak amikor ez a funkció eléri elérhetőség.


**Egy alapkonfiguráció-házirend engedélyezése:**  

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) globális rendszergazdai, biztonsági rendszergazdai vagy feltételes hozzáférésű rendszergazda.

2. Az a **az Azure portal**, a bal oldali navigációs sávon kattintson **Azure Active Directory**.

    ![Azure Active Directory](./media/baseline-protection/02.png)

3. Az a **Azure Active Directory** lap a **biztonsági** területén kattintson **feltételes hozzáférési**.

    ![Feltételes hozzáférés](./media/baseline-protection/05.png)

4. A szabályzatok listájában kattintson egy szabályzatot, amely kezdődik **alapvető házirendet:**. 

5. A szabályzat engedélyezéséhez kattintson **a házirend azonnal használható**.

6. Kattintson a **Save** (Mentés) gombra. 
 
  
 

## <a name="what-you-should-know"></a>Alapismeretek 

Egyéni feltételes hozzáférési szabályzatok kezelése egy prémium szintű Azure AD-licenc szükséges, miközben alapkonfiguráció házirendek az Azure AD összes kiadásokban érhető el.     

A referenciakonfiguráció-házirendben telepítésben a címtárbeli szerepkörök a jogosultsági Azure AD-szerepkörök. 

Ha a parancsfájlok privilegizált használt fiókok, kell cserélni, azokat a [felügyelt identitások az Azure-erőforrások](../managed-identities-azure-resources/overview.md) vagy [tanúsítványokkal rendelkező egyszerű szolgáltatás](../develop/howto-authenticate-service-principal-powershell.md). Ideiglenes Áthidaló megoldásként a referenciakonfiguráció házirendből zárhat ki egyedi felhasználói fiókokhoz. 

Alapkonfiguráció házirendek vonatkoznak az örökölt hitelesítési folyamatok például a POP, IMAP, régebbi Office asztali ügyfelet. 




## <a name="next-steps"></a>További lépések

További információkért lásd:

- [Öt lépést a személyazonosság-infrastruktúra védelme](https://docs.microsoft.com/azure/security/azure-ad-secure-steps)

- [Mi az az Azure Active Directory feltételes hozzáférés?](overview.md) 

