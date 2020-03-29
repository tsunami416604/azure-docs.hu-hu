---
title: Több forrásból származó erőforrás-megosztás (CORS) támogatása | Microsoft dokumentumok
description: Megtudhatja, hogy miként engedélyezheti a CORS-támogatást a Microsoft Azure Storage Services szolgáltatáshoz.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bb296db0d97382deac984369704777de5d5cb362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65147691"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Az Azure Storage-szolgáltatások több eredetre vonatkozó erőforrás-megosztási (CORS) támogatása
A 2013-08-15-ös verziótól kezdve az Azure storage-szolgáltatások támogatják a Blob, Table, Queue és File szolgáltatások több eredetre vonatkozó erőforrás-megosztást (CORS). A CORS egy HTTP-szolgáltatás, amely lehetővé teszi, hogy az egyik tartományban futó webalkalmazások hozzáférjenek egy másik tartomány erőforrásaihoz. A webböngészők [azonos eredetű házirendként](https://www.w3.org/Security/wiki/Same_Origin_Policy) ismert biztonsági korlátozást alkalmaznak, amely megakadályozza, hogy egy weblap egy másik tartományban API-kat hívjon meg; A CORS biztonságos módot biztosít arra, hogy egy tartomány (az eredeti tartomány) egy másik tartományAPI-kat hívjon meg. A [CORS-ra](https://www.w3.org/TR/cors/) vonatkozó részleteket lásd a CORS specifikációban.

A CORS-szabályokat egyenként állíthatja be az egyes tárolási szolgáltatásokhoz, ha meghívja a [Blob szolgáltatás tulajdonságainak beállítása,](https://msdn.microsoft.com/library/hh452235.aspx) [a Várólistaszolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452232.aspx)és a [Táblaszolgáltatás tulajdonságainak beállítása szolgáltatást.](https://msdn.microsoft.com/library/hh452240.aspx) Miután beállította a CORS-szabályokat a szolgáltatáshoz, majd egy megfelelően engedélyezett kérést egy másik tartományból származó szolgáltatással szemben kiértékeli a rendszer annak megállapítására, hogy az engedélyezett-e a megadott szabályok szerint.

> [!NOTE]
> Vegye figyelembe, hogy a CORS nem hitelesítési mechanizmus. A cors engedélyezésekor a tárolóerőforrással szemben benyújtott kérelmeknek megfelelő hitelesítési aláírással kell rendelkezniük, vagy nyilvános erőforrással kell rendelkezniük.
> 
> 

## <a name="understanding-cors-requests"></a>A CORS-kérelmek ismertetése
A CORS-kérelem egy forrástartományból két külön kérelemből állhat:

* Elővizsgálati kérelem, amely lekérdezi a szolgáltatás által előírt CORS-korlátozásokat. Az elővizsgálati kérelemre csak akkor van szükség, ha a kérelem metódusa [egyszerű módszer,](https://www.w3.org/TR/cors/)azaz GET, HEAD vagy POST.
* A kívánt erőforrással szemben benyújtott tényleges kérelem.

### <a name="preflight-request"></a>Elővizsgálati kérelem
Az elővizsgálati kérelem lekérdezi a CORS-korlátozásokat, amelyeket a fiók tulajdonosa a storage szolgáltatáshoz létrehozott. A webböngésző (vagy más felhasználói ügynök) olyan OPTIONS-kérelmet küld, amely tartalmazza a kérelemfejléceket, a metódust és a forrástartományt. A tárolási szolgáltatás a cors-szabályok előre konfigurált készlete alapján értékeli ki a kívánt műveletet, amely meghatározza, hogy mely forrástartományok, kérési módszerek és kérésfejlécek adhatók meg egy tárolási erőforrással kapcsolatos tényleges kérelemben.

Ha a CORS engedélyezve van a szolgáltatáshoz, és van egy CORS-szabály, amely megfelel az elővizsgálati kérelemnek, a szolgáltatás 200-as állapotkóddal (OK) válaszol, és tartalmazza a szükséges hozzáférés-vezérlési fejléceket a válaszban.

Ha a CORS nincs engedélyezve a szolgáltatáshoz, vagy nincs CORS szabály megfelel az elővizsgálati kérelemnek, a szolgáltatás 403-as állapotkóddal válaszol (Tiltott).

Ha az OPTIONS kérés nem tartalmazza a szükséges CORS-fejléceket (az Origin és access-Control-Request-Method fejléceket), a szolgáltatás a 400-as állapotkóddal válaszol (Rossz kérés).

Vegye figyelembe, hogy az elővizsgálati kérelem kiértékelése a szolgáltatás (Blob, Várólista és tábla) és nem a kért erőforrás ellen. A kérelem sikeres eléréséhez a fióktulajdonosnak engedélyeznie kell a CORS-t a fiókszolgáltatás tulajdonságainak részeként.

### <a name="actual-request"></a>Tényleges kérelem
Miután az elővizsgálati kérelmet elfogadta, és a válasz visszaadta, a böngésző küldi a tényleges kérelmet a tárolási erőforrás ellen. A böngésző azonnal elutasítja a tényleges kérelmet, ha az elővizsgálati kérelmet elutasítják.

A tényleges kérelem normál kérésként lesz kezelve a tárolási szolgáltatással szemben. Az Origin fejléc jelenléte azt jelzi, hogy a kérelem egy CORS-kérelem, és a szolgáltatás ellenőrzi a megfelelő CORS-szabályokat. Ha egyezést talál, a hozzáférés-vezérlés fejlécei hozzáadódnak a válaszhoz, és visszaküldik őket az ügyfélnek. Ha nem talál egyezést, a CORS hozzáférés-vezérlési fejlécek nem kerülnek visszaadásra.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Cors engedélyezése az Azure Storage-szolgáltatásokhoz
A CORS-szabályok a szolgáltatási szinten vannak beállítva, ezért minden szolgáltatáshoz (Blob, Várólista és tábla) külön kell engedélyeznie vagy letiltania a CORS-t. Alapértelmezés szerint a CORS minden szolgáltatásban le van tiltva. A CORS engedélyezéséhez be kell állítania a megfelelő szolgáltatástulajdonságokat a 2013-08-15-ös vagy újabb verzióhasználatával, és hozzá kell adnia a CORS-szabályokat a szolgáltatás tulajdonságaihoz. A CORS engedélyezéséről és letiltásáról, valamint a CORS-szabályok beállításáról a [Blob-szolgáltatás tulajdonságainak beállítása,](https://msdn.microsoft.com/library/hh452235.aspx) [a Várólistaszolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452232.aspx)és [a Táblaszolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452240.aspx)..

Az alábbiakban egy CORS-szabályból álló minta látható, amelyet a Szolgáltatás tulajdonságainak beállítása művelet teljében ad meg:

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

A CORS-szabályban szereplő minden elem leírása az alábbiakban található:

* **AllowedOrigins**: A forrás tartományok, amelyek számára megengedett, hogy a kérelem ellen a tárolási szolgáltatás CORS-on keresztül. A forrástartomány az a tartomány, ahonnan a kérelem származik. Vegye figyelembe, hogy a kezdőpontnak pontos kis- és nagybetűket megkülönböztető egyezésnek kell lennie a felhasználó életkora által a szolgáltatásnak küldött forrással. A helyettesítő karakter "*" használatával is engedélyezheti, hogy az összes forrástartomány cors-on keresztül küldjön kéréseket. A fenti példában a http:\//www.contoso.com\/és http: /www.fabrikam.com tartományok kéréseket tehetnek a cors használatával a szolgáltatás ellen.
* **AllowedMethods**: Azok a metódusok (HTTP-kérési műveletek), amelyeket az eredeti tartomány használhat cors-kérelemhez. A fenti példában csak a PUT és a GET kérések engedélyezettek.
* **AllowedHeaders**: A kérelem fejlécek, hogy a forrás tartomány ban megadhatja a CORS kérelmet. A fenti példában az x-ms-meta-data, x-ms-meta-target és x-ms-meta-abc kezdetű összes metaadat-fejléc engedélyezett. Ne feledje, hogy a helyettesítő karakter "*" azt jelzi, hogy a megadott előtaggal kezdődő fejlécek engedélyezettek.
* **ExposedHeaders**: A válasz fejlécek, amelyek a CORS-kérésre adott válaszban elküldhetők, és amelyeket a böngésző a kérelem kibocsátójának elérhetővé tett. A fenti példában a böngésző arra utasítja, hogy tegye ki az x-ms-meta kezdetű fejléceket.
* **MaxAgeInSeconds**: Az a maximális időtartam, amerre a böngészőnek gyorsítótárazza az elővizsgálati BEÁLLÍTÁSOK kérelmet.

Az Azure storage-szolgáltatások támogatása prefix fejlécek megadása az **AllowedHeaders** és **a ExposedHeaders** elemekhez. A fejlécek kategóriájának engedélyezéséhez megadhat egy közös előtagot az adott kategóriához. Ha például *az x-ms-meta** előtaggal rendelkező fejlécként adja meg, akkor létrejön egy szabály, amely megfelel az x-ms-meta kezdetű összes fejlécnek.

A CORS-szabályokra a következő korlátozások vonatkoznak:

* Legfeljebb öt CORS-szabályt adhat meg tárolószolgáltatásonként (Blob, Table és Queue).
* A kérelemben lévő CORS-szabályok beállításainak maximális mérete az XML-címkék kivételével nem haladhatja meg a 2 KB-ot.
* Az engedélyezett fejlécek, a szabadon álló fejlécek vagy az engedélyezett origó késedelmi hossza nem haladhatja meg a 256 karaktert.
* Az engedélyezett fejlécek és a szabadon álló fejlécek a következők lehetnek:
  * Literális fejlécek, ahol a pontos fejlécnév van megadva, például **x-ms-meta-feldolgozott**. A kérésben legfeljebb 64 literális fejléc adható meg.
  * Előtagos fejlécek, ahol a fejléc előtagja van megadva, például **x-ms-meta-data***. Az előtag ily módon történő megadása lehetővé teszi vagy elérhetővé teszi az adott előtaggal kezdődő fejléceket. A kérelemben legfeljebb két előtaggal adható meg.
* Az **AllowedMethods** elemben megadott metódusok (vagy HTTP-műveletek) meg kell felelniük az Azure storage service API-k által támogatott metódusok. A támogatott módszerek a KÖVETKEZŐK: DELETE, GET, HEAD, MERGE, POST, OPTIONS és PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>A CORS-szabályértékelési logikájának ismertetése
Amikor egy tárolási szolgáltatás elővizsgálati vagy tényleges kérelmet kap, a kérést a szolgáltatáshoz a megfelelő Szolgáltatástulajdonságai beállítása műveleten keresztül létrehozott CORS-szabályok alapján értékeli ki. A CORS-szabályok kiértékelése abban a sorrendben történik, ahogyan a Szolgáltatás tulajdonságainak beállítása művelet kérelemtörzsében be vannak állítva.

A CORS-szabályok értékelése a következőképpen történik:

1. Először a kérelem kezdőtartományát a rendszer összeveti az AllowedOrigins elemben felsorolt **tartományokkal.** Ha az eredeti tartomány szerepel a listában, vagy az összes tartomány a "*" helyettesítő karakterrel engedélyezett, akkor a szabályok kiértékelése folytatódik. Ha az eredeti tartomány nem szerepel, akkor a kérelem sikertelen lesz.
2. Ezután a kérelem metódusát (vagy HTTP-műveletét) a rendszer összeveti az **AllowedMethods** elemben felsorolt metódusokkal. Ha a módszer szerepel a listában, akkor a szabályok értékelése folytatódik; ha nem, akkor a kérés sikertelen lesz.
3. Ha a kérelem megegyezik egy szabály a származási tartomány és a módszer, hogy a szabály van kiválasztva a kérelem feldolgozásához, és nincs további szabályok kiértékelése. A kérelem sikeresse, azonban a kérelemben megadott fejlécek összevethető az AllowedHeaders elemben felsorolt **fejlécekkel.** Ha az elküldött fejlécek nem egyeznek meg az engedélyezett fejlécekkel, a kérés sikertelen lesz.

Mivel a szabályok feldolgozása abban a sorrendben történik, ahogy a kérelemtörzsben szerepelnek, ajánlott eljárások azt javasoljuk, hogy a lista első részében adja meg a legszigorúbb szabályokat az eredetre vonatkozóan, hogy ezeket a rendszer először értékelje ki. A lista végén adja meg a kevésbé korlátozó szabályokat – például az összes eredetet engedélyező szabályt.

### <a name="example--cors-rules-evaluation"></a>Példa – CORS-szabályok értékelése
A következő példa egy részleges kérelem törzsegy művelet et a tárolási szolgáltatások cors-szabályok beállítására. A kérelem létrehozásáról a [Blobszolgáltatás tulajdonságainak beállítása,](https://msdn.microsoft.com/library/hh452235.aspx) [a Várólista-szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452232.aspx)és a [Táblaszolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452240.aspx) című témakörben talál.

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

Ezután vegye figyelembe a következő CORS-kérelmeket:

| Kérés |  |  | Válasz |  |
| --- | --- | --- | --- | --- |
| **Módszer** |**Forrás** |**Kérelemfejlécek** |**Szabály egyezése** |**Eredmény** |
| **Tesz** |http:\//www.contoso.com |x-ms-blob-content-type |Első szabály |Sikeres |
| **Kap** |http:\//www.contoso.com |x-ms-blob-content-type |Második szabály |Sikeres |
| **Kap** |http:\//www.contoso.com |x-ms-client-request-id |Második szabály |Hiba |

Az első kérelem megegyezik az első szabállyal – az eredettartomány megegyezik az engedélyezett forrásokkal, a metódus megfelel az engedélyezett metódusokkal, és a fejléc megegyezik az engedélyezett fejlécekkel –, így sikeres.

A második kérelem nem felel meg az első szabálynak, mert a metódus nem felel meg az engedélyezett metódusok. Ez azonban megfelel a második szabálynak, így sikerül.

A harmadik kérelem megegyezik a második szabály a származási tartomány és a módszer, így nincs további szabályok kiértékelése. Az *x-ms-client-request-id fejlécet* azonban a második szabály nem engedélyezi, így a kérés sikertelen lesz, annak ellenére, hogy a harmadik szabály szemantikája lehetővé tette volna, hogy sikeres legyen.

> [!NOTE]
> Bár ez a példa egy kevésbé korlátozó szabályt mutat be egy szigorúbb szabály előtt, általában az ajánlott eljárás a legszigorúbb szabályok felsorolása.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>A Vary fejléc beállításának ismertetése
A *Vary* fejléc egy szabványos HTTP/1.1 fejléc, amely egy kérelemfejléc-mezőkészletből áll, amely tájékoztatja a böngészőt vagy a felhasználói ügynököt a kiszolgáló által a kérelem feldolgozásához kiválasztott feltételekről. A *Vary* fejlécet elsősorban proxyk, böngészők és CDN-ek gyorsítótárazására használják, amelyek segítségével meghatározható, hogy a válasz hogyan legyen gyorsítótárazva. További információt a Vary [fejléc specifikációja tartalmaz.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)

Amikor a böngésző vagy egy másik felhasználói ügynök gyorsítótárazza a CORS-kérelemből érkező választ, a forrástartomány lesz az engedélyezett forrás. Amikor egy második tartomány ugyanazt a kérelmet ad ki egy tárolóerőforrásra, miközben a gyorsítótár aktív, a felhasználói ügynök lekéri a gyorsítótárazott forrástartományt. A második tartomány nem egyezik meg a gyorsítótárazott tartománnyal, így a kérés sikertelen lesz, ha egyébként sikerülne. Bizonyos esetekben az Azure Storage a Vary fejlécet az **Originre** állítja, hogy utasítsa a felhasználói ügynököt, hogy küldje el a következő CORS-kérelmet a szolgáltatásnak, ha a kérelmező tartomány eltér a gyorsítótárazott eredetűtől.

Az Azure Storage a Következő esetekben állítja be a *Vary* fejlécet az **Originre** a tényleges GET/HEAD kérelmekhez:

* Ha a kérelem eredete pontosan megegyezik a CORS-szabály által meghatározott engedélyezett forrással. Ahhoz, hogy pontos egyezés legyen, a CORS-szabály nem tartalmazhat helyettesítő karaktert * ' karaktert.
* Nincs olyan szabály, amely megfelelne a kérelem eredetének, de a CORS engedélyezve van a storage szolgáltatásszámára.

Abban az esetben, ha egy GET/HEAD kérelem megegyezik egy CORS-szabály, amely lehetővé teszi az összes eredete, a válasz azt jelzi, hogy minden eredete engedélyezett, és a felhasználói ügynök gyorsítótár lehetővé teszi a további kérelmeket bármely forrástartományból, amíg a gyorsítótár aktív.

Vegye figyelembe, hogy a GET/HEAD metódustól eltérő módszereket használó kérelmek esetén a tárolási szolgáltatások nem állítja be a Vary fejlécet, mivel az ezekre a módszerekre adott válaszokat a felhasználói ügynökök nem gyorsítótárazza.

Az alábbi táblázat azt mutatja be, hogy az Azure Storage hogyan fog reagálni a GET/HEAD kérelmekre a korábban említett esetek alapján:

| Kérés | A számla beállítása és a szabálykiértékelés eredménye |  |  | Válasz |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| **A forrásfejléc kérésre jelen van** |**EHHEZ A szolgáltatáshoz megadott CORS-szabály(ok)** |**Egyező szabály létezik, amely lehetővé teszi az összes origins(*)** |**A pontos eredetegyezéshez egyezési szabály létezik.** |**A válasz tartalmazza az Originre beállított Vary fejlécet** |**A válasz tartalmazza az Access-Control-Allowed-Origin-t: "*"** |**A válasz tartalmazza az access-control-exposed-headers fejléceket** |
| Nem |Nem |Nem |Nem |Nem |Nem |Nem |
| Nem |Igen |Nem |Nem |Igen |Nem |Nem |
| Nem |Igen |Igen |Nem |Nem |Igen |Igen |
| Igen |Nem |Nem |Nem |Nem |Nem |Nem |
| Igen |Igen |Nem |Igen |Igen |Nem |Igen |
| Igen |Igen |Nem |Nem |Igen |Nem |Nem |
| Igen |Igen |Igen |Nem |Nem |Igen |Igen |

## <a name="billing-for-cors-requests"></a>CorS-kérelmek számlázása
A sikeres elővizsgálati kérelmek számlázása akkor történik meg, ha engedélyezte a CORS szolgáltatást a fiókjához (a [Blob szolgáltatás tulajdonságainak beállítása,](https://msdn.microsoft.com/library/hh452235.aspx) [a Várólistaszolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452232.aspx)vagy a [Táblaszolgáltatás tulajdonságainak beállítása)](https://msdn.microsoft.com/library/hh452240.aspx)hívásával. A díjak minimalizálása érdekében fontolja meg a CORS-szabályok **MaxAgeInSeconds** elemének nagy értékre állítását, hogy a felhasználói ügynök gyorsítótárazza a kérelmet.

A sikertelen elővizsgálati kérelmeket nem számlázjuk ki.

## <a name="next-steps"></a>További lépések
[Blob szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452235.aspx)

[Várólista-szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452232.aspx)

[Táblaszolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C több forrásból származó erőforrás-megosztási specifikáció](https://www.w3.org/TR/cors/)

