---
title: Probléma az Azure AD Gallery-alkalmazás konfigurálásával kapcsolatos rendszergazdai hitelesítő adatok mentésekor | Microsoft Docs
description: Az Azure AD-alkalmazás-katalógusban már felsorolt alkalmazások felhasználó általi üzembe helyezésének konfigurálásakor felmerülő gyakori problémák elhárítása
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 152b704ca597fb473a820124ee6147d6d9bc7845
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381480"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Probléma a rendszergazdai hitelesítő adatok mentésekor, miközben a felhasználók üzembe helyezését egy Azure Active Directory Gallery-alkalmazáshoz konfigurálja 

Ha a Azure Portal használatával konfigurálja az [automatikus felhasználó-üzembe](user-provisioning.md) helyezést egy vállalati alkalmazáshoz, előfordulhat, hogy a következő helyzetekben találkozhat:

* Az alkalmazáshoz megadott **rendszergazdai hitelesítő adatok** érvényesek, és a **tesztelési kapcsolatok** gomb is működik. A hitelesítő adatokat azonban nem lehet menteni, és a Azure Portal általános hibaüzenetet ad vissza.

Ha az SAML-alapú egyszeri bejelentkezés ugyanahhoz az alkalmazáshoz is konfigurálva van, a hiba legvalószínűbb oka az, hogy az Azure AD belső, a tanúsítványokra és a hitelesítő adatokra vonatkozó tárolási korlátja túllépve.

Az Azure AD jelenleg legfeljebb 1024 bájtos tárolókapacitást biztosít minden tanúsítványhoz, titkos jogkivonathoz, hitelesítő adathoz, valamint az alkalmazás egy példányához társított konfigurációs adatokhoz (más néven az Azure AD-beli egyszerű szolgáltatásnév).

Ha az SAML-alapú egyszeri bejelentkezés konfigurálva van, az SAML-tokenek aláírásához használt tanúsítvány itt tárolódik, és gyakran a terület 50%-át használja fel.

A felhasználók üzembe helyezésének beállítása során megadott titkos jogkivonatok, URI-k, értesítő e-mail-címek, felhasználónevek és jelszavak meghaladják a tárolási korlátot.

## <a name="how-to-work-around-this-issue"></a>A probléma megkerülése 

Ezt a problémát kétféleképpen lehet megkerülni ma:

1. **Használjon két katalógusbeli alkalmazás-példányt, egyet az egyszeri bejelentkezéshez és egyet a felhasználók üzembe** helyezéséhez – a Gallery Application [LinkedIn jogosultságszint](../saas-apps/linkedinelevate-tutorial.md) -emelési lehetőségének megadásával például megadhatja a LinkedIn-emelést a katalógusból, és konfigurálhatja az egyszeri bejelentkezéshez. A kiépítés érdekében vegye fel a LinkedIn újabb példányát az Azure AD-alkalmazás-katalógusból, és nevezze el "LinkedIn jogosultságszint (kiépítés)" értékre. Ennél a második példánynál konfigurálja [](../saas-apps/linkedinelevate-provisioning-tutorial.md)a kiépítés szolgáltatást, de ne egyszeri bejelentkezést. A megkerülő megoldás használatakor ugyanazokat a felhasználókat és csoportokat [](assign-user-or-group-access-portal.md) kell hozzárendelni mindkét alkalmazáshoz. 

2. **Csökkentse a tárolt konfigurációs adatok mennyiségét** – a létesítés lap [rendszergazdai hitelesítő](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) adatok szakaszában megadott összes adat ugyanabban a helyen TÁROLÓDIK, mint az SAML-tanúsítvány. Habár előfordulhat, hogy az összes adat hosszának csökkentése nem lehetséges, néhány opcionális konfigurációs mező, például az értesítési **e-mail** is eltávolítható.

## <a name="next-steps"></a>További lépések
[A felhasználók üzembe helyezésének és megszüntetésének beállítása SaaS-alkalmazásokhoz](user-provisioning.md)
