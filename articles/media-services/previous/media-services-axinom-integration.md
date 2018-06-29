---
title: Widevine-licencek kézbesíthet Azure Media Services Axinom használatával |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan használható az Azure Media Services (AMS) által a PlayReady vagy a Widevine DRMs AMS dinamikusan titkosított adatfolyam továbbítására. A PlayReady-licenc Media Services PlayReady licenckiszolgáló származik, és Widevine-licenc Axinom licenckiszolgáló hozta.
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
ms.openlocfilehash: 81247863eb86752113989f6e48e79f5c8bc75505
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061154"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Az Axinom használata a Widevine-licencek kézbestéséhez az Azure Media Servicesbe
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Áttekintés
Az Azure Media Services (AMS) hozzáadta a Google Widevine dynamic védelmi (lásd: [Mingfei's blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) részletekért). Emellett Azure Media Player (AMP) is felvette a Widevine-támogatás (lásd: [AMP dokumentum](http://amp.azure.net/libs/amp/latest/docs/) részletekért). Ez az adatfolyam a több-native-DRM (PlayReady és Widevine) CENC által védett DASH-tartalom egy fő befejezéséről MSE és EME modern böngésző támogatja a.

A Media Services .NET SDK verzió 3.5.2 verziótól kezdődően a Media Services lehetővé teszi a Widevine-licencsablon konfigurálása és Widevine-licencek. A Widevine-licencek továbbításának támogatásához a következő AMS-partnereket is használhatja: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) vagy [castLabs](http://castlabs.com/company/partners/azure/).

Ez a cikk ismerteti, hogyan integrálható, és tesztelje a Widevine licenckiszolgáló Axinom kezeli. Pontosabban ismerteti:  

* Konfigurálja a dynamic Common Encryption multi-DRM (PlayReady és Widevine) megfelelő licenc licenckérési URL;
* A JWT jogkivonat létrehozása érdekében végzett eszközfigyelés a kiszolgáló licenckövetelmények;
* Fejlesztés az Azure Media Player alkalmazást, amely kezeli a JWT jogkivonat hitelesítéssel; licenc beszerzése

A teljes rendszert vagy a tartalomkulcsot, áramló kulcs azonosítója, kulcs kezdőérték, JTW token és a jogcímek legjobb jelentheti a következő ábra:

![VONAL- és CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
A dinamikus védelem és a kulcs objektumtovábbítási szabályzat konfigurálása, lásd: Mingfei's blog: [Widevine-csomagolás konfigurálása az Azure Media Services](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Dinamikus CENC védelmet az adatfolyam-rendelkeznek a következő mindkét DASH multi-DRM konfigurálható:

1. PlayReady-védelem MS Edge és IE11, amelynek a token engedélyezési korlátozás. A token korlátozott házirend által a Secure Token Service (STS), például az Azure Active Directory; kiadott tokennek kell fűzni
2. Chrome Widevine védelmét, akkor lehet szükség tokent használó hitelesítés az egy másik STS által kibocsátott jogkivonatot. 

Lásd: [JWT jogkivonat generációs](media-services-axinom-integration.md#jwt-token-generation) miért Azure Active Directory nem használható az STS szolgáltatással Axinom tartozó Widevine licenckiszolgáló szakaszát.

### <a name="considerations"></a>Megfontolandó szempontok
1. A megadott Axinom kell használnia kulcs kezdőérték (8888000000000000000000000000000000000000) és a létrehozott vagy kiválasztott kulcs azonosítója a tartalomkulcs kulcs kézbesítési szolgáltatás létrehozásához. Axinom licenckiszolgáló azonos kulcs mag, amely érvényes a teszteléshez és éles alapján tartalomkulcs tartalmazó összes licencet.
2. A Widevine licenc licenckérési URL-cím tesztelési: [ https://drm-widevine-licensing.axtest.net/AcquireLicense ](https://drm-widevine-licensing.axtest.net/AcquireLicense). A HTTP és a HTTS engedélyezettek.

## <a name="azure-media-player-preparation"></a>Az Azure Media Player előkészítése
AMP v1.4.0 PlayReady és Widevine DRM-Védelemmel is dinamikusan csomagolt AMS tartalom lejátszását támogatja.
Widevine-licenckiszolgáló nem token hitelesítés szükséges, ha nincs szükség további kell tennie a Widevine által védett DASH tartalom tesztelése. Tegyük fel, a AMP team biztosít egy egyszerű [minta](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html), ahol megtekintheti a peremhálózati és IE11 a PlayReady és Widevine a Chrome működéséhez.
A Widevine licenckiszolgáló Axinom által biztosított JWT jogkivonat hitelesítést igényel. A JWT jogkivonat kell küldhető el az "X-AxDRM-üzenet" HTTP-fejléc licenc kérelmet. Erre a célra adja hozzá a következő javascript a weblapot üzemeltető AMP a forrás beállítása előtt kell:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

A többi AMP kód AMP dokumentumot mint szabványos AMP API [Itt](http://amp.azure.net/libs/amp/latest/docs/).

A fenti javascript beállítás egyéni engedélyezési fejléc még mindig a rövid távú megközelítést előtt a hivatalos hosszú távú megközelítést AMP megjelenik.

## <a name="jwt-token-generation"></a>JWT jogkivonat létrehozása
Tesztelési Axinom Widevine licenckiszolgáló JWT jogkivonat hitelesítés szükséges. Emellett a jogcímeket a JWT jogkivonat egyik egy összetett típusú egyszerű adattípus helyett.

Sajnos az Azure AD csak abban az esetben JWT-jogkivonatokat a primitív típusú. Hasonlóképpen .NET-keretrendszer API (System.IdentityModel.Tokens.SecurityTokenHandler és JwtPayload) csak lehetővé teszi összetett objektumtípus bemeneti jogcímekként. Azonban a jogcímek továbbra is karakterláncként szerializálni. Ezért nem tudjuk használni, a két a JWT jogkivonat Widevine-licenc kérelem generálásához bármelyikét.

John Sheehan [JWT NuGet-csomag](https://www.nuget.org/packages/JWT) megfelel az igényeinek, így a NuGet csomagot használni fogjuk.

Az alábbiakban van a kódot a szükséges JOGCÍMEKKEL Axinom Widevine licenckiszolgáló által megkövetelt tesztelési előállítása JWT jogkivonat esetében:

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

Licenckiszolgáló Axinom Widevine

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Megfontolandó szempontok
1. Annak ellenére, hogy AMS PlayReady-licenctovábbítási szolgáltatásra szükséges "tulajdonosi =" megelőző egy hitelesítési jogkivonatot, Axinom Widevine licenckiszolgáló nem használja fel.
2. A Axinom kommunikációs kulcs aláírási kulcs lesz. Fontos, hexadecimális karakterláncnak, de kezelni kell bájt karakterlánc sorozataként kódolás esetén. Ez a metódus ConvertHexStringToByteArray érhető el.

## <a name="retrieving-key-id"></a>Kulcsazonosítója beolvasása
Talán észrevette, hogy a kód generálásához jwt-t a token, kulcs azonosító megadása kötelező. Mivel a JWT jogkivonat kell lennie a előtt betöltése AMP player, készen áll a kulcs Azonosítójának kell létrehozni a JWT jogkivonat kérhető.

Természetesen többféleképpen is kulcs fenntartási beolvasandó azonosítóját. Például tárolhatjuk egy kulcs azonosítója és egy adatbázisban tartalmak metaadatait. Vagy kérheti le kulcsazonosító DASH MPD (Media bemutató leírása) fájlból. Az alábbi kódot a ez utóbbi van.

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
Widevine-támogatás az Azure Media Services Content Protection, mind az Azure Media Player legújabb hozzáadásával dolgozunk beállításától adatfolyamként való küldése a kötőjel + több-native-DRM (PlayReady + Widevine) mindkét AMS és Widevine-licenc PlayReady licenc szolgáltatással kiszolgáló Axinom a következő modern böngészők:

* Chrome
* Windows 10-es Microsoft Edge
* A Windows 8.1 és Windows 10 IE 11
* Is támogatott Silverlight és az Azure Media Player URL-CÍMÉRE Firefox (asztali verzió) és a Safari böngésző Mac (nem csak iOS)

A következő paramétereket kell megadni a minimális megoldás emelés Axinom Widevine licenckiszolgáló. Kulcs kivételével Azonosítóját, a többi paraméter által biztosított Axinom a Widevine-kiszolgáló beállításai alapján.

| Paraméter | Hogyan használja fel azokat |
| --- | --- |
| Kommunikációs kulcs azonosítója |Meg kell adni a jogcím "com_key_id" JWT jogkivonat értékeként (lásd: [ez](media-services-axinom-integration.md#jwt-token-generation) szakaszban). |
| Kommunikációs kulcs |A JWT jogkivonat aláíró kulcsként kell használni (lásd: [ez](media-services-axinom-integration.md#jwt-token-generation) szakaszban). |
| Kulcs kezdőérték |Kell használni a tartalomkulcsot az adott tartalom kulcs azonosítója (lásd: [ez](media-services-axinom-integration.md#content-protection) szakaszban). |
| Widevine-licenc-licenckérési URL-cím |Kell használni az adategység továbbítási házirendjét az DASH adatfolyamként történő konfigurálásával (lásd: [ez](media-services-axinom-integration.md#content-protection) szakaszban). |
| Tartalom azonosítója |Lehet, hogy tartalmazzák a JWT jogkivonat jogosultság üzenet jogcím értékét (lásd: [ez](media-services-axinom-integration.md#jwt-token-generation) szakaszban). |

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Nyugták
Ez a dokumentum kialakításához része volt a következő személyek tudomásul szeretnénk: a Axinom Kristjan Jõgi Mingfei Pozsony és Amit Rajput.

