---
title: Mi az az egy alapkonfiguráció protection az Azure Active Directory feltételes hozzáférés |} Microsoft Docs
description: Ismerje meg, hogyan védelem alapkonfigurációt biztosítja, hogy legalább az eredeti szintű biztonságot engedélyezve a környezetben.
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
ms.date: 06/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 25ae4db2cd4f2a2cea74c428a272c6868acaa5c5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35249270"
---
# <a name="what-is-baseline-protection"></a>Mi az az eredeti protection?  

Az utolsó évben identitás támadások 300 %-kal nőtt. A környezet védelméhez a egyre növekvő támadások, az Azure Active Directory (Azure AD) nevű alapterv védelmi új szolgáltatást vezet be. Védelem alapkonfigurációt olyan előre definiált feltételes hozzáférési házirendek. Ezek a házirendek célja, hogy legyen legalább az eredeti szintű biztonságot engedélyezve a környezetben. 

Az előzetes szüksége alapterv szabályzatok engedélyezéséhez, ha szeretné aktiválni azokat. POST általánosan rendelkezésre álló, ezeket a házirendeket a rendszer alapértelmezés szerint engedélyezve van. 

Az első alapterv védelmi házirend többtényezős Hitelesítést követel meg kiemelt jogosultságokkal rendelkező fiókok. Védje meg kiemelt jogosultságú fiókok támadó rengeteg kárt teheti meg, hogy kritikus védelméhez először ezeket a fiókokat. A következő kiemelt szerepköröket szerepelnek a szabályzat hatóköre: 

- Globális rendszergazda  

- SharePoint-rendszergazda  

- Exchange-rendszergazda  

- Feltételes hozzáférésű rendszergazda  

- Biztonsági rendszergazda  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

## <a name="how-to-get-started"></a>Első lépések 

Ahhoz, hogy az alapvető házirendet:  

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) globális rendszergazda, a rendszergazda vagy a feltételes hozzáférés rendszergazdaként.

2. Az a **Azure-portálon**, kattintson a bal oldali navigációs sávja **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. Az a **Azure Active Directory** lap a **kezelése** kattintson **feltételes hozzáférés**.

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-baseline-protection/03.png)

4. A házirendek, kattintson a **alapvető házirendet: többtényezős hitelesítés megkövetelése rendszergazdák (előzetes verzió)**. 

5. A házirend engedélyezéséhez kattintson **házirend azonnal használható**.

6. Kattintson a **Save** (Mentés) gombra. 
 

Az alapvető házirendet tartalmaz, amelyek felhasználókat és csoportokat kizárására. Egy kizárni kívánt *[sürgős rendszergazdai fiókja](active-directory-admin-manage-emergency-access-accounts.md)* annak érdekében, nem záródik ki a bérlő.
  
 

## <a name="what-you-should-know"></a>Tudnivalók 

A könyvtár szerepköröket, amelyek szerepelnek az alapvető házirendet a legtöbb jogosultsági szintű Azure AD szerepkör is. Visszajelzések alapján esetleg mások is megtalálható a jövőben. 

Ha már rendelkezik rendszergazdai jogosultságokkal rendelkező fiókok a parancsfájlok, használjon [felügyelt szolgáltatás identitásának (MSI)](managed-service-identity/overview.md) vagy [rendszerbiztonsági tagok (tanúsítványokkal) szolgáltatás](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) helyette. Ideiglenes megoldásként kizárhat meghatározott felhasználói fiókok a baseline házirendből. 

A házirend érvényes régebbi hitelesítési adatfolyamok, például a POP-ra, IMAP, régebbi asztali Office-ügyfélen. 

## <a name="next-steps"></a>További lépések

Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).

Ha készen áll a környezet feltételes hozzáférési házirend-beállításokkal, tekintse meg a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md). 
