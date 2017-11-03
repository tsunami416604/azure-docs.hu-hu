---
title: "Az Azure AD az önkiszolgáló jelszó-átállítási áttekintése |} Microsoft Docs"
description: "A szervezet milyen is az Azure AD önkiszolgáló jelszó alaphelyzetbe állítása do?"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
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
ms.openlocfilehash: f577ee95c34f9bc3065c026bc943d30622b9d658
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>Az Azure AD az önkiszolgáló jelszó-változtatási az informatikai szakemberek számára

Az Azure Active Directory (Azure AD) önkiszolgáló jelszó-változtatási (SSPR) lehetővé teszi, hogy a felhasználók visszaállíthassák a jelszavukat a saját, amikor és ahol szükséges, miközben lehetővé teszi a rendszergazdák hogyan lekérdezi a jelszó alaphelyzetbe állítása irányítását. Felhasználók hívja a segélyszolgálatot csak a jelszó visszaállítása nem szükséges.

* **Az önkiszolgáló jelszóváltoztatáshoz** – felhasználó tudja a jelszót, de új módosítani szeretné.
* **Az önkiszolgáló jelszó-átállítási** – felhasználó nem tud bejelentkezni, és jelszavuk egy vagy több következő érvényesített hitelesítési módszer használatával szeretné.
   * Szöveges üzenet mobiltelefonra érvényesített
   * Telefonhívás érvényesített mobile- vagy office telefonon
   * E-mailben egy érvényesített másodlagos e-mail fiókba
   * A biztonsági kérdésekre adott válaszok
* **Önkiszolgáló fiókfeloldási** – felhasználó nem tudja jelentkezzen be a jelszavát zárolva van, és feloldhatja a fiók zárolását a hitelesítési módszerek használatát rendszergazdai beavatkozás nélkül szeretné.

## <a name="why-choose-azure-ad-self-service-password-reset"></a>Miért válassza az Azure AD az önkiszolgáló jelszóváltoztatás

* **Csökkentheti a költségeket** segélyszolgálat, személyes jelszó alaphelyzetbe állítása általában egy informatikai szervezet támogatási hívások 20 %-át. 
* **Végfelhasználói élmény javítása** és **segélyszolgálat csökkenthető** azzal, hogy a végfelhasználók a saját jelszó problémák megoldásához egyszerre egy támogatási kérést megnyitásakor vagy a segélyszolgálat hívása nélkül power.
* **A meghajtó mobilitási** , a felhasználók alaphelyzetbe állíthatja a jelszavukat a bárhol vannak.
* **Irányítást tarthat fenn** biztonsági házirend. Rendszergazdák továbbra is a ma rendelkeznek házirendeknek az érvényesítését.

Ha készen áll, Ismerkedés az Azure AD SSPR használatával a [gyors üzembe helyezési útmutató](active-directory-passwords-getting-started.md) , és gyorsan a felhasználók a saját jelszavak alaphelyzetbe állítását.

## <a name="azure-ad-self-service-password-reset-availability"></a>Az Azure AD az önkiszolgáló jelszó elérhetőségének alapállapotba állítása

Az Azure AD az önkiszolgáló jelszó alaphelyzetbe állítása a három réteg attól függően, hogy az előfizetés érhető el.

* **Az Azure AD ingyenes** – csak felhőalapú rendszergazdák alaphelyzetbe állíthatja a saját jelszavukat.
* **Az Azure AD alapszintű** vagy bármely **fizetős Office 365-előfizetéssel** – csak felhőalapú felhasználók visszaállíthatják a saját jelszavukat.
* **Prémium szintű Azure AD** – összevont bármely felhasználó vagy a rendszergazda, csak felhőalapú, beleértve, vagy a szinkronizált felhasználók és az új jelszó is. A helyszíni jelszavak a jelszóvisszaírás engedélyezése szükséges.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Az Azure AD díjszabás, szolgáltatásiszint-szerződés, frissítések és terv

Részletes információkat olvashat licencelés, a díjszabás és a jövőbeli tervek megtalálhatók a helyeket, hajtsa végre.

* [**Az Azure AD díjszabása**](https://azure.microsoft.com/pricing/details/active-directory/)
* [**Az Office 365 díjszabása**](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [**Az Azure szolgáltatásiszint-szerződések**](https://azure.microsoft.com/support/legal/sla/)
* [**A Microsoft Online Services szolgáltatásiszint-szerződés**](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [**Azure-hírt**](https://azure.microsoft.com/updates/)
* [**Azure menetrend**](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>Következő lépések

* Készen áll az önkiszolgáló jelszó-Változtatási első lépései? [Az Azure AD telepítő önkiszolgáló jelszóváltoztatás szolgáltatás](active-directory-passwords-getting-started.md).
* A felhasználók számára a készlet útmutatásának található sikeres SSPR központi telepítésének megtervezése a [ **bevezetési útmutató**](active-directory-passwords-best-practices.md).
* [A jelszó megváltoztatására](active-directory-passwords-update-your-own-password.md).
* [Az önkiszolgáló jelszó-változtatási regisztrációs](active-directory-passwords-reset-register.md).