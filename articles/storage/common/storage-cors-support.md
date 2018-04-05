---
title: Eltérő eredetű erőforrások megosztása (CORS) támogatást |} Microsoft Docs
description: Ismerje meg a CORS-támogatás engedélyezése a Microsoft Azure tárolási szolgáltatásokhoz.
services: storage
documentationcenter: .net
author: cbrooksmsft
manager: carmonm
editor: tysonn
ms.assetid: a0229595-5b64-4898-b8d6-fa2625ea6887
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: cbrooks
ms.openlocfilehash: 8d189d3ec3e6081dd37b912824f287cd75f39b35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Eltérő eredetű erőforrások megosztása (CORS) támogatása az Azure Storage szolgáltatások
2013-08-15 verziójával kezdve az Azure storage szolgáltatások támogatja az eltérő eredetű erőforrások megosztása (CORS) a Blob, Table, várólista és fájl számára. A CORS az HTTP szolgáltatása: lehetővé teszi, hogy a webalkalmazás fut egy tartomány egy másik tartományban lévő erőforrások eléréséhez. Webböngészők néven ismert biztonsági korlátozások megvalósítása [azonos eredetű házirend](http://www.w3.org/Security/wiki/Same_Origin_Policy) , amely megakadályozza, hogy egy másik tartományban; hívási API-weblap A CORS engedélyezése tartománya (a forrástartomány) API-k hívására egy másik tartományban biztonságos lehetőséget biztosít. Tekintse meg a [CORS specification](http://www.w3.org/TR/cors/) CORS leírását.

A CORS szabályainak beállítása külön-külön az egyes tárolási szolgáltatások meghívásával [Blob szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452235.aspx), [várólista-tulajdonságok beállítása](https://msdn.microsoft.com/library/hh452232.aspx), és [Table szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452240.aspx). Ha egyszer már megadta a CORS-szabályokat a szolgáltatáshoz, majd más tartományokból a szolgáltatásra szóló megfelelően hitelesített kérelem kiértékelendő annak meghatározásához, hogy a megadott szabályok szerint engedélyezett.

> [!NOTE]
> Vegye figyelembe, hogy a CORS nem olyan hitelesítési módszert. Kérését a tároló egyik erőforrásához szemben ha engedélyezve van a CORS rendelkeznie kell a megfelelő hitelesítési aláírás, vagy egy nyilvános erőforrás elleni kell tenni.
> 
> 

## <a name="understanding-cors-requests"></a>A CORS kérelmek ismertetése
A CORS kérelem származási tartományból két külön kérelmet állhat:

* A elővizsgálati kérelmet, amely lekérdezi a CORS korlátozásai a szolgáltatást. Az ellenőrzési kérés szükség, ha a kérelem a módszer egy [egyszerű módszer](http://www.w3.org/TR/cors/), ami azt jelenti, GET, HEAD vagy POST.
* A tényleges kérést, a kívánt erőforrás ellen.

### <a name="preflight-request"></a>Ellenőrzési kérés
Az ellenőrzési kérés lekérdezéseket a CORS-korlátozások, amely a fiók tulajdonosa által a társzolgáltatás létrehozva. A webböngésző (vagy egyéb felhasználói ügynök) küld, amely tartalmazza a kérelemfejléc, metódus és a forrás tartományi beállítások kérelmet. A tároló szolgáltatás értékeli ki a kívánt műveletet a CORS-szabályokat, amelyek adja meg, melyik eredettartományból, a kérelem metódusok és a kérelem fejlécében a tároló egyik erőforrásához egy tényleges kérelmet a adható meg előre beállított készlete alapján.

CORS engedélyezve van a szolgáltatáshoz, és van, amely megfelel az ellenőrzési kérés CORS szabály, ha a szolgáltatás válaszol, állapotkód: 200 (OK), és tartalmazza a szükséges hozzáférési fejlécek a válaszban.

Ha a szolgáltatás számára nem engedélyezett a CORS, vagy nincs CORS szabály megegyezik az ellenőrzési kérés, a szolgáltatás válaszol, állapotkód: 403 (tiltott).

Ha a beállítások kérelem nem tartalmazza a szükséges CORS fejléceket (a forrás- és hozzáférés-vezérlési-kérelem-metódus fejlécekkel együtt), a szolgáltatás válaszol, állapotkód: 400 (hibás kérés).

Vegye figyelembe, hogy az ellenőrzési kérés ki lesz értékelve a szolgáltatás (Blob, Queue és Table) és nem a kért erőforrás elleni. A fiók tulajdonosának kell engedélyezte a CORS ahhoz, hogy a kérelem sikeres fióktulajdonságok szolgáltatás részeként.

### <a name="actual-request"></a>Tényleges kérelem
Miután az ellenőrzés kérelem elfogadása, és a választ ad vissza, a böngésző csatolva az a tárolási erőforrások tényleges kérelmet. A böngésző megtagadja a tényleges azonnal kérése, ha az ellenőrzés vonatkozó kérés elutasítva.

A tényleges kérelmet a rendszer a társzolgáltatás normál kérelmet. A forrás-fejléc jelzi, hogy a kérelem CORS kérelmet, és a szolgáltatás ellenőrzi a megfelelő CORS-szabályokat. Ha a program egyezést talál, a hozzáférés-vezérlő fejlécekkel együtt a válaszhoz felvétele, illetve az ügyfélnek küldött. Nem található egyezés, ha a CORS hozzáférési fejlécek nem lehet megjeleníteni.

## <a name="enabling-cors-for-the-azure-storage-services"></a>A CORS engedélyezése az Azure Storage szolgáltatás
CORS-szabályokat úgy van beállítva, a szolgáltatás szintjén engedélyezhető vagy tiltható le a CORS az egyes szolgáltatásokhoz (Blob, várólista és tábla) kell külön-külön. A CORS minden egyes szolgáltatás alapértelmezés szerint le van tiltva. CORS engedélyezéséhez állítsa be a megfelelő szolgáltatásra tulajdonságokat verziójával 2013-08-15 kell vagy újabb verzióját, és adja hozzá a CORS-szabályokat a szolgáltatás tulajdonságai. Engedélyezheti vagy tilthatja le részletei CORS szolgáltatás és beállítása a CORS-szabályokat, tekintse meg [Blob szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452235.aspx), [várólista-tulajdonságok beállítása](https://msdn.microsoft.com/library/hh452232.aspx), és [Table szolgáltatás beállítása Tulajdonságok](https://msdn.microsoft.com/library/hh452240.aspx).

Itt látható egy minta egy CORS szabályt, a szolgáltatás tulajdonságainak beállítása művelet keresztül:

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

A CORS szabályban foglalt egyes elemei az alábbiakban olvasható:

* **AllowedOrigins**: A eredettartományból, szemben a társzolgáltatás keresztül CORS kérés számára. A forrástartomány a tartományhoz, ahonnan a kérés származik. Vegye figyelembe, hogy a forrás a forrás, amely a felhasználói kora küld a szolgáltatásnak a kis-és nagybetűket pontosan egyeznie kell lennie. A helyettesítő karakter is használható ' *' minden eredettartományból kéréseket a CORS segítségével történő engedélyezéséhez. A fenti példában, a tartományok [ http://www.contoso.com ](http://www.contoso.com) és [ http://www.fabrikam.com ](http://www.fabrikam.com) kéréseiket a CORS használatával szolgáltatásra.
* **AllowedMethods**: A (HTTP-kérelem műveletek) használó metódusok a forrástartomány előfordulhat, hogy a CORS-kérelemre. A fenti példában csak a PUT és a GET kérelmek engedélyezettek.
* **AllowedHeaders**: A kérelem fejlécében, hogy a forrástartomány adhatnak meg a CORS kérésre. A fenti példában az x-ms-metaadatok, x-ms-metaadat-tároló és az x-ms-meta-abc kezdve minden metaadat fejléc engedélyezett. Vegye figyelembe, hogy a helyettesítő karakter "*" jelzi, hogy engedélyezve van-e a-tól kezdődően fejlécben megadott előtag.
* **ExposedHeaders**: a CORS-kérelemre válaszként küldött és jelennek meg, ha a böngésző számára a kérelmet kibocsátó response fejlécekkel együtt. A fenti példában az a böngésző teszi közzé a-tól kezdődően fejléc x-ms-meta arra utasította.
* **MaxAgeInSeconds**: kérése a maximális időt, hogy a böngésző kell gyorsítótárazza az ellenőrzési beállítások.

Az Azure storage szolgáltatásainak megadását oszloplistájában fejlécek támogatja mind a **AllowedHeaders** és **ExposedHeaders** elemek. Ahhoz, hogy egy kategóriát a fejlécek, egy közös kategória-előtagot is megadhat. Például megadó *x-ms-meta** oszloplistájában fejléc alapján lesznek meghatározva azok egy szabályt, amely az összes fejléc x-ms-meta kezdődő fog egyezni.

CORS-szabályokat a következő korlátozások vonatkoznak:

* Tárolási szolgáltatás (Blob, Table és Queue) esetében legfeljebb öt CORS-szabályokat is megadhat.
* Összes CORS szabályok beállítása a kérés XML-címkék nélkül maximális mérete legfeljebb 2 KB lehet.
* Egy engedélyezett fejléc, kitett fejléc vagy eredet engedélyezett hosszát nem lehet hosszabb 256 karakternél.
* Megengedett fejlécek és elérhetőségi fejlécek lehetnek:
  * Literális fejlécek, ahol a pontos fejlécnév valósul meg, például a **x-ms-meta-feldolgozott**. A kérésre egy legfeljebb 64 literális fejlécek adható meg.
  * Fejlécek, ahol a fejléc előtag valósul meg, például a következő előtaggal ** x-ms-meta-data x. Adja meg a előtag ily módon lehetővé teszi, és elérhetővé teszi a fejlécet, amely a megadott előtaggal kezdődik. A kérésre legfeljebb két oszloplistájában fejléc adható meg.
* A módszerek (vagy a HTTP-műveletek) szerepel a **AllowedMethods** elemet meg kell felelnie az Azure storage szolgáltatás API-k által támogatott módszerek. Támogatott módszereket törlése, GET, HEAD, egyesítési, POST, beállítások és a PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>CORS értékelési szabálylogikával ismertetése
Egy tároló szolgáltatás előzetes vagy tényleges kérelmet kap, ha a kérésre a CORS-szabályokat a szolgáltatás a megfelelő szolgáltatás tulajdonságainak beállítása művelet keresztül létesítése alapján értékeli ki. CORS-szabályokat, amelyben a kérés törzsében a szolgáltatás tulajdonságainak beállítása művelet voltak beállítva sorrendben értékeli ki a rendszer.

CORS-szabályokat az alábbiak szerint értékeli:

1. Először a kérelem a forrástartomány felsorolt tartományok összeveti a **AllowedOrigins** elemet. Ha a forrástartomány szerepel a listában, vagy minden engedélyezett és a helyettesítő karakter "*", majd a kiértékelési hibákra vonatkozó szabályokat. Ha a forrástartomány nincs megadva, akkor a kérelem sikertelen lesz.
2. A következő a kérelem metódus (vagy HTTP-műveletet) ellenőrizi módszert a **AllowedMethods** elemet. Ha a metódus szerepel a listában, majd szabályok értékelésének folytatja; Ha nem, akkor a kérés nem teljesíthető.
3. Ha a kérelem megfelel egy szabályt a forrástartomány és az metódust, ez a szabály feldolgozni a kérelmet, és nincsenek további szabályok kiértékelése az van kiválasztva. Mielőtt a kérelem sikeres, azonban a kérésben megadott fejléc veti össze a fejlécek szerepelnek a **AllowedHeaders** elemet. Ha az elküldött fejlécek nem egyezik a megengedett fejlécek, a kérelem sikertelen lesz.

Mivel a szabályok feldolgozása a ahhoz, azok a kérés törzsében szerepelnek, a bevált gyakorlat része, hogy a szigorúbb szabályok források először meg a listában, hogy ezek értékeli ki a rendszer először. Adja meg a szabályokat, amelyek kevésbé korlátozó – például egy szabályt, amely engedélyezi az összes források – a lista végére.

### <a name="example--cors-rules-evaluation"></a>Példa – CORS szabályok kiértékelése
A következő példa bemutatja a tárolási szolgáltatások CORS-szabályokat állíthat be a művelet egy részleges kérés törzsében. Lásd: [Blob szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452235.aspx), [várólista-tulajdonságok beállítása](https://msdn.microsoft.com/library/hh452232.aspx), és [Table szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452240.aspx) talál részletes információt hozhat létre a kérelmet.

```xml
<Cors>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>PUT,HEAD</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>*</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
    </CorsRule>
</Cors>
```

Ezt követően vegye figyelembe a következő CORS kérelmeket:

| Kérés |  |  | Válasz |  |
| --- | --- | --- | --- | --- |
| **Módszer** |**Forrás** |**Kérelem fejlécei** |**A szabály egyezés** |**eredménye** |
| **PUT** |http://www.contoso.com |x-ms-blob-content-type |Első szabály |Sikeres |
| **GET** |http://www.contoso.com |x-ms-blob-content-type |Második szabály |Sikeres |
| **GET** |http://www.contoso.com |x-ms-client-request-id |Második szabály |Hiba |

Az első kérelem megfelel az első szabály – a forrástartomány megegyezik az engedélyezett eredetet, a metódus felel meg az engedélyezett metódusok és a fejléc megegyezik az engedélyezett fejlécek – és így sikeres lesz.

A második kérelem nem egyezik az első szabály, mert a metódus nem egyezik meg az engedélyezett metódusok. Azonban egyezik a második szabálynak, így ez sikeres.

A harmadik kérelem megfelel a forrástartomány és metódus, a második szabálynak, így nincsenek további szabályok kiértékelése. Azonban a *x-ms-client-request-id fejléc* nem engedélyezett a második szabály, ezért a kérelem sikertelen lesz, annak ellenére, hogy a harmadik szabály szemantikáját használhatott volna sikeres.

> [!NOTE]
> Bár ez a példa bemutatja egy kevésbé korlátozó szabály szigorúbb egy előtt, az ajánlott eljárás általában listázásához először a szigorúbb szabályokat.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Hogyan van beállítva a Vary fejléce ismertetése
A *Vary* fejléc olyan készlete, amely tájékoztatja a böngésző vagy a felhasználói ügynök a feltételeket, amelyek a kérelem feldolgozása a kiszolgáló által kiválasztott vonatkozó kérelem fejlécmezők álló szabványos HTTP/1.1 fejléc. A *Vary* fejléc főleg a gyorsítótárazáshoz proxyk, a böngésző támogatja, és a tartalomtovábbító, amelyek segítségével határozza meg, hogyan kell gyorsítótárazza a választ. További információkért lásd: a előírása a [Vary fejléce](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

A böngésző vagy egy másik felhasználói ügynök gyorsítótárazza a CORS kérelmet kapott válasz, ha a forrástartomány gyorsítótárazza, mint az engedélyezett forrása. Ha a második tartomány ki a tároló-erőforrások esetében a kérésben, amíg a gyorsítótár aktív, a felhasználói ügynök lekéri a gyorsítótárazott forrástartományt. A második tartománya nem felel meg a gyorsítótárazott tartományifiók, ezért a kérelem sikertelen lesz, ha más módon járnak. Bizonyos esetekben az Azure Storage állítja be a Vary fejléce **származási** arra utasítani a felhasználói ügynök CORS kérést küld a szolgáltatást, amikor a kérést küldő tartomány eltér a gyorsítótárazott forrás.

Az Azure Storage-készletek a *Vary* fejlécének **származási** a tényleges GET vagy HEAD kérelem a következő esetekben:

* Ha a kérelem származási pontosan megegyezik az engedélyezett forrása a CORS szabály szerint. Pontosan egyeznie kell a CORS szabály nem tartalmazhatnak meg a helyettesítő karakter "*" karaktert.
* Nincs a kérelem származási egyező szabály, de a CORS engedélyezve van a tároló szolgáltatást.

Abban az esetben, ha egy GET vagy HEAD kérelem megfelel-e a CORS szabályt, amely lehetővé teszi, hogy minden eredet a válasz azt jelzi, hogy minden eredet engedélyezett, a felhasználói ügynök gyorsítótár lehetővé teszi bármely forrástartományt érkező későbbi kérelmeket, amíg a gyorsítótár aktív.

Vegye figyelembe, hogy a módszerekkel nem GET vagy HEAD kérelmek, a tárolási szolgáltatások nem állítja be a Vary fejléce, mivel ezek a módszerek válaszokat nem gyorsítótárazzák a felhasználói ügynök.

Az alábbi táblázat tartalmazza az Azure storage a korábban említett eset alapján GET vagy HEAD kérésére válaszolni fog:

| Kérés | Fiók beállítás és a szabály kiértékelés eredménye |  |  | Válasz |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Megtalálható a kérelem származási fejléc** |**Ez a szolgáltatás számára megadott CORS szabályok** |**Létezik egyező szabály, amely lehetővé teszi az összes origins(*)** |**Létezik egyező szabály forrás pontos egyezés** |**Válasz Vary fejléce forrás beállítása tartalmazza** |**Válasz tartalmazza a hozzáférés-vezérlési-engedélyezett-forrása: "*"** |**Válasz tartalmazza a hozzáférés-vezérlési-kitett-fejlécek** |
| Nem |Nem |Nem |Nem |Nem |Nem |Nem |
| Nem |Igen |Nem |Nem |Igen |Nem |Nem |
| Nem |Igen |Igen |Nem |Nem |Igen |Igen |
| Igen |Nem |Nem |Nem |Nem |Nem |Nem |
| Igen |Igen |Nem |Igen |Igen |Nem |Igen |
| Igen |Igen |Nem |Nem |Igen |Nem |Nem |
| Igen |Igen |Igen |Nem |Nem |Igen |Igen |

## <a name="billing-for-cors-requests"></a>A CORS kérelmek számlázási
Sikeres ellenőrzés kéri, ha engedélyezte a fiók a tárolási szolgáltatások bármelyikéhez CORS számlázása (meghívásával [Blob szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452235.aspx), [várólista-tulajdonságok beállítása](https://msdn.microsoft.com/library/hh452232.aspx), vagy [Tábla szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452240.aspx)). Költségek minimalizálása érdekében fontolja meg a beállítás a **MaxAgeInSeconds** eleme a CORS-szabályok nagy értéket, hogy a felhasználói ügynök gyorsítótárazza a kérelmet.

Sikertelen ellenőrzés kérelmek nem lesz terhelve.

## <a name="next-steps"></a>További lépések
[Blob szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452235.aspx)

[Várólista-szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452232.aspx)

[Table szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C eltérő eredetű erőforrások megosztása meghatározása](http://www.w3.org/TR/cors/)

