---
title: "Speciális alkalmazás frissítési témakörök |} Microsoft Docs"
description: "Ez a cikk ismertet néhány speciális témakörök vonatkozó frissítése a Service Fabric-alkalmazás."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar;chackdan
ms.openlocfilehash: 8d3b922f3d50b645ac9db2cc879a319df1262e0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>A Service Fabric az alkalmazásfrissítés: speciális kapcsolatos témakörök
## <a name="adding-or-removing-services-during-an-application-upgrade"></a>Hozzáadásával vagy eltávolításával a szolgáltatások egy alkalmazás frissítése során
Ha egy alkalmazás már telepítve, és közzétett frissítéseként ad hozzá egy új szolgáltatás, az új szolgáltatás a telepített alkalmazás kerül.  Ilyen frissítés nem befolyásolja a szolgáltatások, amelyek már az alkalmazás részei voltak. Azonban az új szolgáltatás aktívnak el kell indítani, amely hozzá lett adva a szolgáltatás egy példánya (használja a `New-ServiceFabricService` parancsmag).

Szolgáltatások frissítés részeként-alkalmazásból is eltávolítható lesz. Azonban a frissítés folytatása előtt le kell állítani a to-be törölt szolgáltatás az összes jelenlegi példányát (használja a `Remove-ServiceFabricService` parancsmag).

## <a name="manual-upgrade-mode"></a>Manuális frissítési módra
> [!NOTE]
> A nem figyelt manuális mód csak a sikertelen vagy felfüggesztett frissítés figyelembe kell venni. A figyelt módja Service Fabric-alkalmazások esetében az ajánlott frissítési mód.
>
>

Az Azure Service Fabric több frissítési módok támogatásához a fejlesztési és éles fürt biztosít. A kiválasztott központi telepítési beállítások különböző környezetek eltérőek lehetnek.

A figyelt működés közbeni frissítés alkalmazása a legjellemzőbb frissítés az éles környezetben használandó. A házirend megadása esetén a Service Fabric biztosítja, hogy az alkalmazás megfelelő előtt a frissítés előrehalad.

 Az alkalmazás-rendszergazda segítségével a manuális működés közbeni alkalmazás frissítési mód a frissítési folyamat állapota teljes hozzáféréssel rendelkeznek a különböző frissítési tartományok keresztül. Ebben a módban akkor hasznos, ha testreszabott vagy összetett értékelési állapotházirend szükség, vagy a nem hagyományos frissítés történik (például az alkalmazás már szerepel az adatveszteség).

Végül az automatizált működés közbeni alkalmazásfrissítés akkor hasznos, fejlesztési vagy tesztelési környezetben adja meg a szolgáltatás a fejlesztés során gyors iterációs ciklust.

## <a name="change-to-manual-upgrade-mode"></a>Manuális frissítési módra
**Manuális**--állítsa le a jelenlegi UD: az alkalmazásfrissítés, és módosítsa a frissítési mód a nem figyelt manuális. A rendszergazdának manuálisan hívására **MoveNextApplicationUpgradeDomainAsync** a frissítés folytatásához, vagy új frissítés lekérhetik indul el, a visszaállítás. Miután a frissítés manuális módba lép, marad a manuális üzemmódban addig, amíg az új frissítést lehet kezdeményezni. A **GetApplicationUpgradeProgressAsync** parancs visszaadja a HÁLÓ\_alkalmazás\_frissítése\_állapot\_működés közbeni\_előre\_FÜGGŐBEN.

## <a name="upgrade-with-a-diff-package"></a>A diff csomag frissítése
A Service Fabric-alkalmazás tudja frissíteni a teljes, önállóan alkalmazási csomaggal rendelkező kiépítés. Az alkalmazás csak a frissített alkalmazásfájlok, a frissített alkalmazásjegyzék és a service manifest fájlokat tartalmazó különbözeti csomag segítségével is frissíthető.

A teljes alkalmazáscsomagok elindításához és a Service Fabric-alkalmazás futtatásához szükséges összes fájlt tartalmazza. A különbözeti csomag csak a megváltozott a utolsó és az aktuális frissítés között fájlokat tartalmazza, valamint a teljes alkalmazás jegyzékfájlja és a service manifest fájlt. Az alkalmazásjegyzék vagy a szolgáltatás jegyzék, amely nem található a build elrendezésben hivatkozás az image store kell keresni.

Teljes alkalmazáscsomagok egy alkalmazást a fürt első telepítéséhez szükségesek. Soron következő frissítések a teljes alkalmazáscsomagok vagy különbözeti csomag is lehet.

Ha különbözeti csomagot lenne a legjobb megoldás alkalommal:

* Különbözeti csomag használata ajánlott, ha a nagy alkalmazáscsomagok, amelyek több service manifest fájl és/vagy több kód csomagok, konfigurációs csomagokat vagy adatok csomagok hivatkozik.
* Diff csomag részesíti előnyben, ha egy üzembe helyezési rendszerrel, amely hoz létre a build elrendezés közvetlenül a az alkalmazás létrehozási folyamata. Ebben az esetben annak ellenére, hogy a kód nem változott, újonnan beépített szerelvények egy másik ellenőrzőösszeg beolvasása. A teljes alkalmazás csomag használata, hogy frissítse az összes kódot csomag igényelnének. Diff csomagot használ, csak megadja a módosított fájlokra és a jegyzékfájlt, amelyekben módosult a verzióra.

Ha egy alkalmazás frissítve van, a Visual Studio használatával, a különbözeti csomag automatikusan van közzétéve. Hozzon létre manuálisan egy különbségi csomag, az alkalmazás jegyzékében, és a szolgáltatás jegyzékfájlokat frissíteni kell, de csak a módosított csomagok szerepelnie kell a végső alkalmazáscsomagot.

Például kezdjük azokkal a következő alkalmazás (verziószámok ismertetése könnyű előírt):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Most tegyük fel, csak a kód csomag service1 a PowerShell használatával különbözeti csomagot szeretne. Most a frissített alkalmazás struktúrája a következő mappa:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Ebben az esetben frissíti az alkalmazás jegyzékében 2.0.0, és a szolgáltatás jegyzékfájl service1, hogy tükrözze a csomag frissítése. A mappa az alkalmazáscsomag az alábbi szerkezettel rendelkezik:

```text
app1/
  service1/
    code/
```

## <a name="next-steps"></a>Következő lépések
[Az alkalmazás használata a Visual Studio frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti Önt az alkalmazásfrissítés Visual Studio használatával.

[Az alkalmazás használatával Powershell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti Önt az alkalmazásfrissítés PowerShell használatával.

Szabályozhatja, hogy az alkalmazás használatával frissíti [frissítése paraméterek](service-fabric-application-upgrade-parameters.md).

Az alkalmazásfrissítéseket által használatának megtanulása kompatibilissé [Adatszerializálás](service-fabric-application-upgrade-data-serialization.md).

Alkalmazásfrissítések gyakori problémáinak megoldásához hajtsa végre a hivatkozással [Alkalmazásfrissítések hibaelhárítási](service-fabric-application-upgrade-troubleshooting.md).
