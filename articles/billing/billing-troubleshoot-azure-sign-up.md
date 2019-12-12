---
title: Az Azure-regisztráció hibaelhárítása
description: Az új fiók regisztrálásakor felmerülő problémák megoldása a Microsoft Azure Portal Fiókközpontjában.
services: billing
author: v-miegge
manager: dcscontentpm
editor: v-jesits
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: f1414f96a00626b8e47decd22e4560bff1394468
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872723"
---
# <a name="troubleshoot-azure-sign-up"></a>Az Azure-regisztráció hibaelhárítása

Előfordulhat, hogy problémába ütközik, amikor új fiókot próbál meg regisztrálni a Microsoft Azure Portalon vagy az Azure Fiókközpontban. A probléma elhárítása előtt ellenőrizze az alábbiakat:

- Az Azure-fiók profiljában megadott adatok (például a kapcsolattartási e-mail-cím, a postai cím és a telefonszám) helyesek.
- A hitelkártyaadatok helyesek.
- Ugyanezekkel az adatokkal még nem hozott létre Microsoft-fiókot.

## <a name="resolutions"></a>Megoldások

A probléma megoldásához válassza ki a problémát, amely az Azure-ba való regisztráció során jelentkezik.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Hiba: *A regisztrációt nem lehet folytatni a fiókkal kapcsolatos probléma miatt. Forduljon a számlázási ügyfélszolgálathoz.*

A probléma megoldásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Fiókközpontba](https://account.azure.com/Profile) a fiókadminisztrátori hitelesítő adatokkal.

2. Válassza a **Részletek szerkesztése** lehetőséget.

3. Ellenőrizze, hogy a címet tartalmazó mezők ki vannak-e töltve, és a megadottak helyesek-e.

4. Az Azure-előfizetésre való regisztráció során ellenőrizze, hogy a hitelkártya-regisztrációhoz tartozó számlázási cím megfelel-e a banki adatoknak.

Ha továbbra is megjelenik a hibaüzenet, próbáljon egy másik böngészőből regisztrálni.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>A *Személyazonosság ellenőrzése kártyával* szakaszban található folyamatjelző lefagy.

A személyazonosság kártyával történő ellenőrzésének elvégzéséhez engedélyeznie kell a harmadik féltől származó cookie-kat a böngészőben.

![Személyazonosság ellenőrzése kártyával](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
A böngésző cookie-beállításainak frissítéséhez hajtsa végre az alábbi lépéseket.

1. Ha Chrome-ot használ, válassza a **Beállítások** > **Speciális beállítások megjelenítése** > **Adatvédelem** > **Webhelybeállítások** lehetőséget. Kapcsolja ki a **Harmadik féltől származó cookie-k és webhelyadatok letiltása** beállítást.

2. Ha Microsoft Edge-et használ, válassza a **Beállítások** > **Speciális beállítások megnyitása** > **Cookie-k** > **Cookie-k engedélyezése** lehetőséget.

3. Frissítse az Azure regisztrációs lapját, és ellenőrizze, hogy megoldódott-e a probléma.

4. Ha a frissítés nem oldotta meg a problémát, zárja be, és indítsa újra a böngészőt, majd próbálkozzon újra.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>A hitelkártyaűrlap nem támogatja a számlázási címet

A számlázási címnek **Az Ön adatai** szakaszban kiválasztott országban kell lennie. Ügyeljen arra, hogy a helyes országot válassza ki.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Nem érkezett szöveges üzenet vagy hívás a regisztráció részét képező fiókellenőrzés során

Bár a folyamat általában gyors, az ellenőrző kód megérkezéséig akár négy perc is eltelhet. Az ellenőrzéshez megadott telefonszámot a rendszer nem tárolja a fiók kapcsolattartási telefonszámaként.

Néhány további tipp:

- A telefonos ellenőrzéshez nem használható VoIP-telefonszám.
- Ellenőrizze a megadott telefonszámot, beleértve a legördülő menüből kiválasztott országkódot.
- Ha a telefonja nem fogad szöveges üzeneteket (SMS-eket), próbálkozzon a **Hívást kérek** lehetőséggel.
- Ellenőrizze, hogy a telefonja fogadhat-e hívást vagy SMS-üzenetet egyesült államokbeli telefonszámról.

Ha megkapta a szöveges üzenetet vagy fogadta a telefonhívást, adja meg a kapott kódot a szövegmezőben.

### <a name="credit-card-declined-or-not-accepted"></a>A hitelkártya el lett utasítva, vagy nem elfogadott

A virtuális hitelkártyák vagy bankkártyák és a feltöltőkártyák nem használhatók fizetőeszközként Azure-előfizetésekhez. Azzal kapcsolatban, hogy mi okozhatja még a kártya elutasítását, tekintse meg a bank- vagy hitelkártya [Azure-ba történő regisztráció során való elutasítását](https://support.microsoft.com/help/4042960) ismertető részt.

### <a name="free-trial-is-not-available"></a>Nem érhető el az ingyenes próbaverzió

Használt már korábban Azure-előfizetést? Az Azure használati feltételeinek értelmében az ingyenes próbaverzió aktiválására csak az Azure új felhasználói jogosultak. Ha korábban már volt bármilyen típusú Azure-előfizetése, akkor nem aktiválhatja az ingyenes próbaverziót. Érdemes lehet regisztrálnia egy [használatalapú fizetéses előfizetésre](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Megjelent egy díjtétel az ingyenes próbafiókban

A regisztráció után előfordulhat, hogy megjelenik egy kis összegű azonosítási zárolás a hitelkártyához tartozó számláján. Ez az összeg 3–5 napon belül fel lesz oldva. Ha aggódik a költségek kezelése miatt, tekintse meg [a váratlan költségek elkerülését](billing-getting-started.md) bemutató témakört.

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Nem lehet aktiválni az olyan Azure-kedvezménycsomagokat, mint az MSDN, a BizSpark, a BizSpark Plus vagy az MPN

Ellenőrizze, hogy a megfelelő bejelentkezési hitelesítő adatokat használja-e. Ezután tekintse meg a kedvezményprogramot, és ellenőrizze, hogy jogosult-e erre.

- MSDN 
  - Az [MSDN-fiók lapján](https://msdn.microsoft.com/subscriptions/manage/default.aspx) ellenőrizze a jogosultsági állapotát.
  - Ha nem tudja ellenőrizni az állapotát, forduljon az [MSDN-előfizetések ügyfélszolgálati központjához](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft for Startups
  - Microsoft for Startups-jogosultsági állapotának ellenőrzéséhez jelentkezzen be a [Microsoft for Startups portáljára](https://startups.microsoft.com/#start-two).
  - Ha nem tudja ellenőrizni az állapotát, kérjen segítséget a [Microsoft for Startups fórumain](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN 
  - Jogosultsági állapota ellenőrzéséhez jelentkezzen be az [MPN portáljára](https://mspartner.microsoft.com/Pages/Locale.aspx). Ha rendelkezik a megfelelő [Cloud Platform-kompetenciákkal](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx), lehetséges, hogy további kedvezményekre is jogosult.
  - Ha nem tudja ellenőrizni az állapotát, forduljon az [MPN ügyfélszolgálatához](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Nem lehet aktiválni az új Azure in Open-előfizetést

Azure in Open-előfizetés létrehozásához rendelkeznie kell egy olyan érvényes, online szolgáltatás aktiválására szolgáló (OSA-) kulccsal, amelyhez legalább egy Azure in Open-jogkivonat hozzá van rendelve. Ha nem rendelkezik OSA-kulccsal, lépjen kapcsolatba a [Microsoft Pinpointban](https://pinpoint.microsoft.com/) felsorolt valamelyik Microsoft-partnerrel.

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Hiba: *Jelenleg nem jogosult Azure-előfizetésre*

A probléma megoldásához ellenőrizze, hogy az alábbiak teljesülnek-e:

- Az Azure-fiók profiljában megadott adatok (például a kapcsolattartási e-mail-cím, a postai cím és a telefonszám) helyesek.
- A hitelkártyaadatok helyesek.
- Ugyanezekkel az adatokkal még nem hozott létre Microsoft-fiókot.

### <a name="error-your-current-account-type-is-not-supported"></a>Hiba: *A jelenlegi fióktípus nem támogatott*

Ez a probléma akkor fordulhat elő, ha a fiók regisztrálva van egy olyan [nem felügyelt Azure AD-címtárban](../active-directory/users-groups-roles/directory-self-service-signup.md), amely nem a szervezet Azure AD-címtára. 

A probléma megoldásához regisztráljon Azure-fiókra egy másik fiók használatával, vagy vegye át a nem felügyelt AD-címtárat. További információkért tekintse meg a [nem felügyelt címtár az Azure Active Directoryban rendszergazdaként történő átvételét](../active-directory/users-groups-roles/domains-admin-takeover.md) ismertető cikket.
 
## <a name="additional-help-resources"></a>További támogatási forrásanyagok

Az Azure-számlázással és -előfizetésekkel kapcsolatos további hibaelhárítási cikkek

- [Elutasított bankkártya](billing-troubleshoot-declined-card.md)
- [Bejelentkezéssel kapcsolatos problémák az előfizetésben](billing-troubleshoot-sign-in-issue.md)
- [Nem található előfizetés](billing-no-subscriptions-found.md)
- [Vállalati költségek nézet letiltva](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Ha segítségre van szüksége, vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- [Az Azure-számlázás dokumentációja](index.md)
