---
title: Egyszeri bejelentkezés alkalmazásokhoz az Azure AD-alkalmazásproxyval |} A Microsoft Docs
description: Kapcsolja be egyszeri bejelentkezést a közzétett helyszíni alkalmazásait az Azure AD-alkalmazásproxy az Azure Portalon.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/20/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: fa12dd5e9dbe25bad947abed5ab1c732d231b25c
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39362822"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Az egyszeri bejelentkezés alkalmazásproxyval való vaulting jelszó

Az Azure Active Directory Application Proxy segítségével növelje hatékonyságát azáltal, hogy a távoli alkalmazottak biztonságosan érhetik el azokat, túl a helyszíni alkalmazás-közzététel. Az Azure Portalon is állíthat be egyszeri bejelentkezéssel (SSO) ezekre az alkalmazásokra. A felhasználók csak az Azure AD-hitelesítést kell, és nem kell ismét jelentkezzen be a vállalati alkalmazás eléréséhez.

Az alkalmazásproxy támogatja több [egyszeri bejelentkezési módok](application-proxy-single-sign-on.md). Jelszóalapú bejelentkezés célja az alkalmazásokat, amelyek egy felhasználónév – jelszó kombinációt használja a hitelesítéshez. Az alkalmazás konfigurálása a jelszóalapú bejelentkezés, a felhasználóknak kell jelentkezzen be a helyszíni alkalmazás egyszer. Ezt követően az Azure Active Directory tárolja a bejelentkezési adatait, és automatikusan átadja az alkalmazás, amikor a felhasználók távolról elérni. 

Érdemes már közzétett és tesztelte az alkalmazást az alkalmazásproxy használatával. Ha nem, kövesse a lépéseket a [alkalmazások közzététele az Azure AD-alkalmazásproxy](application-proxy-publish-azure-portal.md) majd térjen vissza ide. 

## <a name="set-up-password-vaulting-for-your-application"></a>Az alkalmazás vaulting jelszó beállítása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Válassza ki **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás**.
3. A listában jelölje ki az alkalmazást, amelyet szeretne az egyszeri bejelentkezés beállítása.  
4. Válassza ki **egyszeri bejelentkezési**.

   ![Válassza ki az egyszeri bejelentkezés](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

5. Az egyszeri bejelentkezési módot válassza **jelszóalapú bejelentkezés**.
6. A bejelentkezési URL-címet adja meg az URL-címet az oldal amelyen a felhasználók megadják felhasználónevüket és jelszavukat, jelentkezzen be az alkalmazást a vállalati hálózaton kívül. Ez lehet a külső URL-CÍMÉT, amikor az alkalmazás alkalmazásproxyn keresztül közzétett létrehozott. 

   ![Válassza ki a jelszóalapú bejelentkezés, és adja meg az URL-cím](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

7. Kattintson a **Mentés** gombra.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Az alkalmazás tesztelése

Nyissa meg a külső URL-cím, amely az alkalmazás konfigurálva a távoli hozzáféréshez. Jelentkezzen be hitelesítő adataival, amelyet az alkalmazás (vagy egy Ön által beállított hozzáférési a teszt fiók hitelesítő adatait). Miután a bejelentkezés sikeres, hagyja meg az alkalmazást, és térjen vissza a hitelesítő adatok újbóli megadása nélkül kell lennie. 

## <a name="next-steps"></a>További lépések

- Olvassa el más módokon megvalósításához [egyszeri bejelentkezést az alkalmazásproxy használatával](application-proxy-single-sign-on.md)
- Ismerje meg [fér hozzá az alkalmazásokhoz távolról az Azure AD-alkalmazásproxy történő futtatásának biztonsági szempontjai](application-proxy-security.md)