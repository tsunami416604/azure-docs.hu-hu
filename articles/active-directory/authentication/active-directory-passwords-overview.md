---
title: Az Azure AD önkiszolgáló jelszó alaphelyzetbe állítása – áttekintés |} A Microsoft Docs
description: Milyen is az Azure AD önkiszolgáló jelszó-visszaállítás tegye a szervezet számára?
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e28918fe9e26221738fe234ad41923c58a6ac260
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049021"
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Az Azure AD önkiszolgáló jelszó-visszaállítás informatikai szakemberek számára.

Az Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR), a felhasználók is jelszavaikat a saját akkor, ha és helyezhetik. Egy időben a rendszergazdák szabályozhatják, hogyan lekérdezi a jelszó alaphelyzetbe állítása. Felhasználók többé nem kell hívja a segélyszolgálatot csak, hogy új jelszót kérnek. Az Azure AD SSPR tartalmazza:

* **Önkiszolgáló jelszómódosítás**: A felhasználó ismeri a jelszavát, de szeretne váltani valami újat kínál.
* **Az önkiszolgáló jelszó-visszaállítási**: A felhasználó nem tud bejelentkezni, és szeretné az új jelszót kérnek az alábbi ellenőrzött hitelesítési módszerek valamelyikének használatával:
   * Szöveges üzenet küldése egy ellenőrzött mobiltelefonra.
   * Telefonhívás ellenőrzött mobile vagy az office-telefonjára.
   * E-mail küldése egy hitelesített másodlagos e-mail-fiókjába.
   * Ahol az megválaszolhatja biztonsági kérdéseit.
* **Önkiszolgáló fiókfeloldási**: A felhasználó nem tud bejelentkezni a jelszavát, és zárolva van. A felhasználó a saját fiók rendszergazdai beavatkozás nélkül feloldása a hitelesítési módszereket használatával szeretne.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

## <a name="why-choose-azure-ad-sspr"></a>Miért érdemes választani az Azure AD SSPR

Az Azure AD SSPR segítséget:

* **Csökkentheti a költségeket** súgó, ügyfélszolgálati személyes jelszóbeállítások általában egy informatikai szervezet támogatási hívások 20 %-fiókot. 
* **Végfelhasználói élmény javítása** és **minimalizálható a help desk** azzal, hogy a végfelhasználók a teljesítmény, a saját jelszavát problémák megoldásához. Hiba esetén nem kell hívja a segélyszolgálatot, vagy hozzon létre egy támogatási kérést.
* **Mobilitási meghajtó** , a felhasználók bárhonnan átállíthatják a jelszavukat alaphelyzetbe állíthatja.
* **Feletti** a biztonsági házirend. A rendszergazdák továbbra is a mai rendelkeznek a házirendeknek az érvényesítését.

Ha készen áll, akkor is használatának első lépései az Azure AD SSPR a [gyors üzembe helyezési útmutató](quickstart-sspr.md). Segítségével gyorsan biztosíthat a felhasználók alaphelyzetbe állíthatja saját jelszavait.

## <a name="azure-ad-sspr-availability"></a>Azure AD SSPR rendelkezésre állása

Az Azure AD SSPR-előfizetésétől függően három csomagban érhető el:

* **Az Azure AD ingyenes**: csak felhőalapú rendszergazdák állíthatják alaphelyzetbe a saját jelszavát.
* **Az Azure AD alapszintű** vagy bármely **Office 365-előfizetéssel**: csak felhőalapú felhasználói is alaphelyzetbe állíthatja saját jelszavait.
* **Az Azure AD Premium**: bármely felhasználó vagy rendszergazda, beleértve a csak felhőalapú, összevont, átmenő hitelesítés vagy a jelszó kivonatoló szinkronizált felhasználók, alaphelyzetbe állíthatja saját jelszavait. A helyszíni jelszavak engedélyezni kell a jelszóvisszaírás szükséges.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Az Azure AD-díjszabás, szolgáltatásiszint-szerződés, frissítések és ütemterv

Licencelés, a díjszabást és a jövőbeli tervek kapcsolatban bővebben a következő helyeken találhatók:

* [Az Azure AD szolgáltatás díjszabását.](https://azure.microsoft.com/pricing/details/active-directory/)
* [Az Office 365-díjszabás](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Az Azure szolgáltatásiszint-szerződések](https://azure.microsoft.com/support/legal/sla/)
* [A Microsoft Online Services szolgáltatásiszint-szerződés](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Azure-frissítések](https://azure.microsoft.com/updates/)
* [Azure-ütemterv](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>További lépések

* Készen áll az SSPR használatának első lépései? [Állítsa be az Azure AD önkiszolgáló jelszó-visszaállítási](quickstart-sspr.md).
* A található útmutató segítségével a felhasználók számára a sikeres SSPR-telepítés megtervezése a [bevezetési útmutató](howto-sspr-deployment.md).
* [Új jelszó kérése vagy jelszó módosítása](../user-help/active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](../user-help/active-directory-passwords-reset-register.md).
