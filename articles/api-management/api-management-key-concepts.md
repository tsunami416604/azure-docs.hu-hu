---
title: "Az Azure API Management áttekintése és főbb fogalmai | Microsoft Docs"
description: "Ismerje meg az API-kat, a termékeket, a szerepköröket, a csoportokat és az API Management többi alapfogalmát."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 0410e0176d5c853e1110fe3460c7d9314e7fd397
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="what-is-api-management"></a>Mi az API Management?

Az API Management (APIM) segít a szervezeteknek közzétenni az API-kat a külső, a partner- és a belső fejlesztők számára, hogy ki tudják bontakoztatni az adataikban és szolgáltatásaikban rejlő lehetőségeket. A vállalkozások világszerte digitális platformon szeretnék bővíteni a működésüket, új csatornákat szeretnének létrehozni, új ügyfeleket szeretnének találni és mélyíteni szeretnék a meglévő ügyfelekkel kialakított kapcsolatot. Az API Management a fejlesztők bevonásán, az üzleti elemzéseken, a biztonságon és a védelmen keresztül biztosítja az alapvető kompetenciákat az API-program sikeressé tételéhez. Az Azure API Management szolgáltatással bármely háttérrendszer alapján elindíthat egy teljes értékű API-programot.

Ez a cikk áttekintést nyújt az APIM-ot érintő gyakori forgatókönyvekről.  Emellett rövid áttekintést ad az APIM rendszer főbb összetevőiről. A cikk ezután részletesebben mutatja be az egyes összetevőket.

## <a name="overview"></a>Áttekintés

Az API Management használatához a rendszergazdák API-kat hoznak létre. Minden API egy vagy több műveletből áll, és minden API-t hozzá lehet adni egy vagy több termékhez. Az API-k használatához a fejlesztőknek elő kell fizetniük egy termékre, amely tartalmazza az adott API-t, utána meghívhatják az API műveletét, amelyre érvényesek az esetleg érvényben lévő használati házirendek. A gyakori forgatókönyvek a következők:

* **A mobil infrastruktúra biztonságossá tétele** a hozzáférés API-kulcsokkal történő korlátozásával, a szolgáltatásmegtagadási támadások szabályozással történő megelőzésével vagy speciális biztonsági házirendek, például a JWT-jogkivonat alapú érvényesítés használatával.
* **Az ISV-k partneri rendszereinek engedélyezése** a fejlesztői portálon keresztüli gyors partnerfelvétellel és egy API-homlokzat építésével annak érdekében, hogy a belső megvalósításokról leválassza a partnerek általi használatra nem alkalmas megvalósításokat.
* **Belső API-program futtatása** egy központi hely felajánlásával, ahol a szervezet tagjai kommunikálhatnak az API-k rendelkezésre állásáról és a legújabb módosításaikról, illetve a hozzáférés szervezeti fiókok alapján történő korlátozásával, mindezt az API-átjáró és a háttérrendszer közötti biztonságos csatorna alapján.

A rendszer az alábbi összetevőkből áll:

* Az **API-átjáró** az a végpont, amely:
  
  * Fogadja az API-hívásokat, és továbbítja őket a háttérrendszerekre.
  * Ellenőrzi az API-kulcsokat, JWT-jogkivonatokat, tanúsítványokat és más hitelesítő adatokat.
  * Betartatja a használati kvótákat és a sebességkorlátokat.
  * Villámgyorsan, kódmódosítás nélkül átalakítja az API-kat.
  * Gyorsítótárazza a háttérrendszer válaszait, ahol ez be van állítva.
  * Elemzési céllal naplózza a hívások metaadatait.
* Az **Azure Portal** az a rendszergazdai felület, ahol beállíthatja az API-programot. A következőkre lehet használni:
  
  * API-séma meghatározása vagy importálása.
  * API-k termékekbe csomagolása.
  * Házirendek, például kvóták vagy átalakítások beállítása az API-kra.
  * Elemzések lekérése.
  * Felhasználók kezelése.
* A **Developer portal** a fejlesztők fő webhelye, ahol a következőket tehetik:
  
  * Elolvashatják az API-dokumentációt.
  * API-kat próbálhatnak ki az interaktív konzollal.
  * Létrehozhatnak egy fiókot és előfizethetnek, hogy API-kulcsokat szerezzenek.
  * Hozzáférhetnek a használat adataikról készült elemzésekhez.

További információkért tekintse meg a [Felhőalapú API Management: Az API-kban rejlő lehetőségek kihasználása](http://j.mp/ms-apim-whitepaper) című PDF tanulmányt. A CITO Research által írt, az API Management szolgáltatást bemutató tanulmány az alábbiakat tárgyalja: 
 
 * Gyakori API-követelmények és kihívások
 * Az API-k leválasztása és a homlokzatok bemutatása
 * A fejlesztők számára gyors és egyszerű munkakezdés
 * A hozzáférés biztonságossá tétele
 * Elemzések és mérőszámok
 * Ellenőrzés és áttekintés az API Management-platformmal
 * Felhőalapú és helyszíni megoldások használata
 * Azure API Management
 
## <a name="apis"></a>API-k és műveletek
Az API-k az API Management szolgáltatáspéldányok alapjai. Minden API egy, a fejlesztők számára elérhető műveletkészletet képvisel. Minden API tartalmaz egy hivatkozást az API-t megvalósító háttérszolgáltatáshoz, a műveletei pedig leképezik a háttérszolgáltatás által megvalósított műveleteket. Az API Management műveletei részletesen konfigurálhatók, szabályozni lehet az URL-címmegfeleltetést, a lekérdezések és útvonalak paramétereit, a kérelmek és válaszok tartalmát, valamint a művelet válaszainak gyorsítótárazását. A sebességkorlát, a kvóták és az IP-korlátozás házirendjeit az API vagy az egyéni műveletek szintjén is meg lehet valósítani.

További információkért lásd: [API-k létrehozása][How to create APIs] és [Műveletek hozzáadása API-khoz][How to add operations to an API].

## <a name="products"></a> Termékek
Az API-k a termékeken keresztül válnak elérhetővé a fejlesztők számára. Az API Management szolgáltatásban a termékek egy vagy több API-val rendelkeznek, emellett címmel, leírással és használati feltételekkel vannak konfigurálva. A termékeknek két típusa létezik: **Nyílt** és **Védett**. A védett termékeket csak előfizetők használhatják, míg a nyílt termékeket előfizetés nélkül is lehet használni. Amikor egy termék készen áll a fejlesztők általi használatra, közzé lehet tenni. A közzététel után a fejlesztők meg tudják tekinteni, védett termékek esetén pedig elő tudnak rájuk fizetni. Az előfizetés jóváhagyása a termék szintjén van konfigurálva, és vagy rendszergazdai jóváhagyás kell hozzá, vagy automatikusan jóvá lesz hagyva.

A csoportok használatával szabályozható a fejlesztők hozzáférése a termékhez. A csoportok számára a termékek biztosítanak láthatóságot, a fejlesztők pedig megtekinthetik a csoportjuk számára látható termékeket és előfizethetnek rájuk. 

## <a name="groups"></a> Csoportok
A csoportok használatával szabályozható a fejlesztők hozzáférése a termékhez. Az API Management az alábbi megváltoztathatatlan rendszercsoportokkal rendelkezik:

* **Rendszergazdák** – A csoportot Azure-előfizető rendszergazdák alkotják. A rendszergazdák kezelik az API Management szolgáltatáspéldányokat, valamint ők hozzák létre az API-kat, a műveleteket és a fejlesztők által használt termékeket.
* **Fejlesztők** – A fejlesztői portál hitelesített felhasználói tartoznak ebbe a csoportba. A fejlesztők olyan ügyfelek, akik alkalmazásokat hoznak létre az API-k segítségével. A fejlesztők hozzáférhetnek a fejlesztői portálhoz, és olyan alkalmazásokat készíthetnek, amelyek egy API műveleteit hívják meg.
* **Vendégek** – A fejlesztői portál nem hitelesített felhasználói tartoznak ebbe a csoportba, például az egyik API Management példány fejlesztői portálját meglátogató leendő ügyfelek. A vendégek kaphatnak bizonyos szintű, csak olvasási hozzáférést, például megtekinthetnek API-kat, de nem hívhatják meg őket.

Ezeken a rendszercsoportokon kívül a rendszergazdák létrehozhatnak egyéni csoportokat vagy [áttelepíthetik a társított Azure Active Directory-bérlők külső csoportjait is](api-management-howto-aad.md). A fejlesztők mellett az egyéni és külső csoportoknak is lehet adni láthatóságot és hozzáférést az API-termékekhez. Például egy adott partnerszervezet fejlesztői számára létre lehet hozni egy egyéni csoportot, és hozzáférést lehet nekik biztosítani a megfelelő API-kat tartalmazó termék API-jaihoz. Egy felhasználó egyszerre több csoport tagja is lehet.

További információkért lásd: [Csoportok létrehozása és használata][How to create and use groups].

## <a name="developers"></a> Fejlesztők
A fejlesztők képviselik a felhasználói fiókokat az API Management szolgáltatáspéldányban. A fejlesztőket létrehozhatják vagy meghívhatják a rendszergazdák, vagy a [Fejlesztői portálon][Developer portal] is regisztrálhatnak. Minden fejlesztő egy vagy több csoport tagja, és előfizethet azokra a termékekre, amelyek láthatóságot biztosítanak a csoportjaik számára.

Amikor a fejlesztők előfizetnek egy termékre, megkapják a termék elsődleges és másodlagos azonosítóját. Ezt az azonosítót akkor kell használniuk, amikor a termék API-jait meghívják.

További információkért lásd: [Fejlesztők létrehozása vagy meghívása][How to create or invite developers] és [Csoportok társítása a fejlesztőkhöz][How to associate groups with developers].

## <a name="policies"></a> Házirendek
A házirend az API Management kiemelkedő funkciója, amely lehetővé teszi az Azure Portal számára, hogy konfigurálással módosítsa az API viselkedését. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A népszerű utasítások közé tartozik a formátumátalakítás XML-ről JSON-ra, a hívások sebességének korlátozása a fejlesztőktől érkező hívások számának korlátozásához és számos más elérhető házirend.

A házirend-kifejezéseket attribútumértékekként vagy szövegértékekként lehet használni bármelyik API Management házirendben, hacsak a házirend másként nem rendelkezik. Néhány házirend, például a [Vezérlés folyamata](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) és a [Változó beállítása](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) házirend-kifejezéseken alapul. További információ: [Speciális szabályzatok](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies) és [Szabályzatkifejezések](https://msdn.microsoft.com/library/azure/dn910913.aspx).


Az API Management házirendek teljes listájáért tekintse meg a [Házirend-referencia][Policy reference] szakaszt. További információ a házirendek használatáról és konfigurálásáról: [API Management házirendek][API Management policies]. Ha egy sebességkorlát- és kvótaházirendekkel rendelkező termék létrehozásához keres oktatóanyagot, tekintse meg a [Speciális termékbeállítások létrehozása és konfigurálása][How create and configure advanced product settings] című szakaszt.


## <a name="developer-portal"></a> Fejlesztői portálon
A fejlesztők a fejlesztői portálon ismerhetik meg az API-kat, tekinthetik meg és hívhatják meg a műveleteket, és ott fizethetnek elő a termékekre. A leendő ügyfelek meglátogathatják a fejlesztői portált, megtekinthetik az API-kat és a műveleteket, illetve regisztrálhatnak. A fejlesztői portál URL-címe az API Management szolgáltatáspéldányhoz tartozó Azure Portal irányítópultján található.

A fejlesztői portál megjelenését és működését egyéni tartalom hozzáadásával, a stílusok testre szabásával és a vállalat emblémájának hozzáadásával lehet személyre szabni.

## <a name="api-management-and-the-api-economy"></a>API Management és az API-k gazdaságossága

Ha többet szeretne megtudni az API Management szolgáltatásról, tekintse meg az alábbi bemutatót a 2017-es Microsoft Ignite konferenciáról.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2186/player]
> 
> 

## <a name="next-steps"></a>További lépések

Hajtsa végre az alábbi gyors útmutatót, és kezdje el használni az Azure API Management szolgáltatást:

> [!div class="nextstepaction"]
> [Azure API Management-példány létrehozása](get-started-create-service-instance.md)

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: api-management-howto-create-apis.md
[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: transform-api.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: api-management-policy-reference.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: get-started-create-service-instance.md




