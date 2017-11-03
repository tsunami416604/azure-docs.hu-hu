---
title: "A jelszóvisszaíró az Azure AD SSPR |} Microsoft Docs"
description: "Az Azure AD és az Azure AD használatával a helyszíni jelszavakat visszaírási kapcsolódás"
services: active-directory
keywords: "Az Active directory-jelszókezelés, jelszókezelés, az Azure AD self service jelszó alaphelyzetbe állítása"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 8ce4d6d9024dc4ce3956220eb0678a6295b0b7ab
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="password-writeback-overview"></a>Jelszó visszaírási áttekintése

A jelszóvisszaírás Azure AD-jelszavakat írhasson vissza a helyszíni Active Directory konfigurálását teszi lehetővé. Azt nem kell beállítása és kezelése bonyolult helyszíni önkiszolgáló jelszó-visszaállítási megoldás, és a felhasználók visszaállíthassák a helyszíni jelszavukat, bárhol legyenek is kényelmes felhőalapú megoldást biztosít. A jelszóvisszaírás összetevő [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) , hogy engedélyezve van, és támogatás az aktuális előfizetők által használt [Azure Active Directory-kiadások](active-directory-editions.md).

A jelszóvisszaírás a következő szolgáltatásokat biztosítja:

* **Késleltetés visszajelzés nulla** -jelszóvisszaírás egy aszinkron művelet. A felhasználók arról azonnal a jelszavát nem felelt meg a házirend, vagy nem tudta alaphelyzetbe állítani, vagy megváltozott a bármilyen okból.
* **Támogatja az AD FS vagy más összevonási technológiákat használó felhasználók számára jelszavak alaphelyzetbe állítását** -jelszóvisszaírás, mindaddig, amíg az összevont felhasználói fiókok szinkronizálása az Azure AD-bérlő be tudja kezelni a helyszíni AD-jelszavaikat a felhőből.
* **Támogatja a felhasználók a jelszavak alaphelyzetbe állítását [Jelszókivonat-szinkronizálás](./connect/active-directory-aadconnectsync-implement-password-synchronization.md)**  – Ha a jelszó alaphelyzetbe állítása szolgáltatás észleli, hogy a szinkronizált felhasználói fiók engedélyezve van a Jelszókivonat-szinkronizálás, a rendszer visszaállítja a helyszíni mindkét a fiók és a jelszó egyidejűleg felhőalapú.
* **Támogatja a jelszavak módosítása a hozzáférési panel és az Office 365** – Ha összevont vagy a jelszó szinkronizálva felhasználók jelszavukat lejárt vagy nem lejárt, azt e jelszavakat írhasson vissza a helyi AD-környezet származnak.
* **Támogatja a biztonsági jelszavak írása, amikor egy rendszergazda az Azure-portálon állíthatja őket** – amikor egy rendszergazda visszaállítja a felhasználói jelszó a [Azure-portálon](https://portal.azure.com), ha a felhasználónak össze van vonva, vagy a jelszó szinkronizálva lesznek állítva a jelszót a helyi Active Directory, valamint a rendszergazda kijelölése Ez jelenleg nem támogatott az Office felügyeleti portálon.
* **Kikényszeríti a helyszíni AD jelszóházirendek** – Ha a felhasználó alaphelyzetbe állítja a jelszót, azt győződjön meg arról, hogy megfelel-e a helyszíni AD-házirend előtt véglegesítése a a könyvtárhoz. Ez magában foglalja az előzmények, összetettségét, kor, jelszószűrők és egyéb jelszó korlátozásokat a helyi AD-ben definiált.
* **Nincs szükség a bejövő tűzfalszabályokat** -jelszóvisszaíró az Azure Service Bus relay használja, mint a mögöttes kommunikációs csatornát, ami azt jelenti, hogy nem kell megnyitni a bejövő portra a tűzfalon ahhoz, hogy a funkció használatához.
* **Védett csoportok a helyszíni Active Directoryban található felhasználói fiókok esetében nem támogatott** – védett csoportokkal kapcsolatos további információért tekintse meg az [védett fiókok és csoportok Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>A jelszóvisszaírás működése

Amikor egy összevont vagy a jelszó kivonatát szinkronizált felhasználói elérhető lesz az új, vagy módosítania kell a jelszavát, a felhőben, az alábbiak történnek:

1. Azt ellenőrzi, milyen típusú jelszót a felhasználó rendelkezik-e.
    * Ha látható a jelszót a helyszínen felügyelt
        * Ellenőrizzük, hogy ha a visszaírási szolgáltatás működik-e és fut, ha, azt, hogy a felhasználó folytassa
        * A visszaírási szolgáltatás nem működik-e, ha azt a felhasználó értesítése, hogy a jelszavát most nem állítható alaphelyzetbe
2. Ezután a felhasználó a megfelelő hitelesítési kapui továbbítja, és eléri a jelszó alaphelyzetbe állítása képernyő.
3. A felhasználó kiválaszt egy új jelszót, és megerősíti, hogy azt.
4. Esetén a Küldés gombra kattintva a visszaírási telepítés során létrehozott szimmetrikus kulccsal azt titkosítani a titkosítatlan szöveges jelszó.
5. A jelszó-titkosítási, miután magában foglalja azt szerepel az adattartalomban, amely számára küldi el egy HTTPS-csatornán keresztül a bérlői-specifikus service bus-továbbító (beállított azt is, a visszaírási telepítés során). A továbbító védi, hogy csak a helyszíni telepítési ismeri véletlenszerűen létrehozott jelszót.
6. Ha az üzenet elérte a service bus, a jelszó alaphelyzetbe állítása végpont automatikusan felébred, és láthatja, hogy rendelkezik-e a visszaállítási kérelem függőben.
7. A szolgáltatás majd keresi a felhasználó az adott felhő horgonyattribútum használatával. A kereséshez sikeres legyen:

    * A user objektum már léteznie kell az AD-összekötő terület
    * A user objektum össze kell kapcsolni a megfelelő MV-objektum
    * A user objektum a megfelelő AAD connector objektum össze kell kapcsolni.
    * Az AD-összekötő objektum való hivatkozás a MV rendelkeznie kell a szinkronizálási szabály `Microsoft.InfromADUserAccountEnabled.xxx` hivatkozásra. <br> <br>
    Amikor a hívás a felhőből érkezik, a Szinkronizáló vezérlő a cloudAnchor attribútumot használja a AAD connector terület objektum, a hivatkozás a MV-objektum biztonsági követi kereséséhez, és majd követi a hivatkozás vissza az AD-objektum. Több Active Directory-objektumok (Többerdős) a felhasználónak kell, mert a szinkronizálási motor támaszkodik a `Microsoft.InfromADUserAccountEnabled.xxx` hivatkozás a megfelelő sablon kiválasztásához.

    > [!Note]
    > A logika miatt az Azure AD Connect képes kommunikálni a PDC-emulátor a jelszóvisszaírás működéséhez kell lennie. Ennek engedélyezéséhez manuálisan kell, ha csatlakozhat az Azure AD Connect a PDC-emulátor kattintson a jobb gombbal a a **tulajdonságok** jelölje be az Active Directory szinkronizálási összekötő **directory konfigurálása partíciók**. Ezután keresse meg a **tartományvezérlő kapcsolati beállításainak** szakaszát, és jelölje be a **csak az előnyben részesített tartományvezérlők használatának** jelölőnégyzetét. Akkor is, ha az elsődleges tartományvezérlő nem a PDC-emulátor, az Azure AD Connect megkísérli a jelszóvisszaírás PDC csatlakozni.

8. Ha a felhasználói fiók található, azt közvetlenül a megfelelő AD-erdőben a jelszó alaphelyzetbe állítása történt kísérlet.
9. Ha a jelszó-beállítási művelet sikeres, azt a felhasználó értesítése a jelszó megváltozott.
    > [!NOTE]
    > Abban az esetben, amikor a felhasználó jelszava szinkronizálja az Azure AD-jelszó-szinkronizálás segítségével esély van, hogy a helyszíni jelszóházirend gyengébb, mint a felhő jelszóházirend-e. Ebben az esetben azt még kényszerítése függetlenül a helyi házirend, és helyette a Jelszókivonat-szinkronizálást, a jelszó kivonatát a szinkronizálandó engedélyezése. Ez biztosítja, hogy a helyi házirend van érvényben a felhőben, attól függetlenül egyszeri bejelentkezést biztosítanak a jelszó-szinkronizálás és az összevonási használatakor.

10. A jelszó beállítása művelet sikertelen lesz, ha azt a hibaüzenetet a felhasználó számára, és hogy azok próbálja meg újból.
    * A művelet a következő miatt sikertelen lehet
        * A szolgáltatás nem működik
        * A jelszót állított nem felelt meg a szervezet házirendek
        * Nem található a felhasználó a helyi AD-ben

    Ezekben az esetekben számos rendelkezik egy adott üzenet azt, és a felhasználó értesítése, mit tehet a probléma megoldásához.

## <a name="configuring-password-writeback"></a>A jelszóvisszaírás konfigurálása

Azt javasoljuk, hogy használja-e az automatikus frissítési szolgáltatása [az Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) Ha jelszóvisszaírás használni kívánt.

A DirSync és az Azure AD Sync már nem támogatott azt jelenti, hogy a cikk a jelszóvisszaírás engedélyezése [frissíthet a DirSync és Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md) segítséget az átállás további részleteket tartalmaz.

Az alábbi lépések azt feltételezik, hogy a környezet használatával már konfigurálta az Azure AD Connect a [Express](./connect/active-directory-aadconnect-get-started-express.md) vagy [egyéni](./connect/active-directory-aadconnect-get-started-custom.md) beállításait.

1. Konfigurálni és jelszó visszaírási való bejelentkezéshez az Azure AD Connect-kiszolgáló engedélyezése és elindítani a **az Azure AD Connect** konfigurációs varázsló.
2. Az üdvözlőképernyőn kattintson **konfigurálása**.
3. További feladatok képernyőn kattintson a **testre szabhatja a szinkronizálási beállítások** majd **következő**.
4. A csatlakozás az Azure ad Szolgáltatáshoz képernyőn, adjon meg egy globális rendszergazdai hitelesítő adatok, és válassza a **következő**.
5. A csatlakozás a könyvtárak és a tartomány és a kérelemszűrés megvizsgálja, OU választhat **következő**.
6. A választható funkciókat felsoroló képernyőjén jelölje be a jelölőnégyzetet a **jelszóvisszaírás** kattintson **következő**.
   ![Az Azure AD Connectben a jelszóvisszaírás engedélyezése][Writeback]
7. A Ready to beállítása képernyő, kattintson a **konfigurálása** és várja meg a folyamat befejezéséhez.
8. Amikor megjelenik a konfigurációs befejeződik, kattintson a **Kilépés**

## <a name="active-directory-permissions"></a>Active Directory-engedélyek

Az Azure AD Connect segédprogram megadott fiók kell rendelkeznie jelszó alaphelyzetbe állítása, a jelszó módosítása, az írási engedélyek lockoutTime, és akár tartalomvédelmi pwdLastSet kiterjesztett írási engedéllyel a legfelső szintű objektumának **tartományonként** az adott erdőben **vagy** a felhasználó szervezeti egységek sspr hatókörében szánja.

Ha nem biztos abban, hogy milyen fiókot a fenti hivatkozik, nyissa meg az Azure Active Directory Connect konfigurációs felhasználói felület, és kattintson a nézet jelenlegi konfigurációs beállítást. A fiókot hozzá kell adnia az engedélyt megtalálható-e a "Címtárak szinkronizálása"

Ezek az engedélyek beállítása lehetővé teszi, hogy a MA-szolgáltatásfiókja kezelheti a jelszavakat az erdőben lévő felhasználói fiókok nevében minden egyes erdőhöz. **Ha nem rendeli hozzá ezeket az engedélyeket, majd, annak ellenére, hogy a visszaírás konfigurációja megfelelőnek, jelenik meg felhasználók hibákba megkísérlésekor. a helyszíni jelszavak kezelése a felhőből.**

> [!NOTE]
> Ez eltarthat egy óráig vagy tovább ezen engedélyeket a a címtár összes objektumába replikálja.
>

Megtörténik a jelszóvisszaírás megfelelő engedélyeinek beállítása

1. Nyissa meg a megfelelő tartományi rendszergazdai engedélyekkel rendelkező fiók Active Directory – felhasználók és számítógépek
2. A Nézet menü ellenőrizze, hogy a speciális funkciók be van kapcsolva
3. A bal oldali panelen kattintson a jobb gombbal a tartomány gyökeréhez képviselő objektum, és válassza a Tulajdonságok
    * A biztonság lapon
    * Majd a Speciális gombra.
4. Az engedélyek lapon kattintson a Hozzáadás gombra.
5. Válassza ki a fiókot, amely engedélyek alkalmazott (az Azure AD Connect telepítés)
6. Válassza a legördülő listája vonatkozik leszármazott felhasználó objektumai
7. Az engedélyek négyzeteket a következő
    * Unexpire jelszó
    * Jelszó alaphelyzetbe állítása
    * Jelszó módosítása
    * LockoutTime írása
    * PwdLastSet írása
8. Kattintson az alkalmaz/OK keresztül alkalmazza, és zárja be minden megnyitott párbeszédpanelen.

## <a name="licensing-requirements-for-password-writeback"></a>A jelszóvisszaírás licencelési követelményei

Licencelési, lásd: kapcsolatos információk [jelszóvisszaírás szükséges licencek](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) vagy a következő helyek

* [Az Azure Active Directory árképzési hely](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Biztonságos hatékony Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>Helyszíni hitelesítési módot támogat. a jelszó visszaírásához.

A jelszóvisszaírás a következő felhasználói jelszó esetében működik:

* **Csak felhőalapú felhasználók**: a jelszóvisszaírás nem vonatkozik ebben a helyzetben, mert nem a helyszíni jelszó
* **Jelszó-szinkronizált felhasználók**: a jelszóvisszaírás támogatott
* **Összevont felhasználók**: a jelszóvisszaírás támogatott
* **Áteresztő hitelesítés felhasználók**: a jelszóvisszaírás támogatott

### <a name="user-and-admin-operations-supported-for-password-writeback"></a>A jelszóvisszaíró támogatja felhasználói és felügyeleti műveletek

Jelszavak írt vissza a következő esetekben:

* **Végfelhasználói támogatott műveletek**
  * A végfelhasználói önkiszolgáló önkéntes jelszó művelet módosítása
  * A végfelhasználói önkiszolgáló kényszerített jelszó műveletet (például a jelszó lejárati idejét)
  * A végfelhasználói önkiszolgáló jelszó-átállítási származó a [jelszó-változtatási portál](https://passwordreset.microsoftonline.com)
* **Rendszergazda támogatott műveletek**
  * A rendszergazda önkiszolgáló önkéntes jelszó művelet módosítása
  * Minden rendszergazda kényszerített önkiszolgáló jelszó műveletet (például a jelszó lejárati idejét)
  * A rendszergazda az önkiszolgáló jelszó-átállítási származó a [jelszó-változtatási portál](https://passwordreset.microsoftonline.com)
  * Minden rendszergazda által kezdeményezett végfelhasználói jelszót alaphelyzetbe állítani a [a klasszikus Azure portálon](https://manage.windowsazure.com)
  * Minden rendszergazda által kezdeményezett végfelhasználói jelszót alaphelyzetbe állítani a [Azure-portálon](https://portal.azure.com)

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>Nem támogatott a jelszóvisszaírás felhasználói és felügyeleti műveletek

A jelszavak **nem** visszaírását a következő esetek bármelyikében:

* **Nem támogatott a végfelhasználói műveletek**
  * A végfelhasználók a saját PowerShell v1, v2 és az Azure AD Graph API jelszó alaphelyzetbe állításával
* **Nem támogatott rendszergazdai műveletek**
  * Minden rendszergazda által kezdeményezett végfelhasználói jelszót alaphelyzetbe állítani a [Office felügyeleti portálon](https://portal.office.com)
  * A rendszergazda által kezdeményezett végfelhasználói jelszó-változtatási PowerShell v1, v2 vagy az Azure AD Graph API

Dolgozunk ennek ezekkel a korlátozásokkal eltávolítani, amíg nincs egy meghatározott ütemterv még is osztjuk.

## <a name="password-writeback-security-model"></a>Jelszó visszaírási biztonsági modell

A jelszóvisszaírás egy olyan rendkívül biztonságos szolgáltatás.  Annak érdekében, hogy az adatok védelméről is gondoskodik, engedélyezzük a 4-rétegzett biztonsági modellt, amely az alábbiakban olvasható.

* **Bérlői-specifikus service-bus-továbbító**
  * A szolgáltatás beállítása, beállítjuk a bérlő-specifikus service bus-továbbító, amely Microsoft soha nem rendelkezik hozzáféréssel véletlenszerűen létrehozott erős jelszóval védett.
* **Zárolt, dokumentumtitkosítási erős jelszó-titkosítási kulcs**
  * A service bus relay létrehozása után azt használjuk a hálózaton keresztül ismét a jelszót titkosítására erős szimmetrikus kulcs létrehozása. Ez a kulcs él, csak a vállalat titkos tároló a felhőben, ami erősen zárolva, és naplózva, csakúgy, mint a jelszó a címtárban.
* **Iparági szabványos TLS**
  1. Ha a jelszó alaphelyzetbe állítása, vagy módosítsa a művelethez a felhőben, azt a titkosítatlan szöveges jelszó igénybe vehet, és a nyilvános kulcs titkosításához.
  2. A Microsoft helyezzen, amely egy HTTPS üzenet, amely használatával a Microsoft SSL-tanúsítványok a service bus-továbbító számára egy titkosított csatornán keresztül zajlik.
  3. Az üzenet megérkezése le a Service Busba, után a helyszíni ügynök felébred, és a Service Bus korábban létrehozott erős jelszóval hitelesíti.
  4. A helyszíni ügynök szerzi be a titkosított üzeneteket, visszafejti a Microsoft hozza létre a titkos kulcsot használja.
  5. A helyszíni ügynök megpróbálja állítsa be a jelszót az AD DS SetPassword API-n keresztül.
     * Ez a lépés nem mi lehetővé teszi az AD a helyi jelszóházirend (összetettségét, kor, előzmények, szűrők, stb.) kényszerítése a felhőben.
* **Üzenet lejárati házirendek** 
  * Ha az üzenet a Service Bus helyezkedik el, mert a helyszíni szolgáltatás le van állítva, akkor lejár, és néhány percig még tovább a biztonság növelése után el kell távolítani.

### <a name="password-writeback-encryption-details"></a>Jelszó visszaírási titkosítási részletek

A titkosítás lépései a jelszó-visszaállítási folyamatot végighalad az elküldés után, mielőtt a helyszíni környezetben maximális szolgáltatás megbízhatóságának biztosítása megérkeznek, és biztonsági folyamata az alábbiakban olvasható.

* **1. lépés – jelszó-titkosítási 2048 bites RSA-kulcsával** – Miután a felhasználó elküld egy 2048 bites RSA-kulcs visszaírását a helyszíni, először a beküldött jelszó maga jelszóval van titkosítva.
* **2. lépés - csomag szintű titkosítást AES-GCM-mel** - és a teljes csomag (jelszó + szükséges metaadatok) titkosított AES-GCM szolgáltatással. A titkosítás megakadályozza, hogy bárki, aki közvetlen hozzáféréssel az alapul szolgáló Szolgáltatásbusz-csatornához megjelenítése/illetéktelenül tartalmát.
* **Lépés a 3 - TLS keresztül történik az összes kommunikáció / SSL** -Szolgáltatásbusz a kommunikációt egy SSL/TLS-csatorna történik. Titkosítási a tartalom jogosulatlan harmadik felek biztonságossá tételére.
* **Automatikus kulcsváltást hathavonta** – minden hatodik hónapban, vagy minden alkalommal, amikor a jelszóvisszaírás van letiltva / újra engedélyezik az Azure AD Connect azt automatikusan minden helyettesítő kulcsok maximális szolgáltatás biztonsági és biztonságának.

### <a name="password-writeback-bandwidth-usage"></a>Jelszó visszaírási sávszélesség-használat

A jelszóvisszaírás egy kis sávszélességű szolgáltatás, amely kérelmeket küld vissza a helyszíni ügynök csak az alábbi körülmények:

1. Két üzeneteket küldhet, ha engedélyezése vagy letiltása a szolgáltatás az Azure AD Connect használatával.
2. Mindaddig, amíg a szolgáltatás fut egy üzenetet küld 5 percenként egyszer, a szolgáltatás szívverést.
3. Két üzenetek küldése történik, minden alkalommal új jelszó küldése
    * A művelet végrehajtásához a kérelem első üzenet
    * Második ilyen üzenet, amely tartalmazza a művelet eredményét, és a következő körülmények között küldött:
        * Minden alkalommal, amikor a felhasználó önkiszolgáló jelszó alaphelyzetbe állítása során egy új jelszó küldése.
        * Minden alkalommal, amikor egy új jelszó küldése a felhasználói jelszó-változtatási művelet során.
        * Minden alkalommal, amikor egy új jelszó küldése során egy rendszergazda által kezdeményezett felhasználói jelszó alaphelyzetbe (az Azure felügyeleti portálon csak).

#### <a name="message-size-and-bandwidth-considerations"></a>Üzenet mérete és a sávszélesség kapcsolatos szempontok

Minden, a fenti üzenet mérete általában a 1 kb szélsőséges terhelés alatt is, a jelszó visszaírási szolgáltatást nem használ-e a sávszélesség néhány kilobit / másodperc. Minden üzenetet a rendszer a valós idejű küldi el, mert csak akkor, ha a jelszó-frissítési művelet által igényelt, és az üzenet mérete ezért kicsi, mert a sávszélesség-használat a visszaírási funkció tulajdonképpen túl kicsi a valódi mérhető hatással van.

## <a name="next-steps"></a>Következő lépések

* [Hogyan végezze el a sikeres bevezetéshez az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-best-practices.md)
* [A jelszó megváltoztatására](active-directory-passwords-update-your-own-password.md).
* [Az önkiszolgáló jelszó-változtatási regisztrációs](active-directory-passwords-reset-register.md).
* [Licencelés kérdése van?](active-directory-passwords-licensing.md)
* [Milyen adatok SSPR használja, és milyen adatokat kell tölteni a felhasználók számára?](active-directory-passwords-data.md)
* [Hitelesítési módszerek állnak rendelkezésre a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Mik a házirend-beállításokban az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-policy.md)
* [Hogyan jelentést az önkiszolgáló jelszó-Változtatási tevékenység?](active-directory-passwords-reporting.md)
* [Mik az önkiszolgáló jelszó-Változtatási közül az összes, és mit azokat a következőket:?](active-directory-passwords-how-it-works.md)
* [Szerintem valami nem működik. Hogyan hibáinak elhárítása az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-troubleshoot.md)
* [A rendszer nem jelzett valahol máshol kérdést kell](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Az Azure AD Connectben a jelszóvisszaírás engedélyezése"
