---
title: A jelszóvisszaíró az Azure AD SSPR |} Microsoft Docs
description: Használja az Azure AD és az Azure AD Connect a visszaírási jelszavuk a helyi címtárhoz
services: active-directory
keywords: Az Active directory-jelszókezelés, jelszókezelés, az Azure AD self service jelszó alaphelyzetbe állítása
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: a7a81f51e0873da38f25deb687e4d03ccf67e11a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="password-writeback-overview"></a>Jelszó visszaírási áttekintése

A jelszóvisszaírás, az Azure Active Directory (Azure AD) a jelszavakat írhasson vissza a helyszíni Active Directory konfigurálható. A jelszóvisszaírás beállítása és kezelése bonyolult helyszíni önkiszolgáló jelszó-visszaállításból (SSPR) megoldást kell, és a felhasználók visszaállíthassák a helyszíni jelszavukat, bárhol legyenek is kényelmes felhőalapú megoldást biztosít. A jelszóvisszaírás összetevő [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) , hogy engedélyezve van, és támogatás az aktuális előfizetők által használt [Azure Active Directory-kiadások](active-directory-whatis.md).

A jelszóvisszaírás a következő szolgáltatásokat biztosítja:

* **Nulla késleltetésre visszajelzést biztosítanak a**: a jelszóvisszaírás egy aszinkron művelet. A felhasználók arról azonnal a jelszavát nem felelt meg a házirend vagy nem alaphelyzetbe állítása vagy bármilyen okból megváltozott.
* **Támogatja a jelszó alaphelyzetbe állítása a felhasználók számára, amelyek használják az Active Directory összevonási szolgáltatások (AD FS) vagy más összevonási technológiák**: A jelszóvisszaírás, mindaddig, amíg az összevont felhasználói fiókok szinkronizálása az Azure AD-bérlőn, képesek a helyszíni Active Directory-jelszavaikat felügyelete a felhőből.
* **Támogatja a jelszó alaphelyzetbe állítása használó felhasználók** [Jelszókivonat-szinkronizálás](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md): Ha a jelszó alaphelyzetbe állítása szolgáltatás észleli, hogy a szinkronizált felhasználói fiók engedélyezve van-e a Jelszókivonat-szinkronizálás, a rendszer visszaállítja-e mind a fiók a helyi és a felhő jelszó egyidejűleg.
* **Támogatja a jelszó alaphelyzetbe állítása a felhasználók számára, hogy átmenő hitelesítést használó**: a jelszóvisszaírás, mindaddig, amíg az áteresztő hitelesítés fiókok szinkronizálása az Azure AD-bérlő be tudja kezelni a helyszíni Active Directory-jelszavakkal a felhőből.
* **Támogatja a jelszó is módosul a hozzáférési panel és az Office 365**: Ha összevont vagy a jelszó szinkronizálva felhasználók jelszavukat lejárt vagy nem lejárt, azt e jelszavakat írhasson vissza a helyi Active Directory-környezet származnak.
* **Jelszavak visszaírása támogatja, amikor egy rendszergazda visszaállítja azokat az Azure portálról**: amikor egy rendszergazda a felhasználó jelszavának alaphelyzetbe állítása a [Azure-portálon](https://portal.azure.com), ha a felhasználónak össze van vonva, vagy a jelszó szinkronizálva lesznek állítva a jelszót a rendszergazda úgy dönt, valamint a helyi Active Directoryban. Ez a funkció jelenleg nem támogatott az Office felügyeleti portálon.
* **A helyszíni Active Directory-jelszó házirendek érvénybe lépteti**: amikor a felhasználó visszaállítja a jelszavát, hogy győződjön meg arról, hogy megfelel-e a helyszíni Active Directory házirendet ahhoz, hogy véglegesítheti a könyvtárhoz. A felülvizsgálati tartalmazza, az előzmények, összetettségét, kor, jelszószűrők és egyéb jelszó korlátozásokat a helyi Active Directory megadott ellenőrzését.
* **Nincs szükség a bejövő tűzfalszabályokat**: a jelszóvisszaírás egy Azure Service Bus relay használja, mint az alapul szolgáló kommunikációs csatornát. Nem kell megnyitni a bejövő portra a tűzfalon ahhoz, hogy a funkció használatához.
* **Védett csoportok a helyszíni Active Directoryban található felhasználói fiókok esetében nem támogatott**: védett csoportokkal kapcsolatos további információkért lásd: [védett fiókok és csoportok Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>A jelszóvisszaírás működése

Amikor egy összevont vagy a jelszó kivonatát szinkronizált felhasználói elérhető lesz az új, vagy módosítania kell a jelszavát, a felhőben, az alábbiak történnek:

1. Azt ellenőrzi, milyen típusú jelszót a felhasználó rendelkezik-e. Ha azt látja, hogy a jelszó helyszínen felügyelt:
   * Ellenőrizzük, ha a visszaírási szolgáltatás megfelelően működik, és. Ha fut, azt, hogy a felhasználó, a folytatáshoz.
   * A visszaírási szolgáltatás nem működik-e, ha azt a felhasználó értesítése, hogy a jelszó jelenleg nem állítható alaphelyzetbe.
2. Ezután a felhasználó a megfelelő hitelesítési kapui továbbítja, és eléri a **jelszó-átállítási** lap.
3. A felhasználó kiválaszt egy új jelszót, és megerősíti, hogy azt.
4. Amikor a felhasználó kijelöli **Submit**, azt a titkosítatlan szöveges jelszó titkosítás a visszaírási telepítés során létrehozott szimmetrikus kulccsal.
5. A jelszó-titkosítási, miután magában foglalja azt szerepel az adattartalomban, amely számára küldi el egy HTTPS-csatornán keresztül a bérlői-specifikus service bus-továbbító (beállított azt is, a visszaírási telepítés során). A továbbító védi, hogy csak a helyszíni telepítési ismeri véletlenszerűen létrehozott jelszót.
6. Az üzenet elérte a service bus, a jelszó-visszaállítási végpont automatikusan felébred, és láthatja, hogy rendelkezik-e a visszaállítási kérelem függőben.
7. A szolgáltatás majd keresi a felhasználó a felhő horgonyattribútum használatával. A kereséshez sikeres legyen:

    * A user objektum léteznie kell az Active Directory-összekötő terület.
    * A user objektum össze kell kapcsolni a megfelelő (MV) metaverzum-objektum.
    * A user objektum a megfelelő Azure Active Directory-összekötő objektum össze kell kapcsolni.
    * Az Active Directory-összekötő objektumból hivatkozásra a MV rendelkeznie kell a szinkronizálási szabály `Microsoft.InfromADUserAccountEnabled.xxx` hivatkozásra. <br> <br>
    A hívás a felhőből érkezik, a Szinkronizáló vezérlő használja a **cloudAnchor** megkeresheti az Azure Active Directory-összekötő terület objektum attribútum. Majd utáni vissza a hivatkozás a MV-objektum, és majd utáni vissza a hivatkozás az Active Directory-objektum. Több Active Directory-objektumok (Többerdős) a felhasználónak lehet, mert a szinkronizálási motor támaszkodik a `Microsoft.InfromADUserAccountEnabled.xxx` hivatkozás a megfelelő sablon kiválasztásához.

    > [!Note]
    > Miatt ez a módszer a jelszó visszaírási működéséhez az Azure AD Connect képesnek kell lennie az elsődleges tartományvezérlő (PDC) emulátor kommunikálni. Ennek engedélyezéséhez manuálisan kell, ha az Azure AD Connect csatlakozhat a PDC-emulátor. Kattintson a jobb gombbal a **tulajdonságok** az Active Directory-szinkronizálás összekötő, majd válassza ki **könyvtárpartíciók konfigurálása**. Ott, keresse meg a **tartományvezérlő kapcsolat beállításai** szakaszt, és jelölje be a négyzetet című **csak használja az elsődleges tartományvezérlő**. Akkor is, ha az elsődleges tartományvezérlő nem a PDC-emulátor, az Azure AD Connect megkísérli a jelszóvisszaírás PDC csatlakozni.

8. Után a felhasználói fiók található, azt megkísérli a jelszó közvetlenül a megfelelő Active Directory-erdőben.
9. Ha a jelszó-beállítási művelet sikeres, azt a felhasználó értesítése a jelszó megváltozott.
    > [!NOTE]
    > Ha a jelszó szinkronizálva az Azure AD-jelszó-szinkronizálás használatával, esély van, hogy a helyszíni jelszóházirend gyengébb, mint a felhő jelszóházirend-e. Ebben az esetben azt még kényszerítése függetlenül a helyi házirendet, és inkább a Jelszókivonat-szinkronizálást, a jelszó kivonatát szinkronizálni. Ez a házirend biztosítja, hogy a helyi házirend van érvényben a felhőben, attól függetlenül egyszeri bejelentkezést biztosítanak a jelszó-szinkronizálás és az összevonási használatakor.

10. Ha a jelszó művelet sikertelen lesz, azt hiba térjen vissza a felhasználói, és hogy azok próbálja meg újból. A művelet sikertelen lehet, mert:
    * A szolgáltatás nem működik.
    * A jelszót állított nem felelt meg a szervezet házirendjeit.
    * Azt előfordulhat, hogy a felhasználó nem található a helyi Active Directoryban.

    Ezekben az esetekben számos rendelkezik egy adott üzenet azt, és a felhasználó értesítése, mit tehet a probléma megoldásához.

## <a name="configure-password-writeback"></a>A jelszóvisszaírás konfigurálása

Azt javasoljuk, hogy használja-e az automatikus frissítési szolgáltatása [az Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) Ha jelszóvisszaírás használni kívánt.

A DirSync és az Azure AD Sync egyik módja a jelszóvisszaírás engedélyezése már nem támogatott. Segítséget az átállás további információkért lásd: [frissíthet a DirSync és Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md).

A következő lépések azt feltételezik, hogy már be vannak állítva az Azure AD Connect a környezet használatával a [Express](./connect/active-directory-aadconnect-get-started-express.md) vagy [egyéni](./connect/active-directory-aadconnect-get-started-custom.md) beállításait.

1. Jelentkezzen be az Azure AD Connect-kiszolgáló konfigurálásához, és a jelszóvisszaíró engedélyezéséhez, és indítsa el a **az Azure AD Connect** konfigurációs varázsló.
2. Az a **üdvözlő** lapon jelölje be **konfigurálása**.
3. Az a **további feladatok** lapon jelölje be **testre szabhatja a szinkronizálási beállítások**, majd válassza ki **következő**.
4. Az a **az Azure AD Connect** lapon adjon meg egy globális rendszergazdai hitelesítő adatok, és válassza ki **következő**.
5. Az a **könyvtárak csatlakozás** és **tartomány vagy szervezeti egység** kiválasztása lapok szűrése, **tovább**.
6. A a **választható szolgáltatások** lapján jelölje be a jelölőnégyzetet a **jelszóvisszaírás** válassza **következő**.
   ![Az Azure AD Connectben a jelszóvisszaírás engedélyezése][Writeback]
7. Az a **beállíthatja az** lapon jelölje be **konfigurálása** és várja meg a folyamat befejezéséhez.
8. Amikor megjelenik a konfigurációs Befejezés, válassza ki a **kilépési**.

A jelszóvisszaírás, kapcsolódó általános hibaelhárítási műveletek című [jelszóvisszaírás hibaelhárítása](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) a hibaelhárítási cikkben.

## <a name="active-directory-permissions"></a>Active Directory-engedélyek

A fiók megadva, az Azure AD Connect segédprogramot. a következő elemek be kell ha sspr hatókörben kell:

* **Új jelszó létrehozása** 
* **Jelszó módosítása** 
* **Írási engedélyek** a `lockoutTime`  
* **Írási engedélyek** a `pwdLastSet`
* **Bővített jogosultságokkal** akár:
   * A legfelső szintű objektumának *tartományonként* az adott erdőben
   * A felhasználó szervezeti egységben (OU) szeretné sspr hatókörében

Ha nem tudja, melyik fiókra a ismertetett fiókot hivatkozik, nyissa meg az Azure Active Directory Connect konfigurációs felhasználói felület, és válassza ki a **aktuális konfiguráció megtekintése** lehetőséget. A fiókot, amelyet hozzá kell adnia, hogy megtalálható-e engedéllyel **címtárak szinkronizálása**.

Ha ezeket az engedélyeket, az egyes erdőkhöz MA-szolgáltatásfiókja kezelheti jelszavakat az erdőben lévő felhasználói fiókok nevében. 

> [!IMPORTANT]
> Ha nem rendeli hozzá ezeket az engedélyeket, majd, annak ellenére, hogy a visszaírás konfigurációja megfelelőnek, megjelenik a felhasználók hibákat tapasztalnak, amikor azok megkísérlik a helyszíni jelszavak kezeléséhez a felhőből.
>

> [!NOTE]
> Akkor lehet, hogy eltarthat egy óráig vagy tovább ezen engedélyeket a a címtár összes objektumába replikálja.
>

A jelszóvisszaírás megtörténik a megfelelő engedélyek beállítása, végezze el az alábbi lépéseket:

1. Nyissa meg az Active Directory – felhasználók és számítógépek egy olyan fiókkal, amely a megfelelő tartományi rendszergazdai engedélyekkel rendelkezik.
2. Az a **nézet** menü, győződjön meg arról, hogy **speciális szolgáltatások** be van kapcsolva.
3. A bal oldali panelen kattintson a jobb gombbal a tartomány, és válassza ki a legfelső szintű képviselő objektum **tulajdonságok** > **biztonsági** > **speciális**.
4. Az a **engedélyek** lapon jelölje be **Hozzáadás**.
5. Válassza ki a fiókot, amely engedélyek alkalmazott (az Azure AD Connect telepítőt).
6. Az a **vonatkozik** legördülő listában válassza **leszármazott felhasználó** objektumok.
7. A **engedélyek**, válassza ki a mezőket a következő:
    * **Új jelszó létrehozása**
    * **Jelszó módosítása**
    * **LockoutTime írása**
    * **PwdLastSet írása**
8. Válassza ki **alkalmaz/OK** alkalmazni a változtatásokat, és lépjen ki az összes megnyitott párbeszédpanelen.

## <a name="licensing-requirements-for-password-writeback"></a>A jelszóvisszaírás licencelési követelményei

Licenceléssel kapcsolatos információkért lásd: [jelszóvisszaírás szükséges licencek](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) vagy a következő helyeken:

* [Az Azure Active Directory-hely díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>A jelszóvisszaírás támogató helyszíni hitelesítési módot

A következő felhasználói jelszó típus jelszó visszaírási támogatását:

* **Csak felhőalapú felhasználók**: a jelszóvisszaírás nem vonatkozik ebben a helyzetben, mert nem a helyszíni jelszó.
* **Jelszó-szinkronizált felhasználók**: a jelszóvisszaírás esetén támogatott.
* **Összevont felhasználók**: a jelszóvisszaírás esetén támogatott.
* **Áteresztő hitelesítés felhasználók**: a jelszóvisszaírás esetén támogatott.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>A jelszóvisszaírás támogató felhasználói és rendszergazdai műveletek

Jelszavak írt vissza a következő esetekben:

* **Támogatott végfelhasználói műveletek**
  * A végfelhasználói önkiszolgáló önkéntes jelszó művelet módosítása
  * Minden végfelhasználói önkiszolgáló kényszerített jelszó művelet, például a jelszavak módosítása
  * A végfelhasználói önkiszolgáló jelszó-átállítási származó a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com)
* **A rendszergazda támogatott műveletek**
  * A rendszergazda önkiszolgáló önkéntes jelszó művelet módosítása
  * Minden rendszergazda önkiszolgáló kényszerített jelszó művelet, például a jelszavak módosítása
  * A rendszergazda az önkiszolgáló jelszó-változtatási származó a [jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com)
  * Minden rendszergazda által kezdeményezett végfelhasználói jelszót alaphelyzetbe állítani a [Azure-portálon](https://portal.azure.com)

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Felhasználói és rendszergazdai műveletek nem támogatottak a jelszóvisszaíró

A jelszavak *nem* visszaírását a következő esetek bármelyikében:

* **Nem támogatott a végfelhasználói műveletek**
  * A végfelhasználók a saját jelszó alaphelyzetbe állításával PowerShell verzió: 1, 2-es vagy az Azure AD Graph API használatával
* **A rendszergazda nem támogatott műveletek**
  * Minden rendszergazda által kezdeményezett végfelhasználói jelszót alaphelyzetbe állítani a [Office felügyeleti portálon](https://portal.office.com)
  * A rendszergazda által kezdeményezett végfelhasználói jelszó-változtatási PowerShell verzió: 1, 2-es verzióját vagy az Azure AD Graph API

Ezekkel a korlátozásokkal eltávolítása dolgozunk, de egy meghatározott ütemterv is osztjuk meg, még nem tudunk.

## <a name="password-writeback-security-model"></a>Jelszó visszaírási biztonsági modell

A jelszóvisszaírás egy olyan rendkívül biztonságos szolgáltatás. Annak érdekében, hogy az adatok védelméről is gondoskodik, engedélyezzük egy négy rétegzett biztonsági modellnek, amelyek leírása a következő:

* **Bérlői-specifikus service-bus-továbbító**
  * A szolgáltatás beállítása, beállítjuk a bérlő-specifikus service bus-továbbító, amely Microsoft soha nem rendelkezik hozzáféréssel véletlenszerűen létrehozott erős jelszóval védett.
* **Zárolt, dokumentumtitkosítási erős jelszó-titkosítási kulcs**
  * A service bus relay létrehozása után létrehozhatunk használjuk a hálózaton keresztül ismét a jelszót titkosítására erős szimmetrikus kulcsot. Ezt a kulcsot csak él, a vállalat titkos tároló a felhőben, ami erősen zárolva, és naplózva, csakúgy, mint bármely más jelszót a címtárban.
* **Iparági szabványos Transport Layer Security (TLS)**
  1. Ha a jelszó alaphelyzetbe állítása, vagy módosítsa a művelethez a felhőben, azt a titkosítatlan szöveges jelszó igénybe vehet, és a nyilvános kulcs titkosításához.
  2. A Microsoft helyezzen el, amely a service bus-továbbító a Microsoft SSL-tanúsítványok használatával egy titkosított csatornán keresztül küldött HTTPS üzenetben.
  3. Az üzenet érkezik a service bus, miután a helyszíni ügynök felébred, és hitelesíti a service bus számára a korábban létrehozott erős jelszót.
  4. A helyszíni ügynök szerzi be a titkosított üzenetet, és visszafejti a Microsoft hozza létre a titkos kulcsot használja.
  5. A helyszíni ügynök próbálja beállítani a jelszót az AD DS SetPassword API-n keresztül. Ez a lépés nem mi lehetővé teszi az Active Directory a helyszíni jelszóházirend (például a összetettségét, kor, előzményei és szűrők) kényszerítése a felhőben.
* **Üzenet lejárati házirendek** 
  * Ha az üzenet a service bus helyezkedik el, mert a helyszíni szolgáltatás le van állítva, érvénytelenné válik, és pár perc múlva törlődnek. Az időkorlát és az üzenet eltávolítása növeli a biztonságot még tovább.

### <a name="password-writeback-encryption-details"></a>Jelszó visszaírási titkosítási részletek

Miután a felhasználó elküldi az új jelszó létrehozását, a visszaállítási kérelem végig kell vinnie számos titkosítási lépést a helyszíni környezetben megérkezése előtt. Ezek a titkosítás lépései győződjön meg arról, maximális szolgáltatás megbízhatóságát. Azok az alábbiak szerint:

* **1. lépés: Jelszó-titkosítási 2048 bites RSA-kulcsával**: visszaírását a helyszíni jelszó elküldése után a beküldött jelszó magát egy 2048 bites RSA kulcs van titkosítva.
* **2. lépés: Csomag szintű titkosítást AES-GCM-mel**: A teljes csomag, a jelszó és a szükséges metaadatok AES-GCM használatával titkosítja. A titkosítás megakadályozza, hogy bárki, aki közvetlen hozzáféréssel az alapul szolgáló Szolgáltatásbusz-csatornához megtekintése vagy illetéktelen módosítást a tartalma.
* **3. lépés: Az összes kommunikáció a TLS/SSL keresztül történik.**: Szolgáltatásbusz kommunikációt történik, az SSL/TLS-csatorna. Titkosítási a tartalom jogosulatlan harmadik felek biztonságossá tételére.
* **Automatikus kulcsváltást hathavonta**: félévente, vagy minden alkalommal jelszóvisszaírás le van tiltva, majd újra engedélyezik, az Azure AD Connect. A Microsoft automatikusan maximális szolgáltatás biztonsági és biztonságának minden kulcs váltása.

### <a name="password-writeback-bandwidth-usage"></a>Jelszó visszaírási sávszélesség-használat

A jelszóvisszaírás egy kis sávszélességű szolgáltatás, amely csak kérést küld vissza a helyszíni ügynök a következő esetekben:

* Két üzenetek küldése történik, ha a szolgáltatás engedélyezve van-e az Azure AD Connect használatával.
* Mindaddig, amíg a szolgáltatás fut egy üzenetet küld 5 percenként egyszer, a szolgáltatás szívverést.
* Két üzenetek küldése történik, minden egyes új jelszó küldése idő:
    * Az első üzenet tulajdonképpen egy kérelem a művelet végrehajtásához.
    * A második ilyen üzenet tartalmazza a művelet eredményét, majd továbbítja a következő esetekben:
        * Minden alkalommal, amikor a felhasználó önkiszolgáló jelszó alaphelyzetbe állítása során egy új jelszó küldése.
        * Minden alkalommal, amikor egy új jelszó küldése a felhasználói jelszó-változtatási művelet során.
        * Minden alkalommal, amikor egy új jelszó küldése során egy rendszergazda által kezdeményezett felhasználói jelszó alaphelyzetbe állítása (csak az Azure felügyeleti portálon).

#### <a name="message-size-and-bandwidth-considerations"></a>Üzenet mérete és a sávszélesség kapcsolatos szempontok

Minden, az üzenet a fentiekben ismertetett mérete jellemzően 1 KB alatt. Maga a jelszó visszaírási szolgáltatás alatt szélsőséges terhelés esetén is nem használ-e a sávszélesség néhány kilobit / másodperc. Minden üzenetet a rendszer a valós idejű küldi el, mert csak akkor, ha a jelszó-frissítési művelet által igényelt, és az üzenet mérete ezért kicsi, mert a sávszélesség-használat a visszaírási funkció nem elég nagy mérhető hatással van.

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](active-directory-passwords-best-practices.md)
* [Új jelszó kérése vagy jelszó módosítása](active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](active-directory-passwords-reset-register.md).
* [Kérdése van a licenceléssel kapcsolatban?](active-directory-passwords-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](active-directory-passwords-data.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](active-directory-passwords-policy.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](active-directory-passwords-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](active-directory-passwords-how-it-works.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Az Azure AD Connectben a jelszóvisszaírás engedélyezése"
