---
title: Bejelentkezési tevékenységre vonatkozó jelentések hibakódjai az Azure Active Directory portálon | Microsoft Docs
description: Bejelentkezési tevékenységekre vonatkozó jelentések hibakódjainak referenciája.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f1ce786b748fedd1ec4c722b28bc11c28672c2f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443402"
---
# <a name="sign-in-activity-report-error-codes"></a>Bejelentkezési tevékenységre vonatkozó jelentések hibakódjai 

Által biztosított információkkal a [felhasználói bejelentkezésekre](concept-sign-ins.md), például a kérdésekre adott válaszok keresése:

- Ki jelentkezett be az alkalmazásom?
- Mely alkalmazások lettek bejelentkezett?
- Melyik bejelentkezés sikertelen volt, hogy miért?

A sikertelen bejelentkezési kísérlet, látni fogja a hibához tartozó hibakód. Ez a cikk a hibakódok és azok leírásait követése javasolt művelet együtt sorolja fel, ha vannak ilyenek. 

## <a name="how-can-i-display-failed-sign-ins"></a>Hogyan tudom megjeleníteni a sikertelen bejelentkezéseket? 

Keresse meg a [bejelentkezésekre](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) a a [az Azure portal](https://portal.azure.com).

![Bejelentkezési tevékenység](./media/reference-sign-ins-error-codes/61.png "Sign-in activity")

Az összes sikertelen bejelentkezések kattintva megjeleníthető a jelentést szűrheti **hiba** a a **bejelentkezési állapot** legördülő listából.

![Bejelentkezési tevékenység](./media/reference-sign-ins-error-codes/06.png "Sign-in activity")

Szűrt lista elem kijelölésével megnyílik a **tevékenység részletei: Bejelentkezések** panelen. Ez a nézet nyújt további információt a sikertelen bejelentkezési esemény, többek között a **bejelentkezési hiba kódja** és **a hiba oka**.

![Bejelentkezési tevékenység](./media/reference-sign-ins-error-codes/05.png "Sign-in activity")

Szoftveresen is segítségével végezheti el a bejelentkezési adatokat a [reporting API](concept-reporting-api.md).

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
|50000|Probléma lépett fel a bejelentkezési szolgáltatással kapcsolatban. [Hozzon létre támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a probléma megoldásához.|
|50001|A szolgáltatásnév nem található ebben a bérlőben. Ez akkor fordulhat elő, ha az alkalmazás nincs telepítve a bérlő rendszergazdája, vagy ha az egyszerű erőforrás nem található a címtárban, vagy érvénytelen.|
|50002|A bejelentkezés a bérlő korlátozott proxyhozzáférése miatt nem sikerült. Ha a saját bérlő-házirend, módosíthatja a probléma megoldásához a korlátozott bérlői beállításokat.|
|50003|A bejelentkezés hiányzó aláírókulcs vagy tanúsítvány miatt nem sikerült. Ez akkor fordulhat elő, ha az alkalmazásban nem volt konfigurálva aláírókulcs. Tekintse meg a következő helyen ismertetett megoldásokat: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Ha a probléma tartósan fennáll, forduljon az alkalmazás tulajdonosa vagy az alkalmazás-rendszergazda.|
|50005|Felhasználó próbált meg bejelentkezni az eszköz egy platform, amely jelenleg nem támogatja a feltételes hozzáférési szabályzat segítségével.|
|50006| Az aláírás ellenőrzése érvénytelen aláírás miatt sikertelen volt. Tekintse meg a következő helyen ismertetett megoldást: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Ha a probléma tartósan fennáll, forduljon az alkalmazás tulajdonosa vagy az alkalmazás-rendszergazda.|
|50007|Ehhez az alkalmazáshoz nem található partnertitkosítási tanúsítvány. [Hozzon létre egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a Microsofttal a probléma kijavítva.|
|50008|Hiányzik a SAML helyességi feltétel, vagy rosszul van konfigurálva a jogkivonatban. Forduljon az összevonási szolgáltatójához.|
|50010|A célközönség URI-ellenőrzése sikertelen volt az alkalmazás esetében, mert a jogkivonat célközönsége nincs konfigurálva. Lépjen kapcsolatba az alkalmazás tulajdonosa a feloldásához.|
|50011|A válaszcím hiányzik, rosszul van konfigurálva, vagy nem egyezik az alkalmazáshoz konfigurált válaszcímekkel. Próbálja meg a megoldást webhelyen felsorolt [ https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application ](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Ha a probléma tartósan fennáll, forduljon az alkalmazás tulajdonosa vagy az alkalmazás-rendszergazda.|
|50012| Ez az általános hibaüzenet, amely azt jelzi, hogy a hitelesítés sikertelen volt. Ez akkor fordulhat elő, például a hitelesítő adatokat vagy a jogcímeket a kérelemben hiányzik vagy érvénytelen. Győződjön meg arról, hogy a kérelmet küld-e a megfelelő hitelesítő adatokat és a jogcímeket. |
|50013|Helyességi feltétel alkalmazása különböző okok miatt érvénytelen. Például a jogkivonat kibocsátója nem felel meg az api-verzió érvényes idő hatókörén belüli, a jogkivonat lejárt vagy helytelen formátumú, vagy a frissítési jogkivonat található a helyességi feltétel nem egy elsődleges frissítési jogkivonatot.|
|50017|Az érvényesítés nem sikerült az alábbi okok egyike miatt:<ul><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A várt CrlSegment nem található</li><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A különbözeti CRL terjesztési pontja megfelelő CRL terjesztési pont nélkül lett konfigurálva</li><li>időtúllépési hiba miatt nem sikerült érvényes CRL-szegmenseket lekérni</li><li>A CRL letöltése nem sikerült</li></ul>Lépjen kapcsolatba a bérlői rendszergazda.|
|50020|A felhasználó nem jogosult a következő okok valamelyike.<ul><li>A felhasználó megpróbál bejelentkezni egy olyan MSA-fiókkal, a v1-végponttal</li><li>A felhasználó nem létezik a bérlőben.</li></ul> Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50027|A JWT-jogkivonat az alábbi okok miatt érvénytelen:<ul><li>nem tartalmazza az egyszeri kulcs jogcímét vagy a tárgy jogcímét</li><li>tárgyazonosító eltérése</li><li>dupla jogcím az idToken-jogcímekben</li><li>nem várt kiállító</li><li>nem várt célközönség</li><li>nem az érvényes időtartományon belül van </li><li>a jogkivonat formátuma nem megfelelő</li><li>A kiállító külső azonosító jogkivonata nem felelt meg az aláírás-ellenőrzésen.</li></ul>Lépjen kapcsolatba az alkalmazás tulajdonosával|
|50029|Érvénytelen URI – a tartománynév érvénytelen karaktert tartalmaz. Lépjen kapcsolatba a bérlői rendszergazda.|
|50034|A felhasználó nem létezik a címtárban. A bérlői rendszergazdától.|
|50042|A védőérték elő az páros azonosítója nincs megadva elvileg. Lépjen kapcsolatba a bérlői rendszergazda.|
|50048|A tárgy nem egyezik meg a kiállító jogcímmel az ügyfél helyességi feltételében. Lépjen kapcsolatba a bérlői rendszergazda.|
|50050|A kérelem nem megfelelő formátumú. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50053|Fiók zárolva van, mert a felhasználó próbált meg bejelentkezni túl sokszor helytelen felhasználói Azonosítóval vagy jelszóval.|
|50055|Érvénytelen jelszó, lejárt jelszót írt be.|
|50056|Érvénytelen vagy null értékű jelszó – a jelszó nem létezik tárolja a felhasználó számára.|
|50057|A felhasználói fiók le van tiltva. Egy rendszergazda letiltotta a fiókot.|
|50058|Az alkalmazást csendes bejelentkezést próbált meg végrehajtani, de a felhasználót nem lehetett csendesen bejelentkeztetni. Az alkalmazás jogosultságot ad a felhasználók bejelentkezési lehetőség egy interaktív folyamat elindításához szükséges. Forduljon az alkalmazás tulajdonosa.|
|50059|A felhasználó nem létezik a címtárban. A bérlői rendszergazdától.|
|50061|A kijelentkezési kérés érvénytelen. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50072|Felhasználói kell igényelnie (interaktív) a kéttényezős hitelesítés.|
|50074|A felhasználó nem adott jó választ az MFA-kérdésre.|
|50076|Felhasználó nem felelt meg az MFA-hitelesítést (nem interaktív).|
|50079|A felhasználónak kéttényezős hitelesítést (nem interaktív bejelentkezések) igényléséhez.|
|50085|A frissítési jogkivonathoz közösségi identitásszolgáltatói bejelentkezés szükséges. A felhasználó próbálja meg újra a felhasználónevével és jelszavával történő bejelentkezéssel rendelkezik.|
|50089|Flow-jogkivonat lejárt – a hitelesítés sikertelen. Próbálja meg újra a felhasználónevével és jelszavával történő bejelentkezéssel felhasználó|
|50097|Eszköz-hitelesítés szükséges. Ez akkor fordulhat elő, mert a DeviceId vagy DeviceAltSecId jogcímek **null**, vagy nem megfelelő az eszköz azonosítója eszköz van-e.|
|50099|A JWT-aláírás érvénytelen. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50105|A bejelentkezett felhasználó nincs hozzárendelve a bejelentkezett alkalmazás egyik szerepköréhez sem. Rendelje hozzá a felhasználót az alkalmazáshoz. További információ: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|A kért összevonásitartomány-objektum nem létezik. Lépjen kapcsolatba a bérlői rendszergazda.|
|50120|JWT-fejléchiba. Lépjen kapcsolatba a bérlői rendszergazda.|
|50124|A jogcím-átalakítás érvénytelen bemeneti paramétert tartalmaz. Lépjen kapcsolatba a bérlői rendszergazda a házirend módosítása.|
|50125|Jelentkezzen be egy új jelszó kérése vagy jelszó-regisztrációs bejegyzés miatt megszakadt.|
|50126|Érvénytelen felhasználónév vagy jelszó, vagy érvénytelen helyszíni felhasználónév vagy jelszó.|
|50127|A felhasználónak kell telepíteni a közvetítő alkalmazás hozzáférhetnek a tartalmaihoz.|
|50128|Érvénytelen tartománynév - információ a bérlő-azonosító nem található vagy a kérelem, vagy hallgatólagos által a megadott hitelesítő adatokat.|
|50129|Eszköz nem áll munkahelyhez csatlakoztatott - **munkahelyi csatlakoztatás** szükséges regisztrálni az eszközt.|
|50130|Jogcím értéke nem értelmezhető ismert hitelesítési módszert.|
|50131|Különböző feltételes hozzáférési hibákban használatos. Például Rossz Windows-eszközállapot, a kérés gyanús tevékenység miatt blokkolva, a hozzáférési szabályzattal és a biztonsági szabályzattal kapcsolatos döntések.|
|50132|A hitelesítő adatok vissza lettek vonva az alábbi okok miatt:<ul><li>Az SSO-összetevő érvénytelen vagy lejárt</li><li>A munkamenet nem elég friss az alkalmazáshoz</li><li>A rendszer csendes bejelentkezési kérelmet küldött, de a felhasználó Azure AD-munkamenete érvénytelen vagy lejárt.</li></ul>|
|50133|A jelszó lejárta vagy nemrégi módosítása miatt a munkamenet érvénytelen.|
|50135|Jelszó módosítása miatt a fiók kockázati megadása kötelező.|
|50136|Átirányítás MSA munkamenet alkalmazás – önálló MSA-munkamenet észlelhető. |
|50140|Ez a hiba a „Bejelentkezve szeretnék maradni” megszakítás miatt lépett fel, miközben a felhasználó megpróbált bejelentkezni. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a korrelációs azonosítóval, a kérelemazonosítóval és a hibakóddal a részletek megismeréséhez. |
|50143|Munkamenet eltérő - munkamenet érvénytelen, mert felhasználói bérlő nem egyezik meg a tartomány mutató különböző erőforrás miatt.  [Hozzon létre egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a korrelációs Azonosítót, a kérelem azonosítója és a hiba kódja további információért.|
|50144|A felhasználó Active Directory jelszava lejárt. A felhasználó egy új jelszót létrehozni, vagy a végfelhasználó számára az önkiszolgáló visszaállítási eszközzel rendelkezik.|
|50146|Ezt az alkalmazást egy alkalmazásspecifikus bejelentkezési kulccsal kell konfigurálni. Az alkalmazás nem ilyennel van konfigurálva, vagy a kulcs lejárt vagy még nem érvényes. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50148|A code_verifier nem egyezik meg a PKCE hitelesítési kérésben megadott code_challenge értékkel. Lépjen kapcsolatba az alkalmazás fejlesztőjével. |
|50155|Eszköz-hitelesítés nem sikerült a felhasználó számára.|
|50158|Külső biztonsági kérdést tettünk fel nem teljesült.|
|50161|Külső szolgáltató által küldött jogcímek nem megfelelő vagy hiányzó jogcím szolgáltató külső kérte.|
|50166|Nem sikerült a kérés küldése a jogcím-szolgáltatói.|
|50169|A tartomány nem az aktuális szolgáltatásnévtér konfigurált tartománya.|
|50172|A külső jogcímszolgáltató nincs jóváhagyva. Lépjen kapcsolatba a bérlői rendszergazda|
|50173|Friss hitelesítési jogkivonat szükséges. A felhasználó jelentkezzen be újra a friss hitelesítő adatokkal rendelkezik.|
|50177|Külső challenge csatlakoztatott felhasználók számára nem támogatott.|
|50178|Munkamenet-vezérlő nem támogatott a csatlakoztatott felhasználók számára.|
|50180|Integrált Windows-hitelesítés szükséges. Engedélyezze a Seamless SSO-t a bérlőn.|
|51001|Tartományemlékeztető nem szerepel a helyszíni biztonsági azonosítóval – a helyszíni egyszerű Felhasználónévvel.|
|51004|A felhasználói fiók nem létezik a címtárban.|
|51006|Integrált Windows-hitelesítés szükséges. A felhasználó jelentkezett be, hogy hiányzik a jogcím-n keresztül munkamenet-jogkivonat használatával. A felhasználónak újra be kell jelentkeznie.|
|52004|A felhasználó nem járult hozzá a LinkedIn-erőforrásokhoz való hozzáféréshez. |
|53000|A feltételes hozzáférési szabályzathoz megfelelő eszköz szükséges, és az eszköz nem megfelelő. A felhasználónak regisztrálnia kell az eszközét az Intune-ban például egy jóváhagyott MDM-szolgáltató rendelkezik.|
|53001|A feltételes hozzáférési szabályzathoz tartományhoz csatlakoztatott eszköz szükséges, és az eszköz nincs tartományhoz csatlakoztatva. Rendelkezik felhasználói használata egy tartományhoz csatlakoztatott eszköz.|
|53002|A használt alkalmazás nem feltételes hozzáféréshez jóváhagyott alkalmazás. A felhasználónak a jóváhagyott alkalmazások listáján szereplő alkalmazást kell használnia a hozzáféréshez.|
|53003|A hozzáférés blokkolva a feltételes hozzáférési szabályzatok miatt.|
|53004|A tartalom elérése előtt a felhasználónak el kell végeznie a többtényezős hitelesítés regisztrációs folyamatát. A felhasználónak regisztrálnia kell többtényezős hitelesítésre.|
|65001|Az X alkalmazás nem rendelkezik engedéllyel az Y alkalmazás eléréséhez, vagy az engedély vissza lett vonva. Vagy: a felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás X azonosítóval való használatába. Küldjön egy interaktív engedélyezési kérést ehhez a felhasználóhoz és erőforráshoz. Vagy a felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás segítségével azonosító x küldjön egy engedélyezési kérést a bérlő rendszergazdájának alkalmazás nevében: Y erőforrás: Z.|
|65004|A felhasználó elutasította az alkalmazáshoz való hozzáférés jóváhagyását. Kérje meg a felhasználót, hogy jelentkezzen be újra, és hagyja jóvá az alkalmazást|
|65005|Az alkalmazás által igényelt erőforrás-hozzáférési lista nem tartalmaz az erőforrás által felderíthető alkalmazásokat, vagy az ügyfélalkalmazás olyan erőforrás hozzáférését kérte, amely nincs meghatározva a szükséges erőforrás-hozzáférési listán, vagy a Graph szolgáltatás rossz kérést adott vissza, vagy az erőforrás nem található. Ha az alkalmazás támogatja az SAML-t, akkor előfordulhat, hogy nem a megfelelő azonosítóval (entitással) konfigurálta az alkalmazást. Próbálja ki az SAML-lel kapcsolatos megoldásokat, amelyek a következő hivatkozáson találhatók: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|A hozzáférés érvénytelen az alábbi okok miatt:<ul><li>A szükséges SAML 2.0 helyességi feltétel érvénytelen tárgyjóváhagyási metódussal rendelkezik</li><li>Az alkalmazás OnBehalfOf folyamata nem támogatott a V2-ben</li><li>Elsődleges frissítési jogkivonat nincs aláírva a munkamenetkulccsal</li><li>Érvénytelen külső frissítési jogkivonat</li><li>A megadott hozzáférés egy másik bérlőhöz lett beszerezve.</li></ul>|
|70001|Az X nevű alkalmazás nem található az Y nevű bérlőben. Ez akkor történhet, ha az X azonosítójú alkalmazást nem a bérlő rendszergazdája telepítette, vagy nem fogadta el egy felhasználó sem a bérlőben. Előfordulhat, hogy van konfigurálva az azonosító értékét az alkalmazás vagy rossz bérlőhöz küldte a a hitelesítési kérést.|
|70002|Az alkalmazás érvénytelen ügyfél-hitelesítő adatokat adott vissza. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|70003|Az alkalmazás nem támogatott hozzáférési típust adott vissza. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|70004|Az alkalmazás nem támogatott átirányítási URI-t adott vissza. Az ügyfél által megadott átirányítási cím nem egyezik meg egyik konfigurált címmel sem, illetve az OIDC jóváhagyási listán szereplő címekkel sem. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|70005|Az alkalmazás nem támogatott választípust adott vissza az alábbi okok miatt:<ul><li>A „jogkivonat” választípus nincs engedélyezve az alkalmazás számára</li><li>Az id_token típusú válaszhoz az OpenID hatókör szükséges – a kódolt wctx nem támogatott OAuth paraméterértéket tartalmaz</li></ul>Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|70007|Az alkalmazás a nem támogatott response_mode értéket adta vissza egy jogkivonat kérésekor. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|70008|A megadott engedélyezési kód vagy frissítési jogkivonat lejárt, vagy vissza lett vonva. Rendelkezik a felhasználó újra bejelentkezik.|
|70011|Az alkalmazás által kért hatókör érvénytelen. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|70012|Kiszolgálóhiba történt az MSA-felhasználó (fogyasztó) hitelesítése során. Ismételje meg a bejelentkezés, és ha a probléma tartósan fennáll, [hozzon létre egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Érvénytelen ellenőrzőkód: a felhasználó rossz felhasználói kódot írt be az eszközkód-folyamathoz. Engedélyezési nem engedélyezett.|
|70019|Az ellenőrzőkód lejárt. Ismételje meg a bejelentkezéskor a felhasználó rendelkezik.|
|70037|Helytelen válasz az ellenőrző kérdésre. Távoli hitelesítési munkamenet megtagadva.|
|75001|Hiba történt a SAML-üzenetkötés során.|
|75003|Az alkalmazás nem támogatott kötéssel kapcsolatos hibát adott vissza (a SAML-protokollválasz csak HTTP POST-kötésen keresztül küldhető el). Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|75005|Az Azure AD nem támogatja az alkalmazás egyszeri bejelentkezésre vonatkozó SAML-kérelmét. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|75008|A rendszer elutasította az alkalmazásból érkező kérelmet, mert a SAML-kérelem nem várt céllal rendelkezik. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|75011|A felhasználó által a szolgáltatásban való hitelesítésre használt hitelesítési mód nem egyezik a kért hitelesítési móddal. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|75016|A SAML2 hitelesítési kérelem érvénytelen NameIdPolicy értékkel rendelkezik. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|80001|A hitelesítési ügynök nem tudott csatlakozni az Active Directory-hoz. Győződjön meg arról, hogy a hitelesítési ügynök telepítve van egy olyan tartományhoz csatlakozó gépen, amely rálát a felhasználó bejelentkezési kérelmét kiszolgáló tartományvezérlőre.|
|80002|Belső hiba történt. A jelszó-ellenőrzési kérelem túllépte az időkorlátot. Nem sikerült elküldeni a hitelesítési kérést a belső hibrid identitásszolgáltatásnak. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez|
|80003|A hitelesítési ügynök érvénytelen választ kapott. Ismeretlen hiba történt a helyszíni Active Directoryban történő hitelesítésre tett kísérlet során. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez|
|80005|A hitelesítési ügynök: Ismeretlen hiba történt a válasz a hitelesítési ügynök feldolgozása közben. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez|
|80007|A hitelesítési ügynök nem tudta ellenőrizni a felhasználó jelszavát.|
|80010|A hitelesítési ügynök nem tudta visszafejteni a jelszót. |
|80011|A hitelesítési ügynök nem tudta lekérni a titkosítási kulcsot.|
|80012|A felhasználó próbált meg bejelentkezni kívül esik a megengedett óra (ez van megadva az ad-ben).|
|80013|A hitelesítési kísérlet a hitelesítési ügynököt és az AD-t futtató számítógép közötti időeltérés miatt nem sikerült. Javítsa ki az időszinkronizálási problémákat|
|80014|A hitelesítési ügynök túllépte az időkorlátot. [Hozzon létre egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) hibakód, korrelációs Azonosítót, és Datetime erről a hibáról további részletekért.|
|81001|A felhasználó Kerberos-jegye túl nagy. Ez akkor fordulhat elő, ha a felhasználó túl sok csoportban szerepel, és így a Kerberos-jegy túl sok csoporttagságot tartalmaz. Csökkentse a felhasználó csoporttagságait, majd próbálkozzon újra.|
|81005|Hitelesítési csomag nem támogatott.|
|81007|Bérlő közvetlen egyszeri bejelentkezés nincs engedélyezve.|
|81012|Ez nem hiba történik. Ez azt jelzi, hogy az Azure AD bejelentkezni próbáló felhasználó különbözik az eszközbe jelentkezett felhasználótól. Biztonságosan figyelmen kívül hagyhatja ezt a kódot a naplókat.|
|90010|A kérelem nem támogatott különböző okok miatt. Például a kérés használatával jön létre egy nem támogatott kérelmi metódus (csak POST metódus támogatott), vagy az, hogy a kért jogkivonatot aláíró algoritmus nem támogatott. Lépjen kapcsolatba az alkalmazás fejlesztőjével.|
|90014| Hiányzik egy kötelező mező protokoll üzenet, forduljon az alkalmazás tulajdonosa. Ha az alkalmazás tulajdonosa, győződjön meg arról, hogy rendelkezik-e a szükséges paramétereket, a bejelentkezési kérelem számára. |
|90072| A fióknak rendelkeznie kell adni a bérlőn külső felhasználóként először. Kijelentkezés és bejelentkezés újra egy másik Azure AD-fiókot.|
|90094| A támogatás rendszergazdai engedélyekkel kell rendelkeznie. Kérje meg a bérlői rendszergazda történő ehhez az alkalmazáshoz.|
|500133| A helyességi feltétel nem esik az érvényes idő. Győződjön meg arról, hogy a hozzáférési jogkivonat nem járt le, a felhasználó helyességi feltétel használata előtt, vagy kérje meg egy új jogkivonatot.|

## <a name="next-steps"></a>További lépések

* [Bejelentkezések jelentések áttekintése](concept-sign-ins.md)
* [Az Azure AD-jelentések programozás alapú hozzáférést](concept-reporting-api.md)
