---
title: A helyszíni Azure AD jelszó védelmi ügynök verziókiadásai
description: Dokumentumok verzió kiadását és viselkedésének módosítási előzmények
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 89d64a28d2fe43464995e434c9f3807047b29492
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913636"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>Előzetes verzió: Azure AD jelszó védelmi ügynök korábbi verziók

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="12250"></a>1.2.25.0

Kiadás dátuma: 2018/11/01 /.

Javításokat tartalmaz:

* DC-ügynök és a proxy szolgáltatás már nem kell sikertelen a tanúsítvány megbízhatósági hibák miatt.
* DC-ügynök és a proxy szolgáltatás rendelkezik a FIPS előírásainak megfelelő gépek további javításokra.
* Proxy szolgáltatás mostantól fog megfelelően működni a TLS 1.2-es csak hálózati környezetben.
* Kisebb teljesítményt és háttértárat javításai
* A továbbfejlesztett naplózás

Változások:

* A minimálisan szükséges operációs rendszer szintjén a Proxy szolgáltatás most már a Windows Server 2012 R2. A tartományvezérlő-ügynök szolgáltatás minimálisan szükséges operációs rendszer szintjén, a Windows Server 2012 marad.
* A jelszó adatérvényesítési algoritmust egy kibontott karakter normalizálási táblát használja. Emiatt előfordulhat, hogy a korábbi verziók elfogadott el lettek utasítva, jelszavakat.

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
* Kisebb teljesítményt és háttértárat javításai
* A továbbfejlesztett naplózás

## <a name="11103"></a>1.1.10.3

Kiadás dátuma: 2018/6/15 /.

Kezdeti nyilvános előzetes kiadás

## <a name="next-steps"></a>További lépések

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
