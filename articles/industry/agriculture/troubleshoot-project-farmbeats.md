---
title: Hibakeresés
description: Az Azure FarmBeats hibáinak megoldása.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 22c304b26eddaee4444f6eb12957e2a6fedf7810
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793282"
---
# <a name="troubleshooting"></a>Hibakeresés

A következő szakaszok ismertetik az Azure FarmBeats kapcsolatos gyakori problémákat és azok javításának módját.

További segítségért írjon nekünk a következő címen: farmbeatssupport@microsoft.com, a telepítő. log fájl belefoglalása az e-mailben.

 A következő lépésekkel töltheti le az Deploy. log fájlt:

1. A Kiemelt ikon, és válassza a **Letöltés** lehetőséget a legördülő menüből.

    ![A Project Farm veri](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. A következő képernyőn adja meg a telepítő. log fájljának elérési útját. Például: farmbeats-deployer. log.

## <a name="sensor-telemetry"></a>Érzékelő telemetria

### <a name="telemetry-not-seen"></a>A telemetria nem látható

**Tünet**: az eszközök/érzékelők üzembe helyezése megtörtént, és a FarmBeats csatolva van az eszköz partneréhez. de nem tudja lekérdezni vagy megtekinteni a telemetria a FarmBeats.

**Javítási művelet**: látogasson el a Azure Portalra, és kövesse az alábbi lépéseket:

1. Nyissa meg a FarmBeats adatközpont-erőforráscsoportot.   
2. Válassza az **Event hub** (DatafeedEventHubNamespace...) lehetőséget, és keresse meg a bejövő üzenetek számát.   
3. Ha nincsenek **beérkező üzenetek**, forduljon az eszköz partneréhez.  
4. Ha vannak **Bejövő üzenetek**, lépjen kapcsolatba farmbeatssupport@microsoft.com adatközponttal és gyorssegéd-naplókkal, valamint rögzített telemetria.

A naplók letöltésének megismeréséhez tekintse meg a dokumentum [naplók szakaszát](#collect-logs-manually) .  

### <a name="dont-have-the-eventhub-connection-string"></a>Nem rendelkezik a Eventhub-kapcsolatok karakterláncával

**Javítási művelet**: látogasson el a Datahub hencegő oldalra, és kövesse az alábbi lépéseket:
1. Navigáljon a partner API-hoz
2. Kattintson a GET-> kipróbálás – > végrehajtás lehetőségre
3. Jegyezze fel annak az érzékelő-partnernek a Partner-azonosítóját, amelyre kíváncsi
4. Lépjen vissza a partner API-ra, és kattintson a GET/{ID} elemre.
5. Határozza meg a 3. lépésben szereplő azonosítót, és kattintson a végrehajtás gombra.
6. Az API-válasznak tartalmaznia kell a EventHub-kapcsolatok sztringjét.

### <a name="device-appears-offline"></a>Az eszköz offline állapotban jelenik meg

**Tünetek**: az eszközök telepítve vannak, és az FarmBeats társítva van. Az eszközök online állapotba kerülnek, és telemetria adatokat küldenek, de offline állapotban jelennek meg.

**Javító művelet**: a jelentési időköz nincs konfigurálva ehhez az eszközhöz. A jelentéskészítési időköz beállításához forduljon az eszköz gyártójához. 

### <a name="error-deleting-a-resource"></a>Hiba történt az erőforrás törlésekor

Az alábbi gyakori hibák fordulnak elő egy eszköz törlésekor:  

**Üzenet**: az eszköz az érzékelőkre hivatkozik: egy vagy több érzékelő van társítva az eszközhöz. Törölje az érzékelőket, majd törölje az eszközt.  

**Jelentés**: az eszköz a farmban üzembe helyezett több érzékelőhöz van társítva.   

**Javítási művelet**:  

1. Törölje az eszközhöz társított érzékelőket a gyorssegéden keresztül.  
2. Ha az érzékelőket egy másik eszközhöz szeretné hozzárendelni, kérje meg az eszköz partnerét, hogy tegye ugyanezt.  
3. Törölje az eszközt a DELETE API-hívással, ha a Force paraméter értéke "true".  

**Üzenet**: az eszköz az eszközökön ParentDeviceId van hivatkozva: egy vagy több eszköz van társítva az eszközhöz alárendelt eszközként. Törölje, majd törölje az eszközt.  

**Jelentés**: az eszközön más eszközök is vannak társítva  

**Javító művelet**

1. Az adott eszközhöz társított eszközök törlése  
2. Az adott eszköz törlése  

    > [!NOTE]
    > Az eszköz nem törölhető, ha az érzékelők társítva vannak hozzá. A társított érzékelők törlésével kapcsolatos további információkért lásd: [érzékelők törlése](get-sensor-data-from-sensor-partner.md) az érzékelő adatainak beolvasása című fejezetben.


## <a name="issues-with-jobs"></a>Problémák a feladatokkal

### <a name="farmbeats-internal-error"></a>FarmBeats belső hiba

**Üzenet**: FarmBeats belső hiba, további részletekért tekintse meg a hibaelhárítási útmutatót.

**Javító művelet**: Ennek oka az lehet, hogy az adatfolyamatban ideiglenes Hiba történt. Hozza létre újra a feladatot. Ha a hiba továbbra is fennáll, vegye fel a hibát a FarmBeats fórumba, vagy forduljon a FarmBeatsSupport@microsoft.comhoz.

## <a name="accelerator-troubleshooting"></a>Gyorssegéd hibaelhárítása

### <a name="access-control"></a>Access Control

**Hiba történt a szerepkör-hozzárendelés hozzáadásakor**

**Üzenet**: nem található egyező felhasználó

**Javítási művelet**: keresse meg azt az e-mail-azonosítót, amelyhez szerepkör-hozzárendelést szeretne végezni. Az e-mail-AZONOSÍTÓnak pontosan egyeznie kell az adott felhasználó számára a Active Directoryban regisztráltal. Ha a hiba továbbra is fennáll, vegye fel a hibát a FarmBeats fórumban, vagy forduljon FarmBeatsSupport@microsoft.com

### <a name="unable-to-log-in-to-accelerator"></a>Nem lehet bejelentkezni a Gyorssegédbe

**Üzenet**: hiba: Ön nem jogosult a szolgáltatás meghívására. Az engedélyezéshez forduljon a rendszergazdához.

**Javítási művelet**: kérje meg a rendszergazdát, hogy engedélyezze a FarmBeats-telepítés elérését. Ezt a RoleAssignment API-k, illetve a Gyorssegéd beállítások paneljének Access Control segítségével végezheti el.  

Ha már felvette a hibát, és megtekinti ezt a hibát, próbálkozzon újra az oldal frissítésével.  Ha a hiba továbbra is fennáll, vegye fel a hibát a FarmBeats fórumba, vagy forduljon a FarmBeatsSupport@microsoft.comhoz.

![A Project Farm veri](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>Gyorssegéd – ismeretlen hiba történt  

**Üzenet**: hiba: ismeretlen hiba történt.

**Javító művelet**: Ez akkor fordul elő, ha a lapot túl sokáig hagyja tétlenül. Frissítse az oldalt.  

Ha a hiba továbbra is fennáll, vegye fel a hibát a FarmBeats fórumban, vagy lépjen kapcsolatba FarmBeatsSupport@microsoft.com

**A FarmBeats-gyorsító nem jeleníti meg a legújabb verziót még a FarmBeatsDeployment frissítése után is**

**Javító művelet**: Ez a szolgáltatás munkavégző általi megőrzésének a böngészőben való megőrzése miatt történik.
Zárjunk be minden olyan böngésző fület, amelynél a Gyorssegéd meg van nyitva, és zárjuk be a böngészőablakot. Indítsa el a böngésző új példányát, és töltse be újra a Gyorssegéd URI-JÁT. Ekkor a rendszer betölti a Gyorssegéd új verzióját.

## <a name="sentinel-imagery-related-issues"></a>Sentinel-képekkel kapcsolatos problémák

### <a name="sentinel-wrong-username-or-password"></a>Sentinel rossz Felhasználónév vagy jelszó

**Sikertelen feladatok üzenete**; Az erőforrás eléréséhez teljes hitelesítés szükséges.

**Javítási művelet**: futtassa újra a telepítőt az adatközpont megfelelő felhasználónévvel és jelszóval való frissítéséhez.

**Javítási művelet**: futtassa újra a sikertelen feladatot, vagy futtasson egy Satellite Indexes feladatot a 5-7 napos dátumtartomány esetében, és ellenőrizze, hogy a feladatok sikeresek-e.

### <a name="sentinel-hub-wrong-url-or-not-accessible"></a>Sentinel hub helytelen URL-cím vagy nem érhető el 

**Sikertelen feladatok üzenete**: Hoppá, hiba történt. Az elérni próbált lap (átmenetileg) nem érhető el. 

**Javítási művelet**:
1.  Nyissa meg a Sentinel URL-címét (https://scihub.copernicus.eu/dhus/) a böngészőben, és ellenőrizze, hogy a webhely elérhető-e. 
2.  Ha a webhely nem érhető el, ellenőrizze, hogy a tűzfal vagy a vállalati hálózat stb. blokkolja-e a webhelyet, és hajtsa végre a fenti URL-cím engedélyezéséhez szükséges lépéseket. 
3.  Futtassa újra a sikertelen feladatot, vagy futtasson egy Satellite Indexes feladatot a 5-7 napos dátumtartomány esetében, és ellenőrizze, hogy a feladatok sikeresek-e.  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel-kiszolgáló leállása karbantartáshoz

**Sikertelen feladatok üzenete**: a Kopernikusz Open Access hub hamarosan vissza fog térni! Sajnos a kellemetlenségért elnézést végzünk. Hamarosan ismét online fogunk! 

**Javítási művelet**:

1.  Ez a probléma akkor fordulhat elő, ha a Sentinel-kiszolgálón bármilyen karbantartási tevékenység történik. 
2.  Ha egy adott feladatnak vagy folyamatnak a fenti ok miatt meghiúsul, egy kis idő elteltével küldje el újra a feladatot. 
3.  A felhasználó meglátogathatja https://scihub.copernicus.eu/news/ a tervezett/nem tervezett Sentinel karbantartási tevékenységekkel kapcsolatos információk megtekintéséhez.  
4.  Futtassa újra a sikertelen feladatot, vagy futtasson egy Satellite Indexes feladatot a 5-7 napos dátumtartomány esetében, és ellenőrizze, hogy a feladatok sikeresek-e.

### <a name="sentinel-maximum-number-of-connections-reached"></a>A Sentinel elérte a kapcsolatok maximális számát

**Sikertelen feladatok üzenete**: a "<username>" felhasználó által elért két egyidejű folyamat maximális száma 

**Javító művelet**
1.  Ha bármelyik feladatot a fenti ok miatt nem sikerül végrehajtani, ugyanazt a Sentinel-fiókot használja egy másik telepítésben/szoftverben. 
2.  A felhasználó létrehozhat új Sentinel-fiókot, és újból futtathatja a telepítőt az adatközpont új Sentinel-felhasználónévvel és jelszóval való frissítéséhez.  
3.  Futtassa újra a sikertelen feladatot, vagy futtasson egy Satellite Indexes feladatot a 5-7 napos dátumtartomány esetében, és ellenőrizze, hogy a feladatok sikeresek-e.

### <a name="sentinel-server-refused-connection"></a>A Sentinel-kiszolgáló elutasította a kapcsolatokat 

**Sikertelen feladatok üzenete**:

A kiszolgáló visszautasította a következő helyen lévő kapcsolatokat: http://172.30.175.69:8983/solr/dhus 

**Javító művelet**: Ez a probléma akkor fordulhat elő, ha a Sentinel-kiszolgálón bármilyen karbantartási tevékenység történik. 
1.  Ha egy adott feladatnak vagy folyamatnak a fenti ok miatt meghiúsul, egy kis idő elteltével küldje el újra a feladatot. 
2.  A felhasználó meglátogathatja https://scihub.copernicus.eu/news/ a tervezett/nem tervezett Sentinel karbantartási tevékenységekkel kapcsolatos információk megtekintéséhez.  
3.  Futtassa újra a sikertelen feladatot, vagy futtasson egy Satellite Indexes feladatot a 5-7 napos dátumtartomány esetében, és ellenőrizze, hogy a feladatok sikeresek-e.


## <a name="collect-logs-manually"></a>Naplók manuális gyűjtése

Azure Storage Explorer [telepítéséhez és üzembe helyezéséhez]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) .

### <a name="how-to-collect-data-hub-adf-job-logs"></a>Az adatközponti ADF-feladatok naplóinak összegyűjtése
1. Bejelentkezés https://portal.azure.com
2. A **Keresés** szövegmezőben keresse meg a FarmBeats adatközpont-erőforráscsoport kifejezést.

    > [!NOTE]
    > Válassza ki azt az adatközpont-erőforráscsoportot, amely a FarmBeats telepítésének időpontjában volt megadva.

Az erőforráscsoport irányítópultján keresse meg a (datahublogs...) Storage-fiókot. Például: datahublogsmvxmq  

1.  A **Storage-fiók** irányítópultjának megtekintéséhez válassza ki a Storage-fiókot a **Name (név** ) oszlopban.
2.  A **megnyitás Azure Storage Explorer** alkalmazás megtekintéséhez a (datahubblogs...) lapon válassza a **Megnyitás az Explorerben** lehetőséget.
3.  A bal oldali panelen (datahubblogs...), **blob-tárolók**lapon válassza a **feladatok – naplók**lehetőséget.
4.  A **feladatok – naplók** lapon válassza a **Letöltés**lehetőséget.
5.  Válassza ki a helyet, ahová le szeretné tölteni a naplókat a számítógép egy helyi mappájába.
6.  Küldje el a letöltött zip-fájlt a farmbeatssupport@microsoft.com

    ![A Project Farm veri](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>A gyorsító ADF-feladatok naplóinak összegyűjtése

1.  Bejelentkezés https://portal.azure.com
2.  A **Keresés** szövegmezőben keressen rá a FarmBeats-gyorsító erőforráscsoport kifejezésre.

    > [!NOTE]
    > Válassza ki a FarmBeats-telepítés időpontjában megadott gyorssegéd-erőforráscsoportot.

3.  Az erőforráscsoport irányítópultján keressen a Storage... elemre. Storage-fiók. Például: storagedop4k
4.  A Storage-fiók irányítópultjának megtekintéséhez válassza ki a Storage-fiókot a **Name (név** ) oszlopban.
5.  A Megnyitás Azure Storage Explorer alkalmazás megtekintéséhez a (Storage...) lapon válassza a **Megnyitás az Explorerben** lehetőséget.
6.  A bal oldali panelen < tároló...), blob- **tárolók**elemre, majd válassza a **feladatok – naplók**lehetőséget.
7.  A **feladatok – naplók** lapon válassza a **Letöltés**lehetőséget.
8.  Válassza ki a helyet, ahová le szeretné tölteni a naplókat a számítógép egy helyi mappájába.
9.  Küldje el a letöltött zip-fájlt a farmbeatssupport@microsoft.com


### <a name="how-to-collect-data-hub-app-service-logs"></a>Az adatközpont app Service-naplók összegyűjtése

1.  Bejelentkezés https://portal.azure.com
2.  A **Keresés** szövegmezőben keresse meg a FarmBeats adatközpont-erőforráscsoport kifejezést.

    > [!NOTE]
    > Válassza ki azt az adatközpont-erőforráscsoportot, amely a FarmBeats telepítésének időpontjában volt megadva.

3.  Az erőforráscsoporthoz keresse meg a (datahublogs....) Storage-fiókot. Például: datahublogsmvxmq
4.  A **Storage-fiók** irányítópultjának megtekintéséhez válassza ki a Storage-fiókot a **Name (név** ) oszlopban.
5.  A **megnyitás Azure Storage Explorer** alkalmazás megtekintéséhez a (datahubblogs...) lapon válassza a **Megnyitás az Explorerben** lehetőséget.
6.  A bal oldali panelen (datahubblogs...), blob- **tárolók**, majd válassza a appinsights-naplók lehetőséget.
7.  A appinsights-naplók lapon válassza a **Letöltés**lehetőséget.
8.  Válassza ki azt az elérési utat, amellyel le szeretné tölteni a naplókat a számítógép egy helyi mappájába.
9.  Küldje el a letöltött mappát farmbeatssupport@microsoft.com

### <a name="how-to-collect-accelerator-app-service-logs"></a>A gyorsított app Service-naplók összegyűjtése

1.  Bejelentkezés https://portal.azure.com
2.  A **Keresés**területen keressen rá a FarmBeats-gyorsító erőforráscsoport kifejezésre.

    > [!NOTE]
    > Válassza ki azt a Farmbeats-gyorsító erőforráscsoportot, amelyet a FarmBeats üzembe helyezésének időpontjában biztosít.

3.  Az erőforráscsoporthoz keresse meg a (Storage...) Storage-fiókot. Például: storagedop4k
4.  A **Storage-fiók** irányítópultjának megtekintéséhez válassza ki a Storage-fiókot a **Name (név** ) oszlopban.
5.  A **megnyitás Azure Storage Explorer** alkalmazás megtekintéséhez a (Storage...) lapon válassza a **Megnyitás az Explorerben** lehetőséget.
6.  A bal oldali panelen (Storage...), blob- **tárolók**területen válassza a appinsights-naplók lehetőséget.
7.  A appinsights-naplók lapon válassza a **Letöltés**lehetőséget.
8.  Válassza ki a helyet, ahová le szeretné tölteni a naplókat a számítógép egy helyi mappájába.
9.  Küldje el a letöltött mappát farmbeatssupport@microsoft.com

## <a name="known-issues"></a>Ismert problémák

## <a name="batch-related-issues"></a>Kötegtel kapcsolatos problémák

**Hiba**: a rendszer elérte a megadott előfizetéshez tartozó Batch-fiókok tartományi fiókra vonatkozó kvótáját.

**Javítási művelet**: növelje a kvótát, vagy törölje a nem használt batch-fiókokat, majd futtassa újra a telepítést.

### <a name="azure-active-directory-related-issues"></a>Azure Active Directory kapcsolódó problémák

**Hiba**: nem sikerült frissíteni a szükséges beállításokat a Azure ad alkalmazás d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: nincs megfelelő jogosultsága a művelet végrehajtásához. Győződjön meg arról, hogy a fenti beállítások megfelelően vannak konfigurálva a Azure AD alkalmazás.

**Jelentés**: az Azure ad-alkalmazás regisztrációs konfigurációja nem történt meg megfelelően.  

**Javítási művelet**: kérdezze meg a rendszergazdát (a bérlő olvasási hozzáférésével) az Azure ad-alkalmazás regisztrációjának létrehozásához használt [parancsfájl](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) használatával. Ez a szkript automatikusan gondoskodik a konfigurációs lépésekről is.

**Hiba**: nem sikerült létrehozni a (z) "dummyname" nevű új Active Directory alkalmazást ebben a bérlőben: már létezik egy azonos értékkel rendelkező objektum az URI azonosítóhoz.

**Jelentés**: már létezik ilyen nevű Azure ad-alkalmazás regisztrálása.

**Javítási művelet**: törölje a meglévő Azure ad-alkalmazás regisztrációját, vagy használja újra a telepítéshez. Ha a meglévő Azure AD-t használja, adja át az alkalmazás AZONOSÍTÓját és az ügyfél titkos kulcsát a telepítőnek, és telepítse újra.

## <a name="inputjson-related-issues"></a>A input. JSON kapcsolatos problémái

**Hiba történt** a bemeneti. JSON fájl bemenetének olvasása közben

**Javító művelet**: Ez a probléma többnyire a helytelen beviteli JSON-elérési út vagy a telepítőhöz tartozó név megadása miatt fordul elő. Végezze el a megfelelő helyesbítéseket, majd próbálkozzon újra a telepítéssel.

**Hiba történt a JSON-bevitel elemzésekor**

**Javító művelet**: Ez a probléma többnyire a bemeneti JSON-fájl helytelen értékei miatt fordul elő. Végezze el a megfelelő helyesbítéseket, és próbálkozzon újra a telepítéssel.

## <a name="high-cpu-usage"></a>Magas processzorhasználat

**Hiba**: e-mail-riasztást kap a nagy CPU-használati riasztásra vonatkozólag. 

**Javítási művelet**: 
1.  Nyissa meg a FarmBeats adatközpont-erőforráscsoportot.
2.  Válassza ki az App Service-t.  
3.  Lépjen a vertikális felskálázás (App Service terv) lehetőségre, és válasszon ki egy megfelelő [árképzési szintet](https://azure.microsoft.com/pricing/details/app-service/windows/)

## <a name="next-steps"></a>Következő lépések

Ha továbbra is problémákba ütközik, lépjen kapcsolatba velünk a [támogatási fórumon](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
