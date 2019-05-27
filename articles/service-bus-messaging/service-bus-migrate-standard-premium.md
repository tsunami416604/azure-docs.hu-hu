---
title: A prémium szint meglévő standard szintű Azure Service Bus-névterek áttelepítése |} A Microsoft Docs
description: Útmutató áttelepítéshez a meglévő Azure Service Bus standard szintű névterek prémium
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 65c207b4d03e7d156c8c871a3642601fd0489ead
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991423"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>A prémium szint meglévő standard szintű Azure Service Bus-névterek áttelepítése
Korábban az Azure Service Bus névtér csak a standard szinten érhető el. Névterek olyan több-bérlős beállítását, amely az alacsony átviteli sebesség és a fejlesztői környezetben vannak optimalizálva. A prémium szint dedikált erőforrások névterenként előre jelezhető késés és nagyobb átviteli sebesség, fix áron kínál. A prémium szint nagy átviteli sebességű és újabb nagyvállalati funkciókat igényelnek, éles üzemi környezetek van optimalizálva.

Ez a cikk ismerteti a meglévő standard szintű névterek áttelepítése a prémium szint.  

>[!WARNING]
> Áttelepítési frissíteni kell a prémium szintű Service Bus a standard szintű névterek számára készült. Az áttelepítési eszköz alacsonyabb verziójúra változtatása nem támogatja.

Egyes megjegyezni pontok: 
- Az áttelepítés hivatott történjen a helyet, ami azt jelenti, hogy a már küldői és fogadói alkalmazások **nem szükséges módosítania kódírásra vagy konfigurálásra**. A meglévő kapcsolati karakterlánc az új prémium szintű névtér automatikusan fog mutatni.
- A **prémium** névteret kell **nincsenek entitások** , az áttelepítés sikeres. 
- Az összes **entitások** a standard szintű névtérben vannak **másolt** a prémium névtérhez az áttelepítés során. 
- Áttelepítés által támogatott **1000 entitás / üzenetkezelési egység** a prémium szint. Azonosíthatja a hány üzenetkezelési egység van szüksége, kezdje a jelenlegi standard névtérben rendelkező entitások száma. 
- Nem lehet közvetlenül áttelepít **alapszintű csomag** való **premier szintű**, azonban az alapszintű, standard első és a standard, Premium, a következő lépésben a migrálással közvetve úgy van.

## <a name="migration-steps"></a>Migrálási lépések
Az áttelepítési folyamat egyes feltételek tartoznak. Ismerje meg az alábbi lépéseket a hibázás lehetőségét csökkentése érdekében. Ezeket a lépéseket a migrálási folyamatot körvonalazzák, és a következő szakaszok a témakör részletesen láthatók.

1. Hozzon létre egy új névteret.
1. Párosítsa a standard és prémium szintű névterek egymáshoz.
1. A prémium szintű névtérre váltani, a standard entitások szinkronizálása (másolási hozzáadásánál).
1. A migrálás véglegesítése.
1. A standard szintű névtér entitások kiürítési a névtér áttelepítés utáni nevet használja.
1. A standard szintű névtér törlése.

>[!IMPORTANT]
> Miután a migrálás véglegesítése után, eléri a régi standard szintű névteret, és kiürítési az üzenetsorok és -előfizetések. Az üzenetek rendelkezik lett ürítve, miután azok küldhető az új prémium szintű névtérre váltani a fogadó alkalmazások általi feldolgozásának. Miután az üzenetsorok és -előfizetések rendelkezik lett ürítve, javasoljuk, hogy törölje a régi standard névtérben.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Telepítse át az Azure CLI vagy a PowerShell segítségével

A prémium szintű a standard szintű Service Bus-névtér áttelepítése az Azure parancssori felület vagy PowerShell eszköz használatával, kövesse az alábbi lépéseket.

1. Hozzon létre egy új Service Bus prémium szintű névteret. Hivatkozhat a [Azure Resource Manager-sablonok](service-bus-resource-manager-namespace.md) vagy [az Azure Portal](service-bus-create-namespace-portal.md). Ügyeljen arra, hogy válasszon **prémium szintű** számára a **serviceBusSku** paraméter.

1. Állítsa be az alábbi környezeti változókat egyszerűsítése érdekében az áttelepítés parancsokat.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Áttelepítés utáni alias/name (post_migration_dns_name) a régi standard szintű névtér a migrálás után eléréséhez használható. Ezzel a kiürítési, az üzenetsorok és az előfizetések, és ezután törölje a névteret.

1. Párosítsa a standard és prémium szintű névterek, és a szinkronizálás indítása a következő paranccsal:

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. Az áttelepítés állapotának ellenőrzéséhez a következő paranccsal:
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Az áttelepítés akkor tekinthető amikor megjelenik a következő értékeket:
    * MigrationState = "Aktív"
    * pendingReplicationsOperationsCount = 0
    * provisioningState "Succeeded" =

    Ez a parancs a migrálási konfiguráció is megjeleníti. Ellenőrizze, hogy a helyesen van-e beállítva az értékeket. A prémium szintű névtér annak érdekében, az üzenetsorok és témakörök létrehozott, és megfeleljenek Mi a standard szintű névtér létezett a portálon is ellenőrizhető.

1. A migrálás véglegesítése a következő teljes parancs végrehajtásával:
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Telepítse át az Azure portal segítségével

Az Azure portal használatával történő áttelepítés ugyanazon logikai folyamata megegyezik a parancsokkal rendelkezik. Kövesse az alábbi lépéseket áttelepítése az Azure portal használatával.

1. Az a **navigációs** bal oldali panelén válassza a menü **áttelepítés prémium szintű**. Kattintson a **Ismerkedés** gombra a folytatáshoz a következő lapra.
    ![Áttelepítési kezdőlapja][]

1. Teljes **telepítő**.
   ![A telepítő névtér][]
   1. Hozzon létre, és rendelje hozzá a prémium szintű névtér áttelepítése a meglévő standard szintű névtérre.
        ![Névtér beállítása – prémium szintű névtér létrehozása][]
   1. Válasszon egy **a Migrálás után neve**. A migrálás befejezése után a standard szintű névtér el ezt a nevet fogja használni.
        ![Névtér beállítása – válassza ki a post áttelepítési neve][]
   1. Válassza ki **'Tovább'** folytatásához.
1. A standard és prémium szintű névterek közötti entitások szinkronizálása.
    ![Névtér - entitások szinkronizálása – kezdő beállítása][]

   1. Válassza ki **szinkronizálás indítása** megkezdéséhez az entitások szinkronizálása.
   1. Válassza ki **Igen** a párbeszédpanel, erősítse meg, és a szinkronizálás elindításához.
   1. Várjon, amíg a szinkronizálás be nem fejeződik. Az állapot az állapotsor érhető el.
        ![Névtér - szinkronizálás entitások - folyamat beállítása][]
        >[!IMPORTANT]
        > Ha bármilyen okból szakítsa meg a migrálást van szüksége, tekintse át a megszakítási folyamat a jelen dokumentum a gyakori kérdésekkel foglalkozó szakaszban.
   1. A szinkronizálás befejezése után jelölje ki a **tovább** az oldal alján.

1. Tekintse át a módosításokat az összefoglalás lapon. Válassza ki **az áttelepítés befejezése** váltani a névterek és az áttelepítés befejezéséhez.
    ![Váltson a névtér - kapcsoló menü][] a Megerősítés lapon jelenik meg, ha az áttelepítés akkor fejeződött be.
    ![Kapcsoló névtér - sikeres][]

## <a name="faqs"></a>Gyakori kérdések

### <a name="what-happens-when-the-migration-is-committed"></a>Mi történik, ha az áttelepítés számára fontos?

A migrálás véglegesítése után a standard szintű névtér mutatott kapcsolati karakterláncot a prémium szintű névtérre váltani fog mutatni.

A küldő és fogadó alkalmazások bontsa a kapcsolatot a standard szintű Namespace, és a automatikusan újracsatlakozzanak a prémium szintű névtér.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Mit a teendő, a standard, prémium szintű az áttelepítés befejezése után?

A standard, prémium szintű áttelepítési biztosítja, hogy az entitás-metaadatok, például a témakörök, előfizetések és szűrőket a prémium szintű névtérre váltani a standard szintű névtér másolódnak. Az állapotüzenet-adatokat, amely a standard szintű névtér véglegesített volt a prémium szintű névtér nem másolja a standard szintű névteret.

A standard szintű névtér előfordulhat, hogy néhány üzenetet küldött, és miközben folyamatban volt a migrálás véglegesítése. Ezeket az üzeneteket a standard szintű Namespace manuálisan kiürítési, és manuálisan küldje el azokat a prémium szintű Namespace. Manuálisan az üzeneteket, használja egy konzolalkalmazás vagy egy parancsfájlt, amely a standard szintű névtér entitások kiüríti a Post áttelepítési DNS-név, amelyet az áttelepítés parancsok a használatával. Ezeket az üzeneteket küldeni a prémium szintű névtér úgy, hogy a hozzáadásuk dolgozhassák fel.

Az üzenetek rendelkezik lett ürítve, miután a standard szintű névtér törlése

>[!IMPORTANT]
> Miután a standard szintű névtér üzeneteit rendelkezik lett ürítve, törölje a standard szintű névtér. Ez azért fontos, mert a kapcsolati karakterláncot, amely kezdetben említett a standard szintű névtér már hivatkozik a prémium szintű névtérre váltani. A standard szintű Namespace többé nem szükséges. A standard szintű névtér áttelepített törlése segítségével újabb elkerülése érdekében.

### <a name="how-much-downtime-do-i-expect"></a>Mennyi állásidő tegye várható?
Az áttelepítési folyamat célja az alkalmazások a várható állásidő csökkentése érdekében. Állásidő csökken a kapcsolati karakterlánc, a küldő és fogadó-alkalmazások használatával az új prémium szintű névtérre mutasson.

A prémium szintű névtér átirányítása a DNS-bejegyzés frissítéséhez szükséges idő az állásidő, amely az alkalmazás által bekövetkezik korlátozódik. Állásidőre körülbelül 5 perc.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Rendelkezik semmilyen konfigurációs módosítást a migrálás során?
Nem, nem változtak kódírásra vagy konfigurálásra az áttelepítés végrehajtásához szükséges. A kapcsolati karakterláncot, amellyel küldői és fogadói alkalmazások hozzáférhetnek a standard szintű Namespace automatikusan a prémium szintű névtér alias-kiszolgálóként van leképezve.

### <a name="what-happens-when-i-abort-the-migration"></a>Mi történik, ha szeretnék megszakíthatja a migrálást?
Az áttelepítés is szakadni használatával a `Abort` parancsot vagy a az Azure Portalon. 

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure Portal

![A folyamat megszakítása – a szinkronizálás megszakítása][]
![megszakíthatja a folyamat – teljes megszakítása][]

Az áttelepítési folyamat leáll, ha megszakítja a szabványostól, a prémium szintű névtérre váltani másolása az entitásokat (témakörök, előfizetések és szűrők), és működésképtelenné válik a párosítást.

A kapcsolati karakterlánc nem frissül, hogy a prémium szintű névtérre mutasson. A meglévő alkalmazások továbbra is működni, mint az áttelepítés megkezdése előtt.

Azonban, nem törölheti az entitásokat a prémium szintű névtér vagy a prémium szintű névtér. Manuálisan törölje az entitásokat, ha úgy döntött, hogy nem a a migrálást.

>[!IMPORTANT]
> Ha úgy dönt, hogy megszakíthatja a migrálást, törölje a korábban kiépített a migráláshoz, hogy az erőforrások nem terheli Namespace premium.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Nem szeretnék az üzenetek kiürítési kell. Mit tegyek?

Előfordulhat, hogy üzeneteket a küldő alkalmazás által küldött és az áttelepítés közben, és elkötelezett a migrálás előtt a tárhely a szabványos Namespace véglegesítve.

Az áttelepítés során a tényleges üzenet adattartalom nem másolja a standard a prémium szintű névtér. Az üzenetek kell manuálisan ürítve, és elküldi a prémium szintű névtérre váltani.

Áttelepítheti a tervezett karbantartás/housekeeping időszak alatt, és nem szeretné manuálisan kiürítési és az üzenetek küldése, ha azonban kövesse az alábbi lépéseket:

1. Állítsa le a küldő alkalmazást. A fogadó alkalmazások feldolgozza az üzeneteket, amelyek jelenleg a standard szintű névtérben, és ki fogja üríteni az üzenetsorba.
1. Után az üzenetsorok és -előfizetések, a standard szintű Namespace az üres, kövesse az áttelepítés a prémium szintű névtérre váltani a standard hajtsa végre a korábban ismertetett eljárás.
1. Az áttelepítés befejezése után újraindíthatja a küldő alkalmazást.
1. A küldők és fogadók automatikusan most már csatlakoznak a prémium szintű névtér esetében.

    >[!NOTE]
    > Nem kell leállítani a migrálás a fogadó alkalmazások.
    >
    > Az áttelepítés befejezése után a fogadó alkalmazások bontsa a kapcsolatot a standard szintű névteret, és a prémium szintű névtér automatikusan csatlakoznak.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet a [standard és prémium szintű üzenetkezelés közötti különbségek](./service-bus-premium-messaging.md).
* További információ a [magas rendelkezésre állás és a Geo-Disaster recovery szempontjai a Service Bus prémium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Áttelepítési kezdőlapja]: ./media/service-bus-standard-premium-migration/1.png
[A telepítő névtér]: ./media/service-bus-standard-premium-migration/2.png
[Névtér beállítása – prémium szintű névtér létrehozása]: ./media/service-bus-standard-premium-migration/3.png
[Névtér beállítása – válassza ki a post áttelepítési neve]: ./media/service-bus-standard-premium-migration/4.png
[Névtér - entitások szinkronizálása – kezdő beállítása]: ./media/service-bus-standard-premium-migration/5.png
[Névtér - szinkronizálás entitások - folyamat beállítása]: ./media/service-bus-standard-premium-migration/8.png
[Váltson a névtér - kapcsoló menü]: ./media/service-bus-standard-premium-migration/9.png
[Kapcsoló névtér - sikeres]: ./media/service-bus-standard-premium-migration/12.png

[A folyamat megszakítása – a szinkronizálás megszakítása]: ./media/service-bus-standard-premium-migration/abort1.png
[A folyamat megszakítása – teljes megszakítása]: ./media/service-bus-standard-premium-migration/abort3.png
