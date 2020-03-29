---
title: Widevine licencsablon áttekintése | Microsoft dokumentumok
description: Ez a témakör áttekintést nyújt a Widevine-licencek konfigurálásához használt Widevine licencsablonról.
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
ms.openlocfilehash: c7511279e66ab598e4ae3c26f053915b7393b39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978390"
---
# <a name="widevine-license-template-overview"></a>Widevine licencsablon – áttekintés 
Az Azure Media Services segítségével konfigurálhatja és kérheti a Google Widevine-licenceket. Amikor a játékos megpróbálja lejátszani a Widevine által védett tartalmat, a rendszer kérelmet küld a licenckézbesítési szolgáltatásnak, hogy szerezzen licencet. Ha a licencszolgáltatás jóváhagyja a kérelmet, a szolgáltatás kiadja a licencet. A rendszer elküldi az ügyfélnek, és a megadott tartalom visszafejtésére és lejátszására szolgál.

A Widevine licenckérelem JSON-üzenetként van formázva.  

>[!NOTE]
> Létrehozhat üres üzenetet értékek nélkül, csak{}" " " " Ezután létrejön egy licencsablon az alapértelmezett értékekkel. Az alapértelmezett a legtöbb esetben működik. A Microsoft-alapú licenckézbesítési forgatókönyvek mindig az alapértelmezett értékeket kell használniuk. Ha be kell állítania a "szolgáltató" és a "content_id" értékeket, a szolgáltatónak meg kell egyeznie a Widevine hitelesítő adataival.

    {  
       "payload": "<license challenge>",
       "content_id": "<content id>" 
       "provider": "<provider>"
       "allowed_track_types": "<types>",
       "content_key_specs": [  
          {  
             "track_type": "<track type 1>"
          },
          {  
             "track_type": "<track type 2>"
          },
          …
       ],
       "policy_overrides": {  
          "can_play": <can play>,
          "can persist": <can persist>,
          "can_renew": <can renew>,
          "rental_duration_seconds": <rental duration>,
          "playback_duration_seconds": <playback duration>,
          "license_duration_seconds": <license duration>,
          "renewal_recovery_duration_seconds": <renewal recovery duration>,
          "renewal_server_url": "<renewal server url>",
          "renewal_delay_seconds": <renewal delay>,
          "renewal_retry_interval_seconds": <renewal retry interval>,
          "renew_with_usage": <renew with usage>
       }
    }

## <a name="json-message"></a>JSON-üzenet
| Név | Érték | Leírás |
| --- | --- | --- |
| payload |Base64 kódolású karakterlánc |Az ügyfél által küldött licenckérelem. |
| content_id |Base64 kódolású karakterlánc |Az egyes content_key_specs.track_type kulcsazonosítójának és tartalomkulcsának származtatására használt azonosító. |
| Szolgáltató |sztring |A tartalomkulcsok és -házirendek megkeresésére szolgál. Ha a Microsoft kulcskézbesítést a Widevine licenckézbesítéshez használja, a rendszer figyelmen kívül hagyja ezt a paramétert. |
| policy_name |sztring |Korábban regisztrált házirend neve. Választható. |
| allowed_track_types |Enum |SD_ONLY vagy SD_HD. Azt szabályozza, hogy mely tartalomkulcsok szerepeljenek a licencben. |
| content_key_specs |A JSON-struktúrák tömbje, lásd a "Tartalomkulcs specifikációi" című részt.  |Finomabb vezérlő, amelyen a tartalomkulcsokat vissza kell adni. További információt a "Tartalomkulcs-specifikációk" című szakaszban talál. Csak az egyik allowed_track_types és content_key_specs érték adható meg. |
| use_policy_overrides_exclusively |Logikai, igaz vagy hamis |Használja a policy_overrides által megadott házirendattribútumokat, és hagyja ki az összes korábban tárolt házirendet. |
| policy_overrides |JSON-struktúra, lásd a "Házirend felülbírálása" című szakaszt. |A licenc házirend-beállításai.  Abban az esetben, ha ez az eszköz egy előre definiált házirenddel rendelkezik, ezeket a megadott értékeket használja a program. |
| session_init |JSON-struktúra, lásd a "Munkamenet inicializálása" című szakaszt. |A választható adatok átkerülnek a licencbe. |
| parse_only |Logikai, igaz vagy hamis |A licenckérelem elemzésre kerül, de nincs licenc kiadása. A licenckérelemből származó értékek azonban a válaszban jelennek meg. |

## <a name="content-key-specs"></a>Tartalomkulcs-specifikációk
Ha létezik egy már létező házirend, nincs szükség a tartalomkulcs-specifikáció egyik értékének megadására sem. Az ehhez a tartalomhoz társított, már meglévő házirend a kimeneti védelem meghatározására szolgál, például a nagy sávszélességű digitális tartalomvédelem (HDCP) és a Copy General Management System (CGMS). Ha egy már meglévő házirend nincs regisztrálva a Widevine licenckiszolgálón, a tartalomszolgáltató beadhatja az értékeket a licenckérelembe.   

Minden content_key_specs értéket meg kell adni az összes számhoz, függetlenül a use_policy_overrides_exclusively beállítástól. 

| Név | Érték | Leírás |
| --- | --- | --- |
| content_key_specs. track_type |sztring |A szám típusának neve. Ha content_key_specs van megadva a licenckérelemben, győződjön meg arról, hogy az összes műsorszámot explicit módon adja meg. Ennek elmulasztása azt eredményezi, hogy nem játszik le 10 másodpercen túl. |
| content_key_specs  <br/> security_level |uint32 |A lejátszás ügyfélrobusztussági követelményeit határozza meg. <br/> - Szoftveralapú fehér doboz kriptográfia szükséges. <br/> - Szoftver kriptográfia és egy homályos dekóder van szükség. <br/> - A kulcsanyag- és kriptográfiai műveleteket hardveresen támogatott megbízható végrehajtási környezetben kell végrehajtani. <br/> - A tartalom titkosítását és dekódolását hardveresen támogatott megbízható végrehajtási környezetben kell végrehajtani.  <br/> - A kriptográfia, dekódolás, és minden kezelése az adathordozó (tömörített és tömörítetlen) kell kezelni a hardver-támogatott megbízható végrehajtási környezetben. |
| content_key_specs <br/> required_output_protection.hdc |HDCP_NONE HDCP_V1 HDCP_V2 |Azt jelzi, hogy szükség van-e HDCP-re. |
| content_key_specs <br/>kulcs |Alap64-<br/>kódolt karakterlánc |A zeneszámhoz használandó tartalomkulcs. Ha meg van adva, a track_type vagy key_id szükséges. A tartalomszolgáltató ezzel a beállítással injektálhatja a zeneszám tartalomkulcsát ahelyett, hogy a Widevine licenckiszolgáló számára engedélyezne egy kulcsot, vagy kikereshetne. |
| content_key_specs,key_id |Base64 kódolású karakterlánc bináris, 16 bájt |A kulcs egyedi azonosítója |

## <a name="policy-overrides"></a>Házirend-felülbírálások
| Név | Érték | Leírás |
| --- | --- | --- |
| policy_overrides. can_play |Logikai, igaz vagy hamis |Azt jelzi, hogy a tartalom lejátszása engedélyezett. Az alapértelmezett érték a false (hamis). |
| policy_overrides. can_persist |Logikai, igaz vagy hamis |Azt jelzi, hogy a licenc offline használatra nem felejtő tárolóban is megőrződhet. Az alapértelmezett érték a false (hamis). |
| policy_overrides. can_renew |Logikai, igaz vagy hamis |Azt jelzi, hogy a licenc megújítása engedélyezett. Ha ez igaz, a licenc időtartama szívveréssel meghosszabbítható. Az alapértelmezett érték a false (hamis). |
| policy_overrides. license_duration_seconds |int64 |Az adott licenc időablakát jelzi. A 0 érték azt jelzi, hogy az időtartam nincs korlátozva. Az alapértelmezett érték 0. |
| policy_overrides. rental_duration_seconds |int64 |Azt jelzi, hogy a lejátszás engedélyezett időablaka engedélyezett. A 0 érték azt jelzi, hogy az időtartam nincs korlátozva. Az alapértelmezett érték 0. |
| policy_overrides. playback_duration_seconds |int64 |A lejátszás utáni időmegtekintési időszak a licenc időtartamán belül. A 0 érték azt jelzi, hogy az időtartam nincs korlátozva. Az alapértelmezett érték 0. |
| policy_overrides. renewal_server_url |sztring |A licenchez kapcsolódó összes szívverési (megújítási) kérelem a megadott URL-címre lesz irányítva. Ez a mező csak akkor használatos, ha can_renew igaz. |
| policy_overrides. renewal_delay_seconds |int64 |Hány másodperccel license_start_time megújítás után az első kísérlet. Ez a mező csak akkor használatos, ha can_renew igaz. Az alapértelmezett érték 0. |
| policy_overrides. renewal_retry_interval_seconds |int64 |A későbbi licencmegújítási kérelmek közötti késleltetésmásodpercben adva, meghibásodás esetén. Ez a mező csak akkor használatos, ha can_renew igaz. |
| policy_overrides. renewal_recovery_duration_seconds |int64 |Az az időszak, amely alatt a lejátszás a megújítási kísérlet közben is folytatódhat, de a licenckiszolgálóval kapcsolatos háttérproblémák miatt sikertelen. A 0 érték azt jelzi, hogy az időtartam nincs korlátozva. Ez a mező csak akkor használatos, ha can_renew igaz. |
| policy_overrides. renew_with_usage |Logikai, igaz vagy hamis |Azt jelzi, hogy a rendszer a licenc megújításra kerül a használat megkezdésekor. Ez a mező csak akkor használatos, ha can_renew igaz. |

## <a name="session-initialization"></a>Munkamenet inicializálása
| Név | Érték | Leírás |
| --- | --- | --- |
| provider_session_token |Base64 kódolású karakterlánc |Ez a munkamenet-jogkivonat visszakerül a licencbe, és a későbbi megújítások során is létezik. A munkamenet-jogkivonat nem marad meg a munkameneteken túl. |
| provider_client_token |Base64 kódolású karakterlánc |A licencválaszban visszaküldésre válaszolandó ügyféltoken. Ha a licenckérelem ügyféljogkivonatot tartalmaz, a rendszer figyelmen kívül hagyja ezt az értéket. Az ügyféljogkivonat a licencmunkameneteken túl is megmarad. |
| override_provider_client_token |Logikai, igaz vagy hamis |Ha hamis, és a licenckérelem tartalmaz egy ügyféljogkivonatot, használja a jogkivonatot a kérelemakkor is, ha egy ügyféljogkivonat van megadva ebben a struktúrában. Ha igaz, mindig használja a struktúrában megadott jogkivonatot. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>A Widevine-licencek konfigurálása .NET-típusokkal
A Media Services .NET API-kat biztosít, amelyekkel konfigurálhatja a Widevine-licenceket. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>A Media Services .NET SDK-ban meghatározott osztályok
A következő osztályok az alábbi típusok definícióit akövetkezők:

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
A következő példa bemutatja, hogyan konfigurálhat a .NET API-kat egy egyszerű Widevine-licenc konfigurálásához:

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

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még
[PlayReady és/vagy Widevine Dynamic Common Encryption használata](media-services-protect-with-playready-widevine.md)

