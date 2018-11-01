---
title: A helyszíni Azure AD jelszó védelmi ügynök verziókiadásai
description: Dokumentumok verzió kiadását és viselkedésének módosítási előzmények
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: c52c84a1311c30c19356bb8a1287b203faf476fc
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50743260"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>Előzetes verzió: Azure AD jelszó védelmi ügynök korábbi verziók

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="12100"></a>1.2.10.0

Kiadás dátuma: 2018/8/17 /.

Javításokat tartalmaz:

* Register-AzureADPasswordProtectionProxy és a Register-AzureADPasswordProtectionForest mostantól támogatja a többtényezős hitelesítés
* Register-AzureADPasswordProtectionProxy WS2012 vagy újabb tartományvezérlő a tartományban titkosítási hibák elkerülése érdekében igényel.
* DC-ügynökszolgáltatás megbízhatóbb kapcsolatos új jelszóházirend az Azure-ból az indításakor.
* DC-ügynökszolgáltatás kérelmezi egy új jelszóházirend az Azure-ból minden órában, ha szükséges, de fog most ehhez egy véletlenszerűen kiválasztott kezdési időpontban.
* DC-ügynök szolgáltatás már nem az új tartományvezérlő hirdetményt replikaként az előléptetés előtt egy kiszolgálón telepítve egy befejezésére való határozatlan idejű késedelem miatt.
* DC-ügynökszolgáltatás most tartsa tiszteletben a "A Windows Server Active Directory jelszavas védelem engedélyezése" konfigurációs beállítás
* Mindkét DC-ügynök és a proxy telepítők mostantól támogatja a helyben frissítés verzióra való későbbi verziókban.

> [!WARNING]
> 1.1.10.3 verzióból a helybeni frissítést nem támogatott, és a egy telepítési hibát eredményez. A frissítési verzióra 1.2.10 vagy újabb, kell először teljesen távolítsa el a tartományvezérlő-ügynök és a proxy szolgáltatás szoftvert, majd teljesen új az új verzió telepítése. Az Azure AD jelszóvédelem proxyszolgáltatás Újraregisztrálás megadása kötelező.  Regisztrálja újra az erdő nem szükséges.

> [!NOTE]
> Helyben végzett frissítések DC ügynökszoftver követően újra kell indítani.

* DC-ügynök és a proxy szolgáltatás mostantól támogatja a csak a FIPS előírásainak megfelelő algoritmusok használatára konfigurált kiszolgálón futó.
* A továbbfejlesztett naplózás
* Kisebb teljesítményt és háttértárat javításai

## <a name="11103"></a>1.1.10.3

Kiadás dátuma: 2018/6/15 /.

Kezdeti nyilvános előzetes kiadás

## <a name="next-steps"></a>További lépések

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
