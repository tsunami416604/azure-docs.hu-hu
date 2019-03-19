---
title: Az Azure Service Fabric actors ReliableDictionaryActorStateProvider beállításainak módosítása |} A Microsoft Docs
description: Ismerje meg az Azure Service Fabric állapotalapú actors ReliableDictionaryActorStateProvider típusú konfigurálása.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: ''
ms.assetid: 79b48ffa-2474-4f1c-a857-3471f9590ded
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 224899e92684d83d33fbd61408e67d4ec11ec0a3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898737"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>A Reliable Actors – ReliableDictionaryActorStateProvider konfigurálása
A reliabledictionaryactorstateprovider alapértelmezett konfigurációjának a settings.xml fájlt hoz létre a Visual Studio csomag gyökérkönyvtárába a Config mappában található a megadott aktor módosításával módosíthatja.

Az Azure Service Fabric-futtatókörnyezet előre definiált nevek a settings.xml fájlban keres, és felhasználja a konfigurációs értékeket az alapul szolgáló futásidejű összetevők létrehozása során.

> [!NOTE]
> Tegye **nem** törölni vagy módosítani a szakaszneveket, a következő konfigurációk jön létre a Visual Studio-megoldásban settings.xml fájlban.
> 
> 

Is találhatók, amelyek befolyásolják a ReliableDictionaryActorStateProvider konfigurációjának globális beállítások.

## <a name="global-configuration"></a>A globális konfiguráció
A globális konfiguráció a fürtjegyzék a fürt a KtlLogger szakaszban van megadva. Lehetővé teszi a megosztott napló helyét és méretét, valamint a globális memóriakorlátokat a naplózó által használt konfigurációját. Vegye figyelembe, hogy a fürtjegyzék hatással vannak az összes ReliableDictionaryActorStateProvider használó szolgáltatások és a reliable stateful services.

A fürtjegyzék egy egyetlen XML-fájl, amely tartalmazza a beállítások és konfigurációk, amely a alkalmazni a csomópontok és a szolgáltatások a fürtben. A fájl neve általában ClusterManifest.xml. Láthatja, hogy a fürt a fürt a Get-ServiceFabricClusterManifest powershell-paranccsal jegyzékfájl.

### <a name="configuration-names"></a>Konfigurációs nevek
| Name (Név) | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |kilobájt |8388608 |A naplózó írási memória pufferkészlet rendszermag módban lefoglalni KB minimális száma. Gyorsítótárazás lemezre írás előtt állapotinformációkat a memóriakészletben használható. |
| WriteBufferMemoryPoolMaximumInKB |kilobájt |Korlátlan |Amelyhez a naplózó írási pufferkészletben memória maximális mérete növelhető. |
| SharedLogId |GUID |"" |Adja meg egy egyedi GUID Azonosítót az alapértelmezett megosztott naplófájl a fürt összes csomópontja, amely a szolgáltatás adott konfigurációban a SharedLogId nem adja meg az összes megbízható szolgáltatások által használt azonosítására használható. Ha SharedLogId van megadva, majd SharedLogPath is kötelező. |
| SharedLogPath |Teljes elérési útja |"" |Itt adhatja meg a teljes elérési útja, ahol a megosztott naplófájlban a fürt összes csomópontja, amely a szolgáltatás adott konfigurációban a SharedLogPath nem adja meg az összes megbízható szolgáltatás használja. Azonban ha SharedLogPath van megadva, majd SharedLogId is kötelező. |
| SharedLogSizeInMB |megabájt |8192 |Ennyi statikusan lefoglalni a közös naplók MB szabad lemezterület. Az értéknek kell lennie, 2048 vagy nagyobb. |

### <a name="sample-cluster-manifest-section"></a>Minta cluster manifest szakasz
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Megjegyzések
A naplózó rendelkezik egy globális készlet nem lapozható kernel memória érhető el az összes megbízható szolgáltatás a csomópont állapotadatok gyorsítótárazáshoz, előtt a reliable Services replika társított ír a dedikált napló számára lefoglalt memória. A készlet méretét a WriteBufferMemoryPoolMinimumInKB és WriteBufferMemoryPoolMaximumInKB beállításai vezérlik majd. WriteBufferMemoryPoolMinimumInKB megadja a memóriakészlet kezdeti mérete és a legkisebb mérete, amelyhez a memóriakészletben lehet zsugorítani. WriteBufferMemoryPoolMaximumInKB, amelyre a memóriakészletben előfordulhat, hogy növelje a legnagyobb mérete. Minden egyes megnyitott reliable Services-replika növelhető a memóriakészletben mérete legfeljebb WriteBufferMemoryPoolMaximumInKB rendszer meghatározott értékkel. Ha több igény szerint a memóriakészletében nincs elég memória, kérelmek, a memória időszakú késleltetéssel kerül mindaddig, amíg elérhető memória. Ezért ha az írási puffert memóriakészletben túl kicsi, egy adott konfigurációhoz majd a teljesítmény romolhat.

A SharedLogId és SharedLogPath beállítások mindig használhatók együtt adható meg GUID és a helyét csomópontjaihoz tartozó alapértelmezett megosztott napló a fürtben. Az alapértelmezett megosztott napló, amely nem adja meg a beállításokat a settings.xml az adott szolgáltatás minden reliable Services szolgál. A legjobb teljesítmény érdekében közös naplófájlokat a versengés csökkentése érdekében a megosztott naplófájl kizárólag a használt lemezen kell elhelyezni.

SharedLogSizeInMB megadja az alapértelmezett megosztott naplók az összes csomóponton lefoglalandó területet.  SharedLogId és SharedLogPath nem kell megadni ahhoz, hogy SharedLogSizeInMB adni.

## <a name="replicator-security-configuration"></a>A replikáló biztonsági konfiguráció
A replikáló biztonsági konfigurációk szolgálnak a replikáció során használt kommunikációs csatornát. Ez azt jelenti, hogy a szolgáltatások nem láthatják a többi összes replikációs forgalom, amely biztosítja az adatok magas rendelkezésre állású legyen is biztonságos.
Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza, hogy a replikációs biztonságot.

> [!IMPORTANT]
> A Linux-csomópontokat a tanúsítványok kell PEM-formátumú. További információk megkeresése és tanúsítványok konfigurálása Linux kapcsolatban lásd: [tanúsítványok konfigurálása Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Szakasz neve
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikációs konfiguráció
A replikáló konfigurációk segítségével konfigurálhatja a replikátor, amely feladata, hogy az Aktor Riasztásiállapot-szolgáltató állapota magas megbízhatóságú replikál, és az állapot helyi megőrzése.
Az alapértelmezett konfiguráció a Visual Studio-sablon által létrehozott és elegendőnek kell lennie. Ez a szakasz ismerteti a replikátor finomhangolása elérhető további konfigurációs.

### <a name="section-name"></a>Szakasz neve
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurációs nevek
| Name (Név) | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |másodperc |0.015 |Adott időszakban, amelyhez a replikátor, miután kapott egy művelet, mielőtt elküldené a másodlagos vár az elsődleges biztonsági nyugtázást. Bármely más nyugtázás a intervallumon belül feldolgozott műveletek kell elküldeni, egy választ kapnak. |
| ReplicatorEndpoint |– |Alapértelmezett – kötelező paraméter |IP-cím és az elsődleges és másodlagos replikátor kommunikálni más gyártóitól a replika által használt port megadása A TCP-erőforrás végpontjának a szolgáltatásjegyzékben ez kell hivatkoznia. Tekintse meg [szolgáltatásjegyzéki erőforrások](service-fabric-service-manifest-resources.md) további végpont erőforrások meghatározása szolgáltatásjegyzékben kapcsolatban. |
| MaxReplicationMessageSize |Bájt |50 MB |Egy üzenet továbbítható replikációs adatok maximális mérete. |
| MaxPrimaryReplicationQueueSize |Műveletek száma |8192 |Az elsődleges üzenetsor-műveletek maximális száma. Egy műveletet a fel nem szabadul, miután az elsődleges replikátor nyugtázást fogad az összes másodlagos gyártóitól. Ez az érték nagyobb, mint 64 és 2 hatványának kell lennie. |
| MaxSecondaryReplicationQueueSize |Műveletek száma |16384 |A másodlagos üzenetsor-műveletek maximális száma. Egy művelet fel nem szabadul, az állapot megőrzése révén magas rendelkezésre állású elvégzése után. Ez az érték nagyobb, mint 64 és 2 hatványának kell lennie. |
| CheckpointThresholdInMB |MB |200 |Az állapot alkulcsaihoz log-fájl terület mennyiségét. |
| MaxRecordSizeInKB |KB |1024 |Legnagyobb rekord méretet, amely a replikátor írhatnak a naplóban. Ez az érték 4 és 16-nál nagyobb többszörösének kell lennie. |
| OptimizeLogForLowerDiskUsage |Logikai |true |TRUE érték esetén a napló van konfigurálva, hogy a replika dedikált naplófájlt hoz létre egy ritka NTFS-fájl használatával. Ez csökkenti a tényleges lemezterület-használat a fájlt. Hamis érték esetén a fájl jön létre a rögzített értékesítjük, ami a legjobb írási teljesítményt biztosítanak. |
| SharedLogId |GUID azonosítója |"" |Adja meg egy egyedi GUID azonosítót a megosztott naplófájlban, és a replika használt azonosítására használható. Szolgáltatások általában, ne használja ezt a beállítást. Azonban ha SharedLogId van megadva, majd SharedLogPath is kötelező. |
| SharedLogPath |Teljes elérési útja |"" |Itt adhatja meg a teljes elérési útja, ahol létrejön a megosztott naplófájlt a replika. Szolgáltatások általában, ne használja ezt a beállítást. Azonban ha SharedLogPath van megadva, majd SharedLogId is kötelező. |

## <a name="sample-configuration-file"></a>Minta konfigurációs fájl
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## <a name="remarks"></a>Megjegyzések
A BatchAcknowledgementInterval a paraméterrel állítható be replikáció késése. "0" értéket eredményez a lehető legkisebb késleltetést, átviteli sebesség cserébe (a nyugtázó további üzenetek küldhetők és feldolgozott kevesebb nyugtázás tartalmazó kell).
BatchAcknowledgementInterval minél nagyobb az értéke, annál az általános replikációs átviteli sebességet, cserébe magasabb. műveleti késés. Ez közvetlenül a rendszer lefordítja arra a tranzakció-véglegesítések késését.

A CheckpointThresholdInMB paraméter szabályozza a lemezterület-állapot adatainak tárolására a replika dedikált naplófájlban a replikátor használhatja. Növelésére, mint az alapértelmezett értéke azt eredményezheti, hogy új replikát hozzáadásakor a készlethez, gyorsabb újrakonfigurálása. A részleges állapotátvitel, amely miatt a műveletek a naplóban szereplő további előzmények rendelkezésre állásának okozza. Ez potenciálisan növelheti a replika helyreállítási idő rendszerösszeomlás után.

OptimizeForLowerDiskUsage értéke igaz, ha log fájl terület lesz túlterhelt, hogy aktív replikák tárolhatja állam bővebben a naplófájlokban, míg az inaktív replikák kevesebb lemezterületet igényel fogja használni. Ez lehetővé teszi több replika a csomópont. OptimizeForLowerDiskUsage értéke HAMIS, ha az állapot információ íródik a naplófájlok időnél gyorsabban van szüksége.

A MaxRecordSizeInKB beállítás határozza meg, hogy egy rekord, amely a replikátor által a naplófájlba írható maximális méretét. A legtöbb esetben az alapértelmezett rekord 1024 KB-os mérete optimális. Előfordulhat azonban, ha a szolgáltatás részeként az állapotinformációkat nagyobb adatelemek okoz, majd ezt az értéket kell kell emelni. Nincs kis azért előnyös MaxRecordSizeInKB kisebb, mint 1024, így kisebb rekordok csak a szükséges a kisebb rekord hely használja. Terveink szerint már, hogy ez az érték csak ritka esetekben módosítani kellene.

A SharedLogId és SharedLogPath beállításokat mindig együtt használja, hogy a csomópont egy külön megosztott naplóban az alapértelmezett megosztott naplóból egy szolgáltatást. A hatékonyság növelése érdekében a lehető legtöbb szolgáltatások meg kell adnia az ugyanabba a megosztott naplófájlba. Megosztott fájlok olyan lemezeken, amelyek kizárólag használják a megosztott naplófájl fő áthelyezését a versengés csökkentése érdekében kell elhelyezni. Terveink szerint már, hogy ezek az értékek csak ritka esetekben módosítani kellene.

