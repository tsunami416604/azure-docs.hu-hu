---
title: Helyszíni jelszó-visszaírás önkiszolgáló jelszó-visszaállítással – Azure Active Directory
description: Megtudhatja, hogy az Azure Active Directoryban hogyan lehet jelszómódosítási vagy -visszaállítási eseményeket visszaírni egy helyszíni címtárkörnyezetbe
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 787c15c11c995c7eb30662131302658175c7f877
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393017"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Hogyan működik az önkiszolgáló jelszó-visszaállítási visszaírás az Azure Active Directoryban?

Az Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználók számára, hogy alaphelyzetbe állítják jelszavukat a felhőben, de a legtöbb vállalat rendelkezik egy helyszíni Active Directory tartományi szolgáltatások (AD DS) környezettel is, ahol a felhasználók léteznek. A jelszó-visszaírás az [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) szolgáltatással engedélyezett szolgáltatás, amely lehetővé teszi a felhőbeli jelszómódosítások valós idejű visszaírását egy meglévő helyszíni címtárba. Ebben a konfigurációban, amikor a felhasználók módosítják vagy alaphelyzetbe állítják jelszavukat az SSPR használatával a felhőben, a frissített jelszavakat is visszaírt a helyszíni AD DS környezetbe

A jelszóvisszaírás a következő hibrid identitásmodelleket használó környezetekben támogatott:

* [Jelszókivonat szinkronizálása](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Átmenő hitelesítés](../hybrid/how-to-connect-pta.md)
* [Active Directory összevonási szolgáltatások](../hybrid/how-to-connect-fed-management.md)

A jelszóvisszaírás a következő szolgáltatásokat nyújtja:

* **Helyszíni Active Directory tartományi szolgáltatások (AD DS) jelszóházirendek kényszerítése**: Amikor egy felhasználó alaphelyzetbe állítja a jelszavát, ellenőrzi, hogy megfelel-e a helyszíni Active Directory tartományi szolgáltatások házirendjének, mielőtt véglegesíti azt a címtárban. Ez az ellenőrzés magában foglalja az előzmények, az összetettség, az életkor, a jelszószűrők és az AD DS-ben megadott egyéb jelszókorlátozások ellenőrzését.
* **Nulla késleltetésű visszajelzés:** A jelszóvisszaírás szinkron művelet. A felhasználók azonnal értesítést kapnak, ha jelszavuk nem felel meg a házirendnek, vagy bármilyen okból nem állíthatók alaphelyzetbe vagy módosíthatók.
* **Támogatja a jelszómódosításokat a hozzáférési panelről és az Office 365-ből:** Ha összevont vagy jelszókivonatos szinkronizált felhasználók módosítják lejárt vagy nem lejárt jelszavukat, a rendszer visszaírja ezeket a jelszavakat az Active Árkok ds-be.
* **Támogatja a jelszó-visszaírás, amikor egy rendszergazda alaphelyzetbe állítja őket az Azure Portalon:** Ha egy rendszergazda alaphelyzetbe állítja a felhasználó jelszavát az [Azure Portalon,](https://portal.azure.com)ha a felhasználó összevont vagy jelszókivonat szinkronizálva, a jelszó visszaírása a helyszíni. Ez a funkció jelenleg nem támogatott az Office felügyeleti portálon.
* **Nem igényel bejövő tűzfalszabályokat:** A jelszó-visszaírás az Azure Service Bus-továbbítót használja alapul szolgáló kommunikációs csatornaként. Minden kommunikáció a 443-as porton keresztül történik.

> [!NOTE]
> A helyszíni AD-ben a védett csoportokon belül létező rendszergazdai fiókok jelszó-visszaírással használhatók. A rendszergazdák módosíthatják jelszavukat a felhőben, de a jelszó-visszaállítással nem állíthatják alaphelyzetbe az elfelejtett jelszót. A védett csoportokról további információt a Védett fiókok és csoportok az Active Directoryban című [témakörben talál.](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory)

## <a name="how-password-writeback-works"></a>A jelszóvisszaíró működése

Amikor egy összevont vagy jelszókivonatos szinkronizált felhasználó megpróbálja alaphelyzetbe állítani vagy módosítani a jelszavát a felhőben, a következő műveletek történnek:

1. A rendszer ellenőrzi, hogy milyen típusú jelszóval rendelkezik a felhasználó. Ha a jelszó a helyszínen van kezelve:
   * A visszaírási szolgáltatás működésbe hozása ellenőriz. Ha ez így van, a felhasználó folytathatja.
   * Ha a visszaírási szolgáltatás nem működik, a felhasználó értesítést kap arról, hogy a jelszó nem állítható alaphelyzetbe most.
1. Ezután a felhasználó átadja a megfelelő hitelesítési kapukat, és eléri a **Jelszó alaphelyzetbe állítása** lapot.
1. A felhasználó kiválaszt egy új jelszót, és megerősíti azt.
1. Amikor a felhasználó a **Küldés**lehetőséget választja, az egyszerű szöveges jelszó a visszaírási beállítási folyamat során létrehozott szimmetrikus kulccsal lesz titkosítva.
1. A titkosított jelszó szerepel a hasznos adat, amely egy HTTPS-csatornán keresztül küldi el a bérlő-specifikus szolgáltatásbusz-továbbító (amely be van állítva az Ön számára a visszaírási telepítési folyamat során). Ezt a továbbítót egy véletlenszerűen generált jelszó védi, amelyet csak a helyszíni telepítés tud.
1. Miután az üzenet elérte a szolgáltatásbuszt, a jelszó-visszaállítási végpont automatikusan felébred, és látja, hogy egy alaphelyzetbe állítási kérelem függőben van.
1. A szolgáltatás ezután megkeresi a felhasználót a felhőhorgony attribútum használatával. Ahhoz, hogy ez a kérdés sikeres legyen, a következő feltételeknek kell teljesülniük:

   * A felhasználói objektumnak léteznie kell az Active Directory összekötő térben.
   * A felhasználói objektumot a megfelelő metaverzum (MV) objektumhoz kell kapcsolni.
   * A felhasználói objektumot csatolni kell a megfelelő Azure Active Directory-összekötő objektumhoz.
   * Az Active Directory-összekötő objektumból az MV-hez való `Microsoft.InfromADUserAccountEnabled.xxx` kapcsolódásnak rendelkeznie kell a kapcsolaton a szinkronizálási szabállyal.

   Amikor a hívás érkezik a felhőből, a szinkronizálási motor a **cloudAnchor** attribútum ot használja az Azure Active Directory-összekötő űrobjektum. Ezután követi a hivatkozást vissza az MV-objektum, majd követi a kapcsolatot vissza az Active Directory objektumot. Mivel ugyanahhoz a felhasználóhoz több Active Directory-objektum (több erdő) `Microsoft.InfromADUserAccountEnabled.xxx` is létezhet, a szinkronizálási motor a hivatkozásra támaszkodva a megfelelőt választja ki.

1. Miután megtalálta a felhasználói fiókot, a rendszer megkísérli a jelszó visszaállítását közvetlenül a megfelelő Active Directory erdőben.
1. Ha a jelszókészlet sikeres, a felhasználó nak azt a felhasználó nak kell mondania, hogy a jelszó megváltozott.

   > [!NOTE]
   > Ha a felhasználó jelszókivonata jelszókivonat-szinkronizálással szinkronizálódik az Azure AD-vel, akkor fennáll annak az esélye, hogy a helyszíni jelszóházirend gyengébb, mint a felhőjelszó-szabályzat. Ebben az esetben a helyszíni házirend kényszerítve. Ez a szabályzat biztosítja, hogy a helyszíni szabályzat a felhőben, függetlenül attól, hogy jelszókivonat-szinkronizálás vagy összevonás i használatával egyszeri bejelentkezés.

1. Ha a jelszókészlet műveletsikertelen, egy hiba miatt a felhasználó újra próbálkozik. A művelet a következő okok miatt sikertelen lehet:
    * A szolgáltatás nem volt.
    * A kiválasztott jelszó nem felel meg a szervezet irányelveinek.
    * Nem található a felhasználó a helyi Active Directoryban.

   A hibaüzenetek útmutatást nyújtanak a felhasználóknak, hogy rendszergazdai beavatkozás nélkül kísérelhessék meg a probléma megoldását.

## <a name="password-writeback-security"></a>Jelszóvisszaírás biztonsága

A jelszóvisszaírás rendkívül biztonságos szolgáltatás. Az adatok védelme érdekében a négyszintű biztonsági modell az alábbiak szerint engedélyezve van:

* **Bérlő-specifikus szolgáltatás-busz relé**
   * A szolgáltatás beállításakor egy bérlő-specifikus szolgáltatásbusz-továbbító van beállítva, amely et egy véletlenszerűen generált erős jelszó véd, amelyhez a Microsoft soha nem fér hozzá.
* **Zárolva, kriptográfiailag erős, jelszótitkosítási kulcs**
   * A szolgáltatásbusz-továbbító létrehozása után létrejön egy erős szimmetrikus kulcs, amely a jelszó titkosítására szolgál, ahogy az a vezetéken keresztül érkezik. Ez a kulcs csak a vállalat titkos tárolójában él a felhőben, amely erősen zárolva van és naplózva van, csakúgy, mint bármely más jelszó a címtárban.
* **Iparági szabvány szállítási réteg biztonsága (TLS)**
   1. Amikor jelszó-visszaállítási vagy -módosítási művelet történik a felhőben, az egyszerű szöveges jelszó titkosítva lesz a nyilvános kulccsal.
   1. A titkosított jelszó egy HTTPS-üzenetbe kerül, amelyet egy titkosított csatornán keresztül küld a service bus relay microsoft TLS/SSL tanúsítványai segítségével.
   1. Miután az üzenet megérkezik a szolgáltatásbuszba, a helyszíni ügynök felébred, és hitelesíti magát a szolgáltatásbuszon a korábban létrehozott erős jelszó használatával.
   1. A helyszíni ügynök felveszi a titkosított üzenetet, és visszafejti azt a személyes kulcs használatával.
   1. A helyszíni ügynök megpróbálja beállítani a jelszót az AD DS SetPassword API-n keresztül. Ez a lépés teszi lehetővé az Active Directory helyszíni jelszóházirendjének (például a felhő összetettségének, korának, előzményeinek és szűrőinek) a kényszerítését.
* **Üzenet elévülési irányelvei**
   * Ha az üzenet a kiszolgálóbuszban található, mert a helyszíni szolgáltatás nem működik, az időtúlmúlik, és néhány perc múlva törlődik. Az üzenet idő- és eltávolítása tovább növeli a biztonságot.

### <a name="password-writeback-encryption-details"></a>Jelszóvisszaírás titkosítási adatai

Miután a felhasználó beküldi a jelszó-visszaállítást, az alaphelyzetbe állítási kérelem több titkosítási lépésen megy keresztül, mielőtt megérkezik a helyszíni környezetbe. Ezek a titkosítási lépések maximális megbízhatóságot és biztonságot biztosítanak a szolgáltatás ban. Ezek leírása a következő:

1. **Jelszótitkosítás 2048 bites RSA-kulccsal:** Miután a felhasználó beküldte a szükséges jelszót a helyszíni rendszerbe, maga a beküldött jelszó egy 2048 bites RSA-kulccsal van titkosítva.
1. **Csomagszintű titkosítás AES-GCM-mel:** A teljes csomag, a jelszó és a szükséges metaadatok az AES-GCM használatával titkosítva vannak. Ez a titkosítás megakadályozza, hogy bárki, aki közvetlen hozzáféréssel rendelkezik az alapul szolgáló ServiceBus csatornához, megtekintse vagy meghamisítsa a tartalmat.
1. **Minden kommunikáció TLS/SSL-en keresztül történik:** A ServiceBus-szal való minden kommunikáció SSL/TLS-csatornán történik. Ez a titkosítás védi a tartalmat a jogosulatlan harmadik felek.
1. **Automatikus kulcsváltás hathavonta:** Minden kulcs hathavonta átvált, vagy minden alkalommal, amikor a jelszó-visszaírás le van tiltva, majd újra engedélyezve van az Azure AD Connecten, a maximális szolgáltatásbiztonság és -biztonság érdekében.

### <a name="password-writeback-bandwidth-usage"></a>Jelszóvisszaírás sávszélesség-használata

A jelszóvisszaírás egy kis sávszélességű szolgáltatás, amely csak a következő körülmények között küldi vissza a kérelmeket a helyszíni ügynöknek:

* Két üzenetet küld, ha a funkció engedélyezve van, vagy le van tiltva az Azure AD Connect.
* Öt percenként egy üzenet érkezik szolgáltatásszívverésként, amíg a szolgáltatás fut.
* Minden alkalommal két üzenet kerül elküldésre, amikor új jelszót küld be:
   * Az első üzenet a művelet végrehajtására vonatkozó kérés.
   * A második üzenet a művelet eredményét tartalmazza, és a következő körülmények között kerül elküldésre:
      * Minden alkalommal, amikor egy felhasználó önkiszolgáló jelszó-visszaállítás során új jelszót küld be.
      * Minden alkalommal, amikor egy felhasználói jelszómódosítási művelet során új jelszót küld el.
      * Minden alkalommal, amikor egy admin által kezdeményezett felhasználói jelszó-visszaállítás során új jelszót küld el (csak az Azure felügyeleti portálokon).

#### <a name="message-size-and-bandwidth-considerations"></a>Az üzenetek méretével és sávszélességével kapcsolatos szempontok

A korábban leírt üzenetek mérete általában 1 KB alatt van. Még extrém terhelések esetén is a jelszóvisszaírási szolgáltatás másodpercenként néhány kilobitot fogyaszt. Mivel minden üzenet küldése valós időben történik, csak akkor, ha egy jelszófrissítési művelet megköveteli, és mivel az üzenet mérete olyan kicsi, a visszaírási képesség sávszélesség-használata túl kicsi ahhoz, hogy mérhető hatást fejtsen ki.

## <a name="supported-writeback-operations"></a>Támogatott visszaírási műveletek

A jelszavak at a következő helyzetekben írják vissza:

* **Támogatott végfelhasználói műveletek**
   * Bármely végfelhasználói önkiszolgáló önkéntes jelszómódosítási művelet.
   * A végfelhasználói önkiszolgáló kényszerítési kényszerítés módosítja a jelszóműveletet, például a jelszó lejárata.
   * A [jelszó-visszaállítási portálról](https://passwordreset.microsoftonline.com)származó végfelhasználói önkiszolgáló jelszó-visszaállítási beállítások visszaállítása.

* **Támogatott rendszergazdai műveletek**
   * Minden rendszergazda önkiszolgáló önkéntes változás jelszó művelet.
   * A rendszergazda imitátora módosítja a jelszóműveletet, például a jelszó lejárata.
   * A [jelszó-visszaállítási portálról](https://passwordreset.microsoftonline.com)származó rendszergazdai önkiszolgáló jelszó-visszaállítási beállítások visszaállítása .
   * A rendszergazda által kezdeményezett végfelhasználói jelszó alaphelyzetbe állítása az [Azure](https://portal.azure.com)Portalról.

## <a name="unsupported-writeback-operations"></a>Nem támogatott visszaírási műveletek

A jelszavakat a rendszer nem írja vissza az alábbi helyzetekben:

* **Nem támogatott végfelhasználói műveletek**
   * Bármely végfelhasználó alaphelyzetbe saját jelszavát a PowerShell 1-es, 2-es vagy a Microsoft Graph API használatával.
* **Nem támogatott rendszergazdai műveletek**
   * A rendszergazda által kezdeményezett végfelhasználói jelszó alaphelyzetbe állítása a PowerShell 1-es, 2-es vagy Microsoft Graph API-jából.
   * A rendszergazda által kezdeményezett végfelhasználói jelszó-visszaállítás a [Microsoft 365 felügyeleti központból.](https://admin.microsoft.com)

> [!WARNING]
> Használja a "Felhasználónak meg kell változtatnia a jelszót a következő bejelentkezéskor" a helyszíni Active Directory tartományi szolgáltatások felügyeleti eszközeiben, például az Active Directory – felhasználók és számítógépek vagy az Active Directory felügyeleti központ az Azure AD Connect előzetes verzióbeli szolgáltatásaként. További információ: [Jelszókivonat-szinkronizálás megvalósítása az Azure AD Connect-szinkronizálással.](../hybrid/how-to-connect-password-hash-synchronization.md)

## <a name="next-steps"></a>További lépések

Az SSPR-visszaírás első lépéseivel hajtsa végre a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: Önkiszolgáló jelszó-visszaállítás (SSPR) visszaírásának engedélyezése](tutorial-enable-writeback.md)
