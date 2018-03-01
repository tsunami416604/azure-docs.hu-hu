---
title: "Probléma rendszergazdai hitelesítő adatok mentése során a felhasználók átadása egy Azure AD-katalógusában alkalmazás konfigurálása |} Microsoft Docs"
description: "Konfigurálását a felhasználók átadása egy alkalmazás már szerepel az Azure AD Application Gallery tapasztalt kapcsolatos gyakori hibák elhárítása"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: asmalser
ms.openlocfilehash: 6617345c8923b1fc8081b01ddfe8b4bedf10b6ea
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>A probléma rendszergazdai hitelesítő adatok mentése során a felhasználók átadása egy Azure Active Directory Képtár alkalmazás konfigurálása 

Amikor konfigurálása az Azure portál használatával [automatikus felhasználólétesítés](active-directory-saas-app-provisioning.md) egy vállalati alkalmazás olyan helyzet állhat elő ahol:

* A **rendszergazdai hitelesítő adataival** megadott alkalmazására érvényesek, és a **kapcsolat tesztelése** works gombra. Azonban a hitelesítő adatok nem menthetők, és az Azure portál egy általános hibaüzenetet adja vissza.

SAML-alapú egyszeri bejelentkezést is konfigurálva van ugyanahhoz az alkalmazáshoz, ha a hiba legvalószínűbb oka, hogy az Azure AD belső, alkalmazásonkénti tárolási kapacitása a tanúsítványok, és a rendszer túllépte a hitelesítő adatokat.

Jelenleg a Azure AD is rendelkezik a tárolókészlet teljes tárhelykapacitását kihasználja egy kilobájtos tanúsítványok, titkos jogkivonatokat, hitelesítő adatok és a egyetlen példány futhat egy alkalmazás (más néven a szolgáltatás egyszerű rekordnak az Azure AD) konfigurációs adatait.

Ha SAML-alapú egyszeri bejelentkezésre van konfigurálva, a tanúsítványt a SAML-jogkivonatokat aláírásához használt itt, és gyakran használ több mint 50 % százalékát.

Bármely titkos jogkivonatokat, URI-k, értesítési e-mail címek, felhasználónevek és jelszavak beolvasása telepítése felhasználólétesítés során megadott a tárolási korlát túllépését okozhatják.

## <a name="how-to-work-around-this-issue"></a>Hogyan működnek a probléma megoldásához 

A probléma megoldása ma két lehetséges módja van:

1. **Két gyűjtemény alkalmazáspéldányok, az egyszeri bejelentkezés és az egyikben felhasználói történő üzembe helyezéséhez használjon** -véve a gyűjtemény alkalmazás [LinkedIn jogosultságszint-emelés](active-directory-saas-linkedinelevate-tutorial.md) például LinkedIn jogosultságszint-emelés hozzáadása a gyűjteményből és konfigurálása az egyszeri bejelentkezéshez. Kialakítási, adja hozzá az Azure AD-alkalmazásgyűjtemény LinkedIn jogosultságszint-emelés egy másik példánya, és nevezze el "LinkedIn jogosultságszint (kiépítés).-emelés" Ez a második példány konfigurálása [kiépítés](active-directory-saas-linkedinelevate-provisioning-tutorial.md), de nem egyszeri bejelentkezés. Ez a megoldás használata esetén az azonos felhasználókat és csoportokat kell lenniük [hozzárendelt](active-directory-coreapps-assign-user-azure-portal.md) mindkét alkalmazásokhoz. 

2. **Tárolt konfigurációs adatok mennyiségének csökkentésére** -a megadott összes adat a [rendszergazdai hitelesítő adataival](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) szakaszban az üzembe helyezési lapon SAML-tanúsítványt ugyanazon a helyen tárolja. Nem lehet az adatok hosszának csökkentése érdekében, miközben néhány választható konfigurációs mezők, például a **értesítő e-mailt** távolíthatja el.

## <a name="next-steps"></a>További lépések
[Felhasználó üzembe helyezést és megszüntetést SaaS-alkalmazásokhoz való konfigurálásához](active-directory-saas-app-provisioning.md)
