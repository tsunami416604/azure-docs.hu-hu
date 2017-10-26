---
title: "Az Azure API Management áttekintése és főbb fogalmai | Microsoft Docs"
description: "Ismerje meg az API-kat, a termékeket, a szerepköröket, a csoportokat és az API Management többi alapfogalmát."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: e71da405-835a-48f3-956f-45c1a85698d7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: becffc6011ef1dd49e07d22880d3346036629393
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="what-is-api-management"></a>Mi az API Management?
Az API Management segít a szervezeteknek közzétenni az API-kat külső, partneri és belső fejlesztők számára, hogy ki tudják bontakoztatni az adataikban és szolgáltatásaikban rejlő lehetőségeket. A vállalkozások világszerte digitális platformon szeretnék bővíteni a működésüket, új csatornákat szeretnének létrehozni, új ügyfeleket szeretnének találni és mélyíteni szeretnék a meglévő ügyfelekkel kialakított kapcsolatot. Az API Management a fejlesztők bevonásán, az üzleti elemzéseken, a biztonságon és a védelmen keresztül biztosítja az alapvető kompetenciákat az API-program sikeressé tételéhez.

Az alábbi videó segítségével áttekintheti az Azure API Management szolgáltatást, és megtudhatja, hogyan adhat hozzá funkciókat az API-jához az API Management használatával, például hozzáférés-vezérlést, sebességkorlátozást, figyelést, eseménynaplózást és a válaszok gyorsítótárazását, minimális befektetett munkával.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-API-Management-Overview/player]
> 
> 

Az API Management használatához a rendszergazdák API-kat hoznak létre. Minden API egy vagy több műveletből áll, és minden API-t hozzá lehet adni egy vagy több termékhez. Az API-k használatához a fejlesztőknek elő kell fizetniük egy termékre, amely tartalmazza az adott API-t, utána meghívhatják az API műveletét, amelyre érvényesek az esetleg érvényben lévő használati házirendek.

Ez a témakör áttekintést nyújt az API Management alapfogalmairól.

> [!NOTE]
> További információkért tekintse meg a [Felhőalapú API Management: Az API-kban rejlő lehetőségek kihasználása](http://j.mp/ms-apim-whitepaper) című PDF tanulmányt. A CITO Research által írt, az API Management szolgáltatást bemutató tanulmány az alábbiakat tárgyalja: 
> 
> * Gyakori API-követelmények és kihívások
> * Az API-k leválasztása és a homlokzatok bemutatása
> * A fejlesztők számára gyors és egyszerű munkakezdés
> * A hozzáférés biztonságossá tétele
> * Elemzések és mérőszámok
> * Ellenőrzés és áttekintés az API Management-platformmal
> * Felhőalapú és helyszíni megoldások használata
> * Azure API Management
> 
> 

## <a name="apis"></a>API-k és műveletek
Az API-k az API Management szolgáltatáspéldányok alapjai. Minden API egy, a fejlesztők számára elérhető műveletkészletet képvisel. Minden API tartalmaz egy hivatkozást az API-t megvalósító háttérszolgáltatáshoz, a műveletei pedig leképezik a háttérszolgáltatás által megvalósított műveleteket. Az API Management műveletei részletesen konfigurálhatók, szabályozni lehet az URL-címmegfeleltetést, a lekérdezések és útvonalak paramétereit, a kérelmek és válaszok tartalmát, valamint a művelet válaszainak gyorsítótárazását. A sebességkorlát, a kvóták és az IP-korlátozás házirendjeit az API vagy az egyéni műveletek szintjén is meg lehet valósítani.

További információkért lásd: [API-k létrehozása][How to create APIs] és [Műveletek hozzáadása API-khoz][How to add operations to an API].

## <a name="products"></a> Termékek
Az API-k a termékeken keresztül válnak elérhetővé a fejlesztők számára. Az API Management szolgáltatásban a termékek egy vagy több API-val rendelkeznek, emellett címmel, leírással és használati feltételekkel vannak konfigurálva. A termékeknek két típusa létezik: **Nyílt** és **Védett**. A védett termékeket csak előfizetők használhatják, míg a nyílt termékeket előfizetés nélkül is lehet használni. Amikor egy termék készen áll a fejlesztők általi használatra, közzé lehet azt tenni. A közzététel után a fejlesztők meg tudják tekinteni, védett termékek esetén pedig elő tudnak rájuk fizetni. Az előfizetés jóváhagyása a termék szintjén van konfigurálva, és vagy rendszergazdai jóváhagyás kell hozzá, vagy automatikusan jóvá lesz hagyva.

A csoportok használatával szabályozható a fejlesztők hozzáférése a termékhez. A csoportok számára a termékek biztosítanak láthatóságot, a fejlesztők pedig megtekinthetik a csoportjuk számára látható termékeket és előfizethetnek rájuk. 

További információkért lásd: A [Termék létrehozása és közzététele][How to create and publish a product] című szakasz és az alábbi videó.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

## <a name="groups"></a> Csoportok
A csoportok használatával szabályozható a fejlesztők hozzáférése a termékhez. Az API Management az alábbi megváltoztathatatlan rendszercsoportokkal rendelkezik.

* **Rendszergazdák** – A csoportot Azure-előfizető rendszergazdák alkotják. A rendszergazdák kezelik az API Management szolgáltatáspéldányokat, valamint ők hozzák létre az API-kat, a műveleteket és a fejlesztők által használt termékeket.
* **Fejlesztők** – A fejlesztői portál hitelesített felhasználói tartoznak ebbe a csoportba. A fejlesztők olyan ügyfelek, akik alkalmazásokat hoznak létre az API-k segítségével. A fejlesztők hozzáférhetnek a fejlesztői portálhoz, és olyan alkalmazásokat készíthetnek, amelyek egy API műveleteit hívják meg.
* **Vendégek** – A fejlesztői portál nem hitelesített felhasználói tartoznak ebbe a csoportba, például az egyik API Management példány fejlesztői portálját meglátogató leendő ügyfelek. A vendégek kaphatnak bizonyos szintű, csak olvasási hozzáférést, például megtekinthetnek API-kat, de nem hívhatják meg őket.

Ezeken a rendszercsoportokon kívül a rendszergazdák létrehozhatnak egyéni csoportokat vagy [áttelepíthetik a társított Azure Active Directory-bérlők külső csoportjait is](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). A fejlesztők mellett az egyéni és külső csoportoknak is lehet adni láthatóságot és hozzáférést az API-termékekhez. Például egy adott partnerszervezet fejlesztői számára létre lehet hozni egy egyéni csoportot, és hozzáférést lehet nekik biztosítani a megfelelő API-kat tartalmazó termék API-jaihoz. Egy felhasználó egyszerre több csoport tagja is lehet.

További információkért lásd: [Csoportok létrehozása és használata][How to create and use groups].

## <a name="developers"></a> Fejlesztők
A fejlesztők képviselik a felhasználói fiókokat az API Management szolgáltatáspéldányban. A fejlesztőket létrehozhatják vagy meghívhatják a rendszergazdák, vagy a [Fejlesztői portálon][Developer portal] is regisztrálhatnak. Minden fejlesztő egy vagy több csoport tagja, és előfizethet azokra a termékekre, amelyek láthatóságot biztosítanak a csoportjaik számára.

Amikor a fejlesztők előfizetnek egy termékre, megkapják a termék elsődleges és másodlagos azonosítóját. Ezt az azonosítót akkor kell használniuk, amikor a termék API-jait meghívják.

További információkért lásd: [Fejlesztők létrehozása vagy meghívása][How to create or invite developers] és [Csoportok társítása a fejlesztőkhöz][How to associate groups with developers].

## <a name="policies"></a> Házirendek
A házirend az API Management kiemelkedő funkciója, amely lehetővé teszi a közzétevő számára, hogy konfigurálással módosítsa az API viselkedését. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A népszerű utasítások közé tartozik a formátumátalakítás XML-ről JSON-ra, a hívások sebességének korlátozása a fejlesztőktől érkező hívások mennyiségének korlátozásához, és számos más elérhető házirend.

A házirend-kifejezéseket attribútumértékekként vagy szövegértékekként lehet használni bármelyik API Management házirendben, hacsak a házirend másként nem rendelkezik. Néhány házirend, például a [Vezérlés folyamata](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) és a [Változó beállítása](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable) házirend-kifejezéseken alapul. További információt a [Speciális házirendek](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies) és a [Házirend-kifejezések](https://msdn.microsoft.com/library/azure/dn910913.aspx) című cikkben, továbbá az alábbi videóban talál.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

Az API Management házirendek teljes listájáért tekintse meg a [Házirend-referencia][Policy reference] szakaszt. További információ a házirendek használatáról és konfigurálásáról: [API Management házirendek][API Management policies]. Ha egy sebességkorlát- és kvótaházirendekkel rendelkező termék létrehozásához keres oktatóanyagot, tekintse meg a [Speciális termékbeállítások létrehozása és konfigurálása][How create and configure advanced product settings] című szakaszt. Egy bemutatóért tekintse meg az alábbi videót.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="developer-portal"></a> Fejlesztői portálon
A fejlesztők a fejlesztői portálon ismerhetik meg az API-kat, tekinthetik meg és hívhatják meg a műveleteket, és ott fizethetnek elő a termékekre. A leendő ügyfelek meglátogathatják a fejlesztői portált, megtekinthetik az API-kat és a műveleteket, illetve regisztrálhatnak. A fejlesztői portál URL-címe az API Management szolgáltatáspéldányhoz tartozó klasszikus Azure portál irányítópultján található.

A fejlesztői portál megjelenését és működését egyéni tartalom hozzáadásával, a stílusok testre szabásával és a vállalat emblémájának hozzáadásával lehet személyre szabni.

## <a name="api-management-and-the-api-economy"></a>API Management és az API-k gazdaságossága
Ha többet szeretne megtudni az API Management szolgáltatásról, tekintse meg az alábbi bemutatót a 2015 -ös Microsoft Ignite konferenciáról.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3708/player]
> 
> 

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
[How create and configure advanced product settings]: api-management-howto-product-with-rules.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: api-management-policy-reference.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance




