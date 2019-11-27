---
title: Az automatikus központi telepítés létrehozása az Azure portal – Azure IoT Edge |} A Microsoft Docs
description: Eszközök automatikus csoportok az IoT Edge üzemelő példány létrehozása az Azure portal használatával
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 286bab7b7fdbe42190c32dabb42c59d6fc094b2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457367"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Üzembe helyezés és monitorozás az Azure portal használatával nagy mennyiségű IoT Edge-modulok

Hozzon létre egy **IoT Edge automatikus központi telepítést** a Azure Portalban, hogy egyszerre több eszközön is kezelhesse a folyamatban lévő központi telepítéseket. A IoT Edge automatikus központi telepítései a IoT Hub [automatikus Eszközkezelő](/azure/iot-hub/iot-hub-automatic-device-management) funkciójának részét képezik. A központi telepítések olyan dinamikus folyamatok, amelyek lehetővé teszik több modul üzembe helyezését több eszközön, nyomon követni a modulok állapotát és állapotát, és szükség esetén módosításokat hajthat végre. 

További információ: [IoT Edge automatikus központi telepítésének ismertetése egyetlen eszközön vagy nagy méretekben](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Címkék használatával eszközök azonosítása

Központi telepítés létrehozásához, akkor megadhatja, mely eszközöket szeretné befolyásolni. A Azure IoT Edge a **címkével** rendelkező eszközöket azonosítja az eszköz Twin-ben. Minden eszköz több címkével is rendelkezhet, amelyeket bármilyen módon meghatározhat a megoldásához. Például ha Ön kezeli a telephelyi intelligens épületek, előfordulhat, hogy hozzá a következő címkék eszköz:

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

Vannak egy központi telepítést hozhat létre öt lépést. A következő szakaszok egyenként végig. 

### <a name="step-1-name-and-label"></a>1\. lépés: Név és címke

1. Adjon meg egy egyedi nevet, amely legfeljebb 128 kisbetűk használata a központi telepítés. Kerülje a szóközöket, és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.
1. A központi telepítések nyomon követéséhez kulcs-érték párokkal adhat hozzá címkéket. Például: **HostPlatform** és **Linux**, vagy **Version** és **3.0.1**.
1. Kattintson a **tovább** gombra a második lépésre való áttéréshez. 

### <a name="step-2-add-modules-optional"></a>2\. lépés: (Nem kötelező) modulok hozzáadása

Akár 20 modult is hozzáadhat egy központi telepítéshez. 

Ha modulokat nem tartalmazó központi telepítést hoz létre, az eltávolítja a megcélzott eszközökről származó aktuális modulokat. 

Az Azure Stream Analytics modul hozzáadásához kövesse az alábbi lépéseket:

1. A lap **központi telepítési modulok** szakaszában kattintson a **Hozzáadás**gombra.
1. Válassza ki **Azure stream Analytics modult**.
1. Válassza ki az **előfizetést** a legördülő menüből.
1. Válassza ki a IoT **Edge-feladatot** a legördülő menüből.
1. Válassza a **Mentés** lehetőséget, hogy hozzáadja a modult a központi telepítéshez. 

Egyéni kód hozzáadása modulként, vagy manuálisan adja hozzá az Azure-szolgáltatások modul, kövesse az alábbi lépéseket:

1. A lap **Container Registry beállítások** szakaszában adja meg a központi telepítés moduljának lemezképeit tartalmazó privát tároló-beállításjegyzékek nevét és hitelesítő adatait. A IoT Edge ügynök a 500-es hibát fogja jelenteni, ha nem találja a tároló beállításjegyzékének hitelesítő adatait a Docker-rendszerképhez.
1. A lap **központi telepítési modulok** szakaszában kattintson a **Hozzáadás**gombra.
1. Válassza ki **IoT Edge modult**.
1. Adjon **nevet**a modulnak.
1. A **RENDSZERKÉP URI** mezőjébe írja be a modulhoz tartozó tároló rendszerképét. 
1. Adja meg a tárolóhoz átadandó **tároló-létrehozási beállításokat** . További információ: [Docker Create](https://docs.docker.com/engine/reference/commandline/create/).
1. Az **Újraindítási szabályzat**kiválasztásához használja a legördülő menüt. Az alábbi lehetőségek közül választhat: 
   * **Mindig** – a modul mindig újraindul, ha valamilyen okból leáll.
   * **soha** – a modul soha nem indul újra, ha bármilyen okból leáll.
   * **hiba** esetén – a modul újraindul, ha összeomlik, de nem, ha nem áll le tisztán. 
   * **sérült** állapotban – a modul újraindul, ha összeomlik vagy visszaadja a nem kifogástalan állapotot. Szolgáltatás minden modulnak a health állapot függvény végrehajtásához. 
1. A modul **kívánt állapotának** kiválasztásához használja a legördülő menüt. Az alábbi lehetőségek közül választhat:
   * a **futó** Futtatás az alapértelmezett beállítás. A modul futtatása után azonnal parancsfájlműveletekkel elindul.
   * **Leállítva** – az üzembe helyezés után a modul tétlen marad mindaddig, amíg meg nem hívja az Ön vagy egy másik modul indítását.
1. Válassza ki a **Module Twin kívánt tulajdonságainak megadása** lehetőséget, ha címkéket vagy egyéb tulajdonságokat szeretne felvenni a Twin modulba.
1. Adja meg a modul **környezeti változóit** . A környezeti változók konfigurációs adatokat biztosítanak egy modulhoz.
1. Válassza a **Mentés** lehetőséget, hogy hozzáadja a modult a központi telepítéshez. 

Miután konfigurálta az összes modult a központi telepítéshez, kattintson a **tovább** gombra a harmadik lépésre való áttéréshez.

### <a name="step-3-specify-routes-optional"></a>3\. lépés: Adja meg az útvonalakat (nem kötelező)

Útvonalak megadása, hogyan modulok kommunikálnak egymással egy telepítésben található. Alapértelmezés szerint a varázsló egy **route (útvonal** ) nevű útvonalat ad meg, amelyet **a/* be $upstream * *-ban határoz meg, ami azt jelenti, hogy bármely modulból kimenetet küld az IoT hubhoz.  

Adja hozzá vagy frissítse az útvonalakat a [deklarált útvonalak](module-composition.md#declare-routes)információi alapján, majd kattintson a **tovább** gombra a felülvizsgálati szakasz folytatásához.

### <a name="step-4-specify-metrics-optional"></a>4\. lépés: mérőszámok megadása (nem kötelező)

A metrikák a konfigurációs tartalom alkalmazásának eredményeképpen a különböző állapotok összegzési számát adják meg.

1. Adja meg a **metrika nevének**nevét.

1. Adja meg a **mérőszám feltételeinek**lekérdezését. A lekérdezés IoT Edge hub-modul Twin [jelentett tulajdonságain](module-edgeagent-edgehub.md#edgehub-reported-properties)alapul. A metrika a lekérdezés által visszaadott sorok számát jelöli.

   Például:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>5\. lépés: eszközök megcélzása

Az eszközökről a tags tulajdonság használatával a kívánt eszközök, amelyek megkapják a központi telepítés céljaként. 

Több központi telepítést megcélozhatnak ugyanarra az eszközre, mivel egy prioritást minden egyes üzembe helyezési kell adnia. Ha ütközés lép fel, a legmagasabb prioritású üzemelő példány (a nagyobb érték magasabb prioritást jelez) a WINS. Ha két üzembe helyezés azonos prioritású számot, azt, amelyik a legtöbb készült nemrégiben wins. 

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

## <a name="monitor-a-deployment"></a>Egy központi telepítésének figyelése

A központi telepítés a részletek megtekintéséhez és az azt futtató eszközök figyelését, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz. 
1. Válassza a **IoT Edge**lehetőséget.
1. Válassza **IoT Edge központi telepítések**lehetőséget. 

   ![IoT Edge-telepítések megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Vizsgálja meg a központi telepítés listában. Az egyes központi telepítések esetében az alábbi adatokat tekintheti meg:
   * **Azonosító** – az üzemelő példány neve.
   * **Cél feltétel** – a megcélzott eszközök definiálásához használt címke.
   * **Prioritás** – az üzemelő példányhoz rendelt prioritási szám.
   * A - **megcélzott** **rendszermetrikák** határozzák meg a célcsoport-kezelési feltételnek megfelelő IoT hub az ikrek számát **, valamint azt** , hogy hány eszközön lett alkalmazva a központi telepítési tartalom a IoT hub. 
   * **Eszköz metrikái** – a központi telepítésben szereplő IoT Edge eszközök száma a IoT Edge ügyfél futtatókörnyezetének sikerességéről vagy hibáiról.
   * **Egyéni metrikák** – a központi telepítéshez megadott összes metrika esetében az üzembe helyezési jelentéskészítési adatok IoT Edge eszközeinek száma.
   * **Létrehozás időpontja** – a központi telepítés létrehozásának időbélyegzője. Az időbélyegző ties megszüntetése, ha a két üzembe helyezés azonos prioritású szolgál. 
1. Válassza ki a figyelni kívánt telepítést.  
1. Vizsgálja meg az üzembe helyezés részleteiről. Lapok segítségével ellenőrizze az üzemelő példány részleteit.

## <a name="modify-a-deployment"></a>Központi telepítés módosítása

Amikor módosít egy központi telepítést, a módosítások azonnal replikálja az összes megcélzott eszközre. 

A célfeltétel frissít, ha elő a következő frissítéseket:

* Ha egy eszköz nem felelt meg a régi célfeltétel, de az új célfeltétel megfelel, és a központi telepítés rendszer a legmagasabb prioritású az eszközön, a központi telepítéshez az eszköz van alkalmazva. 
* Ha már nem a központi telepítés aktuálisan futó eszköz megfelel-e a célfeltétel, eltávolítja a központi telepítés és veszi fel a következő legmagasabb prioritású üzembe helyezés. 
* Ha már nem a központi telepítés aktuálisan futó eszköz megfelel-e a célként megadott feltétel, és nem felel meg a célfeltétel más központi telepítések, majd nincs változás történik az eszközön. Az eszköz addig a jelenlegi modulok fut, a jelenlegi állapotuk, de nem felügyelt már a központi telepítés részeként. Megfelel a célfeltétel, bármely más konfigurációért, miután eltávolítja a központi telepítés, és az új kiszolgálón vesz igénybe. 

Központi telepítés módosításához használja az alábbi lépéseket: 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz. 
1. Válassza a **IoT Edge**lehetőséget.
1. Válassza **IoT Edge központi telepítések**lehetőséget. 

   ![IoT Edge-telepítések megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Válassza ki a telepítést, amelyet módosítani szeretne. 
1. Hajtsa végre a frissítéseket a következő mezőket: 
   * Célfeltétel
   * Metrikák – módosíthatja vagy törölheti a definiált mérőszámokat, illetve újakat adhat hozzá.
   * Címkék
   * Prioritás
1. Kattintson a **Mentés** gombra.
1. Kövesse az [üzembe helyezés figyelése](#monitor-a-deployment) című témakör lépéseit a változások megtekintéséhez. 

## <a name="delete-a-deployment"></a>Üzemelő példányának törlése

Ha töröl egy központi telepítést, a következő legmagasabb prioritású üzembe helyezés az egyik eszközön sem igénybe vehet. Ha az eszközök nem felelnek meg a célfeltétel, bármely más konfigurációért, majd a modulok nem lesznek eltávolítva az üzemelő példány törlése. 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz. 
1. Válassza a **IoT Edge**lehetőséget.
1. Válassza **IoT Edge központi telepítések**lehetőséget. 

   ![IoT Edge-telepítések megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. A jelölőnégyzet segítségével válassza ki a törölni kívánt központi telepítés. 
1. Válassza a **Törlés** elemet.
1. Kérdés megtudhatja, hogy, hogy ez a művelet törli a központi telepítés és minden eszköz korábbi állapotára visszaállítani.  Ez azt jelenti, hogy alacsonyabb prioritású üzemelő példány lesz érvényben.  Ha nincs más központi telepítés megcélozva, a rendszer nem távolítja el a modulokat. Ha el kívánja távolítani az összes modult az eszközről, nulla modulok egy központi telepítés létrehozásához, és telepítheti az ugyanazokkal az eszközökkel. A folytatáshoz válassza az **Igen** lehetőséget. 

## <a name="next-steps"></a>Következő lépések

További információ a [modulok IoT Edge eszközökön való telepítéséről](module-deployment-monitoring.md).
