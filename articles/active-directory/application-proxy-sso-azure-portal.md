---
title: "Egyszeri bejelentkezés alkalmazásokhoz az Azure AD alkalmazásproxy |} Microsoft Docs"
description: "Kapcsolja be a egyszeri bejelentkezés az Azure AD alkalmazásproxy az Azure-portálon a közzétett helyszíni alkalmazásokhoz."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 4ca913d79d9abd4b50e4f280eb993235ca543b0f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Az egyszeri bejelentkezés az alkalmazásproxy vaulting jelszó

Az Azure Active Directory Alkalmazásproxyjával segít a helyszíni alkalmazások közzététele, hogy a távoli alkalmazottak biztonságosan érhetik el azokat, túl által a termelékenység növelése. Az Azure portálon is állíthatja be az egyszeri bejelentkezés (SSO) ezekhez az alkalmazásokhoz. A felhasználók csak kell az Azure AD hitelesíti, és nem kell ismét jelentkezzen be a vállalati alkalmazás eléréséhez.

Alkalmazásproxy támogatja több [az egyszeri bejelentkezési módok](application-proxy-sso-overview.md). Jelszó alapú bejelentkezés készült alkalmazásokat, amelyek egy felhasználónév/jelszó kombináció használják a hitelesítéshez. Konfigurálásakor jelszó alapú bejelentkezés az alkalmazáshoz, a felhasználók rendelkeznek a helyszíni alkalmazások egyszer bejelentkezhet. Ezt követően az Azure Active Directory a bejelentkezési adatait tárolja, és automatikusan biztosítja azt az alkalmazásnak, amikor a felhasználók távolról el. 

Kell már rendelkezik közzétett és tesztelni az alkalmazást az alkalmazásproxy. Ha nem, kövesse a lépéseket [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](application-proxy-publish-azure-portal.md) majd térjen vissza ide. 

## <a name="set-up-password-vaulting-for-your-application"></a>Az alkalmazás vaulting jelszó beállítása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Válassza ki **Azure Active Directory** > **vállalati alkalmazások** > **összes alkalmazás**.
3. A listában jelölje ki az egyszeri bejelentkezés állítsa be a kívánt alkalmazást.  
4. Válassza ki **egyszeri bejelentkezés**.

   ![Válassza ki az egyszeri bejelentkezés](./media/application-proxy-sso-azure-portal/select-sso.png)

5. Az egyszeri bejelentkezés mód kiválasztása **jelszóalapú bejelentkezés**.
6. A bejelentkezési URL-címet adja meg az URL-címet a lap ahol adja meg a felhasználók a felhasználónevével és jelszavával bejelentkezni az alkalmazást a vállalati hálózaton kívülről. Ennek az lehet az alkalmazás-proxyn keresztül történő közzétételekor létrehozott külső URL-CÍMÉT. 

   ![Válassza a jelszó alapú bejelentkezés, és írja be az URL-cím](./media/application-proxy-sso-azure-portal/password-sso.png)

7. Kattintson a **Mentés** gombra.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Az alkalmazás tesztelése

Ugrás a külső URL-címet, az alkalmazás állított be a távoli hozzáféréshez. Jelentkezzen be a hitelesítő adatait az alkalmazás (vagy egy Ön által beállított hozzáféréssel rendelkező teszt fiók hitelesítő adatait). Amikor bejelentkezik sikeresen hagyja meg az alkalmazást, és térjen vissza a hitelesítő adatok ismételt beírása nélkül kell lennie. 

## <a name="next-steps"></a>Következő lépések

- Olvassa el, egyéb módjai megvalósításához [egyszeri bejelentkezéshez az alkalmazásproxy](application-proxy-sso-overview.md)
- További tudnivalók [távolról az Azure AD alkalmazásproxy alkalmazásokhoz fér hozzá biztonsági szempontjai](application-proxy-security-considerations.md)