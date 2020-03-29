---
title: Az Azure Media Services v3-as és Widevine-licencsablonja – áttekintés
description: Ez a témakör áttekintést nyújt a Widevine-licencek konfigurálásához használt Widevine licencsablonról.
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
ms.openlocfilehash: 94ce5e45a9a43e81020096ddc0a67429b286d9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705632"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Media Services v3 a Widevine licencsablonnal – áttekintés

Az Azure Media Services lehetővé teszi a tartalom titkosítását a **Google Widevine segítségével.** A Media Services a Widevine licencek kézbesítésével is rendelkezik. Az Azure Media Services API-k használatával konfigurálhatja a Widevine-licenceket. Amikor egy játékos megpróbálja lejátszani a Widevine által védett tartalmat, a rendszer kérést küld a licenckézbesítési szolgáltatásnak a licenc megszerzéséhez. Ha a licencszolgáltatás jóváhagyja a kérelmet, a szolgáltatás kiadja a licencet. A rendszer elküldi az ügyfélnek, és a megadott tartalom visszafejtésére és lejátszására szolgál.

A Widevine licenckérelem JSON-üzenetként van formázva.  

>[!NOTE]
> Létrehozhat üres üzenetet értékek nélkül, csak{}" " " " Ezután létrejön egy licencsablon az alapértelmezett értékekkel. Az alapértelmezett a legtöbb esetben működik. A Microsoft-alapú licenckézbesítési forgatókönyvek mindig az alapértelmezett értékeket kell használniuk. Ha be kell állítania a "szolgáltató" és a "content_id" értékeket, a szolgáltatónak meg kell egyeznie a Widevine hitelesítő adataival.

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
          "renew_with_usage":<renew with usage>
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
Ha létezik egy már meglévő házirend, nincs szükség a tartalomkulcs-specifikáció egyik értékének megadására sem. Az ehhez a tartalomhoz társított, már meglévő házirend a kimeneti védelem meghatározására szolgál, például a nagy sávszélességű digitális tartalomvédelem (HDCP) és a Copy General Management System (CGMS). Ha egy már meglévő házirend nincs regisztrálva a Widevine licenckiszolgálón, a tartalomszolgáltató beadhatja az értékeket a licenckérelembe.   

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
| &#46;policy_overrides can_play |Logikai, igaz vagy hamis |Azt jelzi, hogy a tartalom lejátszása engedélyezett. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;can_persist |Logikai, igaz vagy hamis |Azt jelzi, hogy a licenc offline használatra nem felejtő tárolóban is megőrződhet. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;can_renew |Logikai, igaz vagy hamis |Azt jelzi, hogy a licenc megújítása engedélyezett. Ha ez igaz, a licenc időtartama szívveréssel meghosszabbítható. Az alapértelmezett érték a false (hamis). |
| &#46;license_duration_seconds policy_overrides |int64 |Az adott licenc időablakát jelzi. A 0 érték azt jelzi, hogy az időtartam nincs korlátozva. Az alapértelmezett érték 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Azt jelzi, hogy a lejátszás engedélyezett időablaka engedélyezett. A 0 érték azt jelzi, hogy az időtartam nincs korlátozva. Az alapértelmezett érték 0. |
| &#46;playback_duration_seconds policy_overrides |int64 |A lejátszás utáni időmegtekintési időszak a licenc időtartamán belül. A 0 érték azt jelzi, hogy az időtartam nincs korlátozva. Az alapértelmezett érték 0. |
| policy_overrides&#46;&#46;&#46;renewal_server_url |sztring |A licenchez kapcsolódó összes szívverési (megújítási) kérelem a megadott URL-címre irányul. Ez a mező csak akkor használatos, ha can_renew igaz. |
| &#46;policy_overrides renewal_delay_seconds |int64 |Hány másodperccel license_start_time megújítás után az első kísérlet. Ez a mező csak akkor használatos, ha can_renew igaz. Az alapértelmezett érték 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |A későbbi licencmegújítási kérelmek közötti késleltetésmásodpercben adva, meghibásodás esetén. Ez a mező csak akkor használatos, ha can_renew igaz. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |Az az időszak, amely alatt a lejátszás a megújítási kísérlet közben is folytatódhat, de a licenckiszolgálóval kapcsolatos háttérproblémák miatt sikertelen. A 0 érték azt jelzi, hogy az időtartam nincs korlátozva. Ez a mező csak akkor használatos, ha can_renew igaz. |
| policy_overrides&#46;renew_with_usage |Logikai, igaz vagy hamis |Azt jelzi, hogy a rendszer a licenc megújításra kerül a használat megkezdésekor. Ez a mező csak akkor használatos, ha can_renew igaz. |

## <a name="session-initialization"></a>Munkamenet inicializálása
| Név | Érték | Leírás |
| --- | --- | --- |
| provider_session_token |Base64 kódolású karakterlánc |Ez a munkamenet-jogkivonat visszakerül a licencbe, és a későbbi megújítások során is létezik. A munkamenet-jogkivonat nem marad meg a munkameneteken túl. |
| provider_client_token |Base64 kódolású karakterlánc |A licencválaszban visszaküldésre válaszolandó ügyféltoken. Ha a licenckérelem ügyféljogkivonatot tartalmaz, a rendszer figyelmen kívül hagyja ezt az értéket. Az ügyféljogkivonat a licencmunkameneteken túl is megmarad. |
| override_provider_client_token |Logikai, igaz vagy hamis |Ha hamis, és a licenckérelem tartalmaz egy ügyféljogkivonatot, használja a jogkivonatot a kérelemakkor is, ha egy ügyféljogkivonat van megadva ebben a struktúrában. Ha igaz, mindig használja a struktúrában megadott jogkivonatot. |

## <a name="configure-your-widevine-license-with-net"></a>A Widevine-licenc konfigurálása a .NET segítségével 

A Media Services olyan osztályt biztosít, amely lehetővé teszi a Widevine-licenc konfigurálását. A licenc létrehozásához adja át a JSON-t a [WidevineTemplate -nek.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)

A sablon konfigurálásához a következőket teheti:

### <a name="directly-construct-a-json-string"></a>JSON-karakterlánc közvetlen összeállítása

Ez a módszer hibaveszélyes lehet. Javasoljuk, hogy más módszert használjon, amelyet a szükséges osztályok meghatározása és [a JSON -ba szerializál.](#classes)

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a>A szükséges osztályok meghatározása és a JSON-ra való szerializálás

#### <a name="define-classes"></a>Osztályok meghatározása

A következő példa a Widevine JSON-sémára leképezett osztályok definícióinak példáját mutatja be. Az osztályokat a JSON-karakterláncba való szerializáláselőtt példányosítani hozhatja.  

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

Az előző szakaszban definiált osztályok segítségével hozza létre a [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)konfigurálására használt JSON-t:

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

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="next-steps"></a>További lépések

Nézze meg, hogyan [védhet a DRM-mel](protect-with-drm.md)
