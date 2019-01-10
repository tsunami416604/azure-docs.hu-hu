---
title: Az Axinom használata a Widevine licencek kézbesítéséhez az Azure Media Services |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan használhatja az Azure Media Services (AMS), hogy olyan adatfolyamra, amely a PlayReady vagy a Widevine DRMs AMS dinamikusan titkosítja. A Media Services PlayReady licenckiszolgáló származik, a PlayReady-licenc, és a Widevine-licenc Axinom licenckiszolgáló hozta.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;Mingfeiy;rajputam;Juliako
ms.openlocfilehash: d269818e82261c51b63379bb41f69efdc21de18a
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191258"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Az Axinom használata a Widevine-licencek kézbestéséhez az Azure Media Servicesbe
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Áttekintés
Az Azure Media Services (AMS) hozzá van adva a Google Widevine dynamic védelmi (lásd: [Mingfei's blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) részletekért). Emellett az Azure Media Player (és) is hozzáadott Widevine támogatást (lásd: [AMP dokumentum](http://amp.azure.net/libs/amp/latest/docs/) részletekért). A modern böngészőkben MSE és EME ellátott Ez az egy fő befejezéséről az CENC a több-native többplatformos DRM (PlayReady és Widevine) védett DASH-tartalmak online lejátszásához.

-Es verziótól kezdve a Media Services .NET SDK 3.5.2-es verziójában kerültek bevezetésre a Media Services segítségével Widevine-licencsablon konfigurálásához, illetve Widevine-licencek is. A következő AMS-partnereket is használhatja a Widevine-licencek biztosításához: [Az Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Ez a cikk bemutatja, hogyan integrálható, és a Widevine-licenc kiszolgáló Axinom által kezelt tesztelése. Pontosabban a ismerteti:  

* A többplatformos DRM (PlayReady és Widevine) megfelelő licenc licenckérési URL-; dynamic Common Encryption titkosítás konfigurálása
* Annak érdekében, hogy a licenc kiszolgáló követelményeinek; a JWT jogkivonat létrehozása
* Fejlesztés az Azure Media Player alkalmazásra, amely kezeli a JWT jogkivonat-hitelesítés; licenc beszerzése

A teljes rendszer és a folyamat a tartalomkulcsot, a kulcs azonosítója, kulcsfontosságú kezdőérték, JTW token és a jogcímek legjobb leírható a következő ábra:

![DASH és CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Dinamikus protection és a kulcs objektumtovábbítási szabályzat konfigurálásához, tekintse meg a Mingfei's blog: [Widevine-csomagolás konfigurálása az Azure Media Services](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Dinamikus CENC védelmi DRM-mel a DASH-streamelési kellene a következő két konfigurálható:

1. A Microsoft Edge és IE11 engedélyezési jogkivonat korlátozás sikerült rendelkező PlayReady-védelmet. A tokennel korlátozott szabályzatokhoz által egy biztonságos jogkivonat-szolgáltatás (STS), például az Azure Active Directory; kiadott jogkivonatnak kell kísérnie
2. Chrome Widevine védelmét, akkor lehet szükség eszközjogkivonattal történő hitelesítés egy másik STS által kiállított jogkivonatok. 

Lásd: [JWT jogkivonat létrehozása](media-services-axinom-integration.md#jwt-token-generation) miért az Azure Active Directory nem használható az STS szolgáltatással Axinom a Widevine-licenc kiszolgáló a következő szakaszban.

### <a name="considerations"></a>Megfontolandó szempontok
1. A megadott Axinom kell használnia kulcs kezdőérték (8888000000000000000000000000000000000000) és a létrehozott vagy kiválasztott kulcs azonosítója kulcstovábbítást konfigurálásához a tartalom kulcs létrehozásához. Az Axinom licenckiszolgáló az azonos, tesztelési és éles érvényes kulcs kezdőérték alapján tartalomkulcs tartalmazó összes licencét.
2. A Widevine license licenckérési URL-cím tesztelési: [ https://drm-widevine-licensing.axtest.net/AcquireLicense ](https://drm-widevine-licensing.axtest.net/AcquireLicense). A HTTP- és HTTS engedélyezettek.

## <a name="azure-media-player-preparation"></a>Az Azure Media Player előkészítése
AMP v1.4.0 támogatja, amely dinamikusan mind a PlayReady és Widevine DRM-mel együtt van csomagolva AMS-tartalmak lejátszását.
Ha Widevine-licenc kiszolgáló nincs szükség a jogkivonat-hitelesítés, nincs további kell tennie egy Widevine által védett DASH tartalom teszteléséhez. Egy vonatkozó példáért a AMP csapat nyújt egy egyszerű [minta](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html), ahol megtekintheti a Microsoft Edge és az IE11 playreadyvel és Widevine-Chrome működik.
A Widevine-licenckiszolgáló Axinom által biztosított JWT jogkivonat-hitelesítés szükséges. A JWT jogkivonat az "X-AxDRM-üzenet" HTTP-fejlécben licenc kérelmet el kell. Erre a célra a weblapot üzemeltető és a forrás beállítása előtt adja hozzá a következő javascript kell:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

A többi AMP kód egy standard AMP API hasonlóan AMP dokumentum [Itt](http://amp.azure.net/libs/amp/latest/docs/).

A fenti javascript beállítás egyéni engedélyezési fejléc még mindig a rövid távú megközelítést akkor szabadul fel, és a hivatalos hosszú távú megközelítés előtt.

## <a name="jwt-token-generation"></a>JWT jogkivonat létrehozása
Az Axinom Widevine licenckiszolgáló tesztelési JWT jogkivonat-hitelesítés szükséges. Emellett a JWT jogkivonat jogcímeiben egyik egy összetett objektumtípus egyszerű adattípus helyett.

Sajnos az Azure AD csak abban az esetben JWT-jogkivonatokkal adhatja az egyszerű típusú. Hasonlóképpen .NET Framework és API-val (System.IdentityModel.Tokens.SecurityTokenHandler JwtPayload) csak lehetővé teszi összetett objektum típusú bemeneti jogcímekként. Azonban a jogcímek továbbra is karakterláncként serializovat. Ezért a JWT jogkivonat esetében a Widevine-licenc kérelem létrehozásához a két bármelyike nem használjuk.

John Sheehan [JWT NuGet-csomag](https://www.nuget.org/packages/JWT) igényeinek, így a NuGet csomagot használni fogjuk.

Az alábbi, a kód a teszteléshez szükséges jogcímek Axinom Widevine-licenc kiszolgáló által kért létrehozni JWT jogkivonat esetében:

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

Az Axinom Widevine licenckiszolgáló

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Megfontolandó szempontok
1. Annak ellenére, hogy az AMS PlayReady szolgáltatásra vonatkozó szükséges "tulajdonosi =" megelőző egy hitelesítési tokent, Axinom Widevine licenckiszolgáló nem használja fel.
2. Az Axinom kommunikációs kulcs aláírási kulcsa lesz. A kulcs a hexadecimális karakterlánc, azonban kezelni kell bájt nem karakterlánc sorozataként kódolás esetén. Ez a metódus ConvertHexStringToByteArray érhető el.

## <a name="retrieving-key-id"></a>Kulcsazonosító beolvasása
Talán már észrevette, hogy a kód létrehozásához a JWT jogkivonat, key azonosító megadása kötelező. Mivel a JWT jogkivonat kell előtt betöltése és lejátszó, készen áll a kulcs azonosítója kell lehet beolvasni a JWT jogkivonat létrehozásához.

Természetesen többféleképpen beolvasni a visszatartás kulcs azonosítóját. Például egy tárolhat kulcs azonosítója és a egy adatbázisban tartalmak metaadatait. Letöltheti vagy kulcsazonosító DASH MPD (Media bemutató leírása) fájlból. Az alábbi kódot az utóbbi van.

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
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
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

## <a name="summary"></a>Összegzés
A legújabb emellett az Azure Media Services Content Protection és az Azure Media Player Widevine támogatását tudjuk átvitel DASH + több-native többplatformos DRM (PlayReady és Widevine) és az AMS- és Widevine-licencek mindkét PlayReady-licenc szolgáltatás megvalósítása a következő modern böngészőkre Axinom kiszolgáló:

* Chrome
* A Windows 10-es Microsoft Edge-ben
* A Windows 8.1 és Windows 10-es IE 11
* Firefox (asztalon) és a Safari böngésző Mac (nem IOS-es) is támogatottak a Silverlight és az Azure Media Player ugyanazon URL-CÍMÉT

A következő paraméterek szükségesek, a minimális megoldás funkcióinak kihasználásával Axinom Widevine license kiszolgálón. Kivéve a kulcs Azonosítóját, a többi paraméterek által biztosított Axinom a Widevine-kiszolgáló beállításai alapján.

| Paraméter | Hogyan használja fel azokat |
| --- | --- |
| Kommunikációs kulcs azonosítója |Bele kell foglalni a jogcím "com_key_id" JWT jogkivonat értékével (lásd: [ez](media-services-axinom-integration.md#jwt-token-generation) szakaszt). |
| Kommunikációs kulcs |Az aláíró kulcs JWT jogkivonat kell használni (lásd: [ez](media-services-axinom-integration.md#jwt-token-generation) szakaszt). |
| Kulcs kezdőérték |Kell használni az adott tartalom tartalmi kulcs létrehozásához kulcs azonosítója (lásd: [ez](media-services-axinom-integration.md#content-protection) szakaszt). |
| A Widevine-licenckérési URL-cím |A DASH-streamelési objektumtovábbítási szabályzat konfigurálásához kell használni (lásd: [ez](media-services-axinom-integration.md#content-protection) szakaszt). |
| Tartalmi kulcs azonosítója |Része legyen a JWT jogkivonat jogosultság üzenet jogcím értékét kell lennie (lásd: [ez](media-services-axinom-integration.md#jwt-token-generation) szakaszt). |

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Visszaigazolás
Szeretnénk elfogadja az alábbi személyek, aki hozzájárult felé, hogy ez a dokumentum létrehozása: Az Axinom Kristjan Jõgi Mingfei Pozsony, és Amit Rajput.

