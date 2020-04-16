---
title: Az Azure Service Fabric fürtbeállításainak módosítása
description: Ez a cikk ismerteti a háló beállításait és a háló frissítési szabályzatok, amelyek testre szabhatók.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: c2e280af814a3e10ad84c5ba07fc376868fcd851
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416243"
---
# <a name="customize-service-fabric-cluster-settings"></a>Service Fabric-fürt beállításainak testreszabása
Ez a cikk ismerteti a különböző háló beállításait a Service Fabric-fürt, amely testre szabható. Az Azure-ban üzemeltetett fürtök esetében testreszabhatja a beállításokat az [Azure Portalon](https://portal.azure.com) keresztül vagy egy Azure Resource Manager-sablon használatával. További információ: [Az Azure-fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-azure.md)című témakörben talál. Önálló fürtök esetén a *fürtökConfig.json* fájl frissítésével és a fürt konfigurációs frissítésével testreszabhatja a beállításokat. További információt [az Önálló fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md)című témakörben talál.

Három különböző frissítési szabályzat létezik:

- **Dinamikus** – a dinamikus konfiguráció módosításai nem okoznak folyamat-újraindítást sem a Service Fabric-folyamatok, sem a szolgáltatásgazda folyamatok. 
- **Statikus** – a statikus konfiguráció módosításai hatására a Service Fabric-csomópont újraindul a módosítás felhasználása érdekében. A csomópontok szolgáltatásai újraindulnak.
- **Nem Engedélyezett** – Ezek a beállítások nem módosíthatók. A beállítások módosításához a fürt meg semmisítése és egy új fürt létrehozása szükséges. 

Az alábbi lista a Fabric-beállításokat, amelyek testre szabható, szakasz szerint rendezve.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/http

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy (ApplicationCertificateValidationPolicy)|karakterlánc, az alapértelmezett érték "Nincs"|Statikus| Ez nem érvényesíti a kiszolgálótanúsítványt; sikeres legyen a kérés. Tekintse meg a config ServiceCertificateThumbprints a vesszővel elválasztott listáját ujjlenyomatok a távoli tanúsítványok, hogy a fordított proxy megbízhat. A fordított proxy által megbízhat távoli tanúsítvány tulajdonosnevét és kiállítói ujjlenyomatát a config ServiceCommonNameAndIssuer című dokumentumból olvassa el. További információ: [Proxy biztonságos kapcsolat visszakapcsolása](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkMéret |Uint, alapértelmezett érték: 16384 |Dinamikus| Megadja a test olvasásához használt adattömb méretét bájtban. |
|CrlCheckingFlag|uint, az alapértelmezett érték: 0x40000000 |Dinamikus| Jelzők az alkalmazás/szolgáltatás tanúsítványlánc érvényesítéséhez; pl. crl-ellenőrzés 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY A 0-ra állítás letiltja a visszavont tanúsítványok listájának ellenőrzését A támogatott értékek teljes listáját a CertGetCertificateChain dwFlagjei dokumentálják:https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|AlapértelmezettHttpRequestTimeout |Idő másodpercben. alapértelmezett 120 |Dinamikus|Adja meg az időtartományt másodpercben.  Megadja az alapértelmezett kérelem időmeghosszabbítása a http-kérelmek feldolgozása a http app átjáró. |
|ForwardClientCertificate (Továbbítügyfél-tanúsítvány)|az alapértelmezett érték HAMIS|Dinamikus|Ha hamis, a fordított proxy nem kér az ügyféltanúsítványt. Ha a beállítás igaz, fordított proxy kérni fogja az ügyfél tanúsítvány tls kézfogás és továbbítja a base64 kódolt PEM formátumú karakterláncot a szolgáltatás egy fejléc nevű X-Client-Certificate.The szolgáltatás nem felel meg a kérelem megfelelő állapotkódot a tanúsítvány adatok vizsgálata után. Ha ez igaz, és az ügyfél nem mutat be tanúsítványt, a fordított proxy egy üres fejlécet továbbít, és hagyja, hogy a szolgáltatás kezelje az esetet. Fordított proxy fog működni, mint egy átlátszó réteg. További információ: [Ügyféltanúsítvány-hitelesítés beállítása.](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy) |
|GatewayAuthCredentialType típus |karakterlánc, az alapértelmezett érték "Nincs" |Statikus| A http-alkalmazásátjáró végpontja in használandó biztonsági hitelesítő adatok típusát jelzi: Az érvényes értékek: Nincs/X509. |
|GatewayX509CertificateFindType |karakterlánc, az alapértelmezett érték a "FindByThumbprint" |Dinamikus| Azt jelzi, hogy miként kereshet tanúsítványt a GatewayX509CertificateStoreName Supported value: FindByThumbprint érték által megadott tárolóban; FindBySubjectName. |
|GatewayX509CertificateFindValue | karakterlánc, az alapértelmezett a "" |Dinamikus| A http-alkalmazásátjáró-tanúsítvány megkereséséhez használt keresési szűrőérték. Ez a tanúsítvány a https-végponton van konfigurálva, és az alkalmazás identitásának ellenőrzésére is használható, ha a szolgáltatások szükséges. Először a FindValue értéket keresse meg; és ha ez nem létezik; A FindValueSecondary keresett. |
|GatewayX509CertificateFindValueMásodlagos | karakterlánc, az alapértelmezett a "" |Dinamikus|A http-alkalmazásátjáró-tanúsítvány megkereséséhez használt keresési szűrőérték. Ez a tanúsítvány a https-végponton van konfigurálva, és az alkalmazás identitásának ellenőrzésére is használható, ha a szolgáltatások szükséges. Először a FindValue értéket keresse meg; és ha ez nem létezik; A FindValueSecondary keresett.|
|GatewayX509CertificateStoreName |karakterlánc, az alapértelmezett érték "Saját" |Dinamikus| A http app átjáró tanúsítványát tartalmazó X.509 tanúsítványtároló neve. |
|httpRequestConnectTimeout|TimeSpan, az alapértelmezett érték common::TimeSpan::FromSeconds(5)|Dinamikus|Adja meg az időtartományt másodpercben.  Megadja a http-átjáróról küldött http-kérelmek csatlakozási időmedését.  |
|IgnoreCrlOfflineError|bool, az alapértelmezett érték IGAZ|Dinamikus|A visszavont tanúsítványok listájának offline hibáját figyelmen kívül hagyja-e az alkalmazás/szolgáltatás tanúsítványának ellenőrzéséhez. |
|IsEnabled |Bool, az alapértelmezett érték hamis |Statikus| A HttpApplicationGateway engedélyezése/letiltása. A HttpApplicationGateway alapértelmezés szerint le van tiltva, és ezt a konfigurációt be kell állítani az engedélyezéséhez. |
|NumberOfParallelOperations | Uint, az alapértelmezett érték 5000 |Statikus|A http-kiszolgálóvárólistába felkönyvelni utolesztő olvasmányok száma. Ez határozza meg a HttpGateway által teljesíthető egyidejű kérelmek számát. |
|RemoveServiceResponseHeaders|karakterláncot, az alapértelmezett a "Dátum; Kiszolgáló"|Statikus|A szolgáltatásválaszból eltávolítandó válaszfejlécek pontosvesszővel vagy vesszővel tagolt listája; mielőtt továbbítana az ügyfélnek. Ha ez üres karakterláncra van állítva; adja át a szolgáltatás által visszaadott összes fejlécet. Azaz ne írja felül a dátumot és a kiszolgálót |
|ResolveServiceBackoffInterval |Idő másodpercben, az alapértelmezett érték 5 |Dinamikus|Adja meg az időtartományt másodpercben.  Megadja az alapértelmezett biztonsági szünet időközt, mielőtt újra megpróbálna egy sikertelen feloldási szolgáltatási műveletet. |
|SecureOnlyMode (BiztonságosCsak mód)|az alapértelmezett érték HAMIS|Dinamikus| SecureOnlyMode: true: Reverse Proxy csak továbbítja a biztonságos végpontokat közzé. false: Reverse Proxy továbbíthatja kérelmek biztonságos / nem biztonságos végpontok. További információ: [Proxyvégpont-kijelölési logika megfordítása.](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints)  |
|ServiceCertificateThumbprints|karakterlánc, az alapértelmezett a ""|Dinamikus|A fordított proxy által megbízhat távoli certs ujjlenyomatainak vesszővel elválasztott listája. További információ: [Proxy biztonságos kapcsolat visszakapcsolása](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|X509NameMap, az alapértelmezett érték Nincs|Dinamikus| A fordított proxy által megbízhat távoli certs tulajdonosneve és kiállítóujjlenyomata. További információ: [Proxy biztonságos kapcsolat visszakapcsolása](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|MinReplicaSetMéret|int, az alapértelmezett érték 0|Statikus|A MinReplicaSetSize for BackupRestoreService |
|Elhelyezési kényszerek|karakterlánc, az alapértelmezett a ""|Statikus|    A BackupRestore szolgáltatás elhelyezési kényszerei |
|SecretEncryptionCertThumbprint|karakterlánc, az alapértelmezett a ""|Dinamikus|A titkos titkosítási X509 tanúsítvány ujjlenyomata |
|SecretEncryptionCertX509StoreName|karakterlánc, az alapértelmezett érték "Saját"|    Dinamikus|    Ez azt a tanúsítványt jelzi, amelyet a creds titkosításához és visszafejtéséhez használ Az X.509 tanúsítványtároló neve, amelyet a Biztonsági másolat-visszaállítás szolgáltatás által használt tárolóhitelesítő adatok titkosítására használnak |
|CélReplicaSetMéret|int, az alapértelmezett érték 0|Statikus| A TargetReplicaSetSize for BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | Bool, az alapértelmezett érték hamis |Dinamikus|Azt jelzi, hogy az egyéni frissítésrendezési házirendek engedélyezettek-e. Ezzel a funkció kétfázisú frissítéssel végezhető el. A Service Fabric 6.5 támogatja a fürt- vagy alkalmazásfrissítések során a frissítési tartományok rendezési házirendjének megadását. A támogatott házirendek a numerikus, lexikografikus, a reversenumeric és a reverselexicographic. Az alapértelmezett érték a Numerikus. A szolgáltatás használatához a ClusterManager/ AllowCustomUpgradeSortPolicies fürtjegyzék-beállítást az SF 6.5-ös kód frissítése után második konfigurációs frissítési lépésként Igaz értékre kell állítani. Fontos, hogy ez két fázisban történik, ellenkező esetben a kódfrissítés összezavarodhat a frissítési sorrenddel kapcsolatban az első frissítés során.|
|EnableDefaultServicesUpgrade | Bool, az alapértelmezett érték hamis |Dinamikus|Engedélyezze az alapértelmezett szolgáltatások frissítését az alkalmazás frissítése során. Az alapértelmezett szolgáltatásleírások felülíródnak a frissítés után. |
|FabricUpgradeHealthCheckInterval |Idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Az állapotellenőrzés gyakorisága a figyelt Fabric frissítés során |
|FabricUpgradeStatusPollInterval |Idő másodpercben, az alapértelmezett érték 60 |Dinamikus|A Fabric frissítési állapotlekérdezésének gyakorisága. Ez az érték határozza meg a GetFabricUpgradeProgress-hívásfrissítési arányt |
|ImageBuilderTimeoutBuffer |Idő másodpercben, az alapértelmezett érték 3 |Dinamikus|Adja meg az időtartományt másodpercben. Az az idő, amerre az Image Builder adott időmegadási hibák visszatérhetnek az ügyfélhez. Ha ez a puffer túl kicsi; ezután az ügyfél időtúljár a kiszolgáló előtt, és általános időtúlfutási hibát kap. |
|InfrastructureTaskHealthCheckRetryTimeout | Idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Adja meg az időtartományt másodpercben. A sikertelen állapot-ellenőrzések újrapróbálkozásával töltött idő az infrastruktúra-feladat utófeldolgozása közben. Az átadott állapot-ellenőrzés megfigyelése visszaállítja ezt az időzítőt. |
|InfrastructureTaskHealthCheckStableDuration | Idő másodpercben, az alapértelmezett érték 0|Dinamikus| Adja meg az időtartományt másodpercben. Az egymást követő állapot-ellenőrzések megfigyelésére az infrastruktúra-feladat utófeldolgozása sikeres befejezése előtt eltelt idő. A sikertelen állapot-ellenőrzés megfigyelése alaphelyzetbe állítja ezt az időzítőt. |
|InfrastructureTaskHealthCheckWaitDuration |Idő másodpercben, az alapértelmezett érték 0|Dinamikus| Adja meg az időtartományt másodpercben. Az infrastruktúra-feladat utófeldolgozása után az állapotellenőrzések megkezdése előtt várakoznivaló idő. |
|InfrastructureTaskProcessingInterval infrastruktúra | Idő másodpercben, az alapértelmezett érték 10 |Dinamikus|Adja meg az időtartományt másodpercben. Az infrastruktúra-feladatfeldolgozó állapotgép által használt feldolgozási időköz. |
|MaxCommunicationTimeout |Idő másodpercben, az alapértelmezett érték 600 |Dinamikus|Adja meg az időtartományt másodpercben. A ClusterManager és más rendszerszolgáltatások (azaz a) közötti belső kommunikáció maximális időtúlára Elnevezési szolgáltatás; Feladatátvétel-kezelő stb.). Ennek az időtúltöltésnek kisebbnek kell lennie, mint a globális MaxOperationTimeout (mivel az egyes ügyfélműveletekhez több kommunikáció is lehet a rendszerösszetevők között). |
|MaxDataMigrationTimeout |Idő másodpercben, az alapértelmezett érték 600 |Dinamikus|Adja meg az időtartományt másodpercben. Az adatáttelepítési helyreállítási műveletek maximális időhahez való meghosszabbítása a Fabric-frissítés után. |
|MaxOperationRetryDelay |Idő másodpercben, az alapértelmezett érték 5|Dinamikus| Adja meg az időtartományt másodpercben. A belső újrapróbálkozások maximális késleltetése hibák esetén. |
|MaxOperationTimeout |Idő másodpercben, az alapértelmezett érték MaxValue |Dinamikus| Adja meg az időtartományt másodpercben. A Fürtkezelő belső feldolgozási műveleteinek maximális globális időhamon. |
|MaxTimeoutRetryBuffer | Idő másodpercben, az alapértelmezett érték 600 |Dinamikus|Adja meg az időtartományt másodpercben. Az időoutok miatti belső újrapróbálkozások esetén `<Original Time out> + <MaxTimeoutRetryBuffer>`a maximális működési időout a . További időmeghosszabbítás a MinOperationTimeout lépésekben hozzáadódik. |
|MinOperationTimeout | Idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Adja meg az időtartományt másodpercben. A Fürtkezelő belső feldolgozási műveleteinek minimális globális időmeghosszabbítása. |
|MinReplicaSetMéret |Int, az alapértelmezett érték 3 |Nem engedélyezett|A MinReplicaSetSize for ClusterManager. |
|Elhelyezési kényszerek | karakterlánc, az alapértelmezett a "" |Nem engedélyezett|A ClusterManager elhelyezési kényszerei. |
|QuorumLossWaitDuration |Idő másodpercben, az alapértelmezett érték MaxValue |Nem engedélyezett| Adja meg az időtartományt másodpercben. A ClusterManager QuorumLossWaitDuration. |
|ReplicaRestartWaitDuration |Idő másodpercben, az alapértelmezett érték \* (60,0 30)|Nem engedélyezett|Adja meg az időtartományt másodpercben. A ReplicaRestartWaitDuration for ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Idő másodpercben, az alapértelmezett érték 1200 |Dinamikus| Adja meg az időtartományt másodpercben. Ha a ReplicaSetCheckTimeout értéke a Duplaszó maximális értéke; akkor ez felülbírálta az értéke ennek a config céljából visszaállítás. A görgetéshez használt értéket soha nem bírálja felül a függvény. |
|SkipRollbackUpdateDefaultService | Bool, az alapértelmezett érték hamis |Dinamikus|A főkiszolgáló kihagyja a frissített alapértelmezett szolgáltatások visszaállítását az alkalmazásfrissítés visszaállítása során. |
|Készenléti replikálási időtartam | Idő másodpercben, az alapértelmezett érték (3600,0 \* 2)|Nem engedélyezett|Adja meg az időtartományt másodpercben. A ClusterManager standByReplicaKeepDuration szolgáltatása. |
|CélReplicaSetMéret |Int, az alapértelmezett érték 7 |Nem engedélyezett|A ClusterManager targetReplicaSetSize szolgáltatása. |
|UpgradeHealthCheckInterval |Idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Az állapotellenőrzések gyakorisága a figyelt alkalmazásfrissítések során |
|UpgradeStatusPollInterval |Idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Az alkalmazásfrissítési állapot lekérdezésének gyakorisága. Ez az érték határozza meg a GetApplicationUpgradeProgress hívásfrissítési arányát |
|CompleteClientRequest | Bool, az alapértelmezett érték hamis |Dinamikus| Teljes ügyfélkérés, ha a cm elfogadja. |

## <a name="common"></a>Közös

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Idő másodpercben, az alapértelmezett érték 1 |Dinamikus|Adja meg az időtartományt másodpercben. Teljesítményfigyelési időköz. A 0 vagy negatív érték beállítása letiltja a figyelést. |

## <a name="defragmentationemptynodedistributionpolicy"></a>TöredezettségmentesítésEmptyNodeDistributionPolicy
| **Paraméter** | **Engedélyezett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|KeyIntegerValueMap, az alapértelmezett érték: Nincs|Dinamikus|Itt adható meg, hogy a csomópontok kiürítésekekénül a házirend töredezettségmentesítése következik.Specifies the policy töredezettségmentesítés következik, amikor kiüríti a csomópontokat. Egy adott metrika 0 azt jelzi, hogy sf meg kell próbálnia töredezettségmentesítéscsomópontok egyenletesen az UD-k és FDs; 1 csak azt jelzi, hogy a csomópontokat töredezettségmentesítésre kell |

## <a name="defragmentationmetrics"></a>TöredezettségmentesítésMetrimek
| **Paraméter** | **Engedélyezett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|KeyBoolValueMap, az alapértelmezett érték Nincs|Dinamikus|Meghatározza a töredezettségmentesítéshez és nem a terheléselosztáshoz használandó metrikák készletét. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>TöredezettségmentesítésMetricsPercentOrNumberOfNodesTriggeringThreshold
| **Paraméter** | **Engedélyezett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|KeyDoubleValueMap, az alapértelmezett érték: Nincs|Dinamikus|A fürt töredezettségmentesítésének mérlegeléséhez szükséges szabad csomópontok számát határozza meg a tartományban lévő százalék [0,0 - 1,0] vagy az üres csomópontok száma >= 1,0 számként történő megadásával. |

## <a name="diagnostics"></a>Diagnosztika

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Csak adminhttpAudit |Bool, az alapértelmezett igaz | Dinamikus | Zárja ki azokat a HTTP-kérelmeket, amelyek nem befolyásolják a fürt állapotát a naplózásból. Jelenleg; csak a "GET" típusú kérelmek nem tartoznak ide; de ez változhat. |
|AppDiagnosticStoreAccessMegegyezést igényel |Bool, az alapértelmezett igaz | Dinamikus |Az, hogy szükség van-e a megszemélyesítésre, amikor az alkalmazás nevében hozzáfér a diagnosztikai tárolókhoz. |
|AppEtwTraceDeletionAgeInDays |Int, az alapértelmezett érték 3 | Dinamikus |Azon napok száma, amely után töröljük a régi ETL fájlokat tartalmazó alkalmazás ETW nyomokat. |
|ApplicationLogsFormatVersion |Int, az alapértelmezett érték 0 | Dinamikus |Alkalmazásnaplók formátumának verziója. A támogatott értékek 0 és 1. Az 1-es verzió több mezőt tartalmaz az ETW eseményrekordból, mint a 0-s verzió. |
|NaplózáshttpRequests |Bool, az alapértelmezett érték hamis | Dinamikus | A HTTP-naplózás be- és kikapcsolása. A naplózás célja a fürtön végrehajtott tevékenységek megtekintése; beleértve azt is, hogy ki kezdeményezte a kérelmet. Ne feledje, hogy ez a legjobb kísérlet naplózás; és nyomvesztés léphet fel. A "Felhasználó" hitelesítéssel rendelkező HTTP-kérelmek nem kerülnek rögzítésre. |
|RögzítéshttpTelemetria|Bool, az alapértelmezett igaz | Dinamikus | A HTTP-telemetria be- és kikapcsolása. A telemetriai cél az, hogy a Service Fabric képes rögzíteni telemetriai adatokat, hogy segítsen megtervezni a jövőbeli munka és a problémás területek azonosítása. A telemetriai adatok nem rögzítenek személyes adatokat vagy a kérelem törzsét. Telemetriai rögzíti az összes HTTP-kérelmek, kivéve, ha másképp van konfigurálva. |
|Fürtazonosító |Sztring | Dinamikus |A fürt egyedi azonosítója. Ez a fürt létrehozásakor jön létre. |
|Fogyasztói instances |Sztring | Dinamikus |A DCA-fogyasztói példányok listája. |
|DiskFullSafetySpaceInMB |Int, alapértelmezett érték: 1024 | Dinamikus |Hátralévő lemezterület MB-ban a DCA általi használat elleni védelem érdekében. |
|EnableCircularTraceSession |Bool, az alapértelmezett érték hamis | Statikus |A jelző azt jelzi, hogy kell-e körkörös nyomkövetési munkameneteket használni. |
|EnablePlatformEventsFileSink |Bool, az alapértelmezett érték hamis | Statikus |Lemezre írt platformesemények engedélyezése/letiltása |
|EnableTelemetry |Bool, az alapértelmezett igaz | Dinamikus |Ez engedélyezi vagy letiltja a telemetriai adatokat. |
|Csak hibákhttpTelemetry | Bool, az alapértelmezett érték hamis | Dinamikus | Ha a HTTP-telemetriai adatok rögzítése engedélyezve van; csak a sikertelen kérelmeket rögzítse. Ez segít csökkenteni a telemetriai adatokhoz létrehozott események számát. |
|HttpTelemetryCapturePercentage | int, az alapértelmezett érték 50 | Dinamikus | Ha a HTTP-telemetriai adatok rögzítése engedélyezve van; a kérelmeknek csak egy véletlenszerű százalékát rögzíti. Ez segít csökkenteni a telemetriai adatokhoz létrehozott események számát. |
|MaxDiskQuotaInMB |Int, alapértelmezett: 65536 | Dinamikus |Lemezkvóta MB-ban a Windows Fabric naplófájlokhoz. |
|Termelői példák |Sztring | Dinamikus |A DCA-gyártó példányainak listája. |

## <a name="dnsservice"></a>DnsService szolgáltatás
| **Paraméter** | **Engedélyezett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|az alapértelmezett érték HAMIS|Statikus|A particionált szolgáltatások DNS-lekérdezéseinek támogatásának engedélyezésére szolgáló jelző. A szolgáltatás alapértelmezés szerint ki van kapcsolva. További információ: [Service Fabric DNS Service.](service-fabric-dnsservice.md)|
|InstanceCount (Példányszám)|int, az alapértelmezett érték -1|Statikus|Az alapértelmezett érték -1, ami azt jelenti, hogy a DnsService minden csomóponton fut. A OneBox-nak 1-re van szüksége, mivel a DnsService jól ismert 53-as portot használ, így nem lehet több példány ugyanazon a gépen.|
|IsEnabled|az alapértelmezett érték HAMIS|Statikus|Engedélyezi/letiltja a DnsService szolgáltatást. A DnsService alapértelmezés szerint le van tiltva, és ezt a konfigurációt be kell állítani az engedélyezéséhez. |
|PartitionPrefix|karakterlánc, az alapértelmezett a "--"|Statikus|A particionált szolgáltatások DNS-lekérdezéseiben szabályozza a partícióelőtag karakterláncának értékét. Az érték: <ul><li>RFC-kompatibilisnek kell lennie, mivel egy DNS-lekérdezés része lesz.</li><li>Nem tartalmazhat ".' dot, mivel a dot zavarja a DNS-utótag viselkedését.</li><li>Nem lehet hosszabb 5 karakternél.</li><li>Nem lehet üres karakterlánc.</li><li>Ha a PartitionPrefix beállítás felülbírálva van, akkor a PartitionSuffix-et felül kell írni, és fordítva.</li></ul>További információt a [Service Fabric DNS-szolgáltatás.](service-fabric-dnsservice.md).|
|PartitionSutófix|karakterlánc, az alapértelmezett a ""|Statikus|A particionált szolgáltatások DNS-lekérdezéseiben szabályozza a partícióutótag karakterláncának értékét. Az érték: <ul><li>RFC-kompatibilisnek kell lennie, mivel egy DNS-lekérdezés része lesz.</li><li>Nem tartalmazhat ".' dot, mivel a dot zavarja a DNS-utótag viselkedését.</li><li>Nem lehet hosszabb 5 karakternél.</li><li>Ha a PartitionPrefix beállítás felülbírálva van, akkor a PartitionSuffix-et felül kell írni, és fordítva.</li></ul>További információt a [Service Fabric DNS-szolgáltatás.](service-fabric-dnsservice.md). |

## <a name="eventstoreservice"></a>EventStoreService

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|MinReplicaSetMéret|int, az alapértelmezett érték 0|Statikus|A MinReplicaSetSize for EventStore szolgáltatás |
|Elhelyezési kényszerek|karakterlánc, az alapértelmezett a ""|Statikus|    Az EventStore szolgáltatás elhelyezési kényszerei |
|CélReplicaSetMéret|int, az alapértelmezett érték 0|Statikus| Az EventStore szolgáltatás TargetReplicaSetSize szolgáltatása |

## <a name="fabricclient"></a>FabricClient (Hálóclient)

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Idő másodpercben, az alapértelmezett érték 2 |Dinamikus|Adja meg az időtartományt másodpercben. A kapcsolat időtúllépési időköze minden alkalommal, amikor az ügyfél megpróbál kapcsolatot nyitni az átjáróval.|
|HealthOperationTimeout |Idő másodpercben, az alapértelmezett érték 120 |Dinamikus|Adja meg az időtartományt másodpercben. Az Állapotkezelőnek küldött jelentésüzenet időmegadása. |
|HealthReportRetrySendInterval |Idő másodpercben, alapértelmezett 30, minimum 1 |Dinamikus|Adja meg az időtartományt másodpercben. Az az időszak, amikor a jelentéskészítő összetevő újraküldi a halmozott állapotjelentéseket az állapotkezelőnek. |
|HealthReportSendInterval |Idő másodpercben, az alapértelmezett érték 30 |Dinamikus|Adja meg az időtartományt másodpercben. Az az időszak, amikor a jelentéskészítő összetevő elküldi a halmozott állapotjelentéseket az állapotkezelőnek. |
|KeepAliveIntervalinseconds (KeepAliveIntervalInseconds) |Int, alapértelmezett érték 20 |Statikus|Az az időtartam, amikor a FabricClient átvitel e-t is megtartó üzeneteket küld az átjárónak. 0 esetében; a keepAlive le van tiltva. Pozitív értéknek kell lennie. |
|MaxFileSenderThreads |Uint, az alapértelmezett érték 10 |Statikus|A párhuzamosan átvitt fájlok maximális száma. |
|NodeAddresses (Csomópontcímek) |karakterlánc, az alapértelmezett a "" |Statikus|Az elnevezési szolgáltatással való kommunikációhoz használható különböző csomópontokon lévő címek (kapcsolati karakterláncok) gyűjteménye. Kezdetben az ügyfél véletlenszerűen csatlakozik az egyik cím kiválasztásához. Ha egynél több kapcsolati karakterlánc van megadva, és a kapcsolat kommunikációs vagy időtúllépési hiba miatt megszakad; az Ügyfél a következő cím szekvenciális használatára vált. Az újrapróbálkozások szemantikájával kapcsolatos részleteket az Újrapróbálkozások című szakaszban találja. |
|PartitionLocationCacheLimit |Int, alapértelmezett érték: 100000 |Statikus|A szolgáltatásfeloldáshoz gyorsítótárazott partíciók száma (a nincs korlátozás esetén 0-ra van állítva). |
|ÚjratryBackoffInterval |Idő másodpercben, az alapértelmezett érték 3 |Dinamikus|Adja meg az időtartományt másodpercben. A művelet újbóli megkísérlése előtt a visszalépési időköz. |
|ServiceChangePollInterval |Idő másodpercben, az alapértelmezett érték 120 |Dinamikus|Adja meg az időtartományt másodpercben. A szolgáltatás egymást követő szavazások közötti időköz az ügyfélről az átjáróra változik a regisztrált szolgáltatásváltozási értesítések visszahívásához. |

## <a name="fabrichost"></a>FabricHost között

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, az alapértelmezett érték 10 |Dinamikus|Ez az a maximális szám, amelynek rendszerújra próbálkozása sikertelen aktiválás előtt feladja. |
|AktiválásMaxRetryInterval |Idő másodpercben, az alapértelmezett érték 300 |Dinamikus|Adja meg az időtartományt másodpercben. Az aktiválás maximális újrapróbálkozási időköze. Minden folyamatos hiba esetén az újrapróbálkozási időköz min( ActivationMaxRetryInterval; Folyamatos hibák száma * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Idő másodpercben, az alapértelmezett érték 5 |Dinamikus|Adja meg az időtartományt másodpercben. Visszalépési időköz minden aktiválási hiba esetén;Minden folyamatos aktiválási hiba esetén a rendszer újra megpróbálja az aktiválást a MaxActivationFailureCount-ig. Az újrapróbálkozási időköz minden próbálkozáskor a folyamatos aktiválási hiba és az aktiválási visszalépési időköz eredménye. |
|EnableRestartManagement |Bool, az alapértelmezett érték hamis |Dinamikus|Ez a kiszolgáló újraindításának engedélyezésére szolgál. |
|EnableServiceFabricAutomaticUpdates |Bool, az alapértelmezett érték hamis |Dinamikus|Ez lehetővé teszi a háló automatikus frissítését a Windows Update-en keresztül. |
|EnableServiceFabricBaseUpgrade |Bool, az alapértelmezett érték hamis |Dinamikus|Ez a kiszolgáló alapfrissítésének engedélyezésére szolgál. |
|FailureReportingExpeditedReportingIntervalEnabled | Bool, az alapértelmezett igaz | Statikus | Gyorsabb feltöltési sebességet tesz lehetővé a DCA-ban, ha a FabricHost hibajelentési módban van. |
|FailureReportingTimeout | TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(60) | Statikus |Adja meg az időtartományt másodpercben. A DCA hibajelentésidő-elállása abban az esetben, ha a FabricHost korai indítási hibába ütközik. | 
|RunDCAOnStartupFailure | Bool, az alapértelmezett igaz | Statikus |Azt határozza meg, hogy el kell-e indítani a DCA-t a naplók feltöltéséhez, amikor indítási problémákkal szembesülnek a FabricHost-ban. | 
|StartTimeout (Kezdési idő) |Idő másodpercben, az alapértelmezett érték 300 |Dinamikus|Adja meg az időtartományt másodpercben. A fabricaktivációskezelő indítási ideje. |
|StopTimeout (StopTimeout) |Idő másodpercben, az alapértelmezett érték 300 |Dinamikus|Adja meg az időtartományt másodpercben. A szolgáltatott szolgáltatás aktiválásának időmeghosszabbítása; kikapcsolása és frissítése. |

## <a name="fabricnode"></a>FabricNode között

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ÜgyfélAuthX509FindType |karakterlánc, az alapértelmezett érték a "FindByThumbprint" |Dinamikus|Azt jelzi, hogy miként kereshet tanúsítványt az ÜgyfélAuthX509StoreName Supported érték: FindByThumbprint érték által megadott tárolóban; FindBySubjectName. |
|ClientAuthX509FindValue |karakterlánc, az alapértelmezett a "" | Dinamikus|Keresési szűrő értéke a FabricClient alapértelmezett rendszergazdai szerepkör tanúsítványának megkereséséhez. |
|ClientAuthX509FindValueMásodlagos |karakterlánc, az alapértelmezett a "" |Dinamikus|Keresési szűrő értéke a FabricClient alapértelmezett rendszergazdai szerepkör tanúsítványának megkereséséhez. |
|ClientAuthX509StoreName |karakterlánc, az alapértelmezett érték "Saját" |Dinamikus|Az Alapértelmezett hálózati szerepkör FabricClient tanúsítványát tartalmazó X.509 tanúsítványtároló neve. |
|ClusterX509FindType típus |karakterlánc, az alapértelmezett érték a "FindByThumbprint" |Dinamikus|Azt jelzi, hogy miként kereshet fürttanúsítványt a ClusterX509StoreName Supported values: "FindByThumbprint" által megadott tárolóban; "FindBySubjectName" with "FindBySubjectName"; ha több találat van; a legtávolabbi lejárati állságú t használja. |
|ClusterX509FindValue |karakterlánc, az alapértelmezett a "" |Dinamikus|A fürttanúsítvány megkereséséhez használt keresési szűrőérték. |
|ClusterX509FindValueMásodlagos |karakterlánc, az alapértelmezett a "" |Dinamikus|A fürttanúsítvány megkereséséhez használt keresési szűrőérték. |
|ClusterX509Storename |karakterlánc, az alapértelmezett érték "Saját" |Dinamikus|A fürtön belüli kommunikáció biztosításához fürttanúsítványt tartalmazó X.509 tanúsítványtároló neve. |
|EndApplicationPortRange |Int, az alapértelmezett érték 0 |Statikus|Az alrendszer üzemeltetése által kezelt alkalmazásportok vége (nincs bezárólag). Szükséges, ha az EndpointFilteringEnabled igaz a tárhelyen. |
|ServerAuthX509FindType |karakterlánc, az alapértelmezett érték a "FindByThumbprint" |Dinamikus|Azt jelzi, hogy miként kereshetkiszolgálói tanúsítványt a ServerAuthX509StoreName Supported érték: FindByThumbprint érték által megadott tárolóban; FindBySubjectName. |
|ServerAuthX509FindValue |karakterlánc, az alapértelmezett a "" |Dinamikus|A kiszolgálótanúsítvány kereséséhez használt keresési szűrőérték. |
|ServerAuthX509FindValueMásodlagos |karakterlánc, az alapértelmezett a "" |Dinamikus|A kiszolgálótanúsítvány kereséséhez használt keresési szűrőérték. |
|ServerAuthX509StoreName |karakterlánc, az alapértelmezett érték "Saját" |Dinamikus|Az első tárcsai kiszolgálói tanúsítványt tartalmazó X.509 tanúsítványtároló neve. |
|StartApplicationPortRange |Int, az alapértelmezett érték 0 |Statikus|Az alrendszer üzemeltetése által kezelt alkalmazásportok kezdete. Szükséges, ha az EndpointFilteringEnabled igaz a tárhelyen. |
|StateTraceInterval |Idő másodpercben, az alapértelmezett érték 300 |Statikus|Adja meg az időtartományt másodpercben. A csomópont állapotának nyomon követésének időköze az FM/FMM egyes csomópontjain és csomópontjaiban. |
|UserRoleClientX509FindType |karakterlánc, az alapértelmezett érték a "FindByThumbprint" |Dinamikus|Azt jelzi, hogy miként kereshet tanúsítványt a UserRoleClientX509StoreName Supported érték: FindByThumbprint érték által megadott tárolóban; FindBySubjectName. |
|UserRoleClientX509FindValue |karakterlánc, az alapértelmezett a "" |Dinamikus|A FabricClient alapértelmezett felhasználói szerepkör tanúsítványának kereséséhez használt keresési szűrőérték. |
|UserRoleClientX509FindValueMásodlagos |karakterlánc, az alapértelmezett a "" |Dinamikus|A FabricClient alapértelmezett felhasználói szerepkör tanúsítványának kereséséhez használt keresési szűrőérték. |
|UserRoleClientX509Áruházneve |karakterlánc, az alapértelmezett érték "Saját" |Dinamikus|A FabricClient alapértelmezett felhasználói szerepkörhöz tanúsítványt tartalmazó X.509 tanúsítványtároló neve. |

## <a name="failovermanager"></a>Feladatátvétel-kezelő

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Bool, az alapértelmezett érték HAMIS |Dinamikus|Megjelölés, amely jelzi, hogy engedélyezett-e a csomópont állapotának eltávolítása egy magcsomóponthoz |
|BuildReplicaTimeLimit|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(3600)|Dinamikus|Adja meg az időtartományt másodpercben. Az állapotalapú replika létrehozásának határideje; amely után figyelmeztető egészségügyi jelentést indítanak |
|ClusterPauseThreshold|int, az alapértelmezett érték 1|Dinamikus|Ha a rendszerben lévő csomópontok száma ez az érték alá csökken, akkor az elhelyezés; terheléselosztás; és a feladatátvétel leáll. |
|CreateInstanceTimeLimit|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(300)|Dinamikus|Adja meg az időtartományt másodpercben. A stateless példány létrehozásának határideje; amely után figyelmeztető egészségügyi jelentést indítanak |
|Várt fürtméret|int, az alapértelmezett érték 1|Dinamikus|Amikor a fürt kezdetben elindult; az FM megvárja, amíg ez a sok csomópont jelenti magát, mielőtt más szolgáltatásokat helyezne el; beleértve a rendszerszolgáltatásokat, például az elnevezést. Ennek az értéknek a növelésével megnő a fürt indításához szükséges idő; de megakadályozza, hogy a korai csomópontok túlterheltté váljanak, és a további lépések is, amelyek szükségesek lesznek, mivel több csomópont jön létre. Ezt az értéket általában a kezdeti fürtméret egy kis hányadára kell beállítani. |
|Várt NodedeactivationDuration|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(60.0 \* 30)|Dinamikus|Adja meg az időtartományt másodpercben. Ez a csomópont várható időtartama az inaktiválás befejezéséhez. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(60.0 \* 30)|Dinamikus|Adja meg az időtartományt másodpercben. Ez a Windows Fabric-frissítés során frissítendő csomópont várható időtartama. |
|VártReplicaUpgradeDuration|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(60.0 \* 30)|Dinamikus|Adja meg az időtartományt másodpercben. Ez a várt időtartam a csomóponton frissítendő összes replikának az alkalmazás frissítése során. |
|IsSingletonReplicaMoveAllowedKözbenfrissítés közben|bool, az alapértelmezett érték IGAZ|Dinamikus|Ha igaz ra van állítva; az 1-es célreplikával rendelkező replikák a frissítés során áthelyezhetők. |
|MinReplicaSetMéret|int, az alapértelmezett érték 3|Nem engedélyezett|Ez az FM minimális replikakészlet-mérete. Ha az aktív FM-replikák száma ez az érték alá csökken; az FM mindaddig elutasítja a fürt módosításait, amíg legalább a replikák maz száma helyre nem áll |
|Elhelyezési kényszerek|karakterlánc, az alapértelmezett a ""|Nem engedélyezett|A feladatátvevő-kezelő replikáinak elhelyezési megkötései |
|ElhelyezésTimeLimit|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(600)|Dinamikus|Adja meg az időtartományt másodpercben. A célreplikaszám elérésének határideje; amely után figyelmeztető egészségügyi jelentést indítanak |
|QuorumLossWaitDuration |Idő másodpercben, az alapértelmezett érték MaxValue |Dinamikus|Adja meg az időtartományt másodpercben. Ez az a maximális időtartam, amelynek esetében lehetővé tesszük, hogy egy partíció kvórumveszteség állapotban legyen. Ha a partíció ezen időtartam után is kvórumveszteségben van; a partíció helyreáll a kvórum vesztesége, figyelembe véve a lefelé replikák elveszett. Vegye figyelembe, hogy ez adatvesztést okozhat. |
|ÚjrakonfigurálásTimeLimit|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(300)|Dinamikus|Adja meg az időtartományt másodpercben. Az újrakonfigurálás határideje; amely után figyelmeztető egészségügyi jelentést indítanak |
|ReplicaRestartWaitDuration|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(60.0 \* 30)|Nem engedélyezett|Adja meg az időtartományt másodpercben. Ez az FMService ReplicaRestartWaitDuration |
| SeedNodeQuorumAdditionalBufferNodes | int, az alapértelmezett érték 0 | Dinamikus | Puffer vetőmag csomópontok, hogy szükség van fel (együtt kvóruma mag csomópontok) FM lehetővé kell tennie a maximális (totalNumSeedNodes - (seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)) vetőmag csomópontok lemegy. |
|Készenléti replikálási időtartam|Timespan, az alapértelmezett érték Common::TimeSpan::FromSeconds(3600.0 \* 24 \* 7)|Nem engedélyezett|Adja meg az időtartományt másodpercben. Ez az FMService StandByReplicaKeepDuration |
|CélReplicaSetMéret|int, az alapértelmezett érték 7|Nem engedélyezett|Ez a Windows Fabric által karbantartható FM-replikák célszáma. A nagyobb szám az FM-adatok nagyobb megbízhatóságát eredményezi; egy kis teljesítmény kompromisszumot. |
|UsermaxStandByReplicaCount (FelhasználómaxStandByReplicaCount) |Int, az alapértelmezett érték 1 |Dinamikus|A rendszer által a felhasználói szolgáltatásokhoz megőrzött standby replikák alapértelmezett maximális száma. |
|UserReplicaRestartWaitDuration |Idő másodpercben, az alapértelmezett \* érték 60,0 30 |Dinamikus|Adja meg az időtartományt másodpercben. Ha egy megőrzött replika leáll; A Windows Fabric megvárja ezt az időtartamot, amíg a replika biztonsági másolatot készít, mielőtt új cserereplikákat hozna létre (ami az állapot másolatát igényelné). |
|UserStandByReplicaKeepDuration |Idő másodpercben, alapértelmezett: 3600.0 \* 24 \* 7 |Dinamikus|Adja meg az időtartományt másodpercben. Ha egy megőrzött replika lefelé állapotból tér vissza; lehet, hogy már kicserélték. Ez az időzítő határozza meg, hogy az FM mennyi ideig tartja a készenléti replikát, mielőtt elveti azt. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|completedActionKeepdurationinseconds | Int, alapértelmezett: 604800 |Statikus| Ez körülbelül mennyi ideig tarthatja a terminálállapotban lévő műveleteket. Ez a StoredActionCleanupIntervalInSeconds- től is függ; mivel a tisztítási munka csak ezen az intervallumon történik. 604800 az 7 nap. |
|DataLossCheckPollIntervalinszundum|int, az alapértelmezett érték 5|Statikus|Ez az az idő között, az ellenőrzések a rendszer végez, miközben várja az adatvesztés bekövetkezése. Az adatvesztés idotartamánkénti ellenőrzésének száma DataLossCheckWaitDurationInSeconds/this. |
|DatalossCheckWaitdurationinszekundumban|int, az alapértelmezett érték 25|Statikus|A teljes idő; másodpercben; hogy a rendszer megvárja az adatvesztést. Ez a startpartitiondatalossasync() api hívása esetén belsőleg használatos. |
|MinReplicaSetMéret |Int, az alapértelmezett érték 0 |Statikus|A MinReplicaSetSize for FaultAnalysisService. |
|Elhelyezési kényszerek | karakterlánc, az alapértelmezett a ""|Statikus| A FaultAnalysisService elhelyezési kényszerei. |
|QuorumLossWaitDuration | Idő másodpercben, az alapértelmezett érték MaxValue |Statikus|Adja meg az időtartományt másodpercben. A QuorumLossWaitDuration for FaultAnalysisService. |
|ReplicaDropDropWaitdurationinszek|int, az alapértelmezett érték 600|Statikus|Ez a paraméter az adatvesztési API hívásakor használatos. Azt szabályozza, hogy a rendszer mennyi ideig fog várni a replika eldobására, miután a replika eltávolítása belső meghívásra kerül. |
|ReplicaRestartWaitDuration |Idő másodpercben, az alapértelmezett érték 60 perc|Statikus|Adja meg az időtartományt másodpercben. A ReplicaRestartWaitDuration for FaultAnalysisService. |
|Készenléti replikálási időtartam| Idő másodpercben, az alapértelmezett érték (60*24*7) perc |Statikus|Adja meg az időtartományt másodpercben. A FaultAnalysisService StandByReplicaKeepDuration szolgáltatása. |
|StoredActionCleanupIntervalInSeconds | Int, az alapértelmezett érték 3600 |Statikus|Ez az, hogy milyen gyakran a bolt lesz tisztítani. Csak a terminálállapotban végrehajtott műveletek; és hogy a befejezett legalább CompletedActionKeepDurationInSeconds ezelőtt el lesz távolítva. |
|StoredChaosEventCleanupIntervalInSeconds | Int, az alapértelmezett érték 3600 |Statikus|Ez az, hogy milyen gyakran a bolt lesz auditált a razzia; ha az események száma meghaladja a 30000-et; A razzia beindul. |
|CélReplicaSetMéret |Int, az alapértelmezett érték 0 |Statikus|NOT_PLATFORM_UNIX_START a TargetReplicaSetSize for FaultAnalysisService. |

## <a name="federation"></a>Összevonás

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|LeaseDuration (Címbérlet Időtartama) |Idő másodpercben, az alapértelmezett érték 30 |Dinamikus|A bérlet időtartama a csomópont és a szomszédai között. |
|LeaseDurationAcrossFaultTartomány |Idő másodpercben, az alapértelmezett érték 30 |Dinamikus|A bérlet időtartama a csomópont és a szomszédok között a tartalék tartományok között. |

## <a name="filestoreservice"></a>FileStoreService szolgáltatás

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Bool, az alapértelmezett érték IGAZ|Dinamikus|Konfigurációs adatok kal, amelyek meghatározzák, hogy a fájltároló szolgáltatás elfogadja-e az adattömbalapú fájlfeltöltést az alkalmazáscsomag másolása során. |
|AnonymousAccessEnabled | Bool, az alapértelmezett igaz |Statikus|Névtelen hozzáférés engedélyezése/letiltása a FileStoreService megosztásokhoz. |
|CommonName1Ntlmx509Közösnév|karakterlánc, az alapértelmezett a ""|Statikus| A HMAC generálásához használt XMAC köznapi neve a CommonName1NtlmPasswordSecret rendszeren NTLM-hitelesítés esetén |
|CommonName1Ntlmx509StoreLocation|karakterlánc, az alapértelmezett érték "LocalMachine"|Statikus|A HMAC generálásához használt XMAC tárolóhelye a CommonName1NtlmPasswordSecret rendszeren NTLM-hitelesítés használata esetén |
|CommonName1Ntlmx509StoreName|karakterlánc, az alapértelmezett érték "MY"| Statikus|A HMAC generálásához használt XMAC tárolóneve a CommonName1NtlmPasswordSecret rendszeren NTLM-hitelesítés használata esetén |
|CommonName2Ntlmx509Közösnév|karakterlánc, az alapértelmezett a ""|Statikus|A HMAC generálásához használt XMAC köznapi neve a CommonName2NtlmPasswordSecret rendszeren NTLM-hitelesítés esetén |
|CommonName2Ntlmx509StoreLocation|karakterlánc, az alapértelmezett érték "LocalMachine"| Statikus|A HMAC generálásához használt XMAC tárolóhelye a CommonName2NtlmPasswordSecret rendszeren NTLM-hitelesítés használata esetén |
|CommonName2Ntlmx509StoreName|karakterlánc, az alapértelmezett érték "MY"|Statikus| A HMAC generálásához használt XMAC tárolóneve a CommonName2NtlmPasswordSecret rendszeren NTLM-hitelesítés használata esetén |
|CommonNameNtlmPasswordSecret|SecureString, az alapértelmezett érték: Common::SecureString("")| Statikus|A jelszó titok, amely magként használt, hogy ugyanazt a jelszót hozza létre NTLM-hitelesítés használatakor |
|DiskSpaceHealthReportingintervalWhenCloseToOutOfDiskspace |TimeSpan, az alapértelmezett érték common::TimeSpan::FromMinutes(5)|Dinamikus|Adja meg az időtartományt másodpercben. Az állapotesemény jelentéséhez szükséges lemezterület ellenőrzése között eltelt idő, ha a lemez közel van a területhez. |
|DiskSpaceHealthReportingIntervalWhenenoughDiskSpace |TimeSpan, az alapértelmezett érték common::TimeSpan::FromMinutes(15)|Dinamikus|Adja meg az időtartományt másodpercben. Az állapotesemény jelentéséhez szükséges lemezterület ellenőrzése között eltelt idő, ha van elég hely a lemezen. |
|EnableImageStoreHealthReporting |bool, az alapértelmezett érték IGAZ    |Statikus|Konfigurációs adatokkal határozhatja meg, hogy a fájltároló szolgáltatás nak jelentenie kell-e az állapotát. |
|FreeDiskspaceNotificationSizeInKB|int64, az alapértelmezett\*érték 25 1024 |Dinamikus|Az a szabad lemezterület mérete, amely alatt egészségügyi figyelmeztetés fordulhat elő. A konfiguráció minimális értéke és a FreeDiskSpaceNotificationThresholdPercentage konfiguráció az állapotfigyelmeztetés elküldésének meghatározásához használatos. |
|FreeDiskSpaceNotificationThresholdpercentage|dupla, az alapértelmezett érték 0,02 |Dinamikus|A szabad lemezterület azon százaléka, amely alatt állapotfigyelmeztetés fordulhat elő. A konfiguráció minimális értéke és a FreeDiskSpaceNotificationInMB konfiguráció az állapotfigyelmeztetés elküldésének meghatározásához használatos. |
|GenerateV1CommonNameAccount| bool, az alapértelmezett érték IGAZ|Statikus|Itt adható meg, hogy létre kell-e hozni egy v1-es létrehozási algoritmussal rendelkező fiókot. A Service Fabric 6.1-es verziójával kezdve; egy fiókot v2 generáció mindig létre. A V1-fiók olyan verziókról/verziókra való frissítéshez szükséges, amelyek nem támogatják a V2-generációt (6.1 előtt).|
|MaxCopyOperationThreads | Uint, az alapértelmezett érték 0 |Dinamikus| A másodlagos párhuzamos fájlok maximális száma, amelyeket másodlagos az elsődlegesfájlból másolhat. '0' == magok száma. |
|MaxFileOperationThreads | Uint, az alapértelmezett érték 100 |Statikus| A FileOperations (Másolás/áthelyezés) végrehajtásához engedélyezett párhuzamos szálak maximális száma az elsődleges en. '0' == magok száma. |
|MaxRequestProcessingThreads | Uint, az alapértelmezett érték 200 |Statikus|A kérelmek feldolgozására engedélyezett párhuzamos szálak maximális száma az elsődlegesen. '0' == magok száma. |
|MaxSecondaryFileCopyFailureThreshold | Uint, alapértelmezett érték 25|Dinamikus|A másodlagos fájlmásolási újrapróbálkozások maximális száma a feladás előtt. |
|MaxStoreOperations (MaxStoreOperations) | Uint, alapértelmezett érték: 4096 |Statikus|Az elsődlegesen engedélyezett párhuzamos tárolótranzakció-műveletek maximális száma. '0' == magok száma. |
|NamingOperationTimeout |Idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Adja meg az időtartományt másodpercben. Az elnevezési művelet végrehajtásának időtúlidőpontja. |
|ElsődlegesaccountNTLMPasswordSecret | SecureString, az alapértelmezett érték üres |Statikus| A jelszótitok, amely az NTLM-hitelesítés használatakor ugyanazt a jelszót hozta létre. |
|PrimaryAccountNTLMX509StoreLocation | karakterlánc, az alapértelmezett érték "LocalMachine"|Statikus| A HMAC létrehozásához használt X509 tanúsítvány tárolóhelye a PrimaryAccountNTLMPasswordSecret rendszeren NTLM-hitelesítés használata esetén. |
|PrimaryAccountNTLMX509Üzletnév | karakterlánc, az alapértelmezett érték "MY"|Statikus| A HMAC létrehozásához használt X509 tanúsítvány tárolóneve a PrimaryAccountNTLMPasswordSecret rendszeren NTLM-hitelesítés használata esetén. |
|PrimaryAccountNTLMX509Ujjnyomat | karakterlánc, az alapértelmezett a ""|Statikus|Az X509 tanúsítvány ujjlenyomata, amely a HMAC generálására szolgál a PrimaryAccountNTLMPasswordSecret rendszeren NTLM-hitelesítés használata esetén. |
|ElsődlegesAccountType | karakterlánc, az alapértelmezett a "" |Statikus|A FileStoreService-megosztások a kulcstag elsődleges AccountType-ja. |
|PrimaryAccountUserName | karakterlánc, az alapértelmezett a "" |Statikus|A FileStoreService-megosztások a kulcstag elsődleges felhasználóneve. |
|PrimaryAccountUserPassword | SecureString, az alapértelmezett érték üres |Statikus|A FileStoreService-megosztások a főtag elsődleges fiókjelszava. |
|QueryOperationTimeout | Idő másodpercben, az alapértelmezett érték 60 |Dinamikus|Adja meg az időtartományt másodpercben. A lekérdezési művelet végrehajtásának időtúlidőpontja. |
|MásodlagosaccountNTLMPasswordSecret | SecureString, az alapértelmezett érték üres |Statikus| A jelszótitok, amely az NTLM-hitelesítés használatakor ugyanazt a jelszót hozta létre. |
|Másodlagos accountntLMX509StoreLocation | karakterlánc, az alapértelmezett érték "LocalMachine" |Statikus|A HMAC létrehozásához használt X509 tanúsítvány tárolóhelye a SecondaryAccountNTLMPasswordSecret rendszeren NTLM-hitelesítés használata esetén. |
|SecondaryaccountNTLMX509Üzletnév | karakterlánc, az alapértelmezett érték "MY" |Statikus|A HMAC létrehozásához használt X509 tanúsítvány tárolóneve a SecondaryAccountNTLMPasswordSecret rendszeren NTLM-hitelesítés használata esetén. |
|SecondaryAccountNTLMX509Ujjnyomat | karakterlánc, az alapértelmezett a ""| Statikus|Az X509 tanúsítvány ujjlenyomata, amely a HMAC generálására szolgál a SecondaryAccountNTLMPasswordSecret szolgáltatásban NTLM-hitelesítés használata esetén. |
|Másodlagos AccountType | karakterlánc, az alapértelmezett a ""|Statikus| A FileStoreService-megosztások a főtag másodlagos AccountType-ja. |
|SecondaryAccountUserName | karakterlánc, az alapértelmezett a ""| Statikus|A FileStoreService-megosztások a főtag másodlagos felhasználóneve. |
|MásodlagosAccountUserPassword | SecureString, az alapértelmezett érték üres |Statikus|A FileStoreService-megosztások a főtag másodlagos fiókjelszava. |
|SecondaryFileCopyRetryDelayMillimásodperc|uint, az alapértelmezett érték 500|Dinamikus|A fájl másolásának újrapróbálkozási késleltetése (ezredmásodpercben).|
|UseChunkContentInTransportMessage|bool, az alapértelmezett érték IGAZ|Dinamikus|A 6.4-es verzióban bevezetett feltöltési protokoll új verziójának használatára szolgáló jelző. Ez a protokollverzió a service fabric átvitele segítségével fájlokat tölt fel a képtárba, amely jobb teljesítményt nyújt, mint a korábbi verziókban használt SMB protokoll. |

## <a name="healthmanager"></a>HealthManager (Egészségmenedzser)

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Bool, az alapértelmezett érték hamis |Statikus|Fürtállapot-értékelési házirend: engedélyezze alkalmazástípusonkénti állapotkiértékelés. |
|MaxSuggestedNumberOfEntityHealthReports|Int, az alapértelmezett érték 100 |Dinamikus|Az entitás által a figyelő állapotjelentési logikájával kapcsolatos aggályok felvetése előtt kaphatnak állapotjelentések maximális számát. Minden egészségügyi entitás állítólag egy viszonylag kis számú egészségügyi jelentések. Ha a jelentésszám meghaladja ezt a számot; problémák merülhetnek fel a házőrző végrehajtásával kapcsolatban. A túl sok jelentéssel rendelkező entitást egy Figyelmeztetés állapotjelentésben jelöli meg az entitás kiértékelésekor. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ConsiderWarningAsError (Figyelemfigyelmeztesse- |Bool, az alapértelmezett érték hamis |Statikus|Fürtállapot-értékelési házirend: a figyelmeztetéseket a rendszer hibaként kezeli. |
|MaxPercentUnhealthyApplications alkalmazások | Int, az alapértelmezett érték 0 |Statikus|Fürtállapot-kiértékelési házirend: a sérült alkalmazások maximális százaléka lehetővé tette, hogy a fürt kifogástalan állapotú legyen. |
|MaxPercentUnhealthyNodes | Int, az alapértelmezett érték 0 |Statikus|Fürtállapot-értékelési házirend: a nem megfelelő állapotú csomópontok maximális százaléka lehetővé tette a fürt kifogástalan állapotú. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, az alapértelmezett érték 10|Statikus|Fürtfrissítési állapotértékelési házirend: a különbözeti állapotjelző csomópontok maximális százaléka lehetővé tette a fürt kifogástalan állapotának megőrzését |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, az alapértelmezett érték 15|Statikus|Fürtfrissítési állapotkiértékelési házirend: a nem megfelelő állapotú csomópontok különbözetének maximális százaléka egy frissítési tartományban lehetővé tette a fürt kifogástalan állapotának megőrzését |

## <a name="hosting"></a>Üzemeltetés

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Egész szám, az alapértelmezett érték 10 |Dinamikus|Azon alkalmak száma, amikor a rendszer újrapróbálkozása sikertelen volt az aktiválás során, mielőtt feladta volna |
|AktiválásMaxRetryInterval |Idő másodpercben, az alapértelmezett érték 300 |Dinamikus|Minden folyamatos aktiválási hiba esetén a rendszer újrapróbálkozik az aktiválással az AktiválásMaxFailureCount-ig. Az ActivationMaxRetryInterval minden aktiválási hiba után a Várakozási idő intervallumát adja meg az újrapróbálkozás előtt |
|ActivationRetryBackoffInterval |Idő másodpercben, az alapértelmezett érték 5 |Dinamikus|Visszalépési időköz minden aktiválási hibán; Minden folyamatos aktiválási hiba esetén a rendszer újrapróbálkozik az aktiválással a MaxActivationFailureCount értékig. Az újrapróbálkozási időköz minden próbálkozáskor a folyamatos aktiválási hiba és az aktiválási visszalépési időköz eredménye. |
|Aktiválási időout| TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(180)|Dinamikus| Adja meg az időtartományt másodpercben. Az alkalmazás aktiválásának időmeghosszabbítása; kikapcsolása és frissítése. |
|ApplicationHostCloseTimeout| TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus| Adja meg az időtartományt másodpercben. Amikor a Fabric kilépését egy önaktivált folyamatok észlelik; FabricRuntime bezárja az összes replikák a felhasználó gazdagép (applicationhost) folyamat. Ez a szoros művelet időhatora. |
| CnsNetworkPluginCnmUrlPort | wstring, az alapértelmezett érték L"48080" | Statikus | Azure cnm api URL-port |
| CnsNetworkPluginCnsUrlPort | wstring, az alapértelmezett érték L"10090" | Statikus | Azure cns URL-port |
|ContainerServiceArguments|karakterlánc, az alapértelmezett érték :"-H localhost:2375 -H npipe://"|Statikus|A Service Fabric (SF) kezeli a docker démont (kivéve a Windows ügyfélgépeken, például a Win10-en). Ez a konfiguráció lehetővé teszi a felhasználó számára, hogy adja meg az egyéni argumentumokat, amelyeket át kell adni a docker démon indításakor. Ha egyéni argumentumok vannak megadva, a Service Fabric nem adja át a Docker-motornak semmilyen más argumentumot, kivéve a "--pidfile" argumentumot. Ezért a felhasználók nak nem szabad megadniuk a "--pidfile" argumentumot az ügyfélargumentumaik részeként. Emellett az egyéni argumentumok biztosítania kell, hogy a docker démon figyeli az alapértelmezett névcsövet a Windows (vagy unix domain socket Linux) a Service Fabric, hogy képes legyen kommunikálni vele.|
|ContainerServiceLogFileMaxSizeInKb|int, az alapértelmezett érték 32768|Statikus|A docker-tárolók által létrehozott naplófájl maximális fájlmérete.  Csak Windows.|
|ContainerImageDownloadTimeout|int, másodpercek száma, alapértelmezett 1200 (20 mins)|Dinamikus|A kép letöltése előtti másodpercek száma időtúljár.|
|ContainerImagesToSkip (Konténerképek – Ugrás|karakterlánc, képnevek függőleges vonalkarakterrel elválasztva, az alapértelmezett ""|Statikus|Egy vagy több olyan tárolórendszerkép neve, amelyet nem szabad törölni.  A PruneContainerImages paraméterrel használható.|
|ContainerServiceNaplóFájlnév-előtag|karakterlánc, az alapértelmezett érték "sfcontainerlogs"|Statikus|A docker-tárolók által létrehozott naplófájlok fájlnév-előtagja.  Csak Windows.|
|ContainerServiceServiceFileRetentionCount|int, az alapértelmezett érték 10|Statikus|A docker-tárolók által a naplófájlok felülírása előtt létrehozott naplófájlok száma.  Csak Windows.|
|FabricRuntimetimeout létrehozása|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus| Adja meg az időtartományt másodpercben. A sync FabricCreateRuntime hívás időtúlértéke |
|DefaultContainerRepositoryAccountName|karakterlánc, az alapértelmezett a ""|Statikus|Az ApplicationManifest.xml fájlban megadott hitelesítő adatok helyett használt alapértelmezett hitelesítő adatok |
|DefaultContainerRepositoryPassword|karakterlánc, az alapértelmezett a ""|Statikus|Az ApplicationManifest.xml fájlban megadott hitelesítő adatok helyett használt alapértelmezett jelszóhitelesítő adatok|
|DefaultContainerRepositoryPasswordType típus|karakterlánc, az alapértelmezett a ""|Statikus|Ha nem üres karakterlánc, az érték lehet "Titkosított" vagy "SecretsStoreRef".|
|DefaultDnsSearchSuffixEmpty|az alapértelmezett érték HAMIS|Statikus|Alapértelmezés szerint a szolgáltatás neve hozzávan fűzve a tárolószolgáltatások SF DNS-nevéhez. Ez a szolgáltatás leállítja ezt a viselkedést, így a megoldási útvonalban alapértelmezés szerint semmi sem fűzi hozzá az SF DNS-nevét.|
|TelepítésMaxFailureCount|int, az alapértelmezett érték 20| Dinamikus|Az alkalmazás központi telepítése a TelepítésMaxFailureCount alkalommal újra meglesz kísérelve, mielőtt az alkalmazás központi telepítése a csomóponton megbukna.| 
|TelepítésMaxRetryInterval| TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(3600)|Dinamikus| Adja meg az időtartományt másodpercben. A telepítés maximális újrapróbálkozási időköze. Minden folyamatos hiba esetén az újrapróbálkozási időköz min( DeploymentMaxRetryInterval; Folyamatos hibák száma * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(10)|Dinamikus|Adja meg az időtartományt másodpercben. A központi telepítési hiba visszalépési időköze. Minden folyamatos üzembe helyezési hiba esetén a rendszer újra megpróbálja a központi telepítést a MaxDeploymentFailureCount-ig. Az újrapróbálkozási időköz a folyamatos üzembe helyezési hiba és a központi telepítési visszalépési időköz eredménye. |
|DisableContainers (Letiltási tárolók|az alapértelmezett érték HAMIS|Statikus|Konfiguráció a tárolók letiltásához - a DisableContainerServiceStartOnContainerActivatorOpen helyett használatos, amely elavult konfigurációs |
|DisableDockerRequestRetry|az alapértelmezett érték HAMIS |Dinamikus| Alapértelmezés szerint az SF kommunikál a DD-vel (docker dameon) a "DockerRequestTimeout" időtúloldalán minden hozzá küldött http-kérelemhez. Ha a DD nem válaszol ezen az időszakon belül; Az SF újraküldi a kérelmet, ha a legfelső szintű műveletnek még hátra van.  A hyperv konténer; A DD néha sokkal több időt vesz igénybe a tároló felhozásához vagy inaktiválásához. Ilyen esetekben a DD kérelem időbeli ki az SF szempontjából, és SF újrapróbálkozik a műveletet. Néha ez úgy tűnik, hogy növeli a nyomást a DD. Ez a konfiguráció lehetővé teszi, hogy tiltsa le ezt az újrapróbálkozást, és várja meg, amíg a DD válaszol. |
|DnsServerListTwoIps | Bool, az alapértelmezett érték HAMIS | Statikus | Ez a jelzők kétszer adják hozzá a helyi dns-kiszolgálót, hogy enyhítsék a problémák időszakos megoldását. |
| DoNotInjectLocalDnsserver | az alapértelmezett érték HAMIS | Statikus | Megakadályozza, hogy a futásidő a helyi IP-címet dns-kiszolgálóként adja be a tárolókhoz. |
|EnableActivateNoWindow| az alapértelmezett érték HAMIS|Dinamikus| Az aktivált folyamat jön létre a háttérben konzol nélkül. |
|EnableContainerServiceDebugMode|bool, az alapértelmezett érték IGAZ|Statikus|A docker-tárolók naplózásának engedélyezése/letiltása.  Csak Windows.|
|EnableDockerHealthCheckIntegráció|bool, az alapértelmezett érték IGAZ|Statikus|Lehetővé teszi a docker HEALTHCHECK események integrálását a Service Fabric rendszerállapot-jelentésével |
|EnableProcessDebugging|az alapértelmezett érték HAMIS|Dinamikus| Lehetővé teszi az alkalmazásállomások indítását a hibakereső alatt |
|Végpontszolgáltató engedélyezve| az alapértelmezett érték HAMIS|Statikus| Lehetővé teszi a végponti erőforrások kezelése a Fabric által. A FabricNode kezdő és záró alkalmazásporttartományának specifikációja szükséges. |
|FabricContainerAppsEnabled| az alapértelmezett érték HAMIS|Statikus| |
|FirewallPolicyEnabled|az alapértelmezett érték HAMIS|Statikus| Tűzfalportok megnyitásának engedélyezése a ServiceManifest-ben megadott explicit portokkal rendelkező végponti erőforrások számára |
|GetCodePackageActivationContextTimeout|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus|Adja meg az időtartományt másodpercben. A CodePackageActivationContext hívások időtúlértéke. Ez nem vonatkozik az ad hoc szolgáltatásokra. |
|GovernOnlyMainMemoryForFolyamatok|az alapértelmezett érték HAMIS|Statikus|Az erőforrás-szabályozás alapértelmezett viselkedése a MemoryInMB-ben megadott korlát ot a folyamat által használt teljes memória (RAM + swap) mennyiségére korlátozza. Ha túllépik a határértéket; a folyamat outOfmemory kivételt kap. Ha ez a paraméter igaz értékre van állítva; a korlát csak arra a RAM-memóriára lesz alkalmazva, amelyet egy folyamat használni fog. Ha ezt a korlátot túllépik; és ha ez a beállítás igaz; akkor os akarat cserél a fő memória -hoz korong. |
|IPProviderEnabled|az alapértelmezett érték HAMIS|Statikus|Lehetővé teszi az IP-címek kezelését. |
|IsDefaultContainerRepositoryPasswordEncrypted|az alapértelmezett érték HAMIS|Statikus|Azt jelzi, hogy a DefaultContainerRepositoryPassword titkosítva van-e vagy sem.|
|LinuxExternalExecutablePath|karakterlánc, az alapértelmezett érték : "/usr/bin/" |Statikus|A csomópont külső végrehajtható parancsainak elsődleges könyvtára.|
|NTLMAuthenticationenabled|az alapértelmezett érték HAMIS|Statikus| Lehetővé teszi az NTLM használatát a más felhasználókként futó kódcsomagok által, hogy a gépek közötti folyamatok biztonságosan kommunikálhassanak. |
|NTLMAuthenticationpasswordSecret|SecureString, az alapértelmezett érték: Common::SecureString("")|Statikus|Van egy titkosított van, hogy létrehozásához használt jelszót NTLM felhasználók számára. Be kell állítani, ha az NTLMAuthenticationEnabled értéke igaz. A deployer érvényesítette. |
|NTLMSecurityusersbyX509CommonNamesRefreshInterval|TimeSpan, az alapértelmezett érték common::TimeSpan::FromMinutes(3)|Dinamikus|Adja meg az időtartományt másodpercben. Környezetspecifikus beállítások Az a rendszeres időköz, amelyen a Tárhely a FileStoreService NTLM konfigurációhoz használandó új tanúsítványokat vizsgálja. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, az alapértelmezett érték common::TimeSpan::FromMinutes(4)|Dinamikus| Adja meg az időtartományt másodpercben. Az NTLM-felhasználók tanúsítványokkal közös nevek használatával történő konfigurálásának időmeghosszabbítása. Az NTLM-felhasználókszükségesek a FileStoreService-megosztásokhoz. |
|MetszenivalóContainerImages|az alapértelmezett érték HAMIS|Dinamikus| Távolítsa el a nem használt alkalmazástároló-lemezképeket a csomópontokból. Ha egy ApplicationType nincs regisztrálva a Service Fabric-fürtből, az alkalmazás által használt tárolólemezképek törlődnek azokon a csomópontokon, ahol a Service Fabric letöltötte. A metszés óránként fut, így akár egy órát is igénybe vehet (plusz a lemezkép metszéséhez eltelt idő) a lemezképek fürtből való eltávolításához.<br>A Service Fabric soha nem fogja letölteni vagy eltávolítani az alkalmazáshoz nem kapcsolódó képeket.  A manuálisan vagy más módon letöltött, nem kapcsolódó képeket explicit módon el kell távolítani.<br>A nem törölni kívánt lemezképek megadhatók a ContainerImagesToSkip paraméterben.| 
|RegisterCodePackageHostTimeout|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus| Adja meg az időtartományt másodpercben. A FabricRegisterCodePackageHost szinkronizálási hívásának időtúlértéke. Ez csak a többkódos csomagalkalmazások gazdagépeire vonatkozik, például az FWP-re |
|Kérésidő-out|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(30)|Dinamikus| Adja meg az időtartományt másodpercben. Ez a felhasználó alkalmazásgazda és a Fabric folyamata közötti kommunikáció időtúljelentkezését jelenti különböző üzemeltetési műveletekhez, például a gyári regisztrációhoz; futásidejű regisztráció. |
|Runaspolicyenabled| az alapértelmezett érték HAMIS|Statikus| Engedélyezi a futó kódcsomagokat helyi felhasználóként, nem pedig azt a felhasználót, amely alatt a hálófolyamat fut. Annak érdekében, hogy ez a házirend Fabric kell futtatni a RENDSZER vagy a felhasználó, aki SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus|Adja meg az időtartományt másodpercben. A szinkronizálási register(Állapotnélküli/Állapotalapú)ServiceFactory hívás időtúlértéke |
|ServiceTypeDisableFailureThreshold |Egész szám, az alapértelmezett érték 1 |Dinamikus|Ez a hibaszám küszöbértéke, amely után a FailoverManager (FM) értesítést kap arról, hogy tiltsa le a szolgáltatás típusát az adott csomóponton, és próbáljon meg egy másik csomópontot az elhelyezéshez. |
|ServiceTypeDisableGraceInterval|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(30)|Dinamikus|Adja meg az időtartományt másodpercben. Időintervallum, amely után a szolgáltatástípus letiltható |
|ServiceTypeRegistrationTimeout |Idő másodpercben, az alapértelmezett érték 300 |Dinamikus|A ServiceType hálóval való regisztrálásának maximális ideje |
|UseContainerServiceArguments|bool, az alapértelmezett érték IGAZ|Statikus|Ez a konfiguráció azt mondja az üzemeltetési nek, hogy hagyja ki a (config ContainerServiceArguments) átadási argumentumokat a docker démonnak.|

## <a name="httpgateway"></a>HttpÁtjáró

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, az alapértelmezett érték 50 |Statikus| A http-kiszolgálóvárólistába felkönyvelni utolesztő olvasmányok száma. Ez határozza meg a HttpGateway által teljesíthető egyidejű kérelmek számát. |
|HttpGatewayHealthReportSendInterval |Idő másodpercben, az alapértelmezett érték 30 |Statikus|Adja meg az időtartományt másodpercben. Az az időköz, amely en a Http Gateway elküldi a felhalmozott állapotjelentéseket az állapotkezelőnek. |
|httpStrictTransportSecurityfejléc|karakterlánc,az alapértelmezett ""|Dinamikus| Adja meg a Http Strict Transport Security fejlécértékét, amelyet a HttpGateway által küldött minden válaszban meg kell adni. Ha üres karakterláncra van beállítva; ez a fejléc nem fog szerepelni az átjáróválaszban.|
|IsEnabled|Bool, az alapértelmezett érték hamis |Statikus| Engedélyezi/letiltja a HttpGateway-t. A HttpGateway alapértelmezés szerint le van tiltva. |
|MaxEntityBodyMéret |Uint, alapértelmezett érték: 4194304 |Dinamikus|Megadja a törzs maximális méretét, amely egy http-kérelemtől elvárható. Az alapértelmezett érték 4 MB. A Httpgateway sikertelen lesz a kérés, ha ennek az értéknek > méretű. A minimális olvasási adattömb mérete 4096 bájt. Tehát ezt meg kell >= 4096. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Engedélyezve |Bool, az alapértelmezett érték hamis |Statikus|Az ImageStoreService engedélyezett jelzője. Alapértelmezett: false |
|MinReplicaSetMéret | Int, az alapértelmezett érték 3 |Statikus|A MinReplicaSetSize for ImageStoreService. |
|Elhelyezési kényszerek | karakterlánc, az alapértelmezett a "" |Statikus| Az Elhelyezéskényszerek az ImageStoreService számára. |
|QuorumLossWaitDuration | Idő másodpercben, az alapértelmezett érték MaxValue |Statikus| Adja meg az időtartományt másodpercben. A QuorumLossWaitDuration for ImageStoreService. |
|ReplicaRestartWaitDuration | Idő másodpercben, az alapértelmezett \* érték 60,0 30 |Statikus|Adja meg az időtartományt másodpercben. A ReplicaRestartWaitDuration for ImageStoreService. |
|Készenléti replikálási időtartam | Idő másodpercben, az alapértelmezett érték \* 3600,0 2 |Statikus| Adja meg az időtartományt másodpercben. A StandByReplicaKeepDuration for ImageStoreService. |
|CélReplicaSetMéret | Int, az alapértelmezett érték 7 |Statikus|Az ImageStoreService TargetReplicaSetSize szolgáltatása. |

## <a name="ktllogger"></a>KtlLogger között

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration (AutomaticMemoryConfiguration) |Int, az alapértelmezett érték 1 |Dinamikus|Jelző, amely azt jelzi, hogy a memóriabeállításokat automatikusan és dinamikusan kell-e konfigurálni. Ha a nulla, akkor a memória konfigurációs beállításait közvetlenül használják, és nem változnak a rendszer feltételek alapján. Ha az egyik, akkor a memória beállításai automatikusan konfigurálva vannak, és a rendszer feltételek szerint változhatnak. |
|MaximumDestagingWriteOutstandingInKB | Int, az alapértelmezett érték 0 |Dinamikus|A megosztott napló nak a dedikált napló előtt előrelépni engedélyező KB-száma. A 0 használatával jelezze a nincs korlátot.
|SharedLogId |karakterlánc, az alapértelmezett a "" |Statikus|A megosztott naplótároló egyedi guid azonosítója. Használja a "" értéket, ha a hálóadat-gyökér alapértelmezett elérési útját használja. |
|SharedLogPath |karakterlánc, az alapértelmezett a "" |Statikus|A megosztott naplótároló elérési útja és fájlneve a helyhez. Használja a "" alapértelmezett elérési út a háló adatgyökér alatt. |
|SharedLogSizeInMB |Int, alapértelmezett: 8192 |Statikus|A megosztott naplótárolóban lefoglalandó MB száma. |
|SharedLogThrottleLimitInUsed|int, az alapértelmezett érték 0 | Statikus | A megosztott napló használatának százalékos aránya, amely szabályozást okoz. Az értéknek 0 és 100 között kell lennie. A 0 érték az alapértelmezett százalékértéket jelenti. A 100-as érték azt jelenti, hogy egyáltalán nincs szabályozás. Az 1 és 99 közötti érték a naplóhasználat azon százalékát adja meg, amely felett a szabályozás megtörténik; például ha a megosztott napló 10 GB-os, és az érték 90, akkor a szabályozás akkor következik be, amikor 9 GB-ot használ. Az alapértelmezett érték használata ajánlott.|
|WriteBufferMemoryPoolMaximuminKB | Int, az alapértelmezett érték 0 |Dinamikus|Az írási puffermemória-készlet felnőéséhez lehetővé tenni. A 0 használatával jelezze a nincs korlátot. |
|WriteBufferMemoryPoolMinimumInKB |Int, alapértelmezett: 8388608 |Dinamikus|Az írási puffermemória-készlethez eredetileg lefoglalandó KB-szám. A 0 használatával jelezheti, hogy az alapértelmezett értéknek konzisztensnek kell lennie az alábbi SharedLogSizeInMB-mal. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService
| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|IsEnabled|az alapértelmezett érték HAMIS|Statikus|A felügyelt identitásjogkivonat-szolgáltatás jelenlétének és állapotának a fürtben való jelenlétének és állapotának szabályozása jelző,ez a Service Fabric-alkalmazások felügyelt identitásfunkcióinak használatának előfeltétele.|

## <a name="management"></a>Kezelés

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Automatikus anunprovisioninterval|TimeSpan, az alapértelmezett érték common::TimeSpan::FromMinutes(5)|Dinamikus|Adja meg az időtartományt másodpercben. Az automatikus alkalmazástípus-karbantartás során engedélyezett alkalmazástípus-törlési időköz.|
|AzureStorageMaxConnections | Int, alapértelmezett érték: 5000 |Dinamikus|Az azure storage-hoz való egyidejű kapcsolatok maximális száma. |
|AzureStorageMaxWorkerThreads | Int, az alapértelmezett érték 25 |Dinamikus|A munkaszálak maximális száma párhuzamosan. |
|AzureStorageOperationTimeout | Idő másodpercben, az alapértelmezett érték 6000 |Dinamikus|Adja meg az időtartományt másodpercben. Az xstore művelet befejezésének időkitöltése. |
|CleanupApplicationPackageOnProvisionSikeres|az alapértelmezett érték HAMIS |Dinamikus|Engedélyezi vagy letiltja az alkalmazáscsomag automatikus karbantartását a sikeres üzembe álláskor. |
|KarbantartásNem használtalkalmazás-típusok|Bool, az alapértelmezett érték HAMIS |Dinamikus|Ez a konfiguráció, ha engedélyezve van, lehetővé teszi a nem használt alkalmazástípus-verziók automatikus regisztrációjának lebontását, kihagyva a legújabb három nem használt verziót, ezáltal csökkentve a lemezterület által elfoglalt lemezterületet. Az automatikus karbantartás az adott alkalmazástípus sikeres biztosításának végén aktiválódik, és az összes alkalmazástípushoz naponta egyszer rendszeresen fut. A kihagyandó nem használt verziók száma a "MaxUnusedAppTypeVersionsToKeep" paraméterrel konfigurálható. |
|DisableChecksumValidation (Ellenőrzőösszeg érvényesítése) | Bool, az alapértelmezett érték hamis |Statikus| Ez a konfiguráció lehetővé teszi számunkra, hogy engedélyezze vagy tiltsa le az ellenőrzőösszeg-érvényesítést az alkalmazás kiépítése során. |
|DisableServerSideCopy | Bool, az alapértelmezett érték hamis |Statikus|Ez a konfiguráció engedélyezi vagy letiltja az alkalmazáscsomag kiszolgálóoldali példányát az ImageStore-on az alkalmazás kiépítés során. |
|KépcachingEnabled | Bool, az alapértelmezett igaz |Statikus|Ez a konfiguráció lehetővé teszi számunkra, hogy engedélyezze vagy tiltsa le a gyorsítótárazást. |
|ImageStoreConnectionString |SecureString (Biztonságos karakterlánc) |Statikus|Kapcsolati karakterlánc az ImageStore gyökérgyökerezéséhez. |
|ImageStoreMinimumTransfer BPS | Int, alapértelmezett érték: 1024 |Dinamikus|A fürt és az ImageStore közötti minimális átviteli sebesség. Ez az érték határozza meg az időtúlértéket a külső ImageStore elérésekor. Módosítsa ezt az értéket csak akkor, ha a fürt és az ImageStore közötti késés magas, így több idő áll a fürt számára a külső ImageStore-ból való letöltésre. |
|MaxUnusedAppTypeVersionsToKeep | Int, az alapértelmezett érték 3 |Dinamikus|Ez a konfiguráció határozza meg a karbantartáshoz kihagyandó nem használt alkalmazástípus-verziók számát. Ez a paraméter csak akkor alkalmazható, ha a CleanupUnusedApplicationTypes paraméter engedélyezve van. |


## <a name="metricactivitythresholds"></a>MetricActivityThresholds (MetricActivityThresholds)
| **Paraméter** | **Engedélyezett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|KeyIntegerValueMap, az alapértelmezett érték: Nincs|Dinamikus|Meghatározza a metricActivityThresholds készletét a fürt metrikáihoz. Kiegyensúlyozó fog működni, ha maxNodeLoad nagyobb, mint MetricActivityThresholds. A sikkaszt metrikák határozza meg a terhelés összegét egyenlő vagy alatt, amely Service Fabric figyelembe veszi a csomópont üres |

## <a name="metricbalancingthresholds"></a>Metrikus kiegyensúlyozási küszöbértékek
| **Paraméter** | **Engedélyezett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|KeyDoubleValueMap, az alapértelmezett érték: Nincs|Dinamikus|A metricbalancingthresholds készletét határozza meg a fürt metrikáihoz. Kiegyensúlyozás akkor fog működni, ha maxNodeLoad/minNodeLoad nagyobb, mint MetricBalancingThresholds. Töredezettségmentesítés fog működni, ha maxNodeLoad/minNodeLoad legalább egy FD vagy UD kisebb, mint MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Paraméter** | **Engedélyezett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|KeyDoubleValueMap, az alapértelmezett érték: Nincs|Dinamikus|Meghatározza a terhelés azon részét, amely a replikával együtt marad, amikor kicserélik, 0 (a terhelés nem tapad a replikához) és 1 (a replika betöltése - alapértelmezett) |

## <a name="namingservice"></a>NamingService (Elnevezésszolgáltatás)

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, az alapértelmezett érték 0 |Statikus|Az LRU szolgáltatásleírásának gyorsítótárában az elnevezési átjárón tárolt bejegyzések maximális száma (a nincs korlátozás esetén 0 értékre van állítva). |
|MaxClientConnections |Int, az alapértelmezett érték 1000 |Dinamikus|Az ügyfélkapcsolatok maximálisan engedélyezett száma átjárónként. |
|MaxFileOperationTimeout |Idő másodpercben, az alapértelmezett érték 30 |Dinamikus|Adja meg az időtartományt másodpercben. A fájltároló szolgáltatás működéséhez engedélyezett maximális időtöltés. A nagyobb időkérést megmeghatározó kérelmeket a rendszer elutasítja. |
|MaxIndexedEmptyPartitions |Int, az alapértelmezett érték 1000 |Dinamikus|Az újracsatlakozó ügyfelek szinkronizálásához az értesítési gyorsítótárban továbbra is indexelt üres partíciók maximális száma. Az ezen a szám on-én található üres partíciók egyre növekvő keresnek versionsorrendben törlődnek az indexből. Az ügyfelek újracsatlakoztatása továbbra is szinkronizálhatja és fogadhatja a kihagyott üres partíciófrissítéseket; de a szinkronizálási protokoll drágábbá válik. |
|MaxMessageMéret |Int, alapértelmezett\*érték 4\*1024 1024 |Statikus|Az ügyfélcsomópont-kommunikáció maximális üzenetmérete az elnevezés használata során. DOS támadás enyhítése; az alapértelmezett érték 4 MB. |
|MaxNamingServiceHealthReports | Int, az alapértelmezett érték 10 |Dinamikus|A tárolószolgáltatás elnevezési szolgáltatásának egy időben nem megfelelő állapotúnak jelentése. Ha 0; minden lassú művelet elküldésre kerül. |
|MaxOperationTimeout |Idő másodpercben, az alapértelmezett érték 600 |Dinamikus|Adja meg az időtartományt másodpercben. Az ügyfélműveletek maximális időhatora. A nagyobb időkérést megmeghatározó kérelmeket a rendszer elutasítja. |
|MaxOutstandingNotificationsPerClient |Int, az alapértelmezett érték 1000 |Dinamikus|Az ügyfélregisztráció tmegelőző regisztrációja előtt az átjáró által kényszerítetten lezárt függőben lévő értesítések maximális száma. |
|MinReplicaSetMéret | Int, az alapértelmezett érték 3 |Nem engedélyezett| A frissítés végrehajtásához szükséges elnevezési szolgáltatás replikáinak minimális száma. Ha ennél kevesebb kópia van aktív a rendszerben, a megbízhatósági rendszer a kópiák visszaállításáig megtagadja az elnevezési szolgáltatástároló frissítéseit. Ez az érték soha nem lehet több, mint a TargetReplicaSetSize. |
|PartitionCount |Int, az alapértelmezett érték 3 |Nem engedélyezett|Az elnevezési szolgáltatás tárolóján létrehozandó partíciók száma. Minden partíció rendelkezik egy partíciós kulcs, amely megfelel az index; így partíciós kulcsok [0; PartitionCount] létezik. Az elnevezési szolgáltatás partícióiszámának növelése növeli azt a skálát, amelyen az elnevezési szolgáltatás elvégezheti a biztonsági másolat replikákészletében lévő adatok átlagos mennyiségét; az erőforrások fokozott kihasználtságának költségén (mivel a PartitionCount*ReplicaSetSize szolgáltatás replikáit karban kell tartani).|
|Elhelyezési kényszerek | karakterlánc, az alapértelmezett a "" |Nem engedélyezett| Elhelyezési megkötés az elnevezési szolgáltatáshoz. |
|QuorumLossWaitDuration | Idő másodpercben, az alapértelmezett érték MaxValue |Nem engedélyezett| Adja meg az időtartományt másodpercben. Amikor egy elnevezési szolgáltatás kvórumvesztésbe kerül; ez az időzítő elindul. Amikor lejár az FM fogja tekinteni a lefelé replikák elveszett; és megpróbálja visszaszerezni a kvórumot. Nem mintha ez adatvesztést eredményezne. |
|RepairInterval | Idő másodpercben, az alapértelmezett érték 5 |Statikus| Adja meg az időtartományt másodpercben. Időköz, amelyben a névadási inkonzisztencia javítása a hatóság tulajdonosa és a név tulajdonosa indul el. |
|ReplicaRestartWaitDuration | Idő másodpercben, az alapértelmezett érték (60,0 * 30)|Nem engedélyezett| Adja meg az időtartományt másodpercben. Ha egy elnevezési szolgáltatás replika leáll; ez az időzítő elindul. Amikor lejár az FM kezd cserélni a replikák, amelyek nem (ez még nem tekinti őket elveszett). |
|ServiceDescriptionCacheLimit | Int, az alapértelmezett érték 0 |Statikus| Az LRU szolgáltatás leírásának gyorsítótárában az Namingelt tároló szolgáltatásban tárolt bejegyzések maximális száma (nincs korlátozás esetén 0 értékre állítva). |
|ServiceNotificationTimeout |Idő másodpercben, az alapértelmezett érték 30 |Dinamikus|Adja meg az időtartományt másodpercben. A szolgáltatásértesítések ügyfélnek történő kézbesítéseksorán használt időbeli meghosszabbítás. |
|Készenléti replikálási időtartam | Idő másodpercben, az alapértelmezett érték 3600,0 * 2 |Nem engedélyezett| Adja meg az időtartományt másodpercben. Amikor egy elnevezési szolgáltatás replika visszaad egy lefelé állapotban; lehet, hogy már kicserélték. Ez az időzítő határozza meg, hogy az FM mennyi ideig tartja a készenléti replikát, mielőtt elveti azt. |
|CélReplicaSetMéret |Int, az alapértelmezett érték 7 |Nem engedélyezett|Az elnevezési szolgáltatás tároló egyes partícióihoz betöltő replikakészletek száma. A replikakészletek számának növelése növeli az információk megbízhatósági szintjét az Elnevezési szolgáltatástárolóban; csökkenti azt a változást, hogy az információ csomóponthibák következtében elvesz; a Windows Fabric megnövekedett terhelésének és az elnevezési adatok frissítéséhez szükséges időnek a költsége.|

## <a name="nodebufferpercentage"></a>NodeBufferSzázalék
| **Paraméter** | **Engedélyezett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|KeyDoubleValueMap, az alapértelmezett érték: Nincs|Dinamikus|Csomópontkapacitás százaléka metrikanévenként; pufferként használják, hogy a feladatátvételi esethez egy csomóponton tartsanak szabad helyet. |

## <a name="nodecapacities"></a>Csomópontkapacitások

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport |NodeCapacityCollectionMap |Statikus|Különböző metrikák csomópontkapacitásainak gyűjteménye. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport |NodeFaultDomainIdCollection |Statikus|Azokat a tartományokat ismerteti, amelyekhez egy csomópont tartozik. A tartalék tartomány egy URI-n keresztül van definiálva, amely leírja a csomópont helyét az adatközpontban.  A tartalék tartomány URI-i fd:/fd/ formátumúak, amelyet egy URI elérési útszegmens követ.|
|UpgradeDomainId azonosító |karakterlánc, az alapértelmezett a "" |Statikus|Azt a frissítési tartományt írja le, amelyhez egy csomópont tartozik. |

## <a name="nodeproperties"></a>NodeProperties (NodeProperties)

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport |NodePropertyCollectionMap |Statikus|A csomóponttulajdonságok karakterlánckulcs-érték párjainak gyűjteménye. |

## <a name="paas"></a>Paas

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Fürtazonosító |karakterlánc, az alapértelmezett a "" |Nem engedélyezett|X509 tanúsítványtároló, amelyet a háló használ a konfiguráció védelmére. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Számlálók |Sztring | Dinamikus |Vesszővel tagolt teljesítményszámlálók listája. |
|IsEnabled |Bool, az alapértelmezett igaz | Dinamikus |A jelző azt jelzi, hogy engedélyezve van-e a teljesítményszámláló-gyűjtés a helyi csomóponton. |
|MaxCounterBinaryFileSizeInMB |Int, az alapértelmezett érték 1 | Dinamikus |Maximális méret (MB-ban) minden teljesítményszámláló bináris fájljához. |
|NewCounterBinaryFileCreationIntervalinMinutes |Int, az alapértelmezett érték 10 | Dinamikus |Az a maximális időköz (másodpercben), amely után új teljesítményszámláló bináris fájl jön létre. |
|Mintavételi időközökszinszekundumban |Int, az alapértelmezett érték 60 | Dinamikus |Az összegyűjtött teljesítményszámlálók mintavételi időköze. |

## <a name="placementandloadbalancing"></a>Elhelyezés és terheléskiegyensúlyozás

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, az alapértelmezett érték 0 | Dinamikus|Meghatározza az affinitási megkötés prioritását: 0: Kemény; 1: Puha; negatív: Figyelmen kívül hagyás. |
|ApplicationCapacityConstraintPriority | Int, az alapértelmezett érték 0 | Dinamikus|Meghatározza a kapacitásmegkötés prioritását: 0: Kemény; 1: Puha; negatív: Figyelmen kívül hagyás. |
|Automatikus annekt: Elérhető erőforrások|bool, az alapértelmezett érték IGAZ|Statikus|Ez a konfiguráció elindítja a rendelkezésre álló erőforrások automatikus észlelését a csomóponton (CPU és memória) Amikor ez a konfiguráció igaz - valódi kapacitásokat olvasunk és kijavítjuk őket, ha a felhasználó rossz csomópontkapacitásokat adott meg, vagy egyáltalán nem határozta meg őket Ha ez a konfiguráció hamis - nyomon követjük a figyelmeztetést, hogy a felhasználó rossz csomópontkapacitásokat adott meg; de nem fogjuk kijavítani őket; ami azt jelenti, hogy a felhasználó azt szeretné, hogy a kapacitások > legyenek, mint amivel a csomópont valóban rendelkezik, vagy ha a kapacitások nincsenek meghatározva; korlátlan kapacitást fog vállalni |
|BalancingDelayAfterNewNode után | Idő másodpercben, az alapértelmezett érték 120 |Dinamikus|Adja meg az időtartományt másodpercben. Ne kezdje el a kiegyensúlyozási tevékenységeket ezen az időszakon belül egy új csomópont hozzáadása után. |
|BalancingDelayAfterNodeDown | Idő másodpercben, az alapértelmezett érték 120 |Dinamikus|Adja meg az időtartományt másodpercben. Ne kezdje el a kiegyensúlyozó tevékenységeket ezen az időszakon belül egy csomópont lefelé történő esemény után. |
|CapacityKonstraintPriority | Int, az alapértelmezett érték 0 | Dinamikus|Meghatározza a kapacitásmegkötés prioritását: 0: Kemény; 1: Puha; negatív: Figyelmen kívül hagyás. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, alapértelmezett érték 20 | Dinamikus|Azt határozza meg, hogy a ResourceBalancer által kiadott mozgások hány egymást követő alkalommal kerülnek el a diagnosztika elvégzése és az állapotra vonatkozó figyelmeztetések kiadása előtt. Negatív: Ez a feltétel alatt nincs enek figyelmeztetés. |
|ConstraintFixPartialDelayAfterNewNode | Idő másodpercben, az alapértelmezett érték 120 |Dinamikus| Adja meg az időtartományt másodpercben. DNe javítsa ki a FaultDomain és a UpgradeDomain megszorítás idoszakát ebben az időszakban egy új csomópont hozzáadása után. |
|ConstraintFixPartialDelayAfterNodeDown | Idő másodpercben, az alapértelmezett érték 120 |Dinamikus| Adja meg az időtartományt másodpercben. Ne javítsa ki a FaultDomain és a UpgradeDomain megszorítás idoszakában a csomópont lefelé történő eseménye után. |
|Megszorítás:Az egészségjelentési határérték | Int, alapértelmezett érték 50 |Dinamikus| Azt határozza meg, hogy a megszorítást megsértő replika hányszor kell tartósan feloldani a diagnosztika és az állapotjelentések kibocsátása előtt. |
|DetailedConstraintViolationHealthReportLimit | Int, az alapértelmezett érték 200 |Dinamikus| Azt határozza meg, hogy a megszorítást megsértő replika a diagnosztika elvégzése előtt és a részletes állapotjelentések kibocsátása előtt a megszorítást megkell oldani a tartósan nem rögzített állapotban. |
|DetailedDiagnosticsInfoListLimit | Int, alapértelmezett érték 15 |Dinamikus| A diagnosztika előtti csonkolás előtt a megkötésenkénti diagnosztikai bejegyzések számát (részletes információkkal) határozza meg.|
|DetailedNodeListLimit | Int, alapértelmezett érték 15 |Dinamikus| Azt határozza meg, hogy a korlátonként hány csomópontot kell megadni a csonkolás előtt az El nem helyezett replikajelentésekben. |
|DetailedPartitionListLimit | Int, alapértelmezett érték 15 |Dinamikus| A diagnosztika csonkolása előtt felveendő megkötés diagnosztikai bejegyzésenkénti partícióinak számát határozza meg. |
|DetailedVerboseHealthReportLimit | Int, az alapértelmezett érték 200 | Dinamikus|Azt határozza meg, hogy egy el nem helyezett kópiát a részletes állapotjelentések kibocsátása előtt hányszor kell tartósan elhelyezni. |
|EnforceUserServiceMetriccapacityes|az alapértelmezett érték HAMIS | Statikus |Lehetővé teszi a hálószolgáltatások védelmét. Minden felhasználói szolgáltatás egy feladatobjektum/ccsoport alatt van, és meghatározott mennyiségű erőforrásra korlátozódik. Ennek statikusnak kell lennie (a FabricHost újraindítását igényli) a felhasználói feladatobjektum létrehozása/eltávolítása, valamint a Fabric Host megnyitása során végzett korlátok beállítása. |
|FaultDomainConstraintPriority | Int, az alapértelmezett érték 0 |Dinamikus| Meghatározza a tartalék tartománymegkötés prioritását: 0: Kemény; 1: Puha; negatív: Figyelmen kívül hagyás. |
|GlobalMovementThrottleCountingInterval | Idő másodpercben, az alapértelmezett érték 600 |Statikus| Adja meg az időtartományt másodpercben. Adja meg annak az elmúlt időköznek a hosszát, amelyhez tartományreplikamozgásait nyomon kell követni (a GlobalMovementThrottleThreshold-szal együtt használva). 0-ra állítható, hogy teljesen figyelmen kívül hagyja a globális szabályozást. |
|GlobalMovementThrottleKüszöb | Uint, az alapértelmezett érték 1000 |Dinamikus| A kiegyensúlyozási fázisban engedélyezett mozgások maximális száma a GlobalMovementThrottleCountingInterval által jelzett múltbeli intervallumban. |
|GlobalMovementThrottleThresholdForBalancing | Uint, az alapértelmezett érték 0 | Dinamikus|A Kiegyensúlyozási fázisban engedélyezett mozgások maximális száma a GlobalMovementThrottleCountingInterval által jelzett múltbeli intervallumban. A 0 azt jelzi, hogy nincs korlátozás. |
|GlobalMovementThrottleThresholdForPlacement | Uint, az alapértelmezett érték 0 |Dinamikus| Az Elhelyezési fázisban engedélyezett mozgások maximális száma a GlobalMovementThrottleCountingInterval.0 által jelzett múltbeli intervallumban nem jelent korlátot.|
|GlobalMovementThrottleThresholdPercentage|dupla, az alapértelmezett érték 0|Dinamikus|A Kiegyensúlyozási és elhelyezési fázisokban engedélyezett összes mozgás maximális száma (a fürt összes replikájának százalékában kifejezve) a GlobalMovementThrottleCountingInterval által jelzett múltbeli intervallumban. A 0 azt jelzi, hogy nincs korlátozás. Ha mind ez, mind a GlobalMovementThrottleThreshold meg van adva; akkor konzervatívabb határértéket alkalmaznak.|
|GlobalMovementThrottleThresholdPercentageForBalancing|dupla, az alapértelmezett érték 0|Dinamikus|A kiegyensúlyozási fázisban engedélyezett mozgások maximális száma (a PLB-ben lévő replikák teljes számának százalékában kifejezve) a GlobalMovementThrottleCountingInterval által jelzett múltbeli intervallumban. A 0 azt jelzi, hogy nincs korlátozás. Ha ez és a GlobalMovementThrottleThresholdForBalancing is meg van adva; akkor konzervatívabb határértéket alkalmaznak.|
|InBuildThrottlingAssociatedMetric | karakterlánc, az alapértelmezett a "" |Statikus| A társított metrika neve a szabályozás. |
|InBuildThrottlingEnabled | Bool, az alapértelmezett érték hamis |Dinamikus| Határozza meg, hogy a builden i szabályozás engedélyezve van-e. |
|InBuildThrottlingGlobalMaxValue érték | Int, az alapértelmezett érték 0 |Dinamikus|A globálisan engedélyezett beépített replikák maximális száma. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, az alapértelmezett érték hamis | Dinamikus|Azt határozza meg, hogy a feladatátvételi egység bármely típusú frissítése megszakítsa-e a gyors vagy lassú kiegyensúlyozási folyamatot. A megadott "hamis" kiegyensúlyozó futtatás megszakad, ha a FailoverUnit: jön létre/töröl; hiányzó replikákkal rendelkezik; módosított elsődleges replikahely vagy módosított kópiák száma. A kiegyensúlyozásfuttatása nem szakad meg más esetekben – ha a FailoverUnit: további replikákkal rendelkezik; módosította a replikajelzőt; megváltozott csak partíció s e-verzió, vagy bármely más esetben. |
|MinConstraintCheckInterval | Idő másodpercben, az alapértelmezett érték 1 |Dinamikus| Adja meg az időtartományt másodpercben. Azt a minimális időtartamot határozza meg, amelynek két egymást követő megkötés-ellenőrzési kör előtt el kell telnie. |
|MinLoadBalancingInterval | Idő másodpercben, az alapértelmezett érték 5 |Dinamikus| Adja meg az időtartományt másodpercben. Azt a minimális időtartamot határozza meg, amelynek két egymást követő kiegyensúlyozási kör előtt el kell telnie. |
|MinPlacementInterval | Idő másodpercben, az alapértelmezett érték 1 |Dinamikus| Adja meg az időtartományt másodpercben. Azt a minimális időtartamot határozza meg, amelynek két egymást követő elhelyezési kör előtt el kell telnie. |
|MoveExistingReplicaForPlacement | Bool, az alapértelmezett igaz |Dinamikus|Annak beállítása, hogy a meglévő kópiát helyezze-e át az elhelyezés során. |
|MovementPerPartitionThrottleCountingInterval | Idő másodpercben, az alapértelmezett érték 600 |Statikus| Adja meg az időtartományt másodpercben. Adja meg annak az elmúlt időköznek a hosszát, amelyhez az egyes partíciók replikamozgásait nyomon kell követni (a MovementPerPartitionThrottleThreshold-szal együtt). |
|MovementPerPartitionThrottleThreshold | Uint, az alapértelmezett érték 50 |Dinamikus| Nincs kiegyensúlyozásgal kapcsolatos mozgás egy partícióhoz, ha a partíció replikáihoz kapcsolódó mozgások kiegyensúlyozásának száma elérte vagy meghaladta a MovementPerFailoverUnitThrottleThreshold-ot a MovementPerPartitionThrottleCountingInterval által jelzett múltbeli intervallumban. |
|MoveParentToFixAffinityViolation | Bool, az alapértelmezett érték hamis |Dinamikus| Annak beállítása, hogy a szülőkópiák áthelyezhetők-e az affinitási megkötések javításához.|
|Részlegeshelyszolgáltatások | Bool, az alapértelmezett igaz |Dinamikus| Azt határozza meg, hogy a fürtben lévő összes szolgáltatásreplikája "mindent vagy semmit" kerül-e a megfelelő csomópontok ra.|
|PlaceChildWithoutParent nélkül | Bool, az alapértelmezett igaz | Dinamikus|Annak beállítása, hogy meghatározza-e, hogy a gyermekszolgáltatás replika elhelyezhető-e, ha egyetlen szülőkópia sem működik. |
|ElhelyezésConstraintPriority | Int, az alapértelmezett érték 0 | Dinamikus|Meghatározza az elhelyezési megkötés prioritását: 0: Kemény; 1: Puha; negatív: Figyelmen kívül hagyás. |
|ElhelyezésConstraintValidationCacheSize | Int, az alapértelmezett érték 10000 |Dinamikus| Korlátozza az Elhelyezési megkötési kifejezések gyors érvényesítéséhez és gyorsítótárazásához használt táblázat méretét. |
|ElhelyezésSearchTimeout | Idő másodpercben, az alapértelmezett érték 0,5 |Dinamikus| Adja meg az időtartományt másodpercben. A szolgáltatások nyújtása során; legbénáknak kell keresnie, mielőtt visszatérne az eredményre. |
|PLBRefreshGap között | Idő másodpercben, az alapértelmezett érték 1 |Dinamikus| Adja meg az időtartományt másodpercben. Azt a minimális időtartamot határozza meg, amelynek el kell telnie ahhoz, hogy a PLB újra felfrissüljön. |
|PreferredLocationConstraintPriority | Int, az alapértelmezett érték 2| Dinamikus|Meghatározza az előnyben részesített helymegkötés prioritását: 0: Kemény; 1: Puha; 2: Optimalizálás; negatív: Figyelmen kívül hagyás |
|PreferredPrimaryDomainsConstraintPriority| Int, az alapértelmezett érték 1 | Dinamikus| Az elsődleges elsődleges tartománymegkötés prioritását határozza meg: 0: Kemény; 1: Puha; negatív: Figyelmen kívül hagyás |
|PreferUpgradedUD-ok|bool,az alapértelmezett értéke HAMIS|Dinamikus|Be- és kikapcsolja a logikát, amely inkább a már frissített UD-kra költözik. Az SF 7.0-s értékkel kezdődően a paraméter alapértelmezett értéke IGAZ értékről HAMIS értékre változik.|
|PreventTransientOvercommit | Bool, az alapértelmezett érték hamis | Dinamikus|Meghatározza, hogy a PLB azonnal számítson azokra az erőforrásokra, amelyeket a kezdeményezett lépések felszabadítanak. Alapértelmezés szerint; A PLB kezdeményezheti az áthelyezést, és ugyanazon a csomóponton léphet be, amely átmeneti túlterhelést hozhat létre. Ha ezt a paramétert true-ra állítja, az megakadályozza az ilyen típusú túlvéglegesítéseket, és az igény szerinti sikkaszt (más néven placementWithMove) le lesz tiltva. |
|ScaleoutCountConstraintPriority | Int, az alapértelmezett érték 0 |Dinamikus| Meghatározza a horizontális kihatotási számlálási megkötés prioritását: 0: Kemény; 1: Puha; negatív: Figyelmen kívül hagyás. |
|SubclusteringEnabled|Bool, az alapértelmezett érték HAMIS | Dinamikus |Nyugtáztalegység a kiegyensúlyozásszidsztaeltérés számításakor |
|AlfürtözésReportingPolicy| Int, az alapértelmezett érték 1 |Dinamikus|Azt határozza meg, hogy az alfürtküldési állapotjelentések hogyan és mikor kerüljenek elküldésre: 0: Ne jelentsen; 1: Figyelmeztetés; 2: OK |
|SwapPrimaryThrottlingAssociatedMetric | karakterlánc, az alapértelmezett a ""|Statikus| A társított metrika neve a szabályozás. |
|SwapPrimaryThrottlingEnabled | Bool, az alapértelmezett érték hamis|Dinamikus| Határozza meg, hogy a swap-elsődleges szabályozás engedélyezve van-e. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, az alapértelmezett érték 0 |Dinamikus| A globálisan engedélyezett swap-elsődleges replikák maximális száma. |
|TraceCRMReasons |Bool, az alapértelmezett igaz |Dinamikus|Itt adható meg, hogy nyomon kell-e követni a CRM által kibocsátott mozgások okait a működési eseménycsatornára. |
|UpgradeDomainConstraintPriority | Int, az alapértelmezett érték 1| Dinamikus|Meghatározza a frissítési tartomány megkötésének prioritását: 0: Kemény; 1: Puha; negatív: Figyelmen kívül hagyás. |
|UseMoveCostReports | Bool, az alapértelmezett érték hamis | Dinamikus|Arra utasítja az LB-t, hogy hagyja figyelmen kívül a pontozási függvény költségelemét; potenciálisan nagy számú lépés a kiegyensúlyozottabb elhelyezés érdekében. |
|UseSeparateSecondaryLoad | Bool, az alapértelmezett igaz | Dinamikus|A másodlagos replikákhoz külön terhelést kell-e használni. |
|UseSeparateSecondaryMoveCost | Bool, az alapértelmezett érték hamis | Dinamikus|Annak beállítása, hogy melyik határozza meg, hogy a másodlagos replikákhoz külön áthelyezési költséget kell-e használni. |
|ValidatePlacementConstraint | Bool, az alapértelmezett igaz |Dinamikus| Itt adható meg, hogy a szolgáltatás Elhelyezésconstraint kifejezése érvényesítve legyen-e a szolgáltatás ServiceDescription frissítésekor. |
|ValidatePrimaryPlacementConstraintOnPromote| Bool, az alapértelmezett érték IGAZ |Dinamikus|Itt adható meg, hogy a szolgáltatás Elhelyezésconstraint kifejezése ki legyen-e értékelve a feladatátvétel elsődleges beállítására. |
|Részletes healthreportlimit | Int, alapértelmezett érték 20 | Dinamikus|Azt határozza meg, hogy egy kópiának hány alkalommal kell eltenned, mielőtt állapotfigyelmeztetést jelentenek érte (ha a részletes állapotjelentés engedélyezve van). |
|NodeLoadsOperationalTracingEnabled | Bool, az alapértelmezett igaz |Dinamikus|Konfiguráció, amely lehetővé teszi a csomópont terhelése működési szerkezeti nyomkövetés az eseménytárolóban. |
|NodeloadsOperationalTracingInterval | TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(20) | Dinamikus|Adja meg az időtartományt másodpercben. Az az időköz, amellyel a csomópont terhelések nyomon követése az egyes szolgáltatástartományok eseménytárolójára. |

## <a name="reconfigurationagent"></a>Újrakonfigurációügynök

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration alkalmazás | Idő másodpercben, az alapértelmezett érték 900 |Dinamikus|Adja meg az időtartományt másodpercben. Az az időtartam, amelyre a rendszer várni fog az alkalmazásfrissítés során szorosan beragadt replikákkal működő szolgáltatásgazdaok megszüntetéséig.|
|FabricUpgradeMaxReplicaCloseDuration | Idő másodpercben, az alapértelmezett érték 900 |Dinamikus| Adja meg az időtartományt másodpercben. Az időtartam, amelyre a rendszer várni fog a szolgáltatásgazdagépek, amelyek replikák, amelyek a háló frissítése során szorosan beragadt. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(120)|Dinamikus|Adja meg az időtartományt másodpercben. Az az időtartam, amelyre a rendszer várni fog a közeli replikákkal beragadt replikákkal rendelkező szolgáltatásgazdaok megszüntetéséig. Ha ez az érték 0-ra van állítva, a replikák bezárására nem lesz utasítás.|
|NodedeactivationMaxReplicaCloseDuration | Idő másodpercben, az alapértelmezett érték 900 |Dinamikus|Adja meg az időtartományt másodpercben. Az az időtartam, amelyre a rendszer várni fog a csomópont-deaktiválás során szorosan beragadt replikákkal lévő szolgáltatásállomások megszüntetéséig. |
|PeriodikusapiSlowTraceInterval | Idő másodpercben, az alapértelmezett érték 5 perc |Dinamikus| Adja meg az időtartományt másodpercben. PeriodicApiSlowTraceInterval határozza meg az időközt, amely en lassú API-hívások at kell nyomon követni az API-figyelő. |
|ReplicaChangeRoleFailureRestartThreshold|int, az alapértelmezett érték 10|Dinamikus| Egész. Adja meg az API-hibák számát az elsődleges előléptetés során, amely után az automatikus kockázatcsökkentési művelet (replika újraindítása) lesz alkalmazva. |
|ReplicaChangeRoleFailure(Jelentés)|int, alapértelmezett 2147483647|Dinamikus| Egész. Adja meg az API-hibák számát az elsődleges előléptetés során, amely után a figyelmeztető állapotjelentés meglesz.|
|ServiceApiHealthDuration | Idő másodpercben, az alapértelmezett érték 30 perc |Dinamikus| Adja meg az időtartományt másodpercben. ServiceApiHealthDuration határozza meg, hogy mennyi ideig várjuk meg a szolgáltatás API-t futtatni, mielőtt azt jelenti, hogy nem kifogástalan állapotú. |
|ServiceReconfigurationApiHealthDuration | Idő másodpercben, az alapértelmezett érték 30 |Dinamikus| Adja meg az időtartományt másodpercben. ServiceReconfigurationApiHealthDuration határozza meg, hogy mennyi ideig várjuk meg a szolgáltatás API futtatásához, mielőtt jelentést nem kifogástalan. Ez az API-hívások, amelyek hatással vannak a rendelkezésre állásra.|

## <a name="replication"></a>Replikáció
| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, az alapértelmezett érték common::TimeSpan::FromMilliseconds(15)|Statikus|Adja meg az időtartományt másodpercben. Azt határozza meg, hogy a replikátor mennyi időt várjon a művelet fogadása után, mielőtt visszaküldené a nyugtázást. Az ebben az időszakban fogadott egyéb műveletek nyugtázása egyetlen üzenetben lesz visszaküldve, > csökkenti a hálózati forgalmat, de potenciálisan csökkenti a replikátor átviteli mertéjét.|
|MaxCopyQueueMéret|uint, az alapértelmezett érték 1024|Statikus|Ez a maximális érték határozza meg a replikációs műveleteket fenntartó várólista kezdeti méretét. Ne feledje, hogy 2-es teljesítménynek kell lennie. Ha futásidőben a várólista ekkora méretre nő, a művelet az elsődleges és a másodlagos replikátorok között lesz szabályozva.|
|MaxPrimaryReplicationQueueMemorySize|uint, az alapértelmezett érték 0|Statikus|Ez az elsődleges replikációs várólista maximális értéke bájtban.|
|MaxPrimaryReplicationQueueMéret|uint, az alapértelmezett érték 1024|Statikus|Ez az elsődleges replikációs várólistában létező műveletek maximális száma. Ne feledje, hogy 2-es teljesítménynek kell lennie.|
|MaxReplicationMessageSize|uint, alapértelmezett érték: 52428800|Statikus|A replikációs műveletek maximális üzenetmérete. Az alapértelmezett érték 50MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, az alapértelmezett érték 0|Statikus|Ez a másodlagos replikációs várólista maximális értéke bájtban.|
|MaxSecondaryReplicationQueueSize|uint, alapértelmezett érték 2048|Statikus|Ez a másodlagos replikációs várólistában létező műveletek maximális száma. Ne feledje, hogy 2-es teljesítménynek kell lennie.|
|QueueHealthMonitoringInterval|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(30)|Statikus|Adja meg az időtartományt másodpercben. Ez az érték határozza meg a Replikátor által a replikációs műveletvárólistákban lévő figyelmeztető/hiba állapotesemények figyelésére használt időszakot. A "0" érték letiltja az állapotfigyelést |
|QueueHealthWarningatUsagePercent|uint, az alapértelmezett érték 80|Statikus|Ez az érték határozza meg a replikációs várólista-használatot (százalékban), amely után figyelmeztetést jelentünk a magas várólista-használatról. Ezt a QueueHealthMonitoringInterval türelmi időköz után tesszük. Ha a várólista-használat a türelmi intervallumban ez a százalék alá esik|
|Replikátorcím|karakterlánc, az alapértelmezett érték "localhost:0"|Statikus|A végpont formájában egy karakterlánc -'IP:Port', amely a Windows Fabric Replikátor kapcsolatok létesítésére más replikák műveletek küldése/fogadása érdekében.|
|ReplicatorListenAddress cím|karakterlánc, az alapértelmezett érték "localhost:0"|Statikus|A végpont formájában egy karakterlánc -'IP:Port', amely a Windows Fabric Replikátor más replikák műveletek fogadására.|
|ReplikátorPublishAddress|karakterlánc, az alapértelmezett érték "localhost:0"|Statikus|A végpont formájában egy karakterlánc -'IP:Port', amely a Windows Fabric Replikátor műveletek küldésére más replikák.|
|Újrapróbálkozási intervallum|TimeSpan, az alapértelmezett érték common::TimeSpan::FromSeconds(5)|Statikus|Adja meg az időtartományt másodpercben. Ha egy művelet elvész, vagy elutasítja ez az időzítő határozza meg, hogy milyen gyakran a replikátor újra küldi a műveletet.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|IsEnabled|az alapértelmezett érték HAMIS |Statikus|Azt szabályozza, hogy a szolgáltatás engedélyezve van-e a fürtben. |

## <a name="runas"></a>Runas

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Runasaccountname (Runasaccountname) |karakterlánc, az alapértelmezett a "" |Dinamikus|A RunAs fiók nevét jelzi. Ez csak a "DomainUser" vagy a "ManagedServiceAccount" fióktípus esetén szükséges. Az érvényes értékek a következők:user@domain"tartomány\felhasználó" vagy " ". |
|RunasAccountType (RunasAccountType)|karakterlánc, az alapértelmezett a "" |Dinamikus|A RunAs fiók típusát jelzi. Ez minden RunAs szakaszhoz szükséges: Az érvényes értékek a "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunasPassword|karakterlánc, az alapértelmezett a "" |Dinamikus|A RunAs fiók jelszavát jelzi. Ez csak a "DomainUser" fióktípusához szükséges. |

## <a name="runas_dca"></a>RunAs_DCA

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Runasaccountname (Runasaccountname) |karakterlánc, az alapértelmezett a "" |Dinamikus|A RunAs fiók nevét jelzi. Ez csak a "DomainUser" vagy a "ManagedServiceAccount" fióktípus esetén szükséges. Az érvényes értékek a következők:user@domain"tartomány\felhasználó" vagy " ". |
|RunasAccountType (RunasAccountType)|karakterlánc, az alapértelmezett a "" |Dinamikus|A RunAs fiók típusát jelzi. Ez minden RunAs szakaszhoz szükséges: Az érvényes értékek a következők: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunasPassword|karakterlánc, az alapértelmezett a "" |Dinamikus|A RunAs fiók jelszavát jelzi. Ez csak a "DomainUser" fióktípusához szükséges. |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Runasaccountname (Runasaccountname) |karakterlánc, az alapértelmezett a "" |Dinamikus|A RunAs fiók nevét jelzi. Ez csak a "DomainUser" vagy a "ManagedServiceAccount" fióktípus esetén szükséges. Az érvényes értékek a következők:user@domain"tartomány\felhasználó" vagy " ". |
|RunasAccountType (RunasAccountType)|karakterlánc, az alapértelmezett a "" |Dinamikus|A RunAs fiók típusát jelzi. Ez minden RunAs szakaszhoz szükséges: Az érvényes értékek a következők: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunasPassword|karakterlánc, az alapértelmezett a "" |Dinamikus|A RunAs fiók jelszavát jelzi. Ez csak a "DomainUser" fióktípusához szükséges. |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Runasaccountname (Runasaccountname) |karakterlánc, az alapértelmezett a "" |Dinamikus|A RunAs fiók nevét jelzi. Ez csak a "DomainUser" vagy a "ManagedServiceAccount" fióktípus esetén szükséges. Az érvényes értékek a következők:user@domain"tartomány\felhasználó" vagy " ". |
|RunasAccountType (RunasAccountType)|karakterlánc, az alapértelmezett a "" |Dinamikus|A RunAs fiók típusát jelzi. Ez minden RunAs szakaszhoz szükséges: Az érvényes értékek a következők: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunasPassword|karakterlánc, az alapértelmezett a "" |Dinamikus|A RunAs fiók jelszavát jelzi. Ez csak a "DomainUser" fióktípusához szükséges. |

## <a name="security"></a>Biztonság
| **Paraméter** | **Engedélyezett értékek** |**Frissítési szabályzat**| **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|AADCertEndpointFormátum|karakterlánc, az alapértelmezett a ""|Statikus|AAD Cert Endpoint format, alapértelmezett Azure Commercial, nem alapértelmezett környezetben,\/például az Azure Government "https: /login.microsoftonline.us/ /login.microsoftonline.us/{0}/federationmetadata/2007-06/federationmetadata.xml" |
|AADClientalkalmazás|karakterlánc, az alapértelmezett a ""|Statikus|Hálóügyfeleket képviselő natív ügyfélalkalmazás-név vagy azonosító |
|AADClusteralkalmazás|karakterlánc, az alapértelmezett a ""|Statikus|A fürtöt jelölő webAPI-alkalmazás név vagy azonosító |
|AADLoginEndpoint|karakterlánc, az alapértelmezett a ""|Statikus|AAD bejelentkezési végpont, alapértelmezett Azure Commercial, nem alapértelmezett környezetben, például az Azure Government "https:\//login.microsoftonline.us" |
|AADTenantId|karakterlánc, az alapértelmezett a ""|Statikus|Bérlőazonosító (GUID) |
|AcceptExpiredPinnedClusterCertificate|az alapértelmezett érték HAMIS|Dinamikus|Az ujjlenyomatáltal deklarált lejárt fürttanúsítványok elfogadásának jelzője csak a fürttanúsítványokra vonatkozik; hogy életben tartsa a halmazt. |
|AdminClientCertUjjlenyomatok|karakterlánc, az alapértelmezett a ""|Dinamikus|A rendszergazdai szerepkörben lévő ügyfelek által használt tanúsítványok ujjlenyomatai. Ez egy vesszővel tagolt névlista. |
|AADTokenEndpointFormátum|karakterlánc, az alapértelmezett a ""|Statikus|AAD token végpont, alapértelmezett Azure Commercial, nem alapértelmezett környezetben, például az Azure Government "https:\//login.microsoftonline.us/{0}" |
|AdminClientJog|karakterlánc, az alapértelmezett a ""|Dinamikus|Az admin ügyfelektől elvárt összes lehetséges követelés; ugyanolyan formátumú, mint a ClientClaims; ez a lista belsőleg kerül hozzáadásra ClientClaims; így nem kell is hozzá ugyanazokat a bejegyzéseket ClientClaims. |
|AdminClientIdentities|karakterlánc, az alapértelmezett a ""|Dinamikus|A hálózati szerepkörben lévő hálókliensek Windows-identitásai; privilegizált hálóműveletek engedélyezéséhez. Ez egy vesszővel tagolt lista; minden bejegyzés tartományfiók vagy csoportnév. A kényelem kedvéért; a fabric.exe programot futtató fiók automatikusan rendszergazdai szerepkörhöz lesz rendelve; így a ServiceFabricAdministrators csoport is. |
|ApprunasaccountGroupX509mappa|karakterlánc, az alapértelmezett érték /home/sfuser/sfusercerts |Statikus|Az AppRunAsAccountGroup X509 tanúsítványokat és személyes kulcsokat tartalmazó mappa |
|CertificateExpirySafetyMargin|TimeSpan, az alapértelmezett érték common::TimeSpan::FromMinutes(43200)|Statikus|Adja meg az időtartományt másodpercben. A tanúsítvány lejáratának biztonsági tartaléka; a tanúsítvány állapotjelentésének állapota OK-ról Figyelmeztetésre változik, ha a lejárat ennél közelebb van. Az alapértelmezett érték 30 nap. |
|CertificateHealthReportingInterval tanúsítvány|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(3600 * 8)|Statikus|Adja meg az időtartományt másodpercben. A tanúsítvány állapotjelentésének időköze; alapértelmezés szerint 8 óra; 0-ra állítás letiltja a tanúsítvány állapotjelentését |
|ClientCertUjjlenyomatok|karakterlánc, az alapértelmezett a ""|Dinamikus|Az ügyfelek által a fürttel való beszélgetéshez használt tanúsítványok ujjlenyomatai; a fürt ezt a bejövő kapcsolatot engedélyezi. Ez egy vesszővel tagolt névlista. |
|ClientClaimAuthEnabled|az alapértelmezett érték HAMIS|Statikus|Azt jelzi, hogy a jogcímalapú hitelesítés engedélyezve van-e az ügyfeleken; a true implicit módon beállítja a ClientRoleEnabled beállítást. |
|Ügyféljogcímek|karakterlánc, az alapértelmezett a ""|Dinamikus|Az ügyfelektől az átjáróhoz való csatlakozáshoz szükséges összes lehetséges jogcím. Ez egy "OR" lista: \| \| ClaimsEntry ClaimsEntry \| \| ClaimsEntry ... minden ClaimsEntry egy "ÉS" lista: ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|Ügyfélidentitások|karakterlánc, az alapértelmezett a ""|Dinamikus|A FabricClient Windows-identitásai; az átjáró elnevezése ezt használja a bejövő kapcsolatok engedélyezéséhez. Ez egy vesszővel tagolt lista; minden bejegyzés tartományfiók vagy csoportnév. A kényelem kedvéért; a fabric.exe programot futtató fiók automatikusan engedélyezett; így a ServiceFabricAllowedUsers és a ServiceFabricAdministrators csoport. |
|ClientRoleEnabled|az alapértelmezett érték HAMIS|Statikus|Azt jelzi, hogy az ügyfélszerepkör engedélyezve van-e; ha igaz értékre van állítva; az ügyfelek identitásuk alapján kapnak szerepköröket. A V2 esetében; ennek engedélyezése azt jelenti, hogy az AdminClientCommonNames/AdminClientIdentities nem szereplő ügyfél csak írásvédett műveleteket hajthat végre. |
|ClusterCertUjjlenyomatok|karakterlánc, az alapértelmezett a ""|Dinamikus|A fürthöz való csatlakozásra engedélyezett tanúsítványok ujjlenyomatai; vesszővel tagolt névlista. |
|ClusterCredentialType típus|karakterlánc, az alapértelmezett érték "Nincs"|Nem engedélyezett|A fürt védelméhez használandó biztonsági hitelesítő adatok típusát jelzi. Az érvényes értékek a következők: "Nincs/X509/Windows" |
|Fürtidentitások|karakterlánc, az alapértelmezett a ""|Dinamikus|A fürtcsomópontok Windows-identitásai; fürttagság-engedélyezéshez. Ez egy vesszővel tagolt lista; minden bejegyzés egy tartományfiók vagy csoportnév |
|ClusterSpn|karakterlánc, az alapértelmezett a ""|Nem engedélyezett|A fürt egyszerű szolgáltatásneve; ha a háló egyetlen tartományfelhasználóként fut (gMSA/tartományi felhasználói fiók). Ez az SPN a lízing figyelők és figyelők fabric.exe: összevonási figyelők; belső replikációfigyelők; futásidejű szolgáltatásfigyelő és az átjárófigyelő elnevezése. Ezt üresen kell hagyni, ha a háló gépfiókként fut; ebben az esetben az oldalszámítási figyelő SPN csatlakoztatása a figyelő átviteli címéről. |
|CrlCheckingFlag|uint, az alapértelmezett érték: 0x40000000|Dinamikus|Alapértelmezett tanúsítványlánc-érvényesítési jelző; felülírhatják az alkatrészspecifikus zászlóval; pl. Összevonás/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY A 0-ra állítás letiltja a visszavont tanúsítványok listájának ellenőrzését A támogatott értékek teljes listáját a CertGetCertificateChain dwFlags dokumentálja:https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, az alapértelmezett érték common::TimeSpan::FromMinutes(15)|Dinamikus|Adja meg az időtartományt másodpercben. A visszavont tanúsítványok listájának ellenőrzése mennyi ideig van letiltva egy adott tanúsítvány esetében kapcsolat nélküli hiba után; ha a visszavont tanúsítványok listájának offline hibáját figyelmen kívül lehet hagyni. |
|CrlOfflineHealthReportTtl|TimeSpan, az alapértelmezett érték common::TimeSpan::FromMinutes(1440)|Dinamikus|Adja meg az időtartományt másodpercben. |
|DisableFirewallRuleForDomainProfile| bool, az alapértelmezett érték IGAZ |Statikus| Azt jelzi, hogy a tűzfalszabályt nem kell-e engedélyezni a tartományprofilhoz. |
|DisableFirewallRuleForPrivateProfile| bool, az alapértelmezett érték IGAZ |Statikus| Azt jelzi, hogy a tűzfalszabályt nem kell-e engedélyezni a privát profilhoz. | 
|DisableFirewallRuleFor PublicProfile| bool, az alapértelmezett érték IGAZ | Statikus|Azt jelzi, hogy a tűzfalszabályt nem kell-e engedélyezni a nyilvános profilhoz. |
| LinuxMinTlsVersion kényszerítése | az alapértelmezett érték HAMIS | Statikus | Ha igaz ra van állítva; csak a TLS 1.2+ verziója támogatott.  Ha hamis; korábbi TLS-verziókat. Csak Linuxra vonatkozik |
| PrevalidationOnSecurityChanges kényszerítése | az alapértelmezett érték HAMIS| Dinamikus | A fürtfrissítés viselkedésének szabályozása a biztonsági beállítások változásainak észlelésekor. Ha a beállítás "true", a fürt frissítése megpróbálja biztosítani, hogy legalább egy tanúsítvány megfelel a bemutató szabályok bármelyikének képessé teszi a megfelelő érvényességi szabályt. Az ellenőrzés megtörténik, mielőtt az új beállítások bármely csomópontra vonatkozna, de csak a Fürtkezelő szolgáltatás elsődleges replikáját üzemeltető csomóponton fut a frissítés megkezdésekor. Az alapértelmezett érték jelenleg "false" értékre van állítva; a 7.1-es kiadással kezdődően a beállítás "true" lesz az új Azure Service Fabric-fürtöknél.|
|FabricHostSpn között| karakterlánc, az alapértelmezett a "" |Statikus| FabricHost egyszerű szolgáltatásneve; ha a háló egyetlen tartományi felhasználóként (gMSA/tartományi felhasználói fiók) fut, és a FabricHost gépfiók alatt fut. Ez az IPC-figyelő SPN-je a FabricHost számára; amely alapértelmezés szerint üresen kell hagyni, mivel FabricHost fut gépfiók alatt |
|IgnoreCrlOfflineError|az alapértelmezett érték HAMIS|Dinamikus|A visszavont tanúsítványok listájának offline hibáját figyelmen kívül hagyja-e, amikor a kiszolgálóoldali ellenőrzi a bejövő ügyféltanúsítványokat |
|IgnoreSvrCrlOfflineError|bool, az alapértelmezett érték IGAZ|Dinamikus|A visszavont tanúsítványok listájának offline hibáját figyelmen kívül hagyja-e, amikor az ügyféloldal ellenőrzi a bejövő kiszolgálói tanúsítványokat; alapértelmezett érték szerint igaz. A visszavont kiszolgálói tanúsítványokkal elkövetett támadások a DNS kompromittálóságát igénylik; nehezebb, mint a visszavont ügyféltanúsítványok. |
|ServerAuthCredentialType típus|karakterlánc, az alapértelmezett érték "Nincs"|Statikus|A FabricClient és a fürt közötti kommunikáció biztosításához használandó biztonsági hitelesítő adatok típusát jelzi. Az érvényes értékek a következők: "Nincs/X509/Windows" |
|ServerCertUjjlenyomatok|karakterlánc, az alapértelmezett a ""|Dinamikus|A fürt által az ügyfelekkel való beszélgetésre használt kiszolgálói tanúsítványok ujjlenyomatai; az ügyfelek ezt használják a fürt hitelesítésére. Ez egy vesszővel tagolt névlista. |
|SettingsX509StoreName| karakterlánc, az alapértelmezett érték "MY"| Dinamikus|X509 tanúsítványtároló, amelyet a háló használ a konfiguráció védelmére |
|UseClusterCertForIpcServerTlsSecurity|az alapértelmezett érték HAMIS|Statikus|Fürttanúsítvány használata az IPC Server TLS átviteli egység védelméhez |
|X509mappa|karakterlánc, az alapértelmezett : /var/lib/waagent|Statikus|Az X509-tanúsítványokat és személyes kulcsokat tartalmazó mappa |
|TLS1_2_CipherList| sztring| Statikus|Ha nem üres karakterláncra van állítva; felülírja a TLS1.2 és az alatti támogatott titkosítási listát. Lásd a támogatott rejtjellista visszakeresésére szolgáló "openssl-ciphers" dokumentációt és a listaformátumot Példa a TLS1.2 erős titkosítási listájára: "ECDHE-ECDSA-AES256-GCM--SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES-128-GCM-SHA256:ECDHE-ECDSA-AES256-CBC-SHA384:ECDHE-ECDSA-AES128--CBC-SHA256:ECDHE-RSA-AES256-CBC-SHA384:ECDHE-RSA-AES128-CBC-SHA256" Csak Linuxra vonatkozik. |

## <a name="securityadminclientx509names"></a>Biztonsági/AdminClientX509Nevek

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|X509NameMap, az alapértelmezett érték Nincs|Dinamikus|Ez a lista a "Név" és az "Érték" pár. Minden "név" tulajdonosi köznapi névvel vagy Az X509-tanúsítványok Dnsname-je rendszergazdai ügyfélműveletekhez engedélyezett. Egy adott "Név" esetében az "Érték" a kiállító rögzítéséhez szükséges tanúsítvány ujjlenyomatainak vesszővel történő külön listája, ha nem üres, a rendszergazdai ügyféltanúsítványok közvetlen kibocsátójának szerepelnie kell a listában. |

## <a name="securityclientaccess"></a>Biztonság/Ügyfélhozzáférés

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Aktiválásnode |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A csomópont aktiválásának biztonsági konfigurációja. |
|A Testparancs megszakítása |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Egy adott TestCommand törlése - ha repülés közben van. |
|CodePackageControl |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A kódcsomagok újraindításának biztonsági beállításai. |
|CreateApplication alkalmazás |karakterlánc, az alapértelmezett "Admin" | Dinamikus|Az alkalmazások létrehozásához. |
|LétrehozáscomposeDeployment|karakterlánc, az alapértelmezett "Admin"| Dinamikus|Írásfájlok által leírt írási központi telepítés létrehozása |
|CreateGatewayResource|karakterlánc, az alapértelmezett "Admin"| Dinamikus|Átjáró-erőforrás létrehozása |
|CreateName (Létrehozási név) |karakterlánc, az alapértelmezett "Admin" |Dinamikus|Az URI-k létrehozásának elnevezésére szolgáló biztonsági konfiguráció. |
|CreateNetwork (Létrehozáshálózat)|karakterlánc, az alapértelmezett "Admin" |Dinamikus|Tárolóhálózat létrehozása |
|CreateService (Szolgáltatás létrehozása) |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A szolgáltatás létrehozásához. |
|CreateServiceFromTemplate |karakterlánc, az alapértelmezett "Admin" |Dinamikus|A szolgáltatás sablonból történő létrehozásának biztonsági konfigurációja. |
|CreateVolume (Kötet létrehozása)|karakterlánc, az alapértelmezett "Admin"|Dinamikus|Kötet létrehozása |
|DeactivateNode |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Biztonsági konfiguráció egy csomópont inaktiválásához. |
|DeactivateNodesBatch |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Több csomópont inaktiválásának biztonsági konfigurációja. |
|Törlés |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A lemezképtároló ügyfél törlési műveletének biztonsági beállításai. |
|DeleteApplication alkalmazás |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Az alkalmazás törlésének biztonsági konfigurációja. |
|KompkompposeTelepítése|karakterlánc, az alapértelmezett "Admin"| Dinamikus|Törli az összeállítás központi telepítését |
|DeleteGatewayResource|karakterlánc, az alapértelmezett "Admin"| Dinamikus|Átjáró-erőforrás törlése |
|DeleteName (Törlésneve) |karakterlánc, az alapértelmezett "Admin" |Dinamikus|Az URI-törlés elnevezésének biztonsági konfigurációja. |
|DeleteNetwork (Törléshálózat)|karakterlánc, az alapértelmezett "Admin" |Dinamikus|Tárolóhálózat törlése |
|DeleteService szolgáltatás |karakterlánc, az alapértelmezett "Admin" |Dinamikus|A szolgáltatás törlésének biztonsági konfigurációja. |
|DeleteVolume (Kötet törlése)|karakterlánc, az alapértelmezett "Admin"|Dinamikus|Kötet törlése.| 
|EnumerálniTulajdonságok |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" | Dinamikus|Az elnevezési tulajdonság számbavételének biztonsági konfigurációja. |
|SzámbasoroljaAlnevek |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| Az URI-felsorolás elnevezésének biztonsági konfigurációja. |
|Fájltartalom |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A lemezképtároló ügyfélfájl-átvitelének biztonsági konfigurációja (a fürtön kívül). |
|Fájlletöltés |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A lemezképtároló ügyfélfájlletöltési kezdeményezésének biztonsági konfigurációja (a fürtön kívül). |
|FinishInfrastructureTask |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Biztonsági konfiguráció az infrastrukturális feladatok befejezéséhez. |
|GetChaosReport | karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| A Káosz állapotának beolvasása egy adott időtartományon belül. |
|GetClusterConfiguration | karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" | Dinamikus|GetClusterConfiguration indukálja a partíciót. |
|GetClusterConfigurationUpgradeStatus | karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| A GetClusterConfigurationUpgradeStatus indukálja egy partíción. |
|GetFabricUpgradeStatus |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| A fürt frissítési állapotának lekérdezésének biztonsági konfigurációja. |
|GetFolderSize |karakterlánc, az alapértelmezett "Admin" |Dinamikus|A FileStoreService mappaméretének beszerzése biztonsági konfigurációja |
|GetNodeDeactivationStatus |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Biztonsági konfiguráció az inaktiválási állapot ellenőrzéséhez. |
|GetNodeTransitionProgress | karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| Biztonsági konfiguráció a csomópont-átmenet parancs előrehaladásának megszerzéséhez. |
|GetPartitionDataLossProgress | karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" | Dinamikus|Lekéri egy adatvesztés api-hívás meghívásának folyamatát. |
|GetPartitionQuorumLossProgress | karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| Lekéri a kvórumveszteség api-hívás folyamatát. |
|GetPartitionRestartProgress | karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| Lekéri az újraindítási partíció API-hívás folyamatát. |
|GetTikók|karakterlánc, az alapértelmezett "Admin"|Dinamikus|Titkos értékek bekéselése |
|GetServiceDescription |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| Biztonsági konfiguráció a hosszú lekérdezési szolgáltatás értesítések és olvasási szolgáltatás leírása. |
|GetStagingLocation (Átmeneti hely) |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A lemezképtároló ügyfél átmeneti helyének lekérésének biztonsági konfigurációja. |
|GetStoreLocation |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A lemezképtárügyfél-tároló helyének lekérésének biztonsági beállításai. |
|GetUpgradeOrchestrationServiceState|karakterlánc, az alapértelmezett "Admin"| Dinamikus|A GetUpgradeOrchestrationServiceState indukálja egy partíción |
|GetUpgradesPendingApproval |karakterlánc, az alapértelmezett "Admin" |Dinamikus| GetUpgradesPendingApproval függvényt indukál egy partíción. |
|GetUpgradeStatus |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| Az alkalmazás frissítési állapotának lekérdezéséhez biztonságos konfiguráció. |
|Belső lista |karakterlánc, az alapértelmezett "Admin" | Dinamikus|A lemezképtároló ügyfélfájllista-művelet (belső) biztonsági konfigurációja. |
|InvokeContainerApi|karakterlánc,az alapértelmezett "Admin"|Dinamikus|Tároló API meghívása |
|InvokeInfrastructureCommand parancs |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Az infrastruktúra feladatkezelési parancsainak biztonsági konfigurációja. |
|InvokeInfrastructureQuery |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" | Dinamikus|Biztonsági konfiguráció az infrastruktúra-feladatok lekérdezéséhez. |
|Lista |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" | Dinamikus|A lemezképtároló ügyfélfájllistájának műveletének biztonsági beállításai. |
|MoveNextFabricUpgradeDomain |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A fürtfrissítések explicit frissítési tartománnyal történő folytatásának biztonsági konfigurációja. |
|MoveNextUpgradeDomain |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Az alkalmazásfrissítések explicit frissítési tartománnyal történő folytatásának biztonsági konfigurációja. |
|MoveReplicaControl |karakterlánc, az alapértelmezett "Admin" | Dinamikus|A replika áthelyezése. |
|NameExists |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" | Dinamikus|Az URI-létezésellenőrzésének elnevezésére szolgáló biztonsági konfiguráció. |
|NodeControl |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A kezdés biztonsági konfigurációja; megállás; és a csomópontok újraindítása. |
|NodeStateRemoved |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A csomópont állapotának jelentéséhez eltávolított biztonsági konfiguráció. |
|Ping |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| Az ügyfélpingek biztonsági beállításai. |
|PredeployPackageToNode |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Telepítés előtti api. |
|ElőtagResolveService |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| Biztonsági konfiguráció a panaszalapú szolgáltatáselőtag megoldásához. |
|TulajdonságReadBatch |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| A tulajdonságolvasási műveletek elnevezésének biztonsági konfigurációja. |
|TulajdonságWriteBatch |karakterlánc, az alapértelmezett "Admin" |Dinamikus|A tulajdonságírási műveletek elnevezésének biztonsági konfigurációi. |
|ProvisionApplicationType |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Az alkalmazástípus-kiépítés biztonsági konfigurációja. |
|ProvisionFabric |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Az MSI és/vagy a fürtjegyzék-kiépítés biztonsági konfigurációja. |
|Lekérdezés |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| A lekérdezések biztonsági beállításai. |
|RecoverPartition (Partíció helyreállítása) |karakterlánc, az alapértelmezett "Admin" | Dinamikus|A partíció helyreállításához szolgáló biztonsági konfiguráció. |
|Partíciók helyreállítása |karakterlánc, az alapértelmezett "Admin" | Dinamikus|A partíciók helyreállítására szolgáló biztonsági konfiguráció. |
|RecoverServicePartitions |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A szolgáltatáspartíciók helyreállításához szolgáló biztonsági konfiguráció. |
|RecoverSystemPartitions |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A rendszerszolgáltatás-partíciók helyreállításához szolgáló biztonsági konfiguráció. |
|RemoveNodeDeactivations |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Biztonsági konfiguráció a több csomópont inaktiválásának visszaállításához. |
|ReportFabricUpgradeHealth |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A fürtfrissítések folytatásának biztonsági konfigurációja az aktuális frissítési folyamattal. |
|Jelentéshiba |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A jelentési hiba biztonsági konfigurációja. |
|JelentésEgészség |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Az állapot jelentésének biztonsági konfigurációja. |
|ReportUpgradeHealth |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Az alkalmazásfrissítések folytatásának biztonsági konfigurációja az aktuális frissítési folyamattal. |
|Partíció visszaállítása |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| A feladatátvevő egység terhelésvisszaállításának biztonsági konfigurációja. |
|ResolveNameOwner (Névtulajdonos) |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" | Dinamikus|Az URI-tulajdonos névhasználati lehetőség feloldásának biztonsági konfigurációja. |
|Partíció feloldása |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" | Dinamikus|A rendszerszolgáltatások feloldásának biztonsági beállításai. |
|ResolveService (Megoldásszolgáltatás) |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| Biztonsági konfiguráció a panaszalapú szolgáltatás megoldásához. |
|ResolveSystemService|karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó"|Dinamikus| A rendszerszolgáltatások feloldásának biztonsági beállításai |
|RollbackApplicationUpgrade |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Az alkalmazásfrissítések visszaállításának biztonsági beállításai. |
|RollbackFabricUpgrade |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A fürtfrissítések visszaállításának biztonsági beállításai. |
|ServiceNotifications |karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| Az eseményalapú szolgáltatásértesítések biztonsági beállításai. |
|SetUpgradeOrchestrationServiceState|karakterlánc, az alapértelmezett "Admin"| Dinamikus|A SetUpgradeOrchestrationServiceState indukálja egy partíción |
|StartApprovedUpgrades |karakterlánc, az alapértelmezett "Admin" |Dinamikus| StartApprovedUpgrades indukál egy partíción. |
|StartChaos között |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Elindítja a Káoszt - ha még nincs elindítva. |
|StartClusterConfigurationUpgrade |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Indítási fürtkonfigurációs frissítést indukál egy partíción. |
|StartInfrastructureTask |karakterlánc, az alapértelmezett "Admin" | Dinamikus|Biztonsági konfiguráció az infrastrukturális feladatok indításához. |
|StartNodeTransition (StartNodeTransition) |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A csomópont-átmenet indításának biztonsági konfigurációja. |
|StartPartitionDataLoss |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Adatvesztést idéz elő egy partíción. |
|StartPartitionQuorumLoss |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Kvórumvesztést idéz elő egy partíción. |
|StartPartitionRestart |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Ezzel egyidejűleg újraindítja a partíció néhány vagy az összes replikáját. |
|StopChaos között |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Leállítja a káoszt - ha már elindult. |
|ToggleVerboseServicePlacementHealthReporting | karakterlánc, az alapértelmezett\|\|a "Rendszergazda felhasználó" |Dinamikus| Részletes ServicePlacement HealthReporting szolgáltatáselhelyezési állapotjelentés váltásának biztonsági konfigurációja. |
|UnprovisionApplicationType |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Az alkalmazástípus kioldásának biztonsági konfigurációja. |
|UnprovisionFabric |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Az MSI és/vagy a fürtjegyzék biztonsági konfigurációja megszüntetés. |
|MegbízhatatlanTransportControl |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Megbízhatatlan Szállítás a viselkedések hozzáadásához és eltávolításához. |
|UpdateService szolgáltatás |karakterlánc, az alapértelmezett "Admin" |Dinamikus|A szolgáltatásfrissítések biztonsági beállításai. |
|UpgradeApplication alkalmazás |karakterlánc, az alapértelmezett "Admin" |Dinamikus| Az alkalmazásfrissítések indításához vagy megszakításához. |
|UpgradeComposeTelepítése|karakterlánc, az alapértelmezett "Admin"| Dinamikus|Frissíti a komponálási központi telepítést |
|UpgradeFabric |karakterlánc, az alapértelmezett "Admin" |Dinamikus| A fürtfrissítések indításának biztonsági konfigurációja. |
|Feltöltés |karakterlánc, az alapértelmezett "Admin" | Dinamikus|A lemezképtároló ügyfél feltöltési műveletének biztonsági konfigurációja. |

## <a name="securityclientcertificateissuerstores"></a>Biztonság/ClientCertificateIssuerStores

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|IssuerStoreKeyValueMap, az alapértelmezett érték Nincs |Dinamikus|X509 kiállítói tanúsítványtárolók az ügyféltanúsítványokhoz; Név = clientIssuerCN; Érték = vesszővel elválasztott üzletek listája |

## <a name="securityclientx509names"></a>Biztonság/ClientX509Nevek

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|X509NameMap, az alapértelmezett érték Nincs|Dinamikus|Ez a lista a "Név" és az "Érték" pár. Minden "név" tulajdonosi köznapi névvagy az ügyfélműveletekhez engedélyezett X509-tanúsítványok DnsName neve. Egy adott "Név" esetében az "Érték" a kiállító rögzítéséhez szükséges tanúsítvány ujjlenyomatainak vesszővel történő külön listája, ha nem üres, az ügyféltanúsítványok közvetlen kibocsátójának szerepelnie kell a listában.|

## <a name="securityclustercertificateissuerstores"></a>Biztonság/ClusterCertificateIssuerStores

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|IssuerStoreKeyValueMap, az alapértelmezett érték Nincs |Dinamikus|X509 kiállítói tanúsítványtárolók a fürttanúsítványokhoz; Név = clusterIssuerCN; Érték = vesszővel elválasztott üzletek listája |

## <a name="securityclusterx509names"></a>Biztonság/ClusterX509Names

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|X509NameMap, az alapértelmezett érték Nincs|Dinamikus|Ez a lista a "Név" és az "Érték" pár. Minden "név" tulajdonosi köznapi névvagy A fürtműveletekhez engedélyezett X509-tanúsítványok DnsName-je. Egy adott "Név" esetében az érték a kiállító rögzítéséhez szükséges tanúsítvány ujjlenyomatainak vesszővel történő külön listája, ha nem üres, a fürttanúsítványok közvetlen kibocsátójának szerepelnie kell a listában.|

## <a name="securityservercertificateissuerstores"></a>Biztonsági/ServerCertificateIssuerStores

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|IssuerStoreKeyValueMap, az alapértelmezett érték Nincs |Dinamikus|Az X509 kiállítói tanúsítvány tárolói a kiszolgálói tanúsítványokhoz; Név = kiszolgálóIssuerCN; Érték = vesszővel elválasztott üzletek listája |

## <a name="securityserverx509names"></a>Biztonsági/ServerX509-es nevek

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport|X509NameMap, az alapértelmezett érték Nincs|Dinamikus|Ez a lista a "Név" és az "Érték" pár. Minden "Név" tulajdonosi köznapi névvel vagy a kiszolgálói műveletekhez engedélyezett X509-tanúsítványok Dnsname-ével rendelkezik. Egy adott "Név" esetében az "Érték" a kiállító rögzítéséhez szükséges tanúsítvány ujjlenyomatainak vesszővel történő külön listája, ha nem üres, a kiszolgálói tanúsítványok közvetlen kibocsátójának szerepelnie kell a listában.|

## <a name="setup"></a>Telepítés

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ContainerNetworkName|karakterlánc, az alapértelmezett a ""| Statikus |A tárolóhálózat beállításakor használandó hálózati név.|
|ContainerNetworkTelepítő|bool, az alapértelmezett FALSE (Linux) és az alapértelmezett IGAZ (Windows)| Statikus |Azt jelzi, hogy beállít-e tárolóhálózatot.|
|FabricDataRoot |Sztring | Nem engedélyezett |Service Fabric adatgyökér könyvtár. Az Azure alapértelmezett beállítása: d:\svcfab |
|FabricLogRoot |Sztring | Nem engedélyezett |A szolgáltatásháló naplójának gyökérkönyvtára. Ez az, ahol SF naplók és nyomkövetések kerülnek. |
|NodesToBeRemoved|karakterlánc, az alapértelmezett a ""| Dinamikus |A konfigurációs frissítés részeként eltávolítandó csomópontok. (Csak önálló telepítésekhez)|
|ServiceRunAsAccountName |Sztring | Nem engedélyezett |A fiók neve, amely alatt a fabric host szolgáltatás futtatásához. |
|SkipContainerNetworkresetonreboot|az alapértelmezett érték HAMIS|Nem engedélyezett|Azt jelzi, hogy kihagyja-e a tárolóhálózat alaphelyzetbe állítását újraindításkor.|
|SkipFirewallConfiguration (SkipFirewallConfiguration) |Bool, az alapértelmezett érték hamis | Nem engedélyezett |Itt adható meg, hogy a tűzfalbeállításokat a rendszernek kell-e beállítania. Ez csak akkor érvényes, ha windows tűzfalat használ. Ha harmadik féltől származó tűzfalakat használ, meg kell nyitnia a portokat a rendszer és az alkalmazások számára, hogy |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Szolgáltatók |karakterlánc, az alapértelmezett érték "DSTS" |Statikus|Vesszővel külön listát a token-ellenőrző szolgáltatók engedélyezéséhez (érvényes szolgáltatók: DSTS; AAD). Jelenleg csak egy szolgáltató engedélyezhető bármikor. |

## <a name="traceetw"></a>Nyom/Etw

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Szint |Int, az alapértelmezett érték 4 | Dinamikus |A nyomkövetési etw szint az 1, 2, 3, 4 értékeket veheti fel. Ahhoz, hogy támogatott legyen, a nyomkövetési szintet 4-en kell tartania |

## <a name="transactionalreplicator"></a>TransactionalReplicator (tranzakciós sokszorosító)

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Idő másodpercben, az alapértelmezett érték 0,015 | Statikus | Adja meg az időtartományt másodpercben. Azt határozza meg, hogy a replikátor mennyi időt várjon a művelet fogadása után, mielőtt visszaküldené a nyugtázást. Az ebben az időszakban fogadott egyéb műveletek nyugtázása egyetlen üzenetben lesz visszaküldve, > csökkenti a hálózati forgalmat, de potenciálisan csökkenti a replikátor átviteli mertéjét. |
|MaxCopyQueueMéret |Uint, alapértelmezett érték: 16384 | Statikus |Ez a maximális érték határozza meg a replikációs műveleteket fenntartó várólista kezdeti méretét. Ne feledje, hogy 2-es teljesítménynek kell lennie. Ha futásidőben a várólista ekkora méretre nő, a művelet az elsődleges és a másodlagos replikátorok között lesz szabályozva. |
|MaxPrimaryReplicationQueueMemorySize |Uint, az alapértelmezett érték 0 | Statikus |Ez az elsődleges replikációs várólista maximális értéke bájtban. |
|MaxPrimaryReplicationQueueMéret |Uint, alapértelmezett érték: 8192 | Statikus |Ez az elsődleges replikációs várólistában létező műveletek maximális száma. Ne feledje, hogy 2-es teljesítménynek kell lennie. |
|MaxReplicationMessageSize |Uint, alapértelmezett érték: 52428800 | Statikus | A replikációs műveletek maximális üzenetmérete. Az alapértelmezett érték 50MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, az alapértelmezett érték 0 | Statikus |Ez a másodlagos replikációs várólista maximális értéke bájtban. |
|MaxSecondaryReplicationQueueSize |Uint, alapértelmezett érték: 16384 | Statikus |Ez a másodlagos replikációs várólistában létező műveletek maximális száma. Ne feledje, hogy 2-es teljesítménynek kell lennie. |
|Replikátorcím |karakterlánc, az alapértelmezett érték "localhost:0" | Statikus | A végpont formájában egy karakterlánc -'IP:Port', amely a Windows Fabric Replikátor kapcsolatok létesítésére más replikák műveletek küldése/fogadása érdekében. |

## <a name="transport"></a>Átvitel
| **Paraméter** | **Engedélyezett értékek** |**Frissítési házirend** |**Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(60)|Statikus|Adja meg az időtartományt másodpercben. A kapcsolat beállításának időtúlára mind a bejövő, mind az elfogadó oldalon (beleértve a biztonságos módban történő biztonsági egyeztetést is) |
|FrameHeaderErrorCheckingEnabled|bool, az alapértelmezett érték IGAZ|Statikus|Alapértelmezett beállítás a nem biztonságos módban lévő keretfejlécen történő hibaellenőrzéshez; az összetevő-beállítás felülbírálja ezt. |
|MessageErrorCheckingEnabled|bool,az alapértelmezett értéke HAMIS|Statikus|Alapértelmezett beállítás az üzenetfejlécés -törzs nem biztonságos módban történő hibaellenőrzéséhez; az összetevő-beállítás felülbírálja ezt. |
|FeloldásOption|karakterlánc, az alapértelmezett "meghatározatlan"|Statikus|Meghatározza a teljes tartománynnn oldásának módját.  Az érvényes értékek a következők: "meghatározatlan/ipv4/ipv6". |
|SendTimeout (Küldésidő-kiküldés|TimeSpan, az alapértelmezett érték Common::TimeSpan::FromSeconds(300)|Dinamikus|Adja meg az időtartományt másodpercben. Időtúllépés küldése a beragadt kapcsolat észlelésére. A TCP-hibajelentések bizonyos környezetekben nem megbízhatóak. Ezt szükség lehet a rendelkezésre álló hálózati sávszélesség és\*a\/\*kimenő adatok méretének megfelelően módosítani ( MaxMessageSize SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Automatikus frissítés | Bool, az alapértelmezett igaz |Statikus| Automatikus lekérdezési és frissítési művelet egy célállapot-fájl alapján. |
|Automatikus frissítésTelepítés engedélyezve|Bool, az alapértelmezett érték HAMIS|Statikus|A célállapot-fájlon alapuló kódfrissítési művelet automatikus lekérdezése, kiépítése és telepítése.|
|GoalStateExpirationReminderinDays|int, az alapértelmezett érték 30|Statikus|Beállítja, hogy hány nap után jelenjen meg a célállapot-emlékeztető.|
|MinReplicaSetMéret |Int, az alapértelmezett érték 0 |Statikus |A MinReplicaSetSize for UpgradeOrchestrationService.|
|Elhelyezési kényszerek | karakterlánc, az alapértelmezett a "" |Statikus| A UpgradeOrchestrationService elhelyezési kényszerei. |
|QuorumLossWaitDuration | Idő másodpercben, az alapértelmezett érték MaxValue |Statikus| Adja meg az időtartományt másodpercben. A QuorumLossWaitDuration for UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Idő másodpercben, az alapértelmezett érték 60 perc|Statikus| Adja meg az időtartományt másodpercben. A ReplicaRestartWaitDuration for UpgradeOrchestrationService. |
|Készenléti replikálási időtartam | Idő másodpercben, az alapértelmezett érték 60*24*7 perc |Statikus| Adja meg az időtartományt másodpercben. A StandByReplicaKeepDuration for UpgradeOrchestrationService. |
|CélReplicaSetMéret |Int, az alapértelmezett érték 0 |Statikus |A TargetReplicaSetSize for UpgradeOrchestrationService. |
|UpgradeApprovalSzükséges | Bool, az alapértelmezett érték hamis | Statikus|A kódfrissítés beállításához a folytatás előtt rendszergazdai jóváhagyásszükséges. |

## <a name="upgradeservice"></a>UpgradeService

| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|BaseUrl | karakterlánc, az alapértelmezett a "" |Statikus|BaseUrl a UpgradeService számára. |
|Fürtazonosító | karakterlánc, az alapértelmezett a "" |Statikus|Fürtazonosító az UpgradeService szolgáltatáshoz. |
|CoordinatorType típus | karakterlánc, az alapértelmezett érték "WUTest"|Nem engedélyezett|A CoordinatorType for UpgradeService. |
|MinReplicaSetMéret | Int, az alapértelmezett érték 2 |Nem engedélyezett| A MinReplicaSetSize for UpgradeService. |
|OnlyBaseUpgrade (OnlyBaseUpgrade) | Bool, az alapértelmezett érték hamis |Dinamikus|OnlyBaseUpgrade a UpgradeService szolgáltatáshoz. |
|Elhelyezési kényszerek |karakterlánc, az alapértelmezett a "" |Nem engedélyezett|A frissítési kényszerek elhelyezése. |
|PollIntervalinszundum|Timespan, az alapértelmezett érték Common::TimeSpan::FromSeconds(60) |Dinamikus|Adja meg az időtartományt másodpercben. Az ARM-kezelési műveletek UpgradeService lekérdezése közötti időköz. |
|CélReplicaSetMéret | Int, az alapértelmezett érték 3 |Nem engedélyezett| A TargetReplicaSetSize for UpgradeService. |
|TestCabFolder mappa | karakterlánc, az alapértelmezett a "" |Statikus| TestCabFolder for UpgradeService. |
|X509FindType | karakterlánc, az alapértelmezett a ""|Dinamikus| X509FindType for UpgradeService. |
|X509FindValue | karakterlánc, az alapértelmezett a "" |Dinamikus| X509FindValue for UpgradeService. |
|X509Másodlagosfindérték | karakterlánc, az alapértelmezett a "" |Dinamikus| X509SecondaryFindValue for UpgradeService. |
|X509StoreLocation | karakterlánc, az alapértelmezett a "" |Dinamikus| X509StoreLocation for UpgradeService. |
|X509StoreName | karakterlánc, az alapértelmezett érték "Saját"|Dinamikus|X509StoreName for UpgradeService. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacityes
| **Paraméter** | **Engedélyezett értékek** | **Frissítési szabályzat** | **Útmutatás vagy rövid leírás** |
| --- | --- | --- | --- |
|Tulajdonságcsoport| UserServiceMetricCapacitiesMap, az alapértelmezett érték Nincs | Statikus | A felhasználói szolgáltatások erőforrás-irányítási korlátok gyűjteménye statikusnak kell lennie, mivel hatással van az automatikus észlelési logikára |

## <a name="next-steps"></a>További lépések
További információ: [Az Azure-fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-azure.md) és [az önálló fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md)című témakörben található.