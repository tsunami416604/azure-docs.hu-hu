---
title: Monitor többlépéses webes tesztekkel – Azure Application Insights
description: Többlépéses webes tesztek beállítása a webes alkalmazások figyelésére az Azure Application Insights segítségével
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: sdash
ms.openlocfilehash: 3b8baad127b16a1bd9d071d0c3d4df68da8c3304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655940"
---
# <a name="multi-step-web-tests"></a>Többlépéses webes tesztek

Többlépéses webes teszteken keresztül figyelheti a webhelyekkel folytatott URL-ek és interakciók rögzített sorozatát. Ez a cikk végigvezeti a Visual Studio Enterprise többlépéses webes tesztjének létrehozásán.

> [!NOTE]
> A többlépéses webes tesztek a Visual Studio webtesztfájljaitól függenek. [Bejelentették,](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) hogy a Visual Studio 2019 lesz az utolsó verzió webtest funkcióval. Fontos megérteni, hogy bár nem lesznek új funkciók, a Visual Studio 2019 webtest funkciói továbbra is támogatottak, és a termék támogatási életciklusa során továbbra is támogatottak lesznek. Az Azure Monitor termékcsapata [itt](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)foglalkozott a többlépéses rendelkezésre állási tesztek jövőjével kapcsolatos kérdésekkel.  

## <a name="pre-requisites"></a>Előfeltételek

* Visual Studio 2017 Enterprise vagy nagyobb.
* Visual Studio webes teljesítmény- és betöltés-tesztelési eszközök.

A tesztelési eszközök előzetes megkeresése. Indítsa el a **Visual Studio Installer** > **egyedi összetevőit** > a**webes teljesítmény- és betöltési tesztelési eszközök****hibakeresésére** > és tesztelésére.

![Képernyőkép a Visual Studio telepítőjének felhasználói felületéről, amelyen az egyes összetevők ki vannak jelölve, az elem melletti jelölőnégyzettel együtt a webes teljesítmény- és betöltés-tesztelési eszközökhöz](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> A többlépéses webes tesztek további költségekkel járnak. Ha többet szeretne megtudni, olvassa el a [hivatalos árképzési útmutatót](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Többlépéses webes teszt rögzítése 

> [!WARNING]
> A továbbiakban nem javasoljuk a többlépéses felvevő használatát. A felvevőt statikus HTML oldalakhoz fejlesztették ki, alapvető interakciókkal, és nem nyújt funkcionális élményt a modern weboldalakszámára.

A Visual Studio webes tesztjeinek létrehozásával kapcsolatos útmutatásért tekintse meg a [Visual Studio 2019 hivatalos dokumentációját.](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019)

## <a name="upload-the-web-test"></a>A webes teszt feltöltése

1. Az Alkalmazáselemzési portálon az Elérhetőség ablaktáblán válassza a**Teszttípus** >  **Create Test** > létrehozása**többlépéses webes teszt lehetőséget.**

2. Állítsa be a teszthelyeket, a gyakoriságot és a riasztási paramétereket.

### <a name="frequency--location"></a>A & helye gyakorisága

|Beállítás| Magyarázat
|----|----|----|
|**A vizsgálat gyakorisága**| Beállítja, hogy a teszt milyen gyakran fusson az egyes teszthelyeken. Öt perces alapértelmezett gyakorisággal és öt teszthellyel a helyén átlagosan percenként egy teszt történik.|
|**Vizsgálati helyek**| Azok a helyek, ahonnan szervereink webkéréseket küldenek az Ön URL-jére. **Az ajánlott teszthelyek minimális száma öt,** hogy biztosítsuk, hogy meg tudja különböztetni a webhelyproblémáit a hálózati problémáktól. Legfeljebb 16 hely választható ki.

### <a name="success-criteria"></a>Sikerességi feltételek

|Beállítás| Magyarázat
|----|----|----|
| **Teszt időmeghosszabbítása** |Csökkentse ezt az értéket, ha értesítést szeretne tenni a lassú válaszokról. A teszt akkor sikertelen, ha nem érkeznek meg a webhely válaszai ezen az időtartamon belül. Ha bejelölte a **Függő kérelmek elemzése** lehetőséget, akkor az összes képnek, stílusfájlnak, szkriptnek és más függő erőforrásnak meg kell érkeznie ezen az időn belül.|
| **HTTP-válasz** | A visszaadott állapotkód, amely sikeresnek számít. A 200-as kód jelzi, hogy normál weblap lett visszaküldve.|
| **Tartalomegyezés** | Egy madzag, mint a "Welcome!" Teszteljük, hogy minden válaszban előfordul-e a kis- és nagybetűket figyelembe véve is pontos egyezés. Egyszerű sztringnek kell lennie helyettesítő karakterek nélkül. Ne feledje, hogy ha a laptartalom megváltozik, lehet, hogy ezt is frissíteni kell. **A tartalomegyezéssel csak angol karaktereket támogat** |

### <a name="alerts"></a>Riasztások

|Beállítás| Magyarázat
|----|----|----|
|**Közel valós idejű (előzetes verzió)** | Közel valós idejű riasztások használatát javasoljuk. Az ilyen típusú riasztások konfigurálása a rendelkezésre állási teszt létrehozása után történik.  |
|**Klasszikus** | Már nem javasoljuk a klasszikus riasztások az új rendelkezésre állási tesztek használata.|
|**Riasztási hely küszöbértéke**|Javasoljuk, hogy legalább 3/5 helyen. A riasztási hely küszöbértéke és a teszthelyek száma közötti optimális kapcsolat a **riasztási hely küszöbértékének** = **száma – 2, legalább öt teszthellyel.**|

## <a name="configuration"></a>Konfiguráció

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Idő és véletlen számok csatlakoztatása a tesztbe

Tegyük fel, hogy egy olyan eszközt tesztel, amely időfüggő adatokat fogad, például részvényeket egy külső adatcsatornától. A webes teszt rögzítésekor meghatározott időpontokat kell használni, de ezeket a teszt StartTime és EndTime paramétereiként kell beállítani.

![Saját félelmetes állomány app screenshot](./media/availability-multistep/app-insights-72webtest-parameters.png)

A teszt futtatásakor az EndTime paraméternek mindig az aktuális időnek kell lennie, a StartTime paraméternek pedig a 15 perccel korábbi időnek.

A Webes teszt dátumidő beépülő modulja lehetővé teszi az időpontok paraméterezésének kezelését.

1. Adjon hozzá egy webes teszt beépülő modult minden kívánt változóparaméter-értékhez. A webes teszt eszköztárában válassza ki a **Webes teszt beépülő modul hozzáadása** elemet.
    
    ![Webes teszt beépülő modul hozzáadása](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    Ebben a példában a Dátum és idő beépülő modul két példányát használjuk. Az egyik példány a „15 perccel ezelőtt” a másik pedig a „most” paraméterértékhez tartozik.

2. Nyissa meg a beépülő modulok tulajdonságait. Adjon meg egy nevet, és állítsa be úgy, hogy az aktuális időt használja. Az egyiknél állítsa be a következőt: Add Minutes = -15.

    ![Környezeti paraméterek](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. A webes teszt paramétereiben a beépülő modulok nevére a {{plug-in name}} segítségével hivatkozzon.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Ezután töltse fel a tesztet a portálra. Ez a teszt minden futtatásánál a dinamikus értékeket fogja használni.

### <a name="dealing-with-sign-in"></a>A bejelentkezés kezelése

Ha a felhasználók bejelentkeznek az alkalmazásába, számos lehetősége van a bejelentkezés szimulálására a bejelentkezés után megjelenő lapok teszteléséhez. A használt megközelítés az alkalmazás által kínált biztonság típusától függ.

Minden esetben ajánlott létrehozni egy fiókot az alkalmazásában tesztelési célra. Ha lehetséges, korlátozza az engedélyt ezen a tesztfiókon, így elkerülheti, hogy a webes tesztek hatással legyenek a tényleges felhasználókra.

**Egyszerű felhasználónév és jelszó** Rögzítsen egy webes tesztet a szokásos módon. Először törölje a cookie-kat.

**SAML-hitelesítés**

|Tulajdonság neve| Leírás|
|----|-----|
| Közönség Uri | Az SAML-token közönség URI-ja.  Ez a hozzáférés-vezérlési szolgáltatás (ACS) URI-ja– beleértve az ACS-névteret és az állomásnevet. |
| Tanúsítvány jelszava | Az ügyféltanúsítvány jelszava, amely hozzáférést biztosít a beágyazott személyes kulcshoz. |
| Ügyféltanúsítvány  | Az ügyféltanúsítvány értéke a személyes kulccsal Base64 kódolású formátumban. |
| Névazonosító | A jogkivonat névazonosítója |
| Lejárati idő | Az az időtartam, amelyre a jogkivonat érvényes lesz.  Az alapértelmezett érték 5 perc. |
| Hatálybalépési idő | Az az időtartam, amelyre a múltban létrehozott token érvényes lesz (az időeltérések címzéséhez).  Az alapértelmezett érték (negatív) 5 perc. |
| Célkörnyezet paraméterének neve | A létrehozott állítást fogadó környezeti paraméter. |


**Ügyféltitok** Ha az alkalmazás rendelkezik egy bejelentkezési útvonal, amely magában foglalja az ügyfél titkos, használja ezt az útvonalat. Az Azure Active Directory (AAD) mint egy példa olyan szolgáltatásra, amely titkos ügyfélkulccsal történő bejelentkezést biztosít. Az AAD-ben a titkos ügyfélkulcs az alkalmazáskulcs.

Itt egy alkalmazáskulcsot használó Azure-webalkalmazás webes tesztelésre találhat példát:

![Képernyőkép-minta](./media/availability-multistep/client-secret.png)

Szerezze be a tokent az AAD-ből a titkos ügyfélkulcs használatával (AppKey).
Nyerje ki a tulajdonosi jogkivonatot a válaszból.
Az engedélyezési fejléc tulajdonosi jogkivonatával hívja meg az API-t.
Győződjön meg arról, hogy a webes teszt egy tényleges ügyfél - azaz saját alkalmazással rendelkezik az AAD-ben -, és használja az ügyfélazonosító + alkalmazáskulcsát. A teszt alatt álló szolgáltatás saját alkalmazással is rendelkezik az AAD-ben: az alkalmazás appID URI-ja tükröződik az erőforrásmező webes tesztjében.

### <a name="open-authentication"></a>Nyílt hitelesítés
Nyílt hitelesítés például a Microsoft- vagy Google-fiókkal történő bejelentkezés. Számos OAuth protokollt használó alkalmazás biztosítja a titkos ügyfélkódos alternatívát, így az első feladat ennek a lehetőségnek a megvizsgálása.

Ha a tesztnek az OAuth protokollal kell bejelentkeznie, az általános megközelítés a következő:

Használjon egy eszközt, például a Fiddlert a webböngésző, a hitelesítési hely és az alkalmazás közötti forgalom vizsgálatához.
Jelentkezzen be legalább két alkalommal különböző gépek vagy böngészők használatával, illetve hosszú időközönként (hogy a tokennek legyen ideje lejárni).
A különböző munkamenetek összehasonlításával azonosítsa a hitelesítési helyről visszaadott tokent, amelyet a rendszer a bejelentkezést követően továbbad az alkalmazáskiszolgálónak.
Rögzítsen egy webes tesztet a Visual Studióval.
Paraméterezze a tokeneket. Ehhez állítsa be a paramétereket, amikor a hitelesítő visszaküldi a tokent, és használja a webhely felé indított lekérdezésben. (A Visual Studio megpróbálja paraméterezni a tesztet, de a tokeneket nem paraméterezi megfelelően.)

## <a name="troubleshooting"></a>Hibaelhárítás

Dedikált [hibaelhárítási cikk](troubleshoot-availability.md).

## <a name="next-steps"></a>További lépések

* [Rendelkezésre állási riasztások](availability-alerts.md)
* [Url ping webes tesztek](monitor-web-app-availability.md)
