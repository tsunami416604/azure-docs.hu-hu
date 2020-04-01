---
title: Az Azure FarmBeats hibaelhárítása
description: Ez a cikk az Azure FarmBeats hibaelhárítását ismerteti.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 7a31eece6629558b14b614853addce59642e698b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422729"
---
# <a name="troubleshoot"></a>Hibaelhárítás

Ez a cikk megoldásokat kínál a gyakori Azure FarmBeats problémák.

További segítségért lépjen farmbeatssupport@microsoft.comkapcsolatba velünk a . Győződjön meg arról, hogy a **deployer.log** fájlt is szerepeljenek az e-mailben.

A **deployer.log** fájl letöltéséhez tegye a következőket:

1. Jelentkezzen be az **Azure Portalra,** és válassza ki az előfizetést és az Azure AD-bérlőt.
2. Indítsa el a Cloud Shellt az Azure Portal felső navigációs szakaszából.
3. Válassza a **Bash** lehetőséget az előnyben részesített Cloud Shell-élményként.
4. Jelölje ki a kiemelt ikont, majd a legördülő listában válassza a **Letöltés lehetőséget.**

    ![FarmBeats projekt](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. A következő ablaktáblán adja meg a **deployer.log** fájl elérési útját. Írja be például **a farmbeats-deployer.log fájl beírását.**

## <a name="sensor-telemetry"></a>Érzékelő telemetriai adatai

### <a name="cant-view-telemetry-data"></a>Nem lehet megtekinteni a telemetriai adatokat

**Jelenség:** Az eszközök vagy érzékelők telepítve vannak, és összekapcsolta a FarmBeats-t az eszközpartnerével, de nem tudja levenni vagy megtekinteni a telemetriai adatokat a FarmBeats-en.

**Korrekciós intézkedés**:

1. Nyissa meg a FarmBeats Datahub erőforráscsoportot.   

2. Válassza ki az **Event Hub** (DatafeedEventHubNamespace), majd ellenőrizze a bejövő üzenetek száma.

3. A következő lehetőségek közül választhat:   
   - Ha *nincsbejövő üzenet,* forduljon az eszközpartneréhez.  
   - Bejövő *üzenetek*esetén lépjen farmbeatssupport@microsoft.comkapcsolatba a partnerrel. Csatlakoztassa a Datahub és a Accelerator naplók és rögzített telemetriai adatok.

A naplók letöltésének megértéséhez lépjen a ["Naplók manuális gyűjtése"](#collect-logs-manually) című részre.  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nem lehet megtekinteni a telemetriai adatokat az érzékelők ből származó előzmény-/streamelési adatok betöltése után

**Jelenség:** Eszközök vagy érzékelők vannak telepítve, és létrehozta az eszközöket/érzékelőket a FarmBeats és a bevitt telemetriai adatokat az EventHubon, de nem tudja letenni vagy megtekinteni a telemetriai adatokat a FarmBeats.Symptoms: Devices or sensors are deployed, and you've created the devices/sensors on FarmBeats and intested telemetry to the EventHub, but you can't get or view telemettry data on FarmBeats.

**Korrekciós intézkedés**:

1. Győződjön meg róla, hogy helyesen végezte el a partnerregisztrációt - ezt ellenőrizheti a datahub swagger- hez, keresse meg a /Partner API-t, a Get-et, és ellenőrizze, hogy a partner regisztrálva van-e. Ha nem, kövesse az [itt leírt lépéseket](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) a partner hozzáadásához.

2. Győződjön meg arról, hogy a megfelelő Telemetriai üzenetformátumot használta:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":"<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Nem rendelkezik az Azure Event Hubs kapcsolati karakterlánccal

**Korrekciós intézkedés**:

1. A Datahub Swagger ben nyissa meg a Partner API-t.
2. Válassza **a Kipróbálás** > **kivégrehajtása** > **a Végrehajtás lehetőséget.**

> [!NOTE]
> Az Önt érdeklő érzékelőpartner partnerazonosítója.

3. Lépjen vissza a Partner API-hoz, és válassza **a Get/\<ID>** lehetőséget.
4. Adja meg a partnerazonosítót a **3.**

   Az API-válasznak rendelkeznie kell az Event Hubs kapcsolati karakterlánccal.

### <a name="device-appears-offline"></a>Az eszköz offline állapotban jelenik meg

**Jelenségek**: Az eszközök telepítve vannak, és a FarmBeats-t összekapcsolta az eszközpartnerével. Az eszközök online állapotban vannak, és telemetriai adatokat küldenek, de offline állapotban jelennek meg.

**Korrekciós művelet:** A jelentéskészítési időköz nincs konfigurálva ehhez az eszközhöz. A jelentéskészítési időköz beállításához forduljon az eszköz gyártójához. 

### <a name="error-deleting-a-device"></a>Hiba az eszköz törlésekor

Az eszköz törlése közben az alábbi gyakori hibaüzenetek egyike jelenhet meg:  

**Üzenet**: "Az eszközre az érzékelők hivatkoznak: Egy vagy több érzékelő van társítva az eszközhöz. Törölje az érzékelőket, majd törölje az eszközt."  

**Jelentés:** Az eszköz több érzékelővel van társítva, amelyek a farmban vannak telepítve.   

**Korrekciós intézkedés**:  

1. Törölje az eszközhöz társított érzékelőket a gyorsítón keresztül.  
2. Ha az érzékelőket egy másik eszközhöz szeretné társítani, kérje meg az eszköz partnerét, hogy tegye ugyanezt.  
3. Az eszköz törlése `DELETE API` hívással, és állítsa be az erő *paramétert true*.  

**Üzenet**: "Az eszközök parentdeviceId ként hivatkoznak az eszközökre: Egy vagy több eszköz van társítva az eszközhöz gyermekeszközként. Törölje őket, majd törölje ezt az eszközt."  

**Jelentése**: A készülékhez más eszközök is tartoznak.  

**Korrekciós intézkedés**

1. Törölje az adott eszközhöz társított eszközöket.  
2. Törölje az adott eszközt.  

    > [!NOTE]
    > Nem törölhet egy eszközt, ha érzékelők vannak társítva. A társított érzékelők törléséről további információt az Érzékelőadatok törlése az [érzékelőpartnerektől](get-sensor-data-from-sensor-partner.md)című szakaszban található **Érzékelőadatok törlése** című szakaszban talál.

    > A partnerek nem férhetnek hozzá egy eszköz vagy érzékelő törléséhez. Csak a rendszergazdák férhetnek hozzá, hogy ugyanezt tegyék.


## <a name="issues-with-jobs"></a>Problémák a munkahelyek

### <a name="farmbeats-internal-error"></a>FarmBeats belső hiba

**Üzenet**: "FarmBeats belső hiba, további részletekért lásd a hibaelhárítási útmutatót".

**Korrekciós intézkedés**: Ez a probléma az adatfolyamat ideiglenes hibájából eredhet. Hozza létre újra a feladatot. Ha a hiba továbbra is fennáll, adja hozzá a hibaüzenetet FarmBeatsSupport@microsoft.comegy bejegyzéshez a FarmBeats fórumon, vagy lépjen kapcsolatba a .

## <a name="accelerator-troubleshooting"></a>Gyorssegédhiba-elhárítás

### <a name="access-control"></a>Hozzáférés-vezérlés

**Probléma**: Szerepkör-hozzárendelés hozzáadása közben hibaüzenet jelenik meg.

**Üzenet**: "Nem található egyező felhasználó."

**Korrekciós intézkedés:** Ellenőrizze azt az e-mail-azonosítót, amelyhez szerepkör-hozzárendelést próbál hozzáadni. Az e-mail-azonosítónak pontosan egyeznie kell az azonosítóval, amely regisztrálva van az adott felhasználóhoz az Active Directoryban. Ha a hiba továbbra is fennáll, adja hozzá a hibaüzenetet FarmBeatsSupport@microsoft.comegy bejegyzéshez a FarmBeats fórumon, vagy lépjen kapcsolatba a .

### <a name="unable-to-log-in-to-accelerator"></a>Nem lehet bejelentkezni a gyorsítóba

**Üzenet**: "Hiba: Nincs jogosult a szolgáltatás hívására. Kérjen engedélyt a rendszergazdától."

**Korrekciós intézkedés:** Kérje meg a rendszergazdát, hogy engedélyezze a FarmBeats központi telepítésének elérését. Ez a Szerepkör-hozzárendelés API-k postapostával vagy a gyorssegéd **Beállítások** ablaktáblájának hozzáférés-vezérlésével végezhető el.  

Ha már kapott hozzáférést, és ezzel a hibával szembesül, próbálkozzon újra a lap frissítésével. Ha a hiba továbbra is fennáll, adja hozzá a hibaüzenetet FarmBeatsSupport@microsoft.comegy bejegyzéshez a FarmBeats fórumon, vagy lépjen kapcsolatba a .

![FarmBeats projekt](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Gyorssegédekkel kapcsolatos problémák  

**Probléma:** Meghatározatlan ok-gyorssegéd-hiba történt.

**Üzenet**: "Hiba: Ismeretlen hiba történt."

**Korrekciós intézkedés**: Ez a hiba akkor fordul elő, ha túl sokáig hagyja tétlenül az oldalt. Frissítse az oldalt.  

Ha a hiba továbbra is fennáll, adja hozzá a hibaüzenetet FarmBeatsSupport@microsoft.comegy bejegyzéshez a FarmBeats fórumon, vagy lépjen kapcsolatba a .

**Probléma:** A FarmBeats Accelerator nem jeleníti meg a legújabb verziót, még a FarmBeatsDeployment frissítése után sem.

**Korrekciós intézkedés**: Ez a hiba a szolgáltatás dolgozóinak a böngészőben való perzisztenciája miatt fordul elő. Tegye a következőket:

1. Zárja be az összes olyan böngészőlapot, amelyen meg van nyitva a gyorssegéd, és zárja be a böngészőablakot.
2. Indítsa el a böngésző új példányát, és töltse be újra a gyorssegéd URI-ját. Ez a művelet betölti a gyorssegéd új verzióját.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Képekkel kapcsolatos problémák

### <a name="wrong-username-or-password"></a>Rossz felhasználónév vagy jelszó

**Feladathiba üzenet**: "Az erőforrás eléréséhez teljes hitelesítés szükséges."

**Korrekciós intézkedés**:

Tegye a következők egyikét:

- Futtassa újra a telepítőt a Datahub megfelelő felhasználónévvel és jelszóval történő frissítéséhez.
- Futtassa újra a sikertelen feladatot, vagy futtasson egy műholdas indexfeladatot 5 és 7 napos dátumtartományban, majd ellenőrizze, hogy a feladat sikeres-e.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel hub: Nem megfelelő URL vagy webhely nem érhető el 

**Job hiba üzenet:**"Hoppá, valami elromlott. Az elérni kívánt oldal (ideiglenesen) nem érhető el." 

**Korrekciós intézkedés**:
1. Nyissa meg a [Sentinel](https://scihub.copernicus.eu/dhus/) t a böngészőjében, és nézze meg, hogy a webhely elérhető-e. 
2. Ha a webhely nem érhető el, ellenőrizze, hogy a tűzfal, a vállalati hálózat vagy más blokkoló szoftver megakadályozza-e a webhelyhez való hozzáférést, majd tegye meg a szükséges lépéseket a Sentinel URL engedélyezéséhez. 
3. Futtassa újra a sikertelen feladatot, vagy futtasson egy műholdas indexfeladatot 5 és 7 napos dátumtartományban, majd ellenőrizze, hogy a feladat sikeres-e.  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel szerver: Karbantartás miatt leállt

**Job hiba üzenet**: "A Kopernikusz Open Access Hub hamarosan visszatér! Elnézést a kellemetlenségért, jelenleg némi karbantartást végzünk. Hamarosan újra online leszünk!" 

**Korrekciós intézkedés**:

Ez a probléma akkor fordulhat elő, ha a Sentinel kiszolgálón karbantartási tevékenységet végeznek.

1. Ha bármely feladat vagy folyamat sikertelen, mert a karbantartás végrehajtása folyamatban van, küldje el újra a feladatot egy idő után. 

   A tervezett vagy nem tervezett Sentinel karbantartási tevékenységekkel kapcsolatos információkért látogasson el a [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/) webhelyére.  

2. Futtassa újra a sikertelen feladatot, vagy futtasson egy műholdas indexfeladatot 5 és 7 napos dátumtartományban, majd ellenőrizze, hogy a feladat sikeres-e.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: Az elért kapcsolatok maximális száma

**Feladathiba üzenet:**"A felhasználó "\<felhasználónév> által elért két egyidejű folyamat maximális száma".

**Jelentés:** Ha egy feladat sikertelen, mert elérte a kapcsolatok maximális számát, ugyanazt a Sentinel-fiókot használja egy másik szoftvertelepítés.

**Korrekciós intézkedés**: Próbálkozzon az alábbi műveletek egyikével:

* Hozzon létre egy új Sentinel-fiókot, majd futtassa újra a telepítőt a Datahub frissítéséhez egy új Sentinel felhasználónév és jelszó használatával.  
* Futtassa újra a sikertelen feladatot, vagy futtasson egy műholdas indexfeladatot 5 és 7 napos dátumtartományban, majd ellenőrizze, hogy a feladat sikeres-e.

### <a name="sentinel-server-refused-connection"></a>Sentinel szerver: Elutasított kapcsolat 

**Feladathiba üzenet**: "A kiszolgáló http://172.30.175.69:8983/solr/dhusmegtagadta a kapcsolatot: " 

**Korrekciós intézkedés**: Ez a probléma akkor fordulhat elő, ha karbantartási tevékenységet végeznek a Sentinel szerveren. 
1. Ha bármely feladat vagy folyamat sikertelen, mert a karbantartás végrehajtása folyamatban van, küldje el újra a feladatot egy idő után. 

   A tervezett vagy nem tervezett Sentinel karbantartási tevékenységekkel kapcsolatos információkért látogasson el a [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/) webhelyére.  

2. Futtassa újra a sikertelen feladatot, vagy futtasson egy műholdas indexfeladatot 5 és 7 napos dátumtartományban, majd ellenőrizze, hogy a feladat sikeres-e.

### <a name="soil-moisture-map-has-white-areas"></a>Talaj Nedvesség térkép fehér területek 

**Probléma**: A talaj nedvesség térkép jött létre, de a térkép többnyire fehér területek.

**Korrekciós intézkedés**: Ez a probléma akkor fordulhat elő, ha a térkép kérésének idejére létrehozott műholdas indexek NDVI értéke 0,3-nál kisebb. További információkért kérjük, látogasson el [a Sentinel műszaki útmutatójába.](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)
1. Futtassa újra a feladatot egy másik dátumtartományhoz, és ellenőrizze, hogy a műholdas indexekben lévő NDVI-értékek meghaladják-e a 0,3 értéket.

## <a name="collect-logs-manually"></a>Naplók gyűjtése manuálisan

[Az Azure Storage Explorer telepítése és telepítése.]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Azure Data Factory feladatnaplók gyűjtése a Datahubon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A **Keresőmezőben** keresse meg a FarmBeats Datahub erőforráscsoportot.

    > [!NOTE]
    > Válassza ki a FarmBeats telepítése során megadott Datahub erőforráscsoportot.

3. Az **Erőforráscsoport** irányítópulton keresse meg a *datahublogs\* * tárfiókot. Például keressen **datahublogsmvxmq**.  
4. A **Név** oszlopban válassza ki a tárfiókot a **tárfiók** irányítópultjának megtekintéséhez.
5. A **datahubblogs\* ** ablaktáblán válassza a **Megnyitás az Intézőben** lehetőséget az **Open Azure Storage Explorer** alkalmazás megtekintéséhez.
6. A bal oldali ablaktáblában válassza a **Blob Containers**lehetőséget, majd válassza a **feladatnaplók lehetőséget.**
7. A **feladatnaplók** ablaktáblában válassza a **Letöltés**lehetőséget.
8. Töltse le a naplókat a számítógép egy helyi mappájába.
9. Küldje el e-mailben a letöltött .zip fájlt a rendszernek. farmbeatssupport@microsoft.com

    ![FarmBeats projekt](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Azure Data Factory feladatnaplók gyűjtése a gyorssegédben

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A **Keresés mezőben** keresse meg a FarmBeats Accelerator erőforráscsoportot.

    > [!NOTE]
    > Válassza ki a FarmBeats telepítése során megadott gyorssegéd erőforráscsoportot.

3. Az **Erőforráscsoport** irányítópulton keresse meg a tártárfiókot. *\* * Például keressen **storagedop4k\***.
4. Válassza ki a tárfiókot a **Név** oszlopban a **tárfiók** irányítópultjának megtekintéséhez.
5. A **tároló\* ** ablaktáblán válassza a **Megnyitás az intézőben** lehetőséget az Azure Storage Explorer alkalmazás megnyitásához.
6. A bal oldali ablaktáblában válassza a **Blob Containers**lehetőséget, majd válassza a **feladatnaplók lehetőséget.**
7. A **feladatnaplók** ablaktáblában válassza a **Letöltés**lehetőséget.
8. Töltse le a naplókat a számítógép egy helyi mappájába.
9. Küldje el e-mailben a letöltött .zip fájlt a rendszernek. farmbeatssupport@microsoft.com


### <a name="collect-datahub-app-service-logs"></a>Datahub alkalmazásszolgáltatás-naplók gyűjtése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A **Keresőmezőben** keresse meg a FarmBeats Datahub erőforráscsoportot.

    > [!NOTE]
    > Válassza ki a FarmBeats telepítése során megadott Datahub erőforráscsoportot.

3. Az erőforráscsoportban keresse meg a *datahublogs\* * tárfiókot. Például keressen **fordatahublogsmvxmq\***.
4. Válassza ki a tárfiókot a **Név** oszlopban a **tárfiók** irányítópultjának megtekintéséhez.
5. A **datahubblogs\* ** ablaktáblán válassza a **Megnyitás az Intézőben** lehetőséget az Azure Storage Explorer alkalmazás megnyitásához.
6. A bal oldali ablaktáblában válassza a **Blob Containers**lehetőséget, majd válassza az **appinsights-logs**lehetőséget.
7. Az **appinsights-logs** ablaktáblán válassza a **Letöltés lehetőséget.**
8. Töltse le a naplókat a számítógép egy helyi mappájába.
9. Küldje el e-mailben a letöltött .zip fájlt a rendszernek. farmbeatssupport@microsoft.com

### <a name="collect-accelerator-app-service-logs"></a>Gyorssegéd alkalmazásszolgáltatás-naplóinak gyűjtése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A **Keresés mezőben** keresse meg a FarmBeats Accelerator erőforráscsoportot.

    > [!NOTE]
    > Válassza ki a FarmBeats Accelerator erőforráscsoportot, amelyet a FarmBeats telepítése során biztosított.

3. Az erőforráscsoportban keresse meg a tártárfiókot. *\* * Például keressen **storagedop4k\***.
4. Válassza ki a tárfiókot a **Név** oszlopban a **tárfiók** irányítópultjának megtekintéséhez.
5. A **tároló\* ** ablaktáblán válassza a **Megnyitás az intézőben** lehetőséget az Azure Storage Explorer alkalmazás megnyitásához.
6. A bal oldali ablaktáblában válassza a **Blob Containers**lehetőséget, majd válassza az **appinsights-logs**lehetőséget.
7. Az **appinsights-logs** ablaktáblán válassza a **Letöltés lehetőséget.**
8. Töltse le a naplókat a számítógép egy helyi mappájába.
9. Küldje el e-mailben a letöltött mappát a rendszernek. farmbeatssupport@microsoft.com

## <a name="known-issues"></a>Ismert problémák

## <a name="batch-related-issues"></a>A kötegelt tételekkel kapcsolatos problémák

**A következő hibaüzenet**jelenik meg: "Elérte a kötegfiókok regionális fiókkvótáját a megadott előfizetéshez."

**Korrekciós intézkedés:** Növelje a kvótát, vagy törölje a fel nem használt kötegfiókokat, és futtassa újra a központi telepítést.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Az Azure Active Directoryval (Azure AD) kapcsolatos problémák

**A következő hibaüzenet**jelenik meg: "Nem lehetett frissíteni a szükséges beállításokat az Azure AD App d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: Nem elegendő jogosultsága a művelet végrehajtásához. Győződjön meg arról, hogy a fenti beállítások megfelelően vannak konfigurálva az Azure AD alkalmazáshoz."

**Jelentés:** Az Azure AD alkalmazás regisztrációs konfigurációja nem fejeződött be megfelelően.  

**Korrekciós intézkedés:** Kérje meg az informatikai rendszergazdát (a bérlői olvasási hozzáféréssel rendelkező személyt), hogy használja a [parancsfájlunkat](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) az Azure AD-alkalmazás regisztrációjának létrehozásához. Ez a parancsfájl automatikusan gondoskodik a konfigurációs lépésekről is.

**A következő hibaüzenet**jelenik meg:\<"Nem\>lehetett létrehozni az új Active Directory alkalmazás " alkalmazásnevét " ebben a bérlőben: Már létezik egy másik objektum, amelynek értéke megegyezik a tulajdonságazonosító URI-k értékével."

**Jelentés:** Az Azure AD alkalmazás regisztrációja az azonos nevű már létezik.

**Korrekciós intézkedés:** Törölje a meglévő Azure AD-alkalmazás regisztrációját, vagy használja fel újra a telepítéshez. Ha újra felhasználja a meglévő Azure AD-alkalmazás regisztrációját, adja át az alkalmazásazonosítót és az ügyféltitkos kulcsot a telepítőnek, és telepítse újra.

## <a name="issues-with-the-inputjson-file"></a>Problémák a bemeneti.json fájllal

**Hiba**: Hiba történt az *input.json* fájlbemenetek olvasásakor.

**Korrekciós intézkedés**: Ez a probléma általában abból a dúsítási hibaból ered, hogy megadják a megfelelő *input.json* fájl elérési útját vagy nevét a telepítőnek. Tegye meg a megfelelő javításokat, majd próbálkozzon újra a központi telepítéssel.

**Hiba:** Hiba történt az *input.json* fájlban lévő értékek elemzésével.

**Korrekciós intézkedés**: Ez a probléma főként a *input.json* fájlhelytelen értékei miatt merül fel. Tegye meg a megfelelő javításokat, majd próbálkozzon újra a központi telepítéssel.

## <a name="high-cpu-usage"></a>Magas processzorhasználat

**Hiba**: E-mailben értesítést kap, amely **egy magas cpu-használati riasztásra hivatkozik.** 

**Korrekciós intézkedés**: 
1. Nyissa meg a FarmBeats Datahub erőforráscsoportot.
2. Válassza ki az **Alkalmazásszolgáltatást**.  
3. Lépjen az [App Service-díjszabási csoport rakoncátusára,](https://azure.microsoft.com/pricing/details/app-service/windows/)majd válassza ki a megfelelő tarifacsomagot.

## <a name="next-steps"></a>További lépések

Ha még mindig farmbeats problémákkal szembesül, lépjen kapcsolatba [a támogatási fórumunkkal.](https://aka.ms/farmbeatssupport)
