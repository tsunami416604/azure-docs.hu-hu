---
title: Bejelentkezési tevékenységre vonatkozó jelentések hibakódjai az Azure Active Directory portálon | Microsoft Docs
description: Bejelentkezési tevékenységekre vonatkozó jelentések hibakódjainak referenciája.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: aab03c92bee0d3b69062cdcb179eebbb5c0fc8f8
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160246"
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Bejelentkezési tevékenységre vonatkozó jelentések hibakódjai az Azure Active Directory portálon

A felhasználók bejelentkezési jelentésében szereplő információkból az alábbi kérdésekre kaphat választ:

- Ki jelentkezett be az Azure Active Directory használatával?
- Melyik alkalmazásba jelentkeztek be?
- Melyik bejelentkezés volt sikertelen, és miért?

Ez a cikk a hibakódokat és a kapcsolódó leírásaikat tartalmazza. 

## <a name="how-can-i-display-failed-sign-ins"></a>Hogyan tudom megjeleníteni a sikertelen bejelentkezéseket? 

A **[Bejelentkezések](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)** menüponton át vezet az út a bejelentkezési tevékenység adataihoz, a menüpont az **Azure Active** **Tevékenység** szakaszában található.


![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins-errors/61.png "Sign-in activity")

A bejelentkezési jelentésben megjelenítheti az összes sikertelen bejelentkezést. Ehhez válassza a **Sikertelen** elemet a **Bejelentkezési állapot** mezőben.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins-errors/06.png "Sign-in activity")

Ha egy elemre kattint a megjelenő listában, megnyílik a **Tevékenység részletei: Bejelentkezések** panel. Ez a nézet tartalmazza a bejelentkezések összes olyan részletét, amelyet az Azure Active Directory nyomon követ, beleértve a **bejelentkezési hibakódot** és a **sikertelenség okát** is.

![Bejelentkezési tevékenység](./media/active-directory-reporting-activity-sign-ins-errors/05.png "Sign-in activity")


A bejelentkezési adatok Azure Portallal való elérésének alternatívájaként a [jelentéskészítő API-t](active-directory-reporting-api-getting-started-azure-portal.md) is használhatja.


A következő szakasz az összes lehetséges hiba és a kapcsolódó leírások teljes áttekintését tartalmazza. 

## <a name="error-codes"></a>Hibakódok


|Hiba|Leírás|
|---|---|
|16000|Ez az egy belső megvalósítási részletei, és nem hiba történik. Biztonságosan figyelmen kívül hagyhatja ezt a hivatkozást.|
|20001|Probléma lépett fel az összevont identitásszolgáltatóval kapcsolatban. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával.|
|20012|Probléma lépett fel az összevont identitásszolgáltatóval kapcsolatban. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával.|
|20033|Probléma lépett fel az összevont identitásszolgáltatóval kapcsolatban. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával.|
|40008|Probléma lépett fel az összevont identitásszolgáltatóval kapcsolatban. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával.|
|40009|Probléma lépett fel az összevont identitásszolgáltatóval kapcsolatban. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával.|
|40014|Probléma lépett fel az összevont identitásszolgáltatóval kapcsolatban. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával.|
|50000|Probléma lépett fel a bejelentkezési szolgáltatással kapcsolatban. [Hozzon létre támogatási jegyet](fundamentals/active-directory-troubleshooting-support-howto.md) a probléma megoldásához.|
|50001|A szolgáltatásnév nem található ebben a bérlőben. Ez akkor fordulhat elő, ha az alkalmazást nem a bérlő rendszergazdája telepítette. Vagy az egyszerű erőforrás nem található a címtárban, vagy érvénytelen.|
|50002|A bejelentkezés a bérlő korlátozott proxyhozzáférése miatt nem sikerült. Ha ez a saját bérlőszabályzata, módosíthatja a korlátozott bérlő beállításait a probléma elhárításához|
|50003|A bejelentkezés hiányzó aláírókulcs vagy tanúsítvány miatt nem sikerült. Ez akkor fordulhat elő, ha az alkalmazásban nem volt konfigurálva aláírókulcs. Tekintse meg a következő helyen ismertetett megoldásokat: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az alkalmazás tulajdonosával vagy rendszergazdájával|
|50005|A felhasználó olyan platformról próbált meg bejelentkezni az eszközre, amelyet a feltételes hozzáférési szabályzat nem támogat|
|50006| Az aláírás ellenőrzése érvénytelen aláírás miatt sikertelen volt. Tekintse meg a következő helyen ismertetett megoldást: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az alkalmazás tulajdonosával vagy rendszergazdájával|
|50007|Ehhez az alkalmazáshoz nem található partnertitkosítási tanúsítvány. [Küldjön támogatási jegyet](fundamentals/active-directory-troubleshooting-support-howto.md) a Microsofttal ennek javításához|
|50008|Hiányzik a SAML helyességi feltétel, vagy rosszul van konfigurálva a jogkivonatban. Forduljon az összevonási szolgáltatójához.|
|50010|A célközönség URI-ellenőrzése sikertelen volt az alkalmazás esetében, mert a jogkivonat célközönsége nincs konfigurálva. Lépjen kapcsolatba az alkalmazás tulajdonosával|
|50011|A válaszcím hiányzik, rosszul van konfigurálva, vagy nem egyezik az alkalmazáshoz konfigurált válaszcímekkel. Tekintse meg a következő helyen ismertetett megoldást: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Ha továbbra is problémákat tapasztal, lépjen kapcsolatba az alkalmazás tulajdonosával vagy rendszergazdájával|
|50012| Ez az általános hibaüzenet, amely azt jelzi, hogy a hitelesítés sikertelen volt. Ez akkor fordulhat elő, például a hitelesítő adatokat vagy a jogcímeket a kérelemben hiányzik vagy érvénytelen. Győződjön meg arról, hogy a kérelmet küld-e a megfelelő hitelesítő adatokat és a jogcímeket. |
|50013|A helyességi feltétel több ok miatt érvénytelen – A jogkivonat kiállítója nem egyezik meg az API-verzióval az érvényes időtartományban – lejárt – nem megfelelő formátumú – a helyességi feltétel frissítési jogkivonata nem elsődleges frissítési jogkivonat.|
|50017|Az érvényesítés nem sikerült az alábbi okok egyike miatt:<ul><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A várt CrlSegment nem található</li><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A különbözeti CRL terjesztési pontja megfelelő CRL terjesztési pont nélkül lett konfigurálva</li><li>időtúllépési hiba miatt nem sikerült érvényes CRL-szegmenseket lekérni</li><li>A CRL letöltése nem sikerült</li></ul>Lépjen kapcsolatba a bérlő rendszergazdájával.|
|50020|A felhasználó nem jogosult a következő okok valamelyike.<ul><li>A felhasználó megpróbál bejelentkezni egy olyan MSA-fiókkal, a v1-végponttal</li><li>A felhasználó nem létezik a bérlőben.</li></ul> Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50027|A JWT-jogkivonat az alábbi okok miatt érvénytelen:<ul><li>nem tartalmazza az egyszeri kulcs jogcímét vagy a tárgy jogcímét</li><li>tárgyazonosító eltérése</li><li>dupla jogcím az idToken-jogcímekben</li><li>nem várt kiállító</li><li>nem várt célközönség</li><li>nem az érvényes időtartományon belül van </li><li>a jogkivonat formátuma nem megfelelő</li><li>A kiállító külső azonosító jogkivonata nem felelt meg az aláírás-ellenőrzésen.</li></ul>Lépjen kapcsolatba az alkalmazás tulajdonosával|
|50029|Érvénytelen URI – a tartománynév érvénytelen karaktert tartalmaz. Lépjen kapcsolatba a bérlő rendszergazdájával.|
|50034|A felhasználó nem létezik a címtárban. Lépjen kapcsolatba a bérlő rendszergazdájával.|
|50042|Salt érték szükséges a névből hiányzó páros azonosító létrehozásához. Lépjen kapcsolatba a bérlő rendszergazdájával.|
|50048|A tárgy nem egyezik meg a kiállító jogcímmel az ügyfél helyességi feltételében. Lépjen kapcsolatba a bérlő rendszergazdájával.|
|50050|A kérelem nem megfelelő formátumú. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50053|A fiók zárolva van, mert a felhasználó túl sokszor próbált meg bejelentkezni helytelen felhasználói azonosítóval vagy jelszóval.|
|50055|Érvénytelen jelszó, lejárt jelszót írt be.|
|50056|Érvénytelen vagy null jelszó – Ehhez a felhasználóhoz nem létezik jelszó a tárolóban|
|50057|A felhasználói fiók le van tiltva. Egy rendszergazda letiltotta a fiókot.|
|50058|Az alkalmazást csendes bejelentkezést próbált meg végrehajtani, de a felhasználót nem lehetett csendesen bejelentkeztetni. Az alkalmazásnak el kell indítania egy interaktív folyamatot, amely bejelentkezési lehetőséget biztosít a felhasználóknak. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50059|A felhasználó nem létezik a címtárban. Lépjen kapcsolatba a bérlő rendszergazdájával|
|50061|A kijelentkezési kérés érvénytelen. Lépjen kapcsolatba az alkalmazás tulajdonosával|
|50072|A felhasználónak kéttényezős hitelesítést kell igényelnie (interaktív)|
|50074|A felhasználó nem adott jó választ az MFA-kérdésre.|
|50076|A felhasználó nem adott jó választ az MFA-kérdésre (nem interaktív)|
|50079|A felhasználónak kéttényezős hitelesítést kell igényelnie (nem interaktív bejelentkezések)|
|50085|A frissítési jogkivonathoz közösségi identitásszolgáltatói bejelentkezés szükséges. A felhasználónak újra be kell jelentkeznie a felhasználónévvel és jelszóval|
|50089|A folyamat jogkivonata lejárt – a hitelesítés nem sikerült. A felhasználónak újra be kell jelentkeznie a felhasználónévvel és jelszóval|
|50097|Eszközhitelesítés szükséges – a DeviceId – DeviceAltSecId jogcímek null értékűek, vagy nem létezik az eszközazonosítónak megfelelő eszköz|
|50099|A JWT-aláírás érvénytelen. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50105|A bejelentkezett felhasználó nincs hozzárendelve a bejelentkezett alkalmazás egyik szerepköréhez sem. Rendelje hozzá az alkalmazást a felhasználóhoz. További információ: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|A kért összevonásitartomány-objektum nem létezik. Lépjen kapcsolatba a bérlő rendszergazdájával.|
|50120|JWT-fejléchiba. Lépjen kapcsolatba a bérlő rendszergazdájával.|
|50124|A jogcím-átalakítás érvénytelen bemeneti paramétert tartalmaz. Lépjen kapcsolatba a bérlő rendszergazdájával a szabályzat frissítése érdekében.|
|50125|A bejelentkezés új jelszó kérése vagy jelszó-regisztráció bevitele miatt megszakadt|
|50126|Érvénytelen felhasználónév vagy jelszó vagy érvénytelen helyszíni felhasználónév vagy jelszó.|
|50127|A felhasználónak közvetítőalkalmazást kell telepítenie, hogy hozzáférjen ehhez a tartalomhoz.|
|50128|Érvénytelen tartománynév – Nem található a bérlőt azonosító információ a kérelemben, vagy egyik megadott hitelesítő adat sem tartalmazza|
|50129|Az eszköz nincs munkahelyhez csatlakoztatva – Munkahelyi csatlakoztatás szükséges az eszköz regisztrálásához.|
|50130|A jogcím értéke nem értelmezhető ismert hitelesítési módszerként|
|50131|Különböző feltételes hozzáférési hibákban használatos. Például Rossz Windows-eszközállapot, a kérés gyanús tevékenység miatt blokkolva, a hozzáférési szabályzattal és a biztonsági szabályzattal kapcsolatos döntések.|
|50132|A hitelesítő adatok vissza lettek vonva az alábbi okok miatt:<ul><li>Az SSO-összetevő érvénytelen vagy lejárt</li><li>A munkamenet nem elég friss az alkalmazáshoz</li><li>A rendszer csendes bejelentkezési kérelmet küldött, de a felhasználó Azure AD-munkamenete érvénytelen vagy lejárt.</li></ul>|
|50133|A jelszó lejárta vagy nemrégi módosítása miatt a munkamenet érvénytelen.|
|50135|A fiókkockázat miatt jelszómódosítás szükséges|
|50136|Msa-munkamenet átirányítása az alkalmazáshoz – Egy MSA-munkamenet észlelve |
|50140|Ez a hiba a „Bejelentkezve szeretnék maradni” megszakítás miatt lépett fel, miközben a felhasználó megpróbált bejelentkezni. [Küldjön támogatási jegyet](fundamentals/active-directory-troubleshooting-support-howto.md) a korrelációs azonosítóval, a kérelemazonosítóval és a hibakóddal a részletek megismeréséhez. |
|50143|Munkamenet-eltérés – A munkamenet érvénytelen, mert a felhasználó bérlője nem egyezik meg a tartománymutatóval eltérő erőforrások miatt. [Küldjön támogatási jegyet](fundamentals/active-directory-troubleshooting-support-howto.md) a korrelációs azonosítóval, a kérelemazonosítóval és a hibakóddal a részletek megismeréséhez.|
|50144|A felhasználó Active Directory jelszava lejárt. Hozzon létre új jelszót a felhasználó számára, vagy kérje meg a végfelhasználót az önkiszolgáló helyreállítási eszköz használatára|
|50146|Ezt az alkalmazást egy alkalmazásspecifikus bejelentkezési kulccsal kell konfigurálni. Az alkalmazás nem ilyennel van konfigurálva, vagy a kulcs lejárt vagy még nem érvényes. Lépjen kapcsolatba az alkalmazás tulajdonosával|
|50148|A code_verifier nem egyezik meg a PKCE hitelesítési kérésben megadott code_challenge értékkel. Lépjen kapcsolatba az alkalmazás fejlesztőjével. |
|50155|Az eszköz hitelesítése nem sikerült a felhasználó esetében|
|50158|A külső biztonsági ellenőrzés nem teljesült|
|50161|A külső szolgáltató által küldött jogcímek nem elegendőek, vagy hiányzó jogcím lett kérelmezve a külső szolgáltatótól|
|50166|A kérés jogcímszolgáltatónak való küldése nem sikerült|
|50169|A tartomány nem az aktuális szolgáltatásnévtér konfigurált tartománya.|
|50172|A külső jogcímszolgáltató nincs jóváhagyva. Lépjen kapcsolatba a bérlő rendszergazdájával|
|50173|Friss hitelesítési jogkivonat szükséges. Kérje meg a felhasználót, hogy jelentkezzen be újra új hitelesítő adatokkal|
|50177|A külső ellenőrzés nem támogatott az átmenő felhasználók esetében|
|50178|A munkamenet-vezérlés nem támogatott az átmenő felhasználók esetében|
|50180|Integrált Windows-hitelesítés szükséges. Engedélyezze a Seamless SSO-t a bérlőn.|
|51001|Nem található tartománymutató a helyszíni biztonsági azonosítóval – helyszíni UPN|
|51004|A felhasználói fiók nem létezik a címtárban.|
|51006|Integrált Windows-hitelesítés szükséges. A felhasználó egy olyan munkamenet-jogkivonat használatával jelentkezett be, amely nem rendelkezik WIA-jogcímmel. A felhasználónak újra be kell jelentkeznie.|
|52004|A felhasználó nem járult hozzá a LinkedIn-erőforrásokhoz való hozzáféréshez. |
|53000|A feltételes hozzáférési szabályzathoz megfelelő eszköz szükséges, és az eszköz nem megfelelő. Kérje meg a felhasználót, hogy regisztrálja az eszközeit egy jóváhagyott MDM-szolgáltatóval, például az Intune-nal|
|53001|A feltételes hozzáférési szabályzathoz tartományhoz csatlakoztatott eszköz szükséges, és az eszköz nincs tartományhoz csatlakoztatva. Kérje meg a felhasználót, hogy használjon tartományhoz csatlakoztatott eszközt|
|53002|A használt alkalmazás nem feltételes hozzáféréshez jóváhagyott alkalmazás. A felhasználónak a jóváhagyott alkalmazások listáján szereplő alkalmazást kell használnia a hozzáféréshez.|
|53003|A hozzáférés blokkolva a feltételes hozzáférési szabályzatok miatt.|
|53004|A tartalom elérése előtt a felhasználónak el kell végeznie a többtényezős hitelesítés regisztrációs folyamatát. A felhasználónak regisztrálnia kell többtényezős hitelesítésre.|
|65001|Az X alkalmazás nem rendelkezik engedéllyel az Y alkalmazás eléréséhez, vagy az engedély vissza lett vonva. Vagy: a felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás X azonosítóval való használatába. Küldjön egy interaktív engedélyezési kérést ehhez a felhasználóhoz és erőforráshoz. Vagy: a felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás X azonosítóval való használatába. Küldjön egy engedélyezési kérést a bérlő rendszergazdájának, hogy az Y alkalmazás nevében végezzen tevékenységet a Z erőforráson.|
|65004|A felhasználó elutasította az alkalmazáshoz való hozzáférés jóváhagyását. Kérje meg a felhasználót, hogy jelentkezzen be újra, és hagyja jóvá az alkalmazást|
|65005|Az alkalmazás által igényelt erőforrás-hozzáférési lista nem tartalmaz az erőforrás által felderíthető alkalmazásokat, vagy az ügyfélalkalmazás olyan erőforrás hozzáférését kérte, amely nincs meghatározva a szükséges erőforrás-hozzáférési listán, vagy a Graph szolgáltatás rossz kérést adott vissza, vagy az erőforrás nem található. Ha az alkalmazás támogatja az SAML-t, akkor előfordulhat, hogy nem a megfelelő azonosítóval (entitással) konfigurálta az alkalmazást. Próbálja ki az SAML-lel kapcsolatos megoldásokat, amelyek a következő hivatkozáson találhatók: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list)|
|70000|A hozzáférés érvénytelen az alábbi okok miatt:<ul><li>A szükséges SAML 2.0 helyességi feltétel érvénytelen tárgyjóváhagyási metódussal rendelkezik</li><li>Az alkalmazás OnBehalfOf folyamata nem támogatott a V2-ben</li><li>Elsődleges frissítési jogkivonat nincs aláírva a munkamenetkulccsal</li><li>Érvénytelen külső frissítési jogkivonat</li><li>A megadott hozzáférés egy másik bérlőhöz lett beszerezve.</li></ul>|
|70001|Az X nevű alkalmazás nem található az Y nevű bérlőben. Ez akkor történhet, ha az X azonosítójú alkalmazást nem a bérlő rendszergazdája telepítette, vagy nem fogadta el egy felhasználó sem a bérlőben. Előfordulhat, hogy helytelenül konfigurálta az alkalmazás azonosítóját, vagy rossz bérlőre küldte a hitelesítési kérést|
|70002|Az alkalmazás érvénytelen ügyfél-hitelesítő adatokat adott vissza. Lépjen kapcsolatba az alkalmazás tulajdonosával|
|70003|Az alkalmazás nem támogatott hozzáférési típust adott vissza. Lépjen kapcsolatba az alkalmazás tulajdonosával|
|70004|Az alkalmazás nem támogatott átirányítási URI-t adott vissza. Az ügyfél által megadott átirányítási cím nem egyezik meg egyik konfigurált címmel sem, illetve az OIDC jóváhagyási listán szereplő címekkel sem. Lépjen kapcsolatba az alkalmazás tulajdonosával|
|70005|Az alkalmazás nem támogatott választípust adott vissza az alábbi okok miatt:<ul><li>A „jogkivonat” választípus nincs engedélyezve az alkalmazás számára</li><li>Az id_token típusú válaszhoz az OpenID hatókör szükséges – a kódolt wctx nem támogatott OAuth paraméterértéket tartalmaz</li></ul>Lépjen kapcsolatba az alkalmazás tulajdonosával|
|70007|Az alkalmazás a nem támogatott response_mode értéket adta vissza egy jogkivonat kérésekor. Lépjen kapcsolatba az alkalmazás tulajdonosával|
|70008|A megadott engedélyezési kód vagy frissítési jogkivonat lejárt vagy vissza lett vonva. Kérje meg a felhasználót, hogy próbáljon meg újra bejelentkezni|
|70011|Az alkalmazás által kért hatókör érvénytelen. Lépjen kapcsolatba az alkalmazás tulajdonosával|
|70012|Kiszolgálóhiba történt az MSA-felhasználó (fogyasztó) hitelesítése során. Próbálkozzon újra. Ha a hiba továbbra is fennáll, [küldjön támogatási jegyet](fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Érvénytelen ellenőrzőkód: a felhasználó rossz felhasználói kódot írt be az eszközkód-folyamathoz. Az engedélyezés nincs jóváhagyva|
|70019|Az ellenőrzőkód lejárt. Kérje meg a felhasználót, hogy próbáljon meg újra bejelentkezni|
|70037|Helytelen válasz az ellenőrző kérdésre. Távoli hitelesítési munkamenet megtagadva.|
|75001|Hiba történt a SAML-üzenetkötés során.|
|75003|Az alkalmazás nem támogatott kötéssel kapcsolatos hibát adott vissza (a SAML-protokollválasz csak HTTP POST-kötésen keresztül küldhető el). Lépjen kapcsolatba az alkalmazás tulajdonosával|
|75005|Az Azure AD nem támogatja az alkalmazás egyszeri bejelentkezésre vonatkozó SAML-kérelmét. Lépjen kapcsolatba az alkalmazás tulajdonosával|
|75008|A rendszer elutasította az alkalmazásból érkező kérelmet, mert a SAML-kérelem nem várt céllal rendelkezik. Lépjen kapcsolatba az alkalmazás tulajdonosával|
|75011|A felhasználó által a szolgáltatásban való hitelesítésre használt hitelesítési mód nem egyezik a kért hitelesítési móddal. Lépjen kapcsolatba az alkalmazás tulajdonosával|
|75016|A SAML2 hitelesítési kérelem érvénytelen NameIdPolicy értékkel rendelkezik. Lépjen kapcsolatba az alkalmazás tulajdonosával|
|80001|A hitelesítési ügynök nem tudott csatlakozni az Active Directory-hoz. Győződjön meg arról, hogy a hitelesítési ügynök telepítve van egy olyan tartományhoz csatlakozó gépen, amely rálát a felhasználó bejelentkezési kérelmét kiszolgáló tartományvezérlőre.|
|80002|Belső hiba történt. A jelszó-ellenőrzési kérelem túllépte az időkorlátot. Nem sikerült elküldeni a hitelesítési kérést a belső hibrid identitásszolgáltatásnak. [Küldjön támogatási jegyet](fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez|
|80003|A hitelesítési ügynök érvénytelen választ kapott. Ismeretlen hiba történt a helyszíni Active Directoryban történő hitelesítésre tett kísérlet során. [Küldjön támogatási jegyet](fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez|
|80005|Hitelesítési ügynök: Ismeretlen hiba történt a Hitelesítési ügynöktől érkező válasz feldolgozásakor. [Küldjön támogatási jegyet](fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez|
|80007|A hitelesítési ügynök nem tudta ellenőrizni a felhasználó jelszavát.|
|80010|A hitelesítési ügynök nem tudta visszafejteni a jelszót. |
|80011|A hitelesítési ügynök nem tudta lekérni a titkosítási kulcsot.|
|80012|A felhasználók az (AD-ben megadott) engedélyezett időszakon kívül próbáltak meg bejelentkezni|
|80013|A hitelesítési kísérlet a hitelesítési ügynököt és az AD-t futtató számítógép közötti időeltérés miatt nem sikerült. Javítsa ki az időszinkronizálási problémákat|
|80014|A hitelesítési ügynök túllépte az időkorlátot. [Küldjön támogatási jegyet](fundamentals/active-directory-troubleshooting-support-howto.md) a hibakóddal, a korrelációs azonosítóval és a dátum/idő értékkel a hiba részleteinek megismeréséhez|
|81001|A felhasználó Kerberos-jegye túl nagy. Ez akkor fordulhat elő, ha a felhasználó túl sok csoportban szerepel, és így a Kerberos-jegy túl sok csoporttagságot tartalmaz. Csökkentse a felhasználó csoporttagságait, majd próbálkozzon újra.|
|81005|A hitelesítési csomag nem támogatott|
|81007|A bérlőn nincs engedélyezve a Seamless SSO|
|81012|Ez nem hiba történik. Ez azt jelzi, hogy az Azure AD bejelentkezni próbáló felhasználó különbözik az eszközbe jelentkezett felhasználótól. Biztonságosan figyelmen kívül hagyhatja ezt a kódot a naplókat.|
|90010|A kérelem nem támogatott különböző okok miatt. Például a kérés használatával jön létre egy nem támogatott kérelmi metódus (csak POST metódus támogatott), vagy az, hogy a kért jogkivonatot aláíró algoritmus nem támogatott. Lépjen kapcsolatba az alkalmazás fejlesztőjével.|
|90014| Hiányzik egy kötelező mező protokoll üzenet, forduljon az alkalmazás tulajdonosa. Ha az alkalmazás tulajdonosa, győződjön meg arról, hogy rendelkezik-e a szükséges paramétereket, a bejelentkezési kérelem számára. |
|90072| A fióknak rendelkeznie kell adni a bérlőn külső felhasználóként először. Kijelentkezés és bejelentkezés újra egy másik Azure AD-fiókot.|
|90094| A támogatás rendszergazdai engedélyekkel kell rendelkeznie. Kérje meg a bérlői rendszergazda történő ehhez az alkalmazáshoz.|

## <a name="next-steps"></a>További lépések

További információ: [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon](active-directory-reporting-activity-sign-ins.md).
