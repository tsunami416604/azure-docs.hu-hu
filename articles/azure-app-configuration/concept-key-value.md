---
title: Az Azure App konfigurációs kulcs-érték tároló |} A Microsoft Docs
description: Konfigurációs adatok tárolási módját a Azure-alkalmazások konfigurálása az áttekintése
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: f04067358b0b2bae745727a5dd7a1f5554f9f70e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884819"
---
# <a name="key-value-store"></a>Kulcs-érték tároló

Az Azure App konfigurálása kulcs-érték párok, egy egyszerű, de a rugalmas módja alkalmazásbeállításokat, amelyek a fejlesztők számára is felismerhetők a különböző típusú képviselő konfigurációs adatokat tárolja.

## <a name="keys"></a>Kulcsok

Kulcsok számára a kulcs-érték párok szolgál, és tárolására és lekérdezésére a megfelelő értékeket használják. Egy általános gyakorlat a kulcsok szervezheti egy karakterrel elválasztó segítségével hierarchikus névtér (például `/` vagy `:`) olyan egyezmény, amely az alkalmazás a legalkalmasabb alapján. Alkalmazás konfigurálása kulcsok teljes kezeli. Döntse el, hogyan nevük struktúrája vagy kényszerítése minden szabály rajtuk a kulcsok nem azok elemezni.

A konfigurációs adattároló belüli alkalmazás-keretrendszerek használatát miatt előfordulhat, hogy adott elnevezési sémát kulcs-értékeket. Tegyük fel, a Java Spring Cloud keretrendszerben meghatározása `Environment` megadhatja azokat a beállításokat, beleértve a változók lehet paraméterezni a Spring-alkalmazás erőforrások *alkalmazásnév* és *profil*. Kulcsokat a Spring felhővel kapcsolatos konfigurációs adatok általában kezdődik a két elem után külön-külön egy elválasztó alapján.

Alkalmazáskonfiguráció tárolt kulcsok olyan kis-és nagybetűket, unicode-alapú karakterláncok. A kulcsok *app1* és *App1* nem azonos egy alkalmazás a konfigurációs adattárolónál a. Ne feledje az alkalmazáson belül a konfigurációs beállítások használatakor, mivel bizonyos keretrendszerek konfigurációs kulcsok case-insensitively kezelni. Például az ASP.NET Core konfigurációs kezeli a rendszer kulcsok nem betűérzékeny karakterláncként. Alkalmazások konfigurálása az ASP.NET Core-alkalmazáson belül lekérdezésekor kiszámíthatatlan viselkedést elkerüléséhez, amelyet csak a kis-és eltérő kulcsok nem használható.

Ön jogosult-e bármely unicode karaktert használhatja az Alkalmazáskonfigurációt, kivéve a megadott kulcsnevek `*`, `,` és `\` van fenntartva. Egy fenntartott karaktert használniuk kell, ha kell-e escape használatával `\{Reserved Character}`. Az egy kulcs-érték pár 10 ezer karakterek összesített mérete korlátozva van. A kulcs értéke, ez tartalmazza az összes karaktert, és minden ahhoz kapcsolódó attribútumok nem kötelező. Ezt a határt, belül számos hierarchikus szintek, a kulcsok rendelkezhet.

### <a name="designing-key-namespaces"></a>Kulcs névterek tervezése

Kétféleképpen általános konfigurációs adatok használt kulcsok elnevezési: egybesimított vagy hierarchikus. Ezek a metódusok alkalmazás használati szempontjából nagyon hasonló, de ez utóbbi számos előnyt kínál:

* Könnyebben olvasható. Helyett egy hosszú megadott karaktersorozattal egy hierarchikus kulcsnév az elválasztó karakterek mondaton belüli tárolóhelyek funkcionál, és biztosítja a szavak közötti természetes oldaltörések.
* Könnyebben kezelhető. A kulcsnév hierarchia logikai csoportok konfigurációs adatokat jelöli.
* Könnyebben használható. Sokkal egyszerűbb, írhat egy lekérdezést, amely a minta-egyezések hierarchikus kulcsokat, és beolvassa a konfigurációs adatok egy részét. Emellett számos újabb programozási keretrendszer rendelkezik natív módon támogatja a hierarchikus konfigurációs adatok úgy, hogy az alkalmazás győződjön meg arról is, használja az adott csoportok konfigurációjának.

Kulcsokat az Alkalmazáskonfigurációt számos módon hierarchikusan rendezhetők. Az ilyen kulcsokat is felfoghatók [URI-k](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Minden hierarchikus kulcs egy olyan erőforrás *elérési* mikroszolgáltatásokból álló, egy vagy több összetevőt, összekapcsolódva elválasztó alapján. Megadhatja, milyen karakter egy elválasztó alapján milyen az alkalmazás, programozási nyelvet vagy keretrendszert használni kell. Több elválasztó használhatja az Alkalmazáskonfigurációt különböző kulcsok.

Íme néhány példa hogyan strukturálhatja a kulcsnevek hierarchiára:

* A környezetek alapján

        AppName:Test:DB:Endpoint
        AppName:Staging:DB:Endpoint
        AppName:Production:DB:Endpoint

* A Komponensszolgáltatások alapján

        AppName:Service1:Test:DB:Endpoint
        AppName:Service1:Staging:DB:Endpoint
        AppName:Service1:Production:DB:Endpoint
        AppName:Service2:Test:DB:Endpoint
        AppName:Service2:Staging:DB:Endpoint
        AppName:Service2:Production:DB:Endpoint

* A telepítési régió alapján

        AppName:Production:Region1:DB:Endpoint
        AppName:Production:Region2:DB:Endpoint

### <a name="versioning-key-values"></a>Verziókezelés kulcs-érték

Kulcs-értékeket az alkalmazás konfigurációs szükség lehet egy **címke** attribútum. Címkék segítségével különbséget tenni a kulcs-érték ugyanazzal a kulccsal. A kulcs *app1* , a címkék *v1* és *v2* két külön kulcs-érték egy alkalmazás a konfigurációs adattárolónál az űrlapon. Alapértelmezés szerint a kulcs-érték címkéje az üres (vagy `null`).

Alkalmazások konfigurálása automatikusan nem nem verzió kulcs-értékeket, mert módosításuk. Címkék arra, hogy hozzon létre egy kulcs-érték több verziója is használhatja. Például megadhatja, hogy egy alkalmazás verziószáma, vagy egy Git commit-azonosítója a címkéken kulcs-értékeket egy adott build társított.

Ön jogosult-e bármely unicode karaktert, a címkék, használja az alábbiakat kivéve `*`, `,` és `\`, amely van fenntartva. Egy fenntartott karaktert használniuk kell, ha kell-e escape használatával `\{Reserved Character}`.

### <a name="querying-key-values"></a>Lekérdezési kulcs-érték

Minden kulcs-érték egyedileg azonosítja a kulcsok és a egy címke, amely lehet `null`. Adjon meg egy minta lekérdezheti, ha egy alkalmazás konfigurációs adattároló kulcs-értékeket. Az alkalmazás a konfigurációs adattároló adja vissza az összes kulcs-érték, amely megfelel a mintának, és a kapcsolódó értékeket és attribútumok. A következő kulcs minták használhatja az Alkalmazáskonfigurációt REST API-hívások:

| Kulcs | |
|---|---|
| `key` Nincs megadva, vagy `key=*` | Megfelel az összes kulcs |
| `key=abc` | Egyezések kulcsnév **abc** pontosan. |
| `key=abc*` | Egyezések kulcs karakterekkel **abc** |
| `key=*abc` | Egyezések kulcs karakterre végződjön **abc** |
| `key=*abc*` | Egyezések kulcsneveinek tartalmazó **abc** |
| `key=abc,xyz` | Egyezések kulcsneveinek **abc** vagy **xyz**, legfeljebb 5 CSV-k |

A következő címke minták is használható:

| Címke | |
|---|---|
| `label` Nincs megadva, vagy `label=*` | Olyan címke megfelel többek között `null` |
| `label=%00` | Egyezések `null` címkét. |
| `label=1.0.0` | Egyezések címke **1.0.0-s** pontosan |
| `label=1.0.*` | Megegyezik a kezdődő címkéket **1.0-t.** |
| `label=*.0.0` | Megegyezik a címkék, amelyek végződhet **.0.0** |
| `label=*.0.*` | Megegyezik a címkék tartalmazó **.0.** |
| `label=%00,1.0.0` | Megegyezik a címkék `null` vagy **1.0.1**, legfeljebb 5 CSV-k |

## <a name="values"></a>Értékek

Kulcsok rendelt értékeket is olyan unicode karakterláncok. Az összes unicode-karaktereket értékeket is használhat. Van egy nem kötelező felhasználói **tartalomtípus** társított minden egyes érték. Ez az attribútum segítségével tárolja az adatokat (például kódolási sémát) kapcsolatos érték, amely segít az alkalmazás megfelelően feldolgozni azt.

Egy alkalmazás a konfigurációs adattároló, minden kulcsokat és értékeket, beleértve a tárolt konfigurációs adatok titkosítottak, inaktív és átvitel közben. Mindazonáltal Alkalmazáskonfigurációt nem áll egy helyettesítő megoldás az Azure Key Vault. Titkos alkalmazáskulcsok, nem tárolja.

## <a name="next-steps"></a>További lépések

* [Fogalom: Point-in-time snapshot](concept-point-time-snapshot.md)  
