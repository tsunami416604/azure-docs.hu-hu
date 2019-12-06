---
title: Az Azure FarmBeats hibáinak megoldása
description: Ez a cikk az Azure-FarmBeats hibaelhárítását ismerteti.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0a4fb337adfb2f4e6b8edb86ac620103e929c3a8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842135"
---
# <a name="troubleshoot-azure-farmbeats"></a>Az Azure FarmBeats hibáinak megoldása

Ez a cikk az Azure FarmBeats kapcsolatos gyakori problémák megoldásait ismerteti.

További segítségért lépjen kapcsolatba velünk a következő címen: farmbeatssupport@microsoft.com. Ügyeljen arra, hogy tartalmazza az *üzembe helyező. log* fájlt az e-mailben.

A *telepítő. log* fájl letöltéséhez tegye a következőket:

1. Válassza ki a Kiemelt ikont, majd a legördülő listából válassza a **Letöltés**lehetőséget.

    ![Projekt FarmBeats](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

1. A következő ablaktáblán adja meg a *telepítő. log* fájljának elérési útját. Adja meg például a **farmbeats-deployer. log naplófájlt**.

## <a name="sensor-telemetry"></a>Érzékelő telemetria

### <a name="cant-view-telemetry-data"></a>Nem lehet megtekinteni a telemetria-fájlokat

**Tünet**: az eszközök vagy érzékelők üzembe helyezése megtörténik, és a FarmBeats-t csatlakoztatta az eszköz partneréhez, de nem tudja lekérni vagy megtekinteni a telemetria adatait a FarmBeats.

**Javítási művelet**: 

1. Lépjen a FarmBeats Datahub-erőforráscsoporthoz.   
1. Válassza ki az **Event hub** (DatafeedEventHubNamespace) elemet, majd keresse meg a bejövő üzenetek számát.
1. A következő lehetőségek közül választhat:   
   * Ha nincsenek *beérkező üzenetek*, forduljon az eszköz partneréhez.  
   * Ha vannak *Bejövő üzenetek*, forduljon a farmbeatssupport@microsoft.comhoz. Csatolja a Datahub és a Gyorssegéd-naplókat és a rögzített telemetria.

A naplók letöltésének megismeréséhez lépjen a ["naplók manuális gyűjtése"](#collect-logs-manually) szakaszra.  

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Nem rendelkezik az Azure Event Hubs-beli kapcsolatok karakterláncával

**Javítási művelet**: 

1. A Datahub hencegés területen lépjen a partner API-ra.
1. Válassza a **Beolvasás** > **kipróbálás** > **végrehajtás**lehetőséget.
1. Jegyezze fel annak az érzékelő-partnernek a partner-AZONOSÍTÓját, amelyre kíváncsi.
1. Lépjen vissza a partner API-hoz, és válassza a **Get/\<ID >** lehetőséget.
1. Adja meg a partner AZONOSÍTÓját a 3. lépésben, majd válassza a **végrehajtás**lehetőséget.
   
   Az API-válasznak tartalmaznia kell a Event Hubs-kapcsolatok karakterláncát.

### <a name="device-appears-offline"></a>Az eszköz offline állapotban jelenik meg

**Tünetek**: az eszközök telepítve vannak, és a FarmBeats az eszköz partneréhez csatolták. Az eszközök online állapotba kerülnek, és telemetria adatokat küldenek, de offline állapotban jelennek meg.

**Javító művelet**: a jelentési időköz nincs konfigurálva ehhez az eszközhöz. A jelentéskészítési időköz beállításához forduljon az eszköz gyártójához. 

### <a name="error-deleting-a-device"></a>Hiba történt az eszköz törlésekor

Egy eszköz törlésekor a következő gyakori hibák valamelyike merülhet fel:  

**Üzenet**: "az eszköz az érzékelőkre van hivatkozva: egy vagy több érzékelő van társítva az eszközhöz. Törölje az érzékelőket, majd törölje az eszközt. "  

**Jelentés**: az eszköz több, a farmban üzembe helyezett érzékelőhöz van társítva.   

**Javítási művelet**:  

1. Törölje az eszközhöz a Gyorssegéden keresztül társított érzékelőket.  
1. Ha a szenzorokat egy másik eszközhöz szeretné hozzárendelni, kérje meg az eszköz partnerét, hogy tegye ugyanezt.  
1. Törölje az eszközt egy `DELETE API` hívással, és állítsa *igaz*értékre a Force paramétert.  

**Üzenet**: "az eszköz az eszközökön ParentDeviceId van hivatkozva: egy vagy több eszköz van társítva az eszközhöz alárendelt eszközként. Törölje, majd törölje az eszközt. "  

**Jelentés**: az eszközhöz más eszközök tartoznak.  

**Javító művelet**

1. Törölje az adott eszközhöz társított eszközöket.  
1. Törölje az adott eszközt.  

    > [!NOTE]
    > Az eszköz nem törölhető, ha az érzékelők társítva vannak hozzá. A kapcsolódó érzékelők törlésével kapcsolatos további információkért tekintse meg az érzékelők [adatainak beolvasása az érzékelő partnereinktől](get-sensor-data-from-sensor-partner.md)című témakör "az érzékelő törlése" című szakaszát.


## <a name="issues-with-jobs"></a>Problémák a feladatokkal

### <a name="farmbeats-internal-error"></a>FarmBeats belső hiba

**Üzenet**: "FarmBeats belső hiba", további részletekért lásd a hibaelhárítási útmutatót. "

**Javító művelet**: Ez a probléma az adatfolyamatok ideiglenes meghibásodása miatt lehet. Hozza létre újra a feladatot. Ha a hiba továbbra is fennáll, vegye fel a hibaüzenetet a FarmBeats fórumon, vagy forduljon a FarmBeatsSupport@microsoft.comhoz.

## <a name="accelerator-troubleshooting"></a>Gyorssegéd hibaelhárítása

### <a name="access-control"></a>Hozzáférés-vezérlés

**Probléma**: a szerepkör-hozzárendelés hozzáadásakor hibaüzenetet kap.

**Üzenet**: "nem található egyező felhasználó."

**Javítási művelet**: keresse meg azt az e-mail-azonosítót, amelyhez szerepkör-hozzárendelést próbál hozzáadni. Az e-mail-AZONOSÍTÓnak pontosan egyeznie kell az adott felhasználó számára a Active Directoryban regisztrált AZONOSÍTÓval. Ha a hiba továbbra is fennáll, vegye fel a hibaüzenetet a FarmBeats fórumon, vagy forduljon a FarmBeatsSupport@microsoft.comhoz.

### <a name="unable-to-log-in-to-accelerator"></a>Nem lehet bejelentkezni a Gyorssegédbe

**Üzenet**: "hiba: Ön nem jogosult a szolgáltatás meghívására. Az engedélyezéshez forduljon a rendszergazdához. "

**Javítási művelet**: kérje meg a rendszergazdát, hogy engedélyezze a FarmBeats-telepítés elérését. Ezt a RoleAssignment API-k KÖZZÉTÉTELével vagy a Gyorssegéd **Beállítások** paneljének Access Control keresztül végezheti el.  

Ha már engedélyezte a hozzáférést, és megtekinti a hibát, próbálkozzon újra az oldal frissítésével. Ha a hiba továbbra is fennáll, vegye fel a hibaüzenetet a FarmBeats fórumon, vagy forduljon a FarmBeatsSupport@microsoft.comhoz.

![Projekt FarmBeats](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Gyorsító problémák  

**Probléma**: hiba történt a meghatározatlan ok miatt.

**Üzenet**: "hiba: ismeretlen hiba történt."

**Javító művelet**: Ez a hiba akkor fordul elő, ha a lapot túl sokáig hagyja tétlenül. Frissítse az oldalt.  

Ha a hiba továbbra is fennáll, vegye fel a hibaüzenetet a FarmBeats fórumon, vagy forduljon a FarmBeatsSupport@microsoft.comhoz.

**Probléma**: a FarmBeats-gyorsító nem jeleníti meg a legújabb verziót még a FarmBeatsDeployment frissítése után sem.

**Javító művelet**: Ez a hiba a szolgáltatás munkavégző általi megőrzésének a böngészőben való megőrzése miatt fordul elő. Tegye a következőket:
1. Zárjunk be minden olyan böngésző fület, amelyen a Gyorssegéd meg van nyitva, és zárjuk be a böngészőablakot. 
1. Indítsa el a böngésző új példányát, és töltse be újra a Gyorssegéd URI-JÁT. Ez a művelet betölti a Gyorssegéd új verzióját.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: rendszerképekkel kapcsolatos problémák

### <a name="wrong-username-or-password"></a>Helytelen Felhasználónév vagy jelszó

**Sikertelen feladatok üzenete**: "a teljes hitelesítés szükséges az erőforrás eléréséhez."

**Javítási művelet**: 

Folytassa a következők egyikével:
* Futtassa újra a telepítőt a Datahub frissítéséhez a megfelelő felhasználónévvel és jelszóval.
* Futtassa újra a sikertelen feladatot, vagy futtassa a Satellite indexek feladatot egy 5 – 7 napos dátumtartomány esetében, majd ellenőrizze, hogy a művelet sikeres-e.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel hub: helytelen URL vagy hely nem érhető el 

**Sikertelen feladatok üzenete**: "Hoppá, hiba történt. Az elérni próbált lap (átmenetileg) nem érhető el. " 

**Javítási művelet**:
1. Nyissa meg a [Sentinel](https://scihub.copernicus.eu/dhus/) alkalmazást a böngészőben, és ellenőrizze, hogy a webhely elérhető-e. 
1. Ha a webhely nem érhető el, ellenőrizze, hogy a tűzfal, a vállalati hálózat vagy más blokkoló szoftver megakadályozza-e a hozzáférést a webhelyhez, majd hajtsa végre a szükséges lépéseket a Sentinel URL-címének engedélyezéséhez. 
1. Futtassa újra a sikertelen feladatot, vagy futtassa a Satellite indexek feladatot egy 5 – 7 napos dátumtartomány esetében, majd ellenőrizze, hogy a művelet sikeres-e.  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel-kiszolgáló: le karbantartásra

**Sikertelen feladatok üzenete**: "a Kopernikusz Open Access hub hamarosan vissza fog térni! Sajnos a kellemetlenségért elnézést végzünk. Hamarosan ismét elérhető lesz! " 

**Javítási művelet**:

Ez a probléma akkor fordulhat elő, ha a Sentinel-kiszolgálón bármilyen karbantartási tevékenység történik.

1. Ha bármilyen feladatra vagy folyamatra nem kerül sor, mert a karbantartás folyamatban van, egy kis idő elteltével küldje el újra a feladatot. 

   A tervezett vagy nem tervezett Sentinel karbantartási tevékenységekkel kapcsolatos információkért lépjen a [Kopernikusz Open Access hub Hírek](https://scihub.copernicus.eu/news/) webhelyére.  
1. Futtassa újra a sikertelen feladatot, vagy futtassa a Satellite indexek feladatot egy 5 – 7 napos dátumtartomány esetében, majd ellenőrizze, hogy a művelet sikeres-e.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: elérte a kapcsolatok maximális számát

**Sikertelen feladatok üzenete**: "a (z)"\<username > "felhasználó által elért két egyidejű folyamat maximális száma.

**Jelentés**: Ha egy feladatot nem sikerül elérni, mert elérte a kapcsolatok maximális számát, akkor ugyanazt a Sentinel-fiókot használja egy másik szoftver központi telepítésében.

**Javítási művelet**: próbálkozzon a következők valamelyikével:
* Hozzon létre egy új Sentinel-fiókot, majd futtassa újra a telepítőt a Datahub frissítéséhez egy új Sentinel-Felhasználónév és-jelszó használatával.  
* Futtassa újra a sikertelen feladatot, vagy futtasson egy 5 és 7 nap közötti dátumtartományt egy Satellite Indexes feladatot, majd ellenőrizze, hogy a művelet sikeres volt-e.

### <a name="sentinel-server-refused-connection"></a>Sentinel-kiszolgáló: visszautasított kapcsolatok 

**Sikertelen feladatok üzenete**: "a kiszolgáló visszautasította a következőt: http://172.30.175.69:8983/solr/dhus." 

**Javító művelet**: Ez a probléma akkor fordulhat elő, ha a Sentinel-kiszolgálón bármilyen karbantartási tevékenység történik. 
1. Ha bármilyen feladatra vagy folyamatra nem kerül sor, mert a karbantartás folyamatban van, egy kis idő elteltével küldje el újra a feladatot. 

   A tervezett vagy nem tervezett Sentinel karbantartási tevékenységekkel kapcsolatos információkért lépjen a [Kopernikusz Open Access hub Hírek](https://scihub.copernicus.eu/news/) webhelyére.  
1. Futtassa újra a sikertelen feladatot, vagy futtassa a Satellite indexek feladatot egy 5 – 7 napos dátumtartomány esetében, majd ellenőrizze, hogy a művelet sikeres-e.

## <a name="collect-logs-manually"></a>Naplók manuális gyűjtése

[Azure Storage Explorer telepítése és üzembe helyezése]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Azure Data Factory Datahub gyűjtése
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. A **keresőmezőbe** keresse meg a FarmBeats Datahub erőforráscsoportot.

    > [!NOTE]
    > Válassza ki a FarmBeats telepítése során megadott Datahub erőforráscsoportot.

1. Az **erőforráscsoport** irányítópultján keressen rá a *datahublogs\** Storage-fiókra. Keressen például a **datahublogsmvxmq**kifejezésre.  

1. A **Name (név** ) oszlopban válassza ki a Storage-fiókot a **Storage-fiók** irányítópultjának megtekintéséhez.
1. Az **open Azure Storage Explorer** alkalmazás megtekintéséhez a **datahubblogs\*** ablaktáblán válassza a **Megnyitás az Explorerben** lehetőséget.
1. A bal oldali ablaktáblán válassza a **blob-tárolók**lehetőséget, majd válassza a **feladatok naplók**lehetőséget.
1. A **feladatok – naplók** panelen válassza a **Letöltés**lehetőséget.
1. Töltse le a naplókat a számítógép egy helyi mappájába.
1. Küldje el a letöltött. zip fájlt a farmbeatssupport@microsoft.com.

    ![Projekt FarmBeats](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Azure Data Factory-feladatokhoz tartozó naplók gyűjtése a Gyorssegédben 

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. A **keresőmezőbe** keresse meg a FarmBeats-gyorsító erőforráscsoportot.

    > [!NOTE]
    > Válassza ki a FarmBeats üzembe helyezése során megadott gyorssegéd-erőforráscsoportot.

1. Az **erőforráscsoport** irányítópultján keressen rá a *Storage\** Storage-fiókra. Keressen például a **storagedop4k\*** .
1. A **Storage-fiók** irányítópultjának megtekintéséhez válassza ki a Storage-fiókot a **Name (név** ) oszlopban.
1. A Azure Storage Explorer alkalmazás megnyitásához a **storage\*** ablaktáblán válassza a **Megnyitás az Explorerben** lehetőséget.
1. A bal oldali ablaktáblán válassza a **blob-tárolók**lehetőséget, majd válassza a **feladatok naplók**lehetőséget.
1. A **feladatok – naplók** panelen válassza a **Letöltés**lehetőséget.
1. Töltse le a naplókat a számítógép egy helyi mappájába.
1. Küldje el a letöltött. zip fájlt a farmbeatssupport@microsoft.com.


### <a name="collect-datahub-app-service-logs"></a>Datahub app Service-naplók gyűjtése

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. A **keresőmezőbe** keresse meg a FarmBeats Datahub erőforráscsoportot.

    > [!NOTE]
    > Válassza ki a FarmBeats telepítése során megadott Datahub erőforráscsoportot.

1. Az erőforráscsoport területen keressen rá a *datahublogs\** Storage-fiókra. Keressen például a **fordatahublogsmvxmq\*** .
1. A **Storage-fiók** irányítópultjának megtekintéséhez válassza ki a Storage-fiókot a **Name (név** ) oszlopban.
1. A **datahubblogs\*** ablaktáblán válassza a **Megnyitás az explorerben** lehetőséget a Azure Storage Explorer alkalmazás megnyitásához.
1. A bal oldali ablaktáblán válassza a **blob-tárolók**, majd a **appinsights-naplók**elemet.
1. A **appinsights-naplók** panelen válassza a **Letöltés**lehetőséget.
1. Töltse le a naplókat a számítógép egy helyi mappájába.
1. Küldje el a letöltött. zip fájlt a farmbeatssupport@microsoft.com.

### <a name="collect-accelerator-app-service-logs"></a>Gyorsító app Service-naplók gyűjtése

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. A **keresőmezőbe** keresse meg a FarmBeats-gyorsító erőforráscsoportot.

    > [!NOTE]
    > Válassza ki azt a FarmBeats-gyorsító erőforráscsoportot, amelyet a FarmBeats telepítése során adott meg.

1. Az erőforráscsoport területen keressen rá a *storage\** Storage-fiókra. Keressen például a **storagedop4k\*** .
1. A **Storage-fiók** irányítópultjának megtekintéséhez válassza ki a Storage-fiókot a **Name (név** ) oszlopban.
1. A Azure Storage Explorer alkalmazás megnyitásához a **storage\*** ablaktáblán válassza a **Megnyitás az Explorerben** lehetőséget.
1. A bal oldali ablaktáblán válassza a **blob-tárolók**, majd a **appinsights-naplók**elemet.
1. A **appinsights-naplók** panelen válassza a **Letöltés**lehetőséget.
1. Töltse le a naplókat a számítógép egy helyi mappájába.
1. Küldje el a letöltött mappát a farmbeatssupport@microsoft.com.

## <a name="known-issues"></a>Ismert problémák

## <a name="batch-related-issues"></a>Kötegtel kapcsolatos problémák

**Hibaüzenet**: "a megadott előfizetéshez tartozó Batch-fiókok tartományi fiókokra vonatkozó kvótája el lett érve."

**Javítási művelet**: növelje a kvótát, vagy törölje a nem használt batch-fiókokat, és futtassa újra a telepítést.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Azure Active Directory (Azure AD) – kapcsolódó problémák

**Hibaüzenet**: "nem sikerült frissíteni a szükséges beállításokat Azure ad alkalmazás d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: nincs megfelelő jogosultsága a művelet végrehajtásához. Győződjön meg arról, hogy a fenti beállítások megfelelően vannak konfigurálva a Azure AD alkalmazás számára. "

**Jelentés**: az Azure ad-alkalmazás regisztrációs konfigurációja nem fejeződött be megfelelően.  

**Javítási művelet**: kérdezze meg a rendszergazdát (a bérlő olvasási hozzáféréssel rendelkező személyét [) az Azure](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) ad-alkalmazás regisztrációjának létrehozásához. Ez a szkript automatikusan gondoskodik a konfigurációs lépésekről is.

**Hibaüzenet**: "nem sikerült új Active Directory alkalmazást létrehozni a (z)\<alkalmazás neve\>" ebben a bérlőben: már létezik egy olyan objektum, amely azonos értékű a tulajdonságértékek URI azonosítói számára. "

**Jelentés**: már létezik ilyen nevű Azure ad-alkalmazás regisztrálása.

**Javítási művelet**: törölje a meglévő Azure ad-alkalmazás regisztrációját, vagy használja újra a telepítést. Ha újra használja a meglévő Azure AD-regisztrációt, adja át az alkalmazás AZONOSÍTÓját és az ügyfél titkos kulcsát a telepítőnek, és telepítse újra.

## <a name="issues-with-the-inputjson-file"></a>A bemeneti. JSON fájllal kapcsolatos problémák

**Hiba**: hiba történt a bemeneti *. JSON* fájl bemenetének olvasása közben.

**Javító művelet**: Ez a probléma általában a megfelelő *input. JSON* fájl elérési útjának vagy nevének a telepítőhöz való megadásával kapcsolatos hiba miatt fordul elő. Végezze el a megfelelő helyesbítéseket, és próbálja megismételni az újratelepítést.

**Hiba**: hiba történt a *bemeneti. JSON* fájlban lévő értékek elemzésekor.

**Javító művelet**: Ez a probléma többnyire az *input. JSON* fájl helytelen értékei miatt fordul elő. Hajtsa végre a megfelelő javítást, majd próbálja megismételni a telepítést.

## <a name="high-cpu-usage"></a>Magas processzorhasználat

**Hiba**: egy e-mail-riasztást kap, amely *nagy CPU-használati riasztásra*hivatkozik. 

**Javítási művelet**: 
1. Lépjen a FarmBeats Datahub-erőforráscsoporthoz.
1. Válassza ki az App Service-t.  
1. Lépjen a vertikális felskálázás [app Service díjszabása lapra](https://azure.microsoft.com/pricing/details/app-service/windows/), és válassza ki a megfelelő árképzési szintet.

## <a name="next-steps"></a>Következő lépések

Ha továbbra is FarmBeats problémákba ütközik, forduljon a [támogatási fórumhoz](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
