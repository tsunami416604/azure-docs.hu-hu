---
title: Modulok üzembe helyezése méretekben Azure Portal-Azure IoT Edge
description: A Azure Portal használatával automatikus központi telepítéseket hozhat létre IoT Edge-eszközök csoportjaihoz
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 068845bf8cda7ce6abf11eefad0ed176688b34c5
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665848"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>IoT Edge-modulok üzembe helyezése és figyelése a Azure Portal használatával

Hozzon létre egy **IoT Edge automatikus központi telepítést** a Azure Portalban, hogy egyszerre több eszközön is kezelhesse a folyamatban lévő központi telepítéseket. A IoT Edge automatikus központi telepítései a IoT Hub [automatikus Eszközkezelő](/azure/iot-hub/iot-hub-automatic-device-management) funkciójának részét képezik. A központi telepítések olyan dinamikus folyamatok, amelyek lehetővé teszik több modul üzembe helyezését több eszközön, nyomon követni a modulok állapotát és állapotát, és szükség esetén módosításokat hajthat végre. 

További információ: [IoT Edge automatikus központi telepítésének ismertetése egyetlen eszközön vagy nagy méretekben](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Címkék használatával azonosíthatja az eszközöket

A központi telepítés létrehozása előtt meg kell határozni, hogy mely eszközöket kívánja érinteni. A Azure IoT Edge a **címkével** rendelkező eszközöket azonosítja az eszköz Twin-ben. Minden eszköz több címkével is rendelkezhet, amelyeket bármilyen módon meghatározhat a megoldásához. Ha például egy intelligens épületből álló campusot kezel, a következő címkéket adhatja hozzá egy eszközhöz:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

További információ az eszközök ikrekről és címkékről: [az eszközök összevetése és használata az IoT Hubban](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

1. A [Azure Portal](https://portal.azure.com)nyissa meg az IoT hubot. 
1. Válassza a **IoT Edge**lehetőséget.
1. Válassza a **hozzáadás IoT Edge központi telepítés**lehetőséget.

Az üzemelő példány létrehozásának öt lépése van. A következő szakasz végigvezeti a műveletet. 

### <a name="step-1-name-and-label"></a>1\. lépés: név és címke

1. Adja meg az üzembe helyezést egy egyedi névvel, amely akár 128 kisbetűt is tartalmazhat. Kerülje a szóközöket, és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.
1. A központi telepítések nyomon követéséhez kulcs-érték párokkal adhat hozzá címkéket. Például: **HostPlatform** és **Linux**, vagy **Version** és **3.0.1**.
1. Kattintson a **tovább** gombra a második lépésre való áttéréshez. 

### <a name="step-2-add-modules-optional"></a>2\. lépés: modulok hozzáadása (nem kötelező)

Akár 20 modult is hozzáadhat egy központi telepítéshez. 

Ha modulokat nem tartalmazó központi telepítést hoz létre, az eltávolítja a megcélzott eszközökről származó aktuális modulokat. 

Ha Azure Stream Analytics-modult szeretne hozzáadni, kövesse az alábbi lépéseket:

1. A lap **központi telepítési modulok** szakaszában kattintson a **Hozzáadás**gombra.
1. Válassza ki **Azure stream Analytics modult**.
1. Válassza ki az **előfizetést** a legördülő menüből.
1. Válassza ki a IoT **Edge-feladatot** a legördülő menüből.
1. Válassza a **Mentés** lehetőséget, hogy hozzáadja a modult a központi telepítéshez. 

Egyéni kód modulként való hozzáadásához, illetve Azure-szolgáltatási modul manuális hozzáadásához kövesse az alábbi lépéseket:

1. A lap **Container Registry beállítások** szakaszában adja meg a központi telepítés moduljának lemezképeit tartalmazó privát tároló-beállításjegyzékek nevét és hitelesítő adatait. A IoT Edge ügynök a 500-es hibát fogja jelenteni, ha nem találja a tároló beállításjegyzékének hitelesítő adatait a Docker-rendszerképhez.
1. A lap **központi telepítési modulok** szakaszában kattintson a **Hozzáadás**gombra.
1. Válassza ki **IoT Edge modult**.
1. Adjon **nevet**a modulnak.
1. A **RENDSZERKÉP URI** mezőjébe írja be a modulhoz tartozó tároló rendszerképét. 
1. Adja meg a tárolóhoz átadandó **tároló-létrehozási beállításokat** . További információ: [Docker Create](https://docs.docker.com/engine/reference/commandline/create/).
1. Az **Újraindítási szabályzat**kiválasztásához használja a legördülő menüt. Válasszon a következő lehetőségek közül: 
   * **Mindig** – a modul mindig újraindul, ha valamilyen okból leáll.
   * **soha** – a modul soha nem indul újra, ha bármilyen okból leáll.
   * **hiba** esetén – a modul újraindul, ha összeomlik, de nem, ha nem áll le tisztán. 
   * **sérült** állapotban – a modul újraindul, ha összeomlik vagy visszaadja a nem kifogástalan állapotot. Az állapotfigyelő funkció megvalósításához az egyes modulok is felhasználhatók. 
1. A modul **kívánt állapotának** kiválasztásához használja a legördülő menüt. Válasszon a következő lehetőségek közül:
   * a **futó** Futtatás az alapértelmezett beállítás. A modul a telepítése után azonnal elindul.
   * **Leállítva** – az üzembe helyezés után a modul tétlen marad mindaddig, amíg meg nem hívja az Ön vagy egy másik modul indítását.
1. Válassza ki a **Module Twin kívánt tulajdonságainak megadása** lehetőséget, ha címkéket vagy egyéb tulajdonságokat szeretne felvenni a Twin modulba.
1. Adja meg a modul **környezeti változóit** . A környezeti változók konfigurációs adatokat biztosítanak egy modulhoz.
1. Válassza a **Mentés** lehetőséget, hogy hozzáadja a modult a központi telepítéshez. 

Miután konfigurálta az összes modult a központi telepítéshez, kattintson a **tovább** gombra a harmadik lépésre való áttéréshez.

### <a name="step-3-specify-routes-optional"></a>3\. lépés: útvonalak megadása (nem kötelező)

Az útvonalak határozzák meg, hogy a modulok hogyan kommunikáljanak egymással egy központi telepítésen belül. Alapértelmezés szerint a varázsló egy **route (útvonal** ) nevű útvonalat ad meg, amelyet **a/* be $upstream * *-ban határoz meg, ami azt jelenti, hogy bármely modulból kimenetet küld az IoT hubhoz.  

Adja hozzá vagy frissítse az útvonalakat a [deklarált útvonalak](module-composition.md#declare-routes)információi alapján, majd kattintson a **tovább** gombra a felülvizsgálati szakasz folytatásához.

### <a name="step-4-specify-metrics-optional"></a>4\. lépés: mérőszámok megadása (nem kötelező)

A metrikák a konfigurációs tartalom alkalmazásának eredményeképpen a különböző állapotok összegzési számát adják meg.

1. Adja meg a **metrika nevének**nevét.

1. Adja meg a **mérőszám feltételeinek**lekérdezését. A lekérdezés IoT Edge hub-modul Twin [jelentett tulajdonságain](module-edgeagent-edgehub.md#edgehub-reported-properties)alapul. A metrika a lekérdezés által visszaadott sorok számát jelöli.

   Példa:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>5\. lépés: eszközök megcélzása

Az eszközök címkék tulajdonságával megcélozhatja azokat az eszközöket, amelyeknek a központi telepítést meg kell kapniuk. 

Mivel több üzemelő példány is megcélozhatja ugyanazt az eszközt, minden egyes központi telepítésnek prioritási számot kell megadnia. Ha ütközés lép fel, a legmagasabb prioritású üzemelő példány (a nagyobb érték magasabb prioritást jelez) a WINS. Ha két üzemelő példány azonos prioritási számmal rendelkezik, akkor a legutóbb létrehozott WINS. 

1. Adjon meg egy pozitív egész számot a központi telepítési **prioritáshoz**.
1. Adja meg a **cél feltételt** annak meghatározásához, hogy mely eszközök lesznek megcélozva a központi telepítéssel. A feltétel a Device Twin-címkék vagy az eszközök Twin jelentett tulajdonságain alapul, és meg kell egyeznie a kifejezés formátumával. Például `tags.environment='test'` vagy `properties.reported.devicemodel='4000x'`. 
1. A **tovább** gombra kattintva lépjen be az utolsó lépésbe.

### <a name="step-6-review-deployment"></a>6\. lépés: az üzembe helyezés áttekintése

Tekintse át az üzembe helyezési adatokat, majd válassza a **Küldés**lehetőséget.

## <a name="deploy-modules-from-azure-marketplace"></a>Modulok üzembe helyezése az Azure Marketplace-en

Az Azure Marketplace egy online alkalmazások és szolgáltatások piaca, ahol az Azure-on futó Nagyvállalati alkalmazások és megoldások széles körét böngészheti, beleértve a [IoT Edge modulokat](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)is. Az Azure Marketplace a Azure Portal **erőforrás létrehozása**lehetőség alatt is elérhető.

IoT Edge modult az Azure Marketplace-ről vagy a Azure Portalról is üzembe helyezhet:

1. Keresse meg a modult, és kezdje el a telepítési folyamatot.

   * Azure Portal: keresse meg a modult, és válassza a **Létrehozás**lehetőséget.

   * Azure Marketplace:

     1. Keresse meg a modult, és válassza a **Letöltés most**lehetőséget.
     1. A **Folytatás**gombra kattintva nyugtázza a szolgáltató használati feltételeit és adatvédelmi szabályzatát.

1. Válassza ki az előfizetését és azt a IoT Hub, amelyhez a céleszköz csatolva van.

1. Válassza **a üzembe helyezés méretezéskor**lehetőséget.

1. Adja meg, hogy hozzá kívánja-e adni a modult egy új központi telepítéshez vagy egy meglévő üzemelő példány klónozásához; Ha a klónozást választja, válassza ki a meglévő telepítést a listából.

1. A **Létrehozás** lehetőség kiválasztásával folytathatja a központi telepítés méretezésének folyamatát. Megadhatja ugyanazokat a részleteket, mint bármely üzembe helyezésnél.

## <a name="monitor-a-deployment"></a>Központi telepítés figyelése

A központi telepítés részleteinek megtekintéséhez és az azt futtató eszközök figyeléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz. 
1. Válassza a **IoT Edge**lehetőséget.
1. Válassza **IoT Edge központi telepítések**lehetőséget. 

   ![IoT Edge üzemelő példányok megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Vizsgálja meg a központi telepítés listáját. Az egyes központi telepítések esetében az alábbi adatokat tekintheti meg:
   * **Azonosító** – az üzemelő példány neve.
   * **Cél feltétel** – a megcélzott eszközök definiálásához használt címke.
   * **Prioritás** – az üzemelő példányhoz rendelt prioritási szám.
   * A - **megcélzott** **rendszermetrikák** határozzák meg a célcsoport-kezelési feltételnek megfelelő IoT hub az ikrek számát **, valamint azt** , hogy hány eszközön lett alkalmazva a központi telepítési tartalom a IoT hub. 
   * **Eszköz metrikái** – a központi telepítésben szereplő IoT Edge eszközök száma a IoT Edge ügyfél futtatókörnyezetének sikerességéről vagy hibáiról.
   * **Egyéni metrikák** – a központi telepítéshez megadott összes metrika esetében az üzembe helyezési jelentéskészítési adatok IoT Edge eszközeinek száma.
   * **Létrehozás időpontja** – a központi telepítés létrehozásának időbélyegzője. Ez az időbélyeg a kapcsolatok megszakítására szolgál, ha két üzemelő példány azonos prioritással rendelkezik. 
1. Válassza ki a figyelni kívánt központi telepítést.  
1. Vizsgálja meg a központi telepítés részleteit. A fülek segítségével áttekintheti a központi telepítés részleteit.

## <a name="modify-a-deployment"></a>Központi telepítés módosítása

Amikor módosít egy központi telepítést, a módosítások azonnal replikálódnak az összes megadott eszközre. 

Ha frissíti a célként megadott feltételt, a következő frissítések történnek:

* Ha egy eszköz nem felelt meg a régi célként megadott feltételnek, de megfelel az új célként megadott feltételnek, és ez a központi telepítés az eszköz legmagasabb prioritása, akkor ez a központi telepítés az eszközre lesz alkalmazva. 
* Ha a központi telepítést jelenleg futtató eszköz már nem felel meg a célként megadott feltételnek, akkor eltávolítja ezt a központi telepítést, és a következő legmagasabb prioritású üzemelő példányra kerül. 
* Ha a központi telepítést jelenleg futtató eszköz már nem felel meg a célként megadott feltételnek, és nem felel meg a többi üzemelő példány céljának, akkor az eszközön nem történik változás. Az eszköz továbbra is a jelenlegi állapotukban futtatja az aktuális modulokat, de az üzembe helyezés részeként már nem felügyelhető. Ha teljesíti az egyéb üzemelő példányok célját, eltávolítja ezt az üzembe helyezést, és az újat veszi. 

A központi telepítés módosításához kövesse az alábbi lépéseket: 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz. 
1. Válassza a **IoT Edge**lehetőséget.
1. Válassza **IoT Edge központi telepítések**lehetőséget. 

   ![IoT Edge üzemelő példányok megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Válassza ki a módosítani kívánt központi telepítést. 
1. Frissítse a következő mezőket: 
   * Cél feltétel
   * Metrikák – módosíthatja vagy törölheti a definiált mérőszámokat, illetve újakat adhat hozzá.
   * Címkék
   * Prioritás
1. Kattintson a **Mentés** gombra.
1. Kövesse az [üzembe helyezés figyelése](#monitor-a-deployment) című témakör lépéseit a változások megtekintéséhez. 

## <a name="delete-a-deployment"></a>Központi telepítés törlése

Ha töröl egy központi telepítést, minden eszköz a következő legmagasabb prioritású központi telepítésre kerül. Ha az eszközök nem felelnek meg az egyéb üzemelő példányok céljának, akkor a rendszer nem távolítja el a modulokat a központi telepítés törlésekor. 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz. 
1. Válassza a **IoT Edge**lehetőséget.
1. Válassza **IoT Edge központi telepítések**lehetőséget. 

   ![IoT Edge üzemelő példányok megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. A jelölőnégyzet használatával válassza ki a törölni kívánt központi telepítést. 
1. Válassza a **Törlés** elemet.
1. A rendszer értesíti arról, hogy ez a művelet törli ezt a központi telepítést, és az összes eszköz előző állapotára vált vissza.  Ez azt jelenti, hogy alacsonyabb prioritású üzemelő példány lesz érvényben.  Ha nincs más központi telepítés megcélozva, a rendszer nem távolítja el a modulokat. Ha el szeretné távolítani az összes modult az eszközről, hozzon létre egy üzembe helyezést nulla modulokkal, és telepítse azt ugyanarra az eszközre. A folytatáshoz válassza az **Igen** lehetőséget. 

## <a name="next-steps"></a>Következő lépések

További információ a [modulok IoT Edge eszközökön való telepítéséről](module-deployment-monitoring.md).
