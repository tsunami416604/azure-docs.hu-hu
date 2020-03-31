---
title: Az Azure Service Bus névtereinek áttelepítése – prémium szintre
description: Útmutató a meglévő Azure Service Bus standard névterek prémium verzióra való áttelepítésének engedélyezéséhez
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 27e3260b91bebee14ff12188a7dbd6c7cf76355c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385027"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Meglévő Azure Service Bus standard névterek áttelepítése a prémium szintre

Korábban az Azure Service Bus csak a standard szinten kínált névtereket. A névterek olyan több-bérlős beállítások, amelyek alacsony átviteli sebességű és fejlesztői környezetekre vannak optimalizálva. A prémium szintű dedikált erőforrásokat kínál névtérenként a kiszámítható késés és a megnövekedett átviteli sebességgel rögzített áron. A prémium szintű csomag nagy átviteli sebességű és éles környezetek, amelyek további vállalati funkciókat igényelnek.

Ez a cikk ismerteti, hogyan telepítheti át a meglévő szabványos rétegbeli névtereket a prémium szintre.  

>[!WARNING]
> Az áttelepítés a Service Bus szabványos névterei számára készült, amelyeket a prémium szintre kell frissíteni. Az áttelepítési eszköz nem támogatja a visszaminősítést.

Néhány megjegyzés:

- Ennek az áttelepítésnek a helyén kell történnie, ami azt jelenti, hogy a meglévő küldő és fogadó alkalmazások **nem igényelnek módosításokat a kódban vagy a konfigurációban.** A meglévő kapcsolati karakterlánc automatikusan az új prémium szintű névtérre mutat.
- A **prémium szintű** névtér nem rendelkezik **entitások** benne az áttelepítés sikeres.
- A szabványos névtérben lévő összes **entitás** az áttelepítési folyamat során a prémium névtérbe **kerül.**
- Az áttelepítés **1000 entitást** támogat a prémium szinten lévő üzenetkezelő egységenként. Ha meg szeretné határozni, hogy hány üzenetküldési egységre van szüksége, kezdje az aktuális szabványos névtérben található entitások számával.
- Közvetlenül nem tud áttérni **az alapszintről** a **prémium szintre,** de ezt közvetetten megteheti, ha először az alapszintűről a standardra, majd a standardról a prémium szintre való áttérést a következő lépésben migrál.

## <a name="migration-steps"></a>A migrálás lépései

Bizonyos feltételek az áttelepítési folyamathoz kapcsolódnak. Ismerkedjen meg a következő lépésekkel a hibák lehetőségének csökkentése érdekében. Ezek a lépések ismertetik az áttelepítési folyamatot, és a lépésenkénti részletek az alábbi szakaszokban találhatók.

1. Hozzon létre egy új prémium szintű névteret.
1. Párosítsa egymáshoz a standard és a prémium szintű névtereket.
1. Entitások szinkronizálása (másolása) a standardés a prémium szintű névtér között.
1. Az áttelepítés véglegesítése.
1. A szabványos névtérben lévő entitások kiürítése a névtér áttelepítés utáni nevének használatával.
1. Törölje a szabványos névteret.

>[!IMPORTANT]
> Az áttelepítés véglegesítése után fért hozzá a régi szabványos névtérhez, és kiürítheti a várólistákat és az előfizetéseket. Az üzenetek kiürítése után előfordulhat, hogy az új prémium szintű névtérbe kell küldeni őket, hogy a fogadó alkalmazások feldolgozzák őket. A várólisták és előfizetések kiürítése után azt javasoljuk, hogy törölje a régi szabványos névteret.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Áttelepítés az Azure CLI vagy a PowerShell használatával

A Service Bus szabványos névterének az Azure CLI vagy a PowerShell eszközzel az Azure CLI vagy a PowerShell eszközzel prémium verzióra történő áttelepítéséhez kövesse az alábbi lépéseket.

1. Hozzon létre egy új Service Bus prémium névteret. Hivatkozhat az [Azure Resource Manager-sablonokra,](service-bus-resource-manager-namespace.md) vagy [használhatja az Azure Portalt.](service-bus-create-namespace-portal.md) Ügyeljen arra, hogy válassza ki a **szolgáltatásBusSku** paraméter **prémium** kiválasztását.

1. Az áttelepítési parancsok egyszerűsítése érdekében állítsa be a következő környezeti változókat.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Az áttelepítés utáni alias/név (post_migration_dns_name) lesz a régi szabványos névtér-utóáttelepítés eléréséhez. Ezzel kiürítheti a várólistákat és az előfizetéseket, majd törölheti a névteret.

1. Párosítsa a szabványos és a prémium névtereket, és indítsa el a szinkronizálást a következő paranccsal:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Ellenőrizze az áttelepítés állapotát a következő paranccsal:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Az áttelepítés akkor tekinthető teljesnek, ha a következő értékek jelennek meg:

    * MigrationState = "Aktív"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Sikeres"

    Ez a parancs az áttelepítési konfigurációt is megjeleníti. Ellenőrizze, hogy az értékek megfelelően vannak-e beállítva. Ellenőrizze a prémium névteret a portálon, hogy minden várólisták és témakörök hoztak létre, és hogy azok megfelelnek a szabványos névtérben létezett.

1. Az áttelepítés véglegesítése a következő teljes parancs végrehajtásával:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Áttelepítés az Azure Portal használatával

Az Azure Portal használatával történő áttelepítés a parancsok használatával történő áttelepítésével azonos logikai folyamattal rendelkezik. Kövesse az alábbi lépéseket az Azure Portal használatával történő áttelepítéshez.

1. A bal oldali ablaktábla **Navigációs** menüjében válassza az **Áttelepítés prémiumra**lehetőséget. A következő laphoz való továbblépéshez kattintson az **Első lépések** gombra.
    ![Áttelepítési céloldal][]

1. A **telepítés befejezése.**
   ![Beállítási névtér][]
   1. Hozza létre és rendelje hozzá a prémium névteret a meglévő szabványos névtér áttelepítéséhez.
        ![Telepítő névtér – prémium névtér létrehozása][]
   1. Válasszon egy **Áttelepítés utáni nevet**. Ezzel a névvel érheti el a szabványos névteret az áttelepítés befejezése után.
        ![Telepítési névtér - az áttelepítés utáni név kivételezése][]
   1. A folytatáshoz válassza a **"Tovább"** lehetőséget.
1. Entitások szinkronizálása a standard és a prémium szintű névterek között.
    ![Telepítő névtér - szinkronizálási entitások - indítás][]

   1. Válassza **a Szinkronizálás indítása** lehetőséget az entitások szinkronizálásának megkezdéséhez.
   1. A szinkronizálás megerősítéséhez és elindításához válassza az **Igen** lehetőséget a párbeszédpanelen.
   1. Várjon, amíg a szinkronizálás befejeződik. Az állapot elérhető az állapotsoron.
        ![Beállítási névtér - szinkronizálási entitások - folyamat][]
        >[!IMPORTANT]
        > Ha bármilyen okból meg kell szakítania az áttelepítést, tekintse át a megszakítási folyamatot a dokumentum GYIK részében.
   1. A szinkronizálás befejezése után válassza a **Tovább** gombot a lap alján.

1. Tekintse át a módosításokat az összefoglaló oldalon. Válassza **a Teljes áttelepítés** lehetőséget a névterek közötti váltáshoz és az áttelepítés befejezéséhez.
    ![Névtér váltása - váltás menü][]  
    A megerősítő lap akkor jelenik meg, amikor az áttelepítés befejeződött.
    ![Névtér váltása - sikeres][]

## <a name="caveats"></a>Ellenmondások

Az Azure Service Bus standard szint által biztosított funkciók némelyikét az Azure Service Bus Premium tier nem támogatja. Ezek a tervezés, mivel a prémium szintű dedikált erőforrásokat kínál a kiszámítható átviteli és késési.

Itt van egy lista a funkciók nem támogatja a Premium és azok enyhítésére -

### <a name="express-entities"></a>Expressz entitások

   A Prémium verzió nem támogatja azokat az expressz entitásokat, amelyek nem véglegesítenek üzenetadatokat a tároláshoz. A dedikált erőforrások jelentős átviteli javulást biztosítottak, miközben biztosították az adatok megőrzését, ahogy az bármely vállalati üzenetküldő rendszertől elvárható.

   Az áttelepítés során a standard névtérben lévő bármely expressz entitás nem expressz entitásként jön létre a prémium szintű névtérben.

   Ha Az Azure Resource Manager (ARM) sablonokat használja, győződjön meg arról, hogy eltávolítja az "enableExpress" jelzőt a központi telepítési konfigurációból, hogy az automatikus munkafolyamatok hiba nélkül futhassanak.

### <a name="partitioned-entities"></a>Particionált entitások

   Partiparti entitások támogatták a standard rétegben, hogy jobb rendelkezésre állást biztosítson a több-bérlős beállítás. A prémium szinten névtérenként elérhető dedikált erőforrások biztosításával erre a célra már nincs szükség erre.

   Az áttelepítés során a Standard névtérben lévő particionált entitás oka nem particionált entitásként jön létre a prémium szintű névtérben.

   Ha az ARM sablon "enablePartitioning" értékre állítja az "igaz" értéket egy adott várólistára vagy témára vonatkozóan, akkor a bróker figyelmen kívül hagyja.

## <a name="faqs"></a>Gyakori kérdések

### <a name="what-happens-when-the-migration-is-committed"></a>Mi történik, ha az áttelepítés le van sújtva?

Az áttelepítés véglegesítése után a szabványos névtérre mutatott kapcsolati karakterlánc a prémium szintű névtérre mutat.

A küldő és a fogadó alkalmazás bontja a kapcsolatot a szabványos névtérrel, és automatikusan újra csatlakozik a prémium névtérhez.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Mit tegyek, miután a standard prémium áttelepítés befejeződött?

A standard prémium szintű áttelepítés biztosítja, hogy az entitás metaadatait, például a témakörök, előfizetések és szűrők a standard névtérből a prémium névtérbe másolása. A szabványos névtérben véglegesített üzenetadatok nem lesznek átmásolva a normál névtérből a prémium szintű névtérbe.

Előfordulhat, hogy a szabványos névtérben vannak olyan üzenetek, amelyeket az áttelepítés közben küldött és véglegesített. Manuálisan engedje le ezeket az üzeneteket a szabványos névtérből, és manuálisan küldje el őket a prémium névtérbe. Az üzenetek manuális kiürítéséhez használjon olyan konzolalkalmazást vagy parancsfájlt, amely az áttelepítés utáni DNS-név használatával kiüríti a szabványos névtérentitásokat. Küldje el ezeket az üzeneteket a prémium névtérbe, hogy azokat a fogadók feldolgozhassák.

Az üzenetek kiürítése után törölje a szabványos névteret.

>[!IMPORTANT]
> A szabványos névtérből érkező üzenetek kiürítése után törölje a szabványos névteret. Ez azért fontos, mert a szabványos névtérre eredetileg hivatkozott kapcsolati karakterlánc most a prémium szintű névtérre hivatkozik. Többé nem lesz szüksége a szabványos névtérre. Az áttelepített szabványos névtér törlésével csökkentheti a későbbi zavart.

### <a name="how-much-downtime-do-i-expect"></a>Mennyi állásidőre számíthatok?

Az áttelepítési folyamat célja, hogy csökkentse az alkalmazások várható állásidejét. Az állásidő csökken a küldő és a fogadó alkalmazás által az új prémium szintű névtérre való rámutatáshoz használt kapcsolati karakterlánc használatával.

Az alkalmazás által tapasztalt állásidő a DNS-bejegyzés frissítéséhez szükséges időre korlátozódik. Az állásidő körülbelül 5 perc.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Az áttelepítés során módosítani kell a konfigurációt?

Nem, nincs szükség kód- vagy konfigurációs módosításokra az áttelepítéshez. A küldő és fogadó alkalmazások által a szabványos névtér eléréséhez használt kapcsolati karakterlánc automatikusan le lesz képezve, hogy a prémium névtér aliasaként működjön.

### <a name="what-happens-when-i-abort-the-migration"></a>Mi történik az áttelepítés megszakításakor?

Az áttelepítés megszakítható a parancs `Abort` használatával vagy az Azure Portal használatával.

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure portál

![Folyamat megszakítása -][]
![megszakítás szinkronizálás Megszakítás folyamat - megszakítás kész][]

Az áttelepítési folyamat megszakításakor megszakítja az entitások (témakörök, előfizetések és szűrők) másolásának folyamatát a standardból a prémium névtérbe, és megszakítja a párosítást.

A kapcsolati karakterlánc nem frissül, hogy a prémium szintű névtérre mutasson. A meglévő alkalmazások továbbra is ugyanúgy működnek, mint az áttelepítés megkezdése előtt.

Azonban nem törli az entitásokat a prémium névtérben, és nem törli a prémium névteret. Törölje az entitásokat manuálisan, ha úgy döntött, hogy nem lép előre az áttelepítéssel.

>[!IMPORTANT]
> Ha úgy dönt, hogy megszakítja az áttelepítést, törölje a prémium névteret, amelyet az áttelepítéshez kiépített, hogy ne kelljen fizetnie az erőforrásokért.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Nem akarom kiszívni az üzeneteket. Mit tegyek?

Előfordulhatnak olyan üzenetek, amelyeket a küldő alkalmazások küldenek, és véglegesítik a normál névtérben lévő tárolóban, miközben az áttelepítés történik, és közvetlenül az áttelepítés véglegesítése előtt.

Az áttelepítés során a tényleges üzenetadatok/hasznos adat nem másolódnak a standardból a prémium névtérbe. Az üzeneteket manuálisan kell lecsöpögtetetni, majd el kell küldeni a prémium névtérbe.

Ha azonban áttud térni egy tervezett karbantartási/takarítási időszakban, és nem szeretné manuálisan kiüríteni és elküldeni az üzeneteket, kövesse az alábbi lépéseket:

1. Állítsa le a küldő alkalmazásokat. A fogadó alkalmazások feldolgozzák a szabványos névtérben jelenleg lévő üzeneteket, és kiürítik a várólistát.
1. Miután a normál névtérben lévő várólisták és előfizetések üresek, kövesse a korábban leírt eljárást a standardról a prémium névtérbe való áttelepítés végrehajtásához.
1. Az áttelepítés befejezése után újraindíthatja a küldő alkalmazásokat.
1. A feladók és a fogadók mostantól automatikusan csatlakoznak a prémium névtérhez.

    >[!NOTE]
    > Nem kell leállítania a fogadó alkalmazásokat az áttelepítéshez.
    >
    > Az áttelepítés befejezése után a fogadóalkalmazások leváltanak a normál névtérről, és automatikusan csatlakoznak a prémium névtérhez.

## <a name="next-steps"></a>További lépések

* További információ a standard és a [prémium üzenetek közötti különbségekről.](./service-bus-premium-messaging.md)
* Ismerje meg a [High-Availability és a Geo-katasztrófa helyreállítási szempontok Service Bus prémium.](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

[Áttelepítési céloldal]: ./media/service-bus-standard-premium-migration/1.png
[Beállítási névtér]: ./media/service-bus-standard-premium-migration/2.png
[Telepítő névtér – prémium névtér létrehozása]: ./media/service-bus-standard-premium-migration/3.png
[Telepítési névtér - az áttelepítés utáni név kivételezése]: ./media/service-bus-standard-premium-migration/4.png
[Telepítő névtér - szinkronizálási entitások - indítás]: ./media/service-bus-standard-premium-migration/5.png
[Beállítási névtér - szinkronizálási entitások - folyamat]: ./media/service-bus-standard-premium-migration/8.png
[Névtér váltása - váltás menü]: ./media/service-bus-standard-premium-migration/9.png
[Névtér váltása - sikeres]: ./media/service-bus-standard-premium-migration/12.png

[Folyamat megszakítása - szinkronizálás megszakítása]: ./media/service-bus-standard-premium-migration/abort1.png
[Folyamat megszakítása - megszakítás kész]: ./media/service-bus-standard-premium-migration/abort3.png
