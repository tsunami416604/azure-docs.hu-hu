---
title: "Biztonsági jelentések készítése az Azure Active Directory ingyenes és alapszintű, előzetes kiadásában | Microsoft Docs"
description: "Ez a dokumentum az Azure Active Directory előzetes kiadásában elérhető különféle jelentéseket sorolja fel"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/19/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: fa5a6dab1e76d62956cbfdd9b8b0f64c014696db
ms.openlocfilehash: c1a3953c79cfac9d6f55b38971ee2b79ff4244eb


---
# <a name="security-reporting-in-the-azure-active-directory-free-and-basic-edition---preview"></a>Biztonsági jelentések készítése az Azure Active Directory ingyenes és alapszintű, előzetes kiadásában

Az Azure Active Directory [előzetes kiadásának](active-directory-preview-explainer.md) biztonsági jelentéseivel megtudhatja, hogy a környezetben mekkora valószínűséggel sérült egyes felhasználói fiókok biztonsága. 

Az Azure Active Directory észleli a felhasználói fiókokhoz kapcsolódó gyanús tevékenységeket. A rendszer minden egyes észlelt tevékenység esetében létrehoz egy *kockázati esemény* nevű rekordot. További részletek: [Az Azure Active Directory kockázati eseményei](active-directory-identity-protection-risk-events.md). 

A rendszer az észlelt kockázati eseményeket a következők kiszámítására használja:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. További részletek: [Kockázatos bejelentkezések](active-directory-identityprotection.md#risky-sign-ins). 

- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. További részletek: [Kockázatosként megjelölt felhasználók](active-directory-identityprotection.md#users-flagged-for-risk).  


## <a name="risky-sign-ins-report"></a>Kockázatos bejelentkezési jelentések

Az Azure Active Directory ingyenes és alapszintű kiadása biztosít egy listát, amely az észlelt kockázatos bejelentkezéseket tartalmazza a felhasználókra vonatkozóan. A kockázati eseményekről szóló jelentés a következőket biztosítja:

- **Felhasználó** – A bejelentkezési művelet során használt felhasználónév
- **IP** – Az Azure Active Directoryhoz való csatlakozáshoz használt eszköz IP-címe
- **Hely** – Az Azure Active Directoryhoz való csatlakozáshoz használt hely
- **Bejelentkezés ideje** – A bejelentkezés végrehajtásának időpontja
- **Állapot** – A bejelentkezés állapota

A jelentés adatai le is tölthetők.

![Jelentéskészítés](./media/active-directory-reporting-security-azure-portal-free-basic/01.png)

A kockázatos bejelentkezéssel kapcsolatos vizsgálata alapján visszajelzést küldhet az Azure Active Directorynak az alábbi műveletek formájában:

- Feloldás
- Megjelölés téves riasztásként
- Kihagyás
- Újraaktiválás

![Jelentéskészítés](./media/active-directory-reporting-security-azure-portal-free-basic/21.png)

További részletek: [Kockázati események manuális lezárása](active-directory-identityprotection.md#closing-risk-events-manually).


## <a name="users-at-risk-report"></a>Veszélyeztetett felhasználókról szóló jelentés

Az Azure Active Directory ingyenes kiadása biztosít egy listát azokról a felhasználói fiókokról, amelyeknek elképzelhető, hogy sérült a biztonsága. 


![Jelentéskészítés](./media/active-directory-reporting-security-azure-portal-free-basic/03.png)

A listában található felhasználókra kattintva megnyithatja a kapcsolódó felhasználói adatok paneljét.
A veszélyeztetett felhasználók esetében áttekintheti a felhasználó bejelentkezési előzményeit, és szükség esetén alaphelyzetbe állíthatja a jelszót.

![Jelentéskészítés](./media/active-directory-reporting-security-azure-portal-free-basic/46.png)



## <a name="next-steps"></a>Következő lépések

- Az Azure Active Directory jelentéskészítési funkcióival kapcsolatos további részletekért lásd az [Azure Active Directory Reporting-útmutatót](active-directory-reporting-guide.md).
- Az Azure Active Directory Identity Protectionnel kapcsolatos további részletekért lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md).




<!--HONumber=Jan17_HO3-->


