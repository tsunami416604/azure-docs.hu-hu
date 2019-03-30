---
title: Az Azure Service Fabric actors KVSActorStateProvider beállításainak módosítása |} A Microsoft Docs
description: Ismerje meg az Azure Service Fabric állapotalapú actors KVSActorStateProvider típusú konfigurálása.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: chackdan
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 8b10ef18fd389179a4f5422783606c45fa2e0d32
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669198"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>A Reliable Actors – KVSActorStateProvider konfigurálása
A Microsoft Visual Studio csomag gyökerében a Config mappában jön létre a megadott aktor settings.xml fájl módosításával módosíthatja a KVSActorStateProvider használható alapértelmezett konfigurációt.

Az Azure Service Fabric-futtatókörnyezet előre definiált nevek a settings.xml fájlban keres, és felhasználja a konfigurációs értékeket az alapul szolgáló futásidejű összetevők létrehozása során.

> [!NOTE]
> Tegye **nem** törölni vagy módosítani a szakaszneveket, a következő konfigurációk jön létre a Visual Studio-megoldásban settings.xml fájlban.
> 
> 

## <a name="replicator-security-configuration"></a>A replikáló biztonsági konfiguráció
A replikáló biztonsági konfigurációk szolgálnak a replikáció során használt kommunikációs csatornát. Ez azt jelenti, hogy a szolgáltatások nem látja a többi összes replikációs forgalmat, amely biztosítja, hogy az adatok magas rendelkezésre állású legyen is biztonságos.
Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza, hogy a replikációs biztonságot.

> [!IMPORTANT]
> A Linux-csomópontokat a tanúsítványok kell PEM-formátumú. További információk megkeresése és tanúsítványok konfigurálása Linux kapcsolatban lásd: [tanúsítványok konfigurálása Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Szakasz neve
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikációs konfiguráció
A replikáló konfigurációk a replikátor, amely feladata, hogy az Aktor Riasztásiállapot-szolgáltató állapota magas megbízhatóságú konfigurálása.
Az alapértelmezett konfiguráció a Visual Studio-sablon által létrehozott és elegendőnek kell lennie. Ez a szakasz ismerteti a replikátor finomhangolása elérhető további konfigurációs.

### <a name="section-name"></a>Szakasz neve
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Konfigurációs nevek
| Name (Név) | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |másodperc |0.015 |Adott időszakban, amelyhez a replikátor, miután kapott egy művelet, mielőtt elküldené a másodlagos vár az elsődleges biztonsági nyugtázást. Bármely más nyugtázás a intervallumon belül feldolgozott műveletek kell elküldeni, egy választ kapnak. |
| ReplicatorEndpoint |– |Alapértelmezett – kötelező paraméter |IP-cím és az elsődleges és másodlagos replikátor kommunikálni más gyártóitól a replika által használt port megadása A TCP-erőforrás végpontjának a szolgáltatásjegyzékben ez kell hivatkoznia. Tekintse meg [szolgáltatásjegyzéki erőforrások](service-fabric-service-manifest-resources.md) további végpont erőforrások meghatározása szolgáltatásjegyzékben kapcsolatban. |
| retryInterval |másodperc |5 |Az időszak, amely után a replikátor újra továbbítja az üzenet Ha nem kapja meg egy művelet nyugtázása. |
| MaxReplicationMessageSize |Bájt |50 MB |Egy üzenet továbbítható replikációs adatok maximális mérete. |
| MaxPrimaryReplicationQueueSize |Műveletek száma |1024 |Az elsődleges üzenetsor-műveletek maximális száma. Egy műveletet a fel nem szabadul, miután az elsődleges replikátor nyugtázást fogad az összes másodlagos gyártóitól. Ez az érték nagyobb, mint 64 és 2 hatványának kell lennie. |
| MaxSecondaryReplicationQueueSize |Műveletek száma |2048 |A másodlagos üzenetsor-műveletek maximális száma. Egy művelet fel nem szabadul, az állapot megőrzése révén magas rendelkezésre állású elvégzése után. Ez az érték nagyobb, mint 64 és 2 hatványának kell lennie. |

## <a name="store-configuration"></a>Store konfiguráció
Store konfigurációk segítségével konfigurálhatja a helyi tároló, amely továbbra is fennáll az állapot, az épp replikált szolgál.
Az alapértelmezett konfiguráció a Visual Studio-sablon által létrehozott és elegendőnek kell lennie. Ez a szakasz ismerteti a helyi tároló finomhangolása elérhető további konfigurációs.

### <a name="section-name"></a>Szakasz neve
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Konfigurációs nevek
| Name (Név) | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Ezredmásodperc |200 |A maximálisan engedélyezett kötegelési időköz tartós helyi tároló véglegesítések állítja. |
| MaxVerPages |Oldalak száma |16384 |A helyi verziója oldalak maximális számát az adatbázisban tárolja. Meghatározza, hogy a szálankénti függőben lévő tranzakciók maximális számát. |

## <a name="sample-configuration-file"></a>Minta konfigurációs fájl
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
A BatchAcknowledgementInterval a paraméterrel állítható be replikáció késése. "0" értéket eredményez a lehető legkisebb késleltetést, átviteli sebesség cserébe (a nyugtázó további üzenetek küldhetők és feldolgozott kevesebb nyugtázás tartalmazó kell).
BatchAcknowledgementInterval minél nagyobb az értéke, annál az általános replikációs átviteli sebességet, cserébe magasabb. műveleti késés. Ez közvetlenül a rendszer lefordítja arra a tranzakció-véglegesítések késését.

