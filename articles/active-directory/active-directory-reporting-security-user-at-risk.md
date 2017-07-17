---
title: "A veszélyeztetett felhasználókról szóló biztonsági jelentés az Azure Active Directory portálon | Microsoft Docs"
description: "Ismerje meg az Azure Active Directory portál veszélyeztetett felhasználókról szóló biztonsági jelentését"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 01ecb98c02b2a01007c7f76805d4db4b7aeee1f0
ms.contentlocale: hu-hu
ms.lasthandoff: 05/08/2017

---
# A veszélyeztetett felhasználókról szóló biztonsági jelentés az Azure Active Directory portálon
<a id="users-at-risk-security-report-in-the-azure-active-directory-portal" class="xliff"></a>

Az Azure Active Directory (Azure AD) biztonsági jelentéseivel megtudhatja, hogy a környezetben mekkora valószínűséggel sérült egyes felhasználói fiókok biztonsága. 

Az Azure Active Directory észleli a felhasználói fiókokhoz kapcsolódó gyanús tevékenységeket. A rendszer minden egyes észlelt tevékenység esetében létrehoz egy *kockázati esemény* nevű rekordot. További részletek: [Az Azure Active Directory kockázati eseményei](active-directory-identity-protection-risk-events.md). 

A rendszer az észlelt kockázati eseményeket a következők kiszámítására használja:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. További részletek: [Kockázatos bejelentkezések](active-directory-identityprotection.md#risky-sign-ins). 

- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. További részletek: [Kockázatosként megjelölt felhasználók](active-directory-identityprotection.md#users-flagged-for-risk).  

Az Azure Portalon a biztonsági jelentések az **Azure Active Directory** panel **Biztonság** szakaszában találhatók.  

![Kockázatos bejelentkezések](./media/active-directory-reporting-security-user-at-risk/10.png)

## Azure Active Directory – ingyenes és alapszintű kiadások
<a id="azure-active-directory-free-and-basic-edition" class="xliff"></a>

Az Azure Active Directory ingyenes és alapszintű kiadásának Veszélyeztetett felhasználókról szóló jelentése azokról a felhasználói fiókokról biztosít listát, amelyeknek elképzelhető, hogy sérült a biztonsága. 


![Kockázatos bejelentkezések](./media/active-directory-reporting-security-user-at-risk/03.png)

Az egyes felhasználók kijelölésével megnyithatja a kapcsolódó felhasználói adatok paneljét.
A veszélyeztetett felhasználók esetében áttekintheti a felhasználó bejelentkezési előzményeit, és szükség esetén alaphelyzetbe állíthatja a jelszót.

![Kockázatos bejelentkezések](./media/active-directory-reporting-security-user-at-risk/46.png)

## Azure Active Directory – prémium szintű kiadások
<a id="azure-active-directory-premium-editions" class="xliff"></a>

Az Azure Active Directory prémium kiadásaiban a Veszélyeztetett felhasználókról szóló jelentés a következőket tartalmazza:

- Egy [lista azokról a felhasználói fiókokról](active-directory-identityprotection.md#users-flagged-for-risk), amelyeknek elképzelhető, hogy sérült a biztonsága. 

- Összesített adatok az észlelt [kockázati események típusairól](active-directory-identity-protection-risk-events.md)

- Lehetőség a jelentés letöltésére

- Egy [felhasználóikockázat-csökkentési szabályzat](active-directory-identityprotection.md#user-risk-security-policy) konfigurálását  


![Kockázatos bejelentkezések](./media/active-directory-reporting-security-user-at-risk/71.png)

Egy felhasználó kiválasztásakor megkapja a felhasználó részletes jelentési nézetét, amely a következőket teszi lehetővé:

- Az Összes bejelentkezés nézet megnyitását

- A felhasználói jelszó alaphelyzetbe állítását

- Az összes esemény elvetését

- A felhasználóhoz kapcsolódó, jelentett kockázati események vizsgálatát. 


![Kockázatos bejelentkezések](./media/active-directory-reporting-security-user-at-risk/324.png)


Egy kockázati esemény vizsgálatához válassza ki azt a listából.  
Ekkor megnyílik az eseményre vonatkozó **Részletek** panel. A **Részletek** panelen [manuálisan bezárhatja a kockázati eseményeket](active-directory-identityprotection.md#closing-risk-events-manually) vagy újra aktiválhatja a manuálisan bezárt kockázati eseményeket. 


![Kockázatos bejelentkezések](./media/active-directory-reporting-security-user-at-risk/325.png)



## Következő lépések
<a id="next-steps" class="xliff"></a>

- Az Azure Active Directory Identity Protectionnel kapcsolatos további részletekért lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md).


