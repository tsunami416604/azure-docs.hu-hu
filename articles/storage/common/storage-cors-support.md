---
title: Eltérő eredetű erőforrások megosztása (CORS) támogatása |} A Microsoft Docs
description: Ismerje meg a CORS-támogatásának engedélyezése a Microsoft Azure tárolási szolgáltatások.
services: storage
author: cbrooksmsft
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: cbrooks
ms.component: common
ms.openlocfilehash: fd5df50128885f6a96e68c8ad46204bc21d80264
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39531557"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Eltérő eredetű erőforrások megosztása (CORS) támogatása az Azure Storage szolgáltatások
2013-08-15 verzióval kezdve, az Azure storage szolgáltatások támogatja az eltérő eredetű erőforrások megosztása (CORS) a Blob, Table, Queue és fájl szolgáltatások. A CORS egy HTTP-funkció, amely lehetővé teszi egy adott tartományban futó webes alkalmazás egy másik tartományban lévő erőforrások eléréséhez. Webböngészők néven ismert biztonsági korlátozással akadályozzák meg [azonoseredet-](http://www.w3.org/Security/wiki/Same_Origin_Policy) , amely megakadályozza, hogy egy weblap, egy másik tartományban; API-k A CORS biztonságos megoldást nyújt, hogy egy tartomány (a forrástartomány) API-k meghívása egy másik tartományban található. Tekintse meg a [CORS-specifikáció](http://www.w3.org/TR/cors/) CORS részleteiért.

Beállíthatja a CORS-szabályok külön-külön az egyes, a storage-szolgáltatás, meghívásával [állítsa Blob szolgáltatás tulajdonságai](https://msdn.microsoft.com/library/hh452235.aspx), [állítsa be a Queue szolgáltatás tulajdonságai](https://msdn.microsoft.com/library/hh452232.aspx), és [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx). Miután beállította a CORS-szabályok a szolgáltatáshoz, majd más tartományokból a szolgáltatás megfelelő engedéllyel rendelkező kérelem kiértékelendő meghatározni, hogy engedélyezett-e a megadott szabályok alapján.

> [!NOTE]
> Vegye figyelembe, hogy a CORS nem olyan hitelesítési mechanizmust. Bármely kérelem, tároló egyik erőforrásához társzolgáltatásokhoz, ha a CORS engedélyezve van a megfelelő hitelesítés aláírás rendelkeznie, vagy egy nyilvános erőforráson kell tenni.
> 
> 

## <a name="understanding-cors-requests"></a>CORS-kérések ismertetése
A CORS-kéréshez egy forrástartomány a két külön kérelmet állhat:

* Előzetes kérése, amely lekérdezi a CORS a szolgáltatás korlátozásai. Az ellenőrzési kérés megadása kötelező, kivéve, ha a kérelmi metódust egy [egyszerű módszer](http://www.w3.org/TR/cors/), ami azt jelenti, GET, HEAD vagy POST.
* A tényleges kérést, szemben a kívánt erőforrást.

### <a name="preflight-request"></a>Ellenőrzési kérés
Az ellenőrzési kérés lekérdezések a CORS-korlátozásokat, amelyeket a storage szolgáltatás a fiók tulajdonosa által létesített. A böngésző (vagy más felhasználói ügynök) küld, amely tartalmazza a kérelemfejlécek, metódust, és a forrás tartományi OPTIONS-kérést. A storage szolgáltatás értékeli ki a kívánt műveletet adja meg, melyik tartományt, a kérelem metódusok és a kérelemfejlécek megadható egy storage-erőforrások egy tényleges kérelmet a CORS-szabályok egy előre konfigurált készlete alapján.

A CORS engedélyezve van a szolgáltatáshoz, és a CORS-szabály, amely megfelel az ellenőrzési kérelmet, ha a szolgáltatás reagál 200 (OK) állapotkódot, és tartalmazza a szükséges Access-Control fejléceket a válaszban.

Ha a szolgáltatás nincs engedélyezve a CORS, vagy a CORS-szabály sem illeszkedik az ellenőrzési kérelmet, a szolgáltatás válaszol 403 (tiltott) állapotkódot.

Ha a beállítások kérelem nem tartalmazza a szükséges CORS fejléceket (a forrás- és hozzáférés-vezérlési-kérelmi metódus fejlécek), a szolgáltatás 400 (hibás kérés) állapotkódot válaszol.

Vegye figyelembe, hogy egy ellenőrzési kérés abban az esetben a szolgáltatás (Blob, Queue és Table) és nem a kért erőforrás ellen. A fiók tulajdonosa engedélyeznie kell a CORS ahhoz, hogy a kérelem sikeres fióktulajdonságokat szolgáltatás részeként.

### <a name="actual-request"></a>Tényleges kérelem
Miután az ellenőrzési kérelem elfogadása és a válasz, a böngésző csatolva az a tárolási erőforrások a tényleges kérelmet. A böngésző megtagadja a tényleges azonnal kérése, ha az ellenőrzési kérés elutasítása.

A tényleges kérelem számít a storage szolgáltatás normál kérelmet. A forrás-fejléc jelzi, hogy a kérelem egy CORS-kérést, és a szolgáltatás ellenőrzi az egyező CORS-szabályokat. Ha van egyezés, az Access-Control fejléceket a válasz hozzá, és vissza az ügyfélnek küldött. Ha nincs egyezés, a hozzáférés-vezérlési CORS fejlécek nem adja vissza.

## <a name="enabling-cors-for-the-azure-storage-services"></a>A CORS engedélyezése az Azure Storage-szolgáltatásokban
CORS-szabályok a szolgáltatási szinten van beállítva, hogy engedélyezi vagy letiltja a CORS minden egyes szolgáltatás (Blob, Queue és Table) kell külön-külön. A CORS minden egyes szolgáltatás alapértelmezés szerint le van tiltva. Ahhoz, hogy a CORS, állítsa be a megfelelő szolgáltatási tulajdonságokat verziójával 2013-08-15 kell vagy újabb, és a CORS-szabályok hozzáadása a szolgáltatás tulajdonságait. Kapcsolatban, engedélyezni vagy letiltani a CORS egy szolgáltatás és a CORS-szabályok beállítása tekintse meg [állítsa Blob szolgáltatás tulajdonságai](https://msdn.microsoft.com/library/hh452235.aspx), [állítsa be a Queue szolgáltatás tulajdonságai](https://msdn.microsoft.com/library/hh452232.aspx), és [beállítása a Table Service Tulajdonságok](https://msdn.microsoft.com/library/hh452240.aspx).

Íme egy példa egy egyetlen CORS-szabály, egy szolgáltatás tulajdonságainak beállítása művelet keresztül megadott:

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

Minden elem szerepel a CORS-szabály az alábbiakban olvasható:

* **AllowedOrigins**: A tartományt, amely egy kérelmet a storage szolgáltatás CORS használatával engedélyezett. A forrástartomány az a tartomány, ahonnan a kérés származik. Vegye figyelembe, hogy a forrás a forrás, amely a felhasználó betöltötte a szolgáltatás elküldi a pontos kis-és nagybetűket egyeznie kell. A helyettesítő karakter is használható ' *', hogy minden eredettartományból kéréseit a CORS használatával. A fenti példában, a tartományok [ http://www.contoso.com ](http://www.contoso.com) és [ http://www.fabrikam.com ](http://www.fabrikam.com) teheti a CORS használatával szolgáltatásra irányuló kérések.
* **AllowedMethods**: metódusok (HTTP-kéréssel kapcsolatos műveletek), amely a forrástartomány használhat egy CORS-kéréshez. A fenti példában csak a PUT és a GET kérelmek engedélyezettek.
* **AllowedHeaders**: A kérelem fejlécében, hogy a forrástartomány előfordulhat, hogy a CORS-kéréshez adjon meg. A fenti példában x-ms-metaadatok, x-ms-meta-célként, és az x-ms-meta-abc kezdve minden metaadat fejlécek használata engedélyezett. Vegye figyelembe, hogy a helyettesítő karaktert ' *' jelzi, hogy engedélyezve van-e bármilyen fejlécet elején a megadott előtaggal.
* **ExposedHeaders**: válaszfejlécek, melyeket a CORS-kérelemre válaszul és amelyeket a böngésző megjeleníthet a kérelem kibocsátója. A fenti példában a böngésző mentenie bármely x-ms-metaadat-fejléc kezdődő elérhetővé.
* **MaxAgeInSeconds**: kérése legfeljebb ennyi ideig, hogy a böngésző gyorsítótárazza az előzetes OPTIONS.

Az Azure storage szolgáltatások esetében is támogatja a késleltetve előtaggal ellátott fejléc adható a **AllowedHeaders** és **ExposedHeaders** elemeket. Ahhoz, hogy egy kategóriát a fejlécek, egy adott kategória közös előtagot is megadhat. Például megadása *x-ms-meta** szerint előtaggal ellátott fejléc hoz létre egy szabályt, amely az x-ms-meta kezdődő összes fejléc esetében egyezést fog.

CORS-szabályok a következő korlátozások vonatkoznak:

* Storage-szolgáltatás (Blob, Table és Queue) legfeljebb öt CORS-szabályokat is megadhat.
* Az összes CORS-szabályok beállításait a kérés XML-címkék, kivéve a maximális mérete legfeljebb 2 KB.
* Egy engedélyezett fejléc, közzétett fejléc vagy engedélyezett származási hossza legfeljebb 256 karakter.
* Engedélyezett fejlécek és közzétett fejlécek lehetnek:
  * Szövegkonstans fejlécek, ahol a pontos fejlécnév áll rendelkezésre, mint például **x-ms-meta – feldolgozott**. Egy legfeljebb 64 szövegkonstans fejlécek a kérésben adható meg.
  * A fejlécek, ahol a fejléc előtag áll rendelkezésre, például a következő előtaggal ** x-ms-meta-data x. Ily módon előtag megadása lehetővé teszi, vagy tesz közzé bármilyen fejlécet, amely a megadott előtaggal kezdődik. Legfeljebb két előtaggal ellátott fejléc adható meg a kérés adható meg.
* A megadott metódusok (vagy HTTP-műveletek) a **AllowedMethods** elemet meg kell felelnie az Azure storage szolgáltatás API-k által támogatott módszerek. Támogatott módszerek állnak a DELETE, GET, HEAD, EGYESÍTÉS, POST, beállítások és PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>CORS-szabály kiértékelési logika ismertetése
A storage szolgáltatás előzetes vagy tényleges kérelmet kap, amikor a kérést a szolgáltatás a megfelelő szolgáltatás tulajdonságainak beállítása művelet keresztül létrehozta a CORS-szabályok alapján értékeli ki. CORS-szabályok, amelyben voltak beállítva a szolgáltatás tulajdonságainak beállítása művelet a kérelem törzsében szereplő sorrendben értékeli ki.

CORS-szabályok a következők szerint értékeli:

1. Először is be van jelölve a kérelem a forrástartomány ellen a felsorolt tartományok a **AllowedOrigins** elemet. Ha a forrástartomány szerepel a listában, vagy minden tartomány és a helyettesítő karakter engedélyezett "*", majd szabályok kiértékelése telepítése folytatódik. Ha a forrástartomány lehetőség nem része, akkor a kérelem sikertelen lesz.
2. Ezután a metódus (vagy a HTTP-műveletet) a kérés a rendszer összeveti a felsorolt módszerek a **AllowedMethods** elemet. Ha a metódus szerepel a listában, majd szabályok kiértékelése folytatódik; Ha nem, akkor a kérelem sikertelen lesz.
3. A kérelem megfelel valamelyik szabálynak a forrástartomány és a módszert, ha ez a szabály a kérelmet, és nincsenek további szabályok kiértékelése folyamatban van kiválasztva. Mielőtt a kérelem sikeres, azonban a kérésben megadott fejlécek veti össze a felsorolt fejlécek a **AllowedHeaders** elemet. Ha a küldött fejlécek nem egyezik az engedélyezett fejlécek, a kérelem sikertelen lesz.

Mivel ezek meg adva, a kérelem törzsében szereplő sorrendben dolgozzák fel a szabályokat, a bevált gyakorlat, hogy a legszigorúbb szabályokat megállapodást források először meg a listában, hogy ezek a először értékeli. Adja meg a szabályokat, amelyek kevésbé korlátozó – például egy szabályt, amely engedélyezi az összes forrás – a lista végén.

### <a name="example--cors-rules-evaluation"></a>Példa – CORS-szabályok kiértékelése
Az alábbi példa bemutatja a tárolási szolgáltatások CORS-szabályainak beállítása a művelet egy részleges kérelem törzsében. Lásd: [állítsa Blob szolgáltatás tulajdonságai](https://msdn.microsoft.com/library/hh452235.aspx), [állítsa be a Queue szolgáltatás tulajdonságai](https://msdn.microsoft.com/library/hh452232.aspx), és [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx) hozhat létre, a kérelem részleteiért.

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

Ezután vegye figyelembe a következő CORS-kérések:

| Kérés |  |  | Válasz |  |
| --- | --- | --- | --- | --- |
| **Metódus** |**Forrás** |**Kérelem fejlécei** |**A szabály egyezés** |**Eredmény** |
| **PUT** |http://www.contoso.com |x-ms-blob-content-type |Első szabály |Sikeres |
| **GET** |http://www.contoso.com |x-ms-blob-content-type |Második szabály |Sikeres |
| **GET** |http://www.contoso.com |x-ms-client-request-id |Második szabály |Hiba |

Az első kérelem megegyezik az első szabály – a forrástartomány megegyezik az engedélyezett eredetek, a metódus felel meg az engedélyezett metódusok és a fejléc megegyezik az engedélyezett fejlécek – és így sikeres.

A második kérés nem egyezik az első szabály, mert a módszer nem egyezik meg az engedélyezett metódusok. Azt, azonban felel meg a második szabálynak, így azt.

A harmadik kérelem megegyezik a forrástartomány és módot, a második szabálynak, így nincsenek további szabályok kiértékelése. Azonban a *x-ms-client-request-id fejléccel* nem engedélyezi a második szabálynak, ezért a kérelem meghiúsul, annak ellenére, hogy a harmadik szabály szemantikáját használhatott volna sikeres.

> [!NOTE]
> Bár ez a példa bemutatja egy kevésbé korlátozó szabály szigorúbb egy előtt, általánosságban az ajánlott eljárás az első listában a legszigorúbb szabályokat.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Hogyan van beállítva a változtatás fejléc ismertetése
A *változtatás* fejléc kérés üzenetfejlécének mezői, amely a böngészőben vagy a felhasználói ügynök a feltételeket, amelyek a kérelem feldolgozását a kiszolgáló által kiválasztott kapcsolatos tanácsadás, amely szabványos HTTP/1.1 fejléc. A *változtatás* fejléc főleg proxyk, a böngésző és a CDN, amelyek segítségével határozza meg, hogyan érdemes gyorsítótárazni a válasz-gyorsítótárazás használható. További információkért lásd: a-specifikációt az [változtatás fejléc](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Ha a böngészőben vagy egy másik felhasználói ügynök gyorsítótárazza a CORS-kérést kapott választ, a forrástartomány engedélyezett származási gyorsítótárazza. Ha egy másik tartomány kiad egy tárolási erőforrás a kérésben, amíg a gyorsítótár nem aktív, a felhasználói ügynök a gyorsítótárazott forrástartomány kérdezi le. A második tartomány nem felel meg a gyorsítótárazott tartományban, ezért a kérelem meghiúsul, ha egyébként járnak. Bizonyos esetekben az Azure Storage állítja be a változtatás fejléc **forrás** arra utasítani a felhasználói ügynököt az ezt követő CORS kérést küldhet a szolgáltatás, ha a kérelmező tartomány eltér a gyorsítótárazott forrás.

Az Azure Storage-csoportok a *változtatás* fejlécet **forrás** tényleges GET/HEAD-kérések a következő esetekben:

* Ha a kérelem származási pontosan megegyezik a CORS-szabály által meghatározott engedélyezett forrás. Pontosan egyeznie kell a CORS-szabály nem feltétlenül tartalmazzák a helyettesítő karakter "*" karaktert.
* Nincs szabály, a kérelem származási megfelelő, de a CORS engedélyezve van a storage szolgáltatás.

A helyzet, ha a GET/HEAD-kérések megegyezik a CORS-szabály, amely lehetővé teszi az összes forrás a válasz azt jelzi, hogy minden eredet engedélyezett, és a felhasználói ügynök gyorsítótárának lehetővé teszi bármely forrástartomány érkező későbbi kérelmeket, amíg a gyorsítótár nem aktív.

Vegye figyelembe, hogy lépéseire GET/HEAD-kérések számára, a tárolási szolgáltatások nincs beállítva a változtatás fejléc, mivel felhasználói ügynökök által nem gyorsítótárazott válaszokat ezen metódusok meghívásához.

Az alábbi táblázat azt jelzi, hogy az Azure storage GET/HEAD-kérések a korábban említett esetek alapján fog válaszolni:

| Kérés | Fiók beállítását és a szabály kiértékelés eredménye |  |  | Válasz |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Jelen a kérelem származási fejléc** |**Ez a szolgáltatás számára megadott CORS-szabály** |**Egyeztetési szabályt, amely lehetővé teszi az összes origins(*) létezik** |**Egyező szabálya létezik forrás pontos egyezés** |**Válasz tartalmazza a változtatás fejléc forrás beállítása** |**Válasz tartalmazza a hozzáférés-vezérlés – engedélyezett-forrás: "*"** |**Válasz tartalmazza a hozzáférés-vezérlési-elérhetővé tett – fejlécek** |
| Nem |Nem |Nem |Nem |Nem |Nem |Nem |
| Nem |Igen |Nem |Nem |Igen |Nem |Nem |
| Nem |Igen |Igen |Nem |Nem |Igen |Igen |
| Igen |Nem |Nem |Nem |Nem |Nem |Nem |
| Igen |Igen |Nem |Igen |Igen |Nem |Igen |
| Igen |Igen |Nem |Nem |Igen |Nem |Nem |
| Igen |Igen |Igen |Nem |Nem |Igen |Igen |

## <a name="billing-for-cors-requests"></a>CORS-kérések díjszabása
Sikeres ellenőrzés kérelmek számítjuk fel, ha engedélyezte a tárolási szolgáltatások, a fiók CORS (meghívásával [állítsa Blob szolgáltatás tulajdonságai](https://msdn.microsoft.com/library/hh452235.aspx), [állítsa be a Queue szolgáltatás tulajdonságai](https://msdn.microsoft.com/library/hh452232.aspx), vagy [Table Service tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452240.aspx)). Költségek minimalizálása érdekében érdemes lehet a **MaxAgeInSeconds** eleme a CORS szabályok nagy értéket úgy, hogy a felhasználói ügynök gyorsítótárazza a kérelmet.

Sikertelen ellenőrzés kérelmek nem számítjuk fel.

## <a name="next-steps"></a>További lépések
[Blob szolgáltatás tulajdonságainak megadása](https://msdn.microsoft.com/library/hh452235.aspx)

[Queue szolgáltatás tulajdonságainak beállítása](https://msdn.microsoft.com/library/hh452232.aspx)

[Table Service tulajdonságainak megadása](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C eltérő eredetű erőforrások megosztása specifikáció](http://www.w3.org/TR/cors/)

