---
title: A KVSActorStateProvider beállításainak módosítása
description: Ismerje meg az Azure Service Fabric kvsactorstateprovider típusú állapotalapú szereplőinek konfigurálását.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609774"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Megbízható szereplők konfigurálása --KVSActorStateProvider
A KVSActorStateProvider alapértelmezett konfigurációját úgy módosíthatja, hogy módosítja a Microsoft Visual Studio csomag gyökérfájljában a megadott aktor Config mappájában létrehozott settings.xml fájlt.

Az Azure Service Fabric futásidejű megkeresi az előre definiált szakaszneveket a settings.xml fájlban, és felhasználja a konfigurációs értékeket az alapul szolgáló futásidejű összetevők létrehozása közben.

> [!NOTE]
> **Ne** törölje vagy módosítsa a következő konfigurációk szakaszneveit a Visual Studio-megoldásban létrehozott settings.xml fájlban.
> 
> 

## <a name="replicator-security-configuration"></a>Replikátor biztonsági konfigurációja
A Replikátor biztonsági konfigurációi a replikáció során használt kommunikációs csatorna védelmére szolgálnak. Ez azt jelenti, hogy a szolgáltatások nem látják egymás replikációs forgalmát, biztosítva, hogy a magas rendelkezésre állású adatok is biztonságosak legyenek.
Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza a replikáció biztonságát.

> [!IMPORTANT]
> Linux-csomópontokon a tanúsítványoknak PEM-formátumúnak kell lenniük. A tanúsítványok Linuxhoz való megkereséséről és konfigurálásáról a [Tanúsítványok konfigurálása Linuxon](./service-fabric-configure-certificates-linux.md)című témakörben olvashat bővebben. 
> 

### <a name="section-name"></a>Szakasz neve
&lt;ActorName&gt;SzolgáltatásReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikátor konfigurációja
A Replikátor-konfigurációk konfigurálják a replikátort, amely felelős az aktor állapotszolgáltató állapotának rendkívül megbízhatóvá tételéért.
Az alapértelmezett konfigurációt a Visual Studio sablon hozza létre, és elegendőnek kell lennie. Ez a szakasz a replikátor finomhangolására rendelkezésre álló további konfigurációkról szól.

### <a name="section-name"></a>Szakasz neve
&lt;ActorName&gt;szolgáltatásReplicatorConfig

### <a name="configuration-names"></a>Konfigurációnevek
| Név | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Másodperc |0.015 |Az az időszak, amely alatt a másodlagos replikátor vár egy művelet fogadása után, mielőtt visszaküldi a nyugtázást az elsődleges. Az ezen időtartamon belül feldolgozott műveletekhez küldendő minden egyéb nyugtázás egyetlen válaszként kerül elküldésre. |
| ReplikátorVégpont |N/A |Nincs alapértelmezett-kötelező paraméter |IP-cím és port, amelyet az elsődleges/másodlagos replikátor a replikakészlet más replikátoraival való kommunikációhoz használ. Ennek egy TCP-erőforrás végpontra kell hivatkoznia a szolgáltatásjegyzékben. Tekintse meg [a szolgáltatás jegyzékfájl-erőforrások,](service-fabric-service-manifest-resources.md) hogy többet tudjon meg a végpont ierőforrások definiálásáról a szolgáltatásjegyzékben. |
| Újrapróbálkozási intervallum |Másodperc |5 |Az az időszak, amely után a replikátor újra küld egy üzenetet, ha nem kap nyugtát egy műveletről. |
| MaxReplicationMessageSize |Bájt |50 MB |Az egy üzenetben továbbítható replikációs adatok maximális mérete. |
| MaxPrimaryReplicationQueueMéret |Műveletek száma |1024 |Az elsődleges várólistában lévő műveletek maximális száma. Egy művelet felszabadítása után az elsődleges replikátor kap egy nyugtázást az összes másodlagos replikátorok. Ennek az értéknek 64-nél nagyobbnak és 2-es teljesítménynek kell lennie. |
| MaxSecondaryReplicationQueueSize |Műveletek száma |2048 |A másodlagos várólistában lévő műveletek maximális száma. Egy művelet felszabadul, miután az állapotát magas rendelkezésre állásúvá tette a perzisztencia révén. Ennek az értéknek 64-nél nagyobbnak és 2-es teljesítménynek kell lennie. |

## <a name="store-configuration"></a>Áruház konfigurációja
Az üzletkonfigurációk a replikált állapot megőrzésére használt helyi tároló konfigurálására szolgálnak.
Az alapértelmezett konfigurációt a Visual Studio sablon hozza létre, és elegendőnek kell lennie. Ez a szakasz a helyi tároló beállításához rendelkezésre álló további konfigurációkról szól.

### <a name="section-name"></a>Szakasz neve
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Konfigurációnevek
| Név | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInEz milliszekundum |Ezredmásodperc |200 |Beállítja a tartós helyi tárolóvéglegesítések maximális kötegelési időközét. |
| MaxVerPages |Oldalak száma |16384 |A helyi tárolóadatbázis verziólapjainak maximális száma. Meghatározza a függőben lévő tranzakciók maximális számát. |

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
A BatchAcknowledgementInterval paraméter szabályozza a replikáció késését. A "0" érték a lehető legalacsonyabb késést eredményez, az átviteli költség rovására (mivel több nyugtázási üzenetet kell küldeni és feldolgozni, amelyek mindegyike kevesebb nyugtázást tartalmaz).
Minél nagyobb a BatchAcknowledgementInterval értéke, annál nagyobb a teljes replikációs átviteli érték, a nagyobb működési késés költségén. Ez közvetlenül fordítja a tranzakció véglegesítések késése.

