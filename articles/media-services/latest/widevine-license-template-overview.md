---
title: Az Azure Media Services szolgáltatással végzett Widevine-licenc licencsablon áttekintése |} A Microsoft Docs
description: Ez a témakör áttekintést a Widevine-licencsablon, amely a Widevine-licencek konfigurálására szolgál.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: c6fc363a7ab9de215647e371a9d3c846f8688bd5
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548704"
---
# <a name="widevine-license-template-overview"></a>Widevine-licencsablon áttekintése 

Az Azure Media Services lehetővé teszi a tartalmak **Google Widevine**. Media Services a Widevine-licencek modult is tartalmaz. Az Azure Media Services API-k segítségével konfigurálhatja a Widevine-licencek. Ha Widevine-védelemmel ellátott tartalom lejátszása a lejátszó próbál, a licenc beszerzéséhez kérelmet küldött a licenctovábbítási szolgáltatása. A szolgáltatás jóváhagyja a kérést, ha a szolgáltatásproblémák a licencet. Ez van az ügyfélnek küldött, és a megadott tartalom lejátszása és visszafejtésére szolgál.

A Widevine-licenc kérelem egy JSON-ként van formázva.  

>[!NOTE]
> Létrehozhat egy üres üzenetet csak értékkel nem rendelkező "{}." Majd egy licenc-sablon létrehozása az alapértelmezett értékeket. Alapértelmezés szerint a legtöbb esetben működik. A Microsoft-alapú licenckézbesítés forgatókönyvek mindig használja az alapértelmezett értékeket. Állítsa be a "szolgáltató" és "content_id" érték van szüksége, ha egy szolgáltató Widevine hitelesítő adatait meg kell egyeznie.

    {  
       "payload":"<license challenge>",
       "content_id": "<content id>"
       "provider": "<provider>"
       "allowed_track_types":"<types>",
       "content_key_specs":[  
          {  
             "track_type":"<track type 1>"
          },
          {  
             "track_type":"<track type 2>"
          },
          …
       ],
       "policy_overrides":{  
          "can_play":<can play>,
          "can persist":<can persist>,
          "can_renew":<can renew>,
          "rental_duration_seconds":<rental duration>,
          "playback_duration_seconds":<playback duration>,
          "license_duration_seconds":<license duration>,
          "renewal_recovery_duration_seconds":<renewal recovery duration>,
          "renewal_server_url":"<renewal server url>",
          "renewal_delay_seconds":<renewal delay>,
          "renewal_retry_interval_seconds":<renewal retry interval>,
          "renew_with_usage”:<renew with usage>
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
Ha egy már meglévő szabályzat létezik, nincs nincs szükség, ha meg szeretné adni az értékeket a tartalom kulcs beállítása. A már meglévő szabályzatot ehhez a tartalomhoz társított kimeneti védelme, például a nagy sávszélességet igénylő digitális tartalmak védelme (HDCP) és a példány általános felügyeleti rendszert (CGMS) meghatározására szolgál. Ha egy már meglévő házirend nincs regisztrálva a Widevine-licenc kiszolgálóval, a tartalomszolgáltató is behelyezése az értékeket a licenc kérelmet.   

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
| policy_overrides&#46;can_play |Logikai érték true vagy FALSE (hamis) |Azt jelzi, hogy a tartalom lejátszását használata engedélyezett. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;can_persist |Logikai érték true vagy FALSE (hamis) |Azt jelzi, hogy a licenc előfordulhat, hogy őrizhető meg, az offline használatra felejtő storage. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;can_renew |Logikai érték true vagy FALSE (hamis) |Azt jelzi, hogy engedélyezett-e a megújítását, ezt a licencet. Ha az értéke igaz, a licenc időtartamának szívverés lehet kiterjeszteni. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;license_duration_seconds |Int64 |Azt jelzi, hogy az adott licenc időtartományából. 0 érték azt jelzi, hogy nincs-e az időtartam nincs korlátozva. Alapértelmezett érték a 0. |
| policy_overrides&#46;rental_duration_seconds |Int64 |Azt jelzi, az időtartomány, a lejátszás pedig engedélyezett. 0 érték azt jelzi, hogy nincs-e az időtartam nincs korlátozva. Alapértelmezett érték a 0. |
| policy_overrides&#46;playback_duration_seconds |Int64 |A lejátszás elindítása a licenc időtartamának belül követő megtekintése ablakot. 0 érték azt jelzi, hogy nincs-e az időtartam nincs korlátozva. Alapértelmezett érték a 0. |
| policy_overrides&#46;renewal_server_url |sztring |A licencek minden Szívveréses (megújítás) kérések a megadott URL-címet van átirányítva. Csak akkor, ha teljesül can_renew használja ezt a mezőt. |
| policy_overrides&#46;renewal_delay_seconds |Int64 |Hány másodperc után license_start_time megújítása először megkísérlése előtt. Csak akkor, ha teljesül can_renew használja ezt a mezőt. Alapértelmezett érték a 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |Int64 |Adja meg a késleltetés között későbbi licenc megújítási kérések hiba esetén. Csak akkor, ha teljesül can_renew használja ezt a mezőt. |
| policy_overrides&#46;renewal_recovery_duration_seconds |Int64 |Az ablak, mely lejátszás közben kísérlet történik a megújítási továbbra is az idő, de a licenckiszolgáló háttér-problémák miatt volt sikertelen. 0 érték azt jelzi, hogy nincs-e az időtartam nincs korlátozva. Csak akkor, ha teljesül can_renew használja ezt a mezőt. |
| policy_overrides&#46;renew_with_usage |Logikai érték true vagy FALSE (hamis) |Azt jelzi, hogy a licenc küldeni megújítási, használati indításakor. Csak akkor, ha teljesül can_renew használja ezt a mezőt. |

## <a name="session-initialization"></a>Munkamenet inicializálása
| Name (Név) | Érték | Leírás |
| --- | --- | --- |
| provider_session_token |Base64-kódolású karakterlánc |A munkamenet-jogkivonat vissza a licenc átadott, és az ezt követő megújítás létezik. A munkamenet-jogkivonat munkamenetek kívül nem tárol. |
| provider_client_token |Base64-kódolású karakterlánc |Ügyfél token vissza a licenc-válasz küldése. Ha a licenc kérelem egy ügyfél jogkivonatot tartalmaz, a rendszer figyelmen kívül hagyja ezt az értéket. Az ügyfél jogkivonat továbbra is fennáll, licenc-munkamenetek túl. |
| override_provider_client_token |Logikai érték true vagy FALSE (hamis) |Ha hamis és a licenc kérés tartalmaz egy ügyfél jogkivonat, használja a kérelemből származó jogkivonat akkor is, ha egy ügyfél token ebben a struktúrában megadott. Igaz értéke esetén mindig használja az ebben a struktúrában megadott jogkivonat. |

## <a name="configure-your-widevine-license-with-net"></a>A Widevine-licenc lekérhető konfigurálása a .NET használatával 

A Media Services biztosít egy osztályt, amely lehetővé teszi a Widevine-licenc konfigurálását. A licenc létrehozására, adja át a JSON- [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

A sablon konfigurálásához a következőket teheti:

### <a name="directly-construct-a-json-string"></a>Közvetlenül hozhat létre egy JSON-karakterlánc

Előfordulhat, hogy ez a módszer hibalehetőséget magában rejtő. Javasoljuk, hogy más módszert használja, ismertetett [definiálása szükséges osztályokat és szerializálni a JSON-ná](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a id="classes"></a> Adja meg a szükséges osztályokat és szerializálható JSON-ná

#### <a name="define-classes"></a>Osztályok definiálása

Az alábbi példa bemutatja a definíciók, amelyet a Widevine JSON-sémájában leképezése egy példát. Az osztályok példányosítható mielőtt azokat a JSON-karakterláncot.  

```csharp
public class PolicyOverrides
{
    public bool CanPlay { get; set; }
    public bool CanPersist { get; set; }
    public bool CanRenew { get; set; }
    public int RentalDurationSeconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int PlaybackDurationSeconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int LicenseDurationSeconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
}

public class ContentKeySpec
{
    public string TrackType { get; set; }
    public int SecurityLevel { get; set; }
    public OutputProtection RequiredOutputProtection { get; set; }
}

public class OutputProtection
{
    public string HDCP { get; set; }
}

public class WidevineTemplate
{
    public string AllowedTrackTypes { get; set; }
    public ContentKeySpec[] ContentKeySpecs { get; set; }
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>A licenc konfigurálása

Konfigurálásához használt JSON létrehozásához használja az előző szakaszban megadott [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="next-steps"></a>További lépések

Tekintse meg, hogy miként [DRM védelme](protect-with-drm.md)
