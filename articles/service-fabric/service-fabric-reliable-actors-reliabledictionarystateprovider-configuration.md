---
title: ReliableDictionaryActorStateProvider beállításainak módosítása
description: Ismerje meg az Azure Service Fabric állapotalapú szereplők konfigurálása típusú ReliableDictionaryActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609740"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Megbízható szereplők konfigurálása - ReliableDictionaryActorStateProvider
A ReliableDictionaryActorStateProvider alapértelmezett konfigurációját módosíthatja a Visual Studio csomaggyökérében a megadott aktor Config mappájában létrehozott settings.xml fájl módosításával.

Az Azure Service Fabric futásidejű megkeresi az előre definiált szakaszneveket a settings.xml fájlban, és felhasználja a konfigurációs értékeket az alapul szolgáló futásidejű összetevők létrehozása közben.

> [!NOTE]
> **Ne** törölje vagy módosítsa a következő konfigurációk szakaszneveit a Visual Studio-megoldásban létrehozott settings.xml fájlban.
> 
> 

Vannak olyan globális beállítások is, amelyek befolyásolják a ReliableDictionaryActorStateProvider konfigurációját.

## <a name="global-configuration"></a>Globális konfiguráció
A globális konfiguráció a fürt jegyzékfájljában van megadva a KtlLogger szakaszban. Ez lehetővé teszi a konfiguráció a megosztott napló helyét és méretét, valamint a globális memória korlátok által használt naplózó. Vegye figyelembe, hogy a fürtjegyzék változásai hatással vannak a ReliableDictionaryActorStateProvider szolgáltatást és megbízható állapotalapú szolgáltatásokat használó összes szolgáltatásra.

A fürtjegyzék egyetlen XML-fájl, amely a fürt összes csomópontjára és szolgáltatására vonatkozó beállításokat és konfigurációkat tartalmazza. A fájl neve általában ClusterManifest.xml. A Get-ServiceFabricClusterManifest powershell paranccsal láthatja a fürt fürtjegyzékét.

### <a name="configuration-names"></a>Konfigurációnevek
| Név | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobyte |8388608 |Minimális an- kb a kernel módban a naplózó írási puffermemória-készletéhez. Ez a memóriakészlet az állapotadatok lemezre írás előtti gyorsítótárazására szolgál. |
| WriteBufferMemoryPoolMaximuminKB |Kilobyte |Korlátlan |Az a maximális méret, amelyre a naplózó írási puffermemória-készlete növekedhet. |
| SharedLogId |GUID |"" |Egy egyedi GUID-ot ad meg, amelya fürt összes olyan csomópontján lévő összes megbízható szolgáltatás által használt alapértelmezett megosztott megosztott naplófájl azonosítására szolgál, amely nem adja meg a SharedLogId azonosítót a szolgáltatásspecifikus konfigurációban. Ha a SharedLogId meg van adva, akkor a SharedLogPath-ot is meg kell adni. |
| SharedLogPath |Teljesen minősített elérési út neve |"" |Megadja azt a teljesen minősített elérési utat, ahol a fürt összes olyan csomópontján lévő összes megbízható szolgáltatás által használt megosztott naplófájl, amely nem adja meg a SharedLogPath-ot a szolgáltatásspecifikus konfigurációban. Ha azonban a SharedLogPath meg van adva, akkor a SharedLogId azonosítót is meg kell adni. |
| SharedLogSizeInMB |Megabájt |8192 |Megadja a megosztott napló számára statikusan lefoglalandó LEMEZTERÜLET számát. Az értéknek legalább 2048-nak kell lennie. |

### <a name="sample-cluster-manifest-section"></a>Mintafürt jegyzékfájlja
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
A naplózó rendelkezik egy globális memóriakészlet teljében nem lapozható kernel memória, amely elérhető az összes megbízható szolgáltatás egy csomóponton a gyorsítótárazás állapotadatok, mielőtt a dedikált napló társított megbízható szolgáltatás replika. A készlet méretét a WriteBufferMemoryPoolMinimumInKB és a WriteBufferMemoryPoolMaximumInKB beállítások szabályozzák. A WriteBufferMemoryPoolMinimumInKB a memóriakészlet kezdeti méretét és a memóriakészlet zsugorításának legalacsonyabb méretét is megadja. A WriteBufferMemoryPoolMaximumInKB az a legnagyobb méret, amelyre a memóriakészlet növekedhet. Minden megnyitott megbízható szolgáltatásreplika növelheti a memóriakészlet méretét a WriteBufferMemoryPoolMaximumInKB-ig meghatározott rendszerrel. Ha a rendelkezésre állónál nagyobb igény van a memóriakészletből származó memóriára, a memóriakérelmek a memória rendelkezésre állásáig késnek. Ezért ha az írási puffermemória-készlet túl kicsi egy adott konfigurációhoz, akkor a teljesítmény csökkenhet.

A SharedLogId és a SharedLogPath-beállítások mindig együtt vannak használva a fürt összes csomópontjának GUID azonosítójának és helyének meghatározásához. Az alapértelmezett megosztott napló minden olyan megbízható szolgáltatáshoz használatos, amely nem adja meg az adott szolgáltatás settings.xml fájljának beállításait. A legjobb teljesítmény érdekében a megosztott naplófájlokat olyan lemezekre kell helyezni, amelyeket kizárólag a megosztott naplófájlhoz használnak a versengés csökkentése érdekében.

A SharedLogSizeInMB azt a lemezterületet adja meg, amelyet az összes csomóponton az alapértelmezett megosztott naplószámára előre le kell foglalni.  A SharedLogId és a SharedLogPath paramétert nem kell megadni a SharedLogSizeInMB paraméter megadása érdekében.

## <a name="replicator-security-configuration"></a>Replikátor biztonsági konfigurációja
A Replikátor biztonsági konfigurációi a replikáció során használt kommunikációs csatorna védelmére szolgálnak. Ez azt jelenti, hogy a szolgáltatások nem látják egymás replikációs forgalmát, biztosítva a magas rendelkezésre állású adatok at is biztonságos.
Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza a replikáció biztonságát.

> [!IMPORTANT]
> Linux-csomópontokon a tanúsítványoknak PEM-formátumúnak kell lenniük. A tanúsítványok Linuxhoz való megkereséséről és konfigurálásáról a [Tanúsítványok konfigurálása Linuxon](./service-fabric-configure-certificates-linux.md)című témakörben olvashat bővebben. 
> 

### <a name="section-name"></a>Szakasz neve
&lt;ActorName&gt;SzolgáltatásReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikátor konfigurációja
A replikátor konfigurációk konfigurálására szolgál a replikátor, amely felelős azért, hogy az aktor állapotszolgáltató állapota rendkívül megbízható replikálása és az állapot helyi szinten való megőrzése.
Az alapértelmezett konfigurációt a Visual Studio sablon hozza létre, és elegendőnek kell lennie. Ez a szakasz a replikátor finomhangolására rendelkezésre álló további konfigurációkról szól.

### <a name="section-name"></a>Szakasz neve
&lt;ActorName&gt;szolgáltatásReplicatorConfig

### <a name="configuration-names"></a>Konfigurációnevek
| Név | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Másodperc |0.015 |Az az időszak, amely alatt a másodlagos replikátor vár egy művelet fogadása után, mielőtt visszaküldi a nyugtázást az elsődleges. Az ezen időtartamon belül feldolgozott műveletekhez küldendő minden egyéb nyugtázás egyetlen válaszként kerül elküldésre. |
| ReplikátorVégpont |N/A |Nincs alapértelmezett-kötelező paraméter |IP-cím és port, amelyet az elsődleges/másodlagos replikátor a replikakészlet más replikátoraival való kommunikációhoz használ. Ennek egy TCP-erőforrás végpontra kell hivatkoznia a szolgáltatásjegyzékben. Tekintse meg [a szolgáltatás jegyzékfájl-erőforrások,](service-fabric-service-manifest-resources.md) hogy többet tudjon meg a végpont ierőforrások a szolgáltatás jegyzékfájlban. |
| MaxReplicationMessageSize |Bájt |50 MB |Az egy üzenetben továbbítható replikációs adatok maximális mérete. |
| MaxPrimaryReplicationQueueMéret |Műveletek száma |8192 |Az elsődleges várólistában lévő műveletek maximális száma. Egy művelet felszabadítása után az elsődleges replikátor kap egy nyugtázást az összes másodlagos replikátorok. Ennek az értéknek 64-nél nagyobbnak és 2-es teljesítménynek kell lennie. |
| MaxSecondaryReplicationQueueSize |Műveletek száma |16384 |A másodlagos várólistában lévő műveletek maximális száma. Egy művelet felszabadul, miután az állapotát magas rendelkezésre állásúvá tette a perzisztencia révén. Ennek az értéknek 64-nél nagyobbnak és 2-es teljesítménynek kell lennie. |
| EllenőrzőpontThresholdInMB |MB |200 |Azon naplófájl-terület nagy része, amely után az állapot ellenőrzőpont. |
| MaxRecordSizeInKB |KB |1024 |A replikátor által a naplóba beírt legnagyobb rekordméret. Ennek az értéknek 4 többszörösének és 16-nál nagyobbnak kell lennie. |
| OptimizeLogForLowerDiskUsage |Logikai |igaz |Ha igaz, a napló úgy van konfigurálva, hogy a replika dedikált naplófájlja NTFS ritka fájl használatával jön létre. Ez csökkenti a fájl tényleges lemezterület-felhasználását. Ha hamis, a fájl rögzített foglalásokkal jön létre, amelyek a legjobb írási teljesítményt biztosítják. |
| SharedLogId |Guid |"" |A kópiával használt megosztott naplófájl azonosítására szolgáló egyedi guid azonosítót ad meg. A szolgáltatások általában nem használhatják ezt a beállítást. Ha azonban a SharedLogId meg van adva, akkor a SharedLogPath-ot is meg kell adni. |
| SharedLogPath |Teljesen minősített elérési út neve |"" |Megadja azt a teljesen minősített elérési utat, ahová a kópiához megosztott naplófájl létrejön. A szolgáltatások általában nem használhatják ezt a beállítást. Ha azonban a SharedLogPath meg van adva, akkor a SharedLogId azonosítót is meg kell adni. |

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
A BatchAcknowledgementInterval paraméter szabályozza a replikáció késését. A "0" érték a lehető legalacsonyabb késést eredményez, az átviteli költség rovására (mivel több nyugtázási üzenetet kell küldeni és feldolgozni, amelyek mindegyike kevesebb nyugtázást tartalmaz).
Minél nagyobb a BatchAcknowledgementInterval értéke, annál nagyobb a teljes replikációs átviteli érték, a nagyobb működési késés költségén. Ez közvetlenül fordítja a tranzakció véglegesítések késése.

A CheckpointThresholdInMB paraméter azt szabályozza, hogy a replikátor mekkora lemezterületet tárolhat a kópia dedikált naplófájljában. Ha ezt az alapértelmezettnél nagyobb értékre növeli, az gyorsabb újrakonfigurálási időt eredményezhet, amikor új kópiát ad hozzá a készlethez. Ez annak köszönhető, hogy a részleges állapotátvitel a naplóban több művelet előzményeinek rendelkezésre állása miatt történik. Ez potenciálisan növelheti a replika helyreállítási idejét egy összeomlás után.

Ha az OptimizeForLowerDiskUsage értéket igaz értékre állítja, a naplófájl-terület túllesz építve, így az aktív kópiák több állapotinformációt tárolhatnak a naplófájljaikban, míg az inaktív kópiák kevesebb lemezterületet fognak használni. Ez lehetővé teszi további replikák üzemeltetését egy csomóponton. Ha az OptimizeForLowerDiskUsage értéket hamisra állítja, az állapotadatok gyorsabban bekerülnek a naplófájlokba.

A MaxRecordSizeInKB beállítás a replikátor által a naplófájlba írható rekord maximális méretét határozza meg. A legtöbb esetben az alapértelmezett 1024 KB-os rekordméret optimális. Ha azonban a szolgáltatás nagyobb adatelemeket okoz az állapotadatok részének, akkor szükség lehet ennek az értéknek a növelésére. Kevés előnye van annak, hogy a MaxRecordSizeInKB kisebb, mint 1024, mivel a kisebb rekordok csak a kisebb rekordhoz szükséges helyet használják. Arra számítunk, hogy ezt az értéket csak ritka esetekben kell megváltoztatni.

A SharedLogId és a SharedLogPath-beállítások mindig együtt vannak használva, hogy a szolgáltatás a csomópont alapértelmezett megosztott naplójától különálló megosztott naplót használjon. A hatékonyság érdekében a lehető legtöbb szolgáltatásnak meg kell adnia ugyanazt a megosztott naplót. A megosztott naplófájlokat olyan lemezekre kell helyezni, amelyeket kizárólag a megosztott naplófájlhoz használnak a fejmozgással kapcsolatos versengés csökkentése érdekében. Arra számítunk, hogy ezeket az értékeket csak ritka esetekben kell megváltoztatni.

