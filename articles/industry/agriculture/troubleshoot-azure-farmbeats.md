---
title: Az Azure FarmBeats hibaelhárítása
description: Ez a cikk az Azure FarmBeats hibaelhárítását ismerteti.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 74522502f5405b56a3a604f20a695de83ef3c3a5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886483"
---
# <a name="troubleshoot"></a>Hibaelhárítás

Ez a cikk megoldásokat kínál a gyakori Azure FarmBeats problémák. További segítségért forduljon [támogatási fórumunkhoz,](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats) vagy írjon nekünk a. farmbeatssupport@microsoft.com

> [!NOTE]
  > Ha áprilisban telepítette a FarmBeats szolgáltatást, és a feladatok üres hibaüzenettel hibásodnak meg, előfordulhat, hogy a telepítés nem kapott kötegkvótát, mivel az összes erőforrás vészhelyzeti, egészségügyi és egyéb kritikus szükségletekre irányul. Meg kell kérnie a virtuális gépek nek a Batch-fiókhoz való lefoglalását.

## <a name="install-issues"></a>Telepítési problémák

  > [!NOTE]
  > Ha hiba miatt indítja újra a telepítést, a telepítés újbóli elindítása előtt törölje az **erőforráscsoportot,** vagy törölje az összes erőforrást az erőforráscsoportból.

### <a name="invalid-sentinel-credentials"></a>Érvénytelen Sentinel-hitelesítő adatok

A telepítés során megadott Sentinel hitelesítő adatok helytelenek. Indítsa újra a telepítést a megfelelő hitelesítő adatokkal.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>Elérte a Batch-fiókok regionális fiókkvótáját a megadott előfizetéshez

Növelje a kvótát, vagy törölje a nem használt kötegfiókokat, és indítsa újra a telepítést.

### <a name="invalid-resource-group-location"></a>Érvénytelen erőforráscsoport helye

Győződjön meg arról, hogy az **erőforráscsoport** ugyanazon a helyen található, mint a telepítés során megadott **régió.**

### <a name="other-install-issues"></a>Egyéb telepítési problémák

Vegye fel velünk a kapcsolatot a következő adatokkal:

- Az előfizetés azonosítója
- Erőforráscsoport neve
- A központi telepítési hiba naplófájljának csatolásához kövesse az alábbi lépéseket:

    1. Keresse meg az **erőforráscsoportot** az Azure Portalon.
    2. Válassza **a Központi telepítések** lehetőséget a bal oldali **Beállítások** csoportban.
    3. Minden olyan központi telepítésesetén, amely a **Sikertelen állapotú t,** válassza ki a részleteket, és töltse le a központi telepítés részleteit. Csatolja ezt a fájlt az e-mailhez.

## <a name="sensor-telemetry"></a>Érzékelő telemetriai adatai

### <a name="cant-view-telemetry-data"></a>Nem lehet megtekinteni a telemetriai adatokat

**Jelenség:** Az eszközök vagy érzékelők telepítve vannak, és összekapcsolta a FarmBeats-t az eszközpartnerével, de nem tudja levenni vagy megtekinteni a telemetriai adatokat a FarmBeats-en.

**Korrekciós intézkedés**

1. Nyissa meg a FarmBeats Datahub erőforráscsoportot.
2. Válassza ki az **Event Hub** (DatafeedEventHubNamespace), majd ellenőrizze a bejövő üzenetek száma.
3. A következő lehetőségek közül választhat:

   - Ha *nincsbejövő üzenet,* forduljon az eszközpartneréhez.  
   - Bejövő *üzenetek*esetén lépjen kapcsolatba velünk a Datahub és a Accelerator naplóival és a rögzített telemetriai adatokkal.

A naplók letöltésének megértéséhez lépjen a ["Naplók manuális gyűjtése"](#collect-logs-manually) című részre.  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nem lehet megtekinteni a telemetriai adatokat az érzékelők ből származó előzmény-/streamelési adatok betöltése után

**Jelenség:** Eszközök vagy érzékelők vannak telepítve, és létrehozta az eszközöket/érzékelőket a FarmBeats és a bevitt telemetriai adatokat az EventHubon, de nem tudja letenni vagy megtekinteni a telemetriai adatokat a FarmBeats.Symptoms: Devices or sensors are deployed, and you've created the devices/sensors on FarmBeats and intested telemetry to the EventHub, but you can't get or view telemettry data on FarmBeats.

**Korrekciós intézkedés**

1. Győződjön meg róla, hogy helyesen végezte el a partnerregisztrációt - ezt ellenőrizheti a datahub swagger- hez, keresse meg a /Partner API-t, a Get-et, és ellenőrizze, hogy a partner regisztrálva van-e. Ha nem, az alábbi [lépésekkel](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) vehet fel partnert.

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

**Korrekciós intézkedés**

1. A Datahub Swagger ben nyissa meg a Partner API-t.
2. Válassza **a Kipróbálás** > **kivégrehajtása** > **a Végrehajtás lehetőséget.**

> [!NOTE]
> Az Önt érdeklő érzékelőpartner partnerazonosítója.

3. Lépjen vissza a Partner API-hoz, és válassza **a Get/\<ID>** lehetőséget.
4. Adja meg a partnerazonosítót a **3.**

   Az API-válasznak rendelkeznie kell az Event Hubs kapcsolati karakterlánccal.

### <a name="device-appears-offline"></a>Az eszköz offline állapotban jelenik meg

**Jelenségek**: Az eszközök telepítve vannak, és a FarmBeats-t összekapcsolta az eszközpartnerével. Az eszközök online állapotban vannak, és telemetriai adatokat küldenek, de offline állapotban jelennek meg.

**Korrekciós intézkedés** A jelentési időköz nincs konfigurálva ehhez az eszközhöz. A jelentéskészítési időköz beállításához forduljon az eszköz gyártójához. 

### <a name="error-deleting-a-device"></a>Hiba az eszköz törlésekor

Az eszköz törlése közben az alábbi gyakori hibaüzenetek egyike jelenhet meg:  

**Üzenet**: "Az eszközre az érzékelők hivatkoznak: Egy vagy több érzékelő van társítva az eszközhöz. Törölje az érzékelőket, majd törölje az eszközt."  

**Jelentés:** Az eszköz több érzékelővel van társítva, amelyek a farmban vannak telepítve.

**Korrekciós intézkedés**  

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
    > A partnereknek nincs engedélye eszköz vagy érzékelő törlésére. Csak a rendszergazdák rendelkeznek törlési engedéllyel.

## <a name="issues-with-jobs"></a>Problémák a munkahelyek

### <a name="farmbeats-internal-error"></a>FarmBeats belső hiba

**Üzenet**: "FarmBeats belső hiba, további részleteka hibaelhárítási útmutatóban."

**Korrekciós intézkedés** Ez a probléma az adatfolyamat ideiglenes hibájából eredhet. Hozza létre újra a feladatot. Ha a hiba továbbra is fennáll, lépjen kapcsolatba velünk a hibaüzenettel/naplóval.

## <a name="accelerator-troubleshooting"></a>Gyorssegédhiba-elhárítás

### <a name="access-control"></a>Hozzáférés-vezérlés

**Probléma**: Szerepkör-hozzárendelés hozzáadása közben hibaüzenet jelenik meg.

**Üzenet**: "Nem található egyező felhasználó."

**Korrekciós intézkedés** Ellenőrizze azt az e-mail-azonosítót, amelyhez szerepkör-hozzárendelést próbál hozzáadni. Az e-mail-azonosítónak pontosan egyeznie kell az azonosítóval, amely regisztrálva van az adott felhasználóhoz az Active Directoryban. Ha a hiba továbbra is fennáll, lépjen kapcsolatba velünk a hibaüzenettel/naplóval.

### <a name="unable-to-log-in-to-accelerator"></a>Nem lehet bejelentkezni a gyorsítóba

**Üzenet**: "Hiba: Nincs jogosult a szolgáltatás hívására. Kérjen engedélyt a rendszergazdától."

**Korrekciós intézkedés** Kérje meg a rendszergazdát, hogy engedélyezze a FarmBeats központi telepítésének elérését. Ez a Szerepkör-hozzárendelés API-k postapostával vagy a gyorssegéd **Beállítások** ablaktáblájának hozzáférés-vezérlésével végezhető el.  

Ha már kapott hozzáférést, és ezzel a hibával szembesült, próbálkozzon újra a lap frissítésével. Ha a hiba továbbra is fennáll, lépjen kapcsolatba velünk a hibaüzenettel/naplóval.

![FarmBeats projekt](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Gyorssegédekkel kapcsolatos problémák  

**Probléma:** Meghatározatlan ok-gyorssegéd-hiba történt.

**Üzenet**: "Hiba: Ismeretlen hiba történt."

**Korrekciós intézkedés** Ez a hiba akkor fordul elő, ha túl sokáig hagyja tétlenül az oldalt. Frissítse az oldalt. Ha a hiba továbbra is fennáll, lépjen kapcsolatba velünk a hibaüzenettel/naplóval.

**Probléma:** A FarmBeats Accelerator nem jeleníti meg a legújabb verziót, még a FarmBeatsDeployment frissítése után sem.

**Korrekciós intézkedés** Ez a hiba a szolgáltatás dolgozóinak a böngészőben való perzisztenciája miatt fordul elő. Tegye a következőket:

1. Zárja be az összes olyan böngészőlapot, amelyen meg van nyitva a gyorssegéd, és zárja be a böngészőablakot.
2. Indítsa el a böngésző új példányát, és töltse be újra a gyorssegéd URI-ját. Ez a művelet betölti a gyorssegéd új verzióját.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Képekkel kapcsolatos problémák

### <a name="wrong-username-or-password"></a>Rossz felhasználónév vagy jelszó

**Feladathiba üzenet**: "Az erőforrás eléréséhez teljes hitelesítés szükséges."

**Korrekciós intézkedés**: Tegye az alábbiak egyikét:

- Frissítse a FarmBeats-t a megfelelő felhasználónévvel/jelszóval az alábbi lépésekkel, majd próbálkozzon újra a feladattal.

  **Sentinel felhasználónév frissítése**

    1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
    2. A **Keresőmezőben** keresse meg a FarmBeats Datahub erőforráscsoportot.
    3. Válassza ki a tárfiók tártárát***** > **a tárolók** > **kötegelőfájljai** > **to_vm** > **config.ini**
    4. **Szerkesztés kijelölése**
    5. A felhasználónév frissítése a sentinel_account szakaszban

  **Sentinel jelszó frissítése**

    1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
    2. A **Keresőmezőben** keresse meg a FarmBeats Datahub erőforráscsoportot.
    3. Keyvault kiválasztása-*****
    4. A Beállítások csoportban válassza a Hozzáférési házirendek lehetőséget
    5. **Hozzáférési házirend hozzáadása** kiválasztása
    6. Titkos **kezelés** használata a Konfigurálás sablonból beállításhoz, és saját magad hozzáadása az egyszerű höz
    7. Válassza a **Hozzáadás**lehetőséget, majd a **Mentés** gombot a **Hozzáférési házirendek** lapon
    8. A **Beállítások csoportban** válassza a Titkos kulcsok **lehetőséget.**
    9. Válassza a **Sentinel-jelszó** lehetőséget
    10. Hozzon létre egy új verziót az érték, és engedélyezze azt.

- Futtassa újra a sikertelen feladatot, vagy futtasson egy műholdas indexfeladatot 5 és 7 napos dátumtartományban, majd ellenőrizze, hogy a feladat sikeres-e.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel hub: Nem megfelelő URL vagy webhely nem érhető el

**Job hiba üzenet:**"Hoppá, valami elromlott. Az elérni kívánt oldal (ideiglenesen) nem érhető el."

**Korrekciós intézkedés**:

1. Nyissa meg a [Sentinel](https://scihub.copernicus.eu/dhus/) t a böngészőjében, és nézze meg, hogy a webhely elérhető-e.
2. Ha a webhely nem érhető el, ellenőrizze, hogy bármely tűzfal, vállalati hálózat vagy más blokkoló szoftver megakadályozza-e a webhelyhez való hozzáférést, majd tegye meg a szükséges lépéseket a Sentinel URL engedélyezéséhez. 
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

**Jelentés:** Ha egy feladat sikertelen, mert a kapcsolatok maximális száma elérte, ugyanazt a Sentinel-fiókot használja több feladatban.

**Korrekciós intézkedés**: Próbálkozzon az alábbi műveletek egyikével:

* Várja meg, amíg a többi feladat befejeződik, mielőtt újra futtatja a sikertelen feladatot.
* Hozzon létre egy új Sentinel-fiókot, majd frissítse a Sentinel felhasználónevét és jelszavát a FarmBeats-ben.

### <a name="sentinel-server-refused-connection"></a>Sentinel szerver: Elutasított kapcsolat

**Feladathiba üzenet**: "A kiszolgáló http://172.30.175.69:8983/solr/dhusmegtagadta a kapcsolatot: "

**Korrekciós intézkedés**: Ez a probléma akkor fordulhat elő, ha karbantartási tevékenységet végeznek a Sentinel szerveren.

1. Ha bármely feladat vagy folyamat sikertelen, mert a karbantartás végrehajtása folyamatban van, küldje el újra a feladatot egy idő után.

   A tervezett vagy nem tervezett Sentinel karbantartási tevékenységekkel kapcsolatos információkért látogasson el a [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/) webhelyére.  

2. Futtassa újra a sikertelen feladatot, vagy futtasson egy műholdas indexfeladatot 5 és 7 napos dátumtartományban, majd ellenőrizze, hogy a feladat sikeres-e.

### <a name="soil-moisture-map-has-white-areas"></a>Talaj Nedvesség térkép fehér területek

**Probléma**: A **talaj nedvesség térkép** jött létre, de a térkép többnyire fehér területek.

**Korrekciós intézkedés**: Ez a probléma akkor fordulhat elő, ha a térkép kérésének idejére létrehozott műholdas indexek NDVI értéke 0,3-nál kisebb. További információkért látogasson el [a Sentinel műszaki útmutatójába.](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)

1. Futtassa újra a feladatot egy másik dátumtartományhoz, és ellenőrizze, hogy a műholdas indexekben az NDVI-értékek értékei meghaladják-e a 0,3 értéket.

## <a name="collect-logs-manually"></a>Naplók gyűjtése manuálisan

[Az Azure Storage Explorer telepítése és telepítése.]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Azure Data Factory-feladatnaplók vagy App Service-naplók gyűjtése a Datahubon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A **Keresőmezőben** keresse meg a FarmBeats Datahub erőforráscsoportot.
3. Az **Erőforráscsoport** irányítópulton keresse meg a *datahublogs\* * tárfiókot. Például, *datahublogsmvxmq*.  
4. A **Név** oszlopban válassza ki a tárfiókot a **tárfiók** irányítópultjának megtekintéséhez.
5. A **datahubblogs\* ** ablaktáblán válassza a **Megnyitás az Intézőben** lehetőséget az **Open Azure Storage Explorer** alkalmazás megtekintéséhez.
6. A bal oldali ablaktáblában válassza a **Blob-tárolók**lehetőséget, majd válassza ki az Azure Data Factory-naplók vagy **az App Service-naplók alkalmazásinsights-naplók** **feladatnaplóit.**
7. Válassza **a Naplók letöltése** és letöltése a számítógép egy helyi mappájába jelölőnégyzetet.

    ![FarmBeats projekt](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Azure Data Factory feladatnaplók vagy App Service-naplók gyűjtése a gyorssegédhez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A **Keresés mezőben** keresse meg a FarmBeats Accelerator erőforráscsoportot.
3. Az **Erőforráscsoport** irányítópulton keresse meg a tártárfiókot. *\* * Például *storagedop4k\**.
4. Válassza ki a tárfiókot a **Név** oszlopban a **tárfiók** irányítópultjának megtekintéséhez.
5. A **tároló\* ** ablaktáblán válassza a **Megnyitás az intézőben** lehetőséget az Azure Storage Explorer alkalmazás megnyitásához.
6. A bal oldali ablaktáblában válassza a **Blob-tárolók**lehetőséget, majd válassza ki az Azure Data Factory-naplók vagy **az App Service-naplók alkalmazásinsights-naplók** **feladatnaplóit.**
7. Válassza **a Naplók letöltése** és letöltése a számítógép egy helyi mappájába jelölőnégyzetet.

## <a name="high-cpu-usage"></a>Magas processzorhasználat

**Hiba**: E-mailben értesítést kap, amely **egy magas cpu-használati riasztásra hivatkozik.**

**Korrekciós intézkedés**:

1. Nyissa meg a FarmBeats Datahub erőforráscsoportot.
2. Válassza ki az **Alkalmazásszolgáltatást**.  
3. Lépjen az [App Service-díjszabási csoport rakoncátusára,](https://azure.microsoft.com/pricing/details/app-service/windows/)majd válassza ki a megfelelő tarifacsomagot.
