---
title: Az Azure Media Services szolgáltatással végzett Widevine-licenc sablon áttekintése |} Microsoft Docs
description: Ez a témakör áttekintést nyújt a Widevine-licencek konfigurálásához használt Widevine-licencsablon.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: juliako
ms.openlocfilehash: e3af5efd253458401c13f6174d9567f932482eb0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132915"
---
# <a name="widevine-license-template-overview"></a>Widevine-licenc sablon – áttekintés

Azure Media Services segítségével konfigurálhatja és a Google Widevine-licencek kérelmekre. Amikor a Windows Media player számára, hogy a Widevine-védelemmel ellátott tartalomhoz, a licenc beszerzésével kérelmet küldött a licenctovábbítási szolgáltatásra. Ha a szolgáltatás a kérelem jóváhagyása, a szolgáltatás a licencet ad ki. Az ügyfélnek küldött, és a megadott tartalom lejátszása és visszafejtésére szolgál.

A Widevine-licenc kérelem JSON üzenet formátuma.  

>[!NOTE]
> Létrehozhat egy üres üzenetet csak értékek nélküli "{}." A licenc sablonja majd az alapértelmezett jön létre. Az alapértelmezett működik a legtöbb esetben. Microsoft-alapú licenc-kézbesítés forgatókönyvek mindig az alapértelmezett értékeket kell használni. Állítsa be a "provider" és "content_id" értékeket kell, ha egy szolgáltató Widevine hitelesítő adatok meg kell egyeznie.

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
| hasznos adat |A Base64 kódolású karakterlánc |Az ügyfelek által küldött licenc kérelem. |
| content_id |A Base64 kódolású karakterlánc |Azonosító használható fel a kulcs azonosító és a tartalom minden content_key_specs.track_type kulcsát. |
| Szolgáltató |sztring |Tartalom kulcsok és a házirendek kikereséséhez használandó. A Microsoft kulcsfontosságú kézbesítési Widevine-licenc kézbesítésre használata esetén a rendszer figyelmen kívül hagyja ezt a paramétert. |
| házirendnév |sztring |Egy korábban regisztrált házirend nevét. Választható. |
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
| content_key_specs. track_type |sztring |Track adattípus neve. Ha a licenc kérelem content_key_specs van beállítva, ellenőrizze, hogy minden nyomon típusokat explicit módon. Elmulasztása lejátszásához elmúlt 10 másodperc hibát eredményez. |
| content_key_specs  <br/> security_level |UInt32 |Meghatározza a lejátszás ügyfél szolgáltatás megbízhatóságára követelményeit. <br/> -Szoftver-alapú fehér beépített titkosítási szükség. <br/> -Szoftver titkosítás és egy rejtjelezett dekóder szükség. <br/> -A kulcs anyagok és a titkosítási műveletek megbízható végrehajtási hardveres biztonsági környezetben kell végrehajtani. <br/> – A titkosítás és a tartalom dekódolási megbízható végrehajtási hardveres biztonsági környezetben kell végrehajtani.  <br/> – A titkosítás, a dekódolás és az összes kezelése az adathordozót (tömörített és tömörítetlen) megbízható végrehajtási hardveres biztonsági környezetben kell kezelni. |
| content_key_specs <br/> required_output_protection.hDC |karakterlánc, HDCP_NONE, HDCP_V1, HDCP_V2 egyik |Azt jelzi, hogy szükség-e HDCP. |
| content_key_specs <br/>kulcs |A Base64-<br/>kódolású karakterlánc |Az a szám használandó tartalomkulcsot. Ha meg van adva, a track_type vagy key_id megadása kötelező. A tartalomszolgáltató a tartalomkulcsot a track ahelyett, hogy a Widevine licenckiszolgáló generálni, vagy kereshet meg egy kulcs szúrjon használhatja ezt a beállítást. |
| content_key_specs.key_id |A Base64 kódolású karakterlánc bináris, 16 bájt |A kulcs egyedi azonosítója. |

## <a name="policy-overrides"></a>Házirend felülbírálása
| Name (Név) | Érték | Leírás |
| --- | --- | --- |
| policy_overrides&#46;can_play |Logikai érték, IGAZ vagy hamis |Azt jelzi, hogy a tartalom lejátszását engedélyezett. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;can_persist |Logikai érték, IGAZ vagy hamis |Azt jelzi, hogy a licenc előfordulhat, hogy sikerült megőrizni a nem felejtő tárolási kapcsolat nélküli használatra. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;can_renew |Logikai érték, IGAZ vagy hamis |Azt jelzi, hogy ez a licenc megújítása. Amennyiben az értéke igaz, a licenc időtartama szívverés lehet kiterjeszteni. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;license_duration_seconds |Int64 |Azt jelzi, hogy az adott licenccsoport időablakot. A 0 érték azt jelzi, hogy nincs-e korlát időtartama. Alapértelmezett érték 0. |
| policy_overrides&#46;rental_duration_seconds |Int64 |Azt jelzi, a időszak, amíg a lejátszás engedélyezett. A 0 érték azt jelzi, hogy nincs-e korlát időtartama. Alapértelmezett érték 0. |
| policy_overrides&#46;playback_duration_seconds |Int64 |A követő lejátszás elindítása a licenc időtartama belül megtekintése ablakot. A 0 érték azt jelzi, hogy nincs-e korlát időtartama. Alapértelmezett érték 0. |
| policy_overrides&#46;renewal_server_url |sztring |Ez a licenc vonatkozó összes szívverés (megújítási) a megadott URL-CÍMRE irányítja. Csak akkor, ha can_renew igaz használja ezt a mezőt. |
| policy_overrides&#46;renewal_delay_seconds |Int64 |Hány másodperc után license_start_time megújítási először megkísérlése előtt. Csak akkor, ha can_renew igaz használja ezt a mezőt. Alapértelmezett érték 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |Int64 |Adja meg a késleltetés között további licenc megújítási kérelmeket, hiba esetén. Csak akkor, ha can_renew igaz használja ezt a mezőt. |
| policy_overrides&#46;renewal_recovery_duration_seconds |Int64 |Az ablakot, az melyik lejátszás lehet folytatni, amíg a megújítási kísérlet történik, de a licenckiszolgáló háttér-problémák miatt sikertelen. A 0 érték azt jelzi, hogy nincs-e korlát időtartama. Csak akkor, ha can_renew igaz használja ezt a mezőt. |
| policy_overrides&#46;renew_with_usage |Logikai érték, IGAZ vagy hamis |Azt jelzi, hogy a licenc küldeni megújítási, használat indításakor. Csak akkor, ha can_renew igaz használja ezt a mezőt. |

## <a name="session-initialization"></a>Munkamenet-inicializálása
| Name (Név) | Érték | Leírás |
| --- | --- | --- |
| provider_session_token |A Base64 kódolású karakterlánc |A munkamenet jogkivonatából átadott vissza a licenc, és ezt követő megújításokat szerepel. A munkamenet jogkivonatából munkamenetek túl nem is. |
| provider_client_token |A Base64 kódolású karakterlánc |Ügyfél jogkivonatának visszaállítása a licenc válaszul küldendő. Ha a licenc kérelem ügyfél jogkivonatot tartalmaz, a rendszer figyelmen kívül hagyja ezt az értéket. Az ügyfél jogkivonatának továbbra is fennáll, licenc munkamenetek túl. |
| override_provider_client_token |Logikai érték, IGAZ vagy hamis |Ha hamis, valamint a licenc kérelem olyan ügyfél-jogkivonatot tartalmaz, akkor is, ha egy ügyfél jogkivonatának volt megadva, ez a struktúra a használja a jogkivonatot a kérelemből. Igaz értéke esetén mindig használja a lexikális elem van megadva a struktúrában. |

## <a name="configure-your-widevine-license-with-net"></a>A Widevine-licenc .NET konfigurálása 

A Media Services biztosít egy osztály, amely lehetővé teszi a Widevine-licenc konfigurálását. A licenc összeállításához, adja át a JSON [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

A sablon konfigurálása a következőket teheti:

1.  Közvetlenül szerkezet/kódba foglalni a JSON-karakterláncban (amely lehet hibalehetőséget);

    ```csharp
        ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
    };
    ```

2.  Szerkezet osztályok szükséges e JSON attribútumok leképezése tulajdonságokkal rendelkező, és mielőtt azokat a JSON-karakterláncban példányának őket. Az alábbiakban van egy példa az ilyen osztályok és hogyan példányként létrehozott és szerializálni.

    ```csharp
    public class policy_overrides
    {
        public bool can_play { get; set; }
        public bool can_persist { get; set; }
        public bool can_renew { get; set; }
        public int rental_duration_seconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
        public int playback_duration_seconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
        public int license_duration_seconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    }

    public class content_key_spec
    {
        public string track_type { get; set; }
        public int security_level { get; set; }
        public output_protection required_output_protection { get; set; }
    }

    public class output_protection
    {
        public string hdcp { get; set; }
    }

    public class widevine_template
    {
        public string allowed_track_types { get; set; }
        public content_key_spec[] content_key_specs { get; set; }
        public policy_overrides policy_overrides { get; set; }
    }
    ```

### <a name="configure-the-license"></a>A licenc konfigurálása

JSON konfigurálásához használt létrehozásához használja az előző szakaszban megadott [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

```csharp
void ConfigureLicense()
{
    widevine_template objwidevine_template = new widevine_template()
    {
        allowed_track_types = "SD_HD",
        content_key_specs = new content_key_spec[]
        {
            new content_key_spec()
            {
                track_type = "SD",
                security_level = 1,
                required_output_protection = new output_protection()
                {
                hdcp = "HDCP_V2"
                }
            }
        },
        policy_overrides = new policy_overrides()
        {
            can_play = true,
            can_persist = true,
            can_renew = false,
            license_duration_seconds = 2592000,
            playback_duration_seconds = 10800,
            rental_duration_seconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(objwidevine_template)
    };
}
```

## <a name="next-steps"></a>További lépések

[Áttekintés](content-protection-overview.md)