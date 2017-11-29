---
title: "Az Azure Active Directory átmenő hitelesítést biztonsági részletes bemutatója |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan védi az Azure Active Directory (Azure AD) áteresztő hitelesítés a a helyszíni fiókok"
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
ms.openlocfilehash: 4428f3da5dd9423aa60daa697e4ea15c99f92188
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Az Azure Active Directory átmenő hitelesítést biztonsági részletes bemutatója

Ez a cikk az Azure Active Directory (Azure AD) áteresztő hitelesítés működésének részletes leírását tartalmazza. A szolgáltatás a biztonsági szempontjait összpontosít. Ez a cikk a biztonsági és informatikai rendszergazdák, fő megfelelőségi és biztonsági tisztviselő, és más informatikai szakemberek számára felelős informatikai biztonsági és megfelelőségi a kis és közepes méretű szervezeteknek vagy nagy vállalatok számára.

A címzett témaköröket tartalmazza:
- Hogyan kell telepíteni és regisztrálni a hitelesítési ügynökök részletes technikai információkat.
- A jelszavak titkosítása felhasználói bejelentkezés során részletes technikai információkat.
- A biztonsági közötti csatornák helyszíni hitelesítési ügynökök és az Azure AD.
- A biztonságos hitelesítési ügynökök működik kapcsolatos részletes technikai információkat.
- További biztonsági témakörökre mutatnak.

## <a name="key-security-capabilities"></a>Kulcs biztonsági képességei

Ez a funkció a kulcs biztonsági szempontjait a következők:
- A biztonságos multi-központjaként architektúra, amely a bejelentkezési kérelmek a bérlők közötti elkülönítési épül.
- A helyszíni jelszavak soha nem a felhő semmilyen formában vannak tárolva.
- A helyszíni hitelesítési ügynökök figyelését, és a válaszolni, a jelszó érvényesítése kérelmek csak ellenőrizze a kimenő kapcsolatok a hálózaton belül. Nincs szükség a szegélyhálózaton (DMZ) hitelesítési ügynökök telepítéséhez.
- Csak szabványos portot (80-as és 443-as) az Azure AD a hitelesítési ügynököktől kimenő kommunikációra használt. Nem kell megnyitni a bejövő portra a tűzfalon. 
  - 443-as port az összes hitelesített kimenő kommunikáció szolgál.
  - 80-as portot a csak a visszavont tanúsítványok listájának (CRL) letöltése annak biztosítására szolgál, hogy ez a szolgáltatás által használt tanúsítványok vonták.
  - A hálózati követelmények teljes listáját lásd: [Azure Active Directory áteresztő hitelesítés: gyors üzembe helyezési](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).
- Jelszavak, amelyeket a felhasználó bejelentkezési során a felhőben vannak titkosítva, előtt a helyszíni hitelesítési ügynökök fogadja el az Active Directory elvégzett sémaellenőrzésen.
- A HTTPS-csatorna az Azure AD között, és a helyszíni hitelesítési ügynök a kölcsönös hitelesítés.
- A funkció zökkenőmentesen integrálható az Azure AD-felhővédelem képességeit, például a feltételes hozzáférési szabályzatok (Azure többtényezős hitelesítést is beleértve), a azonosító adatok védelmét, és intelligens zárolás.

## <a name="components-involved"></a>Vevő összetevők

Általános működési, az Azure AD szolgáltatás, és az adatok biztonságát, lásd: a [biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/). A következő összetevőket is érintett, a felhasználói bejelentkezés áteresztő hitelesítés használatakor:
- **Az Azure AD STS**: állapot nélküli biztonságijogkivonat-szolgáltatás (STS), amely feldolgozza a bejelentkezési kérelmek és biztonsági jogkivonatokat bocsát ki a felhasználók böngészőjének, ügyfelek vagy szolgáltatások szükség szerint.
- **Az Azure Service Bus**: biztosít a felhő engedélyezve van a vállalati üzenetkezelési és továbbítók kommunikációt, amely segít a helyszíni megoldásokkal csatlakozzon a felhővel.
- **Azure AD Connect hitelesítési ügynök**: egy a helyszíni összetevő figyeli, és a jelszó érvényesítése kérelmekre reagál.
- **Az Azure SQL Database**: a bérlő hitelesítési ügynökök, beleértve a metaadatok és a titkosítási kulcsok információk.
- **Az Active Directory**: A helyszíni Active Directory, a felhasználói fiókoknak és a jelszavak tárolására.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Telepítés és a hitelesítési ügynökök regisztrálás

Hitelesítési ügynökök telepített és regisztrált az Azure ad-val Ha Ön vagy:
   - [Átmenő hitelesítést keresztül az Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [A bejelentkezési kérelmek magas rendelkezésre állásának biztosításához további hitelesítési ügynökök hozzáadása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Egy hitelesítési ügynök használatának első három fő fázisból jár:

1. Hitelesítés az ügynök telepítése
2. Hitelesítés az ügynök regisztrálása
3. Hitelesítési ügynök inicializálása

Az alábbi szakaszok részletesen ezeket a fázisokat ismertetik.

### <a name="authentication-agent-installation"></a>Hitelesítés az ügynök telepítése

Csak a globális rendszergazdák telepítheti egy hitelesítési ügynök (az Azure AD Connect vagy önálló felhasználásával) egy helyszíni kiszolgálón. Telepítés két új bejegyzést, hogy hozzáadja a **Vezérlőpult** > **programok** > **programok és szolgáltatások** listája:
- A hitelesítési ügynök alkalmazás saját magát. Az alkalmazás fut a [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) jogosultságokkal.
- A frissítési alkalmazás, amely automatikusan frissíti a hitelesítési ügynök szolgál. Az alkalmazás fut a [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) jogosultságokkal.

### <a name="authentication-agent-registration"></a>Hitelesítés az ügynök regisztrálása

A hitelesítési ügynök telepítése után kell az Azure ad-val regisztrálja magát. Az Azure AD minden hitelesítési ügynök a biztonságos kommunikáció érdekében az Azure ad-val használhat egyedi, digitális-identitás tanúsítványt rendeli hozzá.

A regisztrációs eljárást is a hitelesítési ügynök és a bérlő van kötve. Ez biztosítja, hogy az Azure AD tudja, hogy az adott hitelesítési ügynök-e az egyetlen, a jelszó érvényesítése tanúsítványigénylések a bérlő számára engedélyezett. Ez az eljárás megismétlődik minden egyes új hitelesítési ügynök, hogy regisztrálja.

A hitelesítési ügynökök az alábbi lépések segítségével regisztrálják magukat az Azure AD:

![Az ügynök regisztrálása](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Az Azure AD először kérelmez, hogy globális rendszergazdaként jelentkezzen be az Azure AD hitelesítő adataikkal. Bejelentkezéskor a hitelesítési ügynök szerez be olyan hozzáférési jogkivonatot, amely képes használni a globális rendszergazda nevében.
2. A hitelesítési ügynök majd létrehoz egy kulcspárt: egy nyilvános kulcsot és titkos kulccsal.
    - A kulcspár keresztül szabványos RSA 2048 bites titkosítást.
    - A titkos kulcs a helyi kiszolgálón marad, ahol a hitelesítési ügynök található.
3. A hitelesítési ügynök "regisztrációs" kérelmet küld az Azure AD a HTTPS PROTOKOLLOKON keresztül a kérelem tartalmazza a következő összetevőkkel:
    - Az 1. lépésben megszerzett jogkivonat.
    - A 2. lépésben létrehozott nyilvános kulcsot.
    - Tanúsítvány-aláírási kérelem (CSR vagy tanúsítványkérelem). A kérelem érvényes digitális identitásokat tanúsítványt, mint a hitelesítésszolgáltató (CA) Azure AD-val.
4. Az Azure AD ellenőrzi a hozzáférési jogkivonat található a regisztrációs kérelmet, és ellenőrzi, hogy a kérés érkezett egy globális rendszergazda.
5. Az Azure AD majd jelentkezik, és egy digitális identitásokat tanúsítványt küld vissza a hitelesítési ügynök.
    - Az Azure ad-ben a legfelső szintű hitelesítésszolgáltató a tanúsítvány aláírására használatos. 

     >[!NOTE]
     > A hitelesítésszolgáltató _nem_ a Windows megbízható legfelső szintű hitelesítésszolgáltatók tárolni.
    - A hitelesítésszolgáltató csak a csatlakoztatott hitelesítési szolgáltatás használja. A hitelesítésszolgáltató csak a hitelesítés az ügynök regisztrálása során ügyfélszolgálati aláírására szolgál.
    -  A Hitelesítésszolgáltatót használ a többi Azure AD szolgáltatásokba egyike.
    - A tanúsítvány tulajdonosának (megkülönböztető nevét vagy megkülönböztető nevét) értéke a bérlő azonosítójával. A DN, amely egyedileg azonosítja a bérlő GUID. A DN hatóköröket a tanúsítvány csak a bérlő való használatra.
6. Az Azure AD a hitelesítési ügynök a nyilvános kulcsának az Azure SQL-adatbázis, amely csak az Azure AD hozzáfér tárolja.
7. A tanúsítvány (5. lépés) a Windows tanúsítványtárolójában a helyi kiszolgálón található (a kifejezetten a [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) helyen). A hitelesítési ügynök és a frissítési alkalmazások egyaránt szolgál.

### <a name="authentication-agent-initialization"></a>Hitelesítési ügynök inicializálása

A hitelesítési ügynök indulásakor az első alkalommal vagy a kiszolgáló regisztrálása után indítsa újra, a biztonságos kommunikáció valósítható meg az Azure AD szolgáltatással, és indítsa el a jelszó érvényesítése kérelmek fogadása úgy kell.

![Ügynök inicializálása](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

Ez hogyan hitelesítési ügynökök inicializálása:

1. A hitelesítési ügynök egy kimenő rendszerindítási kérelmet küld az Azure AD. 
    - A kérelem 443-as porton keresztül történik, és kölcsönösen hitelesített HTTPS-csatornán keresztül. A kérelmet a hitelesítés az ügynök regisztrálása során korábban kiadott ugyanazt a tanúsítványt használja.
2. Az Azure AD válaszol a kérésre azáltal, hogy egy hívóbetűt egy Azure Service Bus-üzenetsorba, a bérlő egyedi, és azonosítja, hogy a bérlő azonosítójával.
3. A hitelesítési ügynök kapcsolatot hoz létre állandó kimenő HTTPS (443-as porton) keresztül a várakozási sorba. 
    - A hitelesítési ügynök lekéri és jelszóellenőrzés tanúsítványigénylések készen áll.

Ha több hitelesítési ügynök a bérlő regisztrált, majd az inicializálási folyamatot biztosítja, hogy minden egyes csatlakozik-e az azonos Service Bus-üzenetsorba. 

## <a name="process-sign-in-requests"></a>Folyamatok bejelentkezési kérelmek 

Az alábbi ábrán látható, hogy átmenő hitelesítés hogyan dolgozza fel a felhasználói bejelentkezési kérelmek.

![Folyamat-bejelentkezés](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

Áteresztő hitelesítés kezeli a felhasználói bejelentkezési kérelem az alábbiak szerint: 

1. Egy felhasználó megpróbál hozzáférni egy alkalmazás, például [Outlook Web App](https://outlook.office365.com/owa).
2. Ha a felhasználó már nem jelentkezett be, az alkalmazás az Azure AD bejelentkezési oldalára irányítja át a böngészőben.
3. Az Azure AD STS szolgáltatás válasza biztonsági a **felhasználói bejelentkezés** lap.
4. A felhasználó megadja a felhasználónevét és jelszavát, az a **felhasználói bejelentkezés** lapot, és választják ki a **bejelentkezési** gombra.
5. Az Azure AD STS egy HTTPS POST kérelemben elküldi a felhasználónevet és jelszót.
6. Az Azure AD STS nyilvános kulcsok beolvasása a bérlő az Azure SQL adatbázis regisztrált hitelesítési ügynökök, és a jelszó titkosítja azokat a. 
    - "N" titkosított jelszót értékek az "N" hitelesítési ügynökökhöz a bérlő regisztrált hoz létre.
7. Az Azure AD STS a jelszó érvényesítése kérést, a felhasználónév és a titkosított értékek, a Service Bus-üzenetsorba az bérlőjéhez tartozik, amely helyezi.
8. Az inicializált hitelesítési ügynökök tartósan csatlakozik a Service Bus-üzenetsorba, mert az egyik rendelkezésre álló hitelesítési ügynökön lekéri a jelszó-ellenőrzési kérelem.
9. A hitelesítési ügynök a nyilvános kulcsát, az azonosítója alapján, és annak titkos kulcsát segítségével visszafejti a titkosított értéket keresi.
10. A hitelesítési ügynök megpróbálja ellenőrizni a felhasználónevét és jelszavát a helyszíni Active Directoryban használatával a [Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) rendelkező a **dwLogonType** paraméter értéke **LOGON32_LOGON_NETWORK**. 
    - Ez az API a azonos API, amellyel Active Directory összevonási szolgáltatások (AD FS) összevont bejelentkezés esetén a felhasználók bejelentkezés.
    - Ez az API a tartományvezérlő található Windows Server standard névfeloldási folyamat támaszkodik.
11. A hitelesítési ügynök a eredményt kap az Active Directoryból, például a sikeres, a felhasználónév vagy jelszó helytelen, vagy a jelszó lejárt.
12. A hitelesítési ügynök továbbítja az eredményt vissza az Azure AD STS kölcsönösen hitelesített HTTPS kimenő csatornán 443-as porton keresztül. Kölcsönös hitelesítést használ, a regisztráció során a hitelesítési ügynök korábban kiadott tanúsítványt.
13. Az Azure AD STS ellenőrzi, hogy ez az eredmény megfelel a megadott bejelentkezési kérését a bérlő.
14. Az Azure AD STS továbbra is a bejelentkezési művelet konfigurált módon. Például ha a jelszó érvényesítése sikeresen befejeződött, a felhasználó lehet, hogy lekéri a multi-factor Authentication vagy az alkalmazás átirányítva.

## <a name="operational-security-of-the-authentication-agents"></a>A hitelesítési ügynök a működési biztonság

Annak érdekében, hogy átmenő hitelesítés továbbra is működik biztonságos, az Azure AD rendszeresen megújítja a hitelesítési ügynökök tanúsítványok. Az Azure AD a megújításokat váltja ki. A megújításokat nem vonatkozik a hitelesítési ügynökök magukat.

![Működési biztonság](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

Az Azure AD hitelesítési ügynök megbízhatósági meghosszabbítása:

1. A hitelesítési ügynök rendszeres időközönként Pingeli az Azure AD ellenőrizze, hogy-e a tanúsítvány megújításához idő óránként. 
    - Ez az ellenőrzés kölcsönösen hitelesített HTTPS-csatornán keresztül történik, és ugyanazt a regisztráció során korábban kiadott tanúsítványt használja.
2. Ha a szolgáltatás azt mutatja, hogy újítsa meg az idő, a hitelesítési ügynök hoz létre egy új kulcspárt: egy nyilvános kulcsot és titkos kulccsal.
    - Ezek a kulcsok akkor jönnek létre, a szokásos RSA 2048 bites titkosítást.
    - A titkos kulcs sosem hagyja el a helyszíni kiszolgálón.
3. A hitelesítési ügynök majd "Tanúsítványmegújítás" kérelmet küld az Azure AD a HTTPS PROTOKOLLOKON keresztül a kérelem tartalmazza a következő összetevőkkel:
    - A meglévő tanúsítványt, a Windows tanúsítványtároló CERT_SYSTEM_STORE_LOCAL_MACHINE helyéről beolvasott. : Nem globális rendszergazda részt ebben az eljárásban így nincs szükség a globális rendszergazda nevében hozzáférési jogkivonat.
    - A 2. lépésben létrehozott nyilvános kulcsot.
    - Tanúsítvány-aláírási kérelem (CSR vagy tanúsítványkérelem). A kérelem új digitális identitásokat tanúsítványt, és az Azure AD a hitelesítő vonatkozik.
4. Az Azure AD érvényesíti a tanúsítványmegújítási kérelmet a meglévő tanúsítványt. Majd ellenőrzi, hogy a kérés érkezett egy hitelesítési ügynök a bérlő regisztrált.
5. Ha a meglévő tanúsítvány még érvényes, az Azure AD majd új identitás digitális tanúsítvány aláírja és az új tanúsítványt állít ki a hitelesítési ügynökre. 
6. Ha a meglévő tanúsítvány lejárt, az Azure AD a hitelesítési ügynök törlése a bérlő regisztrált hitelesítési ügynökök listája. Ezután egy globális rendszergazdának kell manuálisan telepíteni és regisztrálni egy új hitelesítési ügynök.
    - Használja az Azure AD legfelső szintű hitelesítésszolgáltató a tanúsítvány aláírására.
    - A bérlői azonosító, GUID, amely egyedileg azonosítja a bérlő állítsa be a tanúsítvány tulajdonosának (megkülönböztető nevét vagy megkülönböztető nevét). A DN a tanúsítvány csak a bérlő hatóköröket.
6. Az Azure AD a hitelesítési ügynök az új nyilvános kulcsot, amely hozzáféréssel rendelkezik, csak az Azure SQL-adatbázis tárolja. Is a régi, a hitelesítési ügynök tartozó nyilvános kulcs érvénytelenné válik.
7. Az új tanúsítvány (5. lépés) majd tárolni a kiszolgálón a Windows tanúsítványtárolóban (a kifejezetten a [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) helyen).
    - A bizalmi kapcsolat megújítási eljárás nem interaktív (a globális rendszergazda jelenléte) nélkül történik, mert a hitelesítési ügynök már nem rendelkezik hozzáférési CERT_SYSTEM_STORE_LOCAL_MACHINE helyen a meglévő tanúsítvány frissítéséhez. 
    
   > [!NOTE]
   > Ez az eljárás nem maga a tanúsítvány eltávolítása a CERT_SYSTEM_STORE_LOCAL_MACHINE helyét.
8. Az új tanúsítványt az ettől a hitelesítéshez használt. A tanúsítvány minden további megújítás váltja fel a tanúsítványt a CERT_SYSTEM_STORE_LOCAL_MACHINE helyen.

## <a name="auto-update-of-the-authentication-agents"></a>Az automatikus frissítés a hitelesítési ügynökök

A frissítési alkalmazás automatikusan frissíti a hitelesítési ügynök, amikor megjelenik egy új verziója. Az alkalmazás nem jelszó érvényesítése tud semmilyen kérelmet kezelni a bérlő számára. 

Az Azure AD-tároló egy aláírt, a szoftver új verziója **Windows Installer-csomag (MSI)**. Az MSI-fájl használatával legyen aláírva [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) SHA-256 kivonatoló algoritmus. 

![Az automatikus frissítés](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

Az automatikus frissítés hitelesítési ügynök:

1. A frissítési alkalmazás ping-üzenetek az Azure AD minden órában, hogy ellenőrizze, hogy a hitelesítési ügynök egy új verziója érhető el. 
    - Ez az ellenőrzés történik kölcsönösen hitelesített HTTPS-csatornán keresztül ugyanazt a regisztráció során korábban kiadott tanúsítványt. A hitelesítési ügynök, és megtekinti a ossza meg a kiszolgálón tárolt tanúsítvány.
2. Ha új verzió érhető el, az Azure AD vissza a megtekinti a adja vissza az aláírt MSI.
3. Megtekinti a ellenőrzi, hogy a MSI-t a Microsoft aláírta.
4. A frissítési futtatják MSI. Ez a művelet a következő lépésekből áll:

 > [!NOTE]
 > Futtatja a frissítési [helyi rendszer](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) jogosultságokkal.

    - A hitelesítési ügynök szolgáltatás leállítása
    - A hitelesítési ügynök új verzióját telepíti a kiszolgálóra
    - A hitelesítési ügynök szolgáltatás újraindítása

>[!NOTE]
>Ha több hitelesítési ügynök a bérlő regisztrált, az Azure AD nem megújítani tanúsítványaikat, és frissítse azokat egy időben. Ehelyett az Azure AD e fokozatosan ezt a bejelentkezési kérelmek magas rendelkezésre állásának biztosításához.
>


## <a name="next-steps"></a>Következő lépések
- [Aktuális korlátozások](active-directory-aadconnect-pass-through-authentication-current-limitations.md): megtudhatja, mely forgatókönyvek is támogatottak, és melyek nem.
- [Gyors üzembe helyezési](active-directory-aadconnect-pass-through-authentication-quick-start.md):, amelyekből megismerheti az Azure AD áteresztő hitelesítés.
- [Intelligens zárolás](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): az intelligens zárolás funkció konfigurálásához a bérlő felhasználói fiókok védelme.
- [Hogyan működik](active-directory-aadconnect-pass-through-authentication-how-it-works.md): alapismeretei Azure AD áteresztő hitelesítés működéséről.
- [Gyakori kérdések](active-directory-aadconnect-pass-through-authentication-faq.md): gyakran feltett kérdésekre adott válaszok.
- [Hibaelhárítás](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Útmutató: az áteresztő hitelesítés szolgáltatás kapcsolatos gyakori problémák megoldásához.
- [Az Azure AD zökkenőmentes SSO](active-directory-aadconnect-sso.md): további információk a kiegészítő funkció.
