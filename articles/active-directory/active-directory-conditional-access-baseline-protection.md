---
title: Egy alapkonfiguráció védelmi Mi az Azure Active Directory feltételes hozzáférés? – előzetes verzió |} Microsoft Docs
description: Ismerje meg, hogyan védelem alapkonfigurációt biztosítja, hogy legalább az eredeti engedélyezve az Azure Active Directory környezetben biztonsági szintet.
services: active-directory
keywords: alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/21/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 86b57a82573760ac73975e851b2bb4caf769845b
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308560"
---
# <a name="what-is-baseline-protection---preview"></a>Mi az az eredeti protection? – előzetes  

Az utolsó évben identitás támadások 300 %-kal nőtt. A környezet védelméhez a egyre növekvő támadások, az Azure Active Directory (Azure AD) nevű alapterv védelmi új szolgáltatást vezet be. Védelem alapkonfigurációt egy olyan előre definiált [feltételes hozzáférési házirendek](active-directory-conditional-access-azure-portal.md). Ezek a házirendek célja, hogy legyen legalább az eredeti engedélyezett összes kiadásában az Azure AD biztonsági szintet. 

Ez a cikk alapterv védelme az Azure Active Directoryban áttekintést nyújt.


 
## <a name="require-mfa-for-admins"></a>Többtényezős hitelesítés megkövetelése a rendszergazdák számára

Kiemelt jogosultságú fiókok hozzáféréssel rendelkező felhasználók a környezet korlátozás nélkül hozzáférnek. A teljesítmény, ezek a fiókok rendelkeznek-e, mert kezelje őket gondosan. Kiemelt jogosultságú fiókok védelmét egy általános módszer az, hogy elő Fiókellenőrzés erősebb formája, történő bejelentkezéshez használt. Az Azure Active Directoryban azzal, hogy a többtényezős hitelesítés (MFA) erősebb Fiókellenőrzés kaphat.  

**A rendszergazdák számára a többtényezős hitelesítés kötelező** egy olyan alapterv házirend, többtényezős Hitelesítést követel meg a következő könyvtár szerepkörök: 

- Globális rendszergazda  

- SharePoint-rendszergazda  

- Exchange-rendszergazda  

- Feltételes hozzáférésű rendszergazda  

- Biztonsági rendszergazda  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

Az alapvető házirendet tartalmaz, amelyek felhasználókat és csoportokat kizárására. Egy kizárni kívánt *[sürgős rendszergazdai fiókja](active-directory-admin-manage-emergency-access-accounts.md)* annak érdekében, nem záródik ki a bérlő.


## <a name="enable-a-baseline-policy"></a>Egy alapkonfiguráció házirend engedélyezése 

Alapkonfiguráció házirendek még csak előzetes verziójúak, amíg nincs aktiválva alapértelmezés szerint vannak. Egy házirend manuálisan engedélyezze, ha az aktiválás kell. Amint ez a szolgáltatás elérte a nyilvános elérhetőség utáni, a házirendek aktiválása alapértelmezés szerint is. A tervezett változtatásokra miért van továbbá, hogy aktiválják és inaktiválják a harmadik lehetőség egy házirend állapotának beállításához ok: **automatikus engedélyezés a jövőben házirend**. Ezzel a beállítással engedélyezi a Microsoft döntse el, ha egy házirendet aktiválja.      


**Ahhoz, hogy egy alapvető házirendet:**  

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) globális rendszergazda, a rendszergazda vagy a feltételes hozzáférés rendszergazdaként.

2. Az a **Azure-portálon**, kattintson a bal oldali navigációs sávja **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. Az a **Azure Active Directory** lap a **kezelése** kattintson **feltételes hozzáférés**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-baseline-protection/03.png)

4. A házirendek listájában, kattintson a kezdetű **alapvető házirendet:**. 

5. A házirend engedélyezéséhez kattintson **házirend azonnal használható**.

6. Kattintson a **Save** (Mentés) gombra. 
 


  
 

## <a name="what-you-should-know"></a>Tudnivalók 

Egyéni feltételes hozzáférési házirendek kezelése és a prémium szintű Azure AD-licencre van szükség, míg az Azure AD összes kiadásában alapterv házirendek érhetők el.     

A könyvtár szerepköröket, amelyek szerepelnek az alapvető házirendet a legtöbb jogosultsági szintű Azure AD szerepkör is. 

Ha a parancsfájlok a privilegizált használt fiókok, kell-e cserélni őket a [felügyelt szolgáltatás identitásának (MSI)](./managed-service-identity/overview.md) vagy [, tanúsítványok elsődleges szolgáltatás](../azure-resource-manager/resource-group-authenticate-service-principal.md). Ideiglenes megoldásként kizárhat meghatározott felhasználói fiókok a baseline házirendből. 

Alapkonfiguráció házirendek vonatkoznak régebbi hitelesítési adatfolyamok, például a POP-ra, IMAP, régebbi asztali Office-ügyfélen. 




## <a name="next-steps"></a>További lépések

Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).

Ha készen áll a környezet feltételes hozzáférési házirend-beállításokkal, tekintse meg a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md). 
