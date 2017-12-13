---
title: "Ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban |} Microsoft Docs"
description: "További tudnivalók a dolgot tudnia kell, és mi ezzel kerülendő a feltételes hozzáférési szabályzatok konfigurálásakor."
services: active-directory
keywords: "alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 8c6707505a6331b53e06b1de60575dd3637ea477
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Gyakorlati tanácsok a feltételes hozzáférés az Azure Active Directoryban

Ez a témakör a dolgot tudnia kell, és mi ezzel kerülendő a feltételes hozzáférési szabályzatok konfigurálásakor kapcsolatos információkat nyújt. Ebben a témakörben olvasásakor, előtt meg kell Ismerkedjen meg a fogalmakat és terminológiával leírt [feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md)

## <a name="what-you-should-know"></a>Tudnivalók

### <a name="whats-required-to-make-a-policy-work"></a>Mi szükséges munka házirendet?

Ha egy új házirendet hoz létre, nincsenek felhasználók, csoportok, alkalmazások vagy kiválasztott hozzáférés-vezérlést.

![Felhőalkalmazások](./media/active-directory-conditional-access-best-practices/02.png)


Ahhoz, hogy a házirend működik, konfigurálnia kell a következő:


|mi           | Útmutató                                  | miért|
|:--            | :--                                  | :-- |
|**Felhőalkalmazások** |Válasszon egy vagy több alkalmazás van szüksége.  | A feltételes hozzáférési házirend célja az, hogy lehetővé teszik annak finomhangolásához, hogy hogyan engedéllyel rendelkező felhasználók férhetnek hozzá az alkalmazásokat.|
| **Felhasználók és csoportok** | Válasszon legalább egy felhasználót vagy csoportot, amely a kijelölt felhőalapú alkalmazások elérésére jogosult kell. | A feltételes hozzáférési szabályzat, amelynek nincs hozzárendelt felhasználók és csoportok, soha nem aktiválódott. |
| **Hozzáférés-vezérlést** | Válasszon legalább egy hozzáférés-vezérlés kell. | A házirend processzor tudnia kell, mi a teendő, ha a feltételek teljesülnek.|


Ezen alapszintű követelményeken sok esetben is konfigurálnia kell egy feltételt. Házirend akkor is megvalósítható, ha egy konfigurált feltételt, amíg a feltételek közül mind a vezetői tényező a pontosabb beállításra a alkalmazásokhoz való hozzáférés.


![Felhőalkalmazások](./media/active-directory-conditional-access-best-practices/04.png)



### <a name="how-are-assignments-evaluated"></a>Hogyan hozzárendelések értékeli ki a rendszer?

Minden hozzárendeléseket a rendszer logikailag **műveletet**. Ha egynél több hozzárendelés konfigurálva van, a házirend elindítása vonatkozó összes hozzárendelést kell biztosítani.  

Ha a vállalati hálózaton kívül létesített összes kapcsolat alkalmazó hely feltétel beállítása kell, ez által elvégezhető:

- Beleértve **az összes hely**
- Kivéve **a megbízható IP-címek**

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Mi történik, ha a klasszikus Azure portál és a konfigurált Azure-portálon házirendek?  
Azure Active Directory mindkét házirend érvényesíti, és a felhasználó élvezheti a hozzáférést, csak akkor, ha összes követelmény teljesülését.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Mi történik, ha az Intune Silverlight és az Azure-portálhoz a házirendek?
Azure Active Directory mindkét házirend érvényesíti, és a felhasználó élvezheti a hozzáférést, csak akkor, ha összes követelmény teljesülését.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Mi történik, ha a konfigurált felhasználónak több házirendet?  
Minden bejelentkezés az Azure Active Directory kiértékeli az összes házirend, és biztosítja, hogy az összes követelmények teljesülnek-e a felhasználó hozzáférése engedélyezett előtt.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>Feltételes hozzáférés az Exchange ActiveSync szolgáltatással működik?

Igen, az Exchange ActiveSync is használhatja a feltételes hozzáférési házirendben.


## <a name="what-you-should-avoid-doing"></a>Mi kerülendő ezzel

A feltételes hozzáférés keretrendszer egy nagyszerű konfigurációs rugalmasságot biztosít. Azonban rugalmas lehetőségeket biztosítanak azt is jelenti, hogy alaposan tekintse át minden konfigurációs házirend nem kívánt eredmények elkerülése érdekében annak engedélyezése előtt. Ebben a környezetben, akkor kell nagy figyelmet fordítani, mint a teljes érintő hozzárendelések **minden felhasználók / csoportok / felhőalapú alkalmazásokba**.

A környezetben kerülje el a következő beállításokat:


**Az összes felhasználó számára az összes felhőalapú alkalmazásokat:**

- **Letiltja a hozzáférést,** -ebben a konfigurációban a teljes szervezet, amely nincs mindenképpen célszerű blokkolja.

- **Megfelelő eszközökre szükséges** – a felhasználók számára, amely nem regisztrálták az eszközeiket, mégis a házirend nem engedélyezi az összes eléréséhez, beleértve az Intune-portál eléréséhez. Ha Ön rendszergazda a regisztrált eszköz nélküli, akkor ez a házirend letiltja a illetéktelen vissza az Azure-portálon a házirend módosítása.

- **Megkövetelje a tartományhoz való csatlakozást** – Ez a házirend-blokk hozzáférés nem is letiltja a hozzáférést a szervezete összes felhasználója számára, ha még nem rendelkezik a tartományhoz csatlakoztatott eszközön.


**Az összes felhasználó számára az összes felhőalapú alkalmazások, az összes eszközplatformra:**

- **Letiltja a hozzáférést,** -ebben a konfigurációban a teljes szervezet, amely nincs mindenképpen célszerű blokkolja.



## <a name="policy-migration"></a>Házirend áttelepítése

Érdemes lehet a házirendek nem hozott létre az Azure portálon mert áttelepítése:

- Ön nem lehet kezelni előtt forgatókönyvek most meg lehet oldani.

- Csökkentése érdekében egyesítse ezeket által felügyelni kell házirendek száma.   

- Kezelheti egy központi helyen a feltételes hozzáférési szabályzatokat.

- A klasszikus Azure portálon rendszerből.   


További információkért lásd: [klasszikus szabályzatokkal az Azure portálon áttelepítése](active-directory-conditional-access-migration.md).


## <a name="next-steps"></a>Következő lépések

Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).
