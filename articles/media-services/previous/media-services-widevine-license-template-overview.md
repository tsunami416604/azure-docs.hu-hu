---
title: Widevine-licencsablon áttekintése |} A Microsoft Docs
description: Ez a témakör áttekintést a Widevine-licencsablon, amely a Widevine-licencek konfigurálására szolgál.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d0bb72361e1bff3615f6785ac4c91a10ea773498
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312971"
---
# <a name="widevine-license-template-overview"></a>Widevine-licencsablon áttekintése 
Az Azure Media Services segítségével a konfigurálása és a Google Widevine-licenceket kérnek. Ha Widevine-védelemmel ellátott tartalom lejátszása a Media player próbál, egy kérelem érkezik a szolgáltatásra vonatkozó licencet beszereznie. A szolgáltatás jóváhagyja a kérést, ha a szolgáltatásproblémák a licencet. Ez van az ügyfélnek küldött, és a megadott tartalom lejátszása és visszafejtésére szolgál.

A Widevine-licenc kérelem egy JSON-ként van formázva.  

>[!NOTE]
> Létrehozhat egy üres üzenetet csak értékkel nem rendelkező "{}." Majd egy licenc-sablon létrehozása az alapértelmezett értékeket. Alapértelmezés szerint a legtöbb esetben működik. A Microsoft-alapú licenckézbesítés forgatókönyvek mindig használja az alapértelmezett értékeket. Állítsa be a "szolgáltató" és "content_id" érték van szüksége, ha egy szolgáltató Widevine hitelesítő adatait meg kell egyeznie.

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
| hasznos adat |Base64-kódolású karakterlánc |Az ügyfelek által küldött licenc kérelem. |
| content_id |Base64-kódolású karakterlánc |A kulcs azonosítója és a tartalom létrehozására használt azonosító minden content_key_specs.track_type kulcsával. |
| szolgáltató |sztring |Tartalom kulcsok és szabályzatok keresésére használt. A Microsoft kulcskézbesítési használata a Widevine-licenckézbesítés, a rendszer figyelmen kívül hagyja ezt a paramétert. |
| policy_name |sztring |Egy korábban regisztrált házirend nevét. Választható. |
| allowed_track_types |Enum |SD_ONLY vagy SD_HD. Kulcsok tartalmakat vezérlők licenc szerepelnek. |
| content_key_specs |JSON-tömb struktúrák, tekintse meg a következő szakaszt: "Content key specifikációihoz."  |Részletesebben vezérlő a melyik tartalomkulcs való visszatéréshez. További információkért lásd: a szakasz "Content key specifikációihoz." Csak a allowed_track_types és content_key_specs értékek egyike adható meg. |
| use_policy_overrides_exclusively |Logikai érték true vagy FALSE (hamis) |Házirend attribútumainak policy_overrides által megadott használja, és hagyja ki az összes korábban tárolt házirend. |
| policy_overrides |JSON struktúra, című témakör "Házirend-felülbírálások." |Ez a licenc szabályzat beállításai.  Abban az esetben, ha az adategység előre definiált szabályzattal rendelkezik, a rendszer a megadott értékeket használja. |
| session_init |JSON struktúra, című témakör "Munkamenet inicializálása." |Nem kötelező adatok kerülnek a licencet. |
| parse_only |Logikai érték true vagy FALSE (hamis) |A licenc kérelem elemzi, de a licenc nem jelenik meg. Azonban a licenc kérelem értékeket a válaszban visszaadott. |

## <a name="content-key-specs"></a>Tartalom főbb adatait tartalmazza
Egy már létező irányelveit, ha ott nem adja meg az értékeket a tartalom kulcs beállítása nincs szükség. A már létező szabályzatot ehhez a tartalomhoz társított kimeneti védelme, például a nagy sávszélességet igénylő digitális tartalmak védelme (HDCP) és a példány általános felügyeleti rendszert (CGMS) meghatározására szolgál. Ha egy már létező házirend nincs regisztrálva a Widevine-licenc kiszolgálóval, a tartalomszolgáltató is behelyezése az értékeket a licenc kérelmet.   

Minden egyes content_key_specs értéknek meg kell adni az összes nyomon követi, függetlenül a use_policy_overrides_exclusively lehetőséget. 

| Name (Név) | Érték | Leírás |
| --- | --- | --- |
| content_key_specs. track_type |sztring |Track típusnév. Ha content_key_specs van megadva, a licenc-kérelemben, ügyeljen arra, hogy adja meg az összes nyomon típusokat explicit módon. Amennyiben ezt nem sikerült lejátszani elmúlt 10 másodperc eredményez. |
| content_key_specs  <br/> security_level |UInt32 |A lejátszás vonatkozó ügyfélkövetelmények robusztusság határozza meg. <br/> -Fehér-box cryptography szoftveralapú megadása kötelező. <br/> -Szoftver titkosítás és a egy rejtjelezett dekóder szükség. <br/> – A kulcsfontosságú anyagok és a titkosítási műveletek egy megbízható végrehajtási hardveres biztonsági környezetben kell végrehajtani. <br/> – A titkosítás és a tartalom dekódolás egy megbízható végrehajtási hardveres biztonsági környezetben kell végrehajtani.  <br/> – A titkosítás, a dekódolás és az összes kezelése a media (tömörített és tömörítetlen) egy megbízható végrehajtási hardveres biztonsági környezetben kell kezelni. |
| content_key_specs <br/> required_output_protection.hdc |karakterlánc, HDCP_NONE, HDCP_V1, HDCP_V2 egyik |Azt jelzi, hogy szükség-e HDCP. |
| content_key_specs <br/>kulcs |Base64-<br/>a kódolt karakterlánc |A track használandó tartalomkulcsot. Ha meg van adva, a track_type vagy key_id szükség. A tartalomszolgáltató Ez a beállítás segítségével beszúrása a tartalomkulcsot a követése helyett, ami lehetővé teszi a Widevine-licenckiszolgáló készítése vagy kereshet meg egy kulcsot. |
| content_key_specs.key_id |Base-64 kódolású karakterláncot bináris, 16 bájtos |A kulcs egyedi azonosítója. |

## <a name="policy-overrides"></a>A házirend felülbírálások
| Name (Név) | Érték | Leírás |
| --- | --- | --- |
| policy_overrides. can_play |Logikai érték true vagy FALSE (hamis) |Azt jelzi, hogy a tartalom lejátszását használata engedélyezett. Az alapértelmezett érték a false (hamis). |
| policy_overrides. can_persist |Logikai érték true vagy FALSE (hamis) |Azt jelzi, hogy a licenc előfordulhat, hogy őrizhető meg, az offline használatra felejtő storage. Az alapértelmezett érték a false (hamis). |
| policy_overrides. can_renew |Logikai érték true vagy FALSE (hamis) |Azt jelzi, hogy engedélyezett-e a megújítását, ezt a licencet. Ha az értéke igaz, a licenc időtartamának szívverés lehet kiterjeszteni. Az alapértelmezett érték a false (hamis). |
| policy_overrides. license_duration_seconds |Int64 |Azt jelzi, hogy az adott licenc időtartományából. 0 érték azt jelzi, hogy nincs-e az időtartam nincs korlátozva. Alapértelmezett érték a 0. |
| policy_overrides. rental_duration_seconds |Int64 |Azt jelzi, az időtartomány, a lejátszás pedig engedélyezett. 0 érték azt jelzi, hogy nincs-e az időtartam nincs korlátozva. Alapértelmezett érték a 0. |
| policy_overrides. playback_duration_seconds |Int64 |A lejátszás elindítása a licenc időtartamának belül követő megtekintése ablakot. 0 érték azt jelzi, hogy nincs-e az időtartam nincs korlátozva. Alapértelmezett érték a 0. |
| policy_overrides. renewal_server_url |sztring |A licencek minden Szívveréses (megújítás) kérések a megadott URL-címre irányítja. Csak akkor, ha teljesül can_renew használja ezt a mezőt. |
| policy_overrides. renewal_delay_seconds |Int64 |Hány másodperc után license_start_time megújítása először megkísérlése előtt. Csak akkor, ha teljesül can_renew használja ezt a mezőt. Alapértelmezett érték a 0. |
| policy_overrides. renewal_retry_interval_seconds |Int64 |Adja meg a késleltetés között későbbi licenc megújítási kérések hiba esetén. Csak akkor, ha teljesül can_renew használja ezt a mezőt. |
| policy_overrides. renewal_recovery_duration_seconds |Int64 |Az ablak, mely lejátszás közben kísérlet történik a megújítási továbbra is az idő, de a licenckiszolgáló háttér-problémák miatt volt sikertelen. 0 érték azt jelzi, hogy nincs-e az időtartam nincs korlátozva. Csak akkor, ha teljesül can_renew használja ezt a mezőt. |
| policy_overrides. renew_with_usage |Logikai érték true vagy FALSE (hamis) |Azt jelzi, hogy a licenc küldeni megújítási, használati indításakor. Csak akkor, ha teljesül can_renew használja ezt a mezőt. |

## <a name="session-initialization"></a>Munkamenet inicializálása
| Name (Név) | Érték | Leírás |
| --- | --- | --- |
| provider_session_token |Base64-kódolású karakterlánc |A munkamenet-jogkivonat vissza a licenc átadott, és az ezt követő megújítás létezik. A munkamenet-jogkivonat munkamenetek kívül nem tárol. |
| provider_client_token |Base64-kódolású karakterlánc |Ügyfél token vissza a licenc-válasz küldése. Ha a licenc kérelem egy ügyfél jogkivonatot tartalmaz, a rendszer figyelmen kívül hagyja ezt az értéket. Az ügyfél jogkivonat továbbra is fennáll, licenc-munkamenetek túl. |
| override_provider_client_token |Logikai érték true vagy FALSE (hamis) |Ha hamis és a licenc kérés tartalmaz egy ügyfél jogkivonat, használja a kérelemből származó jogkivonat akkor is, ha egy ügyfél token ebben a struktúrában megadott. Igaz értéke esetén mindig használja az ebben a struktúrában megadott jogkivonat. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>A Widevine-licencek konfigurálása .NET Tulajdonságtípusokat használatával
A Media Services .NET API-k, amelyek segítségével konfigurálhatja a Widevine-licenceket biztosít. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>A Media Services .NET SDK osztályok
A következő osztályok ezek a típusok definíciói:

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
Az alábbi példa bemutatja, hogyan használható a .NET API-k egyszerű Widevine-licenc konfigurálása:

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
[PlayReady és/vagy Widevine dynamic common encryption használata](media-services-protect-with-playready-widevine.md)

