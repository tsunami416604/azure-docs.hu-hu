---
title: Azure-alkalmazás konfigurációs kulcs-érték tárolója | Microsoft Docs
description: A konfigurációs információk Azure-alkalmazás konfigurációban való tárolásának áttekintése
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 054de294c9edb0fe5b75da4ac7fd54ce987123de
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185221"
---
# <a name="keys-and-values"></a>Kulcsok és értékek

Az Azure-alkalmazás konfigurációja kulcs-érték párokként tárolja a konfigurációs értékeket. A kulcs-érték párok egyszerű, de rugalmas módon jelenthetik a fejlesztők által ismert különböző típusú alkalmazások beállításait.

## <a name="keys"></a>Kulcsok

A kulcsok a kulcs-érték párok neveként szolgálnak, és a megfelelő értékek tárolására és lekérésére használatosak. Gyakori eljárás a kulcsok hierarchikus névtérbe rendezése egy karakter elválasztójának használatával, például `/` vagy `:`. Használjon az alkalmazáshoz legmegfelelőbb konvenciót. Az alkalmazás konfigurációja a kulcsokat teljes egészében kezeli. Nem elemzi a kulcsokat, hogy megtudja, hogyan épülnek fel a nevük, és hogyan kényszerítenek rájuk.

A konfigurációs adatok alkalmazás-keretrendszereken belüli használata bizonyos elnevezési sémákat határozhat meg a kulcsok értékeihez. A Java Spring Cloud Framework például olyan `Environment` erőforrásokat határoz meg, amelyek az *alkalmazás nevét* és *profilját*tartalmazó változók paramétereit biztosítják a rugós alkalmazás beállításainak. A tavaszi felhőhöz kapcsolódó konfigurációs adatok kulcsai általában a két elem elválasztó karakterrel kezdődnek.

Az alkalmazás konfigurációjában tárolt kulcsok kis-és nagybetűket megkülönböztető, Unicode-alapú karakterláncok. Az *App1* és a *App1* kulcsok eltérnek az alkalmazás konfigurációs tárolójában. Ne feledje, hogy ha a konfigurációs beállításokat egy alkalmazáson belül használja, mert egyes keretrendszerek kis-és nagybetűk nélkül kezelik a konfigurációs kulcsokat. Például a ASP.NET Core konfigurációs rendszer a kulcsokat kis-és nagybetűket nem megkülönböztető karakterláncként kezeli. Ha el szeretné kerülni az alkalmazások konfigurációjának ASP.NET Core alkalmazáson belüli lekérdezését, ne használjon olyan kulcsokat, amelyek csak a burkolattól eltérőek.

Az alkalmazások konfigurációjában megadott összes Unicode-karaktert használhat `*`, `,`és `\`kivételével. Ezek a karakterek le vannak foglalva. Ha foglalt karaktert kell tartalmaznia, el kell menekülnie `\{Reserved Character}`használatával. A kulcs-érték párokban egy 10 000 karakterből álló, kombinált méretkorlát található. Ez a korlát a kulcs, a hozzá tartozó érték és az összes hozzá tartozó opcionális attribútum összes karakterét tartalmazza. Ezen a korláton belül számos hierarchikus szintet is megadhat a kulcsokhoz.

### <a name="design-key-namespaces"></a>Tervezési kulcs névterei

A konfigurációs adataihoz használt kulcsok elnevezésének két általános megközelítése van: Flat vagy hierarchikus. Ezek a módszerek hasonlóak az alkalmazás használati szempontjából, de a hierarchikus elnevezés számos előnyt kínál:

* Könnyebben olvasható. Egy hosszú karaktersorozat helyett a hierarchikus kulcs neve egy mondatban szóközökként működik. Természetes szüneteket is biztosítanak a szavak között.
* Könnyebben kezelhető. A kulcsnév-hierarchia a konfigurációs adategységek logikai csoportjait jelöli.
* Könnyebben használható. Egyszerűbb olyan lekérdezést írni, amely egy hierarchikus struktúra kulcsaira illeszkedik, és csak a konfigurációs adat egy részét kéri le. Emellett számos újabb programozási keretrendszer natív módon támogatja a hierarchikus konfigurációs adatokat, például hogy az alkalmazás képes legyen a különböző konfigurációs beállítások használatára.

Az alkalmazások konfigurációjában többféleképpen rendezheti a kulcsokat. Tekintse át ezeket a kulcsokat [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)-ként. Minden hierarchikus kulcs egy olyan erőforrás *elérési útja* , amely egy vagy több olyan összetevőből áll, amelyek elhatárolók egymáshoz csatlakoznak. Válassza ki az alkalmazás, a programozási nyelv vagy a keretrendszer igényei alapján elválasztóként használandó karaktert. Az alkalmazás konfigurációjában több határolójel is használható a különböző kulcsokhoz.

Íme néhány példa arra, hogyan alakíthatja át a kulcsok nevét egy hierarchiába:

* A Component Services alapján

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Központi telepítési régiók alapján

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Címkék kulcsai

Az alkalmazás konfigurációjában a kulcs értékei opcionálisan rendelkezhetnek Label attribútummal is. A címkék használatával a kulcs értékeit ugyanazzal a kulccsal lehet megkülönböztetni. Az *a* és *B* címkével ellátott *App1* két különálló kulcsot alkotnak az alkalmazás konfigurációs tárolójában. Alapértelmezés szerint a kulcs értékének címkéje üres vagy `null`.

A Label kényelmes módszert biztosít a kulcsok változatának létrehozásához. A címkék gyakori használata több környezet megadására szolgál ugyanahhoz a kulcshoz:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Verzió kulcsának értékei

Az alkalmazás konfigurációja nem használja automatikusan a kulcs értékeit, ahogy azok módosítva lettek. Használjon címkéket úgy, hogy a kulcs értékének több verzióját hozza létre. Megadhat például egy alkalmazás verziószámát vagy egy git commit ID-t a címkékben egy adott szoftver-összeállításhoz társított kulcsok azonosításához.

A címkékben bármilyen Unicode-karaktert használhat, `*`, `,`és `\`kivételével. Ezek a karakterek le vannak foglalva. Egy fenntartott karakter belefoglalásához `\{Reserved Character}`használatával kell elmenekülnie.

### <a name="query-key-values"></a>Lekérdezési kulcs értékei

Minden egyes kulcs értékét egyedileg azonosítják a kulcsa, valamint egy címkét, amely `null`lehet. A kulcsok értékeit egy minta megadásával kérdezheti le. Az alkalmazás konfigurációs tárolója visszaadja az összes olyan kulcs értéket, amely megfelel a mintának, valamint a hozzájuk tartozó értékeknek és attribútumoknak. Az alkalmazás konfigurálásához REST API hívásokban használja az alábbi főbb mintákat:

| Paraméter | |
|---|---|
| `key` ki van hagyva vagy `key=*` | Az összes kulcs egyezése |
| `key=abc` | Pontosan megfelel az **ABC** -kulcs nevének |
| `key=abc*` | Az **ABC** -vel kezdődő kulcsok neveinek felel meg |
| `key=*abc` | Az **ABC** -végződésű kulcsok neveinek felel meg |
| `key=*abc*` | Az **ABC** -t tartalmazó kulcsok neveinek felel meg |
| `key=abc,xyz` | Az **ABC** -vagy **XYZ**-kulcsok neveinek felel meg, legfeljebb öt CSV |

A következő címke mintákat is tartalmazhatja:

| Címke | |
|---|---|
| `label` ki van hagyva vagy `label=*` | Bármely címkére illeszkedik, amely tartalmazza a `null` |
| `label=%00` | Megfelel `null` címkének |
| `label=1.0.0` | Pontosan megfelel a Label **1.0.0** -nak |
| `label=1.0.*` | Az 1,0 karakterrel kezdődő címkéket tartalmazza **.** |
| `label=*.0.0` | A **. 0,0** végződésű címkékre illeszkedik. |
| `label=*.0.*` | A **. 0** karakterláncot tartalmazó címkéket tartalmazza. |
| `label=%00,1.0.0` | A `null` vagy a **1.0.0**címkének felel meg, legfeljebb öt CSV |

## <a name="values"></a>Értékek

A kulcsokhoz rendelt értékek szintén Unicode karakterláncok. Az értékekhez az összes Unicode-karakter használható. Az egyes értékekhez nem kötelező, felhasználó által definiált tartalomtípus van társítva. Ezzel az attribútummal tárolhat adatokat, például egy kódolási sémát egy olyan értékkel kapcsolatban, amely segít az alkalmazásnak megfelelően feldolgozni.

Az alkalmazás-konfigurációs tárolóban tárolt konfigurációs adatok, amelyek tartalmazzák az összes kulcsot és értéket, a rendszer a REST és az átvitel során titkosítja. Az alkalmazás konfigurációja nem helyettesítő megoldás a Azure Key Vault. Ne tárolja az alkalmazás titkos kulcsait.

## <a name="next-steps"></a>Következő lépések

* [Időponthoz tartozó pillanatkép](./concept-point-time-snapshot.md)  
* [Szolgáltatások kezelése](./concept-feature-management.md)  
