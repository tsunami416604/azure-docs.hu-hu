---
title: "Az Azure Event Hubs földrajzi-vész-helyreállítási |} Microsoft Docs"
description: "Feladatátvételi földrajzi régió használatáról, és hajtsa végre az Azure Event Hubs katasztrófa utáni helyreállítás"
services: event-hubs
documentationcenter: 
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sethm
ms.openlocfilehash: 94c2782b3166fbc65ae755291a82a2a14556b96f
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2017
---
# <a name="azure-event-hubs-geo-disaster-recovery-preview"></a>Az Azure Event Hubs földrajzi-vész-helyreállítási (előzetes verzió)

Regionális adatközpontok leállás következik be, ha nagyon fontos az adatok feldolgozásához továbbra is működik egy másik régióban vagy datacenter. Ilyen *földrajzi-vész-helyreállítási* és *georeplikáció* bármely vállalati fontos funkciókat. Az Azure Event Hubs földrajzi-vész-helyreállítási és a georeplikáció, a névterek szintjén is támogatja. 

A földrajzi-vész-helyreállítási funkció Azure Event hubs egy vész-helyreállítási megoldást. A fogalmakat és a jelen cikkben ismertetett munkafolyamat alkalmazza a vészhelyreállítási forgatókönyveket, és nem átmeneti, illetve ideiglenes kimaradások esetén.

A Microsoft Azure-ban vész-helyreállítási részletes tárgyalását lásd: [Ez a cikk](/azure/architecture/resiliency/disaster-recovery-azure-applications). 

## <a name="terminology"></a>Terminológia

**Párosítás**: az elsődleges névtér nevezzük *aktív* , illetve üzeneteket fogad. A Feladatátvevőfürt-névtér *passzív* és nem fogadhat üzeneteket. A metaadatok között is a Szinkronizáló, így is képes zökkenőmentesen fogadja az üzeneteket alkalmazás kód módosítás nélkül. Az aktív terület és passzív régió közötti vészhelyreállítási konfigurációban létrehozó nevezik *párosítás* a régiók.

**Alias**: beállított vészhelyreállítási konfiguráció nevét. Az alias nyújt egy teljesen minősített tartománynév (FQDN) egyetlen stabil kapcsolati karakterláncot. Alkalmazások ez alias a kapcsolati karakterlánc használatával csatlakozni a névtérhez.

**Metaadatok**: event hub nevét, fogyasztói csoportok, partíciók, az átviteli egységek, entitásokat és a névtér társított tulajdonságok hivatkozik.

## <a name="enable-geo-disaster-recovery"></a>Földrajzi-katasztrófa utáni helyreállítás engedélyezése

Az Event Hubs földrajzi-vész-helyreállítási 3 lépésben engedélyezi: 

1. Hozzon létre egy földrajzi-párosítás, amely létrehoz egy alias kapcsolati karakterláncot, és élő metaadatok replikációs biztosít. 
2. Frissítse a meglévő ügyfél-kapcsolati karakterláncok az 1. lépésben létrehozott aliast.
3. Kezdeményezze a feladatátvételt: a földrajzi párosítás megszakad, és az alias az új elsődleges névtérként másodlagos névterére mutat.

Az alábbi ábrán láthatók a munkafolyamat:

![Földrajzi párosítás folyamata][1] 

### <a name="step-1-create-a-geo-pairing"></a>1. lépés:, hozzon létre egy földrajzi párosítás

Hozzon létre egy két régiók közötti párosítása, egy elsődleges névtér és egy másodlagos névtér van szükség. Ezután hozzon létre egy aliast a földrajzi pár kialakításához. Ha a névterek van párosítva alias, a metaadatok rendszeres időközönként replikálódik mindkét névtérre. 

A következő kód bemutatja, hogyan ehhez:

```csharp
ArmDisasterRecovery adr = await client.DisasterRecoveryConfigs.CreateOrUpdateAsync(
                                    config.PrimaryResourceGroupName,
                                    config.PrimaryNamespace,
                                    config.Alias,
                                    new ArmDisasterRecovery(){ PartnerNamespace = config.SecondaryNamespace});
```

### <a name="step-2-update-existing-client-connection-strings"></a>2. lépés: a meglévő ügyfél kapcsolati karakterláncok frissítése

A földrajzi párosítás végrehajtása után a kapcsolati karakterláncokkal, amelyek az elsődleges névterek frissíteni kell, hogy a alias kapcsolati karakterlánc mutasson. Szerezze be a kapcsolati karakterláncokat, a következő példában látható módon:

```csharp
var accessKeys = await client.Namespaces.ListKeysAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
```

### <a name="step-3-initiate-a-failover"></a>3. lépés: kezdeményezze a feladatátvételt

Ha katasztrófa történik, vagy ha úgy dönt, hogy a feladatátvételt a másodlagos névtér kezdeményezni, metaadatokat és az adatokat indítsa el a másodlagos névtér összekapcsolását. Mivel az alkalmazások a alias kapcsolati karakterláncokat, semmilyen további műveletet,, automatikusan elindítja a olvasását és írását az event hubs a másodlagos névtérben. 

A következő kód bemutatja, hogyan elindíthatja a feladatátvételt:

```csharp
await client.DisasterRecoveryConfigs.FailOverAsync(config.SecondaryResourceGroupName,
                                                   config.SecondaryNamespace, config.Alias);
```

Miután a feladatátvétel befejeződött, és szüksége van az elsődleges névtérben található adatok, az adatok kinyerése érdekében kell használnia az event hubs egy explicit kapcsolati karakterlánc az elsődleges névtérben.

### <a name="other-operations-optional"></a>Más műveletek (nem kötelező)

Törés az a földrajzi jelenlegi párosítását, és törölheti a alias, az alábbi kódban látható módon is. Vegye figyelembe, hogy törli az alias kapcsolati karakterláncot, akkor meg kell szakítania a földrajzi paring:

```csharp
// Break pairing
await client.DisasterRecoveryConfigs.BreakPairingAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, config.Alias);

// Delete alias connection string
// Before the alias is deleted, pairing must be broken
await client.DisasterRecoveryConfigs.DeleteAsync(config.PrimaryResourceGroupName,
                                                 config.PrimaryNamespace, config.Alias);
```

## <a name="considerations-for-public-preview"></a>A nyilvános előzetes verziójához kapcsolatos szempontok

Ebben a kiadásban a következő érdemes figyelembe venni:

1. A földrajzi-vész-helyreállítási képességet csak a északi középső Régiójában és déli középső Régiójában régiók érhető el. 
2. A funkció csak az újonnan létrehozott névterek támogatott.
3. Az előzetes kiadásban csak metaadat replikáció engedélyezve van. Tényleges adatokat a rendszer nem replikálja.
4. Az előzetes kiadás esetén a szolgáltatás engedélyezéséhez költség nélkül. Azonban az elsődleges, mind a másodlagos névterek lesz függő díj terheli a fenntartott átviteli egységek.

## <a name="next-steps"></a>Következő lépések

* A [mintát a Githubon](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) végigvezeti egy egyszerű munkafolyamatot, amely létrehoz egy földrajzi párosítás és indít el egy feladatátvételt egy vész-helyreállítási forgatókönyv.
* A [REST API-referenciában](/rest/api/eventhub/disasterrecoveryconfigs) API-k ismerteti a földrajzi-vészhelyreállítási konfigurációban végrehajtásához.

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/eh-geodr1.png

