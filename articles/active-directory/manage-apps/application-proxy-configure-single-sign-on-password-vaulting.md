---
title: Egyszeri bejelentkezés az alkalmazásokba az Azure AD alkalmazásproxyval | Microsoft dokumentumok
description: Kapcsolja be az egyszeri bejelentkezést a közzétett helyszíni alkalmazásokhoz az Azure AD alkalmazásproxyval az Azure Portalon.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0259a8d9fcb4c9c513ab2c31103c9a8488e90ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025741"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Jelszótároló az alkalmazásproxyval való egyszeri bejelentkezéshez

Az Azure Active Directory alkalmazásproxy a helyszíni alkalmazások közzétételével segít a termelékenység növelésében, hogy a távoli alkalmazottak is biztonságosan elérhessék azokat. Az Azure Portalon is beállíthatja az egyszeri bejelentkezést (SSO) ezekhez az alkalmazásokhoz. A felhasználóknak csak az Azure AD-vel kell hitelesíteniük magukat, és a vállalati alkalmazáshoz anélkül férhetnek hozzá, hogy újra be kellene jelentkezniük.

Az alkalmazásproxy számos [egyszeri bejelentkezési módot](what-is-single-sign-on.md#choosing-a-single-sign-on-method)támogat. A jelszóalapú bejelentkezés olyan alkalmazásokhoz készült, amelyek felhasználónév/jelszó kombinációt használnak a hitelesítéshez. Amikor jelszóalapú bejelentkezést konfigurál az alkalmazáshoz, a felhasználóknak egyszer kell bejelentkezniük a helyszíni alkalmazásba. Ezt követően az Azure Active Directory tárolja a bejelentkezési adatokat, és automatikusan biztosítja az alkalmazás számára, amikor a felhasználók távolról hozzáférnek.

Az alkalmazást már közzé kellett volna tennie, és tesztelnie kellett volna az alkalmazásproxyval. Ha nem, kövesse az Alkalmazások közzététele az [Azure AD alkalmazásproxy használatával](application-proxy-add-on-premises-application.md) című lépéseit, majd jöjjön vissza ide.

## <a name="set-up-password-vaulting-for-your-application"></a>Jelszó-tároló beállítása az alkalmazáshoz

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) rendszergazdaként.
1. Válassza az **Azure Active Directory** > **Enterprise alkalmazások** > **minden alkalmazás lehetőséget.**
1. A listából válassza ki azt az alkalmazást, amelyet az SSO-val szeretne beállítani.  
1. Válassza **az Alkalmazásproxy**lehetőséget . 
1. Módosítsa az **Előhitelesítés típust** **áthaladásra,** és válassza a **Mentés lehetőséget.** Később újra visszaválthat **az Azure Active Directory-típusra!** 
1. Válassza **az Egyszeri bejelentkezés**lehetőséget.

   ![Válassza az Egyszeri bejelentkezés lehetőséget az alkalmazás áttekintő lapján](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Az Egyszeri bejelentkezés módhoz válassza a **Jelszóalapú bejelentkezés**lehetőséget.
1. A bejelentkezési URL-címhez adja meg annak a lapnak az URL-címét, ahol a felhasználók megadják a felhasználónevüket és jelszavukat, hogy a vállalati hálózaton kívül jelentkezzenek be az alkalmazásba. Ez lehet az a külső URL-cím, amelyet akkor hozott létre, amikor közzétette az alkalmazást az alkalmazásproxyn keresztül.

   ![Jelszóalapú bejelentkezés kiválasztása és URL-cím megadása](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Kattintson a **Mentés** gombra.
1. Válassza **az Alkalmazásproxy**lehetőséget . 
1. Módosítsa az **előhitelesítés típusát** **az Azure Active Directoryra,** és válassza a **Mentés**lehetőséget. 
1. Válassza a **Felhasználók és csoportok lehetőséget.**
1. Rendeljen felhasználókat az alkalmazáshoz a **Felhasználó hozzáadása**lehetőség kiválasztásával. 
1. Ha egy felhasználó hitelesítő adatait előre definiálni szeretné, jelölje be a felhasználónév előtti jelölőnégyzetet, és válassza a **Hitelesítő adatok frissítése**lehetőséget.
1. Válassza az **Azure Active Directory** > **alkalmazás regisztrációja** > **Minden alkalmazás**lehetőséget.
1. A listából válassza ki a jelszó-sso-val konfigurált alkalmazást.
1. Válassza a **Márkajelzés lehetőséget.** 
1. Frissítse a **kezdőlap URL-címét** a **Bejelentkezési URL-címmel** a Jelszó-sSO lapon, és válassza a **Mentés lehetőséget.**  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Az alkalmazás tesztelése

Nyissa meg a Saját alkalmazások portált. Jelentkezzen be hitelesítő adataival (vagy a hozzáféréssel beállított tesztfiók hitelesítő adataival). Miután sikeresen bejelentkezett, kattintson az alkalmazás ikonjára. Ez elindíthatja a My Apps Secure Bejelentkezésböngésző bővítmény telepítését. Ha a felhasználó előre definiált hitelesítő adatokkal rendelkezik, az alkalmazás hitelesítésének automatikusan meg kell történnie, ellenkező esetben először meg kell adnia a felhasználónevet vagy a jelszót. 

## <a name="next-steps"></a>További lépések

- További információ az egyszeri bejelentkezés megvalósításának egyéb [módjairól](what-is-single-sign-on.md)
- Információ az [Azure AD alkalmazásproxyval távolról elérhető alkalmazások elérésével kapcsolatos biztonsági szempontokról](application-proxy-security.md)
