---
title: Widevine-licenc sablon áttekintése |} Microsoft Docs
description: Ez a témakör áttekintést nyújt a Widevine-licencek konfigurálásához használt Widevine-licencsablon.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: eefe82eb022584029b7afb0f2c3524d400c700bd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790481"
---
# <a name="widevine-license-template-overview"></a>Widevine-licenc sablon – áttekintés
Azure Media Services segítségével konfigurálhatja és a Google Widevine-licencek kérelmekre. Amikor a Windows Media player számára, hogy a Widevine-védelemmel ellátott tartalomhoz, a licenc beszerzésével kérelmet küldött a licenctovábbítási szolgáltatásra. Ha a szolgáltatás a kérelem jóváhagyása, a szolgáltatás a licencet ad ki. Az ügyfélnek küldött, és a megadott tartalom lejátszása és visszafejtésére szolgál.

A Widevine-licenc kérelem JSON üzenet formátuma.  

>[!NOTE]
> Létrehozhat egy üres üzenetet csak értékek nélküli "{}." A licenc sablonja majd az alapértelmezett jön létre. Az alapértelmezett működik a legtöbb esetben. Microsoft-alapú licenc-kézbesítés forgatókönyvek mindig az alapértelmezett értékeket kell használni. Állítsa be a "provider" és "content_id" értékeket kell, ha egy szolgáltató Widevine hitelesítő adatok meg kell egyeznie.

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>JSON-üzenet
| Name (Név) | Érték | Leírás |
| --- | --- | --- |
| tartalom |A Base64 kódolású karakterlánc |Az ügyfelek által küldött licenc kérelem. |
| content_id |A Base64 kódolású karakterlánc |Azonosító használható fel a kulcs azonosító és a tartalom minden content_key_specs.track_type kulcsát. |
| Szolgáltató |karakterlánc |Tartalom kulcsok és a házirendek kikereséséhez használandó. A Microsoft kulcsfontosságú kézbesítési Widevine-licenc kézbesítésre használata esetén a rendszer figyelmen kívül hagyja ezt a paramétert. |
| házirendnév |karakterlánc |Egy korábban regisztrált házirend nevét. Választható. |
| allowed_track_types |Enum |SD_ONLY vagy SD_HD. Vezérlők, amelyek tartalom kulcsok szerepelnek licencet. |
| content_key_specs |JSON tömbje struktúrák, című témakör "Tartalom kulcs specifikációk."  |Térjen vissza a tartalom billentyűket egyeztetését megbízhatatlanná levő. További információkért lásd: a szakasz "Tartalom kulcs specifikációk." Csak a allowed_track_types és content_key_specs értékek egyike adható meg. |
| use_policy_overrides_exclusively |Logikai érték, IGAZ vagy hamis |Policy_overrides által megadott házirend attribútumok használata, és hagyja el az összes korábban tárolt házirend. |
| policy_overrides |JSON struktúra, című témakör "Házirend felülbírálások." |Ez a licenc házirend beállításait.  Abban az esetben, ha ez az eszköz egy előre definiált szabályzattal rendelkezik, a megadott értékeket fogja használni. |
| session_init |JSON struktúra, című témakör "Munkamenet inicializálása." |Nem kötelező adatok kerülnek a licenc. |
| parse_only |Logikai érték, IGAZ vagy hamis |A licenc kérelem elemzi, de licenc nem jelenik meg. Azonban a licenc kérelemből értékek vannak a visszatérő válaszban. |

## <a name="content-key-specs"></a>Tartalom kulcs specifikációk
Ha egy már meglévő házirend létezik, nincs szükség van valamely értékét adja meg a tartalom kulcs beállítása. Ehhez a tartalomhoz társított már meglévő házirend segítségével határozza meg a kimeneti védelmet, például a nagy sávszélességű digitális tartalom HDCP védelmi és a példány általános felügyeleti rendszer (CGMS). Ha egy már meglévő házirend a Widevine-licenc kiszolgáló nincs regisztrálva, a tartalomszolgáltató is behelyezése az értékeket a licenc kérelmet.   

Minden egyes content_key_specs értéknek meg kell adni az összes nyomon követi, függetlenül a use_policy_overrides_exclusively lehetőséget. 

| Name (Név) | Érték | Leírás |
| --- | --- | --- |
| content_key_specs. track_type |karakterlánc |Track adattípus neve. Ha a licenc kérelem content_key_specs van beállítva, ellenőrizze, hogy minden nyomon típusokat explicit módon. Elmulasztása lejátszásához elmúlt 10 másodperc hibát eredményez. |
| content_key_specs  <br/> security_level |UInt32 |Meghatározza a lejátszás ügyfél szolgáltatás megbízhatóságára követelményeit. <br/> -Szoftver-alapú fehér beépített titkosítási szükség. <br/> -Szoftver titkosítás és egy rejtjelezett dekóder szükség. <br/> -A kulcs anyagok és a titkosítási műveletek megbízható végrehajtási hardveres biztonsági környezetben kell végrehajtani. <br/> – A titkosítás és a tartalom dekódolási megbízható végrehajtási hardveres biztonsági környezetben kell végrehajtani.  <br/> – A titkosítás, a dekódolás és az összes kezelése az adathordozót (tömörített és tömörítetlen) megbízható végrehajtási hardveres biztonsági környezetben kell kezelni. |
| content_key_specs <br/> required_output_protection.hDC |karakterlánc, HDCP_NONE, HDCP_V1, HDCP_V2 egyik |Azt jelzi, hogy szükség-e HDCP. |
| content_key_specs <br/>kulcs |A Base64-<br/>kódolású karakterlánc |Az a szám használandó tartalomkulcsot. Ha meg van adva, a track_type vagy key_id megadása kötelező. A tartalomszolgáltató a tartalomkulcsot a track ahelyett, hogy a Widevine licenckiszolgáló generálni, vagy kereshet meg egy kulcs szúrjon használhatja ezt a beállítást. |
| content_key_specs.key_id |A Base64 kódolású karakterlánc bináris, 16 bájt |A kulcs egyedi azonosítója. |

## <a name="policy-overrides"></a>Házirend felülbírálása
| Name (Név) | Érték | Leírás |
| --- | --- | --- |
| policy_overrides. can_play |Logikai érték, IGAZ vagy hamis |Azt jelzi, hogy a tartalom lejátszását engedélyezett. Az alapértelmezett érték a false (hamis). |
| policy_overrides. can_persist |Logikai érték, IGAZ vagy hamis |Azt jelzi, hogy a licenc előfordulhat, hogy sikerült megőrizni a nem felejtő tárolási kapcsolat nélküli használatra. Az alapértelmezett érték a false (hamis). |
| policy_overrides. can_renew |Logikai érték, IGAZ vagy hamis |Azt jelzi, hogy ez a licenc megújítása. Amennyiben az értéke igaz, a licenc időtartama szívverés lehet kiterjeszteni. Az alapértelmezett érték a false (hamis). |
| policy_overrides. license_duration_seconds |Int64 |Azt jelzi, hogy az adott licenccsoport időablakot. A 0 érték azt jelzi, hogy nincs-e korlát időtartama. Alapértelmezett érték 0. |
| policy_overrides. rental_duration_seconds |Int64 |Azt jelzi, a időszak, amíg a lejátszás engedélyezett. A 0 érték azt jelzi, hogy nincs-e korlát időtartama. Alapértelmezett érték 0. |
| policy_overrides. playback_duration_seconds |Int64 |A követő lejátszás elindítása a licenc időtartama belül megtekintése ablakot. A 0 érték azt jelzi, hogy nincs-e korlát időtartama. Alapértelmezett érték 0. |
| policy_overrides. renewal_server_url |karakterlánc |Ez a licenc vonatkozó összes szívverés (megújítási) a megadott URL-CÍMRE irányítja. Csak akkor, ha can_renew igaz használja ezt a mezőt. |
| policy_overrides. renewal_delay_seconds |Int64 |Hány másodperc után license_start_time megújítási először megkísérlése előtt. Csak akkor, ha can_renew igaz használja ezt a mezőt. Alapértelmezett érték 0. |
| policy_overrides. renewal_retry_interval_seconds |Int64 |Adja meg a késleltetés között további licenc megújítási kérelmeket, hiba esetén. Csak akkor, ha can_renew igaz használja ezt a mezőt. |
| policy_overrides. renewal_recovery_duration_seconds |Int64 |Az ablakot, az melyik lejátszás lehet folytatni, amíg a megújítási kísérlet történik, de a licenckiszolgáló háttér-problémák miatt sikertelen. A 0 érték azt jelzi, hogy nincs-e korlát időtartama. Csak akkor, ha can_renew igaz használja ezt a mezőt. |
| policy_overrides. renew_with_usage |Logikai érték, IGAZ vagy hamis |Azt jelzi, hogy a licenc küldeni megújítási, használat indításakor. Csak akkor, ha can_renew igaz használja ezt a mezőt. |

## <a name="session-initialization"></a>Munkamenet-inicializálása
| Name (Név) | Érték | Leírás |
| --- | --- | --- |
| provider_session_token |A Base64 kódolású karakterlánc |A munkamenet jogkivonatából átadott vissza a licenc, és ezt követő megújításokat szerepel. A munkamenet jogkivonatából munkamenetek túl nem is. |
| provider_client_token |A Base64 kódolású karakterlánc |Ügyfél jogkivonatának visszaállítása a licenc válaszul küldendő. Ha a licenc kérelem ügyfél jogkivonatot tartalmaz, a rendszer figyelmen kívül hagyja ezt az értéket. Az ügyfél jogkivonatának továbbra is fennáll, licenc munkamenetek túl. |
| override_provider_client_token |Logikai érték, IGAZ vagy hamis |Ha hamis, valamint a licenc kérelem olyan ügyfél-jogkivonatot tartalmaz, akkor is, ha egy ügyfél jogkivonatának volt megadva, ez a struktúra a használja a jogkivonatot a kérelemből. Igaz értéke esetén mindig használja a lexikális elem van megadva a struktúrában. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>A Widevine-licencek konfigurálása .NET típusok használatával
A Media Services .NET API-k, amelyek segítségével konfigurálhatja a Widevine-licenceket biztosít. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>A Media Services .NET SDK osztályok
A következő osztályok láthatók a következő típusú meghatározásai:

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>Példa
A következő példa bemutatja, hogyan egy egyszerű Widevine-licenc konfigurálása a .NET API-k segítségével:

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még
[A dynamic common encryption PlayReady és/vagy Widevine használatára](media-services-protect-with-playready-widevine.md)

