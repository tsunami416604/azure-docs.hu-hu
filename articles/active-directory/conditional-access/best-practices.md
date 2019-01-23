---
title: Ajánlott eljárások az Azure Active Directory feltételes hozzáférés |} A Microsoft Docs
description: Tudnivalók ismertetése, és mi ennek során kerülje a feltételes hozzáférési szabályzatok konfigurálásakor.
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
ms.date: 08/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 62bb9b6b4b0edd9e45b317c3c4e18872bae2eec4
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452836"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Az Azure Active Directory-beli feltételes hozzáférésre vonatkozó ajánlott eljárások

A [Azure Active Directory (Azure AD) feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md), szabályozhatja, hogy jogosult felhasználók hozzáférésének a felhőalapú alkalmazások. Ez a cikk azt ismerteti kapcsolatban:

- Tudnivalók 
- Mi az kerülje ennek során a feltételes hozzáférési szabályzatok konfigurálásakor. 

Ez a cikk feltételezi, hogy Ön ismeri a fogalmakat és a terminológia leírt [Mi az az Azure Active Directory feltételes hozzáférés?](../active-directory-conditional-access-azure-portal.md)



## <a name="whats-required-to-make-a-policy-work"></a>Mi szükséges munka házirendet egy?

Amikor létrehoz egy új szabályzatot, nincsenek nincsenek felhasználók, csoportok, alkalmazások vagy kiválasztott hozzáférés-vezérlést.

![Felhőalkalmazások](./media/best-practices/02.png)


Ahhoz, hogy a szabályzat működik, konfigurálnia kell:


|Mi           | Hogyan                                  | miért|
|:--            | :--                                  | :-- |
|**Felhőalkalmazások** |Válassza ki egy vagy több alkalmazást kell.  | A feltételes hozzáférési szabályzat célja, hogy szabályozhatja, hogy jogosult felhasználók érhetik el az alkalmazásokat a felhőben.|
| **Felhasználók és csoportok** | Válasszon ki legalább egy felhasználót vagy csoportot, amely a kiválasztott felhőalapú alkalmazások elérésére jogosult kell. | Feltételes hozzáférési szabályzatot, amely nem rendelkezik felhasználók és csoportok hozzárendelve, soha nem aktiválódott. |
| **Hozzáférés-vezérlés** | Meg kell választania legalább egy hozzáférés-vezérlés. | Ha a feltételek teljesülnek, a házirend processzor tudnia kell, mi a teendő.|




## <a name="what-you-should-know"></a>Alapismeretek

### <a name="how-are-assignments-evaluated"></a>Hogyan értékeli a hozzárendeléseket?

Az összes hozzárendelés vannak logikailag **műveletet**. Ha egynél több hozzárendelési konfigurálva van, az összes hozzárendelés aktiválhat egy házirendet kell teljesülnie.  

Ha szeretne egy helyfeltétel, amelyekre vonatkozik a vállalati hálózaton kívül létesített összes kapcsolat konfigurálása:

- Például **az összes hely**
- Kizárandó **minden megbízható IP-címek**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Mi a teendő, ha az Azure AD felügyeleti portálon kizárása?

Ha egy feltételes hozzáférési házirendben egy helytelen beállítás miatt az Azure AD portálon kizárása:

- Győződjön meg arról, hogy vannak-e más rendszergazdák a szervezet, amely még nincs letiltva. Az Azure portal-hozzáféréssel rendelkező rendszergazda letilthatja a szabályzatot, amely negatív hatással van a bejelentkezés. 

- A rendszergazdák a szervezet egyik sem frissítheti a házirendet, ha támogatási kérelmet szeretne. A Microsoft támogatási ellenőrizheti és frissítheti a feltételes hozzáférési szabályzatokat, amely megakadályozza, hogy hozzáférést.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Mi történik, ha a klasszikus Azure portál és az Azure Portalon konfigurált szabályzatok?  

Mindkét házirendeket tartat be az Azure Active Directory és a felhasználó hozzáférést kap, csak akkor, ha a szükséges összes követelmény teljesülését.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Mi történik, ha a szabályzat az Intune Silverlight portal és az Azure Portalon?

Mindkét házirendeket tartat be az Azure Active Directory és a felhasználó hozzáférést kap, csak akkor, ha a szükséges összes követelmény teljesülését.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Mi történik, ha van konfigurálva ugyanaz a felhasználó számára több házirend?  

Minden bejelentkezéshez az Azure Active Directory kiértékeli az összes szabályzat, és biztosítja, hogy a szükséges összes követelmény teljesül-e előtt a felhasználónak hozzáférése engedélyezett. Hozzáférés letiltása trumps minden egyéb olyan konfigurációs beállításoknak. 


### <a name="does-conditional-access-work-with-exchange-activesync"></a>Feltételes hozzáférés az Exchange ActiveSync szolgáltatással működik?

Igen, használhatja az Exchange ActiveSync feltételes hozzáférési szabályzat.






## <a name="what-you-should-avoid-doing"></a>Milyen kerülje ennek során

A feltételes hozzáférés keretrendszer egy nagyszerű konfigurációs rugalmasságot biztosít. Azonban rendkívül rugalmas, azt is jelenti, hogy alaposan tekintse át minden konfigurációs szabályzat nem kívánt eredmények elkerülése érdekében közzététele előtt. Ebben a környezetben, például érintő teljes hozzárendelések külön figyelmet kell fordítani **minden felhasználók / csoportok / felhőalkalmazások**.

A környezetben kerülje a következő beállításokat:


**Az összes felhasználó számára az összes felhőalapú alkalmazások:**

- **Hozzáférés letiltása** – Ez a konfiguráció blokkolja a teljes szervezet, amely egyértelműen nem célszerű.

- **Megfelelő eszköz megkövetelése** – a felhasználók számára, amely nem regisztrálták az eszközeiket, de ez a szabályzat letiltja a hozzáférést az Intune-portálon az összes hozzáférést. Ha Ön rendszergazda a regisztrált eszköz nélküli, akkor ez a szabályzat letiltja a ki újra az Azure Portalon módosíthatja a szabályzatot.

- **A tartományhoz való csatlakozás megkövetelése** – Ez a házirend letiltása hozzáférés potenciálisan is letiltja a hozzáférést a szervezet összes felhasználója számára, ha még nem rendelkezik a tartományhoz csatlakoztatott eszköz.


**Minden felhasználó, a minden felhőalapú alkalmazásra, az összes eszközplatformot:**

- **Hozzáférés letiltása** – Ez a konfiguráció blokkolja a teljes szervezet, amely egyértelműen nem célszerű.


## <a name="how-should-you-deploy-a-new-policy"></a>Hogyan kell üzembe helyezni egy új szabályzatot?

Első lépésként ki kell értékelni a csoportházirend használatával a [mi történik, ha eszköze](what-if-tool.md).

Ha helyezheti üzembe egy új házirendet a környezet készen áll, akkor tegye ezt fázisban történik:

1. A házirend alkalmazása a felhasználók egy kis készletét, és ellenőrizze a várt módon. 

2.  Amikor kibővít egy szabályzatot, amely további felhasználókat tartalmazzák, továbbra is minden rendszergazda zárni a szabályzatból. Ez biztosítja, hogy a rendszergazdák továbbra is rendelkezik hozzáféréssel és frissítheti egy szabályzatot, ha szükség egy az megváltoztatására.

3. A szabályzat minden felhasználóra vonatkozni csak akkor, ha ez valóban szükség. 

Ajánlott eljárásként hozzon létre egy felhasználói fiókkal, amely:

- Dedikált házirend felügyelet 
- Minden szabályzat zárva


## <a name="policy-migration"></a>Szabályzat áttelepítése

Vegye figyelembe, hogy a házirendeket, nem hozott létre az Azure Portalon mert áttelepítése:

- Most meg lehet oldani a forgatókönyvek, mielőtt nem tudta kezelni.

- A szabályzatok által összevonni őket felügyelni kell számát csökkentheti.   

- Egyetlen központi helyen a feltételes hozzáférési szabályzatokat is kezelheti.

- A klasszikus Azure portálon visszavontuk.   


További információkért lásd: [az Azure Portalon klasszikus szabályzatok Migrálása](policy-migration.md).


## <a name="next-steps"></a>További lépések

Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md).
