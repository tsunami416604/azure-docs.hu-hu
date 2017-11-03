---
title: "Az Azure Service Bus földrajzi-vész-helyreállítási |} Microsoft Docs"
description: "Feladatátvételi földrajzi régió használatáról, és hajtsa végre a vész-helyreállítási Azure Service Bus"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: sethm
ms.openlocfilehash: 49f2992245d694f85b7b1f1c34339f1445c9d699
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="azure-service-bus-geo-disaster-recovery-preview"></a>Az Azure Service Bus földrajzi-vész-helyreállítási (előzetes verzió)

Regionális adatközpontok leállás következik be, ha nagyon fontos az adatok feldolgozásához továbbra is működik egy másik régióban vagy datacenter. Ilyen *földrajzi-vész-helyreállítási* és *georeplikáció* bármely vállalati fontos funkciókat. Az Azure Service Bus földrajzi-vész-helyreállítási és a georeplikáció, a névterek szintjén is támogatja. 

A földrajzi-vész-helyreállítási preview érhető el jelenleg csak a két régióban (**északi középső Régiójában** és **déli középső Régiójában)**.

## <a name="outages-and-disasters"></a>Kimaradások és vészhelyzetek

A [ajánlott eljárások az alkalmazások a Service Bus kimaradások és vészhelyzetek szigetelő](service-bus-outages-disasters.md) cikk fontos megjegyezni, amely "kimaradások" és "katasztrófák," közötti különbséget tesz. Egy *szolgáltatáskimaradás* az Azure Service Bus elérhetetlenség, és hatással lehet a szolgáltatás, például egy üzenetküldési tárolóban, vagy akár a teljes adatközpont néhány összetevője. Azonban a probléma kijavítása után a Service Bus ismét elérhetővé válik. Nem tervezett kimaradás általában nem okoznak üzenetek vagy egyéb adatok elvesztését. Egy példa ilyen kimaradás lehet áramkimaradás az adatközpontban.

A *katasztrófa* van definiálva, a Service Bus állandó, vagy hosszabb távú megszűnését [méretezési egység](service-bus-architecture.md#service-bus-scale-units) vagy datacenter. A datacenter is előfordulhat, hogy nem ismét elérhetővé válik, vagy lehet a óráig vagy napig. Ilyen katasztrófák példák tűz, elárasztás vagy földrengés. Egy olyan vészhelyzet esetén váló állandó egyes üzenetek vagy egyéb adatok elvesztését okozhatja. Azonban a legtöbb esetben kell adatvesztés nélküli, és üzenetek helyreállíthatók legyenek, amikor az Adatközpont készítsen biztonsági másolatot.

A földrajzi-vész-helyreállítási funkció az Azure Service Bus egy vész-helyreállítási megoldást. A fogalmakat és a jelen cikkben ismertetett munkafolyamat alkalmazza a vészhelyreállítási forgatókönyveket, és nem átmeneti, illetve ideiglenes kimaradások esetén.  

## <a name="basic-concepts-and-terms"></a>Alapvető fogalmakat és szakkifejezéseket tartalmazza

A vész-helyreállítási szolgáltatás metaadat vész-helyreállítási valósítja meg, és elsődleges és egy másodlagos vész-helyreállítási névterek támaszkodik. Vegye figyelembe, hogy a földrajzi-vész-helyreállítási funkció elérhető [prémium névterekben](service-bus-premium-messaging.md) csak. Nem kell a beállítások kapcsolati karakterlánc módosításait, a kapcsolat alias keresztül.

Ez a cikk a következő kifejezéseket használjuk:

-  *Alias*: A fő kapcsolati karakterláncot.

-  *Elsődleges és másodlagos névtér*: az alias vannak rendelve az áttelepítendő névterek ismerteti. Az elsődleges "aktív", illetve üzeneteket fogad, a másodlagos "passzív", és nem fogadhat üzeneteket. A metaadatok között is a Szinkronizáló, így is képes zökkenőmentesen fogadja az üzeneteket alkalmazás kód módosítás nélkül.

-  *Metaadatok*: az Azure Service Bus objektumok ábrázolása. Jelenleg csak támogatjuk a metaadatok.

-  *Feladatátvételi*: a másodlagos névtér aktiválása folyamatán. Üzenetek lekérni a korábban elsődleges névtér, amint elérhetővé válik újra, és törölje a névteret. Egy másik feladatátvevő létrehozásához ad hozzá egy új másodlagos névtér létesített kapcsolatot. Ha azt szeretné, a korábbi elsődleges névtér újból egy feladatátvétel után, el kell távolítani az összes meglévő entitások eltávolítása a névtérből. Ügyeljen arra, hogy előtte összes üzeneteket fogadni.

## <a name="failover-workflow"></a>Feladatátvételi munkafolyamat

Az alábbi szakasz a részben áttekintjük, hogyan kell beállítani a kezdeti feladatátvételt, és hogyan haladni ettől a teljes folyamat.

![1][]

Először egy elsődleges és másodlagos névtér beállítása, majd hozzon létre egy párosításhoz. A párosítást lehetővé teszi az aliast való csatlakozásra használja. Használja az alias, mert nincs kapcsolati karakterláncok módosítása. Csak új névtér a feladatátvételi párosítás lehet hozzáadni. Végül hozzá kell adnia néhány eseményindító logikájának (például egy üzleti logika, amely észleli, ha a névtér nem érhető el, és a feladatátvételt kezdeményez). A névtér rendelkezésre állási használatával ellenőrizheti a [üzenet böngészés](message-browsing.md) Service Bus-képességét.

Figyelés és katasztrófa-helyreállítás a beállítása után vessen egy pillantást a feladatátvételi folyamat. Az eseményindító indít el egy feladatátvételt, vagy manuálisan kezdeményezze a feladatátvételt, ha két lépésre szükség:

1. Egy másik kimaradás esetén érdemes átvehet újra. Ezért beállítása egy második passzív névteret, és frissítse a jelenlegi párosítását. 
2. Lekéréses a korábban elsődleges névtérből származó üzenetek, az új névtéren elérhetővé válik. Ezt követően ismét felhasználni, vagy törölje a régi elsődleges névteret.

![2][]

## <a name="set-up-disaster-recovery"></a>Vészhelyreállítás beállítása

Ez a szakasz ismerteti, hogyan hozhat létre a saját Service Bus földrajzi-vész helyreállítási kódot. Ehhez szüksége két névtér független helyeken; például, Dél-amerikai és északi középső Régiójában. Az alábbi példában a Visual Studio 2017.

1.  Hozzon létre egy új **konzol App(.Net Framework)** a Visual Studio projekt, és adjon neki egy nevet, például **SBGeoDR**.

2.  A következő NuGet-csomagok telepítése:
    1.  Microsoft.IdentityModel.Clients.ActiveDirectory
    2.  Microsoft.Azure.Management.ServiceBus

3. Ellenőrizze, hogy a Newtonsoft.Json NuGet-csomagot használ verziójának 10.0.3 verziója.

3.  Adja hozzá a következő `using` utasítást, hogy a kód:

    ```csharp
    using System.Threading;
    using Microsoft.Azure.Management.ServiceBus;
    using Microsoft.Azure.Management.ServiceBus.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

4. Módosítsa a `main()` adható hozzá két prémium névterekben:

    ```csharp
    // 1. Create primary namespace (optional).

    var namespaceParams = new SBNamespace()
    {
        Location = "South Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    var namespace1 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, namespaceParams);

    // 2. Create secondary namespace (optional if you already have an empty namespace available).

    var namespaceParams2 = new SBNamespace()
    {
        Location = "North Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    // If you re-run this program while namespaces are still paired this operation will fail with a bad request.
    // This is because we block all updates on secondary namespaces once it is paired.

    var namespace2 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRSecondaryNS, namespaceParams2);
    ```

5. Engedélyezze a két névtér közötti párosítása, és lekérése az alias az entitások csatlakozni később használhatja:

    ```csharp
    // 3. Pair the namespaces to enable DR.

    ArmDisasterRecovery drStatus = client.DisasterRecoveryConfigs.CreateOrUpdate(
        resourceGroupName,
        geoDRPrimaryNS,
        alias,
        new ArmDisasterRecovery { PartnerNamespace = geoDRSecondaryNS });

    // A similar loop can be used to check if other operations (Failover, BreakPairing, Delete) 
    // mentioned below have been successful.
    while (drStatus.ProvisioningState != ProvisioningStateDR.Succeeded)
    {
        Console.WriteLine("Waiting for DR to be set up. Current state: " +
        drStatus.ProvisioningState);
        drStatus = client.DisasterRecoveryConfigs.Get(
        resourceGroupName,
        geoDRPrimaryNS,
        alias);

        Thread.CurrentThread.Join(TimeSpan.FromSeconds(30));
    }
    ```

Sikeresen beállította két párosított névtér. Most entitásokat, és figyelje meg a metaadat-szinkronizálás is létrehozhat. Ha szeretné azonnal ezt követően engedélyezze a metaadatok szinkronizálása némi időbe, végezzen el egy feladatátvételt. Például hozzáadhat egy rövid alvási idő:

```csharp
client.Topics.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", new SBTopic());
client.Subscriptions.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", "myTopic-Sub1", new SBSubscription());

// sleeping to allow metadata to sync across primary and secondary
Thread.Sleep(1000 * 60);
```

Ezen a ponton a portálon vagy az Azure Resource Manageren keresztül entitások hozzáadása, és tekintse meg, hogyan szinkronizálni. Kivéve, ha a csomag feladatátvételre manuálisan, érdemes létrehozni egy alkalmazást, amely az elsődleges névtér figyeli, és kezdeményezi, ha nem érhető el. 

## <a name="initiate-a-failover"></a>Kezdeményezzen feladatátvételt

A következő kód bemutatja, hogyan kezdeményezzen feladatátvételt:

```csharp
// Note that this failover operation is always run against the secondary namespace 
// (because primary might be down at time of failover).

client.DisasterRecoveryConfigs.FailOver(resourceGroupName, geoDRSecondaryNS, alias);
```

Miután a feladatátvétel indít el, passzív új névtér hozzáadása, és hozza létre újból a párosítást. Hozzon létre egy új párosítást a kódot az előző szakaszban látható. Ezenkívül el kell távolítani az üzenetek a régi elsődleges névtér a feladatátvétel befejezése után. Üzenetek fogadása egy üzenetsorból való, tekintse meg a [Ismerkedés a várólisták](service-bus-dotnet-get-started-with-queues.md).

## <a name="how-to-disable-geo-disaster-recovery"></a>Földrajzi-katasztrófa utáni helyreállítás letiltása

A következő kód bemutatja, hogyan egy névtér párosítás letiltása:

```csharp
client.DisasterRecoveryConfigs.BreakPairing(resourceGroupName, geoDRPrimaryNS, alias);
```

A következő kódot a létrehozott alias törli:

```csharp
// Delete the DR config (alias).
// Note that this operation must run against the namespace to which the alias is currently pointing.
// If you break the pairing and want to delete the namespaces afterwards, you must delete the alias first.

client.DisasterRecoveryConfigs.Delete(resourceGroupName, geoDRPrimaryNS, alias);
```

## <a name="steps-after-a-failover-failback"></a>A feladatátvétel (feladat-visszavétel) követő lépések

A feladatátvétel után hajtsa végre a következő két lépést:

1.  Hozzon létre egy új passzív másodlagos névtér. A kód egy korábbi szakaszában.
2.  Távolítsa el a fennmaradó üzenetek a várólistából.

## <a name="alias-connection-string-and-test-code"></a>Alias kapcsolati karakterláncot és a vizsgálati kódja

Ha azt szeretné, a feladatátvételi folyamat teszteléséhez, írhat mintaalkalmazás, amely a leküldéses értesítések üzeneteket az aliast használni elsődleges névtérhez. Ehhez győződjön meg arról, hogy az aktív névtér szerezze be a alias kapcsolati karakterláncot. Az aktuális előzetes kiadással nincs más adapter közvetlenül beszerzése a kapcsolati karakterláncot. Az alábbi példakód előtt, és a feladatátvétel után csatlakozik:

```csharp
var accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRPrimaryNS, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;

if(aliasPrimaryConnectionString == null)
{
    accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRSecondaryNS, "RootManageSharedAccessKey");
    aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
    aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
}
```

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a földrajzi-vész-helyreállítási [itt REST API-referenciában](/rest/api/servicebus/disasterrecoveryconfigs).
- Futtassa a földrajzi-vész-helyreállítási [mintát a Githubon](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Tekintse meg a földrajzi-vész-helyreállítási [mintát, amely üzeneteket küld egy aliast](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Tudhat meg többet a Service Bus üzenetkezelés, tekintse meg a következő cikkeket:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [REST API-n](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
