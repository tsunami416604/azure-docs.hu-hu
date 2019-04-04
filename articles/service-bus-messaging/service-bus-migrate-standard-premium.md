---
title: Meglévő Azure Service Bus standard szintű névterek áttelepítéséhez a prémium szintű |} A Microsoft Docs
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
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: 7b153c36e10f1d4e2be2a0cf42f998c31cb6473a
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896425"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-premium-tier"></a>A prémium szintű meglévő Azure Service Bus standard szintű névterek áttelepítése

Korábban az Azure Service Bus névtér csak a Standard szinten érhető el. Ezek olyan több-bérlős beállítását, amely az alacsony átviteli sebesség és a fejlesztői környezetbe lettek optimalizálva.

A közelmúltban Azure Service Bus névterenként előre jelezhető késés és nagyobb átviteli sebesség, fix áron nagy adattovábbítási kapacitása és az éles környezetekre van optimalizálva, amelyek dedikált erőforrásokat kínáló prémium szintű kínáló kibontva További vállalati szolgáltatásokat igénylő.

Az alábbi eszközök a meglévő Standard szintű névteret át kell helyezni a prémium szint lehetővé teszi.

>[!WARNING]
> Áttelepítés célja standard szintű Service Bus-névtérhez kell ***frissített*** a prémium szintre.
>
> Az áttelepítési eszközkészlet ***nem*** támogatja az alacsonyabb verzióra való visszatérést.
>[!NOTE]
> Az áttelepítés történik hivatott ***helyen***.
>
> Ez azt jelenti, hogy meglévő küldő és fogadó-alkalmazások nem igénylik bármilyen kód vagy a konfiguráció módosítása.
>
> A meglévő kapcsolati karakterlánc az új prémium szintű névtér automatikusan fog mutatni.
>
> Emellett a standard szintű névtérben lévő összes entitáshoz van **átmásolt** a prémium szintű névtér az áttelepítés során.
>
>
> Támogatjuk a ***1000 entitások / üzenetkezelési egység*** a prémium szintű, így azonosíthatja a hány üzenetkezelési egység van szüksége, indítsa el az a jelenlegi Standard névtérben rendelkező entitások száma.

## <a name="migration-steps"></a>Migrálási lépések

>[!IMPORTANT]
> Nincsenek társított az áttelepítési folyamat bizonyos korlátozásokkal. Minden kért teljes Ismerkedjen meg a folyamat lépései lehetőségeit a hibák csökkentése érdekében.

A konkrét részletes áttelepítési folyamat részleteit az alábbi útmutatók a.

A logikai lépései a következők-

1. Hozzon létre egy új névteret.
2. Párosítsa a Standard és prémium szintű névtér egymáshoz.
3. Szinkronizálási (másolási hozzáadásánál) entitások standardról prémium szintű névtérre váltani
4. A migrálás véglegesítése
5. A standard szintű névtérben, a névtér áttelepítés utáni neve használatával kiürítési entitások
6. A standard szintű névtér törlése

>[!NOTE]
> Miután a migrálás véglegesítése után, nagyon fontos a régi Standard névtér eléréséhez, és az üzenetsorok és -előfizetések ki kiürítési.
>
> Az üzenetek rendelkezik lett ürítve miután azok küldhető az új prémium szintű névtérre váltani a fogadó alkalmazások általi feldolgozásának.
>
> Miután az üzenetsorok és -előfizetések rendelkezik lett ürítve, javasoljuk, hogy a régi Standard névtér törlése. Ön nem lehet kellene azt!

### <a name="migrate-using-azure-cli-or-powershell"></a>Migrálása az Azure CLI vagy a PowerShell használatával

A standard szintű Service Bus-névtér áttelepítése az Azure parancssori felület vagy PowerShell eszközzel prémium, tekintse meg az alábbi útmutató.

1. Hozzon létre egy új prémium szintű Service Bus-névteret. Hivatkozhat a [Azure Resource Manager-sablonok](service-bus-resource-manager-namespace.md) vagy [az Azure Portal](service-bus-create-namespace-portal.md). Ügyeljen arra, hogy a "Prémium" Válassza ki a **serviceBusSku** paraméter.

2. Állítsa be az alábbi környezeti változókat, egyszerűsítheti az áttelepítési parancsokat.
   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the Premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the Standard namespace>
   ```

    >[!IMPORTANT]
    > Az áttelepítés utáni neve (post_migration_dns_name) a régi standard szintű névtér a migrálás után eléréséhez használható. Ez az üzenetsorok és az előfizetések kiürítési, és ezután törölje a névteret kell használnia.

3. **Pár** a Standard és prémium szintű névterek és **szinkronizálás indítása** használja az alábbi parancsot:

    ```
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


4. Az áttelepítés használatával állapotának ellenőrzéséhez az alábbi parancsot:
    ```
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Az áttelepítés akkor tekinthető mikor
    * MigrationState = "Aktív"
    * pendingReplicationsOperationsCount = 0
    * provisioningState "Succeeded" =

    Ez a parancs a migrálási konfiguráció is megjeleníti. Kérjük, győződjön meg arról, hogy az értékek vannak beállítva az előző ellenőrizze deklarálva.

    Ezenkívül is ellenőrizheti a portálon győződjön meg arról, hogy az üzenetsorok és témakörök létrehozott, és a standard szintű névtér létezett, hogy mi ezek megegyeznek a prémium szintű névtér.

5. A migrálás véglegesítése a teljes az alábbi parancs végrehajtásával:
   ```
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-using-azure-portal"></a>Migrálása az Azure portal használatával

Áttelepítés az Azure Portalon keresztül ugyanazon logikai folyamata megegyezik a parancsokkal rendelkezik. Tekintse meg a folyamat részletes útmutató a portal használatával történő áttelepítés alatt.

1. Válassza ki a **"Áttelepítés prémium szintű"** menüpont a navigációs menüben a bal oldali panelen. Kattintson a **"Első lépések"** gombra kattintva lépjen tovább a következő lapon.
    ![Áttelepítési kezdőlapja][]

2. Teljes **telepítő**.
   ![A telepítő névtér][]
   1. Hozzon létre, és rendelje hozzá a prémium szintű névtér áttelepítése a meglévő standard szintű névtérre.
        ![Névtér beállítása – prémium szintű névtér létrehozása][]
   2. Válassza ki a **(a Migrálás után name)** a migrálás befejezése után a standard szintű névtér szerint eléréséhez.
        ![Névtér beállítása – válassza ki a post áttelepítési neve][]
   3. Kattintson a **'Tovább'** a folytatáshoz.
3. **Szinkronizálási** entitások között a Standard és prémium szintű névtér.
    ![Névtér - entitások szinkronizálása – kezdő beállítása][]

   1. Kattintson a **szinkronizálás indítása** megkezdéséhez az entitások szinkronizálása.
   2. Kattintson a **"Igen"** az előugró megerősíti a szinkronizálás elindításához.
   3. Várja meg, amíg a **szinkronizálási** befejeződött. Az állapot szeretné elérhetővé tenni az állapotsorban.
        ![Névtér - szinkronizálás entitások - folyamat beállítása][]
        >[!IMPORTANT]
        > Ha szeretne **megszakítása** bármilyen okból, tekintse át a megszakítási folyamat a jelen dokumentum a gyakori kérdésekkel foglalkozó szakaszban.
   4. A szinkronizálás befejeződése után kattintson a **'Tovább'** gombra a lap alján.

4. Tekintse át a módosításokat az összefoglalás lapon.
    ![Váltson a névtér - kapcsoló menü][]

5. Kattintson a **"Az áttelepítés befejezése"** váltson a névterek és az áttelepítés befejezéséhez.
    ![Kapcsoló névtér - sikeres][]

## <a name="faqs"></a>Gyakori kérdések

### <a name="what-happens-when-the-migration-is-committed"></a>Mi történik, ha az áttelepítés számára fontos?

A migrálás véglegesítése után a standard szintű névtér mutatott kapcsolati karakterláncot a prémium szintű névtérre váltani fog mutatni.

A küldő és fogadó alkalmazások bontsa a kapcsolatot a standard szintű Namespace, és a automatikusan újracsatlakozzanak a prémium szintű névtér.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Mit a teendő, a Standard, prémium szintű az áttelepítés befejezése után?

A Standard, prémium szintű áttelepítési biztosítja, hogy az entitás-metaadatok (témakörök, előfizetések, szűrőket, et al.) vannak keresztül másolja a standardról prémium szintű névtérre váltani. Az állapotüzenet-adatokat, amely a standard szintű névtér volt véglegesített nem át van másolva a standardról prémium szintű névtér.

Ezért a standard szintű névtér előfordulhat, hogy néhány üzenetet küldött, és miközben folyamatban volt a migrálás véglegesítése. Ezeket az üzeneteket kell manuálisan, a standard szintű Namespace ürítve és keresztül küldött, a prémium szintű Namespace manuálisan.

Ehhez, ***kell*** egy konzolalkalmazás vagy parancsfájlt, amely a standard szintű névtér entitások kiürítési a **Post áttelepítési DNS-név** , hogy a migrálás parancsok megadott, és elküldheti ezeket üzeneteket a a prémium szintű Namespace úgy, hogy a hozzáadásuk dolgozhassák fel.

Ha üzeneteket kell lett ürítve, folytassa a standard szintű névtér törlése.

>[!IMPORTANT]
> Ne feledje, hogy miután a standard szintű névtér üzeneteit rendelkezik lett ürítve, hogy **kell** a standard szintű névtér törlése.
>
> Ez azért fontos, mert a kapcsolati karakterláncot, amely kezdetben említett a standard szintű névtér most ténylegesen a prémium szintű névtér hivatkozik. Meg nem lehet kellene a standard szintű Namespace többé.
>
> Egy későbbi időpontban mindebben segítségével telepített át, a standard szintű névtér törlése csökkenti. 

### <a name="how-much-downtime-do-i-expect"></a>Mennyi állásidő tegye várható?
Az áttelepítési folyamat a fent leírt célja az alkalmazások a várható állásidő csökkentése érdekében. Ez a kapcsolati karakterláncot, a küldő és fogadó kérelmek átirányítása az új prémium szintű névteret használó felügyelniük történik.

Az alkalmazás által tapasztalt üzemszünet, hogy a prémium szintű névtérre mutasson a DNS-bejegyzés frissítéséhez szükséges idő mennyisége korlátozva.

Ez is kell azt feltételezi, hogy ***körülbelül 5 perc***.

### <a name="do-i-have-to-make-any-configuration-changes-while-performing-the-migration"></a>Rendelkezik a migrálás végrehajtása közben semmilyen konfigurációs módosítást?
Nem, nem változtak kódokat, illetve konfigurációs az áttelepítés végrehajtásához szükséges. A kapcsolati karakterláncot, amellyel küldői és fogadói alkalmazások hozzáférhetnek a standard szintű Namespace automatikusan hozzá van rendelve, aki egy **alias** a prémium szintű Namespace számára.

### <a name="what-happens-when-i-abort-the-migration"></a>Mi történik, ha szeretnék megszakíthatja a migrálást?
Áttelepítés vagy a "Megszakítás" paranccsal vagy az Azure Portalon is megszakítja. 

#### <a name="azure-cli-or-powershell"></a>Az Azure CLI vagy a PowerShell használatával

    az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace

#### <a name="azure-portal"></a>Azure Portal

![A folyamat megszakítása – a szinkronizálás megszakítása][]
![megszakíthatja a folyamat – teljes megszakítása][]

Ha az áttelepítési folyamat végrehajtása megszakadt, ténylegesen az entitásokat (témakörök, előfizetések és szűrők) keresztül másolja a folyamat megszakítása standardról prémium szintű névtérre váltani, és működésképtelenné válik a párosítást.

A kapcsolati karakterlánc **nem** frissítve, hogy a prémium szintű névtérre mutasson. A meglévő alkalmazások továbbra is működni, mint az áttelepítés megkezdése előtt.

Azonban ez **nem** törölheti az entitásokat a prémium szintű névtér vagy a prémium szintű névtér magát. Azt manuálisan hajtható végre, ha Ön döntött, hogy nem halad a migrálást követően.

>[!IMPORTANT]
> Ha úgy dönt, hogy megszakíthatja a migrálást, kérjük, törölje a korábban kiépített az áttelepítés prémium szintű Namespace úgy, hogy az erőforrások nem terheli.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Nem szeretnék az üzenetek kiürítési kell. Mit tegyek?

Előfordulhat, hogy a küldő alkalmazás által küldött és az áttelepítés során a tárhely a szabványos Namespace véglegesített üzeneteket, és közvetlenül az áttelepítés előtt véglegesítve.

Tekintettel arra, hogy az áttelepítés során a tényleges üzenet adattartalom nem át van másolva standardról prémium szintű, ezeket kell manuálisan ürítve, és elküldi a prémium szintű névtérre váltani.

Azonban ha egy tervezett karbantartási/housekeeping időszak alatt telepítheti át, és nem szeretné manuálisan kiürítési, és az üzenetek küldéséhez, kövesse az alábbi lépések szükségesek:

1. Állítsa le a küldő alkalmazást, és a fogadók a üzeneteket feldolgoznia, amelyeket jelenleg a standard szintű névtérben, és a várólista kiürítési engedélyezése.
2. Miután az üzenetsorok és -előfizetések, a standard szintű Namespace az üres, kövesse a hajtsa végre az áttelepítés Standard, prémium szintű névtér a fent leírt eljárást.
3. Az áttelepítés befejezése után újraindíthatja a küldő alkalmazást.
4. A küldők és fogadók automatikusan most már csatlakoznak a prémium szintű névtér esetében.

    >[!NOTE]
    > A migrálás a fogadó nem kell leállítani.
    >
    > Az áttelepítés befejeződése után a fogadók bontsa a kapcsolatot a standard szintű névteret, és a prémium szintű névtér automatikusan csatlakoznak.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet a [Standard és prémium szintű üzenetkezelés közötti különbségek](./service-bus-premium-messaging.md)
* További információk a magas rendelkezésre állás és a Geo-Diaster helyreállítási szempontjai az Service Bus prémium szintű [Itt](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

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
