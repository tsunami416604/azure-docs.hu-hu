---
title: Az Azure mikroszolgáltatások KVSActorStateProvider beállításainak módosítását |} Microsoft Docs
description: Azure Service Fabric állapot-nyilvántartó szereplője KVSActorStateProvider típusú beállításának ismertetése.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: f29754c73db74f02214522a4de15904e65df0e98
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208260"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Reliable Actors--KVSActorStateProvider konfigurálása
Módosíthatja a KVSActorStateProvider használható alapértelmezett konfigurációt a settings.xml fájl, amelyet a Microsoft Visual Studio csomag legfelső szintű konfigurációs mappában lévő megadott szereplő módosításával.

Az Azure Service Fabric-futtatókörnyezet megkeresi az előre definiált nevek a settings.xml fájlban, és a konfigurációs értékeket fel az alapul szolgáló futásidejű összetevők létrehozása során.

> [!NOTE]
> Tegye **nem** törölni vagy módosítani a szakaszneveket, a settings.xml fájlban, a Visual Studio megoldás létrejövő konfigurációt.
> 
> 

## <a name="replicator-security-configuration"></a>A replikáló biztonsági konfiguráció
A replikáció során használt kommunikációs csatornát replikátor biztonsági beállításokkal szolgálnak. Ez azt jelenti, hogy a szolgáltatások nem látható egymás replikációs forgalmat, amely biztosítja, hogy a magas rendelkezésre állási adatok is biztonságos.
Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza, hogy a replikációs biztonságot.

### <a name="section-name"></a>Section name
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikációs konfiguráció
Replikációs konfigurációk konfigurálása a replikátor, amely feladata, hogy az Aktor Állapotszolgáltató állapot nagymértékben megbízható.
Az alapértelmezett konfiguráció a Visual Studio-sablon által generált és elegendőnek kell lennie. Ez a szakasz beszél finomhangolhatják a replikátor rendelkezésre álló további beállításokat.

### <a name="section-name"></a>Section name
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurációs nevek
| Name (Név) | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |másodperc |0.015 |Az időszak, amely a következő művelet elküldése előtt fogadása után a másodlagos vár replikátor biztonsági nyugtázást az elsődleges. Ezt az időközt végrehajtva műveletek küldésének bármely más nyugták küldése a egy választ. |
| ReplicatorEndpoint |– |Nincs alapértelmezett érték – a kötelező paraméter: |IP-cím és az elsődleges és másodlagos replikátor kommunikálni más gyártóitól a replika által használt port beállítása. Ez hivatkoznia kell a TCP-erőforrás végpont a szolgáltatás jegyzékben. Tekintse meg [Service manifest erőforrások](service-fabric-service-manifest-resources.md) további végpont erőforrások definiálása a szolgáltatás jegyzékben. |
| RetryInterval |másodperc |5 |Az időszak, amely után a replikátor újra továbbítja a üzenet Ha azt nem kap egy művelet nyugtázása. |
| MaxReplicationMessageSize |Bájt |50 MB |Replikációs adatok egyetlen üzenetben továbbítható maximális mérete. |
| MaxPrimaryReplicationQueueSize |Műveletek száma |1024 |Az elsődleges várólistában lévő műveletek maximális száma. Egy műveletet a fel nem szabadul, miután az elsődleges replikációs nyugtázást fogad az összes másodlagos gyártóitól. Ez az érték 64 és 2 valamelyik hatványa nagyobbnak kell lennie. |
| MaxSecondaryReplicationQueueSize |Műveletek száma |2048 |A másodlagos várólista műveletek maximális száma. Egy művelet fel nem szabadul állapotában adatmegőrzési keresztül magas rendelkezésre állású elvégzése után. Ez az érték 64 és 2 valamelyik hatványa nagyobbnak kell lennie. |

## <a name="store-configuration"></a>Adattár konfigurálása
Tárolási konfigurációk a helyi tárolójába, amellyel az állapot, a replikált megőrzésére konfigurálására szolgálnak.
Az alapértelmezett konfiguráció a Visual Studio-sablon által generált és elegendőnek kell lennie. Ez a szakasz a helyi tárolójába hangolására rendelkezésre álló további beállításokat beszél.

### <a name="section-name"></a>Section name
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Konfigurációs nevek
| Name (Név) | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Ezredmásodperc |200 |Beállítja a tartós helyi tárolási véglegesítések intervallumát kötegelés maximális. |
| MaxVerPages |Lapok száma |16384 |A maximális oldalszámot verzióját a helyi adatbázis tárolja. Meghatározza, hogy a nyitott tranzakciók maximális száma. |

## <a name="sample-configuration-file"></a>Minta konfigurációs fájlt
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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

