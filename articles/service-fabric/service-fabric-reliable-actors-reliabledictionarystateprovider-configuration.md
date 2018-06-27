---
title: Az Azure mikroszolgáltatások ReliableDictionaryActorStateProvider beállításainak módosítását |} Microsoft Docs
description: Azure Service Fabric állapot-nyilvántartó szereplője ReliableDictionaryActorStateProvider típusú beállításának ismertetése.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: ''
ms.assetid: 79b48ffa-2474-4f1c-a857-3471f9590ded
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 1c7e31f9da2b2f9fe7a3a7c64cd6927224cb9fa1
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017895"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Reliable Actors--ReliableDictionaryActorStateProvider konfigurálása
Módosíthatja a ReliableDictionaryActorStateProvider használható alapértelmezett konfigurációt a settings.xml fájlban megadott szereplő a Visual Studio csomag legfelső szintű a Config mappában létrehozott módosításával.

Az Azure Service Fabric-futtatókörnyezet megkeresi az előre definiált nevek a settings.xml fájlban, és a konfigurációs értékeket fel az alapul szolgáló futásidejű összetevők létrehozása során.

> [!NOTE]
> Tegye **nem** törölni vagy módosítani a szakaszneveket, a settings.xml fájlban, a Visual Studio megoldás létrejövő konfigurációt.
> 
> 

Globális beállítások ReliableDictionaryActorStateProvider konfigurációját érintő is vannak.

## <a name="global-configuration"></a>A globális konfiguráció
A globális konfigurációs a fürtjegyzékben, a fürt KtlLogger szakaszban van megadva. Ez lehetővé teszi, hogy a megosztott napló helyét és méretét, valamint a globális memóriakorlátokat a naplózó által használt konfigurációs. Vegye figyelembe, hogy a fürtjegyzékben hatással vannak ReliableDictionaryActorStateProvider használó összes szolgáltatást és megbízható állapotalapú szolgáltatások.

A fürtjegyzékben, amely tárolja a beállításokat és konfigurációkat, amelyek minden csomópont és a fürt szolgáltatások egyetlen XML-fájl. A fájl neve általában ClusterManifest.xml. Láthatja, hogy a fürt jegyzékének a fürthöz, a Get-ServiceFabricClusterManifest powershell-paranccsal.

### <a name="configuration-names"></a>Konfigurációs nevek
| Name (Név) | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |kilobájt |8388608 |Lefoglalni a kernel módban naplózó írási puffer memóriakészletben KB-os minimális száma. A memóriakészletben használható állapotadatokat előtt lemezre írás gyorsítótárazását. |
| WriteBufferMemoryPoolMaximumInKB |kilobájt |Korlátlan |Milyen mértékben növelhető a, amelyhez a naplózó írható memória pufferkészlet maximális mérete. |
| SharedLogId |GUID |"" |Adja meg egy egyedi GUID azonosítója, az alapértelmezett megosztott naplófájl a fürt minden csomópontjára, amelyek nem adnak meg a SharedLogId a szolgáltatáskonfiguráció egyedi az összes megbízható szolgáltatás által használt azonosítására használható. Ha SharedLogId meg van adva, majd SharedLogPath is kötelező. |
| SharedLogPath |Teljes elérési útja |"" |Adja meg a teljes elérési útja, ahol a megosztott naplófájl a fürt minden csomópontjára, amelyek nem adnak meg a SharedLogPath a szolgáltatáskonfiguráció egyedi az összes megbízható szolgáltatások használnak. Azonban ha SharedLogPath meg van adva, majd SharedLogId is kötelező. |
| SharedLogSizeInMB |megabájt |8192 |Azt a statikusan lefoglalni a megosztott napló MB szabad lemezterület. Az érték 2048 vagy nagyobb lehet. |

### <a name="sample-cluster-manifest-section"></a>A minta fürtöt manifest szakasz
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
A tranzakciónaplókat tartalmazó globális memóriát lefoglalni érhető el az összes megbízható szolgáltatáshoz a csomópont állapotadatok gyorsítótárazása, mielőtt a megbízható szolgáltatás replika társított a dedikált naplóba való írása nem lapozható kernelmemória rendelkezik. A készlet méretét a WriteBufferMemoryPoolMinimumInKB és WriteBufferMemoryPoolMaximumInKB beállítások vezérlik. WriteBufferMemoryPoolMinimumInKB határozza meg, mind a kezdeti memóriakészlet és a legkisebb mérete, amelyre a memóriakészletben csökkentheti. WriteBufferMemoryPoolMaximumInKB mérete a legmagasabb, amelyhez a memóriakészletben is növekszik. Minden megnyitott megbízható szolgáltatás replika növelheti a memória-készlet mérete legfeljebb WriteBufferMemoryPoolMaximumInKB megállapítása szerint a rendszer összeggel. Ha további igény szerint a memóriakészletében található, mint amennyi rendelkezésre álló memória, memória kérelmek program elhalasztja mindaddig, amíg elérhető memória. Ezért ha memóriakészletben írási puffer túl kicsi az adott konfigurációs majd a teljesítmény romolhat.

A SharedLogId és SharedLogPath-beállítások mindig használhatók együtt adható meg a GUID Azonosítót és az összes csomópont az alapértelmezett megosztott napló helye a fürt. Az alapértelmezett megosztott napló az összes megbízható szolgáltatás, amely nem adja meg a beállításokat az adott szolgáltatás settings.xml szolgál. A legjobb teljesítmény érdekében a megosztott fájlok kizárólag használják a megosztott naplófájl való versengés csökkentheti lemezekre kell elhelyezni.

SharedLogSizeInMB meghatározza a szabad lemezterület az alapértelmezett megosztott napló minden csomóponton készletméret.  SharedLogId és SharedLogPath nem kell megadni ahhoz, hogy SharedLogSizeInMB meghatározását.

## <a name="replicator-security-configuration"></a>A replikáló biztonsági konfiguráció
A replikáció során használt kommunikációs csatornát replikátor biztonsági beállításokkal szolgálnak. Ez azt jelenti, hogy a szolgáltatások nem látható egymás replikációs forgalmat, amely biztosítja a magas rendelkezésre állási adatok is biztonságos.
Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza, hogy a replikációs biztonságot.

> [!IMPORTANT]
> Linux-csomópont a tanúsítványok kell PEM-formátumú. További információ keresése és tanúsítványok konfigurálása Linux további tudnivalókért lásd: [tanúsítványok konfigurálása Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Szakasz neve
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikációs konfiguráció
Replikációs beállítások segítségével konfigurálhatja a replikátor, amely feladata, hogy az Aktor Állapotszolgáltató állapot nagymértékben megbízható replikálódik, és az állapot helyi megőrzése.
Az alapértelmezett konfiguráció a Visual Studio-sablon által generált és elegendőnek kell lennie. Ez a szakasz beszél finomhangolhatják a replikátor rendelkezésre álló további beállításokat.

### <a name="section-name"></a>Szakasz neve
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurációs nevek
| Name (Név) | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |másodperc |0.015 |Az időszak, amely a következő művelet elküldése előtt fogadása után a másodlagos vár replikátor biztonsági nyugtázást az elsődleges. Ezt az időközt végrehajtva műveletek küldésének bármely más nyugták küldése a egy választ. |
| ReplicatorEndpoint |– |Nincs alapértelmezett érték – a kötelező paraméter: |IP-cím és az elsődleges és másodlagos replikátor kommunikálni más gyártóitól a replika által használt port beállítása. Ez hivatkoznia kell a TCP-erőforrás végpont a szolgáltatás jegyzékben. Tekintse meg [Service manifest erőforrások](service-fabric-service-manifest-resources.md) további végpont erőforrások definiálása szolgáltatás jegyzékben. |
| MaxReplicationMessageSize |Bájt |50 MB |Replikációs adatok egyetlen üzenetben továbbítható maximális mérete. |
| MaxPrimaryReplicationQueueSize |Műveletek száma |8192 |Az elsődleges várólistában lévő műveletek maximális száma. Egy műveletet a fel nem szabadul, miután az elsődleges replikációs nyugtázást fogad az összes másodlagos gyártóitól. Ez az érték 64 és 2 valamelyik hatványa nagyobbnak kell lennie. |
| MaxSecondaryReplicationQueueSize |Műveletek száma |16384 |A másodlagos várólista műveletek maximális száma. Egy művelet fel nem szabadul állapotában adatmegőrzési keresztül magas rendelkezésre állású elvégzése után. Ez az érték 64 és 2 valamelyik hatványa nagyobbnak kell lennie. |
| CheckpointThresholdInMB |MB |200 |Az állapot alkulcsaihoz fájl naplóterület mennyisége. |
| MaxRecordSizeInKB |KB |1024 |A replikátor írhat a naplóban szereplő legnagyobb rekordméretet. Ez az érték nagyobb, mint 16 és 4 többszörösének kell lennie. |
| OptimizeLogForLowerDiskUsage |Logikai |true |Amikor igaz értékű, a naplót, hogy a replika dedikált naplófájlt hoz létre egy NTFS-ritka fájl használatával van konfigurálva. Ez csökkenti a tényleges lemezterület-használat a fájlt. Hamis érték esetén a fájl rögzített foglalásokat, a legjobb írási teljesítményt biztosító hozza létre. |
| SharedLogId |GUID |"" |Adja meg egy egyedi GUID azonosítója, a megosztott naplófájlt a replika használt azonosítására használható. Szolgáltatások általában, ne használja ezt a beállítást. Azonban ha SharedLogId meg van adva, majd SharedLogPath is kötelező. |
| SharedLogPath |Teljes elérési útja |"" |Adja meg a teljes elérési útja, ahol létrejön-e a megosztott naplófájlban a replikára vonatkozóan. Szolgáltatások általában, ne használja ezt a beállítást. Azonban ha SharedLogPath meg van adva, majd SharedLogId is kötelező. |

## <a name="sample-configuration-file"></a>Minta konfigurációs fájlt
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
A BatchAcknowledgementInterval a paraméterrel állítható be replikációs késés. "0" értéket eredményez a lehető legkisebb késleltetést, ugyan átviteli sebesség (a további fel nyugtázási üzeneteket kell küldött és feldolgozásra, kevesebb nyugták tartalmazó).
Minél nagyobb a BatchAcknowledgementInterval értéke, annál magasabb a teljes replikációs teljesítményt, magasabb művelet késés használ. Ez közvetlenül a Tiltás késése a tranzakciók véglegesítése több.

A CheckpointThresholdInMB paraméter határozza meg, amellyel a replikátor tárolni állapotadatait a replika dedikált naplófájl lemezterületet. Növekvő ennek alapértelmezett értéke azt eredményezheti, hogy ha egy új replika hozzáadódik a gyorsabb újrakonfigurálása. Ez az az oka, amelyek miatt a naplóban szereplő műveletek további előzmények rendelkezésre állását akkor történik részleges állapot átvitelét. A potenciálisan növelheti a helyreállítási idő a replikák rendszerösszeomlás után.

Ha OptimizeForLowerDiskUsage igaz értékre van beállítva, napló fájl terület lesz túlzott kiosztott, hogy aktív replikákat tárolhat további állapotadatokat a naplófájlokat, amíg inaktív replikák kevesebb lemezterületet fogja használni. Ez lehetővé teszi több replika csomóponton. OptimizeForLowerDiskUsage értéke HAMIS, ha az állapot információkat gyorsabban írja a rendszernapló fájljaiban.

A MaxRecordSizeInKB beállítás határozza meg, hogy egy rekordot, amely a replikátor által a naplófájl írható maximális méretét. A legtöbb esetben az alapértelmezett 1024 KB-os rekord mérete optimális. Előfordulhat azonban, ha a szolgáltatás okozza az állapotinformációkat részeként nagyobb adatelemek, majd ezt az értéket kell növelni. Nincs a MaxRecordSizeInKB kisebb, mint 1024, így kisebb rekordok használja csak a kisebb bejegyzés szükséges hely sok előnye. Elvárjuk, hogy ez az érték csak ritka esetekben módosítani kellene.

A SharedLogId és SharedLogPath beállítások az alapértelmezett megosztott naplóból egy különálló megosztott naplót használja a csomópont szolgáltatás mindig használhatók együtt. A hatékonyság a lehető legtöbb szolgáltatások megosztott napló kell meghatároznia. Megosztott naplófájlok központi adatátviteli versengés csökkentése érdekében a megosztott naplófájl, kizárólag a használt lemezen kell elhelyezni. Elvárjuk, hogy ezek az értékek csak ritka esetekben módosítani kellene.

