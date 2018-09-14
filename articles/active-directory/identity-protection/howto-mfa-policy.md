---
title: A többtényezős hitelesítési regisztrációs házirend konfigurálása az Azure Active Directory Identity Protection |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure AD Identity Protection többtényezős hitelesítési regisztrációs házirend.
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: e626260dba3155ef56ee4a784aab2c6fd6897295
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45581362"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Útmutató: A többtényezős hitelesítési regisztrációs házirend konfigurálása

Az Azure AD Identity Protection segítséget az üzembe helyezést (MFA) többtényezős hitelesítési regisztrációs házirend konfigurálásával. Ez a cikk leírja, mi a házirend használható egy konfigurálásának.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>Mi az a többtényezős hitelesítési regisztrációs házirend?

Az Azure multi-factor authentication a egy módszer annak ellenőrzése, akik, amely több, mint felhasználónév és jelszó szükséges. Biztosít egy második biztonsági szintként, felhasználói bejelentkezéseket és tranzakciókat.  
Azt javasoljuk, hogy az Azure multi-factor authentication a felhasználók bejelentkezési folyamatába igényel el, mert azt:

* Szolgáltatás egyszerű ellenőrzési lehetőség számos szigorú hitelesítést végez
* A szervezet védelme és helyreállítása a fiók feltörések előkészítése a kulcsfontosságú szerepet játszik az

![Felhasználói kockázati házirend](./media/howto-mfa-policy/1019.png "felhasználói kockázati házirend")

További részletekért lásd: [Mi az Azure multi-factor Authentication?](../authentication/multi-factor-authentication.md)

## <a name="configuration"></a>Konfiguráció

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

- Az a **Azure AD Identity Protection** panelen, a a **konfigurálása** területén kattintson **többtényezős hitelesítési regisztráció**.

    ![Többtényezős hitelesítési szabályzat](./media/howto-mfa-policy/1019.png "többtényezős hitelesítési szabályzat")

### <a name="settings"></a>Beállítások

* Állítsa be a felhasználókat és csoportokat a szabályzat vonatkozik:

    ![Többtényezős hitelesítési szabályzat](./media/howto-mfa-policy/1020.png "többtényezős hitelesítési szabályzat")
* Állítsa be az aktiválásakor a szabályzatot érvényesíteni kívánt vezérlőket::  

    ![Többtényezős hitelesítési szabályzat](./media/howto-mfa-policy/1021.png "többtényezős hitelesítési szabályzat")
* Váltás a házirend állapota:

    ![Többtényezős hitelesítési szabályzat](./media/howto-mfa-policy/403.png "többtényezős hitelesítési szabályzat")
* Az aktuális regisztrációs állapot megtekintése:

    ![Többtényezős hitelesítési szabályzat](./media/howto-mfa-policy/1022.png "többtényezős hitelesítési szabályzat")

A kapcsolódó felhasználói szolgáltatások áttekintését lásd:

* [A multi-factor authentication regisztrációs folyamat](flows.md#multi-factor-authentication-registration).  
* [Bejelentkezési élmény az Azure AD Identity Protection](flows.md).  



## <a name="next-steps"></a>További lépések

Az Azure AD Identity Protection áttekintést kaphat, tekintse meg a [áttekintése az Azure AD Identity Protection](overview.md).
