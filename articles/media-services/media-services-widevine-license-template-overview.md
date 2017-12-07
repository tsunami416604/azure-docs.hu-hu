---
title: "Widevine-licenc sablon áttekintése |} Microsoft Docs"
description: "Ez a témakör áttekintést a Widevine-licencsablon, Widevine-licencek konfigurálására használhatók."
author: juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 68d519cd36d41728f57419cd6cecd2a79d65a4af
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="widevine-license-template-overview"></a>Widevine-licenc sablon – áttekintés
Az Azure Media Services lehetővé teszi, hogy konfigurálja és Widevine-licencek kérelem. Ha a végfelhasználó player próbál Widevine védett tartalom lejátszása, a licenc beszerzésével kérelmet küldött a kézbesítési szolgáltatás. Ha a szolgáltatás a kérelem jóváhagyása, a licenc, amely az ügyfélnek küldött és fejti vissza és a megadott tartalom lejátszásához használható ad ki.

Widevine-licenc kérelem JSON üzenet formátuma.  

>[!NOTE]
> Ha szeretné, létrehozhat egy üres értékek nélküli csak "{}", és egy licenc-sablon létrehozza az összes alapértelmezett. Az alapértelmezett működik a legtöbb esetben. Például a MS-alapú licenc kézbesítési esetében, amely alapértelmezett mindig kell lennie. Állítsa be a "provider" és "content_id" értékeket kell, ha egy szolgáltató Google Widevine hitelesítő adatok meg kell egyeznie.

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
| Név | Érték | Leírás |
| --- | --- | --- |
| tartalom |A Base64 kódolású karakterlánc |Az ügyfelek által küldött licenc kérelem. |
| content_id |A Base64 kódolású karakterlánc |Használható KeyId(s) és a tartalom kulcsoknak az egyes content_key_specs.track_type azonosítója. |
| Szolgáltató |Karakterlánc |Tartalom kulcsok és a házirendek kikereséséhez használandó. MS kulcs kézbesítési Widevine-licenc kézbesítésre használata esetén a rendszer figyelmen kívül hagyja ezt a paramétert. |
| házirendnév |Karakterlánc |Egy korábban regisztrált házirend nevét. Optional |
| allowed_track_types |Enum |SD_ONLY vagy SD_HD. Vezérlők, amelyek kulcsok tartalom szerepelnie kell egy licencet |
| content_key_specs |a tömb JSON struktúrák, lásd: **tartalom kulcs specifikációk** alatt |Egy egyeztetését megbízhatatlanná vezérlő a tartalmat a kulcsok való visszatéréshez. Tartalom kulcs Spec alatt talál információt.  Allowed_track_types és content_key_specs csak egy adható meg. |
| use_policy_overrides_exclusively |Logikai érték. IGAZ vagy HAMIS eredményt ad |Policy_overrides által megadott házirend attribútumok használata, és hagyja el az összes korábban tárolt házirend. |
| policy_overrides |JSON-struktúrát, lásd: **házirend felülírja** alatt |Ez a licenc házirend beállításait.  Abban az esetben, ha ez az eszköz egy előre definiált szabályzattal rendelkezik, a megadott értékeket fogja használni. |
| session_init |JSON-struktúrát, lásd: **munkamenet inicializálási** alatt |Választható adatok kapott licencet. |
| parse_only |Logikai érték. IGAZ vagy HAMIS eredményt ad |A licenc kérelem elemzi, de licenc nem jelenik meg. Azonban értékek a licenc-kérelmet a válaszban visszaadott képernyő. |

## <a name="content-key-specs"></a>Tartalom kulcs specifikációk
Ha egy már meglévő házirend létezik, nincs szükség van valamely értékét adja meg a tartalom kulcs specifikációi.  A már meglévő házirend ehhez a tartalomhoz társított használandó a kimeneti védelmet, például HDCP és CGMS határozza meg.  Ha egy már meglévő házirend a Widevine-licenc kiszolgáló nincs regisztrálva, a tartalomszolgáltató is behelyezése az értékeket a licenc kérelmet.   

Minden egyes content_key_specs összes nyomon követi, függetlenül a beállítás use_policy_overrides_exclusively kell adni. 

| Név | Érték | Leírás |
| --- | --- | --- |
| content_key_specs. track_type |Karakterlánc |Track adattípus neve. Ha a licenc kérelem content_key_specs van beállítva, ellenőrizze, hogy minden nyomon típusokat explicit módon. Elmulasztása eredményez hiba lejátszásához elmúlt 10 másodperc. |
| content_key_specs  <br/> security_level |UInt32 |Meghatározza a lejátszás ügyfél szolgáltatás megbízhatóságára követelményeit. <br/> 1 - szoftveres whitebox titkosítási szükség. <br/> 2 - szoftver titkosítási és egy rejtjelezett dekóder szükség. <br/> 3 - a hardveres biztonsági megbízható végrehajtási környezetekben a kulcsokat tárol, és a titkosítási műveleteket kell elvégezni. <br/> 4 – a titkosítás és a tartalom dekódolási hardver megbízható végrehajtási biztonsági környezetben kell végrehajtani.  <br/> 5 - a titkosítási, dekódolási és az adathordozó (tömörített és tömörítetlen) összes kezelési hardver megbízható végrehajtási biztonsági környezetben kell kezelni. |
| content_key_specs <br/> required_output_protection.hDC |karakterlánc - egyikét: HDCP_NONE, HDCP_V1, HDCP_V2 |Azt jelzi, hogy szükség van-e a HDCP |
| content_key_specs <br/>kulcs |a Base64 <br/>kódolású karakterlánc |Az a szám használandó tartalomkulcsot. Ha meg van adva, a track_type vagy key_id megadása kötelező.  Ez a beállítás lehetővé teszi, hogy a tartalomszolgáltató szúrjon a tartalomkulcsot a track ahelyett, hogy a Widevine licenckiszolgáló létrehozni, vagy keresési kulcs. |
| content_key_specs.key_id |Base64 kódolású karakterlánc bináris, 16 bájt |A kulcs egyedi azonosítója. |

## <a name="policy-overrides"></a>Házirend felülbírálása
| Név | Érték | Leírás |
| --- | --- | --- |
| policy_overrides. can_play |Logikai érték. IGAZ vagy HAMIS eredményt ad |Azt jelzi, hogy a tartalom lejátszását engedélyezett. Alapértelmezett értéke false. |
| policy_overrides. can_persist |Logikai érték. IGAZ vagy HAMIS eredményt ad |Azt jelzi, hogy a licenc előfordulhat, hogy sikerült-e megőrizni a nem felejtő kapcsolat nélküli használatra. Alapértelmezett értéke false. |
| policy_overrides. can_renew |logikai változó értéke IGAZ vagy HAMIS eredményt ad |Azt jelzi, hogy ez a licenc megújítása. Amennyiben az értéke igaz, a licenc időtartama szívverés lehet kiterjeszteni. Alapértelmezett értéke false. |
| policy_overrides. license_duration_seconds |Int64 |Azt jelzi, hogy az adott licenccsoport időablakot. A 0 érték azt jelzi, hogy nincs-e korlát időtartama. Alapértelmezett érték 0. |
| policy_overrides. rental_duration_seconds |Int64 |Azt jelzi, a időszak, amíg a lejátszás engedélyezett. A 0 érték azt jelzi, hogy nincs-e korlát időtartama. Alapértelmezett érték 0. |
| policy_overrides. playback_duration_seconds |Int64 |Az idő után a lejátszás elindítása a licenc időtartama belül megtekintése ablak. A 0 érték azt jelzi, hogy nincs-e korlát időtartama. Alapértelmezett érték 0. |
| policy_overrides. renewal_server_url |Karakterlánc |Ez a licenc vonatkozó összes szívverés (megújítási) kell arra, hogy a megadott URL-cím. Ha igaz can_renew csak használja ezt a mezőt. |
| policy_overrides. renewal_delay_seconds |Int64 |Hány másodperc után license_start_time, megújítási először megkísérlése előtt. Ha igaz can_renew csak használja ezt a mezőt. Az alapértelmezett érték: 0 |
| policy_overrides. renewal_retry_interval_seconds |Int64 |Adja meg a késleltetés között további licenc megújítási kérelmeket, hiba esetén. Ha igaz can_renew csak használja ezt a mezőt. |
| policy_overrides. renewal_recovery_duration_seconds |Int64 |Az ablak az időt, amelyben a lejátszás lehet folytatni, amíg a megújítási időtartama a licenckiszolgáló megkísérelt, de sikertelen, mert a háttérrendszer problémáit. A 0 érték azt jelzi, hogy nincs-e korlát időtartama. Ha igaz can_renew csak használja ezt a mezőt. |
| policy_overrides. renew_with_usage |logikai változó értéke IGAZ vagy HAMIS eredményt ad |Azt jelzi, hogy a licenc küldik a megújítási használat indításakor. Ha igaz can_renew csak használja ezt a mezőt. |

## <a name="session-initialization"></a>Munkamenet-inicializálása
| Név | Érték | Leírás |
| --- | --- | --- |
| provider_session_token |A Base64 kódolású karakterlánc |A munkamenet jogkivonatából átadott vissza a licenc, és ezt követő megújításokat van jelen.  A munkameneti jogkivonat nem fogja megőrizni munkamenetek túl. |
| provider_client_token |A Base64 kódolású karakterlánc |Ügyfél jogkivonatának visszaállítása a licenc válaszul küldendő.  Ha a licenc kérelem ügyfél jogkivonatot tartalmaz, a rendszer figyelmen kívül hagyja ezt az értéket. Az ügyfél jogkivonatának licenc munkamenetek túl fogja is. |
| override_provider_client_token |Logikai érték. IGAZ vagy HAMIS eredményt ad |Ha hamis, valamint a licenc kérelem olyan ügyfél-jogkivonatot tartalmaz, akkor is, ha egy ügyfél jogkivonatának volt megadva, ez a struktúra a használja a jogkivonatot a kérelemből.  Igaz értéke esetén mindig használja a lexikális elem van megadva a struktúrában. |

## <a name="configure-your-widevine-licenses-using-net-types"></a>A .NET-típus használatával Widevine-licencek konfigurálása
A Media Services .NET API-k, amelyek lehetővé teszik a Widevine-licencek konfigurálása biztosít. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>A Media Services .NET SDK osztályok
Az alábbiakban a következő típusú definíciókat.

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
A következő példa bemutatja, hogyan egy egyszerű Widevine-licenc konfigurálása a .NET API-k segítségével.

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
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[PlayReady és/vagy Widevine Dynamic Common Encryption használatával](media-services-protect-with-playready-widevine.md)

