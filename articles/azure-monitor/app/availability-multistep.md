---
title: Többlépéses webes teszt a webalkalmazás és az Azure Application Insights figyelése |} A Microsoft Docs
description: Az Azure Application Insights webes alkalmazások monitorozására való telepítési többlépéses webes tesztek
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d8bfe92af4e8afc4edae76efb2e1cb7b287c7aa9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305051"
---
# <a name="multi-step-web-tests"></a>Többlépéses webes tesztek

Figyelheti az URL egy rögzített feladatütemezési és a egy webhelyet a többlépéses webes tesztek keresztül folytatott interakciót. Ez a cikk részletesen ismerteti a folyamatot, amely egy többlépéses webes teszt létrehozása a Visual Studio Enterprise segítségével.

> [!NOTE]
> Többlépéses webes tesztek rendelkezik hozzájuk társított további költség. További információt a további részletekért a [hivatalos díjszabási útmutató](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="pre-requisites"></a>Előfeltételek

* A Visual Studio 2017 Enterprise vagy nagyobb.
* A Visual Studio webes teljesítmény- és terheléses tesztelési eszközök.

Keresse meg a tesztelési eszközök előfeltételt. Indítsa el a **Visual Studio telepítőjének** > **az egyes összetevők** > **Hibakeresés és tesztelés**  >   **Webes teljesítmény és a tesztelési eszközök betöltése**.

![Az egyes összetevők a cikk a webes teljesítmény és a terheléses tesztelési eszközök mellett egy jelölőnégyzet kijelölve a Visual Studio telepítőjének felhasználói felület képernyőképe](./media/availability-multistep/web-performance-load-testing.png)

## <a name="record-a-multi-step-web-test"></a>Jegyezze fel a többlépéses webes teszt

Többlépéses teszt létrehozásához rögzíteni kell a forgatókönyvet a Visual Studio Enterprise segítségével, majd fel kell tölteni a felvételt az Application Insights-ba. Az Application Insights visszajátssza a forgatókönyvet a megadott időközönként, és ellenőrzi a választ.

> [!IMPORTANT]
> * A tesztekben nem használhat kódolt függvényeket vagy hurkokat. A .webtest szkriptnek teljes egészében tartalmaznia kell a tesztet. Ehelyett azonban standard beépülő modulok is használhatók.
> * Kizárólag angol karakterek támogatottak a többlépéses webes tesztekben. Ha más nyelven használja a Visual Studiót, kérjük, frissítse a webes teszt definíciófájlját a nem angol karakterek fordításához/kihagyásához.

A webes munkamenet rögzítéséhez használja a Visual Studio Enterprise-t.

1. Hozzon létre egy webes teljesítmény- és terheléses tesztelési projekteket. **Fájl** > **új** > **projekt** > **Visual C#**   >  **teszt**

    ![A Visual Studio új projekt felhasználói felület](./media/availability-multistep/vs-web-performance-and-load-test.png)

2. Nyissa meg a `.webtest` fájlt, és a felvétel indításához.

    ![Felvétel a felhasználói felület a Visual Studio test](./media/availability-multistep/open-web-test.png)

3. Kattintson végig a lépéseken, azt szeretné, hogy a teszt részeként a felvétel szimulálásához.

    ![Böngészőrögzítés felhasználói felület](./media/availability-multistep/record.png)

4. Szerkessze a tesztet:

    * Adjon hozzá ellenőrzéseket a kapott szöveg- és válaszkódok ellenőrzéséhez.
    * Távolítsa el a uneccesary kölcsönhatások. Sikerült függő kérelmek képek eltávolítása, vagy adja hozzá a nyomon követési helyeket, amelyek nem releváns, figyelembe véve a teszt sikeres.
    
    Ne feledje, hogy csak a teszt szkriptjét szerkesztheti – egyéni kód hozzáadása, vagy más webes tesztekre hívja. Ne szúrjon be hurkokat a tesztbe. Használhatja a normál webes teszt beépülő modulokat.

5. Futtassa a tesztet a Visual Studióban ellenőrizze, és ellenőrizze, hogy működik-e.

    A webes teszt futtatója megnyit egy webböngészőt, és megismétli a rögzített műveleteket. Győződjön meg arról, hogy minden a várt módon viselkedik.

## <a name="upload-the-web-test"></a>A webes teszt feltöltése

1. Az Application Insights portálon, a rendelkezésre állási panelen válassza ki a **teszteléséhez hozzon létre** > **teszt típusa** > **többlépéses webes teszt**.

2. Állítsa be a teszt, gyakoriságát és riasztási paramétereit.

### <a name="frequency--location"></a>Gyakoriság & hely

|Beállítás| Magyarázat
|----|----|----|
|**Teszt gyakorisága**| Megadja, hogy milyen gyakran fut a teszt mindegyik teszthelyen. Öt perces alapértelmezett gyakorisággal és öt teszthellyel a helyén átlagosan percenként egy teszt történik.|
|**Teszthelyek**| Azok a területek, ahol kiszolgálóink webes kéréseket küldjön az URL-cím a. **Az ajánlott teszthelyszín minimális száma az öt** biztosítására, hogy megkülönböztethesse problémák webhelye a hálózati hibáktól. Legfeljebb 16 hely választható ki.

### <a name="success-criteria"></a>Sikeresség feltétele

|Beállítás| Magyarázat
|----|----|----|
| **Teszt időkorlátja** |Csökkenti az értéket, riasztást kap a lassú válaszokról. A teszt akkor sikertelen, ha nem érkeznek meg a webhely válaszai ezen az időtartamon belül. Ha bejelölte a **Függő kérelmek elemzése** lehetőséget, akkor az összes képnek, stílusfájlnak, szkriptnek és más függő erőforrásnak meg kell érkeznie ezen az időn belül.|
| **HTTP-válasz** | Akkor számít sikeres, a visszaadott állapotkód. A 200-as kód jelzi, hogy normál weblap lett visszaküldve.|
| **Tartalmi egyezés** | Egy karakterlánc, például "Üdvözöljük!" Teszteljük, hogy minden válaszban előfordul-e a kis- és nagybetűket figyelembe véve is pontos egyezés. Egyszerű sztringnek kell lennie helyettesítő karakterek nélkül. Ne feledje, hogy ha a laptartalom megváltozik, lehet, hogy ezt is frissíteni kell. **Tartalmi egyezés kizárólag angol karakterek támogatottak** |

### <a name="alerts"></a>Riasztások

|Beállítás| Magyarázat
|----|----|----|
|**Közel valós idejű (előzetes verzió)** | Közel valós idejű riasztások használatát javasoljuk. Ilyen típusú riasztás konfigurálása történik, a rendelkezésre állási teszt létrehozása után.  |
|**Klasszikus** | Már nem javasolt az új rendelkezésre állási teszt klasszikus riasztások használatával.|
|**Riasztási hely küszöbértéke**|Ajánlott legalább 3 vagy 5 helyeket. Az optimális riasztási hely küszöbértéke és tesztelési helyek közötti kapcsolat **riasztási hely küszöbértéke** = **legalább öt teszthelyszín teszthelyszín - 2, száma.**|

## <a name="advanced-configuration"></a>Speciális konfiguráció

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Idő és véletlenszerű számok csatlakoztatja azokat a tesztet

Tegyük fel, hogy egy olyan eszközt tesztel, amely időfüggő adatokat fogad, például részvényeket egy külső adatcsatornától. A webes teszt rögzítésekor meghatározott időpontokat kell használni, de ezeket a teszt StartTime és EndTime paramétereiként kell beállítani.

![Saját Soft tőzsdei alkalmazás képernyőképe](./media/availability-multistep/app-insights-72webtest-parameters.png)

A teszt futtatásakor az EndTime paraméternek mindig az aktuális időnek kell lennie, a StartTime paraméternek pedig a 15 perccel korábbi időnek.

A webes teszt dátum idő beépülő modul kezelni a megoldást kínál alkalommal paraméterezni.

1. Adjon hozzá egy webes teszt beépülő modult minden kívánt változóparaméter-értékhez. A webes teszt eszköztárában válassza ki a **Webes teszt beépülő modul hozzáadása** elemet.
    
    ![Webes teszt beépülő modul hozzáadása](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    Ebben a példában a Dátum és idő beépülő modul két példányát használjuk. Az egyik példány a „15 perccel ezelőtt” a másik pedig a „most” paraméterértékhez tartozik.

2. Nyissa meg a beépülő modulok tulajdonságait. Adjon meg egy nevet, és állítsa be úgy, hogy az aktuális időt használja. Az egyiknél állítsa be a következőt: Add Minutes = -15.

    ![Környezet paraméterek](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. A webes teszt paramétereiben a beépülő modulok nevére a {{plug-in name}} segítségével hivatkozzon.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Ezután töltse fel a tesztet a portálra. Ez a teszt minden futtatásánál a dinamikus értékeket fogja használni.

### <a name="dealing-with-sign-in"></a>A bejelentkezés kezelése

Ha a felhasználók bejelentkeznek az alkalmazásába, számos lehetősége van a bejelentkezés szimulálására a bejelentkezés után megjelenő lapok teszteléséhez. A használt megközelítés az alkalmazás által kínált biztonság típusától függ.

Minden esetben ajánlott létrehozni egy fiókot az alkalmazásában tesztelési célra. Ha lehetséges, korlátozza az engedélyt ezen a tesztfiókon, így elkerülheti, hogy a webes tesztek hatással legyenek a tényleges felhasználókra.

**Egyszerű felhasználónév és jelszó** rögzítsen egy webes tesztet a szokásos módon. Először törölje a cookie-kat.

**SAML-hitelesítés** a webes tesztek számára elérhető SAML beépített modult használja. A beépülő modul által eléréséhez...

**Titkos Ügyfélkód** Ha az alkalmazás egy bejelentkezési módja titkos ügyfélkódot, használja azt a módot. Az Azure Active Directory (AAD) mint egy példa olyan szolgáltatásra, amely titkos ügyfélkulccsal történő bejelentkezést biztosít. Az AAD-ben a titkos ügyfélkulcs az alkalmazáskulcs.

Itt egy alkalmazáskulcsot használó Azure-webalkalmazás webes tesztelésre találhat példát:

![Minta képernyőképe](./media/availability-multistep/client-secret.png)

Szerezze be a tokent az AAD-ből a titkos ügyfélkulcs használatával (AppKey).
Nyerje ki a tulajdonosi jogkivonatot a válaszból.
Az engedélyezési fejléc tulajdonosi jogkivonatával hívja meg az API-t.
Győződjön meg arról, hogy a webes teszt egy tényleges ügyfél –, hogy van saját alkalmazással az aad-ben –, és használja a clientId + -alkalmazás kulcsa. A teszt alatt álló szolgáltatása is rendelkezik saját alkalmazással az aad-ben: az alkalmazásazonosító URI-ját az alkalmazás megjelenik a webes teszt, az erőforráscsoport mezőjében.

### <a name="open-authentication"></a>Nyílt hitelesítés
Nyílt hitelesítés például a Microsoft- vagy Google-fiókkal történő bejelentkezés. Számos OAuth protokollt használó alkalmazás biztosítja a titkos ügyfélkódos alternatívát, így az első feladat ennek a lehetőségnek a megvizsgálása.

Ha a tesztnek az OAuth protokollal kell bejelentkeznie, az általános megközelítés a következő:

Használjon egy eszközt, például a Fiddlert a webböngésző, a hitelesítési hely és az alkalmazás közötti forgalom vizsgálatához.
Jelentkezzen be legalább két alkalommal különböző gépek vagy böngészők használatával, illetve hosszú időközönként (hogy a tokennek legyen ideje lejárni).
A különböző munkamenetek összehasonlításával azonosítsa a hitelesítési helyről visszaadott tokent, amelyet a rendszer a bejelentkezést követően továbbad az alkalmazáskiszolgálónak.
Rögzítsen egy webes tesztet a Visual Studióval.
Paraméterezze a tokeneket. Ehhez állítsa be a paramétereket, amikor a hitelesítő visszaküldi a tokent, és használja a webhely felé indított lekérdezésben. (A Visual Studio megpróbálja paraméterezni a tesztet, de a tokeneket nem paraméterezi megfelelően.)

## <a name="troubleshooting"></a>Hibaelhárítás

Dedikált [hibaelhárításról szóló cikk](troubleshoot-availability.md).

## <a name="next-steps"></a>További lépések

* [Rendelkezésre állási riasztás](availability-alerts.md)
* [URL-ping webes tesztek](monitor-web-app-availability.md)
