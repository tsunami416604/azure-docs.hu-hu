---
title: Az Azure app Configuration kulcs-érték tárolójának ismertetése
description: Ismerje meg, hogyan tárolja a konfigurációs adatkészleteket az Azure-alkalmazások konfigurációjában.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 0b83a35d912c97ae25bc2d69d076e8eae8ca490f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523604"
---
# <a name="keys-and-values"></a>Kulcsok és értékek

Az Azure-alkalmazás konfigurációja kulcs-érték párokként tárolja a konfigurációs értékeket. A kulcs-érték párok a fejlesztők által használt alkalmazások beállításainak egyszerű és rugalmas ábrázolása.

## <a name="keys"></a>Kulcsok

A kulcsok azonosítóként szolgálnak a kulcs-érték párokhoz, és a megfelelő értékek tárolására és lekérésére használatosak. Gyakori eljárás a kulcsok hierarchikus névtérbe rendezése egy karakter elválasztójának használatával, például `/` vagy. `:` Az alkalmazáshoz legjobban illeszkedő konvenciót használjon. Az alkalmazás konfigurációja a kulcsokat teljes egészében kezeli. Nem elemzi a kulcsokat, hogy megtudja, hogyan épülnek fel a nevük, és hogyan kényszerítenek rájuk.

Íme két példa a hierarchiában strukturált kulcsnévre:

* A Component Services alapján

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Központi telepítési régiók alapján

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

A konfigurációs adatok alkalmazás-keretrendszereken belüli használata bizonyos elnevezési sémákat határozhat meg a kulcsok értékeihez. A Java Spring Cloud Framework például olyan erőforrásokat határoz `Environment` meg, amelyek a Spring Application beállításait adják meg.  Ezek az *alkalmazás nevét* és *profilját*tartalmazó változók paraméterei. A tavaszi felhőhöz kapcsolódó konfigurációs adatok kulcsai általában a két elem elválasztó karakterrel kezdődnek.

Az alkalmazás konfigurációjában tárolt kulcsok kis-és nagybetűket megkülönböztető, Unicode-alapú karakterláncok. Az *App1* és a *App1* kulcsok eltérnek az alkalmazás konfigurációs tárolójában. Ne feledje, hogy ha a konfigurációs beállításokat egy alkalmazáson belül használja, mert egyes keretrendszerek kis-és nagybetűk nélkül kezelik a konfigurációs kulcsokat. A kulcsok megkülönböztetésére nem ajánlott a Case használata.

A kulcsok neveiben bármilyen Unicode-karaktert használhat, `*` `,`a, a és `\`a kivételével.  Ha fel kell vennie a fenntartott karakterek egyikét, a használatával `\{Reserved Character}`kell elmenekülnie. 

A kulcs-érték párokban egy összesített méretkorlát 10 KB. Ez a korlát a kulcs, a hozzá tartozó érték és az összes hozzá tartozó opcionális attribútum összes karakterét tartalmazza. Ezen a korláton belül számos hierarchikus szintet is megadhat a kulcsokhoz.

### <a name="design-key-namespaces"></a>Tervezési kulcs névterei

A konfigurációs adataihoz használt kulcsok elnevezésének két általános megközelítése van: Flat vagy hierarchikus. Ezek a módszerek hasonlóak az alkalmazás használati szempontjából, de a hierarchikus elnevezés számos előnyt kínál:

* Könnyebben olvasható. A hierarchikus kulcs nevének határolói a mondatokban szóközökként működnek. Természetes szüneteket is biztosítanak a szavak között.
* Könnyebben kezelhető. A kulcsnév-hierarchia a konfigurációs adategységek logikai csoportjait jelöli.
* Könnyebben használható. Egyszerűbb olyan lekérdezést írni, amely egy hierarchikus struktúra kulcsaira illeszkedik, és csak a konfigurációs adat egy részét kéri le. Emellett számos újabb programozási keretrendszer natív módon támogatja a hierarchikus konfigurációs adatokat, például hogy az alkalmazás képes legyen a különböző konfigurációs beállítások használatára.

Az alkalmazások konfigurációjában többféleképpen rendezheti a kulcsokat. Tekintse át ezeket a kulcsokat [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)-ként. Minden hierarchikus kulcs egy olyan erőforrás *elérési útja* , amely egy vagy több olyan összetevőből áll, amelyek elhatárolók egymáshoz csatlakoznak. Válassza ki az alkalmazás, a programozási nyelv vagy a keretrendszer igényei alapján elválasztóként használandó karaktert. Az alkalmazás konfigurációjában több határolójel is használható a különböző kulcsokhoz.

### <a name="label-keys"></a>Címkék kulcsai

Az alkalmazás konfigurációjában a kulcs értékei opcionálisan rendelkezhetnek Label attribútummal is. A címkék használatával a kulcs értékeit ugyanazzal a kulccsal lehet megkülönböztetni. Az *a* és *B* címkével ellátott *App1* két különálló kulcsot alkotnak az alkalmazás konfigurációs tárolójában. Alapértelmezés szerint a kulcs értéke nem rendelkezik címkével. Ha címkével nem rendelkező kulcsot szeretne explicit módon hivatkozni, `\0` használja a (URL- `%00`cím) értéket.

A Label kényelmes módszert biztosít a kulcsok változatának létrehozásához. A címkék gyakori használata több környezet megadására szolgál ugyanahhoz a kulcshoz:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Verzió kulcsának értékei

Az alkalmazás konfigurációja nem a kulcs értékeit automatikusan. Használjon címkéket úgy, hogy a kulcs értékének több verzióját hozza létre. Megadhat például egy alkalmazás verziószámát vagy egy git commit ID-t a címkékben egy adott szoftver-összeállításhoz társított kulcsok azonosításához.

A címkéken bármilyen Unicode-karaktert használhat, `*` `,`a, a és `\`a kivételével. Ezek a karakterek le vannak foglalva. Egy fenntartott karakter belefoglalásához a használatával `\{Reserved Character}`kell elmenekülnie.

### <a name="query-key-values"></a>Lekérdezési kulcs értékei

Minden egyes kulcs értékét egyedileg azonosítják a kulcsa, valamint egy címke, amely lehet `null`. A kulcsok értékeit egy minta megadásával kérdezheti le. Az alkalmazás konfigurációs tárolója visszaadja az összes olyan kulcs értéket, amely megfelel a mintának, valamint a hozzájuk tartozó értékeknek és attribútumoknak. Az alkalmazás konfigurálásához REST API hívásokban használja az alábbi főbb mintákat:

| Kulcs | |
|---|---|
| `key`nincs megadva vagy`key=*` | Az összes kulcs egyezése |
| `key=abc` | Pontosan megfelel az **ABC** -kulcs nevének |
| `key=abc*` | Az **ABC** -vel kezdődő kulcsok neveinek felel meg |
| `key=abc,xyz` | Az **ABC** vagy az **XYZ**kulcsnévnek felel meg. Legfeljebb öt CSV |

A következő címke mintákat is tartalmazhatja:

| Címke | |
|---|---|
| `label`nincs megadva vagy`label=*` | Megfelel bármely címkének, amely tartalmazza a következőket`null` |
| `label=%00` | Egyezések `null` címkéje |
| `label=1.0.0` | Pontosan megfelel a Label **1.0.0** -nak |
| `label=1.0.*` | Az 1,0 karakterrel kezdődő címkéket tartalmazza **.** |
| `label=%00,1.0.0` | A címkékre `null` vagy a **1.0.0**-ra korlátozódik, legfeljebb öt CSV |

## <a name="values"></a>Értékek

A kulcsokhoz rendelt értékek szintén Unicode karakterláncok. Az értékekhez az összes Unicode-karakter használható. Az egyes értékekhez nem kötelező, felhasználó által definiált tartalomtípus van társítva. Ezzel az attribútummal adatokat tárolhat egy olyan értékről, amely segít az alkalmazásnak megfelelően feldolgozni.

Az alkalmazás-konfigurációs tárolóban tárolt konfigurációs adatok titkosítva maradnak a nyugalmi állapotban és az átvitel során. A kulcsok nincsenek titkosítva a nyugalmi állapotban. Az alkalmazás konfigurációja nem helyettesítő megoldás a Azure Key Vault. Ne tárolja az alkalmazás titkos kulcsait.

## <a name="next-steps"></a>További lépések

* [Adott időpontban készült pillanatképek](./concept-point-time-snapshot.md)  
* [Funkciókezelés](./concept-feature-management.md)  
