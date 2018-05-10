---
title: Az Azure AD az önkiszolgáló jelszó-átállítási áttekintése |} Microsoft Docs
description: A szervezet milyen is az Azure AD önkiszolgáló jelszó alaphelyzetbe állítása do?
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 77d26d860462b1a82315bf1385bccf62047e3da3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
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

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

## <a name="why-choose-azure-ad-sspr"></a>Miért válassza az Azure AD SSPR

Az Azure AD SSPR nyújt segítséget:

* **Csökkentheti a költségeket** súgó, ügyfélszolgálati támogatott jelszó alaphelyzetbe általában egy informatikai szervezet támogatási hívások 20 %-át. 
* **Végfelhasználói élmény javítása** és **csökkenti az ügyfélszolgálati leterheltségét** azzal, hogy a végfelhasználók a saját jelszavakkal kapcsolatos problémák megoldásához a teljesítmény. Nincs szükség az hívja a segélyszolgálatot vagy támogatási kérést nyithat.
* **A meghajtó mobilitási** , a felhasználók alaphelyzetbe állíthatja a jelszavukat a bárhol vannak.
* **Irányítást tarthat fenn** a biztonsági házirend. Rendszergazdák továbbra is a ma rendelkeznek házirendeknek az érvényesítését.

Ha készen áll, Ismerkedés az Azure AD SSPR használatával a [gyors üzembe helyezési útmutató](quickstart-sspr.md). A felhasználók gyorsan hozhat létre új jelszót készíthessenek maguknak biztosíthat.

## <a name="azure-ad-sspr-availability"></a>Az Azure AD SSPR rendelkezésre állása

Az Azure AD SSPR attól függően, hogy az előfizetés három réteg érhető el:

* **Az Azure AD ingyenes**: csak felhőalapú rendszergazdák alaphelyzetbe állíthatja a saját jelszavukat.
* **Az Azure AD alapszintű** vagy bármely **Office 365-előfizetéssel**: csak felhőalapú felhasználók visszaállíthatják a saját jelszavukat.
* **Prémium szintű Azure AD**: bármely felhasználó vagy a rendszergazda, csak felhőalapú, összevont, átmenő hitelesítés vagy a jelszó szinkronizálva kivonatoló felhasználók, például alaphelyzetbe állíthatja a saját jelszavukat. A helyszíni jelszavak a jelszóvisszaírás engedélyezése szükséges.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Az Azure AD díjszabás, szolgáltatásiszint-szerződés, frissítések és terv

További információt a licenceléssel, a díjszabás és a jövőbeli tervek a következő helyen található:

* [Az Azure AD díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
* [Az Office 365 díjszabása](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Az Azure szolgáltatásiszint-szerződések](https://azure.microsoft.com/support/legal/sla/)
* [A Microsoft Online Services szolgáltatásiszint-szerződés](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Azure-hírt](https://azure.microsoft.com/updates/)
* [Azure-ütemterv](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>További lépések

* Készen áll az önkiszolgáló jelszó-Változtatási első lépései? [Állítsa be az Azure AD az önkiszolgáló jelszó alaphelyzetbe állítása](quickstart-sspr.md).
* Az útmutatóban található segítségével a felhasználók sikeres SSPR központi telepítésének megtervezése a [bevezetési útmutató](howto-sspr-deployment.md).
* [Új jelszó kérése vagy jelszó módosítása](../active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](../active-directory-passwords-reset-register.md).
