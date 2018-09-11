---
title: Hibás rendszergazdai hitelesítő adatok mentése az Azure AD katalógusából származó alkalmazásba történő felhasználókiépítés konfigurálása során |} A Microsoft Docs
description: Amikor konfigurálása felhasználókiépítés egy alkalmazás már szerepel az Azure AD Alkalmazáskatalógusában megmérkőzött gyakori hibáinak elhárítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: fe96ecc0ba6904819f0262a2f470e37203a7952e
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357109"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>A probléma az Azure Active Directory-gyűjtemény alkalmazáshoz történő felhasználókiépítés konfigurálása során a rendszergazdai hitelesítő adatok mentése 

Ha az Azure portal használatával konfigurálhatja [felhasználók automatikus átadása](user-provisioning.md) vállalati alkalmazás esetén előfordulhat, hogy olyan helyzet állhat elő ahol:

* A **rendszergazdai hitelesítő adataival** megadott alkalmazására érvényesek, és a **kapcsolat tesztelése** works gombra. Azonban nem lehet menteni a hitelesítő adatokat, és az Azure Portalon egy általános hibaüzenetet adja vissza.

SAML-alapú egyszeri bejelentkezést is konfigurálva ugyanahhoz az alkalmazáshoz, ha a hiba legvalószínűbb oka az, hogy az Azure AD belső, alkalmazásonkénti tárolási kapacitása a tanúsítványok és a rendszer túllépte a hitelesítő adatokat.

Az Azure AD jelenleg a tanúsítványok, titkos jogkivonatok, hitelesítő adatok és kapcsolódó konfigurációs adatokat (más néven egy szolgáltatás egyszerű rekord az Azure AD-) alkalmazás egyetlen példánya társított egy kilobájtoktól a petabájtokig maximális tárolási kapacitása nem.

Ha SAML-alapú egyszeri bejelentkezés konfigurálva van, a SAML-jogkivonatok aláírásához használt tanúsítvány itt tárolja, és gyakran használ több mint 50 % százalékát.

Bármely titkos jogkivonatok, URI-k, értesítési e-mail-címeket, felhasználói neveket és, hogy a felhasználók átadásának beállítása során megadott első okozhat a tárolási korlát túllépését.

## <a name="how-to-work-around-this-issue"></a>Útmutató a probléma megkerüléséhez 

A probléma megkerüléséhez ma két lehetséges módja van:

1. **Két katalógus alkalmazáspéldányok, egy az egyszeri bejelentkezés és egy a felhasználók átadásának** -véve a katalógusból származó alkalmazásra [LinkedIn jogosultságszint-emelés](../saas-apps/linkedinelevate-tutorial.md) tegyük fel, a galériából a LinkedIn jogosultságszint-emelés hozzáadása és konfigurálása Ez az egyszeri bejelentkezés. Üzembe helyezés, LinkedIn jogosultságszint-emelés egy másik példánya hozzáadása az Azure AD-alkalmazásgyűjtemény, és nevezze el "LinkedIn jogosultságszint (kiépítés).-emelés" A második példány konfigurálása [kiépítés](../saas-apps/linkedinelevate-provisioning-tutorial.md), de nem egyszeri bejelentkezés. Ez a megoldás használata esetén az azonos felhasználók és csoportok kell lennie [hozzárendelt](assign-user-or-group-access-portal.md) mindkét alkalmazásokhoz. 

2. **Tárolt konfigurációs adatok mennyiségének csökkentésére** – az összes megadott adatokat, a [rendszergazdai hitelesítő adataival](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) szakaszban az üzembe helyezési lapon SAML-tanúsítványt az ugyanazon a helyen tárolja. Előfordulhat, hogy nem lehet rövidebb minden adatot, bár egyes választható konfiguráció mezők, például a **értesítő e-mailt** távolíthatja el.

## <a name="next-steps"></a>További lépések
[A felhasználó üzembe helyezést és megszüntetést SaaS-alkalmazások konfigurálása](user-provisioning.md)
