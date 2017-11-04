---
title: "Azure Media Services API többi eléréséhez használja az Azure AD hitelesítési |} Microsoft Docs"
description: "Útmutató: Azure Active Directory-hitelesítéssel Azure Media Services API hozzáférhet a többi."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: e5d7a5ec1c28a552420aba5e2cd6c8c7bbf4213d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Az Azure Media Services API REST eléréséhez használja az Azure AD-alapú hitelesítés

Az Azure Media Services team közzétette az Azure Media Services access Azure Active Directory (Azure AD-) hitelesítés támogatása. A jelentette be Azure hozzáférés-vezérlés szolgáltatás hitelesítési a Media Services hozzáféréshez érvényteleníthető terveket is. Minden Azure-előfizetés, és minden Media Services-fiók, az Azure AD-bérlő van csatolva, az Azure AD hitelesítési támogatás számos lehetőséget kínál számos biztonsági szempontból előnyökkel járhat. A változás- és (ha az alkalmazás használja a Media Services .NET SDK) áttelepítési kapcsolatos részletekért lásd: a következő blogbejegyzések és cikkek:

- [Az Azure Media Services időről támogatás az Azure AD és a hozzáférés-vezérlés hitelesítési elavulása](https://azure.microsoft.com/blog/azure%20media%20service%20aad%20auth%20and%20acs%20deprecation)
- [Access Azure Media Services API az Azure AD-hitelesítés használatával](media-services-use-aad-auth-to-access-ams-api.md)
- [Azure Media Services API hozzáférhet a Microsoft .NET hitelesítés használata az Azure AD](media-services-dotnet-get-started-with-aad.md)
- [Ismerkedés az Azure AD-alapú hitelesítés az Azure portál használatával](media-services-portal-get-started-with-aad.md)

Egyes ügyfelek alapján a következő korlátozások vonatkoznak a Media Services-megoldások kifejlesztésére lesz szükség:

*   Microsoft .NET vagy C# programozási nyelven használata, vagy a futtatókörnyezet nincs Windows.
*   Az Azure AD szalagtárak, például az Active Directory hitelesítési Kódtárai nem érhetők el a programozási nyelven, vagy nem használható a futtatókörnyezetben.

Egyes ügyfelek alkalmazások hozzáférés-vezérlés hitelesítéséhez és access Azure Media Services REST API használatával fejlesztett ki. Ezek az ügyfelek szükségük van egy módszerre, csak a REST API-t használja az Azure AD-alapú hitelesítés és további Azure Media Services-hozzáférést. Támaszkodjon az Azure AD-tárak bármelyikét, vagy a Media Services .NET SDK kell. Ez a cikk azt írják le a megoldást, és adja meg mintakód ehhez a forgatókönyvhöz. Mivel a kódot minden REST API-hívásokkal, minden Azure ad-val nem függőség vagy az Azure Media Services library, a kód könnyen fordítható bármely más programozási nyelv.

> [!IMPORTANT]
> A Media Services jelenleg az Azure hozzáférés-vezérlés hitelesítési modell. Azonban hozzáférés-vezérlés hitelesítési elavulttá válik 2018. június 1. Azt javasoljuk, hogy telepítse át az Azure AD hitelesítési modell lehető legrövidebb időn belül.


## <a name="design"></a>Tervezés

Ez a cikk a következő hitelesítési és engedélyezési tervezési használjuk:

*  Protokoll: OAuth 2.0-s. OAuth 2.0 olyan webes biztonsági szabvány, amely lefedi a hitelesítést és az engedélyezést. Azt támogatják Google, a Microsoft, a Facebook-on és a PayPal. 2012. októberi azt lett erősítette meg. A Microsoft az OAuth 2.0 és az OpenID Connect erősen támogatja. Mindkét ezeknek a szabványoknak támogatottak több szolgáltatások és ügyfél könyvtárak, beleértve az Azure Active Directoryban, Open Web Interface .NET (OWIN) Katana, a és az Azure AD-könyvtárak.
*  Támogatás típusa: ügyfél hitelesítő adatai megadják típusa. Ügyfél hitelesítő adatait a négy grant típusok, az OAuth 2.0 egyike. A Microsoft Azure AD Graph API access gyakran használják azt.
*  Hitelesítési módszer: egyszerű szolgáltatást. A hitelesítési mód a felhasználó vagy az interaktív hitelesítés.

Összesen négy alkalmazáshoz vagy szolgáltatáshoz is érintett a Azure AD hitelesítési és engedélyezési folyamata Media Services használatával. A folyamatot, az alkalmazások és szolgáltatások a következő táblázat ismerteti:

|Alkalmazás típusa |Alkalmazás |Folyamat|
|---|---|---|
|Ügyfél | Felhasználói alkalmazás vagy megoldás | Ez az alkalmazás (ténylegesen, a proxy) regisztrálva van az Azure AD-bérlőt, amelyben az Azure-előfizetés és a media service fiók található. A szolgáltatás egyszerű, a regisztrált alkalmazás majd kapnak a tulajdonos vagy közreműködő szerepkörrel a a hozzáférést vezérlő (IAM) a media service fiók. A szolgáltatás egyszerű a app ID és az ügyfél ügyfélkulcs jelképezi. |
|Az identitásszolgáltató (IDP) | Azure AD szolgáltatásba IDP | A regisztrált alkalmazás egyszerű szolgáltatásnév (ügyfél-azonosító és a titkos ügyfélkódot) hitelesítése az Azure AD szolgáltatásba a kiállító terjesztési hely. Ez a hitelesítés a belső és implicit módon történik. Ügyfél-hitelesítő adatok folyamata, mint az ügyfél helyett a felhasználó hitelesítése. |
|Secure Token Service (STS) / OAuth-kiszolgáló | Azure AD szolgáltatásba STS | A kiállító terjesztési hely (vagy az OAuth kiszolgáló OAuth 2.0 feltételeit) hitelesítést követően egy hozzáférési jogkivonat vagy JSON webes jogkivonat (JWT) által kiadott STS/OAuth-kiszolgálóként a hozzáférés az Azure AD a középső rétegbeli erőforrásra: Ebben az esetben a Media Services REST API végpontra. |
|Erőforrás | Media Services REST API-k | Minden Media Services REST API hívása olyan hozzáférési jogkivonatot STS vagy az OAuth-kiszolgálóként az Azure AD által kiadott engedélyezve van. |

Ha a mintakódot futtatja, és rögzítheti a jwt-t vagy a hozzáférési tokent, a jwt-t az alábbi attribútumok tartoznak:

    aud: "https://rest.media.azure.net",

    iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    iat: 1497146280,

    nbf: 1497146280,
    exp: 1497150180,

    aio: "Y2ZgYDjuy7SptPzO/muf+uRu1B+ZDQA=",

    appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6",

    appidacr: "1",

    idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    oid: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    sub: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",

A jwt-t és a négy alkalmazások attribútumokat vagy az előző táblázatban szereplő szolgáltatások között az alábbiakban a leképezéseket:

|Alkalmazás típusa |Alkalmazás |JWT-attribútum |
|---|---|---|
|Ügyfél |Felhasználói alkalmazás vagy megoldás |AppID: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6". A következő szakaszban regisztrálni fogja az Azure AD-alkalmazás ügyfél-azonosító. |
|Az identitásszolgáltató (IDP) | Azure AD szolgáltatásba IDP |IDP: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/" a GUID-azonosító az azonosító a Microsoft-bérlő (microsoft.onmicrosoft.com). Mindegyik bérlő saját, egyedi azonosítóval rendelkezik |
|Secure Token Service (STS) / OAuth-kiszolgáló |Azure AD szolgáltatásba STS | iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/". A globálisan egyedi Azonosítót az az azonosítója a Microsoft-bérlő (microsoft.onmicrosoft.com). |
|Erőforrás | Media Services REST API-k |és: "https://rest.media.azure.net". A címzett, vagy a célközönség és a hozzáférési jogkivonat. |

## <a name="steps-for-setup"></a>A telepítés lépései

Regisztrálásához és az alkalmazás Azure Active Directory (AAD) beállítása, valamint a kulcsok az Azure Media Services REST API-végpont meghívása, tekintse meg a cikk [Ismerkedés az Azure AD-alapú hitelesítés az Azure portál használatával](media-services-portal-get-started-with-aad.md)


## <a name="info-to-collect"></a>Adatok összegyűjtése

REST-kódolási előkészítéséhez gyűjtse össze a kód felvenni a következő adatpontokhoz:

*   Azure AD szolgáltatásba STS-végpont: https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token. Az ehhez a végponthoz a JWT jogkivonat van szükség. Az IDP szolgál, mellett az Azure AD is szolgál az STS szolgáltatással. Az Azure AD kibocsát egy jwt-t, az erőforrások eléréséhez (hozzáférési jogkivonat). A JWT jogkivonat különböző jogcímek rendelkezik.
*   Az Azure Media Services REST API erőforrás vagy a célközönség: https://rest.media.azure.net.
*   Ügyfél-azonosító: Lásd a 2. lépés a [lépéseket a telepítés](#steps-for-setup).
*   Ügyfélkulcs: 2 lépés lásd: [lépéseket a telepítés](#steps-for-setup).
*   A Media Services-fiók REST API-végpont a következő formátumban:

    https://[media_service_account_name].restv2. [data_center].media.azure.net/API 

    Ez az a végpont, mely összes Media Services REST API elleni hívások az alkalmazásban végrehajtott. Például https://willzhanmswjapan.restv2.japanwest.media.azure.net/API.

Ezek a paraméterek öt majd be a web.config vagy az app.config fájl használata a kódban.

## <a name="sample-code"></a>Mintakód

A mintakódot található [az Azure AD-alapú hitelesítés az Azure Media Services Access: mindkét REST API-n keresztül](https://github.com/willzhan/WAMSRESTSoln).

A mintakód két részből áll:

*   A dll-fájl hordozhatóosztálytár-projektjének, amely rendelkezik az Azure AD hitelesítési és engedélyezési REST API-kódban. A Media Services REST API végpontra, a hozzáférési jogkivonat a REST API-hívások metódust is tartalmaz.
*   Konzol teszt ügyfél, amely kezdeményezi az Azure AD-alapú hitelesítés, és különböző Media Services REST API-t hívja.

A minta projekt három lehetőséggel rendelkezik:

*   Az Azure AD hitelesítési keresztül az ügyfél hitelesítő adatait adja meg csak a REST API használatával.
*   Azure Media Services hozzáférés csak a REST API használatával.
*   Azure Storage hozzáférés csak a REST API használatával (a REST API használatával egy Media Services-fiók létrehozásához használt).


## <a name="where-is-the-refresh-token"></a>Hol található a frissítési jogkivonat?

Néhány olvasók kérheti: hol található a frissítési jogkivonat? Miért nem használja a frissítési jogkivonat Itt?

A frissítési jogkivonat célja nem egy hozzáférési jogkivonatot frissítése. Végfelhasználói hitelesítés kihagyásával még mindig érvényes jogkivonat, ha egy korábbi jogkivonat lejár tervezték. A frissítési jogkivonat jobb nevét lehet, hogy valami like "kihagyása a felhasználó újra-sign-in jogkivonatát."

Használja az OAuth 2.0 engedélyezési folyamata (felhasználónévvel és jelszóval, egy felhasználó nevében eljáró) adja meg, ha a frissítési jogkivonat segítséget nyújt a megújított access token beszerzése a kért felhasználói beavatkozás nélkül. Azonban az OAuth 2.0 ügyfél hitelesítő adatai megadják a cikkben ismertetett folyamatot, az ügyfél működik a saját nevében. Minden felhasználói beavatkozás nem szükséges, és a hitelesítési kiszolgáló nem szükséges, hogy biztosítson egy frissítési jogkivonat. Ha hibakeresése a **GetUrlEncodedJWT** metódust, akkor figyelje meg, hogy a jogkivonat végpontjához válaszát rendelkezik-e a hozzáférési tokent, de nincs frissítési jogkivonat.

## <a name="next-steps"></a>Következő lépések

Ismerkedés a [fájlok feltöltése a fiókjához](media-services-dotnet-upload-files.md).
