---
title: Hibakódok a Azure Active Directory portálon | Microsoft Docs
description: Bejelentkezési tevékenységekre vonatkozó jelentések hibakódjainak referenciája.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/08/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: da2d598c7bb6d7b06e57dd497d1e2aebf1b63694
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898889"
---
# <a name="sign-in-activity-report-error-codes"></a>Bejelentkezési tevékenység jelentésének hibakódja 

A [felhasználói bejelentkezési jelentés](concept-sign-ins.md)által nyújtott információk alapján a következő kérdésekre kaphat választ:

- Ki jelentkezett be az alkalmazásba?
- Mely alkalmazások voltak bejelentkezve?
- Mely bejelentkezések sikertelenek és miért?

Ha a bejelentkezés sikertelen, akkor a hibának megfelelő hibakód jelenik meg. Ez a cikk a hibakódokat és azok leírásait sorolja fel, valamint adott esetben a javasolt lépéseket. 

## <a name="how-can-i-display-failed-sign-ins"></a>Hogyan tudom megjeleníteni a sikertelen bejelentkezéseket? 

A [Azure Portal](https://portal.azure.com) menüben válassza a **Azure Active Directory**lehetőséget, vagy keresse meg és válassza ki az **Azure Active Directory** elemet bármelyik oldalon.

![Azure Active Directory kiválasztása](./media/reference-sign-ins-error-codes/select-azure-active-directory.png "Azure Active Directory")

A **figyelés**területen válassza a **bejelentkezések** lehetőséget a [bejelentkezések jelentés](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)megnyitásához.

![Bejelentkezési tevékenység](./media/reference-sign-ins-error-codes/monitoring-sign-ins-in-azure-active-directory.png "Bejelentkezési tevékenység")

Szűrje a jelentést úgy, hogy az összes sikertelen bejelentkezés megjelenjen, ha a **bejelentkezési állapot** legördülő listából kiválasztja a **hiba** lehetőséget.

![Bejelentkezési tevékenység](./media/reference-sign-ins-error-codes/06.png "Bejelentkezési tevékenység")

Ha kiválaszt egy elemet a szűrt listából, megnyílik a **tevékenység részletei: bejelentkezések panel** . Ez a nézet további információkat biztosít a sikertelen bejelentkezési eseményről, beleértve a **bejelentkezési hibakódot** és a **hiba okát**.

![Bejelentkezési tevékenység](./media/reference-sign-ins-error-codes/05.png "Bejelentkezési tevékenység")

A [jelentéskészítési API](concept-reporting-api.md)használatával programozott módon is elérheti a bejelentkezési adataikat.

## <a name="error-codes"></a>Hibakódok


|Hiba|Leírás|
|---|---|
|16000|Ez egy belső implementáció részletei, és nem a hiba feltétele. Ezt a hivatkozást nyugodtan figyelmen kívül hagyhatja.|
|20001|Probléma lépett fel az összevont identitásszolgáltatóval kapcsolatban. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával.|
|20012|Probléma lépett fel az összevont identitásszolgáltatóval kapcsolatban. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával.|
|20033|Probléma lépett fel az összevont identitásszolgáltatóval kapcsolatban. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával.|
|40008|Probléma lépett fel az összevont identitásszolgáltatóval kapcsolatban. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával.|
|40009|Probléma lépett fel az összevont identitásszolgáltatóval kapcsolatban. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával.|
|40014|Probléma lépett fel az összevont identitásszolgáltatóval kapcsolatban. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával.|
|50000|Probléma lépett fel a bejelentkezési szolgáltatással kapcsolatban. [Hozzon létre támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a probléma megoldásához.|
|50001|A szolgáltatásnév nem található ebben a bérlőben. Ez akkor fordulhat elő, ha az alkalmazást nem a bérlő rendszergazdája telepítette, vagy ha az egyszerű erőforrás nem található a címtárban, vagy érvénytelen.|
|50002|A bejelentkezés a bérlő korlátozott proxyhozzáférése miatt nem sikerült. Ha saját bérlői szabályzata van, a probléma megoldásához módosíthatja a korlátozott bérlői beállításokat.|
|50003|A bejelentkezés hiányzó aláírókulcs vagy tanúsítvány miatt nem sikerült. Ez akkor fordulhat elő, ha az alkalmazásban nem volt konfigurálva aláírókulcs. Tekintse meg a következő helyen ismertetett megoldásokat: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Ha a probléma továbbra is fennáll, lépjen kapcsolatba az alkalmazás tulajdonosával vagy az alkalmazás rendszergazdájával.|
|50005|A felhasználó olyan platformról próbált meg bejelentkezni egy eszközre, amelyet jelenleg nem támogat a feltételes hozzáférési házirend.|
|50006| Az aláírás ellenőrzése érvénytelen aláírás miatt sikertelen volt. Tekintse meg a következő helyen ismertetett megoldást: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Ha a probléma továbbra is fennáll, forduljon az alkalmazás tulajdonosához vagy az alkalmazás rendszergazdájához.|
|50007|Ehhez az alkalmazáshoz nem található partnertitkosítási tanúsítvány. [Nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a Microsofttal a rögzített megoldás eléréséhez.|
|50008|Hiányzik a SAML helyességi feltétel, vagy rosszul van konfigurálva a jogkivonatban. Forduljon az összevonási szolgáltatójához.|
|50010|A célközönség URI-ellenőrzése sikertelen volt az alkalmazás esetében, mert a jogkivonat célközönsége nincs konfigurálva. Forduljon az alkalmazás tulajdonosához a feloldáshoz.|
|50011|A válaszcím hiányzik, rosszul van konfigurálva, vagy nem egyezik az alkalmazáshoz konfigurált válaszcímekkel. Próbálja ki a következő helyen felsorolt felbontást: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Ha a probléma továbbra is fennáll, forduljon az alkalmazás tulajdonosához vagy az alkalmazás rendszergazdájához.|
|50012| Ez egy általános hibaüzenet, amely azt jelzi, hogy a hitelesítés sikertelen volt. Ez olyan okok miatt fordulhat elő, mint például a hiányzó vagy érvénytelen hitelesítő adatok vagy jogcímek a kérelemben. Győződjön meg arról, hogy a kérést a megfelelő hitelesítő adatokkal és jogcímekkel küldi el a rendszer. |
|50013|Az állítás különböző okok miatt érvénytelen. Például a jogkivonat kiállítója nem felel meg az API-verziónak az érvényes időtartományon belül, a jogkivonat lejárt vagy helytelen formátumú, vagy az állításban szereplő frissítési jogkivonat nem elsődleges frissítési jogkivonat.|
|50017|Az érvényesítés nem sikerült az alábbi okok egyike miatt:<ul><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A várt CrlSegment nem található</li><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A különbözeti CRL terjesztési pontja megfelelő CRL terjesztési pont nélkül lett konfigurálva</li><li>időtúllépési hiba miatt nem sikerült érvényes CRL-szegmenseket lekérni</li><li>A CRL letöltése nem sikerült</li></ul>Forduljon a bérlői rendszergazdához.|
|50020|A felhasználó a következő okok egyike miatt nem engedélyezett.<ul><li>A felhasználó egy MSA-fiókkal próbál bejelentkezni a v1-es végponttal</li><li>A felhasználó nem létezik a bérlőben.</li></ul> Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50027|A JWT-jogkivonat az alábbi okok miatt érvénytelen:<ul><li>nem tartalmazza az egyszeri kulcs jogcímét vagy a tárgy jogcímét</li><li>tárgyazonosító eltérése</li><li>dupla jogcím az idToken-jogcímekben</li><li>nem várt kiállító</li><li>nem várt célközönség</li><li>nem az érvényes időtartományon belül van </li><li>a jogkivonat formátuma nem megfelelő</li><li>A kiállító külső azonosító jogkivonata nem felelt meg az aláírás-ellenőrzésen.</li></ul>Lépjen kapcsolatba az alkalmazás tulajdonosával|
|50029|Érvénytelen URI – a tartománynév érvénytelen karaktert tartalmaz. Forduljon a bérlői rendszergazdához.|
|50034|A felhasználó nem létezik a címtárban. Forduljon a bérlői rendszergazdához.|
|50042|A páros-azonosító létrehozásához szükséges sót a rendszer elvileg hiányzik. Forduljon a bérlői rendszergazdához.|
|50048|A tárgy nem egyezik meg a kiállító jogcímmel az ügyfél helyességi feltételében. Forduljon a bérlői rendszergazdához.|
|50050|A kérelem nem megfelelő formátumú. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50053|A fiók zárolva van, mert a felhasználó túl sokszor próbált meg bejelentkezni helytelen felhasználói AZONOSÍTÓval vagy jelszóval.|
|50055|Érvénytelen jelszó, lejárt jelszót írt be.|
|50056|Érvénytelen vagy Null jelszó-a jelszó nem létezik a tárolóban ehhez a felhasználóhoz.|
|50057|A felhasználói fiók le van tiltva. Egy rendszergazda letiltotta a fiókot.|
|50058|Az alkalmazást csendes bejelentkezést próbált meg végrehajtani, de a felhasználót nem lehetett csendesen bejelentkeztetni. Az alkalmazásnak olyan interaktív folyamatot kell elindítania, amely lehetővé teszi a felhasználóknak a bejelentkezést. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50059|A felhasználó nem létezik a címtárban. Forduljon a bérlői rendszergazdához.|
|50061|A kijelentkezési kérés érvénytelen. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50072|A felhasználónak regisztrálnia kell a kétfaktoros hitelesítést (interaktív).|
|50074|A felhasználó nem adott jó választ az MFA-kérdésre.|
|50076|A felhasználó nem adta át az MFA-feladatot (nem interaktív).|
|50078|A bemutatott multi-Factor Authentication lejárt, frissítenie kell a multi-Factor Authenticationt a hozzáféréshez.|
|50079|A felhasználónak regisztrálnia kell két faktoros hitelesítést (nem interaktív bejelentkezések).|
|50085|A frissítési jogkivonathoz közösségi identitásszolgáltatói bejelentkezés szükséges. A felhasználó újra bejelentkezik a felhasználónevével és jelszavával.|
|50089|A flow-token lejárt – a hitelesítés sikertelen volt. A felhasználónévvel és a jelszóval próbálja újra bejelentkezni a felhasználóval|
|50097|Az eszköz hitelesítése szükséges. Ez azért fordulhat elő, mert a DeviceId vagy DeviceAltSecId jogcímek **Null értékűek**, vagy ha nem létezik az eszköz azonosítójának megfelelő eszköz.|
|50099|A JWT-aláírás érvénytelen. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50105|A bejelentkezett felhasználó nincs hozzárendelve a bejelentkezett alkalmazás egyik szerepköréhez sem. Rendelje hozzá a felhasználót az alkalmazáshoz. További információ: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|A kért összevonásitartomány-objektum nem létezik. Forduljon a bérlői rendszergazdához.|
|50120|JWT-fejléchiba. Forduljon a bérlői rendszergazdához.|
|50124|A jogcím-átalakítás érvénytelen bemeneti paramétert tartalmaz. A szabályzat frissítéséhez vegye fel a kapcsolatot a bérlő rendszergazdájával.|
|50125|A bejelentkezés a jelszó-visszaállítási vagy a jelszó-regisztrációs bejegyzés miatt megszakadt.|
|50126|A Felhasználónév vagy a jelszó érvénytelen, vagy a helyszíni Felhasználónév vagy jelszó érvénytelen.|
|50127|A felhasználónak telepítenie kell egy közvetítő alkalmazást, hogy hozzáférjen ehhez a tartalomhoz.|
|50128|Érvénytelen tartománynév – a kérelemben vagy a megadott hitelesítő adatokban nem szereplő bérlő által azonosított információk nem találhatók.|
|50129|Az eszköz nincs munkahelyhez csatlakoztatva – a **munkahelyi csatlakoztatás** szükséges az eszköz regisztrálásához.|
|50130|A jogcím értéke nem értelmezhető ismert hitelesítési módszerként.|
|50131|Különféle feltételes hozzáférési hibákban használatos. Például Rossz Windows-eszközállapot, a kérés gyanús tevékenység miatt blokkolva, a hozzáférési szabályzattal és a biztonsági szabályzattal kapcsolatos döntések.|
|50132|A hitelesítő adatok vissza lettek vonva az alábbi okok miatt:<ul><li>Az SSO-összetevő érvénytelen vagy lejárt</li><li>A munkamenet nem elég friss az alkalmazáshoz</li><li>A rendszer csendes bejelentkezési kérelmet küldött, de a felhasználó Azure AD-munkamenete érvénytelen vagy lejárt.</li></ul>|
|50133|A jelszó lejárta vagy nemrégi módosítása miatt a munkamenet érvénytelen.|
|50135|A jelszó módosítása a fiók kockázata miatt szükséges.|
|50136|MSA-munkamenet átirányítása az alkalmazásba – egyetlen MSA-munkamenetet észlelt a rendszer. |
|50140|Ez a hiba a „Bejelentkezve szeretnék maradni” megszakítás miatt lépett fel, miközben a felhasználó megpróbált bejelentkezni. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a korrelációs azonosítóval, a kérelemazonosítóval és a hibakóddal a részletek megismeréséhez. |
|50143|A munkamenet nem egyezik – a munkamenet érvénytelen, mert a felhasználói bérlő nem felel meg a tartománynak a különböző erőforrások miatti célzásnak. További részletekért  [Nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) KORRELÁCIÓs azonosítóval, kérelem-azonosítóval és hibakódtal.|
|50144|A felhasználó Active Directory jelszava lejárt. Hozzon létre egy új jelszót a felhasználó számára, vagy használja a végfelhasználót az önkiszolgáló visszaállítási eszköz használatával.|
|50146|Ezt az alkalmazást egy alkalmazásspecifikus bejelentkezési kulccsal kell konfigurálni. Az alkalmazás nem ilyennel van konfigurálva, vagy a kulcs lejárt vagy még nem érvényes. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|50148|A code_verifier nem egyezik meg a PKCE hitelesítési kérésben megadott code_challenge értékkel. Forduljon az alkalmazás fejlesztőihez. |
|50155|Ehhez a felhasználóhoz nem sikerült az eszköz hitelesítése.|
|50158|A külső biztonsági probléma nem teljesült.|
|50161|A külső szolgáltató által eljuttatott jogcímek nem elegendőek, vagy hiányoznak a külső szolgáltatóhoz kért jogcímek.|
|50166|Nem sikerült elküldeni a kérést a jogcím-szolgáltatónak.|
|50169|A tartomány nem az aktuális szolgáltatásnévtér konfigurált tartománya.|
|50172|A külső jogcímszolgáltató nincs jóváhagyva. Kapcsolatfelvétel a bérlői rendszergazdával|
|50173|Friss hitelesítési jogkivonat szükséges. A felhasználó újra be kell jelentkeznie a friss hitelesítő adatok használatával.|
|50177|A külső kihívás áteresztő felhasználók számára nem támogatott.|
|50178|A munkamenet-vezérlő továbbító felhasználók számára nem támogatott.|
|50180|Integrált Windows-hitelesítés szükséges. Engedélyezze a Seamless SSO-t a bérlőn.|
|50181|Az OTP-hez kapcsolódó hiba a bejelentkezés során. |
|50201|Ez az üzenet jelenik meg a felhasználó bejelentkezésekor, amikor további információt kell megadni a felhasználónak.|
|51001|A tartományi útmutató nem található a helyszíni biztonsági azonosító – helyszíni egyszerű felhasználónév szolgáltatással.|
|51004|A felhasználói fiók nem létezik a címtárban.|
|51006|Integrált Windows-hitelesítés szükséges. A felhasználó a jogcím alapján hiányzó munkamenet-jogkivonat használatával jelentkezett be. A felhasználónak újra be kell jelentkeznie.|
|52004|A felhasználó nem járult hozzá a LinkedIn-erőforrásokhoz való hozzáféréshez. |
|53000|A feltételes hozzáférési szabályzathoz megfelelő eszköz szükséges, és az eszköz nem megfelelő. A felhasználó regisztrálja az eszközét egy jóváhagyott MDM-szolgáltatóval, például az Intune-nal.|
|53001|A feltételes hozzáférési szabályzathoz tartományhoz csatlakoztatott eszköz szükséges, és az eszköz nincs tartományhoz csatlakoztatva. A felhasználó egy tartományhoz csatlakoztatott eszközt használ.|
|53002|A használt alkalmazás nem jóváhagyott alkalmazás a feltételes hozzáféréshez. A felhasználónak a jóváhagyott alkalmazások listáján szereplő alkalmazást kell használnia a hozzáféréshez.|
|53003|A hozzáférés a feltételes hozzáférési házirendek miatt le lett tiltva.|
|53004|A tartalom elérése előtt a felhasználónak el kell végeznie a többtényezős hitelesítés regisztrációs folyamatát. A felhasználónak regisztrálnia kell többtényezős hitelesítésre.|
|53032|Az akusztikai Azure AD Identity Protection házirendek miatt le lett tiltva.|
|65001|Az X alkalmazás nem rendelkezik engedéllyel az Y alkalmazás eléréséhez, vagy az engedély vissza lett vonva. Vagy: a felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás X azonosítóval való használatába. Küldjön egy interaktív engedélyezési kérést ehhez a felhasználóhoz és erőforráshoz. Vagy: a felhasználó vagy a rendszergazda nem egyezett bele az alkalmazás X azonosítóval való használatába. Küldjön egy engedélyezési kérést a bérlő rendszergazdájának, hogy az Y alkalmazás nevében végezzen tevékenységet a Z erőforráson.|
|65004|A felhasználó elutasította az alkalmazáshoz való hozzáférés jóváhagyását. Kérje meg a felhasználót, hogy jelentkezzen be újra, és hagyja jóvá az alkalmazást|
|65005|Az alkalmazás által igényelt erőforrás-hozzáférési lista nem tartalmaz az erőforrás által felderíthető alkalmazásokat, vagy az ügyfélalkalmazás olyan erőforrás hozzáférését kérte, amely nincs meghatározva a szükséges erőforrás-hozzáférési listán, vagy a Graph szolgáltatás rossz kérést adott vissza, vagy az erőforrás nem található. Ha az alkalmazás támogatja az SAML-t, akkor előfordulhat, hogy nem a megfelelő azonosítóval (entitással) konfigurálta az alkalmazást. Próbálja ki az SAML-lel kapcsolatos megoldásokat, amelyek a következő hivatkozáson találhatók: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|A hozzáférés érvénytelen az alábbi okok miatt:<ul><li>A szükséges SAML 2.0 helyességi feltétel érvénytelen tárgyjóváhagyási metódussal rendelkezik</li><li>Az alkalmazás OnBehalfOf folyamata nem támogatott a V2-ben</li><li>Elsődleges frissítési jogkivonat nincs aláírva a munkamenetkulccsal</li><li>Érvénytelen külső frissítési jogkivonat</li><li>A megadott hozzáférés egy másik bérlőhöz lett beszerezve.</li></ul>|
|70001|Az X nevű alkalmazás nem található az Y nevű bérlőben. Ez akkor történhet, ha az X azonosítójú alkalmazást nem a bérlő rendszergazdája telepítette, vagy nem fogadta el egy felhasználó sem a bérlőben. Előfordulhat, hogy helytelenül konfigurálta az alkalmazás azonosító értékét, vagy nem a megfelelő bérlőnek küldi el a hitelesítési kérést.|
|70002|Az alkalmazás érvénytelen ügyfél-hitelesítő adatokat adott vissza. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|70003|Az alkalmazás nem támogatott hozzáférési típust adott vissza. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|70004|Az alkalmazás nem támogatott átirányítási URI-t adott vissza. Az ügyfél által megadott átirányítási cím nem egyezik meg egyik konfigurált címmel sem, illetve az OIDC jóváhagyási listán szereplő címekkel sem. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|70005|Az alkalmazás nem támogatott választípust adott vissza az alábbi okok miatt:<ul><li>A „jogkivonat” választípus nincs engedélyezve az alkalmazás számára</li><li>Az id_token típusú válaszhoz az OpenID hatókör szükséges – a kódolt wctx nem támogatott OAuth paraméterértéket tartalmaz</li></ul>Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|70007|Az alkalmazás a nem támogatott response_mode értéket adta vissza egy jogkivonat kérésekor. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|70008|A megadott engedélyezési kód vagy frissítési jogkivonat lejárt vagy vissza lett vonva. A felhasználó újrapróbálkozik a bejelentkezéssel.|
|70011|Az alkalmazás által kért hatókör érvénytelen. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|70012|Kiszolgálóhiba történt az MSA-felhasználó (fogyasztó) hitelesítése során. Próbálja megismételni a bejelentkezést, és ha nem szűnik meg a probléma, [Nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Érvénytelen ellenőrzőkód: a felhasználó rossz felhasználói kódot írt be az eszközkód-folyamathoz. Az engedélyezés nincs jóváhagyva.|
|70019|Az ellenőrzőkód lejárt. A felhasználó próbálja megismételni a bejelentkezést.|
|70037|Helytelen válasz az ellenőrző kérdésre. Távoli hitelesítési munkamenet megtagadva.|
|70043|Az Azure feltételes hozzáférés munkamenet-kezelésének hatására a munkamenet lejár|
|70044|Az Azure feltételes hozzáférés munkamenet-kezelésének hatására a munkamenet lejár|
|75001|Hiba történt a SAML-üzenetkötés során.|
|75003|Az alkalmazás nem támogatott kötéssel kapcsolatos hibát adott vissza (a SAML-protokollválasz csak HTTP POST-kötésen keresztül küldhető el). Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|75005|Az Azure AD nem támogatja az alkalmazás egyszeri bejelentkezésre vonatkozó SAML-kérelmét. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|75008|A rendszer elutasította az alkalmazásból érkező kérelmet, mert a SAML-kérelem nem várt céllal rendelkezik. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|75011|A felhasználó által a szolgáltatásban való hitelesítésre használt hitelesítési mód nem egyezik a kért hitelesítési móddal. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|75016|A SAML2 hitelesítési kérelem érvénytelen NameIdPolicy értékkel rendelkezik. Lépjen kapcsolatba az alkalmazás tulajdonosával.|
|80001|A hitelesítési ügynök nem tudott csatlakozni az Active Directory-hoz. Győződjön meg arról, hogy a hitelesítési ügynök telepítve van egy olyan tartományhoz csatlakozó gépen, amely rálát a felhasználó bejelentkezési kérelmét kiszolgáló tartományvezérlőre.|
|80002|Belső hiba történt. A jelszó-érvényesítési kérelem időkorlátja lejárt. Nem sikerült elküldeni a hitelesítési kérést a belső hibrid identitási szolgáltatásnak. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez|
|80003|A hitelesítési ügynök érvénytelen választ kapott. Ismeretlen hiba történt a helyszíni Active Directoryban történő hitelesítésre tett kísérlet során. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez|
|80005|Hitelesítési ügynök: Ismeretlen hiba történt a Hitelesítési ügynöktől érkező válasz feldolgozásakor. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez|
|80007|A hitelesítési ügynök nem tudta ellenőrizni a felhasználó jelszavát.|
|80010|A hitelesítési ügynök nem tudta visszafejteni a jelszót. |
|80011|A hitelesítési ügynök nem tudta lekérni a titkosítási kulcsot.|
|80012|A felhasználók az engedélyezett órákon kívül próbáltak bejelentkezni (ez az AD-ben van megadva).|
|80013|A hitelesítési kísérlet a hitelesítési ügynököt és az AD-t futtató számítógép közötti időeltérés miatt nem sikerült. Javítsa ki az időszinkronizálási problémákat|
|80014|A hitelesítési ügynök időkorlátja lejárt. A hibával kapcsolatos további részletekért [Nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a hibakódtal, a KORRELÁCIÓs azonosítóval és a datetime értékkel.|
|81001|A felhasználó Kerberos-jegye túl nagy. Ez akkor fordulhat elő, ha a felhasználó túl sok csoportban szerepel, és így a Kerberos-jegy túl sok csoporttagságot tartalmaz. Csökkentse a felhasználó csoporttagságait, majd próbálkozzon újra.|
|81005|A hitelesítési csomag nem támogatott.|
|81007|A bérlő nincs engedélyezve a zökkenőmentes egyszeri bejelentkezéshez.|
|81012|Ez nem egy hiba feltétele. Azt jelzi, hogy az Azure AD-be bejelentkezni próbáló felhasználó különbözik az eszközbe bejelentkezett felhasználótól. A naplókban biztonságosan figyelmen kívül hagyhatja ezt a kódot.|
|90010|A kérés különböző okokból nem támogatott. A kérést például nem támogatott kérési módszer használatával (csak a POST metódus támogatott) vagy a kért jogkivonat-aláírási algoritmus nem támogatja. Lépjen kapcsolatba az alkalmazás fejlesztőjével.|
|90014| A protokoll üzeneteihez kötelező mező hiányzik, lépjen kapcsolatba az alkalmazás tulajdonosával. Ha Ön az alkalmazás tulajdonosa, ellenőrizze, hogy rendelkezik-e az összes szükséges paraméterrel a bejelentkezési kéréshez. |
|90051| Érvénytelen delegálási jogkivonat. Érvénytelen nemzeti felhő-azonosító ({cloudId}) van megadva.|
|90072| Először hozzá kell adni a fiókot külső felhasználóként a bérlőhöz. Jelentkezzen ki, majd jelentkezzen be újra egy másik Azure AD-fiókkal.|
|90094| Az alkalmazás olyan engedélyeket kért, amelyeknek a bejelentkezett felhasználó nem jogosult beleegyezni a szolgáltatásba, és a felhasználó le lett tiltva. |
|90095| Az alkalmazás olyan engedélyeket kért, amelyeknek a bejelentkezett felhasználó nem jogosult beleegyezni a szolgáltatásba, és a felhasználó megmutatta a [rendszergazdai engedélyezési kérelem](../manage-apps/configure-admin-consent-workflow.md) űrlapját. |
|500011| Az <site address> nevű egyszerű erőforrás nem található a (z) <tenant ID>nevű bérlőben. Ez akkor fordulhat elő, ha az alkalmazást nem a bérlő rendszergazdája telepítette, vagy a bérlő bármelyik felhasználója beleegyezett. Elképzelhető, hogy rossz bérlőhöz küldte a hitelesítési kérést.|
|500021| A bérlőt a vállalati proxy korlátozza. Az erőforrás-hozzáférés megtagadása.|
|500121| Az erős hitelesítési kérelem során sikertelen volt a hitelesítés.|
|500133| Az érvényesítés nem az érvényes időtartományán belül van. Győződjön meg arról, hogy a hozzáférési jogkivonat nem járt le, mielőtt felhasználói állításhoz használta, vagy kérjen új jogkivonatot.|
|530021|Az alkalmazás nem felel meg a feltételes hozzáférés jóváhagyott alkalmazási követelményeinek.|
|530032|Biztonsági házirend tiltja.| 
|700016|A (z) {appIdentifier} azonosítójú alkalmazás nem található a (z) {tenantName} címtárban. Ez akkor fordulhat elő, ha az alkalmazást nem a bérlő rendszergazdája telepítette, vagy a bérlő bármelyik felhasználója beleegyezett. Lehetséges, hogy nem megfelelő bérlőnek küldi el a hitelesítési kérést.|
|900432|A bizalmas ügyfél nem támogatott a felhőalapú kérelemben.|
|5000811|Nem ellenőrizhető az SAML-jogkivonat aláírása. Az aláíró kulcs azonosítója nem egyezik az érvényes regisztrált kulcsokkal.|
|7000215|Érvénytelen ügyfél-titkos kulcs lett megadva.|
|7000218|A kérelem törzsének a következő paramétert kell tartalmaznia: "client_assertion" vagy "client_secret".|


## <a name="next-steps"></a>Következő lépések

* [A bejelentkezések jelentéseinek áttekintése](concept-sign-ins.md)
* [Programozott hozzáférés az Azure AD-jelentésekhez](concept-reporting-api.md)
