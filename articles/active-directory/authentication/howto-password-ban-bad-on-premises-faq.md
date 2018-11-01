---
title: A helyszíni Azure AD jelszóvédelem – gyakori kérdések
description: A helyszíni Azure AD jelszóvédelem – gyakori kérdések
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 59c89c81f618876de48de66a38e1063eb658fba4
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50743295"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Előzetes verzió: Az Azure AD jelszó védelmi helyszíni – gyakori kérdések

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Általános kérdések

**K: mikor eléri az Azure AD jelszóvédelem általánosan elérhető (GA)?**

Bejelentettünk egy GA dátuma még nem.

**K: van a helyszíni Azure AD jelszóvédelem támogatja a nem nyilvános felhők között?**

A helyszíni Azure AD jelszóvédelem nincs – csak a nyilvános felhőben támogatott.

**K: hogyan alkalmazhatók az Azure AD jelszó a hálózatvédelem előnyei a helyszíni felhasználók egy alhalmazára?**

Nem támogatott. Miután üzembe helyezte, és engedélyezve van, nem tesz különbséget az Azure AD jelszóvédelem – az összes felhasználó megkapja egyenlő biztonsági funkcióit.

**K: támogatott-e az Azure AD jelszóvédelem tartalomcsomagjai egyéb jelszó-szűrő-alapú termékek telepítése?**

Igen. Több regisztrált jelszó szűrő DLL-ek egy olyan alapvető Windows szolgáltatás támogatása és a nem kifejezetten az Azure AD jelszóvédelem. Az összes regisztrált jelszó szűrő DLL-ek jelszó el kell fogadnia.

**K: Miért szükséges a sysvol replikáció elosztott fájlrendszer replikációs szolgáltatása?**

A fájlreplikációs szolgáltatás (az elosztott fájlrendszer-replikációs megelőző technológia) számos ismert problémák merülnek fel, és nem teljes mértékben támogatott a Windows Server Active Directory újabb verzióiban. A fájlreplikációs szolgáltatás használatára konfigurált tartományok nulla tesztelését, az Azure AD jelszóvédelem történik.

További információkért tekintse meg a következő cikkeket:

[Az elosztott fájlrendszer-replikációs történő áttelepítés sysvol-replikáció eset](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[A végpont Nigh DRS-hez](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**K: Miért egy újraindítás szükséges, a tartományvezérlő ügynök szoftver telepítése vagy frissítése**

Ez a követelmény alapvető Windows viselkedés okozza.

**K: van, hogy egy adott proxykiszolgáló használata a DC-ügynök konfigurálása?**

Nem.

## <a name="next-steps"></a>További lépések

Ha kérdése van egy helyszíni Azure AD jelszó védelmet, amely itt nem válaszolt, küldje el az alábbi – Köszönjük, hogy egy visszajelzéshez!

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
