---
title: Helyszíni jelszó visszaírási-integráció az Azure AD SSPR-Azure Active Directory
description: A helyszíni AD-infrastruktúrába írt Felhőbeli jelszavak beolvasása
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa66299753ab11dcad280361cb5fb6f0c31ef242
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263919"
---
# <a name="what-is-password-writeback"></a>Mi a jelszó visszaírási?

A felhőalapú jelszó-visszaállítási segédprogram nagyszerű, de a legtöbb vállalat továbbra is rendelkezik egy helyszíni címtárral, ahol a felhasználók léteznek. Hogyan támogatja a Microsoft a hagyományos helyszíni Active Directory (AD) szolgáltatást a Felhőbeli jelszó-módosításokkal való szinkronizálásban? A Password visszaírási egy olyan szolgáltatás, amely [Azure ad Connect](../hybrid/whatis-hybrid-identity.md) lehetővé teszi, hogy a jelszó módosításait a felhőben valós időben lehessen visszaírni egy meglévő helyszíni címtárba.

A jelszó-visszaírási a következőket használó környezetekben támogatott:

* [Active Directory összevonási szolgáltatások](../hybrid/how-to-connect-fed-management.md)
* [Jelszókivonat szinkronizálása](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Átmenő hitelesítés](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> A jelszó-visszaírási nem fog működni azon ügyfelek esetében, akik az [Azure Access Control szolgáltatás (ACS) 2018](../azuread-dev/active-directory-acs-migration.md)-as 1.0.8641.0-verziójának kivonása után a Azure ad Connect verziót használják. Azure AD Connect a 1.0.8641.0 és régebbi verziók többé nem engedélyezik a jelszavak visszaírási, mert az adott funkcióhoz tartozó ACS-től függenek.
>
> A szolgáltatás megszakadásának elkerülése érdekében frissítse a Azure AD Connect egy korábbi verziójáról egy újabb verzióra, és tekintse meg a [Azure ad Connect: frissítés korábbi verzióról a legújabbra](../hybrid/how-to-upgrade-previous-version.md) című cikket.
>

A jelszó visszaírási a következőket biztosítja:

* Helyszíni **Active Directory jelszóházirend kényszerítése**: amikor egy felhasználó visszaállítja a jelszavát, a rendszer ellenőrzi, hogy az megfelel-e a helyszíni Active Directory szabályzatnak, mielőtt véglegesíti azt a címtárba. Ez a felülvizsgálat magában foglalja az előzmények, a bonyolultság, az életkor, a jelszó-szűrők és a helyi Active Directoryban definiált egyéb jelszavak korlátozásának ellenőrzését.
* **Nulla késleltetésű visszajelzés**: a jelszó visszaírási egy szinkron művelet. A felhasználók azonnal értesítést kapnak, ha a jelszavuk nem felelt meg a szabályzatnak, vagy bármilyen okból nem sikerült visszaállítani vagy módosítani.
* **Támogatja a jelszó módosításait a hozzáférési panelen és az Office 365**-ben: Ha az összevont vagy a jelszó-kivonatolással szinkronizált felhasználók megváltoztatják a lejárt vagy nem lejárt jelszavukat, a jelszavak visszakerülnek a helyi Active Directory környezetbe.
* **Támogatja a jelszó visszaírási, amikor egy rendszergazda visszaállítja azokat a Azure Portal**: Ha a rendszergazda visszaállítja a felhasználó jelszavát a [Azure Portalban](https://portal.azure.com), ha a felhasználó összevont vagy jelszó-kivonattal szinkronizálva van, a rendszer visszaírja a jelszót a helyszíni környezetbe. Ez a funkció jelenleg nem támogatott az Office felügyeleti portálon.
* **Nincs szükség bejövő tűzfalszabályok**használatára: a Password visszaírási egy Azure Service Bus továbbítót használ mögöttes kommunikációs csatornaként. Minden kommunikáció a 443-es porton keresztül kimenő.

> [!NOTE]
> A helyszíni AD-ben található védett csoportokba tartozó rendszergazdai fiókok jelszavas visszaírási is használhatók. A rendszergazdák megváltoztathatják a jelszavukat a felhőben, de nem használhatják a jelszó-visszaállítást az elfelejtett jelszavak alaphelyzetbe állításához. A védett csoportokkal kapcsolatos további információkért lásd: [védett fiókok és csoportok a Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="licensing-requirements-for-password-writeback"></a>A jelszó-visszaírási licencelési követelményei

Az **önkiszolgáló jelszó-visszaállítás, illetve a helyszíni visszaírási való módosítás/feloldás az Azure ad prémium funkciója**. A licenceléssel kapcsolatos további információkért tekintse meg a [Azure Active Directory díjszabási webhelyét](https://azure.microsoft.com/pricing/details/active-directory/).

A jelszó-visszaírási használatához a bérlőhöz hozzárendelt következő licencek egyike szükséges:

* Prémium szintű Azure AD P1
* Prémium szintű Azure AD P2
* E3 vagy a3 Enterprise Mobility + Security
* Enterprise Mobility + Security E5 vagy a5
* E3 vagy a3 Microsoft 365
* Microsoft 365 E5 vagy a5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Az önálló Office 365 licencelési csomagok *nem támogatják az "önkiszolgáló jelszó-visszaállítás/módosítás/zárolás a helyszíni visszaírási"* lehetőséget, és a funkció működéséhez a fenti csomagok egyikét kell megkövetelni.

## <a name="how-password-writeback-works"></a>A jelszóvisszaíró működése

Ha egy összevont vagy jelszó-kivonat szinkronizálta a felhasználót a felhőben tárolt jelszavának visszaállítására vagy módosítására, a következő műveletek történnek:

1. Ellenőrizze, hogy a felhasználó milyen típusú jelszót tartalmaz. Ha a jelszót a helyszínen felügyelik:
   * A rendszer ellenőrzéssel látja el, hogy a visszaírási szolgáltatás működik-e. Ha igen, akkor a felhasználó folytathatja.
   * Ha a visszaírási szolgáltatás nem működik, a rendszer értesíti a felhasználót, hogy a jelszavuk most nem állítható alaphelyzetbe.
1. Ezután a felhasználó átadja a megfelelő hitelesítési kapukat, és eléri a **jelszó alaphelyzetbe állítása** lapot.
1. A felhasználó új jelszót választ, és megerősíti.
1. Ha a felhasználó a **Submit (Küldés**) lehetőséget választja, a rendszer titkosítja az egyszerű szöveges jelszót a visszaírási telepítési folyamata során létrehozott szimmetrikus kulccsal.
1. A titkosított jelszót egy olyan hasznos adat tartalmazza, amely egy HTTPS-csatornán keresztül küldi el a bérlő-specifikus Service Bus-továbbítót (amely a visszaírási telepítési folyamata során van beállítva). Ezt a továbbítót véletlenszerűen generált jelszó védi, amelyet csak a helyszíni telepítés tud.
1. Ha az üzenet eléri a Service Bus-t, a jelszó-visszaállítási végpont automatikusan felébred, és úgy látja, hogy függőben van egy visszaállítási kérelem.
1. A szolgáltatás ezután a Cloud Anchor attribútum használatával keresi a felhasználót. A keresés sikeres:

   * A felhasználói objektumnak léteznie kell a Active Directory-összekötő területén.
   * A felhasználói objektumot a megfelelő Metaverse (MV) objektumhoz kell kapcsolni.
   * A felhasználói objektumot össze kell kapcsolni a megfelelő Azure Active Directory összekötő objektummal.
   * Az Active Directory-összekötő objektum és az MV közötti hivatkozásnak a szinkronizálási szabálynak `Microsoft.InfromADUserAccountEnabled.xxx` kell lennie a hivatkozáson.
   
   Ha a hívás a felhőből érkezik, a Szinkronizáló motor a **cloudAnchor** attribútum használatával keresi meg az Azure Active Directory-összekötő terület objektumát. Ezután a hivatkozás visszakerül az MV objektumra, majd a hivatkozás visszakerül a Active Directory objektumra. Mivel több Active Directory objektum (több erdő) is lehet ugyanahhoz a felhasználóhoz, a szinkronizálási motor a `Microsoft.InfromADUserAccountEnabled.xxx` hivatkozásra támaszkodik, hogy kiválassza a megfelelőt.

1. A felhasználói fiók megtalálása után a rendszer megkísérli a jelszó alaphelyzetbe állítását közvetlenül a megfelelő Active Directory erdőben.
1. Ha a jelszó-megadási művelet sikeres, a felhasználó azt mondja, hogy a jelszava módosult.
   > [!NOTE]
   > Ha a felhasználó jelszavas kivonata szinkronizálva van az Azure AD-val a jelszó-kivonatolási szinkronizálás használatával, fennáll a valószínűsége annak, hogy a helyszíni jelszóházirend gyengébb, mint a felhő jelszavas házirendje. Ebben az esetben a helyszíni házirend érvénybe lép. Ez a szabályzat biztosítja, hogy a helyszíni házirend érvénybe lépjen a felhőben, függetlenül attól, hogy az egyszeri bejelentkezéshez jelszó-kivonatolási szinkronizálást vagy összevonást használ.

1. Ha a jelszó-megadási művelet meghiúsul, a rendszer hibaüzenetet kér a felhasználótól, hogy próbálkozzon újra. A művelet sikertelen lehet, mert:
    * A szolgáltatás leállt.
    * A kiválasztott jelszó nem felelt meg a szervezet házirendjeinek.
    * Nem található a felhasználó a helyi Active Directoryban.

      A hibaüzenetek útmutatást nyújtanak a felhasználóknak, hogy rendszergazdai beavatkozás nélkül is fel tudják oldani a megoldást.

## <a name="password-writeback-security"></a>Jelszó visszaírási biztonsága

A jelszó visszaírási egy nagyon biztonságos szolgáltatás. Az adatok védelmének biztosítása érdekében a következőkben ismertetett módon egy négy rétegű biztonsági modell van engedélyezve:

* **Bérlő-specifikus Service-Bus Relay**
   * A szolgáltatás beállításakor a bérlőre jellemző Service Bus Relay olyan véletlenszerűen generált erős jelszóval védett, amelyet a Microsoft soha nem fér hozzá.
* **Zárolt, titkosítási szempontból erős, jelszó-titkosítási kulcs**
   * A Service Bus-továbbító létrehozása után egy erős szimmetrikus kulcs jön létre, amely a jelszó titkosítására szolgál a huzalon keresztül. Ez a kulcs csak a vállalat titkos tárolójában él a felhőben, amely szigorúan le van zárva és naplózva van, ugyanúgy, mint a címtárban lévő többi jelszó.
* **Iparági szabvány Transport Layer Security (TLS)**
   1. Ha a felhőben új jelszó-visszaállítási vagy módosítási művelet történik, a rendszer a nyilvános kulccsal titkosítja az egyszerű szöveges jelszót.
   1. A titkosított jelszó egy titkosított csatornán küldött HTTPS-üzenetbe kerül, amelyet a Microsoft SSL-tanúsítványokkal küld a Service Bus-továbbítónak.
   1. Miután az üzenet megérkezik a Service Bus szolgáltatásba, a helyszíni ügynök felébred, és hitelesíti a szolgáltatást a korábban létrehozott erős jelszó használatával.
   1. A helyszíni ügynök felveszi a titkosított üzenetet, és visszafejti azt a titkos kulcs használatával.
   1. A helyszíni ügynök megpróbálja beállítani a jelszót a AD DS SetPassword API-n keresztül. Ez a lépés lehetővé teszi az Active Directory helyszíni jelszóházirend (például a bonyolultság, az életkor, az előzmények és a szűrők) kényszerítését a felhőben.
* **Üzenet elévülési szabályzatai**
   * Ha az üzenet a Service Bus szolgáltatásban található, mert a helyszíni szolgáltatás nem érhető el, időtúllépést okoz, és néhány perc múlva el lesz távolítva. Az üzenet időtúllépése és eltávolítása még tovább növeli a biztonságot.

### <a name="password-writeback-encryption-details"></a>Jelszó visszaírási-titkosítás részletei

Miután egy felhasználó elküldte a jelszó alaphelyzetbe állítását, az alaphelyzetbe állítási kérelem több titkosítási lépésen megy keresztül, mielőtt megérkezik a helyszíni környezetbe. Ezek a titkosítási lépések biztosítják a szolgáltatás megbízhatóságának és biztonságának maximális biztonságát. Ezek a következőképpen vannak leírva:

* **1. lépés: jelszó-titkosítás 2048 bites RSA-kulccsal**: miután egy felhasználó jelszót küldött vissza a helyszíni környezetbe, a beküldött jelszó egy 2048 bites RSA-kulccsal van titkosítva.
* **2. lépés: csomag szintű TITKOSÍTÁS AES-GCM**: a teljes csomag, a jelszó és a szükséges metaadatok az AES-GCM használatával titkosítva vannak. Ez a titkosítás megakadályozza, hogy bárki közvetlenül hozzáférhessen a mögöttes ServiceBus-csatornához a tartalom megtekintésével vagy módosításával.
* **3. lépés: minden kommunikáció a TLS/SSL protokollon keresztül**történik: az ServiceBus-mel folytatott kommunikáció egy SSL/TLS-csatornán történik. Ez a titkosítás védi a jogosulatlan harmadik féltől származó tartalmat.
* **Automatikus kulcs átadása hat havonta**: minden, hat hónapon át elvégezhető kulcs, vagy ha a jelszó visszaírási le van tiltva, majd újból engedélyezve Azure ad Connecton, a maximális biztonság és biztonság érdekében.

### <a name="password-writeback-bandwidth-usage"></a>Jelszó-visszaírási sávszélesség-használat

A jelszó visszaírási egy alacsony sávszélességű szolgáltatás, amely csak a következő esetekben küld vissza kéréseket a helyszíni ügynöknek:

* A rendszer két üzenetet továbbít, ha a funkció engedélyezve van vagy le van tiltva Azure AD Connecton keresztül.
* Egy üzenet küldése a szolgáltatás szívverése után öt percenként történik, amíg a szolgáltatás fut.
* Minden alkalommal, amikor új jelszó érkezik, a rendszer két üzenetet küld:
   * Az első üzenet egy kérelem a művelet végrehajtásához.
   * A második üzenet tartalmazza a művelet eredményét, és a következő esetekben küldi el a rendszer:
      * Minden alkalommal, amikor új jelszót küld a rendszer a felhasználói önkiszolgáló jelszó-visszaállítás során.
      * Minden alkalommal, amikor új jelszót küldenek egy felhasználói jelszó módosítására szolgáló művelet során.
      * Minden alkalommal, amikor új jelszót küldenek egy rendszergazda által kezdeményezett felhasználói jelszó-visszaállítás során (csak az Azure felügyeleti portálról).

#### <a name="message-size-and-bandwidth-considerations"></a>Az üzenetek mérete és a sávszélességgel kapcsolatos megfontolások

A korábban leírt üzenetek mérete általában 1 KB. A jelszó visszaírási szolgáltatás akár extrém terhelések mellett is igénybe vesz néhány kilobit/másodpercet. Mivel az egyes üzeneteket valós időben küldi el a rendszer, csak akkor, ha a jelszó-frissítési művelet megköveteli, és mivel az üzenet mérete igen kicsi, a visszaírási képesség sávszélesség-használata túl kicsi ahhoz, hogy mérhető hatást lehessen gyakorolni.

## <a name="supported-writeback-operations"></a>Támogatott visszaírási-műveletek

A jelszavakat a következő helyzetekben kell visszaírni:

* **Támogatott végfelhasználói műveletek**
   * Bármely végfelhasználói önkiszolgáló önkéntes jelszó-módosítási művelet
   * Bármilyen végfelhasználói önkiszolgáló kényszerített jelszó-módosítási művelet, például a jelszó lejárata
   * A [jelszó-visszaállítási portálról](https://passwordreset.microsoftonline.com) származó összes végfelhasználói önkiszolgáló jelszó-visszaállítás
* **Támogatott rendszergazdai műveletek**
   * Bármilyen rendszergazdai önkiszolgáló önkéntes jelszó-módosítási művelet
   * Minden rendszergazda önkiszolgáló kényszerített jelszó-módosítási művelet, például a jelszó lejárata
   * A [jelszó-visszaállítási portálról](https://passwordreset.microsoftonline.com) származó összes rendszergazda önkiszolgáló jelszó-visszaállítás
   * A rendszergazda által kezdeményezett végfelhasználói jelszó alaphelyzetbe állítása a [Azure Portal](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Nem támogatott visszaírási-műveletek

A jelszavakat a következő helyzetekben *nem* írja vissza a rendszer:

* **Nem támogatott végfelhasználói műveletek**
   * Bármely végfelhasználó alaphelyzetbe állítja a saját jelszavát a PowerShell 1-es vagy 2-es verziójának használatával vagy a Microsoft Graph API-val
* **Nem támogatott rendszergazdai műveletek**
   * Bármely rendszergazda által kezdeményezett végfelhasználói jelszó alaphelyzetbe állítása a PowerShell 1-es verziójáról, a 2-es verzióról vagy a Microsoft Graph API-ról
   * Bármely rendszergazda által kezdeményezett végfelhasználói jelszó alaphelyzetbe állítása a [Microsoft 365 felügyeleti központban](https://admin.microsoft.com)

> [!WARNING]
> A (z) "a következő bejelentkezéskor a felhasználónak meg kell változtatnia a jelszót" jelölőnégyzet használata a helyszíni Active Directory felügyeleti eszközök, például a Active Directory felhasználók és számítógépek, vagy a Active Directory felügyeleti központ előzetes funkciójaként támogatott Azure AD Connect. További információkért lásd a [jelszó-kivonatok szinkronizálásának megvalósítása Azure ad Connect szinkronizálással](../hybrid/how-to-connect-password-hash-synchronization.md)című cikket.

## <a name="next-steps"></a>Következő lépések

Jelszó engedélyezése visszaírási az oktatóanyag használatával: [jelszó-visszaírási engedélyezése](tutorial-enable-writeback.md)
