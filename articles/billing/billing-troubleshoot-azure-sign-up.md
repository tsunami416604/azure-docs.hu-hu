---
title: Azure-regisztráció – problémamegoldás
description: Probléma megoldása egy új fiók regisztrálására tett kísérlet során a Microsoft Azure Portal Account Centerben.
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 57dc9c05778563bc0c5df1a8ac8895b51008b01f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657072"
---
# <a name="troubleshoot-azure-sign-up"></a>Azure-regisztráció – problémamegoldás

A Microsoft Azure Portal vagy az Azure Account Center új fiókjára való feliratkozáskor probléma merülhet fel. A probléma megoldása előtt először ellenőrizze a következőket:

- Az Azure-fiók profiljához megadott információk (beleértve a kapcsolattartási e-mail-címet, az utca címét és a telefonszámot) helyesek.
- A bankkártya adatai helyesek.
- Még nem rendelkezik ugyanazzal az információval Microsoft-fiók.

## <a name="resolutions"></a>Megoldások

A hibák elhárításához válassza ki azt a problémát, amely az Azure-ba való regisztráció során jelentkezik.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Hiba: *A fiókkal kapcsolatos probléma miatt nem folytatható a regisztráció. Kérjük, forduljon a számlázási támogatási szolgálathoz.*

A probléma megoldásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Account Center](https://account.azure.com/Profile) szolgáltatásba a fiók rendszergazdája hitelesítő adataival.

2. Válassza a **részletek szerkesztése**lehetőséget.

3. Győződjön meg arról, hogy az összes címtartomány elkészült és érvényes.

4. Az Azure-előfizetésre való feliratkozáskor ellenőrizze, hogy a hitelkártya-regisztráció számlázási címe megfelel-e a banki rekordoknak.

Ha továbbra is megkapja a hibaüzenetet, próbáljon meg egy másik böngésző használatával regisztrálni.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>Az állapotjelző sáv lefagy az *Identity ellenőrzés kártyán szakasz alapján* .

Az Identity-ellenőrzés kártyán való elvégzéséhez engedélyezni kell a harmadik féltől származó cookie-kat a böngészőjében.

![Személyazonosság ellenőrzése kártyával](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
A böngésző cookie-beállításainak frissítéséhez kövesse az alábbi lépéseket.

1. Ha a Chrome-ot használja, válassza a **Beállítások** > **Speciális beállítások** > megjelenítése**adatvédelmi** > **tartalom beállításai**lehetőséget. **A harmadik féltől származó cookie-k és a hely**adattípusának törlése.

2. Ha a Microsoft Edge-t használja, > válassza a**Speciális beállítások** > megtekintése**cookie** > -k**nem blokkolja a cookie-** kat lehetőséget.

3. Frissítse az Azure regisztrációs oldalát, és győződjön meg arról, hogy a probléma megoldódott-e.

4. Ha a frissítés nem oldotta meg a problémát, lépjen ki, és indítsa újra a böngészőt, majd próbálkozzon újra.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>A bankkártya űrlap nem támogatja a számlázási címem használatát

A számlázási címnek a **Névjegy** szakaszban kiválasztott országban kell lennie. Győződjön meg arról, hogy a megfelelő országot választotta.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Nincs szöveges üzenet vagy hívás a regisztrációs fiók ellenőrzése során

Bár a folyamat általában gyors, akár négy percet is igénybe vehet, hogy egy ellenőrző kód kézbesítése megtörténjen. Az ellenőrzéshez megadott telefonszám nem a fiókhoz tartozó kapcsolattartói számként van tárolva.

Íme néhány további Tipp:

- A telefonos ellenőrzési folyamathoz nem használható a hangalapú IP-cím (VoiP) telefonszáma.
- Ellenőrizze a beírt telefonszámot, beleértve a legördülő menüben kiválasztott országkódot is.
- Ha a telefon nem kap szöveges üzeneteket (SMS), próbálkozzon a **hívás** lehetőséggel.
- Győződjön meg arról, hogy a telefonja fogadhat-e hívásokat vagy SMS-üzeneteket Egyesült Államok-alapú telefonszámról.

Amikor megjelenik a szöveges üzenet vagy telefonhívás, adja meg a szövegmezőben megjelenő kódot.

### <a name="credit-card-declined-or-not-accepted"></a>A bankkártya visszautasítva vagy nem fogadta el

Az Azure-előfizetések esetében nem engedélyezett a virtuális vagy előre fizetett kredit vagy bankkártyás fizetés. Ha szeretné megtekinteni, hogy mi okozhatja a kártya elutasítását, tekintse meg a bankkártyáját vagy a hitelkártyát az [Azure-regisztráció](https://support.microsoft.com/help/4042960)elutasításakor.

### <a name="free-trial-is-not-available"></a>Az ingyenes próbaverzió nem érhető el

Korábban már használta az Azure-előfizetést? Az Azure használati feltételeinek értelmében az ingyenes próbaverzió aktiválására csak az Azure új felhasználói jogosultak. Ha korábban már volt bármilyen típusú Azure-előfizetése, akkor nem aktiválhatja az ingyenes próbaverziót. Az utólagos elszámolású [](https://azure.microsoft.com/offers/ms-azr-0003p/)előfizetéshez érdemes regisztrálni.

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Az ingyenes próbaverziós fiók díját is megláttam

A regisztrációt követően előfordulhat, hogy a hitelkártya-fiókban egy kis ellenőrzés is látható. Ez három – öt nap múlva törlődik. Ha aggódik a költségek kezelésével kapcsolatban, olvassa el a nem [várt költségek megelőzésével](billing-getting-started.md)kapcsolatos további tudnivalókat.

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Nem aktiválható az Azure Benefit csomag, például az MSDN, a BizSpark, a BizSparkPlus vagy az MPN

Győződjön meg arról, hogy a megfelelő bejelentkezési hitelesítő adatokat használja. Ezután ellenőrizze a juttatási programot, és győződjön meg arról, hogy jogosult.

- MSDN 
  - Ellenőrizze a jogosultsági státuszát az [MSDN-fiók oldalán](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Ha nem tudja ellenőrizni az állapotát, forduljon az [MSDN](https://msdn.microsoft.com/library/aa493452.aspx)-előfizetések ügyfél-szolgáltatási központhoz.
- Microsoft Startupoknak
  - Jelentkezzen be a [Microsoft for Startups portálra](https://startups.microsoft.com/#start-two) , és ellenőrizze a Microsoft for Startups jogosultsági állapotát.
  - Ha nem tudja ellenőrizni az állapotát, segítséget kaphat a [Microsoft for Startups fórumokhoz](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN 
  - Jelentkezzen be az [MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) -portálra a jogosultsági állapot ellenőrzéséhez. Ha rendelkezik a megfelelő [felhőalapú platform](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)-kompetenciákkal, akkor jogosult lehet a további előnyökre.
  - Ha nem tudja ellenőrizni az állapotát, forduljon az [MPN támogatási szolgálatához](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Az új Azure nem aktiválható nyílt előfizetésben

Ha az Azure-t nyitott előfizetésben szeretné létrehozni, rendelkeznie kell egy érvényes online szolgáltatás-aktiválási (OSA) kulccsal, amely legalább egy Azure-beli nyitott tokenben van társítva. Ha nem rendelkezik OSA-kulccsal, lépjen kapcsolatba a [Microsoft Pinpointban](http://pinpoint.microsoft.com/)felsorolt Microsoft-partnerek egyikével.

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Hiba: *Nem jogosult Azure-előfizetésre*

A probléma megoldásához ellenőrizze, hogy a következő elemek teljesülnek-e:

- Az Azure-fiók profiljához megadott információk (beleértve a kapcsolattartási e-mail-címet, az utca címét és a telefonszámot) helyesek.
- A bankkártya adatai helyesek.
- Még nincs olyan Microsoft-fiók, amely ugyanazt az információt használja.

### <a name="error-your-current-account-type-is-not-supported"></a>Hiba: *Az aktuális fiók típusa nem támogatott*

Ez a probléma akkor fordulhat elő, ha a fiók regisztrálva van egy nem [felügyelt Azure ad](../active-directory/users-groups-roles/directory-self-service-signup.md)-címtárban, és nem szerepel a szervezet Azure ad-címtárában. 

A probléma megoldásához jelentkezzen be az Azure-fiókba egy másik fiók használatával, vagy vegye át a nem felügyelt AD könyvtárat. További információ: nem [felügyelt címtár átvétele rendszergazdaként Azure Active Directory](../active-directory/users-groups-roles/domains-admin-takeover.md).
 
## <a name="additional-help-resources"></a>További Súgó-erőforrások

Egyéb hibaelhárítási cikkek az Azure-számlázáshoz és-előfizetésekhez

- [Elutasított bankkártya](billing-troubleshoot-declined-card.md)
- [Előfizetés-bejelentkezési problémák](billing-troubleshoot-sign-in-issue.md)
- [Nem található előfizetés](billing-no-subscriptions-found.md)
- [Vállalati költségek nézet letiltva](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Segítségért forduljon hozzánk

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- [Az Azure számlázási dokumentációja](index.md)
