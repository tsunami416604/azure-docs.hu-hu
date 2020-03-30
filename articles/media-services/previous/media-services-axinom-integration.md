---
title: Widevine-licencek Azure Media Services szolgáltatásához az Axinom használata | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogyan használhatja az Azure Media Services (AMS) egy adatfolyam, amely dinamikusan titkosított AMS mind PlayReady és Widevine DRM-ek. A PlayReady licenc a Media Services PlayReady licenckiszolgálójáról, a Widevine licencpedig az Axinom licenckiszolgálótól származik.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 2ec3276b9b02c29b80d46e5fd31298c909857182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197164"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Az Axinom használata a Widevine-licencek kézbestéséhez az Azure Media Servicesbe 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Áttekintés
Az Azure Media Services (AMS) hozzáadta a Google Widevine dinamikus védelmét (részletekért lásd [a Mingfei blogját).](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) Emellett az Azure Media Player (AMP) widevine-támogatást is hozzáadott (a részleteket lásd [az AMP-dokumentum).](https://amp.azure.net/libs/amp/latest/docs/) Ez jelentős teljesítmény a CENC által védett DASH-tartalmak streamelésében, több natív DRM-mel (PlayReady és Widevine) az MSE-vel és EME-vel felszerelt modern böngészőkben.

A Media Services .NET SDK 3.5.2-es verziójától kezdve a Media Services lehetővé teszi a Widevine licencsablon konfigurálását és a Widevine-licencek beírását. A Widevine-licencek továbbításának támogatásához a következő AMS-partnereket is használhatja: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/) vagy [castLabs](https://castlabs.com/company/partners/azure/).

Ez a cikk az Axinom által kezelt Widevine licenckiszolgáló integrálását és tesztelését ismerteti. Konkrétan a következőkre terjed ki:  

* Dinamikus közös titkosítás konfigurálása több DRM-mel (PlayReady és Widevine) a megfelelő licencbeszerzési URL-ekkel;
* JWT-token létrehozása a licenckiszolgáló követelményeinek teljesítése érdekében;
* Az Azure Media Player alkalmazás fejlesztése, amely jwt tokenhitelesítéssel kezeli a licencbeszerzést;

A teljes rendszer és a tartalomkulcs, a kulcsazonosító, a kulcsmag, a JTW-jogkivonat és annak jogcímei a következő diagramon a legjobban leírhatók:

![DASH és CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
A dinamikus védelem és a kulcskézbesítési szabályzat konfigurálásáról a Mingfei blogjában talál: [A Widevine-csomagolás konfigurálása az Azure Media Services szolgáltatással.](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

A dinamikus CENC-védelmet több DRM-mel konfigurálhatja a DASH streameléshez, amely az alábbiak közül mindkettővel rendelkezik:

1. PlayReady védelem a Microsoft Edge és az IE11, amely rendelkezhet egy jogkivonat engedélyezési korlátozás. A korlátozott jogkivonat-házirendet egy biztonságos jogkivonat-szolgáltatás (STS) által kibocsátott jogkivonatnak kell kísérnie, például az Azure Active Directorynak;
2. Widevine védelem Chrome, megkövetelheti token hitelesítés token által kibocsátott egy másik STS. 

Lásd: [JWT token generáció](media-services-axinom-integration.md#jwt-token-generation) ja, hogy miért az Azure Active Directory nem használható STS-ként az Axinom Widevine licenckiszolgálója számára.

### <a name="considerations"></a>Megfontolandó szempontok
1. Az Axinom által megadott kulcsmagot (888800000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000 Az Axinom licenckiszolgáló kiadja az összes olyan licencet, amely tartalomkulcsokat tartalmaz ugyanazon kulcsmag alapján, amely tesztelésre és élességre is érvényes.
2. A Widevine licencbeszerzés [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense)URL-címe a teszteléshez: . A HTTP és a HTTS egyaránt engedélyezett.

## <a name="azure-media-player-preparation"></a>Az Azure Media Player előkészítése
Az AMP 1.4.0-s kapcsolattámogatja a PlayReady és a Widevine DRM-mel dinamikusan csomagolt AMS-tartalmak lejátszását.
Ha a Widevine licenckiszolgáló nem igényel jogkivonat-hitelesítést, nincs további teendője a Widevine által védett DASH-tartalom teszteléséhez. Például az AMP-csapat egy egyszerű [mintát](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html)biztosít, ahol láthatja, hogy működik a Microsoft Edge és az IE11 a PlayReady és a Chrome Widevine segítségével.
Az Axinom által biztosított Widevine licenckiszolgáló jwt token hitelesítést igényel. A JWT-jogkivonatot licenckérelemmel kell elküldeni egy "X-AxDRM-Message" HTTP-fejlécen keresztül. Erre a célra, meg kell adni a következő javascript a weboldalon hosting AMP beállítása előtt a forrás:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

A többi AMP-kód szabványos AMP API, mint az AMP dokumentum [itt](https://amp.azure.net/libs/amp/latest/docs/).

A fenti javascript beállítására egyéni engedélyezési fejléc még mindig egy rövid távú megközelítés, mielőtt a hivatalos hosszú távú megközelítés AMP szabadul fel.

## <a name="jwt-token-generation"></a>JWT token létrehozása
Az Axinom Widevine licenckiszolgáló teszteléséhez JWT token hitelesítésszükséges. Ezenkívül a JWT-jogkivonat egyik jogcíme a primitív adattípus helyett összetett objektumtípus.

Sajnos az Azure AD csak primitív típusokkal rendelkező JWT-jogkivonatokat adhat ki. Hasonlóképpen a .NET Framework API (System.IdentityModel.Tokens.SecurityTokenHandler és JwtPayload) csak összetett objektumtípus jogcímként való bevitelét teszi lehetővé. A jogcímek azonban továbbra is karakterláncként vannak szerializálva. Ezért nem használhatjuk a kettő egyikét sem a JWT-token generálására a Widevine licenckérelemhez.

John Sheehan [JWT NuGet csomagja](https://www.nuget.org/packages/JWT) megfelel az igényeknek, ezért ezt a NuGet csomagot fogjuk használni.

Az alábbiakban a JWT-jogkivonat létrehozásának kódja látható az Axinom Widevine licenckiszolgáló által a teszteléshez szükséges jogcímekkel:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Axinom Widevine licenckiszolgáló

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Megfontolandó szempontok
1. Annak ellenére, hogy az AMS PlayReady licenckézbesítési szolgáltatáshoz a hitelesítési jogkivonatot megelőző "Bearer=" szükséges, az Axinom Widevine licenckiszolgáló nem használja azt.
2. Az Axinom kommunikációs kulcs aláíró kulcsként szolgál. A kulcs egy hatasztási karakterlánc, de kódoláskor bájtok sorozataként kell kezelni, nem karakterláncként. Ezt a ConvertHexStringToByteArray metódussal lehet elérni.

## <a name="retrieving-key-id"></a>Kulcsazonosító beolvasása
Előfordulhat, hogy észrevette, hogy a JWT-jogkivonat létrehozásának kódjában kulcsazonosító szükséges. Mivel a JWT tokennek készen kell lennie az AMP-lejátszó betöltése előtt, a kulcsazonosítót le kell kérni a JWT token létrehozásához.

Természetesen többféle módon is megszerezheti a kulcsazonosítót. Például tárolhatjuk a kulcsazonosítót a tartalom metaadataival együtt egy adatbázisban. Vagy lekérheti a kulcsazonosítót a DASH MPD (Médiabemutató leírása) fájlból. Az alábbi kód az utóbbira van.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>Összefoglalás

Az Azure Media Services Content Protection és az Azure Media Player legújabb Widevine-támogatásával a DASH + Multi-native-DRM (PlayReady + Widevine) streamelését is megvalósíthatjuk mind a PlayReady licencszolgáltatással az AMS- és a Widevine-licencben, mind a Widevine licencben. szerver Axinom a következő modern böngészők:

* Chrome
* Microsoft Edge a Windows 10 rendszeren
* IE 11 Windows 8.1 és Windows 10 rendszeren
* Mind a Firefox (Desktop) és a Safari Mac (nem iOS) is támogatott keresztül Silverlight és ugyanazt az URL-t az Azure Media Player

A következő paraméterek szükségesek a mini-megoldás kihasználva Axinom Widevine licenc szerver. A kulcsazonosító kivételével a többi paramétert az Axinom biztosítja a Widevine kiszolgáló beállítása alapján.

| Paraméter | Hogyan használják |
| --- | --- |
| Kommunikációs kulcs azonosítója |A JWT-tokenben a "com_key_id" jogcím értékeként kell szerepelnie [(lásd ezt a](media-services-axinom-integration.md#jwt-token-generation) szakaszt). |
| Kommunikációs kulcs |JWT-jogkivonat aláíró kulcsaként kell használni [(lásd ezt a](media-services-axinom-integration.md#jwt-token-generation) szakaszt). |
| Kulcsmag |Tartalomkulcs létrehozásához kell használni bármely adott tartalomkulcs-azonosítóval [(lásd ezt a](media-services-axinom-integration.md#content-protection) szakaszt). |
| Widevine licenc beszerzésének URL-címe |A DASH-streameléseszköz-kézbesítési házirend konfigurálásához kell használni [(lásd ezt a](media-services-axinom-integration.md#content-protection) szakaszt). |
| Tartalomkulcs azonosítója |A JWT-jogkivonat jogosultsági üzenet jogcímének értéke részeként szerepelnie kell [(lásd ezt a](media-services-axinom-integration.md#jwt-token-generation) szakaszt). |

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Köszönetnyilvánítás
Szeretnénk elismerni a következő embereket, akik hozzájárultak a dokumentum létrehozásához: Kristjan Jõgi az Axinomból, Mingfei Yan és Amit Rajput.

