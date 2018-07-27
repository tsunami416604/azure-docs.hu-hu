---
title: Az Azure Service Fabric-fürt beállításainak módosítása |} A Microsoft Docs
description: Ez a cikk ismerteti a háló beállításokat és a fabric frissítési szabályzatok, amelyek testre szabhatók.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2018
ms.author: aljo
ms.openlocfilehash: 56c904c0da87c3b0023fe5c9a125a359e23678dc
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263810"
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>A Service Fabric-fürt beállításait és a háló házirend testreszabása
Ez a dokumentum bemutatja, hogyan szabhatja testre a különböző hálóbeállítások, és a fabric frissítési szabályzatának a Service Fabric-fürt számára. Testre szabható azokhoz a [az Azure portal](https://portal.azure.com) vagy egy Azure Resource Manager-sablon használatával.

> [!NOTE]
> Nem minden beállítás a portálon érhetők el. Abban az esetben az alábbiakban egy beállítás nem érhető el a portálon keresztül testre szabhatja azt egy Azure Resource Manager-sablon használatával.
> 

## <a name="description-of-the-different-upgrade-policies"></a>A különböző frissítési szabályzatok leírása

- **A dinamikus** – egy dinamikus konfiguráció módosításainak, bármely Service Fabric folyamatokhoz vagy a szolgáltatás gazdagép folyamatainak folyamat újraindítása nem váltják ki. 
- **Statikus** – statikus konfiguráció módosításainak hatására a Service Fabric csomópont újraindítása annak érdekében, hogy a módosítás felhasználását. A csomópontok a szolgáltatás újraindul.
- **NotAllowed** – ezek a beállítások nem módosíthatók. Ezek a beállítások megköveteli, hogy a fürt semmisíteni módosítása, és létrehoztunk egy új fürtöt. 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Testre szabhatja a Resource Manager-sablonok használatával fürtbeállítások
Az alábbi lépések bemutatják, hogyan egy új beállítás hozzáadása *MaxDiskQuotaInMB* , a *diagnosztikai* szakaszban.

1. Nyissa meg a következőt: https://resources.azure.com
2. Keresse meg az előfizetés által bővítése **előfizetések** -> **\<előfizetését >** -> **resourceGroups**  ->   **\<Az erőforráscsoport >** -> **szolgáltatók** -> **Microsoft.ServiceFabric**  ->  **fürtök** -> **\<saját fürt neve >**
3. A jobb felső sarokban, válassza ki **olvasási/írási.**
4. Válassza ki **szerkesztése** és frissítheti a `fabricSettings` JSON-elem és a egy új elem hozzáadása:

```
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

A következő fabric testreszabható, beállítások szakasz szerint vannak rendezve.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|sztring, alapértelmezett értéke "None"|Statikus| Ez nem ellenőrzi a kiszolgálótanúsítványt; a kérelem sikeres. Tekintse meg a konfigurációs ServiceCertificateThumbprints távoli, amely a fordított proxy megbízható tanúsítványok ujjlenyomatai vesszővel elválasztott listáját. Tekintse meg a távoli, amely a fordított proxy megbízható tanúsítványok a tulajdonos neve és kiállító ujjlenyomata ServiceCommonNameAndIssuer config. |
|BodyChunkSize |Uint, az alapértelmezett érték 16384 |Dinamikus| Az adattömbök a szervezet olvasásához használt bájtban biztosító méretét. |
|CrlCheckingFlag|uint, alapértelmezett 0x40000000 |Dinamikus| Alkalmazás/szolgáltatás tanúsítványlánc érvényesítésének; jelzők például a CRL ellenőrzése 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY beállítás 0-ra letiltja a visszavont tanúsítványok Listájának ellenőrzése teljes támogatott értékeinek listáján szerződését, CertGetCertificateChain dwFlags szerint: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Időre (másodpercben). alapértelmezett érték a 120 |Dinamikus|Adja meg az időtartam másodpercben.  Az alapértelmezett kérés időtúllépése megtalálható a http-kérelmekre a http-app Gateway feldolgozása folyamatban. |
|ForwardClientCertificate|bool, alapértelmezett érték a hamis|Dinamikus|Ha értéke HAMIS, a fordított proxy nem kéri az ügyféltanúsítványt. Ha értéke igaz, a fordított proxy lesz az SSL-kézfogás során az ügyféltanúsítványt kérhetnek és továbbítja a base64-kódolású PEM formátumú karakterláncot a szolgáltatást az X-ügyfél-Certificate.The service nevű fejléc sikertelen lehet a kérelmet a megfelelő állapotkód: Miután a tanúsítvány-adatok vizsgálatával. Ha ez igaz, és az ügyfél nem jelent-e a tanúsítvány, fordított proxy továbbítása egy üres fejlécet, és lehetővé teszik a szolgáltatást, így kezelni. Fordított proxy transzparens réteg fog működni.|
|GatewayAuthCredentialType |sztring, alapértelmezett értéke "None" |Statikus| Azt jelzi, hogy a rendszer használatát a http app gateway végpont érvényes értékek a biztonsági hitelesítő adatok típusát "None / X 509. |
|GatewayX509CertificateFindType |sztring, alapértelmezett "FindByThumbprint" |Dinamikus| Azt jelzi, hogy a tárolóban GatewayX509CertificateStoreName támogatott értéke által meghatározott tanúsítvány keresése: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | sztring, alapértelmezett érték a "" |Dinamikus| Keresési szűrő keresse meg a http-app gateway tanúsítványt használt érték. Ezt a tanúsítványt a https-végpont konfigurálva van, és a szolgáltatások által szükség esetén az alkalmazás identitását is használható. Első; FindValue keresése Ha nem létezik; FindValueSecondary keresése. |
|GatewayX509CertificateFindValueSecondary | sztring, alapértelmezett érték a "" |Dinamikus|Keresési szűrő keresse meg a http-app gateway tanúsítványt használt érték. Ezt a tanúsítványt a https-végpont konfigurálva van, és a szolgáltatások által szükség esetén az alkalmazás identitását is használható. Első; FindValue keresése Ha nem létezik; FindValueSecondary keresése.|
|GatewayX509CertificateStoreName |sztring, alapértelmezett érték a "Saját" |Dinamikus| Alkalmazás-Gateway http-tanúsítványt tartalmazó X.509 tanúsítvány tároló neve. |
|HttpRequestConnectTimeout|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(5)|Dinamikus|Adja meg az időtartam másodpercben.  A connect timeout megtalálható a http-alkalmazás átjáróról küldött http-kérelmekre.  |
|IgnoreCrlOfflineError|bool, alapértelmezett értéke igaz|Dinamikus|CRL offline alkalmazások vagy szolgáltatások tanúsítvány-ellenőrzés hibát figyelmen kívül kell-e. |
|IsEnabled |Bool, alapértelmezett érték a False (hamis) |Statikus| Engedélyezi vagy letiltja a HttpApplicationGateway. HttpApplicationGateway alapértelmezés szerint le van tiltva, és ez a konfiguráció kell állítani az engedélyezéshez. |
|NumberOfParallelOperations | Uint, alapértelmezett érték az 5000-es |Statikus|Olvasási és az http-kiszolgáló üzenetsorban közzéteendő száma. Ez vezérli, amelyeket teljesíteni tudja az HttpGateway egyidejű kérelmek száma. |
|RemoveServiceResponseHeaders|sztring, alapértelmezett "dátum; Kiszolgáló"|Statikus|Pontosvessző / vesszővel elválasztott listája a válaszfejlécek, melyeket a rendszer eltávolítja a szolgáltatás válasza; való továbbítása előtt az ügyfél. Ha üres karakterláncot; értékre van állítva Adja át a szolgáltatás által visszaadott összes fejléc-van. azaz ne írja felül a dátum és a kiszolgáló |
|ResolveServiceBackoffInterval |Idő (másodpercben), alapértelmezett érték az 5 |Dinamikus|Adja meg az időtartam másodpercben.  Lehetővé teszi az alapértelmezett visszatartási időköz egy sikertelen újrapróbálkozás előtt oldja meg a szolgáltatási művelet. |
|SecureOnlyMode|bool, alapértelmezett érték a hamis|Dinamikus| SecureOnlyMode: true: fordított Proxy szolgáltatásokhoz, amelyek biztonságos végpontok közzététele csak továbbítja. FALSE (hamis): a fordított Proxy biztonságos/nem biztonságos végpontok kérelmeket továbbíthatja.  |
|ServiceCertificateThumbprints|sztring, alapértelmezett érték a ""|Dinamikus|A vesszővel tagolt listája, amelyek a fordított proxy megbízható távoli tanúsítványok ujjlenyomatai.  |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, alapértelmezett értéke None|Dinamikus| A távoli tanúsítványokat, amelyek a fordított proxy megbízható tárgy neve és kiállító ujjlenyomata.|

## <a name="backuprestoreservice"></a>BackupRestoreService
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, alapértelmezett érték 0|Statikus|A MinReplicaSetSize BackupRestoreService számára |
|PlacementConstraints|sztring, alapértelmezett érték a ""|Statikus|  A PlacementConstraints BackupRestore szolgáltatáshoz |
|SecretEncryptionCertThumbprint|sztring, alapértelmezett érték a ""|Dinamikus|A titkos X509 tanúsítvány ujjlenyomata |
|SecretEncryptionCertX509StoreName|sztring, alapértelmezett érték a "Saját"|   Dinamikus|    Ez azt jelzi, hogy a titkosításra és visszafejtésre visszafejtésekor tároló hitelesítő adatok visszaállítása biztonsági mentési szolgáltatás által használt titkosításához használt hitelesítő adatok nevét az X.509 tanúsítvány tároló használni kívánt tanúsítványt |
|TargetReplicaSetSize|int, alapértelmezett érték 0|Statikus| A TargetReplicaSetSize BackupRestoreService számára |

## <a name="clustermanager"></a>ClusterManager
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|Enabledefaultservicesupgrade beállítást | Bool, alapértelmezett érték a False (hamis) |Dinamikus|Frissíti az alapértelmezett szolgáltatások engedélyezése az alkalmazás frissítése során. Alapértelmezett szolgáltatások leírásai felülírják a frissítés után. |
|FabricUpgradeHealthCheckInterval |Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|A gyakoriság állapotadatainak ellenőrzése közben a figyelt Fabric frissítése |
|FabricUpgradeStatusPollInterval |Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|A Fabric frissítési állapot lekérdezése gyakoriságát. Ez az érték határozza meg, a frissítés bármely GetFabricUpgradeProgress hívás aránya |
|ImageBuilderTimeoutBuffer |Idő (másodpercben), az alapértelmezett érték 3 |Dinamikus|Adja meg az időtartam másodpercben. Mennyi ideig, hogy az Image Builder adott időtúllépési hibákat észlel, térjen vissza az ügyfél számára. Ha a puffer túl kicsi. Ezután az ügyfél túllépi az időkorlátot a kiszolgáló előtt, és lekérdezi az általános időtúllépési hiba. |
|InfrastructureTaskHealthCheckRetryTimeout | Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|Adja meg az időtartam másodpercben. Újrapróbálkozás időnk mennyisége állapot-ellenőrzések egy infrastruktúra-feladat utáni feldolgozásakor nem sikerült. Sikeres állapot-ellenőrzése figyelje az időzítő alaphelyzetbe állnak. |
|InfrastructureTaskHealthCheckStableDuration | Idő (másodpercben), az alapértelmezett érték 0|Dinamikus| Adja meg az időtartam másodpercben. Mennyi ideig megfigyelni az egymást követő átadott Állapotellenőrzések utáni feldolgozás egy infrastruktúra-tevékenység előtt sikeresen befejeződik. A sikertelen állapot-ellenőrzése figyelje az időzítő alaphelyzetbe állnak. |
|InfrastructureTaskHealthCheckWaitDuration |Idő (másodpercben), az alapértelmezett érték 0|Dinamikus| Adja meg az időtartam másodpercben. Mennyi ideig kell várni egy infrastruktúra-feladat utáni feldolgozása után az állapot-ellenőrzések megkezdése előtt. |
|InfrastructureTaskProcessingInterval | Idő (másodpercben), az alapértelmezett érték 10 |Dinamikus|Adja meg az időtartam másodpercben. A feldolgozási időköze állapotú gép feldolgozási infrastruktúra feladat által használt. |
|MaxCommunicationTimeout |Idő (másodpercben), alapértelmezett érték a 600 |Dinamikus|Adja meg az időtartam másodpercben. Belső kommunikációs ClusterManager és más rendszerek közötti maximális időtúllépése (azaz; szolgáltatások A Naming Service; Feladatátvevőfürt-kezelő és a stb.). Ez az időkorlát kisebb, mint a globális MaxOperationTimeout (mivel előfordulhat, hogy minden ügyfél művelet rendszer összetevői között több kommunikáció) kell lennie. |
|MaxDataMigrationTimeout |Idő (másodpercben), alapértelmezett érték a 600 |Dinamikus|Adja meg az időtartam másodpercben. A maximális időkorlát az adatok áttelepítési helyreállítási műveletek után a Fabric frissítése megtörtént. |
|MaxOperationRetryDelay |Idő (másodpercben), alapértelmezett érték az 5|Dinamikus| Adja meg az időtartam másodpercben. Hibák vannak fellépő belső újrapróbálkozások maximális késleltetés. |
|MaxOperationTimeout |Idő (másodpercben), alapértelmezett érték a MaxValue |Dinamikus| Adja meg az időtartam másodpercben. A maximális globális időtúllépést ClusterManager műveletek belsőleg feldolgozásra. |
|MaxTimeoutRetryBuffer | Idő (másodpercben), alapértelmezett érték a 600 |Dinamikus|Adja meg az időtartam másodpercben. A maximális művelet időtúllépése megkísérlésekor belső időtúllépések miatt <Original Time out>  +  <MaxTimeoutRetryBuffer>. További időtúllépési MinOperationTimeout egységnyi növekményekben egészül ki. |
|MinOperationTimeout | Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|Adja meg az időtartam másodpercben. A minimális globális időtúllépést ClusterManager műveletek belsőleg feldolgozásra. |
|MinReplicaSetSize |Int, alapértelmezett érték 3 |Nem engedélyezett|A MinReplicaSetSize ClusterManager számára. |
|PlacementConstraints | sztring, alapértelmezett érték a "" |Nem engedélyezett|A PlacementConstraints ClusterManager számára. |
|QuorumLossWaitDuration |Idő (másodpercben), alapértelmezett érték a MaxValue |Nem engedélyezett| Adja meg az időtartam másodpercben. A QuorumLossWaitDuration ClusterManager számára. |
|ReplicaRestartWaitDuration |Idő (másodpercben), az alapértelmezett érték (60.0 * 30)|Nem engedélyezett|Adja meg az időtartam másodpercben. A ReplicaRestartWaitDuration ClusterManager számára. |
|ReplicaSetCheckTimeoutRollbackOverride |Idő (másodpercben), alapértelmezett érték az 1200-as |Dinamikus| Adja meg az időtartam másodpercben. Ha a ReplicaSetCheckTimeout duplaszó; a maximális értékre van állítva. Ezután azt felülbírálja a következő értékkel: Ez a konfiguráció visszaállítása céljából. Összegző-továbbító használt érték nem soha nem bírálják felül. |
|SkipRollbackUpdateDefaultService | Bool, alapértelmezett érték a False (hamis) |Dinamikus|A CM visszaállítása módosított alapértelmezett szolgáltatások kihagyja a frissítési kérelem-visszavonás során. |
|StandByReplicaKeepDuration | Idő (másodpercben), az alapértelmezett érték (3600.0 * 2)|Nem engedélyezett|Adja meg az időtartam másodpercben. A StandByReplicaKeepDuration ClusterManager számára. |
|TargetReplicaSetSize |Int, alapértelmezett érték 7 |Nem engedélyezett|A TargetReplicaSetSize ClusterManager számára. |
|UpgradeHealthCheckInterval |Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|A gyakoriság állapotadatainak ellenőrzi egy figyelt alkalmazásfrissítések során |
|UpgradeStatusPollInterval |Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|Az alkalmazás frissítési állapot lekérdezési gyakorisága. Ez az érték határozza meg, a frissítés bármely GetApplicationUpgradeProgress hívás aránya |

## <a name="common"></a>Közös
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Idő (másodpercben), az alapértelmezett érték 1 |Dinamikus|Adja meg az időtartam másodpercben. Teljesítmény – figyelési időköz. Beállítás 0 vagy negatív érték letiltja a figyelést. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **A paraméter** | **Megengedett értékek** |**Szabályzat frissítése**| **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, alapértelmezett értéke None|Dinamikus|Adja meg a szabályzat töredezettségmentesítés során csomópontok kiürítése követ. Egy metrika a 0 azt jelenti, hogy SF próbálja töredezettségének egyenlően a csomópontok frissítési és tartalék tartományok; között 1 csak azt jelzi, hogy a csomópontok kell töredezettségmentesíteni |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **A paraméter** | **Megengedett értékek** |**Szabályzat frissítése**| **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, alapértelmezett értéke None|Dinamikus|Meghatározza, hogy a töredezettségmentesítéshez, és nem a terheléselosztáshoz használt mérőszámok készletét. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **A paraméter** | **Megengedett értékek** |**Szabályzat frissítése**| **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, alapértelmezett értéke None|Dinamikus|Azt határozza meg, amely van szükség, fontolja meg a fürt töredezettségmentesítése címtartomány megadásával vagy %-os ingyenes csomópontok [0.0-1.0) vagy az as-szám üres csomópontok száma > = 1,0 |

## <a name="diagnostics"></a>Diagnosztika
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, alapértelmezett érték az IGAZ | Dinamikus |E megszemélyesítést akkor fér hozzá a diagnosztikai tárolja az alkalmazás nevében szükség. |
|AppEtwTraceDeletionAgeInDays |Int, alapértelmezett érték 3 | Dinamikus |Ennyi nap elteltével töröljük a régi tartalmazó alkalmazás ETW-nyomkövetések ETL-fájlok. |
|ApplicationLogsFormatVersion |Int, alapértelmezett érték 0 | Dinamikus |Alkalmazás verzióját naplók formátuma. Támogatott értékei a következők: 0 és 1. 1-es verzió fut, 0 az ETW-esemény rekordjára további mezőket tartalmaz. |
|ClusterId |Sztring | Dinamikus |A fürt egyedi azonosítója. Ez a fürt létrehozásakor jön létre. |
|ConsumerInstances |Sztring | Dinamikus |A közvetlen gyorsítótár-ELÉRÉS fogyasztói példányok listáját. |
|DiskFullSafetySpaceInMB |Int, alapértelmezett érték az 1024 | Dinamikus |Fennmaradó szabad lemezterület (MB) megvédeni a közvetlen gyorsítótár-ELÉRÉS használja. |
|EnableCircularTraceSession |Bool, alapértelmezett érték a False (hamis) | Statikus |A jelző azt jelzi, hogy használják-e körkörös nyomkövetési munkamenetek. |
|EnableTelemetry |Bool, alapértelmezett érték az IGAZ | Dinamikus |Ez történik, engedélyezni vagy letiltani a telemetriai adatokat. |
|MaxDiskQuotaInMB |Int, az alapértelmezett érték 65536 értékű | Dinamikus |Disková kvóta v MB-ot a Windows Fabric naplófájlokat. |
|ProducerInstances |Sztring | Dinamikus |A közvetlen gyorsítótár-ELÉRÉS előállító példányok listáját. |

## <a name="dnsservice"></a>Nincs
| **A paraméter** | **Megengedett értékek** |**Szabályzat frissítése**| **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|InstanceCount|int, alapértelmezett – 1|Statikus|alapértelmezett értéke -1, ami azt jelenti, hogy nincs minden csomóponton fut-e. Beépített kell ez 1 értékűre kell beállítani, mivel nincs 53-as, jól ismert portot használ, így több példány nem lehetnek ugyanazon a gépen.|
|IsEnabled|bool, alapértelmezett érték a hamis|Statikus|Engedélyezi vagy letiltja a nincs. Nincs alapértelmezés szerint le van tiltva, és ez a konfiguráció kell állítani az engedélyezéshez. |
|PartitionPrefix|sztring, alapértelmezett érték a "-"|Statikus|Azt szabályozza, hogy a partíció előtag karakterlánc értékét a particionált szolgáltatások DNS-lekérdezésekre. Az érték: <ul><li>Kell RFC-kompatibilis DNS-lekérdezés részeként lesz.</li><li>Nem tartalmazhat egy pont követ, '.', mert pont zavarja a DNS-utótag viselkedés.</li><li>Nem lehet 5 karakternél hosszabb.</li><li>Nem lehet üres karakterlánc.</li><li>Ha felülbírálja a PartitionPrefix beállítást, majd PartitionSuffix felül kell bírálni, és fordítva.</li></ul>További információkért lásd: [Service Fabric DNS-szolgáltatás.](service-fabric-dnsservice.md).|
|PartitionSuffix|sztring, alapértelmezett érték a ""|Statikus|Azt szabályozza, hogy a partíció utótag karakterlánc értékét a particionált szolgáltatások DNS-lekérdezésekre. Az érték: <ul><li>Kell RFC-kompatibilis DNS-lekérdezés részeként lesz.</li><li>Nem tartalmazhat egy pont követ, '.', mert pont zavarja a DNS-utótag viselkedés.</li><li>Nem lehet 5 karakternél hosszabb.</li><li>Ha felülbírálja a PartitionPrefix beállítást, majd PartitionSuffix felül kell bírálni, és fordítva.</li></ul>További információkért lásd: [Service Fabric DNS-szolgáltatás.](service-fabric-dnsservice.md). |

## <a name="fabricclient"></a>FabricClient
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Idő (másodpercben), az alapértelmezett érték 2 |Dinamikus|Adja meg az időtartam másodpercben. Kapcsolat időkorlátja minden alkalommal ügyfél megkísérli az átjáró csatlakoztatásához.|
|HealthOperationTimeout |Idő (másodpercben), alapértelmezett érték a 120 |Dinamikus|Adja meg az időtartam másodpercben. A Health Manager küldött jelentés üzenet időkorlátja. |
|HealthReportRetrySendInterval |Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus|Adja meg az időtartam másodpercben. Időközét jelentéskészítési összetevő újra elküldi halmozott rendszerállapot-jelentések a Health Manager. |
|HealthReportSendInterval |Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus|Adja meg az időtartam másodpercben. A jelentéskészítési időközét küld halmozott rendszerállapot-jelentések a Health Manager összetevő. |
|KeepAliveIntervalInSeconds |Int, az alapértelmezett érték 20 |Statikus|Az időköz, amelyen a FabricClient átviteli életben tartási üzeneteket küld az átjárót. 0; életben tartási le van tiltva. Pozitív számnak kell lennie. |
|MaxFileSenderThreads |Uint, alapértelmezett érték 10 |Statikus|Párhuzamos átvitt fájlok maximális száma. |
|NodeAddresses |sztring, alapértelmezett érték a "" |Statikus|Címek (kapcsolati karakterláncok), amely az elnevezési szolgáltatással való kommunikációra használható különböző csomópontokon gyűjteménye. Az ügyfél először csatlakozik, a címek egyikének kiválasztásával véletlenszerűen. Ha több, mint egy kapcsolati karakterláncot meg van adva, és a egy létesített kapcsolat megszakad a kommunikáció vagy időtúllépési hiba; miatt az ügyfél kapcsolók egymás után a következő címet használja. Az elnevezési szolgáltatás címének ismételje meg az újrapróbálkozások szemantika a részleteket a következő szakaszban talál. |
|PartitionLocationCacheLimit |Int, alapértelmezett értéke pedig 100000 |Statikus|A szolgáltatás feloldásához (0: Nincs korlát beállítása) gyorsítótárazott partíciók száma. |
|RetryBackoffInterval |Idő (másodpercben), az alapértelmezett érték 3 |Dinamikus|Adja meg az időtartam másodpercben. A visszatartási időköz mielőtt újra próbálkozna a művelettel. |
|ServiceChangePollInterval |Idő (másodpercben), alapértelmezett érték a 120 |Dinamikus|Adja meg az időtartam másodpercben. A szolgáltatás az egymást követő lekérdezések közötti időtartam az ügyfél az átjáró számára regisztrált szolgáltatás módosítási értesítések visszahívások változik. |

## <a name="fabrichost"></a>Hálóbeli
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, alapértelmezett érték 10 |Dinamikus|Ez az a maximális szám, amelynek a rendszer újra megpróbálja sikertelen aktiválása előtt központosítását. |
|ActivationMaxRetryInterval |Idő (másodpercben), az alapértelmezett érték 300 |Dinamikus|Adja meg az időtartam másodpercben. Maximális újrapróbálkozási időköz az aktiváláshoz. Minden folyamatos hiba esetén az újrapróbálkozási időköznek számítjuk ki, hogy Min (ActivationMaxRetryInterval; Folyamatos hibaszám * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Idő (másodpercben), alapértelmezett érték az 5 |Dinamikus|Adja meg az időtartam másodpercben. Visszalépési időköz minden az aktiválás sikertelen; minden folyamatos aktiválási hiba esetén a rendszer újra próbálkozik az aktiválási esetében akár a MaxActivationFailureCount. Az újrapróbálkozási időköz minden próbálkozásra egy termék folyamatos az aktiválás sikertelen, és az aktiválási visszatartási időköz. |
|EnableRestartManagement |Bool, alapértelmezett érték a False (hamis) |Dinamikus|Ez a kiszolgáló újraindítására engedélyezése. |
|EnableServiceFabricAutomaticUpdates |Bool, alapértelmezett érték a False (hamis) |Dinamikus|Ez a fabric automatikus frissítése a Windows Update-n keresztül engedélyezése. |
|EnableServiceFabricBaseUpgrade |Bool, alapértelmezett érték a False (hamis) |Dinamikus|Ez a alapvető frissítési kiszolgáló engedélyezéséhez. |
|StartTimeout |Idő (másodpercben), az alapértelmezett érték 300 |Dinamikus|Adja meg az időtartam másodpercben. Fabricactivationmanager indítási időtúllépés. |
|StopTimeout |Idő (másodpercben), az alapértelmezett érték 300 |Dinamikus|Adja meg az időtartam másodpercben. Az üzemeltetett szolgáltatás aktiválási; időkorlátja Inaktiválás és frissítését. |

## <a name="fabricnode"></a>FabricNode
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |sztring, alapértelmezett "FindByThumbprint" |Dinamikus|Azt jelzi, hogy a tárolóban ClientAuthX509StoreName támogatott értéke által meghatározott tanúsítvány keresése: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |sztring, alapértelmezett érték a "" | Dinamikus|Keresési szűrő keresse meg az alapértelmezett rendszergazdai szerepkörhöz FabricClient tanúsítványt használt érték. |
|ClientAuthX509FindValueSecondary |sztring, alapértelmezett érték a "" |Dinamikus|Keresési szűrő keresse meg az alapértelmezett rendszergazdai szerepkörhöz FabricClient tanúsítványt használt érték. |
|ClientAuthX509StoreName |sztring, alapértelmezett érték a "Saját" |Dinamikus|Az alapértelmezett rendszergazdai szerepkörhöz FabricClient tanúsítványt tartalmazó X.509 tanúsítvány tároló neve. |
|ClusterX509FindType |sztring, alapértelmezett "FindByThumbprint" |Dinamikus|Azt jelzi, hogy hogyan keresse meg az áruházban ClusterX509StoreName támogatott értékek által megadott fürttanúsítvány: "FindByThumbprint"; "FindBySubjectName" a "FindBySubjectName"; Ha nincs több találat; a legtávolabbi lejárati rendelkezőt szolgál. |
|ClusterX509FindValue |sztring, alapértelmezett érték a "" |Dinamikus|Keresési szűrő értéke fürttanúsítvány helyének azonosításához használt. |
|ClusterX509FindValueSecondary |sztring, alapértelmezett érték a "" |Dinamikus|Keresési szűrő értéke fürttanúsítvány helyének azonosításához használt. |
|ClusterX509StoreName |sztring, alapértelmezett érték a "Saját" |Dinamikus|X.509 tanúsítvány tároló, amely tartalmazza a fürtön belüli kommunikáció fürttanúsítvány neve. |
|EndApplicationPortRange |Int, alapértelmezett érték 0 |Statikus|A teljes (nincs inkluzív) alrendszer üzemeltető által kezelt alkalmazás port. Szükséges, ha EndpointFilteringEnabled értéke igaz, a üzemeltetési. |
|ServerAuthX509FindType |sztring, alapértelmezett "FindByThumbprint" |Dinamikus|Azt jelzi, hogy a kiszolgálói tanúsítvány a ServerAuthX509StoreName támogatott értéke által meghatározott tárolóban lévő keresése: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |sztring, alapértelmezett érték a "" |Dinamikus|Keresési szűrő kiszolgálói tanúsítvány megkereséséhez használt érték. |
|ServerAuthX509FindValueSecondary |sztring, alapértelmezett érték a "" |Dinamikus|Keresési szűrő kiszolgálói tanúsítvány megkereséséhez használt érték. |
|ServerAuthX509StoreName |sztring, alapértelmezett érték a "Saját" |Dinamikus|X.509 tanúsítvány tároló, amely tartalmazza a entrée service-tanúsítvány neve. |
|StartApplicationPortRange |Int, alapértelmezett érték 0 |Statikus|Az üzemeltető alrendszer által kezelt alkalmazás portok kezdete. Szükséges, ha EndpointFilteringEnabled értéke igaz, a üzemeltetési. |
|StateTraceInterval |Idő (másodpercben), az alapértelmezett érték 300 |Statikus|Adja meg az időtartam másodpercben. A csomópont állapota minden egyes csomóponton és az akár FM/FMM csomópont nyomkövetés intervallumát. |
|UserRoleClientX509FindType |sztring, alapértelmezett "FindByThumbprint" |Dinamikus|Azt jelzi, hogy a tárolóban UserRoleClientX509StoreName támogatott értéke által meghatározott tanúsítvány keresése: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |sztring, alapértelmezett érték a "" |Dinamikus|Keresési szűrő keresse meg az alapértelmezett felhasználói szerepkörhöz FabricClient tanúsítványt használt érték. |
|UserRoleClientX509FindValueSecondary |sztring, alapértelmezett érték a "" |Dinamikus|Keresési szűrő keresse meg az alapértelmezett felhasználói szerepkörhöz FabricClient tanúsítványt használt érték. |
|UserRoleClientX509StoreName |sztring, alapértelmezett érték a "Saját" |Dinamikus|Neve az X.509 tanúsítvány tároló, amely az alapértelmezett felhasználói szerepkörhöz FabricClient tanúsítványt tartalmaz. |

## <a name="failovermanager"></a>FailoverManager
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|BuildReplicaTimeLimit|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(3600)|Dinamikus|Adja meg az időtartam másodpercben. Az időkorlát; állapot-nyilvántartó replika létrehozásához amely után megkezdődik egy figyelmeztetés állapotjelentése |
|ClusterPauseThreshold|int, alapértelmezett értéke 1|Dinamikus|Ha a rendszer a csomópontok számát alább az értéket, majd elhelyezési; terheléselosztás; és feladatátvételi le van állítva. |
|CreateInstanceTimeLimit|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(300)|Dinamikus|Adja meg az időtartam másodpercben. Hozzon létre egy állapot nélküli példányt; határidő amely után megkezdődik egy figyelmeztetés állapotjelentése |
|ExpectedClusterSize|int, alapértelmezett értéke 1|Dinamikus|Ha a fürt már kezdetben elindult; az FM várakozik a mentése jelentéséhez magukat, például a más szolgáltatások; megkezdése előtt számos csomópontok többek között a hasonló elnevezési rendszer szolgáltatásokat. Ez az érték növelése növeli az indítása; fürt szükséges idő az üzemeltető számítógépen korai csomópontokat, és a további lépések, amelyek szükség szerint további csomópontok ismét online lesz azonban nem. Ez az érték általában az egyes töredéke a fürtcsomópontok kezdeti méret kell állítani. |
|ExpectedNodeDeactivationDuration|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(60.0 * 30)|Dinamikus|Adja meg az időtartam másodpercben. Ez a várt időtartam egy csomópont inaktiválása a végrehajtásához. |
|ExpectedNodeFabricUpgradeDuration|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(60.0 * 30)|Dinamikus|Adja meg az időtartam másodpercben. Ez az a várt időtartam frissíteni egy csomópont Windows Fabric frissítése során. |
|ExpectedReplicaUpgradeDuration|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(60.0 * 30)|Dinamikus|Adja meg az időtartam másodpercben. Ez az alkalmazás frissítése során frissítendő csomóponton a replikák várt időtartam. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, alapértelmezett értéke igaz|Dinamikus|Ha igaz értékre kell állítani a frissítés során áthelyezése egy cél másodpéldánykészletének méretét 1-replikák lesz engedélyezett. |
|MinReplicaSetSize|Int, alapértelmezett érték 3|Nem engedélyezett|Ez az a minimális replikakészlet méretét a következő a FM. Ha ez az érték alá csökken az aktív FM replikák száma az FM elutasítják a módosításokat a fürthöz, amíg nem legalább állítható helyre, a replikák minimális száma |
|PlacementConstraints|sztring, alapértelmezett érték a ""|Nem engedélyezett|A feladatátvételi manager replikák elhelyezési korlátozások |
|PlacementTimeLimit|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(600)|Dinamikus|Adja meg az időtartam másodpercben. Az időkorlát miatt a cél replika száma; amely után megkezdődik egy figyelmeztetés állapotjelentése |
|QuorumLossWaitDuration |Idő (másodpercben), alapértelmezett érték a MaxValue |Dinamikus|Adja meg az időtartam másodpercben. Ez az a maximális időtartam, amelynek engedélyezünk egy partíciót a kvórum elvesztése a állapotban kell lennie. Ha a partíció kvórumveszteségben továbbra is; ez az időtartam után a partíció kvórum elvesztése helyreállt az elveszett eszköz lefelé replikákat figyelembe vételével. Vegye figyelembe, hogy ez potenciálisan vonhat maga után az adatvesztést. |
|ReconfigurationTimeLimit|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(300)|Dinamikus|Adja meg az időtartam másodpercben. Az újrakonfigurálás; határidő amely után megkezdődik egy figyelmeztetés állapotjelentése |
|ReplicaRestartWaitDuration|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(60.0 * 30)|Nem engedélyezett|Adja meg az időtartam másodpercben. Ez az a ReplicaRestartWaitDuration a FMService számára |
|StandByReplicaKeepDuration|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|Nem engedélyezett|Adja meg az időtartam másodpercben. Ez az a StandByReplicaKeepDuration a FMService számára |
|TargetReplicaSetSize|Int, alapértelmezett érték 7|Nem engedélyezett|Ez a Windows Fabric megőrzi FM replikák célszáma. Az FM adatok; nagyobb megbízhatóság érdekében eredménye nagyobb a teljesítmény kis vonatkozóan kompromisszumot jelent. |
|UserMaxStandByReplicaCount |Int, alapértelmezett érték 1 |Dinamikus|Az alapértelmezett maximális található StandBy replikák száma, hogy a rendszer megőrzi a szolgáltatást. |
|UserReplicaRestartWaitDuration |Idő (másodpercben), alapértelmezett érték a 60.0 * 30 |Dinamikus|Adja meg az időtartam másodpercben. Ha egy megőrzött replika leáll; Ennek az időtartamnak a replika ismét elérhető nem lesz új helyettesítő replikák (amihez egy másolatot, az állam) létrehozása előtt megvárja, hogy mely Windows Fabric. |
|UserStandByReplicaKeepDuration |Idő (másodpercben), alapértelmezett érték a 3600.0 * 24 * 7 |Dinamikus|Adja meg az időtartam másodpercben. Amikor egy megőrzött replika térjen vissza a lefelé állapotból; Ez előfordulhat, hogy már helyett. Ez az időzítő határozza meg, mennyi ideig a FM fogja megőrizni a készenléti replika előtt, és elveti azt. |

## <a name="faultanalysisservice"></a>FaultAnalysisService
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, az alapértelmezett érték 604800 |Statikus| Ez a körülbelül mennyi műveleteket, amelyeket a Terminálszolgáltatások állapotban tartani. Ez is attól függ, StoredActionCleanupIntervalInSeconds; mivel a tisztítással csak munkát adott időközönként. 604800 befejezéséig 7 nap. |
|DataLossCheckPollIntervalInSeconds|int, alapértelmezett érték 5|Statikus|Ez az, hogy a rendszer hajt végre, miközben az adatvesztés fordulhat elő, hogy az ellenőrzések között eltelt idő. Az adatok elvesztése száma kiszolgálónként belső iteráció ellenőrzött hányszor DataLossCheckWaitDurationInSeconds ez van. |
|DataLossCheckWaitDurationInSeconds|int, alapértelmezett értéke 25|Statikus|Az Összesen ennyi ideig; másodperc; hogy adatvesztés fordulhat elő, hogy a rendszer várakozik. Ez belső használatra szolgál, a StartPartitionDataLossAsync() api meghívásakor. |
|MinReplicaSetSize |Int, alapértelmezett érték 0 |Statikus|A MinReplicaSetSize FaultAnalysisService számára. |
|PlacementConstraints | sztring, alapértelmezett érték a ""|Statikus| A PlacementConstraints FaultAnalysisService számára. |
|QuorumLossWaitDuration | Idő (másodpercben), alapértelmezett érték a MaxValue |Statikus|Adja meg az időtartam másodpercben. A QuorumLossWaitDuration FaultAnalysisService számára. |
|ReplicaDropWaitDurationInSeconds|int, alapértelmezett 600|Statikus|Ezt a paramétert használja az adatvesztés API-t kell meghívni. Azt szabályozza, hogy a rendszer mennyi ideig várjon való eltávolítása után első eldobott replika replika belsőleg meghívásainak rajta. |
|ReplicaRestartWaitDuration |Idő (másodpercben), az alapértelmezett érték 60 perc|Statikus|Adja meg az időtartam másodpercben. A ReplicaRestartWaitDuration FaultAnalysisService számára. |
|StandByReplicaKeepDuration| Idő (másodpercben), alapértelmezett érték (60*24*7) perc |Statikus|Adja meg az időtartam másodpercben. A StandByReplicaKeepDuration FaultAnalysisService számára. |
|StoredActionCleanupIntervalInSeconds | Int, alapértelmezett érték a 3600 |Statikus|Ez az, hogy milyen gyakran törlődnek a tárolóban. Csak azokat a műveleteket, terminál állapotban; és, amely legalább befejeződött ezelőtt CompletedActionKeepDurationInSeconds lesz eltávolítva. |
|StoredChaosEventCleanupIntervalInSeconds | Int, alapértelmezett érték a 3600 |Statikus|Ez az a tároló karbantartása; naplózni gyakoriságát. Ha az események száma legfeljebb 30000; a karbantartás pillanattal a rendszer. |
|TargetReplicaSetSize |Int, alapértelmezett érték 0 |Statikus|NOT_PLATFORM_UNIX_START a TargetReplicaSetSize FaultAnalysisService számára. |

## <a name="federation"></a>Összevonás
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|GlobalTicketLeaseDuration|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(300)|Statikus|Adja meg az időtartam másodpercben. A globális címbérlet fenntartható, mivel a szavazók kell a fürtben található csomópontok. Szavazók küldje el a globális címbérlet, ennek az időtartamnak a fürtön lesznek továbbítva. Ha a időtartama lejár; Ezután a bérlet elvész. A bérletek kvórum elvesztése hatására kénytelen volt megszakítani ezt a fürt; csomópont a csomópontokat a kvórumképzés kommunikációt fogadni az ez idő alatt nem működnek.  Ezt az értéket kell módosítani a fürt mérete alapján. |
|LeaseDuration |Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus|A címbérlet csomópont és szomszédjainak közötti érvényes időtartam. |
|LeaseDurationAcrossFaultDomain |Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus|Tartalék tartomány között egy csomópont és szomszédjainak közötti érvényes a címbérlet időtartama. |

## <a name="filestoreservice"></a>FileStoreService
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|AcceptChunkUpload|bool, alapértelmezett értéke igaz|Dinamikus|A file Storage-tároló szolgáltatás fogad alapú adattömbök fájl feltöltése vagy alkalmazás-csomag másolása során nem Config. |
|AnonymousAccessEnabled | Bool, alapértelmezett érték az IGAZ |Statikus|A FileStoreService megosztások való névtelen hozzáférés engedélyezése vagy letiltása. |
|CommonName1Ntlmx509CommonName|sztring, alapértelmezett érték a ""|Statikus| A köznapi neve a X509 NTLM-hitelesítés használata esetén a HMAC-val a CommonName1NtlmPasswordSecret a létrehozásához használt tanúsítvány |
|CommonName1Ntlmx509StoreLocation|sztring, alapértelmezett a "Helyi gépen lévő"|Statikus|A tároló helyét a X509 NTLM-hitelesítés használata esetén a HMAC-val a CommonName1NtlmPasswordSecret a létrehozásához használt tanúsítvány |
|CommonName1Ntlmx509StoreName|sztring, alapértelmezett a "MY"| Statikus|A tároló nevére a X509 NTLM-hitelesítés használata esetén a HMAC-val a CommonName1NtlmPasswordSecret a létrehozásához használt tanúsítvány |
|CommonName2Ntlmx509CommonName|sztring, alapértelmezett érték a ""|Statikus|A köznapi neve a X509 NTLM-hitelesítés használata esetén a HMAC-val a CommonName2NtlmPasswordSecret a létrehozásához használt tanúsítvány |
|CommonName2Ntlmx509StoreLocation|sztring, alapértelmezett a "Helyi gépen lévő"| Statikus|A tároló helyét a X509 NTLM-hitelesítés használata esetén a HMAC-val a CommonName2NtlmPasswordSecret a létrehozásához használt tanúsítvány |
|CommonName2Ntlmx509StoreName|sztring, alapértelmezett a "MY"|Statikus| A tároló nevére a X509 NTLM-hitelesítés használata esetén a HMAC-val a CommonName2NtlmPasswordSecret a létrehozásához használt tanúsítvány |
|CommonNameNtlmPasswordSecret|SecureString, az alapértelmezett érték Common::SecureString("")| Statikus|A jelszó titkos adatot, amely a kezdőérték használja ugyanazt a jelszót jönnek létre, ha NTLM-hitelesítés használatával |
|GenerateV1CommonNameAccount| bool, alapértelmezett értéke igaz|Statikus|Megadja, hogy egy felhasználói nevet V1 generálási algoritmus rendelkező fiókot létrehozni. A Service Fabric 6.1-es; verzió használatának megkezdése egy fiókra a 2. generációs mindig jön létre. A V1 fiókkal szükség frissítései a /-verziók nem támogatják a 2. generációs (előtt 6.1-es).|
|MaxCopyOperationThreads | Uint, alapértelmezett érték 0 |Dinamikus| Elsődleges másolhatja, amely másodlagos párhuzamos fájlok maximális számát. "0" == magok számát. |
|MaxFileOperationThreads | Uint, alapértelmezett érték 100 |Statikus| Az elsődleges FileOperations (Copy/Move) végrehajtásához engedélyezett párhuzamos szálak maximális száma. "0" == magok számát. |
|MaxRequestProcessingThreads | Uint, az alapértelmezett érték 200 |Statikus|Az elsődleges lévő kérelmek feldolgozásához engedélyezett párhuzamos szálak maximális száma. "0" == magok számát. |
|MaxSecondaryFileCopyFailureThreshold | Uint, alapértelmezett érték 25|Dinamikus|Mielőtt feladja a másodlagos fájl másolása az újrapróbálkozások maximális számát. |
|MaxStoreOperations | Uint, alapértelmezett érték 4096 |Statikus|Párhuzamos tároló tranzakciós műveletek elsődleges engedélyezett maximális számát. "0" == magok számát. |
|NamingOperationTimeout |Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|Adja meg az időtartam másodpercben. Az elnevezési művelet végrehajtása időtúllépés. |
|PrimaryAccountNTLMPasswordSecret | SecureString, alapértelmezett érték üres |Statikus| A jelszó titkos adatot, amely a kezdőérték használja ugyanazt a jelszót jönnek létre, az NTLM-hitelesítés használata esetén. |
|PrimaryAccountNTLMX509StoreLocation | sztring, alapértelmezett a "Helyi gépen lévő"|Statikus| A tároló helyét a X509 NTLM-hitelesítés használata esetén a HMAC-val a PrimaryAccountNTLMPasswordSecret a létrehozásához használt tanúsítvány. |
|PrimaryAccountNTLMX509StoreName | sztring, alapértelmezett a "MY"|Statikus| A tároló nevére a X509 NTLM-hitelesítés használata esetén a HMAC-val a PrimaryAccountNTLMPasswordSecret a létrehozásához használt tanúsítvány. |
|PrimaryAccountNTLMX509Thumbprint | sztring, alapértelmezett érték a ""|Statikus|A X509 ujjlenyomatát NTLM-hitelesítés használata esetén a HMAC-val a PrimaryAccountNTLMPasswordSecret a létrehozásához használt tanúsítvány. |
|PrimaryAccountType | sztring, alapértelmezett érték a "" |Statikus|Az elsődleges AccountType a rendszerbiztonsági tag ACL a FileStoreService fájlmegosztások. |
|PrimaryAccountUserName | sztring, alapértelmezett érték a "" |Statikus|Az elsődleges fióknak az ACL egyszerű felhasználóneve a FileStoreService fájlmegosztások. |
|PrimaryAccountUserPassword | SecureString, alapértelmezett érték üres |Statikus|Az egyszerű ACL elsődleges fiók jelszavát a FileStoreService fájlmegosztások. |
|QueryOperationTimeout | Idő (másodpercben), az alapértelmezett érték 60 |Dinamikus|Adja meg az időtartam másodpercben. Lekérdezési művelet végrehajtása időtúllépés. |
|SecondaryAccountNTLMPasswordSecret | SecureString, alapértelmezett érték üres |Statikus| A jelszó titkos adatot, amely a kezdőérték használja ugyanazt a jelszót jönnek létre, az NTLM-hitelesítés használata esetén. |
|SecondaryAccountNTLMX509StoreLocation | sztring, alapértelmezett a "Helyi gépen lévő" |Statikus|A tároló helyét a X509 NTLM-hitelesítés használata esetén a HMAC-val a SecondaryAccountNTLMPasswordSecret a létrehozásához használt tanúsítvány. |
|SecondaryAccountNTLMX509StoreName | sztring, alapértelmezett a "MY" |Statikus|A tároló nevére a X509 NTLM-hitelesítés használata esetén a HMAC-val a SecondaryAccountNTLMPasswordSecret a létrehozásához használt tanúsítvány. |
|SecondaryAccountNTLMX509Thumbprint | sztring, alapértelmezett érték a ""| Statikus|A X509 ujjlenyomatát NTLM-hitelesítés használata esetén a HMAC-val a SecondaryAccountNTLMPasswordSecret a létrehozásához használt tanúsítvány. |
|SecondaryAccountType | sztring, alapértelmezett érték a ""|Statikus| A másodlagos AccountType a rendszerbiztonsági tag ACL a FileStoreService fájlmegosztások. |
|SecondaryAccountUserName | sztring, alapértelmezett érték a ""| Statikus|A másodlagos fiókot ACL az egyszerű felhasználóneve a FileStoreService fájlmegosztások. |
|SecondaryAccountUserPassword | SecureString, alapértelmezett érték üres |Statikus|A másodlagos fiók jelszavát a rendszerbiztonsági tag ACL a FileStoreService fájlmegosztások. |

## <a name="healthmanager"></a>HealthManager
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Bool, alapértelmezett érték a False (hamis) |Statikus|A fürt állapotának kiértékelési házirend: engedélyezése egy alkalmazás típus állapotának kiértékelését. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool, alapértelmezett érték a False (hamis) |Statikus|A fürt állapotának kiértékelési házirend: figyelmeztetések hibák számít. |
|MaxPercentUnhealthyApplications | Int, alapértelmezett érték 0 |Statikus|A fürt állapotának kiértékelési házirend: sérült alkalmazások maximális százalékos a fürt állapota megfelelő lesz engedélyezett. |
|MaxPercentUnhealthyNodes | Int, alapértelmezett érték 0 |Statikus|A fürt állapotának kiértékelési házirend: sérült csomópontok maximális százalékát a fürt állapota megfelelő lesz engedélyezett. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|Int, alapértelmezett érték 10|Statikus|A fürt frissítési állapotának kiértékelési házirend: a fürt állapota megfelelő lesz engedélyezett az sérült deltacsomópontjainak maximális százalékos |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, alapértelmezés szerinti 15|Statikus|A fürt frissítési állapotának kiértékelési házirend: a fürt állapota megfelelő lesz engedélyezett maximális százalékos értéke a frissítési tartomány sérült csomópontok különbözeti |

## <a name="hosting"></a>Hosting
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Egész szám, alapértelmezett értéke 10 |Dinamikus|Hányszor rendszer újrapróbálkozások előtt központosítását aktiválás nem sikerült |
|ActivationMaxRetryInterval |Idő (másodpercben), az alapértelmezett érték 300 |Dinamikus|Minden folyamatos aktiválási hiba esetén a rendszer újrapróbálkozik az aktiválási esetében akár ActivationMaxFailureCount. ActivationMaxRetryInterval újrapróbálkozás előtti várakozási időtartam minden aktiválási hiba után itt adható meg. |
|ActivationRetryBackoffInterval |Idő (másodpercben), alapértelmezett érték az 5 |Dinamikus|Minden az aktiválás sikertelen; a leállítási időköz Minden folyamatos aktiválási hiba esetén a rendszer újrapróbálkozik az aktiválási esetében akár a MaxActivationFailureCount. Az újrapróbálkozási időköz minden próbálkozásra egy termék folyamatos az aktiválás sikertelen, és az aktiválási visszatartási időköz. |
|ActivationTimeout| Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(180)|Dinamikus| Adja meg az időtartam másodpercben. Az alkalmazás-aktiválásra időkorlátja Inaktiválás és frissítését. |
|ApplicationHostCloseTimeout| Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus| Adja meg az időtartam másodpercben. Amikor egy önkiszolgáló Fabric kilépési észlelt riasztásértesítési folyamatokat aktiválva FabricRuntime bezár minden, a felhasználói gazdagépfolyamathoz (alkalmazásgazda) a replikán. Ez az a a bezárási művelet időkorlátja. |
|ApplicationUpgradeTimeout| Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(360)|Dinamikus| Adja meg az időtartam másodpercben. Az alkalmazásfrissítés időkorlátja. Ha az időkorlát kisebb, mint a "ActivationTimeout" deployerhez sikertelen lesz. |
|Üzemeltetés|sztring, alapértelmezett érték a "-H localhost:2375 -H npipe: / /"|Statikus|Service Fabric (SF) kezeli a docker-démon (kivéve a windows-ügyfélgépekről Win10 hasonlóan). Ez a konfiguráció lehetővé teszi a felhasználóknak meg kell adni a docker-démon indítása, ha egyéni argumentumok. Ha egyéni argumentumok vannak megadva, a Service Fabric nem továbbítja a Docker-motornak, kivéve a "--pidfile" argumentum. Ezért felhasználók nem szabad megadni a(z)--pidfile "argumentum az ügyfél argumentumok részeként. Emellett az egyéni argumentumok biztosítania kell, hogy a docker démon figyeli az alapértelmezett nevesített csövön a Windows (vagy Linux rendszerű Unix-tartománycsatorna) a Service Fabric kommunikálni tudnak.|
|CreateFabricRuntimeTimeout|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus| Adja meg az időtartam másodpercben. Az időkorlát értékét a szinkronizálási FabricCreateRuntime hívása |
|DefaultContainerRepositoryAccountName|sztring, alapértelmezett érték a ""|Statikus|Alapértelmezett hitelesítő adatok helyett ApplicationManifest.xml megadott hitelesítő adatok |
|DefaultContainerRepositoryPassword|sztring, alapértelmezett érték a ""|Statikus|Alapértelmezett jelszavas hitelesítő adatokat megadva hitelesítő adatok a ApplicationManifest.xml helyett|
|DeploymentMaxFailureCount|Int, az alapértelmezett érték 20| Dinamikus|Alkalmazás központi telepítésének DeploymentMaxFailureCount alkalommal sikertelen volt, hogy a csomópont-alkalmazás központi telepítésének előtt a rendszer megpróbálja újból.| 
|DeploymentMaxRetryInterval| Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(3600)|Dinamikus| Adja meg az időtartam másodpercben. Maximális újrapróbálkozási időköz az üzembe helyezéshez. Minden folyamatos hiba esetén az újrapróbálkozási időköznek számítjuk ki, hogy Min (DeploymentMaxRetryInterval; Folyamatos hibaszám * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(10)|Dinamikus|Adja meg az időtartam másodpercben. Visszatartási időköz az üzembe helyezés sikertelen. Minden folyamatos üzembe helyezés hiba esetén a rendszer újra megpróbálja a MaxDeploymentFailureCount legfeljebb üzembe. Az újrapróbálkozási időköznek egy folyamatos üzembe helyezés sikertelen szorzatát és üzembe helyezési leállítási időközét. |
|EnableActivateNoWindow| bool, alapértelmezett érték a hamis|Dinamikus| Az aktivált folyamat a háttérben bármely konzol jön létre. |
|EnableDockerHealthCheckIntegration|bool, alapértelmezett értéke igaz|Statikus|Lehetővé teszi az integrációt a docker HEALTHCHECK események a Service Fabric rendszerállapot-jelentésbe |
|EnableProcessDebugging|bool, alapértelmezett érték a hamis|Dinamikus| Lehetővé teszi, hogy alkalmazás-gazdagépekkel, a hibakereső indításakor |
|EndpointProviderEnabled| bool, alapértelmezett érték a hamis|Statikus| Lehetővé teszi a háló által végpont erőforrások kezelését. Kezdő és záró tartománya a FabricNode meghatározása szükséges. |
|FabricContainerAppsEnabled| bool, alapértelmezett érték a hamis|Statikus| |
|FirewallPolicyEnabled|bool, alapértelmezett érték a hamis|Statikus| Lehetővé teszi, hogy végpont erőforrások tűzfal portjainak megnyitása, ServiceManifest megadott explicit porttal |
|GetCodePackageActivationContextTimeout|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus|Adja meg az időtartam másodpercben. Az időkorlát értékét a CodePackageActivationContext hívásokat. Ez a nem alkalmazható az ad-hoc szolgáltatások. |
|IPProviderEnabled|bool, alapértelmezett érték a hamis|Statikus|Lehetővé teszi az IP-címek kezelését. |
|IsDefaultContainerRepositoryPasswordEncrypted|bool, alapértelmezett érték a hamis|Statikus|A DefaultContainerRepositoryPassword e titkosítva van-e.|
|LinuxExternalExecutablePath|sztring, alapértelmezett érték a "/ usr/bin /" |Statikus|Az elsődleges könyvtár a csomóponton külső végrehajtható parancsot.|
|NTLMAuthenticationEnabled|bool, alapértelmezett érték a hamis|Statikus| Lehetővé teszi az NTLM használatával a kód csomagokat, amelyek a futó más felhasználókkal, hogy a gép közötti folyamatok biztonságosan kommunikálhassanak. |
|NTLMAuthenticationPasswordSecret|SecureString, az alapértelmezett érték Common::SecureString("")|Statikus|Egy titkosított rendelkezik, amely a jelszót az NTLM-felhasználók létrehozására szolgál. Rendelkezik NTLMAuthenticationEnabled teljesülése esetén kell beállítani. A telepítő érvényesítette. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|Időtartam, az alapértelmezett érték Common::TimeSpan::FromMinutes(3)|Dinamikus|Adja meg az időtartam másodpercben. Melyik üzemeltetési rendszeres időközönként megkeresi a FileStoreService NTLM-konfigurációhoz használandó új tanúsítványok környezet-specifikus beállításokat. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|Időtartam, az alapértelmezett érték Common::TimeSpan::FromMinutes(4)|Dinamikus| Adja meg az időtartam másodpercben. A tanúsítvány köznapi nevek használatával NTLM-felhasználók konfigurálásával időkorlátja. Az NTLM-felhasználók FileStoreService megosztások van szükség. |
|RegisterCodePackageHostTimeout|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus| Adja meg az időtartam másodpercben. Az időkorlát értékét a FabricRegisterCodePackageHost szinkronizálási hívás. Ez a tulajdonság csak többszörös kód csomag alkalmazás gazdagépek például FWP vonatkozik |
|RequestTimeout|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(30)|Dinamikus| Adja meg az időtartam másodpercben. Ez jelöli, hogy a felhasználó gazda és a háló folyamat különféle üzemeltetési kapcsolódó műveletek, például a gyári regisztrációs; közötti kommunikáció időtúllépés modul regisztrálása. |
|RunAsPolicyEnabled| bool, alapértelmezett érték a hamis|Statikus| Lehetővé teszi a kód csomagok futtató helyi felhasználók eltérő a felhasználó mely háló fut-e folyamat. Ahhoz, hogy ez a szabályzat Fabric rendszerként vagy SeAssignPrimaryTokenPrivilege rendelkező felhasználóként kell futnia. |
|ServiceFactoryRegistrationTimeout| Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus|Adja meg az időtartam másodpercben. Az időkorlát értékét a szinkronizálási regisztrálása (állapot nélküli és állapotalapú) ServiceFactory hívás |
|ServiceTypeDisableFailureThreshold |Egész szám, alapértelmezett értéke 1 |Dinamikus|Ez a utána FailoverManager (FM) tiltsa le a szolgáltatás típusának ezen a csomóponton, majd próbálja meg egy másik csomópont elhelyezésre értesítés hiba száma küszöbértékét. |
|ServiceTypeDisableGraceInterval|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(30)|Dinamikus|Adja meg az időtartam másodpercben. Időtartam elteltével letiltható a szolgáltatás típusa |
|ServiceTypeRegistrationTimeout |Idő (másodpercben), az alapértelmezett érték 300 |Dinamikus|A ServiceType fabric regisztrálni kell az engedélyezett maximális idő |
|UseContainerServiceArguments|bool, alapértelmezett értéke igaz|Statikus|Ez a konfiguráció arra utasítja a üzemeltető docker-démon (megadott config üzemeltetés) megadásának argumentumok ugráshoz.|

## <a name="httpgateway"></a>HttpGateway
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|AktívFigyelők |Uint, alapértelmezett érték az 50 |Statikus| Olvasási és az http-kiszolgáló üzenetsorban közzéteendő száma. Ez vezérli, amelyeket teljesíteni tudja az HttpGateway egyidejű kérelmek száma. |
|HttpGatewayHealthReportSendInterval |Idő (másodpercben), az alapértelmezett érték 30 |Statikus|Adja meg az időtartam másodpercben. Az időköz, amelyen a Http-átjáró halmozott egészségügyi küld a Health Manager jelentések. |
|IsEnabled|Bool, alapértelmezett érték a False (hamis) |Statikus| Engedélyezi vagy letiltja a HttpGateway. HttpGateway alapértelmezés szerint le van tiltva. |
|MaxEntityBodySize |Uint, az alapértelmezett érték 4194304 |Dinamikus|Lehetővé teszi a szervezet, amely http-kérést a várható maximális méretét. Alapértelmezett érték: 4MB. Httpgateway sikertelen lesz egy kérelmet, ha rendelkezik egy szervezet méretű > ezt az értéket. Minimális olvasási adatrészlet mérete 4096 bájtos. Ezért azt kell > = 4096. |

## <a name="imagestoreclient"></a>ImageStoreClient
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|ClientCopyTimeout | Idő (másodpercben), alapértelmezett érték a 1800 |Dinamikus| Adja meg az időtartam másodpercben. Időtúllépési érték a legfelső szintű kérelmet kép Store szolgáltatás. |
|ClientDefaultTimeout | Idő (másodpercben), az alapértelmezett beállítás 180 |Dinamikus| Adja meg az időtartam másodpercben. Összes nem feltöltési/nem letöltési kérelem időtúllépés értékét (pl. létezik; törlése) rendszerkép Store szolgáltatáshoz. |
|ClientDownloadTimeout | Idő (másodpercben), alapértelmezett érték a 1800 |Dinamikus| Adja meg az időtartam másodpercben. Időtúllépési érték a legfelső szintű letöltési kérelem lemezkép Store szolgáltatáshoz. |
|ClientListTimeout | Idő (másodpercben), alapértelmezett érték a 600 |Dinamikus|Adja meg az időtartam másodpercben. Időtúllépési érték a legfelső szintű listája lekérési kép Store szolgáltatáshoz. |
|ClientUploadTimeout |Idő (másodpercben), alapértelmezett érték a 1800 |Dinamikus|Adja meg az időtartam másodpercben. Időtúllépési érték a legfelső szintű feltöltést kép Store szolgáltatáshoz. |

## <a name="imagestoreservice"></a>ImageStoreService
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|Engedélyezve |Bool, alapértelmezett érték a False (hamis) |Statikus|A ImageStoreService Enabled jelzőt. Alapértelmezett: False (hamis) |
|MinReplicaSetSize | Int, alapértelmezett érték 3 |Statikus|A MinReplicaSetSize ImageStoreService számára. |
|PlacementConstraints | sztring, alapértelmezett érték a "" |Statikus| A PlacementConstraints ImageStoreService számára. |
|QuorumLossWaitDuration | Idő (másodpercben), alapértelmezett érték a MaxValue |Statikus| Adja meg az időtartam másodpercben. A QuorumLossWaitDuration ImageStoreService számára. |
|ReplicaRestartWaitDuration | Idő (másodpercben), alapértelmezett érték a 60.0 * 30 |Statikus|Adja meg az időtartam másodpercben. A ReplicaRestartWaitDuration ImageStoreService számára. |
|StandByReplicaKeepDuration | Idő (másodpercben), alapértelmezett érték a 3600.0 * 2 |Statikus| Adja meg az időtartam másodpercben. A StandByReplicaKeepDuration ImageStoreService számára. |
|TargetReplicaSetSize | Int, alapértelmezett érték 7 |Statikus|A TargetReplicaSetSize ImageStoreService számára. |

## <a name="ktllogger"></a>KtlLogger
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, alapértelmezett érték 1 |Dinamikus|Azt a jelzőt, amely azt jelzi, ha a memória beállításait konfigurálni kell, hogy dinamikusan és automatikusan. Ha a nulla, majd a memória-konfigurációs beállítások közvetlenül használhatók, és ne módosítsa a rendszer feltételek alapján. Ha az egyik, majd a memória beállításainak konfigurálása automatikusan történik, ezért változhat a rendszer feltételek alapján. |
|MaximumDestagingWriteOutstandingInKB | Int, alapértelmezett érték 0 |Dinamikus|A KB-os, hogy a megosztott naplót a modern, a dedikált napló előzetes száma. A 0 korlátlan jelzi.
|SharedLogId |sztring, alapértelmezett érték a "" |Statikus|Közös naplózási tároló egyedi GUID azonosítója. Használatát "" Ha az alapértelmezett elérési út a fabric adatgyökere alatt. |
|SharedLogPath |sztring, alapértelmezett érték a "" |Statikus|Helyre helyezi el a megosztott naplózási tároló elérési útja és fájlneve neve. Használatát "" az alapértelmezett elérési út alapján a fabric adatgyökere használatával. |
|SharedLogSizeInMB |Int, az alapértelmezett érték 8192 |Statikus|Az a közös naplózási tároló lefoglalása MB száma. |
|WriteBufferMemoryPoolMaximumInKB | Int, alapértelmezett érték 0 |Dinamikus|A száma, hogy az írási memória pufferkészletben akár nő KB. A 0 korlátlan jelzi. |
|WriteBufferMemoryPoolMinimumInKB |Int, az alapértelmezett érték 8388608 |Dinamikus|Kezdetben a írási pufferkészlet-memória lefoglalása KB száma. Használja a 0 korlátlan alapértelmezett jelzi az alábbi SharedLogSizeInMB összhangban kell lennie. |

## <a name="management"></a>Kezelés
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|AzureStorageMaxConnections | Int, alapértelmezett érték az 5000-es |Dinamikus|Az azure storage egyidejű kapcsolatok maximális száma. |
|AzureStorageMaxWorkerThreads | int, alapértelmezett értéke 25 |Dinamikus|Párhuzamos munkaszálak maximális száma. |
|AzureStorageOperationTimeout | Idő (másodpercben), alapértelmezett érték a 6000 |Dinamikus|Adja meg az időtartam másodpercben. Időtúllépés xstore művelet elvégzéséhez. |
|CleanupApplicationPackageOnProvisionSuccess|bool, alapértelmezett érték a hamis |Dinamikus|Ez a konfiguráció engedélyezése vagy letiltása az automatikus törléséhez az alkalmazáscsomagot a sikeres üzembe helyezése. |
|DisableChecksumValidation | Bool, alapértelmezett érték a False (hamis) |Statikus| Ez a konfiguráció lehetővé teszi számunkra, hogy engedélyezi vagy letiltja az alkalmazások üzembe helyezése során ellenőrzőösszegének ellenőrzése. |
|DisableServerSideCopy | Bool, alapértelmezett érték a False (hamis) |Statikus|Ez a konfiguráció engedélyezése vagy letiltása az alkalmazáscsomagot a ImageStore a kiszolgálóoldali másolatát alkalmazások üzembe helyezése során. |
|ImageCachingEnabled | Bool, alapértelmezett érték az IGAZ |Statikus|Ez a konfiguráció lehetővé teszi számunkra, hogy engedélyezi vagy letiltja a gyorsítótárazás. |
|ImageStoreConnectionString |SecureString |Statikus|A legfelső szintű ImageStore a kapcsolati karakterlánc. |
|ImageStoreMinimumTransferBPS | Int, alapértelmezett érték az 1024 |Dinamikus|A minimális átviteli sebesség, a fürt és ImageStore között. Ez az érték szolgál az időkorlátot határozza meg, a külső ImageStore elérésekor. Módosítsa ezt az értéket csak akkor, ha a fürt és a ImageStore között késés nagy ahhoz, hogy a külső ImageStore töltheti le a fürt több időt. |

## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **A paraméter** | **Megengedett értékek** |**Szabályzat frissítése**| **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, alapértelmezett értéke None|Dinamikus|Meghatározza, hogy a MetricActivityThresholds készletét a metrikákat a fürt számára. Terheléselosztás fog működni, ha maxNodeLoad MetricActivityThresholds hosszabb. A metrikák töredezettségmentesítési azt határozza meg a terhelés egyenlő mennyisége, vagy amely a Service Fabric figyelembe veszi a csomópont üres alá |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **A paraméter** | **Megengedett értékek** |**Szabályzat frissítése**| **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, alapértelmezett értéke None|Dinamikus|Meghatározza, hogy a MetricBalancingThresholds készletét a metrikákat a fürt számára. Terheléselosztás fog működni, ha maxNodeLoad/minNodeLoad MetricBalancingThresholds hosszabb. Töredezettségmentesítési fog működni, ha legalább egy FD vagy UD maxNodeLoad/minNodeLoad MetricBalancingThresholds kisebb. |

## <a name="namingservice"></a>NamingService
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, alapértelmezett érték 0 |Statikus|A fenntartani a gyorsítótárban LRU szolgáltatás leírása (0: Nincs korlát beállítása) elnevezési átjárónál bejegyzések maximális száma. |
|MaxClientConnections |Int, alapértelmezett érték 1000 |Dinamikus|A maximálisan megengedett száma átjáró ügyfélkapcsolatok száma. |
|MaxFileOperationTimeout |Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus|Adja meg az időtartam másodpercben. A fájl store szolgáltatási művelet számára engedélyezett maximális időkorlát. Próbáljon nagyobb időtúllépést megadó kérelmeket a rendszer elutasítja. |
|MaxIndexedEmptyPartitions |Int, alapértelmezett érték 1000 |Dinamikus|Az értesítési gyorsítótár újracsatlakozására ügyfelek szinkronizálásához indexelt, mely akkor is üres partíciók maximális száma. Ez a szám fent üres partíciókat növekvő keresési verzió az indexből törlődni fog. Újracsatlakozás a ügyfelek továbbra is szinkronizálása és kihagyott üres partíció frissítéseket; de a szinkronizálási protokoll drágább válik. |
|MaxMessageSize |Int, alapértelmezett érték 4\*1024\*1024 |Statikus|Az üzenetek maximális mérete az ügyfél-kommunikációhoz csomópont elnevezési használatakor. Szolgáltatásmegtagadási támadás enyhítése; alapértelmezett érték: 4MB. |
|MaxNamingServiceHealthReports | Int, alapértelmezett érték 10 |Dinamikus|Tároló elnevezési lassú műveletek maximális száma nem megfelelő állapotú jelentések egyszerre szolgáltatás. Ha 0; minden lassú művelet küldi el. |
|MaxOperationTimeout |Idő (másodpercben), alapértelmezett érték a 600 |Dinamikus|Adja meg az időtartam másodpercben. Az engedélyezett műveletek maximális időtúllépése. Próbáljon nagyobb időtúllépést megadó kérelmeket a rendszer elutasítja. |
|MaxOutstandingNotificationsPerClient |Int, alapértelmezett érték 1000 |Dinamikus|A szálankénti függőben lévő értesítések előtt egy ügyfél-regisztrációk maximális számát az átjáró által kényszerített le van zárva. |
|MinReplicaSetSize | Int, alapértelmezett érték 3 |Nem engedélyezett| Írjon be egy frissítés befejezéséhez szükséges elnevezési szolgáltatásban replikák minimális száma. Ha ennél kevesebb replika aktív, a rendszer a megbízhatóság rendszer megtagadja a frissítések a Naming Service Store a replikák visszaállításáig. Ez az érték soha nem lehet több, mint a TargetReplicaSetSize. |
|PartitionCount |Int, alapértelmezett érték 3 |Nem engedélyezett|Az elnevezési szolgáltatás partíciók száma tárolására létrehozni. Mindegyik partíció, amely megfelel az index egyetlen partíciókulcsot tulajdonosa tehát partíciókulcsok [0; PartitionCount) létezik. A méretezési csoport, amely az elnevezési szolgáltatásban minden olyan biztonsági replika által tárolt adatok átlagos lerövidítésével végre elnevezési szolgáltatásban partíciók növekszik számának növelésével meg; az erőforrások megnövekedett felhasználás áron (PartitionCount óta * ReplicaSetSize szolgáltatás replikák fenn kell tartani).|
|PlacementConstraints | sztring, alapértelmezett érték a "" |Nem engedélyezett| Elhelyezési korlátozás az elnevezési szolgáltatás. |
|QuorumLossWaitDuration | Idő (másodpercben), alapértelmezett érték a MaxValue |Nem engedélyezett| Adja meg az időtartam másodpercben. Ha egy elnevezési szolgáltatásban bekerült kvórum elvesztése; az időmérő elindul. A lejárat után a FM figyelembe veszi a lefelé replikákat elveszett; és megpróbálja helyreállítani a kvórum. Nem, hogy ez az adatvesztést eredményezhet. |
|RepairInterval | Idő (másodpercben), alapértelmezett érték az 5 |Statikus| Adja meg az időtartam másodpercben. Indul el, amelyben az elnevezési inkonzisztenciát javítsa ki a szolgáltató tulajdonosa és a tulajdonos neve közötti időköz. |
|ReplicaRestartWaitDuration | Idő (másodpercben), az alapértelmezett érték (60.0 * 30)|Nem engedélyezett| Adja meg az időtartam másodpercben. Ha egy elnevezési szolgáltatásban replika leáll; az időmérő elindul. A lejárat után a FM megkezdik cserélje le a replikákat, amelyek le (azt nem még tartja őket elveszett). |
|ServiceDescriptionCacheLimit | Int, alapértelmezett érték 0 |Statikus| Karbantartása az LRU szolgáltatás adatleíró gyorsítótár a Naming Store szolgáltatás (0: Nincs korlát beállítása) a bejegyzések maximális száma. |
|ServiceNotificationTimeout |Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus|Adja meg az időtartam másodpercben. Az időtúllépés használatos, ha a szolgáltatási értesítések kézbesítése az ügyfélnek. |
|StandByReplicaKeepDuration | Idő (másodpercben), alapértelmezett érték a 3600.0 * 2 |Nem engedélyezett| Adja meg az időtartam másodpercben. Ha egy elnevezési szolgáltatásban replika térjen vissza egy lefelé állapotból; Ez előfordulhat, hogy már helyett. Ez az időzítő határozza meg, mennyi ideig a FM fogja megőrizni a készenléti replika előtt, és elveti azt. |
|TargetReplicaSetSize |Int, alapértelmezett érték 7 |Nem engedélyezett|A replika száma mindegyik partíció az elnevezési szolgáltatásban tároló állítja be. Replikakészletek számának növelése növeli az az információ a Naming Store szolgáltatás; a megbízhatósági szint a változás, hogy az adatok elvesznek eredményeként csomóponthibáknak; csökkentésével a Windows Fabric, és mennyi ideig megnövekedett terhelés áron vesz igénybe az elnevezési adatok frissítését.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **A paraméter** | **Megengedett értékek** |**Szabályzat frissítése**| **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, alapértelmezett értéke None|Dinamikus|Metrika neve; / csomópont kapacitás százaléka használt pufferként néhány szabad hely a csomóponton feladatátvétel esetére megőrzése érdekében. |

## <a name="nodecapacities"></a>NodeCapacities
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Statikus|Különböző metrikák következő fürt(ök) gyűjteménye. |

## <a name="nodedomainids"></a>NodeDomainIds
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Statikus|Azt ismerteti, hogy a tartalék tartományok egy csomópont tartozik. A tartalék tartomány URI, amely a csomópont az Adatközpont helyét ismerteti keresztül van meghatározva.  Tartalék tartomány URI formátuma vannak fd: / fd/URI elérési út szegmens követ.|
|UpgradeDomainId |sztring, alapértelmezett érték a "" |Statikus|Ismerteti a frissítési tartomány egy csomópont tartozik. |

## <a name="nodeproperties"></a>NodeProperties
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Statikus|Csomópont-tulajdonságok kulcs-érték sztringpárok gyűjteménye. |

## <a name="paas"></a>Paas
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|ClusterId |sztring, alapértelmezett érték a "" |Nem engedélyezett|X509 a konfiguráció védelmét a háló által használt tanúsítvány. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|Számlálók |Sztring | Dinamikus |Gyűjtendő teljesítményszámlálókat vesszővel tagolt listája. |
|IsEnabled |Bool, alapértelmezett érték az IGAZ | Dinamikus |Jelző azt jelzi, hogy engedélyezve van-e a helyi csomóponton számláló teljesítménygyűjtés. |
|MaxCounterBinaryFileSizeInMB |Int, alapértelmezett érték 1 | Dinamikus |Maximális mérete (MB) számláló teljesítménye bináris fájl esetében. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, alapértelmezett érték 10 | Dinamikus |Maximális időköz (másodperc) után, amely egy új teljesítmény számláló bináris fájl jön létre. |
|SamplingIntervalInSeconds |Int, alapértelmezett érték 60 | Dinamikus |Mintavételi időköz gyűjtött teljesítményszámlálók. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, alapértelmezett érték 0 | Dinamikus|Meghatározza, hogy a kapcsolat korlátozás prioritását: 0: rögzített; 1: helyreállítható; negatív: figyelmen kívül hagyja. |
|ApplicationCapacityConstraintPriority | Int, alapértelmezett érték 0 | Dinamikus|Meghatározza, hogy a kapacitás korlátozás prioritását: 0: rögzített; 1: helyreállítható; negatív: figyelmen kívül hagyja. |
|AutoDetectAvailableResources|bool, alapértelmezett értéke igaz|Statikus|Ez a konfiguráció aktiválják (Processzor és memória) csomóponton elérhető erőforrások automatikus felismerése Ha ez a konfiguráció beállítása true – rendelkezéseitől olvassa el a valódi kapacitások és javítsa ki azokat, ha a felhasználó rossz fürt(ök) megadva, vagy nem adhat meg hozzájuk minden Ha ez a konfiguráció értéke false - lesz  egy figyelmeztetés, hogy a felhasználó rossz fürt(ök); megadott nyomkövetési de a nem megfelelő. ami azt jelenti, hogy a felhasználó szeretne kapni a megadott kapacitás > mint a csomópont valóban rendelkezik, vagy ha a kapacitások nincs definiálva; azt fogja feltételezni, hogy korlátlan kapacitásra |
|BalancingDelayAfterNewNode | Idő (másodpercben), alapértelmezett érték a 120 |Dinamikus|Adja meg az időtartam másodpercben. Nem indulnak tevékenységek terheléselosztás egy új csomópont a felvett ezen az időn belül. |
|BalancingDelayAfterNodeDown | Idő (másodpercben), alapértelmezett érték a 120 |Dinamikus|Adja meg az időtartam másodpercben. Nem indulnak tevékenységek terheléselosztás egy csomópont le esemény után ezen az időn belül. |
|CapacityConstraintPriority | Int, alapértelmezett érték 0 | Dinamikus|Meghatározza, hogy a kapacitás korlátozás prioritását: 0: rögzített; 1: helyreállítható; negatív: figyelmen kívül hagyja. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, az alapértelmezett érték 20 | Dinamikus|Meghatározza, hogy hány egymást követő alkalommal sem, amely ResourceBalancer által kiállított áthelyezések száma – a rendszer elveti előtt kell elvégezni, diagnosztika és állapotával kapcsolatos figyelmeztetések vannak kibocsátva. Negatív: Nincsenek figyelmeztetések rendelkezésre e feltétel alapján. |
|ConstraintFixPartialDelayAfterNewNode | Idő (másodpercben), alapértelmezett érték a 120 |Dinamikus| Adja meg az időtartam másodpercben. DDo FaultDomain nem javítása és UpgradeDomain megkötés megsértésének egy új csomópont a felvett ezen az időn belül. |
|ConstraintFixPartialDelayAfterNodeDown | Idő (másodpercben), alapértelmezett érték a 120 |Dinamikus| Adja meg az időtartam másodpercben. Nem Fix FaultDomain és UpgradeDomain megkötés megsértésének ne egy csomópont le esemény után ezen az időn belül. |
|ConstraintViolationHealthReportLimit | Int, alapértelmezett érték az 50 |Dinamikus| Határozza meg, hogy hányszor korlátozás megsértése replika előtt kell elvégezni, diagnosztika és a rendszerállapot-jelentéseket vannak kibocsátva tartósan megoldatlan kell rendelkezik. |
|DetailedConstraintViolationHealthReportLimit | Int, az alapértelmezett érték 200 |Dinamikus| Határozza meg, hogy hányszor korlátozás megsértése replika előtt rendszerhez kell tartósan megoldatlan diagnosztikai történik, és részletes jelentések vannak kibocsátva állapot. |
|DetailedDiagnosticsInfoListLimit | int, alapértelmezés szerinti 15 |Dinamikus| Határozza meg (részletes információkkal) diagnosztikai bejegyzések száma / diagnosztika a csonkolás előtt tartalmazza megkötést.|
|DetailedNodeListLimit | int, alapértelmezés szerinti 15 |Dinamikus| A csomópontok maximális száma a helyezett replika jelentések csonkolás előtt tartalmazza megkötést számát határozza meg. |
|DetailedPartitionListLimit | int, alapértelmezés szerinti 15 |Dinamikus| Diagnosztikai bejegyzésenként egy korlátozást a diagnosztika a csonkolás előtt közé tartozik a partíciók száma határozza meg. |
|DetailedVerboseHealthReportLimit | Int, az alapértelmezett érték 200 | Dinamikus|Határozza meg, hogy hányszor helyezett replikájának előtt rendszerhez kell tartósan helyezett részletes rendszerállapot-jelentések vannak kibocsátva. |
|FaultDomainConstraintPriority | Int, alapértelmezett érték 0 |Dinamikus| Meghatározza, hogy a tartalék tartomány korlátozás prioritását: 0: rögzített; 1: helyreállítható; negatív: figyelmen kívül hagyja. |
|GlobalMovementThrottleCountingInterval | Idő (másodpercben), alapértelmezett érték a 600 |Statikus| Adja meg az időtartam másodpercben. Adja meg, amelyek esetében szeretne nyomon követni (GlobalMovementThrottleThreshold együtt használva) a tartomány replika típusú áthelyezések száma az elmúlt időköz hossza. Állítható 0 figyelmen kívül hagyja a globális szabályozás elő. |
|GlobalMovementThrottleThreshold | Uint, alapértelmezett érték 1000 |Dinamikus| Áthelyezések száma – a terheléselosztás fázisban az elmúlt időszakban GlobalMovementThrottleCountingInterval által jelzett engedélyezett maximális számát. |
|GlobalMovementThrottleThresholdForBalancing | Uint, alapértelmezett érték 0 | Dinamikus|Áthelyezések száma – az elmúlt időszakban GlobalMovementThrottleCountingInterval által jelzett fázisban terheléselosztás engedélyezett maximális számát. 0 azt jelzi, hogy nincs korlát. |
|GlobalMovementThrottleThresholdForPlacement | Uint, alapértelmezett érték 0 |Dinamikus| Áthelyezések száma – az elmúlt időszakban GlobalMovementThrottleCountingInterval.0 által jelzett elhelyezési fázisban engedélyezett maximális száma azt jelzi, hogy nincs korlát.|
|GlobalMovementThrottleThresholdPercentage|dupla az alapértelmezett érték 0|Dinamikus|A teljes áthelyezések száma – engedélyezett (a fürtben található replikák száma százalékában kifejezve), a terheléselosztás és elhelyezési fázisban az elmúlt időszakban GlobalMovementThrottleCountingInterval által jelzett maximális számát. 0 azt jelzi, hogy nincs korlát. Ha ez és a megadott GlobalMovementThrottleThreshold; korlátozóbb korlát szolgál majd.|
|GlobalMovementThrottleThresholdPercentageForBalancing|dupla az alapértelmezett érték 0|Dinamikus|Áthelyezések száma – terheléselosztási fázisában (a PLB replikák száma százalékában kifejezve) az elmúlt időszakban GlobalMovementThrottleCountingInterval által jelzett engedélyezett maximális számát. 0 azt jelzi, hogy nincs korlát. Ha ez és a megadott GlobalMovementThrottleThresholdForBalancing; korlátozóbb korlát szolgál majd.|
|InBuildThrottlingAssociatedMetric | sztring, alapértelmezett érték a "" |Statikus| A szabályozási társított metrika neve. |
|InBuildThrottlingEnabled | Bool, alapértelmezett érték a False (hamis) |Dinamikus| Határozza meg a beépített szabályozás engedélyezve van-e. |
|InBuildThrottlingGlobalMaxValue | Int, alapértelmezett érték 0 |Dinamikus|A globálisan engedélyezett a build replikák maximális száma. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, alapértelmezett érték a False (hamis) | Dinamikus|Meghatározza, hogy ha feladatátvételi egység frissítés bármely típusú megszakítási gyors vagy lassú terheléselosztási futtatni. A "false" terheléselosztási futtatása megszakad, ha a megadott FailoverUnit: a létrehozott és törlése; Hiányzik a replikák; módosította elsődleges replika helyére vagy a replikák száma megváltozott. Terheléselosztás a Futtatás nem szakad más esetekben – ha FailoverUnit: extra replikát; bármely replika jelző; megváltozott csak a partíció verzió vagy bármely egyéb módosítani. |
|MinConstraintCheckInterval | Idő (másodpercben), az alapértelmezett érték 1 |Dinamikus| Adja meg az időtartam másodpercben. Határozza meg a minimális, hogy mennyi ideig kell telnie két egymást követő megkötés ellenőrizze kerekíti. |
|MinLoadBalancingInterval | Idő (másodpercben), alapértelmezett érték az 5 |Dinamikus| Adja meg az időtartam másodpercben. Határozza meg a minimális, hogy mennyi ideig két egymást követő terheléselosztási kerekít előtt át kell adnia. |
|MinPlacementInterval | Idő (másodpercben), az alapértelmezett érték 1 |Dinamikus| Adja meg az időtartam másodpercben. Határozza meg a minimális, hogy mennyi ideig két egymást követő elhelyezési kerekít előtt át kell adnia. |
|MoveExistingReplicaForPlacement | Bool, alapértelmezett érték az IGAZ |Dinamikus|Beállítás, amely meghatározza, hogy ha a meglévő replikát áthelyezése az Elhelyezés során. |
|MovementPerPartitionThrottleCountingInterval | Idő (másodpercben), alapértelmezett érték a 600 |Statikus| Adja meg az időtartam másodpercben. Az elmúlt időköz, amelynek replika áthelyezések száma – az egyes partíciók (MovementPerPartitionThrottleThreshold együtt használva) nyomon követésére hosszát jelzik. |
|MovementPerPartitionThrottleThreshold | Uint, alapértelmezett érték az 50 |Dinamikus| Nincs terheléselosztással kapcsolatos adatátviteli fordul elő egy partíció, ha a terheléselosztási az adott partíció replikák kapcsolódó áthelyezések száma elérte vagy túllépte a MovementPerFailoverUnitThrottleThreshold által jelzett az elmúlt időszakban MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, alapértelmezett érték a False (hamis) |Dinamikus| Beállítás, amely meghatározza a szülő replikák áthelyezhető-e kapcsolat megkötések javításához.|
|PartiallyPlaceServices | Bool, alapértelmezett érték az IGAZ |Dinamikus| Meghatározza, hogy ha a fürt összes szolgáltatás replika kerülnek, "mindent vagy semmit" korlátozott megfelelő csomópontok megadott számukra.|
|PlaceChildWithoutParent | Bool, alapértelmezett érték az IGAZ | Dinamikus|Beállítás, amely azt határozza meg, ha az alárendelt szolgáltatás replika is elhelyezni, ha nincs szülő replika nem fel. |
|PlacementConstraintPriority | Int, alapértelmezett érték 0 | Dinamikus|Meghatározza a prioritását az elhelyezési korlátozás: 0: rögzített; 1: helyreállítható; negatív: figyelmen kívül hagyja. |
|PlacementConstraintValidationCacheSize | Int, alapértelmezett érték a 10000 |Dinamikus| A tábla elhelyezési korlátozás kifejezésében gyorsítótárazását és gyors ellenőrzés használt mérete korlátozza. |
|PlacementSearchTimeout | Idő (másodpercben), alapértelmezett érték a 0.5-ös |Dinamikus| Adja meg az időtartam másodpercben. Ha például a szolgáltatások; Keresse meg a hosszú, legfeljebb, mielőtt az eredményt visszaküldi. |
|PLBRefreshGap | Idő (másodpercben), az alapértelmezett érték 1 |Dinamikus| Adja meg az időtartam másodpercben. Határozza meg a minimális, hogy mennyi ideig kell telnie PLB újra frissíti az állapotát. |
|PreferredLocationConstraintPriority | Int, alapértelmezett érték 2| Dinamikus|Meghatározza a prioritását az előnyben részesített földrajzi megszorítás: 0: rögzített; 1: helyreállítható; 2: optimalizálás; negatív: figyelmen kívül hagyása |
|PreventTransientOvercommit | Bool, alapértelmezett érték a False (hamis) | Dinamikus|Meghatározza, hogy kell PLB azonnal száma szabadul fel által kezdeményezett a kurzor erőforrásokon. Alapértelmezett; PLB kezdeményezheti kilép a, és ugyanazon a csomóponton átmeneti hozhat létre, amely az áthelyezés szükségesnél. A paraméter TRUE értékre megakadályozza, hogy ezeket milyen, overcommits és igény szerinti töredezettségmentesítés (más néven placementWithMove) tiltható le. |
|ScaleoutCountConstraintPriority | Int, alapértelmezett érték 0 |Dinamikus| Meghatározza, hogy a horizontális felskálázás száma korlátozás prioritását: 0: rögzített; 1: helyreállítható; negatív: figyelmen kívül hagyja. |
|SwapPrimaryThrottlingAssociatedMetric | sztring, alapértelmezett érték a ""|Statikus| A szabályozási társított metrika neve. |
|SwapPrimaryThrottlingEnabled | Bool, alapértelmezett érték a False (hamis)|Dinamikus| Határozza meg, hogy a lapozófájl-kapacitás-elsődleges-szabályozás engedélyezve van-e. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, alapértelmezett érték 0 |Dinamikus| Lapozófájl-kapacitás elsődleges replika globálisan engedélyezett maximális száma. |
|TraceCRMReasons |Bool, alapértelmezett érték az IGAZ |Dinamikus|Megadja, hogy nyomon követése a tulajdonos áthelyezések száma – a működési események csatorna CRM okait. |
|UpgradeDomainConstraintPriority | Int, alapértelmezett érték 1| Dinamikus|Meghatározza, hogy a frissítési tartomány korlátozás prioritását: 0: rögzített; 1: helyreállítható; negatív: figyelmen kívül hagyja. |
|UseMoveCostReports | Bool, alapértelmezett érték a False (hamis) | Dinamikus|Arra utasítja a figyelmen kívül hagyja a pontozó függvény; költség eleme, LB a kurzor jobban elosztott terhelésű elhelyezésre eredményül kapott potenciálisan nagy száma. |
|UseSeparateSecondaryLoad | Bool, alapértelmezett érték az IGAZ | Dinamikus|Beállítás, amely azt határozza meg, ha használja a különböző másodlagos betöltése. |
|ValidatePlacementConstraint | Bool, alapértelmezett érték az IGAZ |Dinamikus| Itt adhatja meg, e szolgáltatás PlacementConstraint kifejezése érvényesítési szolgáltatások ServiceDescription frissítésekor. |
|VerboseHealthReportLimit | Int, az alapértelmezett érték 20 | Dinamikus|Határozza meg, hogy hányszor egy replika rendelkezik olyan helyezett go, mielőtt egy állapotfigyelési figyelmeztetése jelentett hozzá (Ha részletes állapotfigyelő jelentési engedélyezve van). |

## <a name="reconfigurationagent"></a>ReconfigurationAgent
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Idő (másodpercben), az alapértelmezett érték 900 |Dinamikus|Adja meg az időtartam másodpercben. Az időtartam, amelyhez a rendszer, amely rendelkezik a replikák szolgáltatásgazdák megszakítása előtt várjon nézetben ragadnak, zárja be az alkalmazás frissítése során.|
|FabricUpgradeMaxReplicaCloseDuration | Idő (másodpercben), az alapértelmezett érték 900 |Dinamikus| Adja meg az időtartam másodpercben. Az időtartam, amelyhez a rendszer, amely rendelkezik a replikák szolgáltatásgazdák megszakítása előtt várjon nézetben ragadnak, zárja be a fabric frissítése során. |
|GracefulReplicaShutdownMaxDuration|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus|Adja meg az időtartam másodpercben. Zárja be az az időtartam, amelyhez a rendszer, amely rendelkezik a replikák szolgáltatásgazdák megszakítása előtt várjon mappában. Ha ezt az értéket 0-ra van beállítva, replikák nem lehet kérni bezárásához.|
|NodeDeactivationMaxReplicaCloseDuration | Idő (másodpercben), az alapértelmezett érték 900 |Dinamikus|Adja meg az időtartam másodpercben. Az időtartam, amelyhez a rendszer, amely rendelkezik a replikák szolgáltatásgazdák megszakítása előtt várjon nézetben ragadnak, zárja be a csomópont inaktiválása során. |
|PeriodicApiSlowTraceInterval | Idő (másodpercben), az alapértelmezett érték 5 perc |Dinamikus| Adja meg az időtartam másodpercben. PeriodicApiSlowTraceInterval az időköz, amelyben lassú API-hívást fog szerepelnie az API-figyelő által határozza meg. |
|ReplicaChangeRoleFailureRestartThreshold|Int, alapértelmezett érték 10|Dinamikus| Egész szám. Adja meg az API-hibák száma elteltével automatikusan-kockázatcsökkentési művelet (replika újraindítás) érvényesek elsődleges előléptetése során. |
|ReplicaChangeRoleFailureWarningReportThreshold|int, alapértelmezett érték 2147483647|Dinamikus| Egész szám. Adja meg az API-hibák száma utána állapotjelentés figyelmeztetés generál elsődleges előléptetése során.|
|ServiceApiHealthDuration | Idő (másodpercben), az alapértelmezett érték 30 perc |Dinamikus| Adja meg az időtartam másodpercben. ServiceApiHealthDuration határozza meg, mennyi ideig tegye azt várja, mielőtt azt jelenti nem megfelelő állapotú szolgáltatás API-hoz. |
|ServiceReconfigurationApiHealthDuration | Idő (másodpercben), az alapértelmezett érték 30 |Dinamikus| Adja meg az időtartam másodpercben. ServiceReconfigurationApiHealthDuration határozza meg, mennyi ideig tegye azt várja, mielőtt azt a jelentés nem megfelelő állapotú szolgáltatás API-hoz. Ez az API-hívás, amely hatással van a rendelkezésre állás vonatkozik.|

## <a name="replication"></a>Replikáció
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése**| **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|Időtartam, az alapértelmezett érték Common::TimeSpan::FromMilliseconds(15)|Statikus|Adja meg az időtartam másodpercben. Meghatározza, hogy az, hogy mennyi ideig vár a replikátor, miután kapott egy művelet nyugtázása vissza küldése előtt. Ez az időtartam alatt fogadott műveletnél a nyugtázás a küldött vissza egy adott üzenet -> csökkentése hálózati forgalmat, de potenciálisan csökkenti az átviteli sebességének a replikátor fog rendelkezni.|
|MaxCopyQueueSize|uint, alapértelmezett 1024|Statikus|Ez az a maximális értéke határozza meg, hogy a várólista replikációs műveletek óvjuk, amely a kezdeti értéket. Vegye figyelembe, hogy 2 hatványának kell lennie. Ha a Futtatás ideje alatt az üzenetsor méretének művelethez növekszik szabályozva lesz az elsődleges és másodlagos gyártóitól között.|
|MaxPrimaryReplicationQueueMemorySize|Uint, alapértelmezett érték 0|Statikus|Ez az az elsődleges replikációs sor maximális értéke.|
|MaxPrimaryReplicationQueueSize|uint, alapértelmezett 1024|Statikus|Ez a sikerült léteznek az elsődleges replikációs várólistában lévő műveletek maximális számát. Vegye figyelembe, hogy 2 hatványának kell lennie.|
|MaxReplicationMessageSize|uint, alapértelmezett 52428800|Statikus|Replikációs műveletek üzenetek maximális mérete. Alapértelmezett érték 50 MB-ot.|
|MaxSecondaryReplicationQueueMemorySize|Uint, alapértelmezett érték 0|Statikus|Ez az a másodlagos replikációs várólistában lévő bájtok maximális értéke.|
|MaxSecondaryReplicationQueueSize|uint, alapértelmezett értéke 2048|Statikus|Ez a meglévő sikerült a másodlagos replikációs várólistában lévő műveletek maximális számát. Vegye figyelembe, hogy 2 hatványának kell lennie.|
|QueueHealthMonitoringInterval|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(30)|Statikus|Adja meg az időtartam másodpercben. Ez az érték határozza meg, hogy az adott időszakban, a replikátor által használt monitorozni bármilyen figyelmeztetés vagy hiba a replikációs művelet sorok. "0" érték letiltja az állapotfigyelés |
|QueueHealthWarningAtUsagePercent|uint, alapértelmezett értéke 80|Statikus|Ez az érték határozza meg a replikációs várólista használata (százalék) elteltével azt jelentést figyelmeztetés magas várólista kihasználtsága. Tesszük QueueHealthMonitoringInterval egy türelmi időszak után. Ha a várólista kihasználtsága a türelmi időszak ezen százalékos arány alá esik.|
|ReplicatorAddress|sztring, alapértelmezett "localhost:0"|Statikus|A végpont egy karakterlánc-"IP:Port" annak érdekében, hogy a műveletek küldése/fogadása más replikákkal rendelkező kapcsolatot a Windows Fabric-replikátor által használt formában.|
|ReplicatorListenAddress|sztring, alapértelmezett "localhost:0"|Statikus|A végpont egy karakterlánc-"IP:Port" műveletek fogadjon más replikák a Windows Fabric-replikátor által használt formában.|
|ReplicatorPublishAddress|sztring, alapértelmezett "localhost:0"|Statikus|A végpont egy karakterlánc-"IP:Port" műveletek küldendő egyéb replikák a Windows Fabric-replikátor által használt formában.|
|retryInterval|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(5)|Statikus|Adja meg az időtartam másodpercben. Ha egy művelet elveszik, vagy ez az időzítő elutasított határozza meg, milyen gyakran próbálkozik újra a replikátor küldése a műveletet.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése**| **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|IsEnabled|bool, alapértelmezett érték a hamis |Statikus|Ha a szolgáltatás engedélyezve van a fürtben, vagy nem szabályozza. |

## <a name="runas"></a>Futtatás mint
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|Ezért |sztring, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók nevét. Ez csak akkor szükséges, "Tartomanyifelhasznalo" vagy "ManagedServiceAccount" fiók típusa. Érvényes értékek: "tartomány\felhasználó" vagy "user@domain". |
|RunAsAccountType|sztring, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók típusa. Erre azért van szükség, minden futtató szakasz érvényes értékek: "Tartomanyifelhasznalo/NetworkService/ManagedServiceAccount vagy LocalSystem".|
|Futtatási_jelszó|sztring, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók jelszavát. Ez csak akkor van szükség "Tartomanyifelhasznalo" típusához. |

## <a name="runasdca"></a>RunAs_DCA
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|Ezért |sztring, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók nevét. Ez csak akkor szükséges, "Tartomanyifelhasznalo" vagy "ManagedServiceAccount" fiók típusa. Érvényes értékek: "tartomány\felhasználó" vagy "user@domain". |
|RunAsAccountType|sztring, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók típusa. Erre azért van szükség, minden futtató szakasz érvényes értékek: "LocalUser/Tartomanyifelhasznalo/NetworkService/ManagedServiceAccount vagy LocalSystem". |
|Futtatási_jelszó|sztring, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók jelszavát. Ez csak akkor van szükség "Tartomanyifelhasznalo" típusához. |

## <a name="runasfabric"></a>RunAs_Fabric
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|Ezért |sztring, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók nevét. Ez csak akkor szükséges, "Tartomanyifelhasznalo" vagy "ManagedServiceAccount" fiók típusa. Érvényes értékek: "tartomány\felhasználó" vagy "user@domain". |
|RunAsAccountType|sztring, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók típusa. Erre azért van szükség, minden futtató szakasz érvényes értékek: "LocalUser/Tartomanyifelhasznalo/NetworkService/ManagedServiceAccount vagy LocalSystem". |
|Futtatási_jelszó|sztring, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók jelszavát. Ez csak akkor van szükség "Tartomanyifelhasznalo" típusához. |

## <a name="runashttpgateway"></a>RunAs_HttpGateway
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|Ezért |sztring, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók nevét. Ez csak akkor szükséges, "Tartomanyifelhasznalo" vagy "ManagedServiceAccount" fiók típusa. Érvényes értékek: "tartomány\felhasználó" vagy "user@domain". |
|RunAsAccountType|sztring, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók típusa. Erre azért van szükség, minden futtató szakasz érvényes értékek: "LocalUser/Tartomanyifelhasznalo/NetworkService/ManagedServiceAccount vagy LocalSystem". |
|Futtatási_jelszó|sztring, alapértelmezett érték a "" |Dinamikus|Azt jelzi, hogy a futtató fiók jelszavát. Ez csak akkor van szükség "Tartomanyifelhasznalo" típusához. |

## <a name="security"></a>Biztonság
| **A paraméter** | **Megengedett értékek** |**Szabályzat frissítése**| **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|AADClientApplication|sztring, alapértelmezett érték a ""|Statikus|Natív ügyfél alkalmazás nevét vagy Azonosítóját jelölő Fabric ügyfelek |
|AADClusterApplication|sztring, alapértelmezett érték a ""|Statikus|Webes API-alkalmazás neve vagy a fürt jelölő azonosító |
|AADTenantId|sztring, alapértelmezett érték a ""|Statikus|Bérlő azonosítója (GUID) |
|AdminClientCertThumbprints|sztring, alapértelmezett érték a ""|Dinamikus|Rendszergazdai szerepkör az ügyfelek által használt tanúsítványok ujjlenyomatai. Egy név vesszővel tagolt lista. |
|AdminClientClaims|sztring, alapértelmezett érték a ""|Dinamikus|Minden lehetséges jogcím rendszergazdai ügyfelektől; a várt felhasználónévként ClientClaims; Ez a lista belsőleg lekérdezi hozzáadott ClientClaims; így nincs szükség ClientClaims bejegyzések is hozzáadhat. |
|AdminClientIdentities|sztring, alapértelmezett érték a ""|Dinamikus|Rendszergazdai szerepkör; háló ügyfelének Windows identitások emelt szintű fabric operations engedélyezésére használja. Egy vesszővel elválasztott listát; mindegyik bejegyzés a domain Fióknév vagy a csoport nevét. A kényelem; a fabric.exe futtató fiók automatikusan rendszergazdai szerepkör; tehát van ServiceFabricAdministrators csoportba. |
|CertificateExpirySafetyMargin|Időtartam, az alapértelmezett érték Common::TimeSpan::FromMinutes(43200)|Statikus|Adja meg az időtartam másodpercben. Tanúsítvány lejáratának; biztonsági időkorlát tanúsítvány egészségügyi jelentés figyelmeztetési állapotra vált az OK gombra, ha ennél nagyobb közelebb a lejárati. Alapértelmezett érték 30 nap. |
|CertificateHealthReportingInterval|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(3600 * 8)|Statikus|Adja meg az időtartam másodpercben. Adja meg a tanúsítvány állapotának jelentéskészítési; időköz az alapértelmezett 8 óra; 0-ra a beállítás letiltja a tanúsítvány állapotfigyelő jelentési |
|ClientCertThumbprints|sztring, alapértelmezett érték a ""|Dinamikus|A fürt; kommunikáljon az ügyfelek által használt tanúsítványok ujjlenyomatai fürt használja, ez engedélyezi a bejövő kapcsolatot. Egy név vesszővel tagolt lista. |
|ClientClaimAuthEnabled|bool, alapértelmezett érték a hamis|Statikus|Azt jelzi, ha a jogcím-alapú hitelesítés engedélyezve van-e az ügyfeleken; Ezt a beállítást igaz implicit módon ClientRoleEnabled állítja be. |
|ClientClaims|sztring, alapértelmezett érték a ""|Dinamikus|Az összes lehetséges jogcímeket várt ügyfelek átjáróhoz való csatlakozáskor. Ez a "Vagy" lista: ClaimsEntry \| \| ClaimsEntry \| \| ClaimsEntry... egyes ClaimsEntry "AND" lista: takar = ClaimValue & & takar = ClaimValue & & takar = ClaimValue... |
|ClientIdentities|sztring, alapértelmezett érték a ""|Dinamikus|A FabricClient; Windows-identitások elnevezési átjáró használja, ez a bejövő kapcsolatok engedélyezéséhez. Egy vesszővel elválasztott listát; mindegyik bejegyzés a domain Fióknév vagy a csoport nevét. A kényelem; a fabric.exe futtató fiókot automatikusan engedélyezett ezért olyan csoport ServiceFabricAllowedUsers és ServiceFabricAdministrators. |
|ClientRoleEnabled|bool, alapértelmezett érték a hamis|Statikus|Azt jelzi, hogy az ügyfél szerepkör engedélyezve van-e; megadása esetén TRUE; az ügyfelek hozzárendelt szerepkörök alapján a felhasználók identitását. A v2-ben; engedélyezés, ez azt jelenti, hogy nem AdminClientCommonNames/AdminClientIdentities az ügyfél csak is csak olvasási műveletek végrehajtásához. |
|ClusterCertThumbprints|sztring, alapértelmezett érték a ""|Dinamikus|A fürt; csatlakoztathatják tanúsítványok ujjlenyomatai név vesszővel elválasztott listáját. |
|ClusterCredentialType|sztring, alapértelmezett értéke "None"|Nem engedélyezett|Annak érdekében, hogy a fürt védelme használandó biztonsági hitelesítő adatok típusát jelzi. Érvényes értékek: "Nincs/X509/Windows" |
|ClusterIdentities|sztring, alapértelmezett érték a ""|Dinamikus|A fürtcsomópontok; Windows-identitások fürt tagsági engedélyezési használja. Egy vesszővel elválasztott listát; mindegyik bejegyzés a domain Fióknév vagy a csoport neve |
|ClusterSpn|sztring, alapértelmezett érték a ""|Nem engedélyezett|A fürt; a szolgáltatás egyszerű neve fabric futtatásakor egyetlen tartományi felhasználóként (csoportosan felügyelt számítógépfiók vagy tartományi felhasználói fiók). Az egyszerű szolgáltatásnév bérleti figyelők és a figyelők a fabric.exe: összevonási figyelői; belső replikációs figyelői; futásidejű szolgáltatást figyelő és elnevezési átjáró-figyelő. Ez üresen kell hagyni gép fiókok; háló futtatásakor Ebben az esetben a kiszolgálóoldali számítási figyelő csatlakozás egyszerű Szolgáltatásnevet, a figyelő szállítási cím. |
|CrlCheckingFlag|uint, alapértelmezett 0x40000000|Dinamikus|Alapértelmezett tanúsítvány láncot érvényesítési jelző; összetevő-specifikus jelző; felülbírálható például a összevonási/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ A 0 érték letiltja a CRL ellenőrzése teljes támogatott értékeinek listáján csak beállítás által dwFlags CertGetCertificateChain, dokumentált: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|Időtartam, az alapértelmezett érték Common::TimeSpan::FromMinutes(15)|Dinamikus|Adja meg az időtartam másodpercben. Mennyi ideig CRL-ellenőrzés le van tiltva a megadott tanúsítvány után a kapcsolat nélküli hiba; Ha a CRL offline hiba figyelmen kívül hagyható. |
|CrlOfflineHealthReportTtl|Időtartam, az alapértelmezett érték Common::TimeSpan::FromMinutes(1440)|Dinamikus|Adja meg az időtartam másodpercben. |
|DisableFirewallRuleForDomainProfile| bool, alapértelmezett értéke igaz |Statikus| Azt jelzi, ha az tűzfalszabály nem engedélyezhető a tartományi profilba |
|DisableFirewallRuleForPrivateProfile| bool, alapértelmezett értéke igaz |Statikus| Azt jelzi, ha a tűzfalszabályt is engedélyeznie kell a személyes profil | 
|DisableFirewallRuleForPublicProfile| bool, alapértelmezett értéke igaz | Statikus|Azt jelzi, ha a tűzfalszabályt is engedélyeznie kell a nyilvános profil |
|FabricHostSpn| sztring, alapértelmezett érték a "" |Statikus| A Hálóbeli; a szolgáltatás egyszerű neve Ha a háló (csoportosan felügyelt számítógépfiók vagy tartományi felhasználói fiókot) egyetlen tartományi felhasználóként fut, és a Hálóbeli számítógépfiók alatt fut. Legyen a Hálóbeli; az egyszerű Szolgáltatásnevet az IPC-figyelő amely alapértelmezés szerint üresen kell hagyni, mivel a Hálóbeli számítógépes fiókja alatt fut. |
|IgnoreCrlOfflineError|bool, alapértelmezett érték a hamis|Dinamikus|E figyelmen kívül hagyja a CRL offline hiba, ha a kiszolgálóoldali ellenőrzi a bejövő ügyféltanúsítványok |
|IgnoreSvrCrlOfflineError|bool, alapértelmezett értéke igaz|Dinamikus|E figyelmen kívül hagyja a CRL offline hiba, ha ügyféloldali ellenőrzi a bejövő kiszolgálói tanúsítványok; alapértelmezett érték a true értékre. A visszavont tanúsítványok támadások megkövetelése DNS; csökkenése nehezebb, mint a visszavont ügyféltanúsítványok. |
|ServerAuthCredentialType|sztring, alapértelmezett értéke "None"|Statikus|Annak érdekében, hogy a FabricClient és a fürt közötti kommunikáció védelméhez használandó biztonsági hitelesítő adatok típusát jelzi. Érvényes értékek: "Nincs/X509/Windows" |
|ServerCertThumbprints|sztring, alapértelmezett érték a ""|Dinamikus|Ügyfelek; kommunikáljon a fürt által használt tanúsítványok ujjlenyomatai ügyfelek használják ezt a fürt hitelesítéséhez. Egy név vesszővel tagolt lista. |
|SettingsX509StoreName| sztring, alapértelmezett a "MY"| Dinamikus|A konfiguráció védelmét háló által használt X509 tanúsítványtárolóból |
|UseClusterCertForIpcServerTlsSecurity|bool, alapértelmezett érték a hamis|Statikus|Hogy fürttanúsítvány segítségével IPC kiszolgáló TLS biztonságos átviteli egység |
|X509Folder|sztring, alapértelmezett /var/lib/waagent|Statikus|Mappa ahol X509 találhatók tanúsítványok és titkos kulcsok |

## <a name="securityadminclientx509names"></a>Biztonsági/AdminClientX509Names
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, alapértelmezett értéke None|Dinamikus|Ez a "Name" és "Értéket" pár listáját. Minden egyes "név" DnsName X509, vagy a tulajdonos köznapi nevének van rendszergazdai műveletek engedélyezett tanúsítványok. Az egy adott "Name" "értéke" tanúsítvány-ujjlenyomatok rögzítés kibocsátó külön vesszővel tagolt listája, ha nem üres, a közvetlen rendszergazdai ügyféltanúsítványok-kibocsátó kell lennie a listában. |

## <a name="securityclientaccess"></a>Biztonsági/ClientAccess
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|ActivateNode |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció az aktiváláshoz egy csomópontot. |
|CancelTestCommand |sztring, alapértelmezett az "Admin" |Dinamikus| Egy adott TestCommand - megszakítja, ha közötti átviteléhez. |
|CodePackageControl |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció kódcsomagok újraindulásának. |
|CreateApplication |sztring, alapértelmezett az "Admin" | Dinamikus|Biztonsági konfiguráció az alkalmazás létrehozása. |
|CreateComposeDeployment|sztring, alapértelmezett az "Admin"| Dinamikus|Létrehoz egy új központi telepítési compose-fájl által leírt |
|CreateName |sztring, alapértelmezett az "Admin" |Dinamikus|Biztonsági konfiguráció elnevezési URI létrehozásához. |
|CreateService |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció szolgáltatások létrehozásakor. |
|CreateServiceFromTemplate |sztring, alapértelmezett az "Admin" |Dinamikus|Szolgáltatás létrehozása sablon alapján biztonsági beállításainak konfigurálása. |
|CreateVolume|sztring, alapértelmezett az "Admin"|Dinamikus|Kötet létrehozása |
|DeactivateNode |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció esetében egy csomópont inaktiválása. |
|DeactivateNodesBatch |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció esetében több csomópont inaktiválása. |
|Törlés |sztring, alapértelmezett az "Admin" |Dinamikus| Rendszerkép biztonsági beállításokkal tárolja az ügyfél-törlési művelet. |
|Deleteapplication függvényhez |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció alkalmazása a törlésre. |
|DeleteComposeDeployment|sztring, alapértelmezett az "Admin"| Dinamikus|Az összeállítás üzemelő példány törlése |
|DeleteName |sztring, alapértelmezett az "Admin" |Dinamikus|Biztonsági konfiguráció elnevezési URI törlésre. |
|DeleteService |sztring, alapértelmezett az "Admin" |Dinamikus|Biztonsági konfiguráció szolgáltatás törlésre. |
|DeleteVolume|sztring, alapértelmezett az "Admin"|Dinamikus|Egy kötet törlése.| 
|EnumerateProperties |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" | Dinamikus|Biztonsági konfiguráció elnevezési számbavétele. |
|EnumerateSubnames |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Biztonsági konfiguráció az elnevezési URI enumerálása. |
|FileContent |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció lemezképhez ügyfél fájlátvitel (külső fürthöz) tárolja. |
|FileDownload |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások a lemezképet tároló ügyfél fájl letöltése kezdeményezés (külső fürthöz). |
|FinishInfrastructureTask |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció befejezési infrastruktúra feladatokhoz. |
|Getchaosreport hívásban | sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Lekéri a Chaos állapotát belül egy adott időtartományt. |
|GetClusterConfiguration | sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" | Dinamikus|Kapott GetClusterConfiguration partíción. |
|GetClusterConfigurationUpgradeStatus | sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Kapott GetClusterConfigurationUpgradeStatus partíción. |
|GetFabricUpgradeStatus |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Biztonsági beállítások a fürt frissítési állapotának lekérdezése. |
|GetNodeDeactivationStatus |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció az inaktiválási állapot ellenőrzése. |
|GetNodeTransitionProgress | sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Biztonsági konfiguráció esetében egy csomópont átmenet parancsot a folyamat első. |
|GetPartitionDataLossProgress | sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" | Dinamikus|Lekéri egy invoke adatok elvesztése api-hívás az a folyamat állapotát. |
|GetPartitionQuorumLossProgress | sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Lekéri egy invoke kvórum elvesztése api-hívás az a folyamat állapotát. |
|GetPartitionRestartProgress | sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Lekéri a folyamatban van egy újraindítás partíció api-hívás. |
|GetServiceDescription |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Biztonsági konfiguráció szolgáltatás hosszú-lekérdezési értesítéseket és a szolgáltatások leírásai olvasása. |
|GetStagingLocation |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció lemezképhez lekéréséhez hely átmeneti ügyfél tárolja. |
|GetStoreLocation |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció lemezképhez ügyfél tároló helye lekérés tárolja. |
|GetUpgradeOrchestrationServiceState|sztring, alapértelmezett az "Admin"| Dinamikus|Egy partíción GetUpgradeOrchestrationServiceState kapott |
|GetUpgradesPendingApproval |sztring, alapértelmezett az "Admin" |Dinamikus| Kapott GetUpgradesPendingApproval partíción. |
|GetUpgradeStatus |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Biztonsági beállítások a lekérdezés alkalmazás frissítési állapot. |
|InternalList |sztring, alapértelmezett az "Admin" | Dinamikus|Biztonsági konfiguráció lemezképhez ügyfél file list művelet (belső) tárolja. |
|InvokeContainerApi|sztring, alapértelmezett az "Admin"|Dinamikus|Tároló API meghívása |
|InvokeInfrastructureCommand |sztring, alapértelmezett az "Admin" |Dinamikus| A feladat-kezelési parancsok infrastruktúra biztonsági beállításainak konfigurálása. |
|InvokeInfrastructureQuery |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" | Dinamikus|Biztonsági konfigurációs feladatok lekérdezéséhez. |
|Lista |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" | Dinamikus|Biztonsági konfiguráció lemezképhez ügyfél file list művelet tárolja. |
|MoveNextFabricUpgradeDomain |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások egy frissítési tartományt explicit a fürtfrissítések folytatásához. |
|MoveNextUpgradeDomain |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció folytatásához alkalmazásfrissítések explicit frissítési tartománnyal. |
|MoveReplicaControl |sztring, alapértelmezett az "Admin" | Dinamikus|Helyezze át a replikát. |
|NameExists |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" | Dinamikus|Biztonsági konfiguráció az elnevezési URI meglétének ellenőrzése. |
|NodeControl |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció indítása; leállítása folyamatban van; és a csomópont újraindítása. |
|NodeStateRemoved |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció jelentéskészítési csomópont állapota eltávolítva. |
|Ping |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Biztonsági konfiguráció az ügyfél pingelésre. |
|PredeployPackageToNode |sztring, alapértelmezett az "Admin" |Dinamikus| Üzembe helyezés előtti API-t. |
|PrefixResolveService |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Biztonsági beállítások a megfelelő szolgáltatásra előtag feloldásához. |
|PropertyReadBatch |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Biztonsági konfiguráció elnevezése tulajdonság olvasási műveleteket. |
|PropertyWriteBatch |sztring, alapértelmezett az "Admin" |Dinamikus|Biztonsági konfigurációk elnevezési tulajdonság írása. |
|ProvisionApplicationType |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció az alkalmazások üzembe helyezése típusa. |
|ProvisionFabric |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció MSI és/vagy a fürt jegyzékfájl kiépítéséhez. |
|Lekérdezés |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Biztonsági beállítások a lekérdezésekhez. |
|RecoverPartition |sztring, alapértelmezett az "Admin" | Dinamikus|Biztonsági konfiguráció az egy partíció helyreállításához. |
|RecoverPartitions |sztring, alapértelmezett az "Admin" | Dinamikus|Biztonsági konfiguráció partíciók helyreállításához. |
|RecoverServicePartitions |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció szolgáltatáspartíciók helyreállításához. |
|RecoverSystemPartitions |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfigurációs rendszer szolgáltatáspartíciók helyreállításához. |
|RemoveNodeDeactivations |sztring, alapértelmezett az "Admin" |Dinamikus| Több csomóponton visszaállítása inaktiválásra vonatkozó biztonsági beállítások. |
|ReportFabricUpgradeHealth |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció esetében a jelenlegi frissítési folyamat állapotát a fürtfrissítések folytatása. |
|ReportFault |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció tartalék jelentéskészítéshez. |
|ReportHealth |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció állapotát jelentéskészítési. |
|ReportUpgradeHealth |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások a jelenlegi frissítési folyamat állapotát az alkalmazásfrissítések folytatásához. |
|ResetPartitionLoad |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Biztonsági konfiguráció alaphelyzetbe állítása terhelés egy failoverUnit számára. |
|ResolveNameOwner |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" | Dinamikus|Biztonsági konfiguráció elnevezési URI tulajdonos feloldásához. |
|ResolvePartition |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" | Dinamikus|Biztonsági konfiguráció rendszerszolgáltatások feloldásához. |
|ResolveService |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Biztonsági konfiguráció megfelelő-alapú névfeloldáshoz. |
|ResolveSystemService|sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói"|Dinamikus| Biztonsági konfiguráció rendszerszolgáltatások feloldása |
|RollbackApplicationUpgrade |sztring, alapértelmezett az "Admin" |Dinamikus| Alkalmazásfrissítések visszaállítása biztonsági konfigurációját. |
|RollbackFabricUpgrade |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások a fürtfrissítések visszaállítása. |
|ServiceNotifications |sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Biztonsági konfiguráció eseményalapú szolgáltatási értesítésekhez. |
|SetUpgradeOrchestrationServiceState|sztring, alapértelmezett az "Admin"| Dinamikus|Egy partíción SetUpgradeOrchestrationServiceState kapott |
|StartApprovedUpgrades |sztring, alapértelmezett az "Admin" |Dinamikus| Kapott StartApprovedUpgrades partíción. |
|StartChaos |sztring, alapértelmezett az "Admin" |Dinamikus| Elindítja a Chaos –, ha azt már nem indult el. |
|StartClusterConfigurationUpgrade |sztring, alapértelmezett az "Admin" |Dinamikus| Kapott StartClusterConfigurationUpgrade partíción. |
|StartInfrastructureTask |sztring, alapértelmezett az "Admin" | Dinamikus|Biztonsági konfigurációs feladatok indításához. |
|StartNodeTransition |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció egy csomópontváltó indításához. |
|StartPartitionDataLoss |sztring, alapértelmezett az "Admin" |Dinamikus| Adatvesztés partíción kapott. |
|StartPartitionQuorumLoss |sztring, alapértelmezett az "Admin" |Dinamikus| Kvórum elvesztése a partíción lévő kapott. |
|StartPartitionRestart |sztring, alapértelmezett az "Admin" |Dinamikus| Egyidejűleg újraindítja vagy egésze a replikák partíció. |
|StopChaos |sztring, alapértelmezett az "Admin" |Dinamikus| A Chaos – leállítja elindítása. |
|ToggleVerboseServicePlacementHealthReporting | sztring, alapértelmezett érték a "rendszergazda\|\|felhasználói" |Dinamikus| Részletes ServicePlacement HealthReporting Vizualizációtól biztonsági beállításainak konfigurálása. |
|UnprovisionApplicationType |sztring, alapértelmezett az "Admin" |Dinamikus| Az alkalmazás típus leépítése biztonságának konfigurálása. |
|UnprovisionFabric |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció az MSI és/vagy Cluster Manifest telepítésének visszavonását. |
|UnreliableTransportControl |sztring, alapértelmezett az "Admin" |Dinamikus| Nem megbízható átviteli hozzáadásához és eltávolításához viselkedéseket. |
|UpdateService |sztring, alapértelmezett az "Admin" |Dinamikus|Szolgáltatásfrissítések biztonsági beállításainak konfigurálása. |
|UpgradeApplication |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági konfiguráció elindításáról és alkalmazásfrissítések megszakítása. |
|UpgradeComposeDeployment|sztring, alapértelmezett az "Admin"| Dinamikus|Az összeállítás üzemelő példány frissítése |
|UpgradeFabric |sztring, alapértelmezett az "Admin" |Dinamikus| Biztonsági beállítások a fürtfrissítések indítása. |
|Feltöltés |sztring, alapértelmezett az "Admin" | Dinamikus|Biztonsági konfiguráció lemezképhez ügyfél feltöltési művelet tárolja. |

## <a name="securityclientcertificateissuerstores"></a>Biztonsági/ClientCertificateIssuerStores
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, alapértelmezett értéke None |Dinamikus|X509 kibocsátói tanúsítvány tárolja az ügyféltanúsítványok; Name = clientIssuerCN; Érték = tárolók vesszővel elválasztott listája |

## <a name="securityclientx509names"></a>Biztonsági/ClientX509Names
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, alapértelmezett értéke None|Dinamikus|Ez a "Name" és "Értéket" pár listáját. Minden egyes "név" van a tulajdonos köznapi nevének vagy DnsName X509 az Ügyfélműveletek engedélyezett tanúsítványok. Az egy adott "Name" "értéke" tanúsítvány-ujjlenyomatok rögzítés kibocsátó külön vesszővel tagolt listája, ha nem üres, a közvetlen kibocsátó ügyféltanúsítványok kell lennie a listában.|

## <a name="securityclustercertificateissuerstores"></a>Biztonsági/ClusterCertificateIssuerStores
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, alapértelmezett értéke None |Dinamikus|X509 kibocsátói tanúsítvány tanúsítványtárolói fürttanúsítványok; Name = clusterIssuerCN; Érték = tárolók vesszővel elválasztott listája |

## <a name="securityclusterx509names"></a>Biztonsági/ClusterX509Names
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, alapértelmezett értéke None|Dinamikus|Ez a "Name" és "Értéket" pár listáját. Egyes "név" a tulajdonos köznapi nevének vagy DnsName X509, fürt-műveletek engedélyezett tanúsítványok. Az egy adott "Name" "értéke" tanúsítvány-ujjlenyomatok rögzítés kibocsátó külön vesszővel tagolt listája, ha nem üres, a fürt tanúsítványok közvetlen kibocsátó kell lennie a listában.|

## <a name="securityservercertificateissuerstores"></a>Biztonsági/ServerCertificateIssuerStores
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, alapértelmezett értéke None |Dinamikus|X509 kibocsátói tanúsítvány tanúsítványtárolói kiszolgálói tanúsítványok; Name = serverIssuerCN; Érték = tárolók vesszővel elválasztott listája |

## <a name="securityserverx509names"></a>Biztonsági/ServerX509Names
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, alapértelmezett értéke None|Dinamikus|Ez a "Name" és "Értéket" pár listáját. Minden egyes "név" van a tulajdonos köznapi nevének vagy a X509 DnsName műveletekhez történő engedélyezett tanúsítványok. Az egy adott "Name" "értéke" tanúsítvány-ujjlenyomatok rögzítés kibocsátó külön vesszővel tagolt listája, ha nem üres, a közvetlen kiszolgálói tanúsítványok-kibocsátó kell lennie a listában.|

## <a name="setup"></a>Beállítás
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|ContainerNetworkName|sztring, alapértelmezett érték a ""| Statikus |A hálózat nevét, amikor a tároló hálózat beállítása.|
|ContainerNetworkSetup|bool, alapértelmezett érték a hamis| Statikus |Hozza létre a tároló hálózatot kell-e.|
|FabricDataRoot |Sztring | Nem engedélyezett |A Service Fabric-adatok gyökérkönyvtára. Alapértelmezett Azure d:\svcfab érték |
|FabricLogRoot |Sztring | Nem engedélyezett |Service fabric log gyökérkönyvtára. Ez az, ahol SF naplók és nyomkövetések kerülnek. |
|NodesToBeRemoved|sztring, alapértelmezett érték a ""| Dinamikus |A csomópontok, amely konfigurációs frissítés részeként el kell távolítani. (Csak az önálló verziója telepítéseinek)|
|ServiceRunAsAccountName |Sztring | Nem engedélyezett |A fiók neve, a fabric host szolgáltatás futtatására. |
|SkipContainerNetworkResetOnReboot|bool, alapértelmezett érték a hamis|NotAllowed|Hogy elmaradjon-e visszaállítása folyamatban; tároló hálózati újraindításkor.|
|SkipFirewallConfiguration |Bool, alapértelmezett érték a False (hamis) | Nem engedélyezett |Itt adhatja meg, ha a tűzfal beállításainak meg kell adnia, a rendszer, vagy nem. Ez vonatkozik, csak akkor, ha a windows tűzfalat használja. Ha külső gyártótól származó tűzfalak használ, majd nyissa meg a portokat, a rendszer és alkalmazások általi használatát |

## <a name="tokenvalidationservice"></a>TokenValidationService
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|Szolgáltatók |sztring, alapértelmezett "DSTS" |Statikus|Jogkivonat érvényesítésére alkalmazásszolgáltatók vesszővel elválasztott listáját (érvényes szolgáltatók a következők: DSTS; AAD-BEN). Jelenleg csak egyetlen szolgáltató bármikor engedélyezhető. |

## <a name="traceetw"></a>Nyomkövetési/Etw
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|Szint |Int, alapértelmezett érték 4 | Dinamikus |Nyomkövetés etw szint is igénybe vehet az érték 1, 2, 3, 4. Támogatott a nyomkövetési szintet kell hagynia a 4 |

## <a name="transactionalreplicator"></a>Tranzakciós replikátor
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Idő (másodpercben), alapértelmezett érték a 0.015 | Statikus | Adja meg az időtartam másodpercben. Meghatározza, hogy az, hogy mennyi ideig vár a replikátor, miután kapott egy művelet nyugtázása vissza küldése előtt. Ez az időtartam alatt fogadott műveletnél a nyugtázás a küldött vissza egy adott üzenet -> csökkentése hálózati forgalmat, de potenciálisan csökkenti az átviteli sebességének a replikátor fog rendelkezni. |
|MaxCopyQueueSize |Uint, az alapértelmezett érték 16384 | Statikus |Ez az a maximális értéke határozza meg, hogy a várólista replikációs műveletek óvjuk, amely a kezdeti értéket. Vegye figyelembe, hogy 2 hatványának kell lennie. Ha a Futtatás ideje alatt az üzenetsor méretének művelethez növekszik szabályozva lesz az elsődleges és másodlagos gyártóitól között. |
|MaxPrimaryReplicationQueueMemorySize |Uint, alapértelmezett érték 0 | Statikus |Ez az az elsődleges replikációs sor maximális értéke. |
|MaxPrimaryReplicationQueueSize |Uint, az alapértelmezett érték 8192 | Statikus |Ez a sikerült léteznek az elsődleges replikációs várólistában lévő műveletek maximális számát. Vegye figyelembe, hogy 2 hatványának kell lennie. |
|MaxReplicationMessageSize |uint, alapértelmezett 52428800 | Statikus | Replikációs műveletek üzenetek maximális mérete. Alapértelmezett érték 50 MB-ot. |
|MaxSecondaryReplicationQueueMemorySize |Uint, alapértelmezett érték 0 | Statikus |Ez az a másodlagos replikációs várólistában lévő bájtok maximális értéke. |
|MaxSecondaryReplicationQueueSize |Uint, az alapértelmezett érték 16384 | Statikus |Ez a meglévő sikerült a másodlagos replikációs várólistában lévő műveletek maximális számát. Vegye figyelembe, hogy 2 hatványának kell lennie. |
|ReplicatorAddress |sztring, alapértelmezett "localhost:0" | Statikus | A végpont egy karakterlánc-"IP:Port" annak érdekében, hogy a műveletek küldése/fogadása más replikákkal rendelkező kapcsolatot a Windows Fabric-replikátor által használt formában. |

## <a name="transport"></a>Átvitel
| **A paraméter** | **Megengedett értékek** |**Szabályzat frissítése** |**Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(60)|Statikus|Adja meg az időtartam másodpercben. Időtúllépés a kapcsolat beállítása a bejövő, mind a beküldhetők oldalon (beleértve a biztonsági egyeztetés biztonságos módban) |
|FrameHeaderErrorCheckingEnabled|bool, alapértelmezett értéke igaz|Statikus|Alapértelmezett ellenőrzi az keret fejléc nem biztonságos módban; beállítása összetevő-beállítás felülbírálja ezt. |
|MessageErrorCheckingEnabled|bool, alapértelmezett érték a hamis|Statikus|Alapértelmezett ellenőrzi az üzenet fejlécét és törzsét nem biztonságos módban; beállítása összetevő-beállítás felülbírálja ezt. |
|ResolveOption|sztring, alapértelmezett érték a "Ismeretlen"|Statikus|Azt határozza meg, hogy megoldódott-e teljes Tartományneve.  Érvényes értékek: "nincs megadva vagy ipv4-/ ipv6". |
|SendTimeout|Időtartam, az alapértelmezett érték Common::TimeSpan::FromSeconds(300)|Dinamikus|Adja meg az időtartam másodpercben. Küldési időkorlátjának elakadt kapcsolat észlelése. TCP hiba jelentéseket, amelyek nem megbízható néhány környezetben. Ez előfordulhat, hogy kell igazítani a rendelkezésre álló hálózati sávszélességet és a kimenő adatok mérete alapján (\*MaxMessageSize\/\*SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Bool, alapértelmezett érték az IGAZ |Statikus| Automatikus lekérdezési és frissítési művelet a cél-állapot fájl alapján. |
|MinReplicaSetSize |Int, alapértelmezett érték 0 |Statikus |A MinReplicaSetSize UpgradeOrchestrationService számára.
|PlacementConstraints | sztring, alapértelmezett érték a "" |Statikus| A PlacementConstraints UpgradeOrchestrationService számára. |
|QuorumLossWaitDuration | Idő (másodpercben), alapértelmezett érték a MaxValue |Statikus| Adja meg az időtartam másodpercben. A QuorumLossWaitDuration UpgradeOrchestrationService számára. |
|ReplicaRestartWaitDuration | Idő (másodpercben), az alapértelmezett érték 60 perc|Statikus| Adja meg az időtartam másodpercben. A ReplicaRestartWaitDuration UpgradeOrchestrationService számára. |
|StandByReplicaKeepDuration | Idő (másodpercben), az alapértelmezett érték 60*24*7 perc |Statikus| Adja meg az időtartam másodpercben. A StandByReplicaKeepDuration UpgradeOrchestrationService számára. |
|TargetReplicaSetSize |Int, alapértelmezett érték 0 |Statikus |A TargetReplicaSetSize UpgradeOrchestrationService számára. |
|UpgradeApprovalRequired | Bool, alapértelmezett érték a False (hamis) | Statikus|Beállítása, hogy a folytatás előtt rendszergazdai jóváhagyás megkövetelése, kód frissítése. |

## <a name="upgradeservice"></a>UpgradeService
| **A paraméter** | **Megengedett értékek** | **Szabályzat frissítése** | **Útmutató vagy rövid leírása** |
| --- | --- | --- | --- |
|BaseUrl | sztring, alapértelmezett érték a "" |Statikus|Az UpgradeService BaseUrl. |
|ClusterId | sztring, alapértelmezett érték a "" |Statikus|Az UpgradeService ClusterId. |
|CoordinatorType | sztring, alapértelmezett "WUTest"|Nem engedélyezett|A CoordinatorType az UpgradeService. |
|MinReplicaSetSize | Int, alapértelmezett érték 2 |Nem engedélyezett| A MinReplicaSetSize az UpgradeService. |
|OnlyBaseUpgrade | Bool, alapértelmezett érték a False (hamis) |Dinamikus|Az UpgradeService OnlyBaseUpgrade. |
|PlacementConstraints |sztring, alapértelmezett érték a "" |Nem engedélyezett|A frissítési szolgáltatás PlacementConstraints. |
|TargetReplicaSetSize | Int, alapértelmezett érték 3 |Nem engedélyezett| A TargetReplicaSetSize az UpgradeService. |
|TestCabFolder | sztring, alapértelmezett érték a "" |Statikus| Az UpgradeService TestCabFolder. |
|X509FindType | sztring, alapértelmezett érték a ""|Dinamikus| Az UpgradeService X509FindType. |
|X509FindValue | sztring, alapértelmezett érték a "" |Dinamikus| Az UpgradeService X509FindValue. |
|X509SecondaryFindValue | sztring, alapértelmezett érték a "" |Dinamikus| X509SecondaryFindValue for UpgradeService. |
|X509StoreLocation | sztring, alapértelmezett érték a "" |Dinamikus| Az UpgradeService X509StoreLocation. |
|X509StoreName | sztring, alapértelmezett érték a "Saját"|Dinamikus|Az UpgradeService X509StoreName. |

## <a name="next-steps"></a>További lépések
Olvassa el az alábbi cikkek kezelő további tájékoztatást:

[Adja hozzá, vihetők át, távolítsa el a tanúsítványokat az Azure-fürthöz ](service-fabric-cluster-security-update-certs-azure.md) 

