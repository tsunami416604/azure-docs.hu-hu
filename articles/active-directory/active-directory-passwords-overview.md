---
title: "Az Azure AD az önkiszolgáló jelszó-átállítási áttekintése |} Microsoft Docs"
description: "A szervezet milyen is az Azure AD önkiszolgáló jelszó alaphelyzetbe állítása do?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 433ee5b6a1d6897dd911dcfc5e95c7dd1d977cd5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Az Azure AD az önkiszolgáló jelszó-változtatási az informatikai szakemberek számára

Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítási (SSPR), a felhasználók alaphelyzetbe állíthatja a jelszavukat a saját amikor és ahol szükséges. Egy időben a rendszergazdák szabályozhatják, hogyan lekérdezi a jelszó alaphelyzetbe állítása. Felhasználók hívja a segélyszolgálatot csak a jelszó visszaállítása nem szükséges. Az Azure AD SSPR tartalmazza:

* **Az önkiszolgáló jelszóváltoztatáshoz**: A felhasználó ismeri a jelszavát, de új módosítani szeretné.
* **Az önkiszolgáló jelszó-átállítási**: A felhasználó nem tud bejelentkezni, és jelszavuk egy vagy több következő érvényesített hitelesítési módszer használatával szeretné:
   * Szöveges üzenet küldése egy érvényesített mobiltelefon.
   * Telefonhívás érvényesített mobile- vagy office telefonjára.
   * E-mailt küldeni egy érvényesített másodlagos e-mail fiókot.
   * A biztonsági kérdések megválaszolása.
* **Önkiszolgáló fiókfeloldási**: A felhasználó nem tud bejelentkezni a jelszavát, és zárolva van. A felhasználó a rendszergazda beavatkozása nélkül a fiók zárolását kívánja feloldani a hitelesítési módszerek használatával szeretne.

## <a name="why-choose-azure-ad-sspr"></a>Miért válassza az Azure AD SSPR

Az Azure AD SSPR nyújt segítséget:

* **Csökkentheti a költségeket** súgó, ügyfélszolgálati támogatott jelszó alaphelyzetbe általában egy informatikai szervezet támogatási hívások 20 %-át. 
* **Végfelhasználói élmény javítása** és **csökkenti az ügyfélszolgálati leterheltségét** azzal, hogy a végfelhasználók a saját jelszavakkal kapcsolatos problémák megoldásához a teljesítmény. Nincs szükség az hívja a segélyszolgálatot vagy támogatási kérést nyithat.
* **A meghajtó mobilitási** , a felhasználók alaphelyzetbe állíthatja a jelszavukat a bárhol vannak.
* **Irányítást tarthat fenn** a biztonsági házirend. Rendszergazdák továbbra is a ma rendelkeznek házirendeknek az érvényesítését.

Ha készen áll, Ismerkedés az Azure AD SSPR használatával a [gyors üzembe helyezési útmutató](active-directory-passwords-getting-started.md). A felhasználók gyorsan hozhat létre új jelszót készíthessenek maguknak biztosíthat.

## <a name="azure-ad-sspr-availability"></a>Az Azure AD SSPR rendelkezésre állása

Az Azure AD SSPR attól függően, hogy az előfizetés három réteg érhető el:

* **Az Azure AD ingyenes**: csak felhőalapú rendszergazdák alaphelyzetbe állíthatja a saját jelszavukat.
* **Az Azure AD alapszintű** vagy bármely **Office 365-előfizetéssel**: csak felhőalapú felhasználók visszaállíthatják a saját jelszavukat.
* **Prémium szintű Azure AD**: bármely felhasználó vagy a rendszergazda, csak felhőalapú, beleértve összevont, vagy a szinkronizált felhasználók és az új jelszó is. A helyszíni jelszavak a jelszóvisszaírás engedélyezése szükséges.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Az Azure AD díjszabás, szolgáltatásiszint-szerződés, frissítések és terv

További információt a licenceléssel, a díjszabás és a jövőbeli tervek a következő helyen található:

* [Az Azure AD díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
* [Az Office 365 díjszabása](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Az Azure szolgáltatásiszint-szerződések](https://azure.microsoft.com/support/legal/sla/)
* [A Microsoft Online Services szolgáltatásiszint-szerződés](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Azure-hírt](https://azure.microsoft.com/updates/)
* [Azure-ütemterv](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Következő lépések

* Készen áll az önkiszolgáló jelszó-Változtatási első lépései? [Állítsa be az Azure AD az önkiszolgáló jelszó alaphelyzetbe állítása](active-directory-passwords-getting-started.md).
* Az útmutatóban található segítségével a felhasználók sikeres SSPR központi telepítésének megtervezése a [bevezetési útmutató](active-directory-passwords-best-practices.md).
* [Új jelszó kérése vagy jelszó módosítása](active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](active-directory-passwords-reset-register.md).
