---
title: Probléma az Azure AD-gyűjtemény alkalmazás felügyeleti hitelesítő adatainak mentésekor
description: Az Azure AD alkalmazásgalériában már felsorolt alkalmazásokba történő felhasználói kiépítés konfigurálásakor felmerülő gyakori problémák elhárítása
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ff80224037001e41daf49cd6fc21439b2cc5cff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522866"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Probléma a rendszergazdai hitelesítő adatok mentése az Azure Active Directory-csoportalkalmazásba való felhasználói kiépítés konfigurálása során 

Ha az Azure Portal használatával konfigurálja a vállalati alkalmazások [automatikus felhasználói kiépítését,](user-provisioning.md) előfordulhat, hogy a következő helyzetben:

* Az alkalmazáshoz megadott **rendszergazdai hitelesítő adatok** érvényesek, és a **Kapcsolat tesztelése** gomb működik. A hitelesítő adatok azonban nem menthetők, és az Azure Portal általános hibaüzenetet ad vissza.

Ha saml-alapú egyszeri bejelentkezés is konfigurálva van ugyanahhoz az alkalmazáshoz, a hiba legvalószínűbb oka az, hogy az Azure AD belső, alkalmazásonkénti tárolási korlátját túllépte a tanúsítványok és a hitelesítő adatok tekintetében.

Az Azure AD jelenleg 1024 bájt maximális tárolási kapacitással rendelkezik az összes tanúsítvány, titkos jogkivonat, hitelesítő adatok és az alkalmazás egyetlen példányához társított kapcsolódó konfigurációs adatok (más néven egyszerű szolgáltatásrekord az Azure AD-ben) számára.

Ha saml-alapú egyszeri bejelentkezés van konfigurálva, az SAML-jogkivonatok aláírásához használt tanúsítvány itt tárolódik, és gyakran a terület több mint 50%-át használja fel.

A titkos jogkivonatok, URI-k, értesítési e-mail-címek, felhasználónevek és jelszavak, amelyek et a felhasználó kiépítés beállítása során beírnak, a tárolási korlát túllépését okozhatják.

## <a name="how-to-work-around-this-issue"></a>A probléma megkerülése 

Ma két módon lehet megkerülni ezt a problémát:

1. **Két katalógus alkalmazáspéldány, egy egyszeri bejelentkezéshez és egy a felhasználó kiépítéséhez** – A gallery alkalmazás [LinkedIn Elevate](../saas-apps/linkedinelevate-tutorial.md) példaként, hozzáadhat LinkedIn Elevate a katalógusból, és konfigurálja az egyszeri bejelentkezéshez. Kiépítése, add egy másik példányát LinkedIn Elevate az Azure AD alkalmazáskatalógusból, és a "LinkedIn Elevate (Kiépítés)." Ehhez a második példányhoz konfigurálja [a kiépítést,](../saas-apps/linkedinelevate-provisioning-tutorial.md)de az egyszeri bejelentkezést ne. A megoldás használatakor ugyanazokat a felhasználókat és csoportokat kell [hozzárendelni](../manage-apps/assign-user-or-group-access-portal.md) mindkét alkalmazáshoz. 

2. **Csökkentse a tárolt konfigurációs adatok mennyiségét** – A létesítési lap [Rendszergazdai hitelesítő adatok](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) szakaszában megadott összes adat ugyanazon a helyen tárolódik, mint az SAML-tanúsítvány. Bár előfordulhat, hogy nem lehet csökkenteni az összes adat hosszát, néhány választható konfigurációs mező, például az **értesítési e-mail** eltávolítható.

## <a name="next-steps"></a>További lépések
[A felhasználók kiépítésének és a SaaS-alkalmazásokhoz való kiépítésének konfigurálása](user-provisioning.md)
