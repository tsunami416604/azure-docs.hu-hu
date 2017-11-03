---
title: "Az Azure Active Directory átmenő hitelesítést biztonsági részletesen |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan védi az Azure Active Directory (Azure AD) átmenő hitelesítés a a helyszíni fiókok."
services: active-directory
keywords: "Az Azure AD Connect áteresztő hitelesítés, telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: femila
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.openlocfilehash: a5feadd851b166d0a9a77bd1d124cdd599d5d701
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Az Azure Active Directory átmenő hitelesítést biztonsági részletesen

Ez a cikk áteresztő hitelesítés működéséről további részletes leírását tartalmazza. Több összpontosít a szolgáltatás a biztonsági szempontjait. Ez a témakör fontos biztonsági és a informatikai rendszergazdák, a fő megfelelőségi és biztonsági tisztviselő és a más informatikai biztonsági és megfelelőségi szervezetek kis és közepes vagy nagy vállalatok felelős informatikai szakemberek számára lesz.

Címzett témaköröket tartalmazza:
- Részletes technikai információkat arról, hogy a hitelesítési ügynökök hogyan vannak telepítve és regisztrálva.
- Felhasználói bejelentkezés során a jelszavak titkosítása részletes technikai információkat.
- A csatornák közötti biztonsági helyszíni hitelesítési az ügynökök és az Azure Active Directory (Azure AD).
- Részletes technikai információkat hogyan hitelesítési ügynökök vannak biztonságba működik.
- További biztonsági témakörökre mutatnak.

## <a name="key-security-capabilities"></a>Kulcs biztonsági képességei

Ez a funkció a kulcs biztonsági szempontjait a következők:
- A biztonságos multi-központjaként architektúra, amely a bejelentkezési kérelmek a bérlők közötti elkülönítési épül.
- A helyszíni jelszavak soha nem a felhő semmilyen formában vannak tárolva.
- Helyszíni hitelesítési ügynökök, figyelését és a jelszó érvényesítése kérésekre válaszol, ellenőrizze a kimenő kapcsolatok a hálózaton belül csak. Nincs szükség a szegélyhálózaton (DMZ) hitelesítési ügynökök telepítéséhez.
- Csak szabványos portot (80-as és 443-as) az Azure AD a hitelesítési ügynököktől kimenő kommunikációra használt. Nincs bejövő portokat kell megnyitni a tűzfalon. 
  - 443-as port az összes hitelesített kimenő kommunikációra használja
  - 80-as port csak a visszavont tanúsítványok listájának (CRL) letöltése a annak biztosítására szolgál a szolgáltatás által használt tanúsítványok visszavonása.
  - Lásd: [Itt](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites) a hálózati követelmények teljes listáját.
- Bejelentkezéskor a felhasználó által megadott jelszó a felhőben van titkosítva, mielőtt fogadta el a helyszíni hitelesítési-ügynökök az Active Directory-elvégzett sémaellenőrzésen.
- A HTTPS-csatorna az Azure AD között, és egy helyszíni hitelesítési ügynök által a kölcsönös hitelesítés.
- Az zökkenőmentesen integrálható az Azure AD felhőalapú védelmi képességek, például a feltételes hozzáférési szabályzatok (beleértve a többtényezős hitelesítés), Identity Protection, és intelligens zárolás.

## <a name="components-involved"></a>Vevő összetevők

Általános működési, az Azure AD szolgáltatás és az adatbiztonságot, lásd: a [biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/). A következő összetevőket is érintett, a felhasználói bejelentkezés áteresztő hitelesítés használata esetén:
- **Az Azure AD STS**: egy állapot nélküli biztonsági jogkivonat-szolgáltatás (STS), amely feldolgozza a bejelentkezési kérelmek és biztonsági jogkivonatokat bocsát ki a felhasználók böngészőjének, ügyfelek vagy szolgáltatások szükség szerint.
- **Az Azure Service Bus**: biztosít a felhő engedélyezve van a vállalati üzenetkezelési és továbbítók kommunikációt, amely segít a helyszíni megoldásokkal csatlakozzon a felhővel.
- **Azure AD Connect hitelesítési ügynök (hitelesítési ügynök)**: egy a helyszíni összetevő figyeli, és a jelszó érvényesítése kérelmekre reagál.
- **Az Azure SQL Database**: a bérlő hitelesítési ügynökök, beleértve a metaadatok és a titkosítási kulcsok információk.
- **Active Directory (AD)**: A helyszíni Active Directory, a felhasználói fiókok (és jelszavukat) tárolására.

## <a name="installation-and-registration-of-authentication-agents"></a>Telepítési és hitelesítési ügynök regisztrálása

Hitelesítési ügynökök telepített és regisztrált az Azure AD amikor Ön [Azure AD Connect használatával áteresztő hitelesítés engedélyezése](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature) és mikor, [adja hozzá a további hitelesítési ügynökök magas rendelkezésre állásának biztosításához bejelentkezési kérelmek](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability). Egy hitelesítési ügynök használatának első három fő fázisból jár:

- Hitelesítés az ügynök telepítése
- Hitelesítés az ügynök regisztrálása
- Hitelesítési ügynök inicializálása

Ezen az alábbi témakörök részletesen tárgyalja.

### <a name="authentication-agent-installation"></a>Hitelesítés az ügynök telepítése

Csak a globális rendszergazdák (az Azure AD Connect vagy önálló használatával) hitelesítési ügynök telepíthető egy helyszíni kiszolgálón. Telepítési ezek két új bejegyzést, hogy hozzáadja a **Vezérlőpult -> Programok -> Programok és szolgáltatások** listája:
- A hitelesítési ügynök alkalmazás saját magát. Ez a fut [hálózati szolgáltatás](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) jogosultságokkal.
- A frissítési alkalmazás automatikus frissítése a hitelesítési ügynök használatos. Ez a fut [helyi rendszer](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) jogosultságokkal.


### <a name="authentication-agent-registration"></a>Hitelesítés az ügynök regisztrálása

A hitelesítési ügynök telepítése után kell az Azure ad-val regisztrálja magát. Az Azure AD ennek érdekében hozzárendelése minden hitelesítési ügynök a biztonságos kommunikáció érdekében az Azure ad-val használhat egyedi digitális identitásokat tanúsítványt.

A regisztrációs eljárást is, hogy tudja, hogy az Azure AD, hogy az adott hitelesítési ügynök-e a jelszó érvényesítése tanúsítványigénylések a bérlő számára engedélyezett az egyetlen van kötve a hitelesítési ügynök a bérlővel. Ez az eljárás megismétlődik minden egyes új hitelesítési ügynök, hogy regisztrálja.

Ez hogyan hitelesítési ügynökök regisztrálják magukat az Azure ad-val:

![Az ügynök regisztrálása](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Az Azure AD először kérelmez egy globális rendszergazda Azure ad hitelesítő adataikkal bejelentkezni. Bejelentkezéshez a hitelesítési ügynök megszerzi egy hozzáférési jogkivonat, amely képes használni a globális rendszergazda nevében.
2. A hitelesítési ügynök akkor hoz létre a kulcspár – a nyilvános kulcs és a titkos kulcs.
    - A kulcspár szabványos **RSA 2048 bites** titkosítás.
    - A titkos kulcs sosem hagyja el a helyszíni kiszolgáló, amely a hitelesítési ügynök telepítve van.
3.  A hitelesítési ügynök "regisztrációs" kérelmet küld az Azure AD a HTTPS PROTOKOLLOKON keresztül a kérelem tartalmazza a következő összetevőkkel:
    - A hozzáférési jogkivonat 1. lépésben beszerzett.
    - A 2. lépésben létrehozott nyilvános kulcsot.
    - A **tanúsítvány-aláírási kérelem** (CSR vagy tanúsítványkérelem). Ez a digitális identitásokat tanúsítványt, és az Azure AD a hitelesítő alkalmazása.
4. Az Azure AD ellenőrzi a hozzáférési jogkivonat található a regisztrációs kérelmet, és ellenőrzi, hogy a kérés érkezett egy globális rendszergazda.
5. Az Azure AD akkor jelentkezik, és egy digitális azonosító tanúsítványt állít vissza a hitelesítési ügynök a.
    - A tanúsítvány használatával van aláírva **az Azure AD legfelső szintű tanúsítvány hitelesítésszolgáltatói (CA)**. Vegye figyelembe, hogy a CA _nem_ a Windows **megbízható legfelső szintű hitelesítésszolgáltatók** tárolja.
    - A CA csak a csatlakoztatott hitelesítési szolgáltatás használja. Csak az aláíró ügyfélszolgálati során használatos hitelesítési ügynök regisztrációját.
    - A CA Azure AD-ben minden más szolgáltatáshoz, amelyet nem használ.
    - A tanúsítvány tulajdonosának (**megkülönböztető nevét vagy megkülönböztető**) értékre van állítva a **Bérlőazonosító**. Ez a GUID, amely egyedileg azonosítja a bérlő. Ez érvényesíti a tanúsítványt, és csak a bérlő számára.
6. Az Azure AD a hitelesítési ügynök a nyilvános kulcsának az Azure SQL-adatbázis, amely csak az Azure AD hozzáfér tárolja.
7. A tanúsítvány (5. lépés) a helyi kiszolgálón található a **Windows tanúsítványtároló** (a kifejezetten a  **[CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE)**  vagyis a Location), és a hitelesítési ügynök és a frissítési alkalmazások használják.

### <a name="authentication-agent-initialization"></a>Hitelesítési ügynök inicializálása

A hitelesítési ügynök elindul, amikor első alkalommal vagy a kiszolgáló regisztrálása után indítsa újra, biztonságosan kommunikálni az Azure AD szolgáltatással, és indítsa el a jelszó érvényesítése kérelmek fogadása úgy kell.

![Ügynök inicializálása](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Ez hogyan hitelesítési ügynökök beolvasni inicializálása:



1. A hitelesítési ügynök azáltal, hogy a kimenő "bootstrap" kérelmet az Azure AD kezdődik. 
    - A rendszerindítási kérelmet 443-as porton keresztül történik, és a csatornán kölcsönösen hitelesített HTTPS (hitelesítési ügynök regisztrálása során ugyanaz a tanúsítvány használatával).
2. Az Azure AD válaszol a rendszerindítási kérelemre, adja meg a egy **hozzáférési kulcs** számára egy Azure Service Bus-üzenetsorba, amely egyedi a bérlő (a bérlő azonosítója által azonosított).
3. A hitelesítési ügynök kapcsolatot hoz létre állandó kimenő HTTPS (443-as porton) keresztül a várakozási sorba. 
    - Most már készen áll beolvasása és a jelszó érvényesítése-kérelmeket kezelnek.

Ha több hitelesítési ügynök a bérlő regisztrált, majd az inicializálási folyamatot biztosítja, hogy minden egyes csatlakozik-e az azonos Azure Service Bus-üzenetsorba. 

## <a name="processing-sign-in-requests"></a>Bejelentkezési kérelmek feldolgozása 

Az alábbi ábra bemutatja, hogyan dolgozza fel a áteresztő hitelesítés a felhasználó bejelentkezési kérelmek.

![Bejelentkezési feldolgozása](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

Áteresztő hitelesítés kezeli a felhasználói bejelentkezési kérelem az alábbiak szerint: 

1. Egy felhasználó megpróbál hozzáférni az alkalmazáshoz (például az Outlook Web App - [https://outlook.office365.com/owa](https://outlook.office365.com/owa).)
2. Ha a felhasználó már nem jelentkezett be, az alkalmazás az Azure AD bejelentkezési oldalára irányítja át a böngészőben.
3. Az Azure AD STS szolgáltatás vissza a felhasználói bejelentkezési lap válaszként.
4. A felhasználó felhasználónevét és jelszavát köt az Azure AD bejelentkezési oldal, és a "Bejelentkezés" gombra kattint.
5. Az Azure AD STS HTTPS POST kérelemben elküldi a felhasználónevet és jelszót.
6. Az Azure AD STS nyilvános kulcsok beolvasása az összes hitelesítési ügynök a bérlő az Azure SQL adatbázis regisztrált, és titkosítja a jelszót használja őket. 
    - Az "n" titkosított jelszót hoz létre a bérlő regisztrált "n" hitelesítési ügynökök vonatkozó értékeket.
7. Az Azure AD STS helyezi a jelszó érvényesítése kérelem (a felhasználónév és a titkosított érték) az Azure Service Bus-üzenetsorba, a-bérlőjéhez tartozik.
8. Az inicializált hitelesítési ügynökök tartósan kapcsolódik az Azure Service Bus-üzenetsorba, mert az egyik rendelkezésre álló hitelesítési ügynökön lekéri a jelszó-ellenőrzési kérelem.
9. A hitelesítési ügynök megkeresi a titkosított értéket, amely a nyilvános kulcsra (egy azonosító alapján), és visszafejti a titkos kulccsal.
10. A hitelesítési ügynök megpróbálja ellenőrizni a felhasználónevét és jelszavát a helyszíni Active Directory használatának mellőzését a  **[Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)**  (az a **dwLogonType** paraméter **LOGON32_LOGON_NETWORK**). 
    - Ez az a azonos API jelentkezzen be a bejelentkezési forgatókönyvek külső felhasználók Active Directory összevonási szolgáltatások (AD FS) segítségével.
    - Ez a tartomány Contoller található Windows Server standard névfeloldási folyamat támaszkodik.
11. A hitelesítési ügynök a eredményt kap az Active Directoryból (sikeres, a felhasználónév vagy jelszó helytelen, a jelszó lejárt, a felhasználó zárolva van, és így tovább).
12. A hitelesítési ügynök továbbítja az eredményt vissza az Azure AD STS kölcsönösen hitelesített HTTPS kimenő csatornán 443-as porton keresztül. Kölcsönös hitelesítés használja ugyanazt a regisztráció során a hitelesítési ügynök korábban kiadott tanúsítványt.
13. Az Azure AD STS ellenőrzi, hogy ez az eredmény megfelel a megadott bejelentkezési kérését a bérlő.
14. Az Azure AD STS továbbra is a bejelentkezési művelet konfigurált módon. Például ha a jelszó érvényesítése sikeresen befejeződött, a felhasználó nem lehet lekéri a multi-factor Authentication vagy visszairányítja az alkalmazás.

## <a name="operational-security-of-authentication-agents"></a>A működési biztonság hitelesítési ügynökök

Győződjön meg arról, hogy átmenő hitelesítés továbbra is működik biztonságos, hogy az Azure AD rendszeres időközönként megújítja tanúsítványaikat. Ezek megújításokat az Azure AD által kiváltott, és nem vonatkoznak a hitelesítési ügynökök magukat.

![Működési biztonság](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Ez hogyan hitelesítési ügynök megújítja a megbízhatósági kapcsolatban áll az Azure AD:

1. A hitelesítési ügynök rendszeres időközönként Pingeli az Azure AD (óránként), hogy ellenőrizze, hogy-e a tanúsítvány megújításához idő. 
    - Ezt az ellenőrzést (a regisztráció során kiadott tanúsítvány használatával) kölcsönösen hitelesített HTTPS-csatornán keresztül történik.
2. Ha a szolgáltatás azt mutatja, hogy újítsa meg az idő, a hitelesítési ügynök hoz létre egy új kulcspárt: egy nyilvános kulcsot és titkos kulccsal.
    - Ezek a kulcsok akkor jönnek létre, a szabványos **RSA 2048 bites** titkosítás.
    - A titkos kulcs sosem hagyja el a helyszíni kiszolgálón.
3. A hitelesítési ügynök majd "Tanúsítványmegújítás" kérelmet küld az Azure AD a HTTPS PROTOKOLLOKON keresztül a kérelem tartalmazza a következő összetevőkkel:
    - A meglévő tanúsítvány (lekért **CERT_SYSTEM_STORE_LOCAL_MACHINE** mappájába, mint a Windows tanúsítványtároló). : Nem globális rendszergazda részt ebben az eljárásban, nincs hozzáférési jogkivonat a globális rendszergazda nevében szükség van.
    - A 2. lépésben létrehozott nyilvános kulcsot.
    - A **tanúsítvány-aláírási kérelem** (CSR vagy tanúsítványkérelem). Ez az új digitális identitásokat tanúsítványt, és az Azure AD a hitelesítő alkalmazása.
4. Az Azure AD érvényesíti a tanúsítványmegújítási kérelmet a meglévő tanúsítványt. Majd ellenőrzi, hogy a kérés érkezett egy hitelesítési ügynök a bérlő regisztrált.
5. Ha a meglévő tanúsítvány még érvényes, az Azure AD majd új identitás digitális tanúsítvány aláírja és az új tanúsítványt állít ki a hitelesítési ügynökre. 
6. Ha a meglévő tanúsítvány lejárt, az Azure AD a hitelesítési ügynök törlése a bérlő regisztrált hitelesítési ügynökök listája. Ezután egy globális rendszergazdának kell manuálisan telepíteni és regisztrálni egy új hitelesítési ügynök.
    - A tanúsítvány használatával van aláírva **az Azure AD legfelső szintű tanúsítvány hitelesítésszolgáltatói (CA)**.
    - A tanúsítvány tulajdonosának (**megkülönböztető nevét vagy megkülönböztető**) értékre van állítva a **Bérlőazonosító** egy GUID, amely egyedileg azonosítja a bérlő. Ez azt jelenti, hogy a tanúsítvány csak a bérlő hatókörét.
6. Az Azure AD a hitelesítési ügynök a "new" nyilvános kulcsát, amely hozzáféréssel rendelkezik, csak az Azure SQL-adatbázisban tárolja. És a hitelesítési ügynök társított "régi" nyilvános kulcs érvényteleníti.
7. Az új (kiállított tanúsítványt az 5. lépés) majd tárolni a kiszolgálón a **Windows tanúsítványtároló** (a kifejezetten a  **[CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER)**  vagyis a Location).
    - A bizalmi kapcsolat megújítási eljárás nem interaktív (a globális rendszergazda jelenléte) nélkül történik, mivel a hitelesítési ügynök már nem rendelkezik hozzáféréssel a meglévő tanúsítvány frissítéséhez a **CERT_SYSTEM_STORE_LOCAL_MACHINE** helyét. Vegye figyelembe, hogy maga a tanúsítvány a a **CERT_SYSTEM_STORE_LOCAL_MACHINE** helyét a rendszer nem távolítja el az eljárás során.
8. Az új tanúsítványt az ettől a hitelesítéshez használt. A tanúsítvány minden további megújítás váltja fel a tanúsítványt a **CERT_SYSTEM_STORE_LOCAL_MACHINE** helyét.

## <a name="auto-update-of-authentication-agents"></a>Az automatikus frissítés hitelesítési ügynökök

A frissítési alkalmazás automatikusan frissíti a hitelesítési ügynök, amikor megjelenik egy új verziója. A bérlő nem kezeli a jelszó érvényesítése kéréseit. 

Az Azure AD-tároló egy aláírt, a szoftver új verziója **Windows Installer-csomag (MSI)**. Az MSI-fájl használatával van aláírva [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) rendelkező **SHA256** a kivonatoló algoritmusként. 

![Automatikus frissítés](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

Ez hogyan hitelesítési ügynök lekérdezi automatikus frissítése:

1. Megtekinti a rendszeres időközönként Pingeli az Azure AD (óránként), hogy ellenőrizze, hogy a hitelesítési ügynök egy új verziója érhető el. 
    - Ezt az ellenőrzést (a regisztráció során kiadott tanúsítvány használatával) kölcsönösen hitelesített HTTPS-csatornán keresztül történik. A hitelesítési ügynök, és megtekinti a ossza meg a kiszolgálón tárolt tanúsítvány.
2. Ha új verzió érhető el, az Azure AD vissza a megtekinti a adja vissza az aláírt MSI.
3. Megtekinti a ellenőrzi, hogy a MSI-t a Microsoft aláírta.
4. A frissítési futtatják MSI. Ez a művelet a következő lépéseket hajtja (vegye figyelembe, hogy futtatja a frissítési [helyi rendszer](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) jogosultságokkal):
    - Leállítja a hitelesítési ügynök szolgáltatást.
    - A hitelesítési ügynök új verzióját telepíti a kiszolgálón.
    - A hitelesítési ügynök szolgáltatás újraindul.

>[!NOTE]
>Ha több hitelesítési ügynök a bérlő regisztrált, az Azure AD nem megújítani tanúsítványaikat, és frissítse azokat egy időben. Ehelyett az Azure AD e fokozatosan, a bejelentkezési kérelmek magas rendelkezésre állásának biztosításához.


## <a name="next-steps"></a>Következő lépések
- [**Aktuális korlátozások** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – ismerje meg, melyik forgatókönyvek is támogatottak, és melyek nem.
- [**Gyors üzembe helyezési** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) - létrehozásához, és fut az Azure AD áteresztő hitelesítés.
- [**Intelligens zárolás** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -intelligens zárolás konfigurálása képességet a bérlő a felhasználói fiókok védelme.
- [**Hogyan működik** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -alapismeretei Azure AD áteresztő hitelesítés működéséről.
- [**Gyakran ismételt kérdések** ](active-directory-aadconnect-pass-through-authentication-faq.md) -gyakran feltett kérdésekre adott válaszokat.
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**Az Azure AD zökkenőmentes SSO** ](active-directory-aadconnect-sso.md) -további információ a kiegészítő funkció.

