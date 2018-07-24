---
title: Az Azure Active Directory átmenő hitelesítés a biztonság részletes bemutatása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan védi az Azure Active Directory (Azure AD) átmenő hitelesítése a a helyszíni fiókok
services: active-directory
keywords: Az Azure AD Connect az átmenő hitelesítés, Active Directory telepítése szükséges összetevők SSO, Azure AD egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ad4567ffb927694872d5b86dd38833466f944ca8
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215084"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Az Azure Active Directory átmenő hitelesítés a biztonság részletes bemutatása

Ez a cikk az Azure Active Directory (Azure AD) átmenő hitelesítés működéséről további részletes leírását. A szolgáltatás biztonsági vonatkozásai összpontosít. Ez a cikk a biztonsági és informatikai rendszergazdák, vezető megfelelőségi és biztonsági tisztviselők, és más informatikai szakemberek számára felelős informatikai biztonsági és megfelelőségi, kis és közepes méretű szervezetek számára, vagy a nagyobb cégeknek is.

A tárgyalt témakörök a következők:
- Hogyan kell telepíteni és regisztrálása a hitelesítési ügynökök részletes technikai információkat.
- Felhasználói bejelentkezés során a titkosítási jelszavak újbóli használatának részletes technikai információkat.
- A csatornák között biztonságát a helyszíni hitelesítési ügynökök és az Azure ad-ben.
- Részletes technikai információ a hitelesítési ügynökök az illesztő biztonságának megőrzéséhez.
- Biztonsággal kapcsolatos témaköröket.

## <a name="key-security-capabilities"></a>Fontos biztonsági képességgel

Ez a funkció a kulcs biztonsági beállításait az alábbiak:
- El vannak különítve a bejelentkezési kérelmek, a bérlők között biztonságos, a több-bérlős architektúra alapján készült.
- A helyszíni jelszavak soha nem a felhőben, bármilyen formában vannak tárolva.
- A helyszíni hitelesítési ügynökök, amelyek figyelik, és reagálni, a jelszó érvényesítése kérelmek tétele csak a kimenő kapcsolatokat a hálózaton belül. Esetében nem követelmény a szegélyhálózaton (DMZ) hitelesítési ügynökök telepítéséhez. Ajánlott eljárásként kezeljük az összes olyan kiszolgálóalkalmazást futtató hitelesítési ügynökök, a Tier 0 rendszerek (lásd: [referencia](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Csak szabványos portot (80-as és 443-as) az Azure AD a hitelesítési ügynököktől származó kimenő kommunikációra szolgálnak. Nem kell megnyitni a bejövő portra a tűzfalon. 
  - 443-as port az összes hitelesített kimenő kommunikációra szolgál.
  - 80-as port szolgál csak a visszavont tanúsítványok listájának (CRL) letöltése esetén győződjön meg arról, hogy ez a szolgáltatás által használt tanúsítványok egyike visszavonták.
  - A hálózati követelmények teljes listáját lásd: [Azure Active Directory átmenő hitelesítés: gyors üzembe helyezési](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).
- Hogy a felhasználók a bejelentkezés során adja meg a felhőben vannak titkosítva, előtt a helyszíni hitelesítési ügynökök fogadja el az Active Directory érvényesítésre.
- Az Azure AD között a HTTPS-csatorna és a helyszíni hitelesítési ügynök a kölcsönös hitelesítés védi.
- A felhasználói fiókokhoz védi zökkenőmentesen dolgozik [Azure AD feltételes hozzáférési szabályzatok](../active-directory-conditional-access-azure-portal.md), többek között a multi-factor Authentication (MFA), [örökölt hitelesítés](../active-directory-conditional-access-conditions.md) , illetve [ kiszűri a találgatásos jelszó támadások](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Részt vevő összetevők

További működési, Azure AD szolgáltatás és adat biztonsági kapcsolatos általános információkért lásd: a [biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/). A következő összetevőket is igényel, a felhasználói bejelentkezés az átmenő hitelesítés használatakor:
- **Az Azure AD-STS**: állapot nélküli biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS), amely feldolgozza a bejelentkezési kérelmek és a biztonsági jogkivonatokat bocsát felhasználói böngészők, ügyfelek vagy szolgáltatásokhoz szükség szerint.
- **Az Azure Service Bus**: biztosítja a felhőalapú vállalati üzenetkezeléssel és továbbítók kommunikációt, amely segítséget nyújt a helyszíni megoldások csatlakoztatása a felhő segítségével.
- **Az Azure AD Connect hitelesítési ügynökének**: egy a helyszíni összetevő, amely figyeli és a jelszó érvényesítése kérésekre válaszol.
- **Az Azure SQL Database**: a bérlő hitelesítési ügynökök, ideértve a metaadatok és a titkosítási kulcsok kapcsolatos információkat tartalmazza.
- **Az Active Directory**: A helyszíni Active Directory, a felhasználói fiókokhoz és a jelszavukat tárolására.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Telepítési és regisztrációs a hitelesítési ügynökök

Hitelesítési ügynökök telepítése és az Azure ad-vel regisztrált során, vagy:
   - [Az Azure AD átmenő hitelesítés engedélyezése csatlakoztatása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [A bejelentkezési kérelmek magas rendelkezésre állásának biztosításához a további hitelesítési ügynökök hozzáadása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
A hitelesítési ügynök működő első három fő fázisból foglalja magában:

1. Hitelesítési ügynök telepítése
2. Hitelesítési ügynök regisztrációja
3. Hitelesítési ügynök inicializálása

A következő szakaszok ezeket a fázisokat részletesen tárgyalja.

### <a name="authentication-agent-installation"></a>Hitelesítési ügynök telepítése

Csak a globális rendszergazdák hitelesítési ügynök (használatával telepítheti az Azure AD Connect vagy önálló) egy helyszíni kiszolgálón. Telepítés két új bejegyzést, hogy hozzáadja a **Vezérlőpult** > **programok** > **programok és szolgáltatások** lista:
- A hitelesítési ügynök alkalmazás saját maga. Az alkalmazás fut a [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) jogosultságokkal.
- A frissítési alkalmazás, amelynek a hitelesítési ügynök automatikus frissítéséhez. Az alkalmazás fut a [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) jogosultságokkal.

### <a name="authentication-agent-registration"></a>Hitelesítési ügynök regisztrációja

A hitelesítési ügynök telepítése után kell az Azure ad-ben regisztrálja magát. Az Azure AD minden hitelesítési ügynök a biztonságos kommunikáció érdekében az Azure ad-vel használhat egyedi, a digitális-identity tanúsítványt rendeli hozzá.

A regisztrációs eljárást is összeköti a hitelesítési ügynök a bérlővel. Ez biztosítja, hogy Azure ad-ben tudja, hogy az adott hitelesítési ügynök-e az egyetlen, a bérlőhöz tartozó jelszó ellenőrzési kérések kezelésére jogosult. Ez az eljárás megismétlődik minden egyes új hitelesítési ügynök regisztrálását.

A hitelesítési ügynökök a következő lépések segítségével regisztrálhatják magukat az Azure ad-vel:

![Az ügynök regisztrálása](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure ad-ben először kérelmez, hogy egy globális rendszergazda jelentkezzen be az Azure AD hitelesítő adataikkal. Bejelentkezés, során a hitelesítési ügynök szerez be egy hozzáférési jogkivonatot, amely képes használni a globális rendszergazda nevében.
2. A hitelesítési ügynök ezután létrehoz egy kulcspárt: egy nyilvános kulcs és titkos kulcsot.
    - A kulcspárt szokásos RSA 2048-bites titkosítás révén jön létre.
    - A titkos kulcs a helyszíni kiszolgálón marad, ahol a hitelesítési ügynök található.
3. A hitelesítési ügynök "regisztráció" kérelmet küld az Azure AD-HTTPS,-kapcsolaton keresztül a kérelem tartalmazza a következő összetevőkkel:
    - A hozzáférési jogkivonat beszerzése az 1. lépésben.
    - A 2. lépésben létrehozott nyilvános kulcsot.
    - Tanúsítvány-aláírási kérelem (CSR-fájl vagy tanúsítványkérelem). Ehhez a kérelemhez érvényes digitális azonosító tanúsítvány, az Azure AD-t a hitelesítésszolgáltató (CA).
4. Az Azure AD ellenőrzi a hozzáférési jogkivonatot a regisztrációs kérelem, és ellenőrzi, hogy a kérés érkezett egy globális rendszergazdától.
5. Ezután az Azure AD jelentkezik, és a egy digitális identitásokat tanúsítványt küld vissza a hitelesítési ügynök.
    - Az Azure ad-ben a legfelső szintű hitelesítésszolgáltató a tanúsítvány aláírására használatos. 

     >[!NOTE]
     > A hitelesítésszolgáltató _nem_ Windows megbízható legfelső szintű tanúsítványok tárolójában tárolja.
    - A hitelesítésszolgáltató csak az átmenő hitelesítés szolgáltatást használják. A hitelesítésszolgáltató van csak aláírásához használt tanúsítvány-aláírási kérelmeinek a hitelesítési ügynök regisztrációja során.
    -  A CA használja a további Azure AD-szolgáltatások egyike.
    - A tanúsítvány tulajdonosának (megkülönböztető nevét vagy megkülönböztető) értéke a bérlő azonosítója. A DN-t egy GUID Azonosítót, amely egyedileg azonosítja a bérlő. A DN-t a tanúsítványt a bérlő csak a hatóköröket.
6. Az Azure AD a hitelesítési ügynök nyilvános kulcsát egy Azure SQL database, amely hozzáféréssel rendelkezik az Azure AD csak tárolja.
7. A Windows-tárolóban található a helyi kiszolgálón tárolja a tanúsítványát (5. lépésében) (a kifejezetten a [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) helye). A hitelesítési ügynök és a frissítési alkalmazások egyaránt szolgál.

### <a name="authentication-agent-initialization"></a>Hitelesítési ügynök inicializálása

A hitelesítési ügynök indulásakor az első alkalommal egy kiszolgáló vagy regisztráció után indítsa újra, biztonságosan kommunikál az Azure AD szolgáltatással, és fogadja a kéréseket a jelszó érvényesítése elindításához szükséges.

![Az ügynök inicializálása](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Itt látható, milyen hitelesítési ügynökök inicializálva van:

1. A hitelesítési ügynök egy kimenő rendszerindítási kérelmet küld az Azure ad-hez. 
    - A kérelem 443-as porton keresztül történik, és a egy kölcsönösen hitelesített csatornán keresztül. A kérelem a hitelesítési ügynök regisztrációja során korábban kiadott tanúsítványt használja.
2. Az Azure AD válaszol a kérésre azáltal, hogy egy hozzáférési kulcsot az Azure Service Bus-üzenetsor, amely egyedinek és, amely azonosítja a bérlő azonosítója.
3. A hitelesítési ügynök lehetővé teszi, hogy egy állandó kimenő HTTPS-kapcsolat (a 443-as porton) keresztül az üzenetsorba. 
    - A hitelesítési ügynök jelszó-ellenőrzési kérések kezelésére és beolvashatók készen áll.

Ha több hitelesítési ügynök regisztrálva van a bérlőn, majd az inicializálási folyamatot biztosítja, hogy egyenként csatlakozik-e az azonos Service Bus-üzenetsorba. 

## <a name="process-sign-in-requests"></a>Folyamat bejelentkezési kérelmek 

Az alábbi ábrán látható, hogy az átmenő hitelesítés hogyan dolgozza fel a felhasználói bejelentkezési kérelmek.

![Bejelentkezési folyamat](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

Az átmenő hitelesítés módon kezeli a felhasználói bejelentkezési kérelem: 

1. Egy felhasználó megpróbál hozzáférni egy alkalmazáshoz, például [Outlook Web App](https://outlook.office365.com/owa).
2. Ha a felhasználó még nem jelentkezett be, az alkalmazás a böngésző átirányítja az Azure AD bejelentkezési oldal.
3. Az Azure AD-STS szolgáltatás reagál a biztonsági másolatot a **felhasználói bejelentkezés** lapot.
4. A felhasználó nem ír be a felhasználónevet a **felhasználói bejelentkezés** lapot, majd kiválasztja a **tovább** gombra.
5. A felhasználó beírja a jelszavát, a **felhasználói bejelentkezés** lapot, majd kiválasztja a **bejelentkezési** gombra.
6. A felhasználónév és jelszó egy HTTPS-POST-kérelmet az Azure AD STS elküldi.
7. Azure AD STS kérdezi le a nyilvános kulcsok az Azure SQL database-ből a bérlő regisztrált hitelesítési ügynökök, és a jelszó titkosítja őket használatával. 
    - A bérlő regisztrált "N" hitelesítési ügynökök titkosított "N" jelszó értékeinek küld.
8. Az Azure AD STS helyezi el a jelszó-érvényesítési kérése, amely a felhasználónév és a titkosított jelszót értékek, a Service Bus-üzenetsorba a bérlőjéhez tartozik az alakzatot áll.
9. Az inicializált hitelesítési ügynökök tartósan csatlakozott a Service Bus-üzenetsorba, mert a rendelkezésre álló hitelesítési ügynökök egyike a jelszó-érvényesítési kérése kérdezi le.
10. A hitelesítési ügynök megkeresi a titkosított jelszót érték, amely csak a saját nyilvános kulcsát egy azonosító használatával, és visszafejti őket a hozzá tartozó titkos kulcs használatával.
11. A hitelesítési ügynök próbál ellenőrizze a felhasználónevet és jelszót a helyi Active Directorybeli. Ehhez használja a [Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) együtt a **dwLogonType** paraméterkészlet a**LOGON32_LOGON_NETWORK**. 
    - Az API-t az azonos API-t, amelyet az Active Directory összevonási szolgáltatások (AD FS) a felhasználók az összevont bejelentkezési forgatókönyvek esetében.
    - Az API-t a keresse meg a tartományvezérlő Windows Server standard névfeloldási folyamat támaszkodik.
12. A hitelesítési ügynök az eredményt kap az Active Directory, például a sikeres, a felhasználónév vagy jelszó helytelen, vagy a jelszó lejárt.
13. A hitelesítési ügynök továbbítja az eredmény vissza az Azure ad-ben STS egy kölcsönösen hitelesített kimenő HTTPS-csatornán keresztül 443-as porton keresztül. Kölcsönös hitelesítést a regisztráció során a hitelesítési ügynök korábban kiadott tanúsítványt használ.
14. Az Azure AD STS ellenőrzi, hogy ez az eredmény az adott bejelentkezési kérés a bérlő utal.
15. Az Azure AD STS továbbra is a bejelentkezési művelet konfigurálva. Például, ha a jelszó érvényesítése sikeres volt, a felhasználó előfordulhat, hogy lehet merül fel a multi-factor Authentication vagy átirányítódik az alkalmazásba.

## <a name="operational-security-of-the-authentication-agents"></a>A hitelesítési ügynökök működési biztonság

Annak érdekében, hogy az átmenő hitelesítés működés közben biztonságban maradjon, rendszeres időközönként az Azure AD hitelesítési ügynökök tanúsítványok megújítása. Azure ad-ben a megújításhoz aktivál. A megújítás nem vonatkoznak rájuk a hitelesítési ügynökök magukat.

![Működési biztonság](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Az Azure AD-hitelesítési ügynök megbízhatósági megújítása:

1. A hitelesítési ügynök pingeli rendszeres időközönként az Azure AD-e a tanúsítvány megújításához idő néhány óránként. A tanúsítvány van újítani a lejárata előtt 30 nappal.
    - Ez az ellenőrzés kölcsönösen hitelesített HTTPS-csatornán keresztül történik, és a regisztráció során korábban kiadott tanúsítványt használja.
2. Ha a szolgáltatás jelzi, hogy újítsa meg időt, a hitelesítési ügynök új kulcspárt generál: egy nyilvános kulcs és titkos kulcsot.
    - Ezek a kulcsok akkor jönnek létre standard RSA 2048-bites titkosítás révén.
    - A titkos kulcs sosem hagyja el a helyileg működtetett kiszolgálón.
3. A hitelesítési ügynök majd "Tanúsítványmegújítás" kérelmet küld az Azure AD HTTPS,-kapcsolaton keresztül a kérelem tartalmazza a következő összetevőkkel:
    - A meglévő tanúsítvány CERT_SYSTEM_STORE_LOCAL_MACHINE helye az a Windows-tanúsítványtároló lekért. Nincs nem globális rendszergazda részt vesz ebben az eljárásban így nincs szükség a globális rendszergazda nevében hozzáférési jogkivonatot.
    - A 2. lépésben létrehozott nyilvános kulcsot.
    - Tanúsítvány-aláírási kérelem (CSR-fájl vagy tanúsítványkérelem). A kérelem egy új digitális identitásokat tanúsítványt, az Azure ad-vel, a hitelesítésszolgáltató vonatkozik.
4. Az Azure AD ellenőrzi a meglévő tanúsítványt a tanúsítványmegújítási kérelem végrehajtását. Majd ellenőrzi, hogy a kérés érkezett egy hitelesítési ügynök regisztrálva van a bérlőn.
5. Ha a meglévő tanúsítvány még érvényes, az Azure AD új digitális identitásokat tanúsítvány aláírja, majd az új tanúsítványt állít vissza a hitelesítési ügynök. 
6. Ha a meglévő tanúsítvány lejárt, Azure ad-ben regisztrált hitelesítési ügynökök listája a bérlő a hitelesítési ügynök törli. Majd egy globális rendszergazdának kell manuálisan telepíteni és regisztrálni egy új hitelesítési ügynök.
    - Az Azure ad-ben legfelső szintű hitelesítésszolgáltató segítségével aláírja a tanúsítványt.
    - A tanúsítvány tulajdonosának (megkülönböztető nevét vagy megkülönböztető) beállítása a bérlő azonosítója, egy GUID Azonosítót, amely egyedileg azonosítja a bérlőn. A DN hatóköröket a tanúsítványt csak az bérlőhöz.
6. Az Azure AD az új nyilvános kulcsot a hitelesítési ügynök, amely hozzáféréssel rendelkezik, csak az Azure SQL-adatbázisban tárolja. Azt is a régi nyilvános kulcsot, a hitelesítési ügynök társított érvényteleníti.
7. Az új tanúsítvány (tulajdonos 5. lépésében) majd tárolja a Windows-tárolóban található a kiszolgálón (a kifejezetten a [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) helye).
    - A bizalmi kapcsolat megújítási eljárás nem interaktív (a globális rendszergazda jelenléte) nélkül történik, mert a hitelesítési ügynök már nincs hozzáférés CERT_SYSTEM_STORE_LOCAL_MACHINE a helyen a meglévő tanúsítvány frissítéséhez. 
    
   > [!NOTE]
   > Ez az eljárás nem távolítja el maga a tanúsítvány a CERT_SYSTEM_STORE_LOCAL_MACHINE helyről.
8. Az új tanúsítványt a ezen a ponton történő hitelesítéshez használatos. Minden ezt követő a tanúsítvány megújítása a tanúsítvány CERT_SYSTEM_STORE_LOCAL_MACHINE helyen váltja fel.

## <a name="auto-update-of-the-authentication-agents"></a>A hitelesítési ügynökök automatikus frissítése

A frissítési kérelem automatikusan frissíti a hitelesítési ügynök, amikor egy új verziója. Az alkalmazás nem kezeli a jelszó érvényesítése kérések a bérlő számára. 

Azure ad-ben üzemelteti az új verziót, a szoftver egy aláírt **Windows Installer-csomagot (MSI)**. Az MSI használatával legyen aláírva [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) az SHA256 titkosítást, kivonatoló algoritmus. 

![Automatikus frissítés](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

Az automatikus frissítés hitelesítési ügynök:

1. A frissítési kérelem pingelésre. az Azure AD minden órában, ellenőrizze, hogy van-e a hitelesítési ügynök új verziója érhető el. 
    - Ezt az ellenőrzést a regisztráció során korábban kiadott ugyanazt a tanúsítványt egy kölcsönösen hitelesített HTTPS-csatornán keresztül történik. A hitelesítési ügynök és a frissítési ossza meg a kiszolgálón tárolt tanúsítvány.
2. Ha új verzió érhető el, az Azure AD az aláírt MSI térjen vissza a frissítési adja vissza.
3. A frissítési ellenőrzi, hogy az MSI azt a Microsoft aláírta.
4. A frissítési az MSI futtatja. Ez a művelet a következő lépésekből áll:

 > [!NOTE]
 > Futtatja a frissítési [helyi rendszer](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) jogosultságokkal.

    - A hitelesítési ügynök szolgáltatás leállítása
    - A hitelesítési ügynök új verzióját telepíti a kiszolgálóra
    - A hitelesítési ügynök szolgáltatás újraindítása

>[!NOTE]
>Ha a bérlő regisztrált több hitelesítési ügynök, az Azure AD nem megújítani tanúsítványaikat, és egyszerre frissíteni őket. Ehelyett az Azure AD nem így fokozatosan bejelentkezési kérelmek magas rendelkezésre állásának biztosításához.
>


## <a name="next-steps"></a>További lépések
- [Aktuális korlátozások](active-directory-aadconnect-pass-through-authentication-current-limitations.md): ismerje meg, melyik forgatókönyvek is támogatottak, és melyek nem.
- [Gyors üzembe helyezési](active-directory-aadconnect-pass-through-authentication-quick-start.md): első lépésekhez az Azure AD átmenő hitelesítés.
- [Az AD FS át az átmenő hitelesítés](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) – egy részletes útmutató, amellyel áttelepíteni az átmenő hitelesítés az Active Directory összevonási szolgáltatások (vagy más összevonási technológiákkal).
- [Az intelligens zárolási](../authentication/howto-password-smart-lockout.md): az intelligens zárolás funkciót konfigurálhatja a bérlő felhasználói fiókok védelmét.
- [Hogyan működik](active-directory-aadconnect-pass-through-authentication-how-it-works.md): kapcsolatos alapismereteket tartalmazza az Azure AD átmenő hitelesítés működéséről.
- [Gyakori kérdések](active-directory-aadconnect-pass-through-authentication-faq.md): választ találhat a gyakori kérdésekre.
- [Hibaelhárítás](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): ismerje meg az átmenő hitelesítés szolgáltatás szolgáltatással kapcsolatos gyakori problémák megoldásához.
- [Az Azure AD közvetlen egyszeri bejelentkezés](active-directory-aadconnect-sso.md): További információ a kiegészítő funkció.
