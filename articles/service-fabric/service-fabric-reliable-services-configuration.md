---
title: Az Azure Service Fabric megbízható szolgáltatásainak konfigurálása
description: Ismerje meg az állapotalapú megbízható szolgáltatások konfigurálását egy Azure Service Fabric-alkalmazásban globálisan és egyetlen szolgáltatáshoz.
author: sumukhs
ms.topic: conceptual
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: 9743213394b59af701b25b8be9dd48cf4310b499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645514"
---
# <a name="configure-stateful-reliable-services"></a>Állapotalapú megbízható szolgáltatások konfigurálása
A megbízható szolgáltatások hoz két konfigurációs beállításkészlet létezik. Az egyik készlet globális a fürt összes megbízható szolgáltatásához, míg a másik egy adott megbízható szolgáltatásra jellemző.

## <a name="global-configuration"></a>Globális konfiguráció
A globális megbízható szolgáltatáskonfiguráció a fürt jegyzékfájljában van megadva a KtlLogger szakaszban. Ez lehetővé teszi a konfiguráció a megosztott napló helyét és méretét, valamint a globális memória korlátok által használt naplózó. A fürtjegyzék egyetlen XML-fájl, amely a fürt összes csomópontjára és szolgáltatására vonatkozó beállításokat és konfigurációkat tartalmazza. A fájl neve általában ClusterManifest.xml. A Get-ServiceFabricClusterManifest powershell paranccsal láthatja a fürt fürtjegyzékét.

### <a name="configuration-names"></a>Konfigurációnevek
| Név | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobyte |8388608 |Minimális an- kb a kernel módban a naplózó írási puffermemória-készletéhez. Ez a memóriakészlet az állapotadatok lemezre írás előtti gyorsítótárazására szolgál. |
| WriteBufferMemoryPoolMaximuminKB |Kilobyte |Korlátlan |Az a maximális méret, amelyre a naplózó írási puffermemória-készlete növekedhet. |
| SharedLogId |GUID |"" |Egy egyedi GUID-ot ad meg, amelya fürt összes olyan csomópontján lévő összes megbízható szolgáltatás által használt alapértelmezett megosztott megosztott naplófájl azonosítására szolgál, amely nem adja meg a SharedLogId azonosítót a szolgáltatásspecifikus konfigurációban. Ha a SharedLogId meg van adva, akkor a SharedLogPath-ot is meg kell adni. |
| SharedLogPath |Teljesen minősített elérési út neve |"" |Megadja azt a teljesen minősített elérési utat, ahol a fürt összes olyan csomópontján lévő összes megbízható szolgáltatás által használt megosztott naplófájl, amely nem adja meg a SharedLogPath-ot a szolgáltatásspecifikus konfigurációban. Ha azonban a SharedLogPath meg van adva, akkor a SharedLogId azonosítót is meg kell adni. |
| SharedLogSizeInMB |Megabájt |8192 |Megadja a megosztott napló számára statikusan lefoglalandó LEMEZTERÜLET számát. Az értéknek legalább 2048-nak kell lennie. |

Az Azure ARM vagy a helyszíni JSON-sablon, az alábbi példa bemutatja, hogyan módosíthatja a megosztott tranzakciós napló, amely jön létre, hogy az állapotalapú szolgáltatások megbízható gyűjtemények.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Példa a helyi fejlesztői fürt jegyzékfájlszakaszára
Ha ezt a helyi fejlesztői környezetben szeretné módosítani, módosítania kell a helyi clustermanifest.xml fájlt.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Megjegyzések
A naplózó rendelkezik egy globális memóriakészlet teljében nem lapozható kernel memória, amely elérhető az összes megbízható szolgáltatás egy csomóponton a gyorsítótárazás állapotadatok, mielőtt a dedikált napló társított megbízható szolgáltatás replika. A készlet méretét a WriteBufferMemoryPoolMinimumInKB és a WriteBufferMemoryPoolMaximumInKB beállítások szabályozzák. A WriteBufferMemoryPoolMinimumInKB a memóriakészlet kezdeti méretét és a memóriakészlet zsugorításának legalacsonyabb méretét is megadja. A WriteBufferMemoryPoolMaximumInKB az a legnagyobb méret, amelyre a memóriakészlet növekedhet. Minden megnyitott megbízható szolgáltatásreplika növelheti a memóriakészlet méretét a WriteBufferMemoryPoolMaximumInKB-ig meghatározott rendszerrel. Ha a rendelkezésre állónál nagyobb igény van a memóriakészletből származó memóriára, a memóriakérelmek a memória rendelkezésre állásáig késnek. Ezért ha az írási puffermemória-készlet túl kicsi egy adott konfigurációhoz, akkor a teljesítmény csökkenhet.

A SharedLogId és a SharedLogPath-beállítások mindig együtt vannak használva a fürt összes csomópontjának GUID azonosítójának és helyének meghatározásához. Az alapértelmezett megosztott napló minden olyan megbízható szolgáltatáshoz használatos, amely nem adja meg az adott szolgáltatás settings.xml fájljának beállításait. A legjobb teljesítmény érdekében a megosztott naplófájlokat olyan lemezekre kell helyezni, amelyeket kizárólag a megosztott naplófájlhoz használnak a versengés csökkentése érdekében.

A SharedLogSizeInMB azt a lemezterületet adja meg, amelyet az összes csomóponton az alapértelmezett megosztott naplószámára előre le kell foglalni.  A SharedLogId és a SharedLogPath paramétert nem kell megadni a SharedLogSizeInMB paraméter megadása érdekében.

## <a name="service-specific-configuration"></a>Szolgáltatásspecifikus konfiguráció
Módosíthatja az állapotalapú Reliable Services alapértelmezett konfigurációit a konfigurációs csomag (Config) vagy a szolgáltatás implementációjának (kód) használatával.

* **Config** - A konfiguráció a konfigurációs csomagon keresztül a Microsoft Visual Studio csomag gyökérfájljában az alkalmazás egyes szolgáltatásaihoz létrehozott Settings.xml fájl módosításával történik.
* **Kód** - A kódon keresztüli konfiguráció egy ReliableStateManager használatával érhető el egy ReliableStateManagerConfiguration objektum használatával a megfelelő beállításokkal.

Alapértelmezés szerint az Azure Service Fabric futásidejű megkeresi az előre definiált szakaszneveket a Settings.xml fájlban, és felhasználja a konfigurációs értékeket az alapul szolgáló futásidejű összetevők létrehozása közben.

> [!NOTE]
> **Ne** törölje a következő konfigurációk szakasznevét a Visual Studio-megoldásban létrehozott Settings.xml fájlban, kivéve, ha a szolgáltatást kódsegítségével kívánja konfigurálni.
> A config csomag vagy szakasznevek átnevezéséhez kódmódosításra van szükség a ReliableStateManager konfigurálásakor.
> 
> 

### <a name="replicator-security-configuration"></a>Replikátor biztonsági konfigurációja
A Replikátor biztonsági konfigurációi a replikáció során használt kommunikációs csatorna védelmére szolgálnak. Ez azt jelenti, hogy a szolgáltatások nem fogják látni egymás replikációs forgalmát, biztosítva, hogy a magas rendelkezésre állású adatok is biztonságosak legyenek. Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza a replikáció biztonságát.

> [!IMPORTANT]
> Linux-csomópontokon a tanúsítványoknak PEM-formátumúnak kell lenniük. A tanúsítványok Linuxhoz való megkereséséről és konfigurálásáról a [Tanúsítványok konfigurálása Linuxon](./service-fabric-configure-certificates-linux.md)című témakörben olvashat bővebben. 
> 
> 

### <a name="default-section-name"></a>Alapértelmezett szakasznév
ReplikátorSecurityConfig

> [!NOTE]
> A szakasz névének módosításához felülbírálja a replikátorSecuritySectionName paramétert a ReliableStateManagerConfiguration konstruktorra, amikor létrehozza a ReliableStateManager szolgáltatást ehhez a szolgáltatáshoz.
> 
> 

### <a name="replicator-configuration"></a>Replikátor konfigurációja
Replikátor konfigurációk konfigurálja a replikátor, amely felelős azért, hogy az állapotalapú megbízható szolgáltatás állapota rendkívül megbízható replikálása és az állapot helyi szinten való megőrzése.
Az alapértelmezett konfigurációt a Visual Studio sablon hozza létre, és elegendőnek kell lennie. Ez a szakasz a replikátor finomhangolására rendelkezésre álló további konfigurációkról szól.

### <a name="default-section-name"></a>Alapértelmezett szakasznév
ReplikátorConfig

> [!NOTE]
> A szakasz névének módosításához felülbírálja a replikátorSettingsSectionName paramétert a ReliableStateManagerConfiguration konstruktorra, amikor létrehozza a ReliableStateManager szolgáltatást ehhez a szolgáltatáshoz.
> 
> 

### <a name="configuration-names"></a>Konfigurációnevek
| Név | Unit (Egység) | Alapértelmezett érték | Megjegyzések |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Másodperc |0.015 |Az az időszak, amely alatt a másodlagos replikátor vár egy művelet fogadása után, mielőtt visszaküldi a nyugtázást az elsődleges. Az ezen időtartamon belül feldolgozott műveletekhez küldendő minden egyéb nyugtázás egyetlen válaszként kerül elküldésre. |
| ReplikátorVégpont |N/A |Nincs alapértelmezett-kötelező paraméter |IP-cím és port, amelyet az elsődleges/másodlagos replikátor a replikakészlet más replikátoraival való kommunikációhoz használ. Ennek egy TCP-erőforrás végpontra kell hivatkoznia a szolgáltatásjegyzékben. Tekintse meg [a szolgáltatás jegyzékfájl-erőforrások,](service-fabric-service-manifest-resources.md) ha többet szeretne megtudni a végponti erőforrások definiálása a szolgáltatás jegyzékfájlban. |
| MaxPrimaryReplicationQueueMéret |Műveletek száma |8192 |Az elsődleges várólistában lévő műveletek maximális száma. Egy művelet felszabadítása után az elsődleges replikátor kap egy nyugtázást az összes másodlagos replikátorok. Ennek az értéknek 64-nél nagyobbnak és 2-es teljesítménynek kell lennie. |
| MaxSecondaryReplicationQueueSize |Műveletek száma |16384 |A másodlagos várólistában lévő műveletek maximális száma. Egy művelet felszabadul, miután az állapotát magas rendelkezésre állásúvá tette a perzisztencia révén. Ennek az értéknek 64-nél nagyobbnak és 2-es teljesítménynek kell lennie. |
| EllenőrzőpontThresholdInMB |MB |50 |Azon naplófájl-terület nagy része, amely után az állapot ellenőrzőpont. |
| MaxRecordSizeInKB |KB |1024 |A replikátor által a naplóba beírt legnagyobb rekordméret. Ennek az értéknek 4 többszörösének és 16-nál nagyobbnak kell lennie. |
| MinLogSizeInMB |MB |0 (rendszer határozza meg) |A tranzakciós napló minimális mérete. A napló nem csonkolható a beállítás alatti méretre. A 0 azt jelzi, hogy a replikátor határozza meg a minimális naplóméretet. Ennek az értéknek a növelésével csökken a részleges másolatok és a növekményes biztonsági mentések készítésének lehetősége, mivel a megfelelő naplórekordok csonkolásának esélye csökken. |
| CsonkolásI Küszöbtényező |Tényező |2 |Azt határozza meg, hogy a napló milyen méretben aktiválódik a csonkolás. A csonkolási küszöbértéket a MinLogSizeInMB és a TruncationThresholdFactor szorzata határozza meg. A TruncationThresholdFactor értékének 1-nél nagyobbnak kell lennie. A MinLogSizeInMB * TruncationThresholdFactor értékének kisebbnek kell lennie, mint a MaxStreamSizeInMB. |
| Szabályozási küszöbérték-tényező |Tényező |4 |Azt határozza meg, hogy a napló milyen méretű, a replika elkezdszabályozása. A sávszélesség-szabályozási küszöbértéket (MB-ban) a Max((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)határozza meg. A sávszélesség-szabályozási küszöbértéknek (MB-ban) nagyobbnak kell lennie, mint a csonkolási küszöbérték (MB-ban). A csonkolási küszöbértéknek (MB-ban) kisebbnek kell lennie, mint a MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |A biztonsági mentési naplók maximális mérete (MB-ban) egy adott biztonságimentési naplóláncban. A növekményes biztonsági mentési kérelmek sikertelenek lesznek, ha a növekményes biztonsági mentés létrehoz egy biztonsági mentési naplót, amely a halmozott biztonsági mentési naplókat okozza, mivel a megfelelő teljes biztonsági mentés nagyobb lesz, mint ez a méret. Ilyen esetekben a felhasználónak teljes biztonsági másolatot kell készítenie. |
| SharedLogId |GUID |"" |A kópiával használt megosztott naplófájl azonosítására szolgáló egyedi GUID-ot adja meg. A szolgáltatások általában nem használhatják ezt a beállítást. Ha azonban a SharedLogId meg van adva, akkor a SharedLogPath-ot is meg kell adni. |
| SharedLogPath |Teljesen minősített elérési út neve |"" |Megadja azt a teljesen minősített elérési utat, ahová a kópiához megosztott naplófájl létrejön. A szolgáltatások általában nem használhatják ezt a beállítást. Ha azonban a SharedLogPath meg van adva, akkor a SharedLogId azonosítót is meg kell adni. |
| SlowApiMonitoringDuration |Másodperc |300 |Beállítja a felügyelt API-hívások figyelési időközét. Példa: a felhasználó biztonsági visszahívási funkciót adott meg. Az időköz lejárta után a rendszer figyelmeztető állapotjelentést küld az állapotkezelőnek. |
| LogTruncationIntervalSeconds |Másodperc |0 |Konfigurálható időköz, amelyen a naplócsonkolás minden kópián elindul. Arra szolgál, hogy a napló is csonkolt idő alapján, hanem csak napló mérete. Ez a beállítás a törölt bejegyzések megbízható szótárban való törlését is kényszeríti. Ezért a törölt elemek időben történő törlésének biztosítására használható. |
| EnableStableReads (EnableStableReads) |Logikai |False (Hamis) |A stabil olvasásengedélyezése korlátozza a másodlagos replikákat a kvórum-kosztott értékekre. |

### <a name="sample-configuration-via-code"></a>Mintakonfiguráció kódon keresztül
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Minta konfigurációs fájl
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
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


### <a name="remarks"></a>Megjegyzések
A BatchAcknowledgementInterval szabályozza a replikáció késését. A "0" érték a lehető legalacsonyabb késést eredményez, az átviteli költség rovására (mivel több nyugtázási üzenetet kell küldeni és feldolgozni, amelyek mindegyike kevesebb nyugtázást tartalmaz).
Minél nagyobb a BatchAcknowledgementInterval értéke, annál nagyobb a teljes replikációs átviteli érték, a nagyobb működési késés költségén. Ez közvetlenül fordítja a tranzakció véglegesítések késése.

A CheckpointThresholdInMB értéke azt a lemezterületet szabályozza, amelyet a replikátor a kópia dedikált naplófájljában tárolhat az állapotadatok tárolására. Ha ezt az alapértelmezettnél nagyobb értékre növeli, az gyorsabb újrakonfigurálási időt eredményezhet, amikor új kópiát ad hozzá a készlethez. Ez annak köszönhető, hogy a részleges állapotátvitel a naplóban több művelet előzményeinek rendelkezésre állása miatt történik. Ez potenciálisan növelheti a replika helyreállítási idejét egy összeomlás után.

A MaxRecordSizeInKB beállítás a replikátor által a naplófájlba írható rekord maximális méretét határozza meg. A legtöbb esetben az alapértelmezett 1024 KB-os rekordméret optimális. Ha azonban a szolgáltatás nagyobb adatelemeket okoz az állapotadatok részének, akkor szükség lehet ennek az értéknek a növelésére. Kevés előnye van annak, hogy a MaxRecordSizeInKB kisebb, mint 1024, mivel a kisebb rekordok csak a kisebb rekordhoz szükséges helyet használják. Arra számítunk, hogy ezt az értéket csak ritka esetekben kell megváltoztatni.

A SharedLogId és a SharedLogPath-beállítások mindig együtt vannak használva, hogy a szolgáltatás a csomópont alapértelmezett megosztott naplójától különálló megosztott naplót használjon. A hatékonyság érdekében a lehető legtöbb szolgáltatásnak meg kell adnia ugyanazt a megosztott naplót. A megosztott naplófájlokat olyan lemezekre kell helyezni, amelyek et kizárólag a megosztott naplófájlhoz használják a fejmozgással kapcsolatos versengés csökkentése érdekében. Arra számítunk, hogy ezt az értéket csak ritka esetekben kell megváltoztatni.

## <a name="next-steps"></a>További lépések
* [A Service Fabric-alkalmazás hibakeresése a Visual Studióban](service-fabric-debugging-your-application.md)
* [Fejlesztői referencia a megbízható szolgáltatásokhoz](https://msdn.microsoft.com/library/azure/dn706529.aspx)

