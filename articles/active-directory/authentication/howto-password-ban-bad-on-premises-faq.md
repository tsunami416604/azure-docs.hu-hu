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
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 9046090a45d52a283d28d1c4cc79b44503cb5a9d
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427593"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Előzetes verzió: Azure AD jelszó védelmi helyszíni – gyakori kérdések

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Általános kérdések

**K: Amikor eléri az Azure AD jelszóvédelem általánosan elérhető (GA)?**

Bejelentettünk egy GA dátuma még nem.

**K: Az a helyszíni Azure AD jelszóvédelem nem nyilvános felhőkben támogatott?**

A helyszíni Azure AD jelszóvédelem nincs – csak a nyilvános felhőben támogatott.

**K: Hogyan használhatom az Azure AD jelszó a hálózatvédelem előnyei a helyszíni felhasználók egy alhalmazára?**

Nem támogatott. Miután üzembe helyezte, és engedélyezve van, nem tesz különbséget az Azure AD jelszóvédelem – az összes felhasználó megkapja egyenlő biztonsági funkcióit.

**K: Támogatja az Azure AD jelszóvédelem tartalomcsomagjai egyéb jelszó-szűrő-alapú termékek telepítése?**

Igen. Több regisztrált jelszó szűrő DLL-ek egy olyan alapvető Windows szolgáltatás támogatása és a nem kifejezetten az Azure AD jelszóvédelem. Az összes regisztrált jelszó szűrő DLL-ek jelszó el kell fogadnia.

**K: Elosztott fájlrendszer-replikációs miért van szükség a sysvol replikáció?**

A fájlreplikációs szolgáltatás (az elosztott fájlrendszer-replikációs megelőző technológia) számos ismert problémák merülnek fel, és nem teljes mértékben támogatott a Windows Server Active Directory újabb verzióiban. A fájlreplikációs szolgáltatás használatára konfigurált tartományok nulla tesztelését, az Azure AD jelszóvédelem történik.

További információkért tekintse meg a következő cikkeket:

[Az elosztott fájlrendszer-replikációs történő áttelepítés sysvol-replikáció eset](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[A végpont az FRS Nigh](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**K: Miért van egy újraindítás szükséges, a tartományvezérlő ügynök szoftver telepítése vagy frissítése**

Ez a követelmény alapvető Windows viselkedés okozza.

**K: Van mód konfigurálása a tartományvezérlő-ügynök egy konkrét proxykiszolgálóval?**

Nem.

## <a name="next-steps"></a>További lépések

Ha kérdése van egy helyszíni Azure AD jelszó védelmet, amely itt nem válaszolt, küldje el az alábbi – Köszönjük, hogy egy visszajelzéshez!

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
