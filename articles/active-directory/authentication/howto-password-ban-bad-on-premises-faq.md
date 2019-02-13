---
title: A helyszíni Azure AD-jelszó Protection – gyakori kérdések
description: A helyszíni Azure AD-jelszó Protection – gyakori kérdések
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a4528cde92c5f738fa3fa7f4a649d84b1e79431
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175948"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Előzetes verzió: Az Azure AD jelszóvédelem helyszíni – gyakori kérdések

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Általános kérdések

**K: Amikor eléri az Azure AD jelszóvédelem általánosan elérhető (GA)?**

Általánosan elérhető (előtt március 2019 vége) Q1 CY2019 tervezünk. Köszönjük, hogy mindenki számára, aki a szolgáltatást, hogy a dátum - visszajelzés mellékelt Köszönjük azt!

**K: Az a helyszíni Azure AD-jelszó védelmet támogatja a nem nyilvános felhők?**

A helyszíni Azure AD jelszóvédelem nem – csak akkor támogatott a nyilvános felhőben.

**K: Hogyan használhatom az Azure AD jelszóvédelem előnyeit a helyszíni felhasználók egy alhalmazára?**

Nem támogatott. Miután üzembe helyezte, és engedélyezve van, nem tesz különbséget az Azure AD jelszóvédelem – az összes felhasználó megkapja egyenlő biztonsági funkcióit.

**K: Támogatja az Azure AD-jelszó Protection tartalomcsomagjai egyéb jelszó-szűrő-alapú termékek telepítése?**

Igen. Több regisztrált jelszó szűrő DLL-ek egy olyan alapvető Windows szolgáltatás támogatása és a nem kifejezetten az Azure AD jelszóvédelem. Az összes regisztrált jelszó szűrő DLL-ek jelszó el kell fogadnia.

**K: Elosztott fájlrendszer-replikációs miért van szükség a sysvol replikáció?**

A fájlreplikációs szolgáltatás (az elosztott fájlrendszer-replikációs megelőző technológia) számos ismert problémák merülnek fel, és nem teljes mértékben támogatott a Windows Server Active Directory újabb verzióiban. A fájlreplikációs szolgáltatás használatára konfigurált tartományok nulla tesztelése az Azure AD jelszóvédelem történik.

További információkért tekintse meg a következő cikkeket:

[Az elosztott fájlrendszer-replikációs történő áttelepítés sysvol-replikáció eset](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[A végpont az FRS Nigh](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**K: Miért van egy újraindítás szükséges, a tartományvezérlő ügynök szoftver telepítése vagy frissítése**

Ez a követelmény alapvető Windows viselkedés okozza.

**K: Van mód konfigurálása a tartományvezérlő-ügynök egy konkrét proxykiszolgálóval?**

Nem.

## <a name="next-steps"></a>További lépések

Ha kérdése van egy helyszíni Azure AD jelszóvédelem, amely itt nem válaszolt, küldje el az alábbi – Köszönjük, hogy egy visszajelzéshez!

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
