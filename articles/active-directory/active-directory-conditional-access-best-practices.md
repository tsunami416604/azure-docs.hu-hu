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
ms.date: 09/16/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 119189415e75134ff0c77a551536559b81116fc6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
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

Ha a klasszikus Azure portálon konfigurált házirendek, kell át azokat az Azure-portálon mert:


- Egy felhasználót, aki az Azure klasszikus portál házirend és az Azure portál házirend kell felelnie a mindkét házirend 

- Ha a meglévő szabályzatokat nem települnek át, nem lesz, amelyek hozzáférést-házirendek implementálásához


### <a name="migration-from-the-azure-classic-portal"></a>Áttelepítés a klasszikus Azure portálon

Ebben a forgatókönyvben: 

- Az a [a klasszikus Azure portálon](https://manage.windowsazure.com), konfigurálta:

    - SharePoint Online

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-best-practices/14.png)

    - Eszközalapú feltételes hozzáférési házirend

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-best-practices/15.png)

- A mobilalkalmazás-felügyeleti feltételes hozzáférési szabályzat konfigurálása az Azure portálon szeretné 
 

#### <a name="configuration"></a>Konfiguráció 

- Tekintse át az eszközalapú feltételes hozzáférési házirendek

- Áttelepíthetik őket az Azure-portálon 

- Mobilalkalmazás-felügyeleti feltételes hozzáférési házirendek hozzáadása


### <a name="migrating-from-intune"></a>Az Intune-ból áttelepítése 

Ebben a forgatókönyvben:

- A [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ), vagy Exchange online-hoz egy mobilalkalmazás-felügyeleti feltételes hozzáférési házirenddel rendelkezik, vagy a SharePoint online-hoz konfigurált

    ![Feltételes hozzáférés](./media/active-directory-conditional-access-best-practices/15.png)

- Az áttelepítendő mobilalkalmazás-kezelési feltételes hozzáférés az Azure-portál használatával


#### <a name="configuration"></a>Konfiguráció 
 
- Tekintse át az eszközalapú feltételes hozzáférési házirendek

- Áttelepíthetik őket az Azure-portálon 

- Tekintse át, hogy mobilalkalmazás felügyeleti feltételes hozzáférési szabályzatok beállítása az Exchange Online vagy SharePoint online-hoz az Intune-ban

- A vezérlő hozzáadása **szükséges jóváhagyott alkalmazások** mellett az eszköz-alapú vezérlő 
 

### <a name="migrating-from-the-azure-classic-portal-and-intune"></a>A klasszikus Azure portál és az Intune áttelepítése

Ebben a forgatókönyvben:

- A következő beállításokkal rendelkezik:

    - **A klasszikus Azure portálon:** eszközalapú feltételes 

    - **Intune:** mobilalkalmazás felügyeleti feltételes hozzáférési házirendek 
    
- Mindkét házirend áttelepítendő a mobilalkalmazás-kezelési házirendekkel a feltételes hozzáférés az Azure-portálon


#### <a name="configuration"></a>Konfiguráció

- Tekintse át az eszközalapú feltételes hozzáférési házirendek

- Áttelepíthetik őket az Azure-portálon 

- Tekintse át, akkor a Exchange Online vagy a SharePoint online-hoz az Intune-ban mobilalkalmazás felügyeleti feltételes hozzáférési házirend

- A vezérlő hozzáadása **szükséges jóváhagyott alkalmazások** mellett az eszköz-alapú 




## <a name="next-steps"></a>Következő lépések

Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).
