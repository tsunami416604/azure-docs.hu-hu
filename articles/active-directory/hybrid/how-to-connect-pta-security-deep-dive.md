---
title: Az Azure Active Directory átamenő hitelesítésbiztonsági részletes adatkapcsolata| Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogy az Azure Active Directory (Azure AD) átmenő hitelesítése hogyan védi a helyszíni fiókokat
services: active-directory
keywords: Azure AD Connect áthaladási hitelesítés, active directory telepítése, szükséges összetevők az Azure AD, Egyszeri bejelentkezés, Egyszeri bejelentkezés hez
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ddce8d4d7ca1f03c0a57d0f0c8c41ac122973e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185556"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Az Azure Active Directory átamenő hitelesítésbiztonsági részletes merülése

Ez a cikk részletesebb leírást ad az Azure Active Directory (Azure AD) áthaladási hitelesítés működéséről. A funkció biztonsági szempontjaira összpontosít. Ez a cikk a biztonsági és informatikai rendszergazdák, a vezető megfelelőségi és biztonsági tisztviselők, valamint más informatikai szakemberek, akik felelősek az informatikai biztonság és a megfelelőség a kis-és középvállalkozások vagy nagyvállalatok.

A tárgyalt témák a következők:
- Részletes technikai információk a hitelesítési ügynökök telepítéséről és regisztrálásáról.
- Részletes technikai információk a jelszavak titkosításáról a felhasználó bejelentkezése során.
- A csatornák biztonsága a helyszíni hitelesítési ügynökök és az Azure AD között.
- Részletes technikai információk ahitelesítési ügynökök működési biztonságának megőrzéséről.
- Egyéb, biztonsággal kapcsolatos témakörök.

## <a name="key-security-capabilities"></a>A legfontosabb biztonsági funkciók

Ezek a legfontosabb biztonsági szempontok a funkció:
- Egy biztonságos, több-bérlős architektúrára épül, amely biztosítja a bejelentkezési kérelmek elkülönítését a bérlők között.
- A helyszíni jelszavakat soha nem tárolja a felhőben semmilyen formában.
- A jelszó-érvényesítési kérelmeket figyelő helyszíni hitelesítési ügynökök csak a hálózaton belülről létesítenek kimenő kapcsolatokat. Nincs követelmény, hogy ezeket a hitelesítési ügynököket peremhálózaton (DMZ) telepítse. Ajánlott eljárásként a hitelesítési ügynököket futtató összes kiszolgálót tier 0 rendszerként kezelje (lásd a [hivatkozást).](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)
- Csak a szabványos portok (80 és 443) a hitelesítési ügynökök és az Azure AD kimenő kommunikációja. Nem kell megnyitnia a bejövő portokat a tűzfalon. 
  - A 443-as port minden hitelesített kimenő kommunikációhoz használatos.
  - A 80-as port csak a visszavont tanúsítványok listájának (CRL) letöltésére szolgál annak biztosítására, hogy a szolgáltatás által használt tanúsítványok egyike sem lett visszavonva.
  - A hálózati követelmények teljes listáját az [Azure Active Directory átamenő hitelesítés: gyorsútmutató](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)című témakörben tartalmazza.
- A bejelentkezés során megadott jelszavak a felhőben titkosítva vannak, mielőtt a helyszíni hitelesítési ügynökök elfogadják azokat az Active Directoryval való érvényesítéshez.
- Az Azure AD és a helyszíni hitelesítési ügynök közötti HTTPS-csatorna kölcsönös hitelesítéssel védett.
- Védi a felhasználói fiókokat az [Azure AD feltételes hozzáférésházirendjeivel](../active-directory-conditional-access-azure-portal.md)való zökkenőmentes együttműködéssel, beleértve a többtényezős hitelesítést (MFA), [az örökölt hitelesítés blokkolását](../conditional-access/concept-conditional-access-conditions.md) és a [találgatásos jelszótámadások kiszűrésével.](../authentication/howto-password-smart-lockout.md)

## <a name="components-involved"></a>Érintett összetevők

Az Azure AD működési, szolgáltatás- és adatbiztonsággal kapcsolatos általános részleteket az [Adatvédelmi központban talál.](https://azure.microsoft.com/support/trust-center/) A következő összetevők et használja, ha átmenő hitelesítést használ a felhasználói bejelentkezéshez:
- **Azure AD STS:** Állapotnélküli biztonsági jogkivonat-szolgáltatás (STS), amely feldolgozza a bejelentkezési kérelmeket, és szükség szerint biztonsági jogkivonatokat ad ki a felhasználók böngészőinek, ügyfeleinek vagy szolgáltatásainak.
- **Azure Service Bus**: Felhőalapú kommunikációt biztosít a vállalati üzenetküldési és továbbítási kommunikációval, amely segít a helyszíni megoldások és a felhő összekapcsolásához.
- **Azure AD Connect hitelesítési ügynök:** A helyszíni összetevő, amely figyeli, és válaszol a jelszó érvényesítési kérelmeket.
- **Azure SQL Database:** A bérlő hitelesítési ügynökei adatait tartalmazza, beleértve a metaadatokat és a titkosítási kulcsokat.
- **Active Directory**: A helyszíni Active Directory, ahol a felhasználói fiókok és jelszavuk tárolódnak.

## <a name="installation-and-registration-of-the-authentication-agents"></a>A hitelesítési megbízottak telepítése és regisztrálása

A hitelesítési ügynökök telepítése és regisztrálása az Azure AD-nél akkor lesz telepítve és regisztrálva, ha:
   - [Átmenő hitelesítés engedélyezése az Azure AD Connecten keresztül](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [További hitelesítési ügynökök hozzáadása a bejelentkezési kérelmek magas rendelkezésre állásának biztosításához](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
A hitelesítési ügynök működésének beszerzése három fő fázist foglal magában:

1. Hitelesítési ügynök telepítése
2. Hitelesítési ügynök regisztrálása
3. A hitelesítési ügynök inicializálása

A következő szakaszok részletesen ismertetik ezeket a fázisokat.

### <a name="authentication-agent-installation"></a>Hitelesítési ügynök telepítése

Csak a globális rendszergazdák telepíthetnek egy hitelesítési ügynököt (az Azure AD Connect vagy önálló használatával) egy helyszíni kiszolgálóra. A telepítés két új bejegyzést ad hozzá a **Vezérlőpult** > **programok** > **és szolgáltatások** listájához:
- Magát a hitelesítési ügynök alkalmazást. Ez az alkalmazás [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) jogosultságokkal fut.
- A hitelesítési ügynök automatikus frissítéséhez használt Frissítő alkalmazás. Ez az alkalmazás [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) jogosultságokkal fut.

### <a name="authentication-agent-registration"></a>Hitelesítési ügynök regisztrálása

A hitelesítési ügynök telepítése után regisztrálnia kell magát az Azure AD-vel. Az Azure AD minden hitelesítési ügynökhöz egyedi, digitális identitástanúsítványt rendel, amelyet az Azure AD-vel való biztonságos kommunikációhoz használhat.

A regisztrációs eljárás a hitelesítési ügynököt is összeköti a bérlővel. Ez biztosítja, hogy az Azure AD tudja, hogy ez az adott hitelesítési ügynök az egyetlen, amely jogosult a bérlő jelszó-érvényesítési kérelmek kezelésére. Ez az eljárás minden egyes regisztrálott új hitelesítési ügynök esetében megismétlődik.

A hitelesítési ügynökök a következő lépésekkel regisztrálják magukat az Azure AD-vel:

![Ügynök regisztrációja](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Az Azure AD először azt kéri, hogy egy globális rendszergazda jelentkezzen be az Azure AD-be a hitelesítő adataikkal. A bejelentkezés során a hitelesítési ügynök beszerez egy hozzáférési jogkivonatot, amelyet a globális rendszergazda nevében használhat.
2. A hitelesítési ügynök ezután létrehoz egy kulcspárt: egy nyilvános kulcsot és egy titkos kulcsot.
    - A kulcspár szabványos RSA 2048 bites titkosítással jön létre.
    - A személyes kulcs a helyszíni kiszolgálón marad, ahol a hitelesítési ügynök található.
3. A hitelesítési ügynök "regisztrációs" kérelmet küld az Azure AD-nek HTTPS-kapcsolaton keresztül, a kérelemben szereplő alábbi összetevőkkel:
    - Az 1.
    - A 2.
    - Tanúsítványaláíró kérelem (CSR vagy tanúsítványkérelem). Ez a kérelem egy digitális identitástanúsítványra vonatkozik, amelynek az Azure AD a hitelesítésszolgáltatója.
4. Az Azure AD érvényesíti a hozzáférési jogkivonatot a regisztrációs kérelemben, és ellenőrzi, hogy a kérelem egy globális rendszergazdatől érkezett-e.
5. Az Azure AD majd aláírja, és elküldi a digitális identitástanúsítványt vissza a hitelesítési ügynök.
    - Az Azure AD gyökérhitelesítési szolgáltatása a tanúsítvány aláírására szolgál. 

      > [!NOTE]
      > Ez a hitelesítésszolgáltató _nem_ található a Windows megbízható legfelső szintű tanúsítványszolgáltatói tárolójában.
    - A hitelesítésszolgáltatót csak az átmenő hitelesítés i. A hitelesítésszolgáltató csak az előhitelesítési szerepkört írja alá a hitelesítési ügynök regisztrációja során.
    -  A többi Azure AD-szolgáltatás egyike sem használja ezt a hitelesítésszolgáltatót.
    - A tanúsítvány tulajdonosának (Megkülönböztető név vagy DN) a bérlői azonosítója van beállítva. Ez a DN egy GUID, amely egyedileg azonosítja a bérlőt. Ez a DN a tanúsítványt csak a bérlővel való használatra használja.
6. Az Azure AD tárolja a hitelesítési ügynök nyilvános kulcsát egy Azure SQL-adatbázisban, amelyhez csak az Azure AD rendelkezik hozzáféréssel.
7. A tanúsítvány (az 5. lépésben kiadva) a Windows tanúsítványtárolóban (különösen a [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) helyen) található helyszíni kiszolgálón található. A hitelesítési ügynök és az frissítő alkalmazások is használják.

### <a name="authentication-agent-initialization"></a>A hitelesítési ügynök inicializálása

Amikor a hitelesítési ügynök elindul, vagy a regisztráció után első alkalommal, vagy a kiszolgáló újraindítása után, szüksége van egy módja annak, hogy biztonságosan kommunikáljon az Azure AD szolgáltatással, és megkezdje a jelszó-érvényesítési kérelmek fogadását.

![Ügynök inicializálása](./media/how-to-connect-pta-security-deep-dive/pta2.png)

A hitelesítési megbízottak inicializálása a következőképpen történik:

1. A hitelesítési ügynök kimenő rendszerindítási kérelmet készít az Azure AD-nek. 
    - Ez a kérés a 443-as porton keresztül történik, és egy kölcsönösen hitelesített HTTPS-csatornán keresztül történik. A kérelem ugyanazt a tanúsítványt használja, amelyet a hitelesítési ügynök regisztrálása során kapott.
2. Az Azure AD úgy válaszol a kérésre, hogy egy hozzáférési kulcsot biztosít egy Azure Service Bus-várólistához, amely egyedi a bérlőszámára, és amelyet a bérlőazonosító azonosít.
3. A hitelesítési ügynök állandó kimenő HTTPS-kapcsolatot hoz létre (a 443-as porton keresztül) a várólistához. 
    - A hitelesítési ügynök most már készen áll a jelszó-érvényesítési kérelmek beolvasására és kezelésére.

Ha több hitelesítési ügynökök regisztrálva van a bérlőn, majd az inicializálási eljárás biztosítja, hogy mindegyik csatlakozik az azonos Service Bus-várólistához. 

## <a name="process-sign-in-requests"></a>Bejelentkezési kérelmek feldolgozása 

Az alábbi ábra bemutatja, hogyan dolgozza fel az átmenő hitelesítés a felhasználói bejelentkezési kérelmeket.

![Bejelentkezés folyamata](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Az átmenő hitelesítés a következő módon kezeli a felhasználói bejelentkezési kérelmet: 

1. A felhasználó megpróbál hozzáférni egy alkalmazáshoz, például az [Outlook Web Apphoz.](https://outlook.office365.com/owa)
2. Ha a felhasználó még nincs bejelentkezve, az alkalmazás átirányítja a böngészőt az Azure AD bejelentkezési oldalra.
3. Az Azure AD STS szolgáltatás válaszol a **felhasználó bejelentkezési** lap.
4. A felhasználó beírja a felhasználónevét a **Felhasználó bejelentkezési** lapjára, majd a **Tovább** gombra kattint.
5. A felhasználó beírja a jelszavát a **Felhasználó bejelentkezési** lapjára, majd kiválasztja a **Bejelentkezés** gombot.
6. A felhasználónevet és a jelszót az Azure AD STS egy HTTPS POST-kérelemben küldi el.
7. Az Azure AD STS lekéri a nyilvános kulcsokat a bérlőn regisztrált összes hitelesítési ügynökök az Azure SQL-adatbázisból, és titkosítja a jelszót azok használatával.
    - "N" titkosított jelszóértékeket állít elő a bérlőn regisztrált "N" hitelesítési ügynökökhöz.
8. Az Azure AD STS a jelszó-érvényesítési kérelmet, amely a felhasználónévből és a titkosított jelszóértékekből áll, a bérlőre jellemző Service Bus-várólistára helyezi.
9. Mivel az inicializált hitelesítési ügynökök állandóan kapcsolódnak a Service Bus várólistához, az egyik elérhető hitelesítési ügynök lekéri a jelszó-érvényesítési kérelmet.
10. A hitelesítési ügynök azonosító használatával megkeresi a nyilvános kulcsra jellemző titkosított jelszóértéket, és visszafejti azt a személyes kulcsával.
11. A hitelesítési ügynök a [win32-es LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) használatával megpróbálja érvényesíteni a felhasználónevet és a jelszót érvényesíteni, és a **dwLogonType** paramétert **LOGON32_LOGON_NETWORK.** 
    - Ez az API ugyanaz az API, amelyet az Active Directory összevonási szolgáltatások (AD FS) használnak a felhasználók bebejelentkezéséhez egy összevont bejelentkezési forgatókönyvben.
    - Ez az API a Windows Server szabványos felbontási folyamatára támaszkodik a tartományvezérlő megkereséséhez.
12. A hitelesítési ügynök az eredményt az Active Directorytól kapja, például a sikeres, a felhasználónév vagy a jelszó helytelen, vagy a jelszó lejárt.

   > [!NOTE]
   > Ha a hitelesítési ügynök nem sikerül a bejelentkezési folyamat során, a teljes bejelentkezési kérelem megszakad. Az egyik hitelesítési ügynök bejelentkezési kérelmek nem adnak ki a helyszínen egy másik hitelesítési ügynöknek. Ezek az ügynökök csak a felhővel kommunikálnak, egymással nem.
   
13. A hitelesítési ügynök továbbítja az eredményt vissza az Azure AD STS egy kimenő kölcsönösen hitelesített HTTPS-csatorna a 443-as porton keresztül. A kölcsönös hitelesítés a hitelesítési ügynök számára a regisztráció során korábban kiállított tanúsítványt használja.
14. Az Azure AD STS ellenőrzi, hogy ez az eredmény korrelál-e a bérlőn megadott bejelentkezési kérelemmel.
15. Az Azure AD STS a konfigurált bejelentkezési eljárással folytatódik. Ha például a jelszó érvényesítése sikeres volt, a felhasználó megtámadható a többtényezős hitelesítés, vagy átirányítja vissza az alkalmazásba.

## <a name="operational-security-of-the-authentication-agents"></a>A hitelesítési megbízottak üzembiztonsága

Annak érdekében, hogy az átadó hitelesítés továbbra is működőképes maradjon, az Azure AD rendszeres időközönként megújítja a hitelesítési ügynökök tanúsítványait. Az Azure AD elindítja a megújításokat. A megújításokra nem maguk a hitelesítési ügynökök vonatkoznak.

![Működési biztonság](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Hitelesítési ügynök megbízhatóságának megújítása az Azure AD-vel:

1. A hitelesítési ügynök rendszeresen pingeli az Azure AD-t néhány óránként, hogy ellenőrizze, hogy itt az ideje megújítani a tanúsítványt. A tanúsítvány 30 nappal a lejárata előtt megújul.
    - Ez az ellenőrzés egy kölcsönösen hitelesített HTTPS-csatornán keresztül történik, és ugyanazt a tanúsítványt használja, amelyet a regisztráció során adtak ki.
2. Ha a szolgáltatás azt jelzi, hogy ideje megújítani, a hitelesítési ügynök létrehoz egy új kulcspárt: egy nyilvános kulcsot és egy személyes kulcsot.
    - Ezek a kulcsok szabványos RSA 2048 bites titkosítással jönnek létre.
    - A személyes kulcs soha nem hagyja el a helyszíni kiszolgálót.
3. A hitelesítési ügynök ezután "tanúsítványmegújítási" kérelmet küld az Azure AD-nek HTTPS-en keresztül, a kérelemben szereplő alábbi összetevőkkel:
    - A Windows tanúsítványtároló CERT_SYSTEM_STORE_LOCAL_MACHINE helyéről beolvasott meglévő tanúsítvány. Ebben az eljárásban nincs globális rendszergazda, így nincs szükség hozzáférési jogkivonatra a globális rendszergazda nevében.
    - A 2.
    - Tanúsítványaláíró kérelem (CSR vagy tanúsítványkérelem). Ez a kérelem egy új digitális identitástanúsítványra vonatkozik, amelynek tanúsítványszolgáltatója az Azure AD.
4. Az Azure AD érvényesíti a meglévő tanúsítványt a tanúsítványmegújítási kérelemben. Ezután ellenőrzi, hogy a kérelem egy, a bérlőn regisztrált hitelesítési ügynöktől érkezett-e.
5. Ha a meglévő tanúsítvány továbbra is érvényes, az Azure AD majd aláírja az új digitális identitás tanúsítványt, és kiadja az új tanúsítványt vissza a hitelesítési ügynök. 
6. Ha a meglévő tanúsítvány lejárt, az Azure AD törli a hitelesítési ügynök a bérlő regisztrált hitelesítési ügynökök listájáról. Ezután egy globális rendszergazdának manuálisan kell telepítenie és regisztrálnia egy új hitelesítési ügynököt.
    - Használja az Azure AD legfelső szintű hitelesítésszolgáltató a tanúsítvány aláírásához.
    - Állítsa be a tanúsítvány tulajdonosát (Megkülönböztető név vagy DN) a bérlői azonosítóra, egy GUID-ra, amely egyedileg azonosítja a bérlőt. A DN csak a bérlőnek teszi ki a tanúsítványt.
6. Az Azure AD tárolja a hitelesítési ügynök új nyilvános kulcsát egy Azure SQL-adatbázisban, amelyhez csak az rendelkezik hozzáféréssel. Emellett érvényteleníti a hitelesítési ügynökhöz társított régi nyilvános kulcsot is.
7. Az új tanúsítvány (az 5. lépésben kiadva) ezután a kiszolgálón tárolódik a Windows tanúsítványtárolóban (különösen a [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) helyen).
    - Mivel a megbízhatóság megújítására irányuló eljárás nem interaktív módon történik (a globális rendszergazda jelenléte nélkül), a hitelesítési ügynök nek már nincs hozzáférése a meglévő tanúsítvány frissítéséhez a CERT_SYSTEM_STORE_LOCAL_MACHINE helyen. 
    
   > [!NOTE]
   > Ez az eljárás nem távolítja el magát a tanúsítványt a CERT_SYSTEM_STORE_LOCAL_MACHINE helyről.
8. Ettől a ponttól kezdve az új tanúsítvány hitelesítésre szolgál. A tanúsítvány minden ezt követő megújítása helyettesíti a tanúsítványt a CERT_SYSTEM_STORE_LOCAL_MACHINE helyen.

## <a name="auto-update-of-the-authentication-agents"></a>A hitelesítési megbízottak automatikus frissítése

Az Updater alkalmazás automatikusan frissíti a hitelesítési ügynököt, ha új verzió (hibajavításokkal vagy teljesítménybeli fejlesztésekkel) kerül kiadásra. Az Updater alkalmazás nem kezeli a bérlő jelszó-érvényesítési kérelmeit.

Az Azure AD a szoftver új verzióját egy aláírt **Windows Installer csomagként (MSI)** látja el. Az MSI a [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) és az SHA256 segítségével van aláírva kivonatoló algoritmusként. 

![Automatikus frissítés](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Hitelesítési ügynök automatikus frissítése:

1. Az Updater alkalmazás óránként pingeli az Azure AD-t, hogy ellenőrizze, hogy elérhető-e a hitelesítési ügynök új verziója. 
    - Ez az ellenőrzés egy kölcsönösen hitelesített HTTPS-csatornán keresztül történik, a regisztráció során kiadott tanúsítvány használatával. A hitelesítési ügynök és az frissítő megosztja a kiszolgálón tárolt tanúsítványt.
2. Ha egy új verzió érhető el, az Azure AD visszaadja az aláírt MSI-t az frissítőnek.
3. Az frissítő ellenőrzi, hogy az MSI-t a Microsoft aláírta-e.
4. Az Updater futtatja az MSI-t. Ez a művelet a következő lépéseket foglalja magában:

   > [!NOTE]
   > Az frissítő [helyi rendszerjogosultságokkal](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) fut.

    - A hitelesítési ügynök szolgáltatás leállítása
    - A hitelesítési ügynök új verziójának telepítése a kiszolgálóra
    - Újraindítja a hitelesítési ügynök szolgáltatást

>[!NOTE]
>Ha több hitelesítési ügynökök regisztrálva van a bérlőn, az Azure AD nem újítja meg a tanúsítványokat, vagy frissíti őket egyszerre. Ehelyett az Azure AD ezt egyenként biztosítja a bejelentkezési kérelmek magas rendelkezésre állását.
>


## <a name="next-steps"></a>További lépések
- [Jelenlegi korlátozások](how-to-connect-pta-current-limitations.md): Ismerje meg, hogy mely forgatókönyvek támogatottak, és melyek nem.
- [Rövid útmutató:](how-to-connect-pta-quick-start.md)Az Azure AD áthaladási hitelesítése.
- [Az AD FS-ről átmenő hitelesítésre való áttelepítés](https://aka.ms/adfstoptadpdownload) részletes útmutató az AD FS-ről (vagy más összevonási technológiákról) az átmenő hitelesítésre való áttelepítéshez.
- [Intelligens zárolás:](../authentication/howto-password-smart-lockout.md)Konfigurálja a bérlő intelligens zárolási képességét a felhasználói fiókok védelme érdekében.
- [Hogyan működik:](how-to-connect-pta-how-it-works.md)Ismerje meg az Azure AD áthaladási hitelesítés működésének alapjait.
- [Gyakori kérdések](how-to-connect-pta-faq.md): Válaszok a gyakori kérdésekre.
- [Hibaelhárítás:](tshoot-connect-pass-through-authentication.md)Ismerje meg, hogyan oldhatja meg az átmenő hitelesítés szolgáltatással kapcsolatos gyakori problémákat.
- [Azure AD seamless egyszeri bejelentkezés:](how-to-connect-sso.md)További információ erről a kiegészítő funkcióról.
