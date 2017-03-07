---
title: "Naplózási tevékenységre vonatkozó jelentések az Azure Active Directory portál előzetes kiadásában | Microsoft Docs"
description: "Naplózási tevékenységre vonatkozó jelentések az Azure Active Directory portál előzetes kiadásában – bevezetés"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 74460492c5eb6edfcc67015f8f6894267cb9edc8
ms.openlocfilehash: 5a219219cd5e34713cd6a1c54a98d6bd15310e05
ms.lasthandoff: 02/22/2017


---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal---preview"></a>Naplózási tevékenységre vonatkozó jelentések az Azure Active Directory portál előzetes kiadásában

Az Azure Active Directory [előzetes kiadásában](active-directory-preview-explainer.md) elérhető jelentéskészítéssel minden szükséges információhoz hozzájuthat a környezetével kapcsolatban.

Az Azure Active Directory jelentéskészítési architektúrája a következő elemekből áll:

- **Tevékenység** 
    - **Bejelentkezési tevékenységek** – A felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információk
    - **Naplók** – Rendszertevékenység információk a felhasználó- és csoportfelügyeletre, valamint a felügyelt alkalmazásokra és a címtártevékenységekre vonatkozóan.
- **Biztonság** 
    - **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. További részletek: Kockázatos bejelentkezések.
    - **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. További részletek: Kockázatosként megjelölt felhasználók.

Ezen témakör áttekintést nyújt a naplózási tevékenységekről.
 
## <a name="audit-logs"></a>Naplók

Az Azure Active Directory naplói a rendszertevékenységek rekordjait tartalmazzák megfelelőségi célokból.

Az Azure Portalon három fő kategóriája van a kapcsolódó tevékenységek naplózásának:

- Felhasználók és csoportok   

- Alkalmazások

- Címtár   

A naplózási jelentési tevékenységek teljes listáját [a naplózási jelentési események listájában](active-directory-reporting-audit-events.md#list-of-audit-report-events) tekintheti meg.


A naplózási adatok áttekintését a **Naplók** használatával kezdheti meg, az **Azure Active Directory** **Műveletek** szakaszában található.

![Naplók](./media/active-directory-reporting-activity-audit-logs/61.png "Naplók")

Az auditnapló olyan listanézetet is tartalmaz, amely az aktorokat (*ki*), a tevékenységeket (*mit*) és a célokat is megjeleníti.

![Naplók](./media/active-directory-reporting-activity-audit-logs/345.png "Naplók")

Részletes információk a listanézet elemeire kattintva érhetők el.

![Naplók](./media/active-directory-reporting-activity-audit-logs/873.png "Naplók")


## <a name="users-and-groups-audit-logs"></a>Felhasználók és csoportok auditnaplói

A felhasználó- és csoportalapú naplózási jelentésekkel az alábbi kérdésekre kaphat választ:

- Milyen típusú frissítéseket telepítettek a felhasználók?

- Hány felhasználó lett módosítva?

- Hány jelszó lett módosítva?

- Mit csinált a rendszergazda egy adott címtárban?

- Mely csoportok lettek hozzáadva?

- Történt-e tagsági változás valamelyik csoportban?

- Változtak-e a csoportok tulajdonosai?

- Milyen licencek lettek hozzárendelve egy adott csoporthoz vagy felhasználóhoz?

Ha csak át szeretné tekinteni a felhasználókhoz és csoportokhoz kapcsolódó naplózási adatokat, megnyithat egy szűrt nézetet az **Auditnaplók** menüpontból, amely a **Felhasználók és csoportok** **Tevékenység** szakaszában található.

![Naplók](./media/active-directory-reporting-activity-audit-logs/93.png "Naplók")

## <a name="application-audit-logs"></a>Alkalmazások auditnaplói
Az alkalmazásalapú naplózási jelentésekkel az alábbi kérdésekre kaphat választ:

* Mely alkalmazások lettek hozzáadva vagy frissítve?
* Mely alkalmazások lettek eltávolítva?
* Megváltozott valamelyik alkalmazás egyszerű szolgáltatásneve?
* Történt változás az alkalmazások nevében?
* Ki hagyott jóvá egy adott alkalmazást?

Ha csak át szeretné tekinteni az alkalmazásokhoz kapcsolódó naplózási adatokat, megnyithat egy szűrt nézetet az **Auditnaplók** menüpontból, amely a **Vállalati alkalmazások** **Tevékenység** szakaszában található.

![Naplók](./media/active-directory-reporting-activity-audit-logs/134.png "Naplók")

## <a name="filtering-audit-logs"></a>Auditnaplók szűrése
A következő mezőkkel szűrheti a bejelentkezéseket a megjelenített adatok korlátozása érdekében:

- Dátum és idő

- Szereplő felhasználói egyszerű neve

- Kategória

- Tevékenység erőforrástípusa

- Tevékenység

![Naplók](./media/active-directory-reporting-activity-audit-logs/625.png "Naplók")


A **Kategória** szűrővel leszűkíthető a naplózási jelentés hatóköre a következő kategóriák alapján:

- Alapvető könyvtár

- Önkiszolgáló jelszókezelés

- Önkiszolgáló csoportkezelés

- Automatizált jelszóváltás 

![Naplók](./media/active-directory-reporting-activity-audit-logs/626.png "Naplók")



A **Tevékenység erőforrástípusa** lista tartalma a panel belépési pontjához van kötve.  
Ha a belépési pontja az Azure Active Directory, ez a lista tartalmazza az összes lehetséges tevékenységtípust:

- Címtár

- Felhasználó

- Csoport 

- Alkalmazás 

- Szabályzat

- Eszköz


![Naplózás](./media/active-directory-reporting-activity-audit-logs/627.png "Naplózás")

A listázott tevékenységek hatókörét a tevékenységtípus határozza meg.
Ha például a **Felhasználó** érték van kiválasztva a **Tevékenység típusaként**, a **Tevékenység** lista csak a csoporttal kapcsolatos tevékenységeket tartalmazza.   

![Naplózás](./media/active-directory-reporting-activity-audit-logs/628.png "Naplózás")

Ha a **Felhasználó** értéket választja a **tevékenység típusaként**, egy további szűrőlehetőség érhető el, amely lehetővé teszi a következő **tevékenységerőforrások** alapján való szűrést:

- Azure AD

- O365


![Naplózás](./media/active-directory-reporting-activity-audit-logs/629.png "Naplózás")



Az auditnaplók bejegyzéseit konkrét bejegyzések keresésével is szűrheti.

![Naplózás](./media/active-directory-reporting-activity-audit-logs/237.png "Naplózás")




## <a name="next-steps"></a>Következő lépések
Lásd az [Azure Active Directory Reporting-útmutatót](active-directory-reporting-guide.md).


