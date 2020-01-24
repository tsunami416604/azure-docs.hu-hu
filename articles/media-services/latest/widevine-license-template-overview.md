---
title: A Azure Media Services v3 és a Widevine-licenc sablonjának áttekintése
description: Ez a témakör áttekintést nyújt a Widevine-licencek konfigurálásához használt Widevine-licencekről.
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705632"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>A Media Services v3 és a Widevine-licenc sablonjának áttekintése

A Azure Media Services lehetővé teszi a tartalom titkosítását a **Google Widevine**. A Media Services egy szolgáltatást is biztosít a Widevine-licencek kézbesítéséhez. A Widevine-licencek konfigurálásához Azure Media Services API-kat használhat. Amikor egy játékos megpróbál lejátszani a Widevine-védelemmel ellátott tartalmat, a rendszer elküldi a licenc kézbesítési szolgáltatásának a licenc beszerzésére irányuló kérelmet. Ha a licencelési szolgáltatás jóváhagyja a kérelmet, a szolgáltatás kiadja a licencet. A rendszer elküldi az ügyfélnek, és a megadott tartalom visszafejtésére és lejátszására szolgál.

A Widevine-licencszerződés JSON-üzenetként van formázva.  

>[!NOTE]
> Létrehozhat egy érték nélküli üres üzenetet, amely csak "{}" lehet. Ezt követően a rendszer alapértelmezésekkel hozza létre a licenceket. Az alapértelmezett működés a legtöbb esetben. A Microsoft-alapú licencek kézbesítési forgatókönyvei mindig az alapértelmezett értékeket használják. Ha a "provider" és a "content_id" értéket kell beállítania, a szolgáltatónak meg kell egyeznie a Widevine hitelesítő adataival.

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

| Név | Value (Díj) | Leírás |
| --- | --- | --- |
| adattartalom |Base64 kódolású karakterlánc |Az ügyfél által eljuttatott licencelési kérelem. |
| content_id |Base64 kódolású karakterlánc |Az egyes content_key_specshoz tartozó kulcs-azonosító és a tartalmi kulcs származtatása céljából használt azonosító. track_type. |
| szolgáltató |sztring |A tartalmi kulcsok és szabályzatok keresésére szolgál. Ha a Microsoft Key Delivery szolgáltatás a Widevine-licencek kézbesítéséhez használatos, ezt a paramétert a rendszer figyelmen kívül hagyja. |
| policy_name |sztring |Egy korábban regisztrált szabályzat neve. Választható. |
| allowed_track_types |Enum |SD_ONLY vagy SD_HD. Meghatározza, hogy mely tartalmi kulcsok szerepeljenek a licencekben. |
| content_key_specs |A JSON-struktúrák tömbje a "tartalmi kulcs specifikációi" című szakaszban található.  |A visszaadni kívánt tartalmi kulcsokat tartalmazó finomabb vezérlőelem. További információt a "tartalmi kulcsra vonatkozó specifikációk" című szakaszban talál. A allowed_track_types és a content_key_specs értékek közül csak az egyik adható meg. |
| use_policy_overrides_exclusively |Boolean, True vagy FALSE |Használja a policy_overrides által megadott házirend-attribútumokat, és hagyja ki az összes korábban tárolt házirendet. |
| policy_overrides |JSON-struktúra, tekintse meg a "szabályzat-felülbírálások" című szakaszt. |A licenchez tartozó házirend-beállítások.  Abban az esetben, ha ez az eszköz előre definiált szabályzattal rendelkezik, a rendszer ezeket a megadott értékeket használja. |
| session_init |JSON-struktúra: "munkamenet inicializálásának" szakasza. |A licenc nem kötelező. |
| parse_only |Boolean, True vagy FALSE |A rendszer elemzi a licencszerződést, de nem ad ki licencet. A válaszban azonban visszaadja a licencszerződés értékeit. |

## <a name="content-key-specs"></a>Tartalmi kulcs specifikációi
Ha már létezik meglévő szabályzat, nem kell megadnia a tartalmi kulcs specifikációjában szereplő értékeket. Az ehhez a tartalomhoz társított előre megadott szabályzat a kimeneti védelem meghatározására szolgál, például a nagy sávszélességű digitális Content Protection (HDCP) és a másolási általános felügyeleti rendszer (CGMS) használatával. Ha egy korábban már létező szabályzat nincs regisztrálva a Widevine-kiszolgálón, a tartalomszolgáltató az értékeket a licencszerződésbe szúrhatja be.   

Minden content_key_specs értéket meg kell adni az összes pályán, a use_policy_overrides_exclusively lehetőségtől függetlenül. 

| Név | Value (Díj) | Leírás |
| --- | --- | --- |
| content_key_specs. track_type |sztring |A követési típus neve. Ha content_key_specs van megadva a licencelési kérelemben, ügyeljen arra, hogy explicit módon adja meg az összes nyomkövetési típust. Ennek elmulasztása miatt nem sikerült lejátszani az elmúlt 10 másodpercet. |
| content_key_specs  <br/> security_level |UInt32 |Meghatározza a lejátszáshoz szükséges ügyfél-megbízhatósági követelményeket. <br/> – A szoftveres alapú, fehér dobozos titkosítás szükséges. <br/> – A szoftveres titkosítás és a megzavarodott dekóder szükséges. <br/> – A kulcsfontosságú anyagokat és titkosítási műveleteket egy hardveres megbízható végrehajtási környezetben kell végrehajtani. <br/> – A tartalom titkosítását és visszafejtését hardveres megbízható végrehajtási környezetben kell végrehajtani.  <br/> – A titkosítást, a dekódolást és az adathordozó összes kezelését (tömörített és tömörítetlen) a hardveres megbízhatóságú végrehajtási környezetben kell kezelni. |
| content_key_specs <br/> required_output_protection. HDC |karakterlánc, az egyik HDCP_NONE, HDCP_V1, HDCP_V2 |Azt jelzi, hogy a HDCP kötelező-e. |
| content_key_specs <br/>kulcs |Base64<br/>kódolt sztring |A nyomon követéshez használandó tartalmi kulcs. Ha meg van adva, a track_type vagy key_id megadása kötelező. A tartalomszolgáltató ezzel a kapcsolóval szúrhatja be a nyomkövetési kulcsot, ahelyett, hogy a Widevine-licenckiszolgáló létrehoz vagy megkeres egy kulcsot. |
| content_key_specs. key_id |Base64 kódolású karakterlánc, bináris, 16 bájt |A kulcs egyedi azonosítója. |

## <a name="policy-overrides"></a>Szabályzat felülbírálásai
| Név | Value (Díj) | Leírás |
| --- | --- | --- |
| policy_overrides&#46;can_play |Boolean, True vagy FALSE |Azt jelzi, hogy a tartalom lejátszása engedélyezett. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;can_persist |Boolean, True vagy FALSE |Azt jelzi, hogy a licenc az offline használat érdekében nem felejtő tárolóban maradhat. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;can_renew |Boolean, True vagy FALSE |Azt jelzi, hogy a licenc megújítása engedélyezett. Igaz értéke esetén a licenc időtartama a szívveréssel bővíthető. Az alapértelmezett érték a false (hamis). |
| policy_overrides&#46;license_duration_seconds |Int64 |Megadja az adott licenc időtartományát. A 0 érték azt jelzi, hogy az időtartam nem korlátozható. Az alapértelmezett érték a 0. |
| policy_overrides&#46;rental_duration_seconds |Int64 |Azt jelzi, hogy a lejátszás közben engedélyezett-e az időablak. A 0 érték azt jelzi, hogy az időtartam nem korlátozható. Az alapértelmezett érték a 0. |
| policy_overrides&#46;playback_duration_seconds |Int64 |A megtekintési idő a lejátszás után a licenc időtartamán belül kezdődik. A 0 érték azt jelzi, hogy az időtartam nem korlátozható. Az alapértelmezett érték a 0. |
| policy_overrides&#46;renewal_server_url |sztring |A licenchez tartozó összes szívverési (megújítási) kérelem a megadott URL-címre van irányítva. Ez a mező csak akkor használható, ha a can_renew értéke igaz. |
| policy_overrides&#46;renewal_delay_seconds |Int64 |A megújítás első megkísérlése után hány másodpercig license_start_time. Ez a mező csak akkor használható, ha a can_renew értéke igaz. Az alapértelmezett érték a 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |Int64 |A későbbi licenc-megújítási kérelmek közötti késleltetést adja meg, meghibásodás esetén. Ez a mező csak akkor használható, ha a can_renew értéke igaz. |
| policy_overrides&#46;renewal_recovery_duration_seconds |Int64 |Az az időszak, amelyben a lejátszás folytatódni fog, miközben a rendszer megkísérli a megújítást, de a licenckiszolgálóval kapcsolatos háttérbeli problémák miatt nem sikerült. A 0 érték azt jelzi, hogy az időtartam nem korlátozható. Ez a mező csak akkor használható, ha a can_renew értéke igaz. |
| policy_overrides&#46;renew_with_usage |Boolean, True vagy FALSE |Azt jelzi, hogy a rendszer a használat megkezdése után elküldi a licencet a megújításhoz. Ez a mező csak akkor használható, ha a can_renew értéke igaz. |

## <a name="session-initialization"></a>Munkamenet inicializálása
| Név | Value (Díj) | Leírás |
| --- | --- | --- |
| provider_session_token |Base64 kódolású karakterlánc |Ezt a munkamenet-jogkivonatot visszaadja a licenc, és a későbbi megújításokban van. A munkamenet-jogkivonat nem marad meg a munkameneteken kívül. |
| provider_client_token |Base64 kódolású karakterlánc |Az ügyfél jogkivonata, amelyet vissza kell küldenie a licencelési válaszban. Ha a licencszerződés tartalmaz egy ügyfél-jogkivonatot, a rendszer figyelmen kívül hagyja ezt az értéket. Az ügyfél-jogkivonat a licencelési munkameneteken kívül is fennáll. |
| override_provider_client_token |Boolean, True vagy FALSE |Ha a False (hamis) értékre van beállítva, és a licencszerződés tartalmaz egy ügyfél-jogkivonatot, akkor is használja a jogkivonatot a kérelemből, ha meg van adva egy ügyfél-jogkivonat Ha az értéke igaz, mindig használja az ebben a struktúrában megadott jogkivonatot. |

## <a name="configure-your-widevine-license-with-net"></a>A Widevine-licenc konfigurálása a .NET-tel 

A Media Services egy olyan osztályt biztosít, amely lehetővé teszi a Widevine-licencek konfigurálását. A licenc létrehozásához továbbítsa a JSON-t a [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

A sablon konfigurálásához a következőket teheti:

### <a name="directly-construct-a-json-string"></a>JSON-karakterlánc közvetlen létrehozása

Ez a metódus hibákra hajlamos lehet. Ajánlott más módszert használni a [szükséges osztályok definiálása és a JSON-szerializálása](#classes)című témakörben leírtak szerint.

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a id="classes"></a>A szükséges osztályok definiálása és a JSON-szerializálás

#### <a name="define-classes"></a>Osztályok definiálása

Az alábbi példa egy példát mutat be, amely a JSON-Widevine leképezett osztályok definícióit mutatja be. Az osztályok a JSON-karakterláncba való szerializálás előtt hozhatók létre.  

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

Az előző szakaszban meghatározott osztályok használata a [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)konfigurálásához használt JSON létrehozásához:

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

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="next-steps"></a>Következő lépések

További [információ a DRM elleni védelemről](protect-with-drm.md)
