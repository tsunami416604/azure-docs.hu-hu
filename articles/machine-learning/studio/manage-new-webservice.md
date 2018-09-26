---
title: Az Azure Machine Learning Web Services portál használata |} A Microsoft Docs
description: Az Azure Machine Learning-munkaterületek, való hozzáférés kezelése és üzembe helyezése és gépi Tanulási API-webszolgáltatások kezelése
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: b62cf2ca-dd2a-4a83-bb54-469f948fb026
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.openlocfilehash: 4e1444f385859044b2dadd83c8e9841d8fc78bb0
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096411"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Az Azure Machine Learning Web Services portálon webszolgáltatás kezelése
A Machine Learning új és a klasszikus webszolgáltatások a Microsoft Azure Machine Learning Web Services portál használata kezelheti. A klasszikus webszolgáltatások és új Web services különböző mögöttes technológiák alapján, mivel mindegyikhez rendelkezik némileg eltérő felügyeleti képességeket.

A Machine Learning webszolgáltatások portál a következőket teheti:

* A web service használatának figyelése
* Konfigurálja a leírás, a kulcsok frissítése webes szolgáltatás (csak új), a storage key (csak új), lehetővé naplózás, frissítéséhez és engedélyezése, vagy tiltsa le a mintaadatokat.
* Törölje a webszolgáltatást.
* Hozzon létre, delete vagy update számlázási csomagok (csak új).
* Adja hozzá, és törölje a végpontot (csak Klasszikus modell)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Új erőforrás-kezelő kezeléséhez engedélyekkel-alapú webszolgáltatások

Új webszolgáltatások az Azure-erőforrások üzemelnek. Ennek megfelelően kell rendelkeznie a megfelelő engedélyekkel ahhoz, hogy új webes szolgáltatások telepítésére és kezelésére.  Telepítenie és felügyelnie az új webszolgáltatások, hozzá kell rendelni egy közreműködőjének vagy rendszergazdájának szerepkört az előfizetésre, amelyre a web service telepítve van. A machine learning-munkaterület egy másik felhasználó meghívása akkor, ha kell rendelnie őket az előfizetés közreműködőjének vagy rendszergazdájának szerepkörhöz üzembe helyezése vagy webszolgáltatások kezelése előtt. 

Ha a felhasználó nem rendelkezik a megfelelő engedélyekkel ahhoz, hogy az Azure Machine Learning Web Services portálon erőforrások eléréséhez, a következő hibaüzenetet kapja, egy webszolgáltatás üzembe helyezése közben:

*A webes szolgáltatás telepítése nem sikerült. Ez a fiók nem rendelkezik megfelelő hozzáférési jogosultsággal az Azure-előfizetést, amely tartalmazza a munkaterületen. Annak érdekében, hogy a webszolgáltatás üzembe helyezése az Azure-ba, ugyanazzal a fiókkal kell hívni a munkaterületet, és hozzáférést kell biztosítani az Azure-előfizetést, amely tartalmazza a munkaterületen.*

Munkaterület létrehozásával kapcsolatos további információkért lásd: [létrehozása és megosztása az Azure Machine Learning-munkaterület](create-workspace.md).

Hozzáférési engedélyek beállítása a további információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Új webszolgáltatások kezelése
Az új webszolgáltatások kezelése:

1. Jelentkezzen be a [a Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/quickstart) az Microsoft Azure portal-fiók – használja az Azure-előfizetéssel társított fiókot.
2. Kattintson a menü **webszolgáltatások**.

Ez az előfizetés üzembe helyezett webszolgáltatások listáját jeleníti meg. 

Webszolgáltatás kezelése, kattintson a Web Services. A Web Services-oldaláról a következőket teheti:

* Kattintson a web service lehet kezelni.
* Kattintson a számlázási csomag a webszolgáltatás frissíteni.
* Webszolgáltatás törlése.
* Másolja egy webszolgáltatás, és helyezze üzembe azt egy másik régióban.

Ha rákattint egy webszolgáltatás, a szolgáltatás rövid weboldal nyílik meg. A web service a rövid útmutató lapon a két menüpontok, amelyek lehetővé teszik, hogy a webszolgáltatás kezelése:

* **IRÁNYÍTÓPULT** – lehetővé teszi a webes szolgáltatás használati adatok megtekintéséhez.
* **KONFIGURÁLÁS** – lehetővé teszi, hogy a leíró szöveg hozzáadása a kulcsot a Web service társított storage-fiók frissítése és engedélyezi vagy letiltja a mintaadatok.

### <a name="monitoring-how-the-web-service-is-being-used"></a>A web service használatának figyelése
Kattintson a **IRÁNYÍTÓPULT** fülre.

Az irányítópulton megtekintheti a webes szolgáltatás teljes használatának egy időszakon belül. Az időszak megtekintéséhez a jobb felső sarokban a használati diagramot a időszak legördülő menüből kiválaszthatja. Az irányítópult a következő információkat jelenít meg:

* **Kérelmek időalapú** kérések száma lépés grafikonon jeleníti meg a kijelölt időszakra. Ha használat kiugrások azonosítása segíthet.
* **Kérés-válasz kérelmek** a szolgáltatás megkapta a kijelölt időszak és ezek közül hány sikertelen kérés-válasz hívások teljes számát jeleníti meg.
* **Átlagos kérelem-válasz számítási idő** átlagát a fogadott kérések végrehajtásához szükséges időt jeleníti meg.
* **Batch-kérelmek** a szolgáltatás megkapta a kijelölt időszak és ezek közül hány sikertelen kérések összesített számát jeleníti meg.
* **Átlagos késés feladat** átlagát a fogadott kérések végrehajtásához szükséges időt jeleníti meg.
* **Hibák** bekövetkezett hibák összesített számát jeleníti meg a webszolgáltatás-hívások.
* **Költségek Services** jeleníti meg a szolgáltatáshoz társított a számlázási csomag díjai.

### <a name="configuring-the-web-service"></a>A webszolgáltatás konfigurálása
Kattintson a **KONFIGURÁLÁSA** menüpont.

Az alábbi tulajdonságok frissíthetők:

* **Leírás** lehetővé teszi, hogy adja meg a webszolgáltatás leírását.
* **Cím** lehetővé teszi, hogy adjon meg egy címet a webszolgáltatás
* **Kulcsok** lehetővé teszi, hogy az elsődleges és másodlagos API-kulcsainak rotálása.
* **Tárfiók kulcsa** lehetővé teszi, hogy a kulcsot a Web service módosítások társított storage-fiók frissítéséhez. 
* **Engedélyezze a mintaadatok** lehetővé teszi, hogy adja meg a mintaadatokat, amellyel tesztelheti a kérés-válasz szolgáltatás. Ha a webszolgáltatás a Machine Learning Studióban létrehozott, a mintaadatok átveszi a rendszer az adatokat a használt a modell betanításához. Ha a szolgáltatás programozott módon létrehozott, az adatok a példaadatokat a JSON-csomag részeként megadott származik.

### <a name="managing-billing-plans"></a>Számlázási tervek kezelése
Kattintson a **csomagok** menüpont a webes szolgáltatások rövid oldaláról. A csomag társított webszolgáltatás kezelése a csomagot is kattinthat.

* **Új** lehetővé teszi, hogy hozzon létre egy új csomagot.
* **Csomag hozzáadása/eltávolítása példány** lehetővé teszi, hogy "horizontális felskálázása" kapacitás egy meglévő csomagot.
* **Frissítés/alacsonyabb szintű** lehetővé teszi, hogy "vertikális felskálázás" kapacitás egy meglévő csomagot.
* **Törlés** lehetővé teszi, hogy a csomag törlése.

Kattintson egy tervet a megfelelő irányítópult megtekintéséhez. Az irányítópult biztosít pillanatkép vagy a csomag használati egy kiválasztott időszakban. Válassza ki az adott időszakban megtekintéséhez, kattintson a **időszak** az irányítópult jobb felső részén a legördülő listából. 

A terv irányítópultján a következő információkat biztosítja:

* **Terv leírása** a költségek és a kapacitás a tervhez társított információit jeleníti meg.
* **Használati megtervezése** tranzakciók és a számítási óra, amely rendelkezik a csomag lett terhelve számát jeleníti meg.
* **A Web Services** webszolgáltatásokat, amelyekhez ezt a tervet használja számát jeleníti meg.
* **Az első webes szolgáltatás által hívások** jelenít meg arról, hogy a terv vannak terhelve hívások négy webszolgáltatások.
* **Az első webes szolgáltatások által a számítási óra** a négy webszolgáltatások által használt számítási erőforrások díjkötelesek, szemben a terv jeleníti meg.

## <a name="manage-classic-web-services"></a>A klasszikus webszolgáltatások kezelése
> [!NOTE]
> Ebben a szakaszban a klasszikus webszolgáltatások esetében az Azure Machine Learning webszolgáltatások portálján kezelése történik. A Machine Learning Studio és az Azure Portalon a klasszikus webszolgáltatások kezelésével kapcsolatban további információkért lásd: [kezelése az Azure Machine Learning-munkaterület](manage-workspace.md).
> 
> 

A klasszikus webszolgáltatások kezelése:

1. Jelentkezzen be a [a Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/quickstart) az Microsoft Azure portal-fiók – használja az Azure-előfizetéssel társított fiókot.
2. Kattintson a menü **klasszikus webszolgáltatások**.

Klasszikus webszolgáltatás kezeléséhez kattintson **klasszikus webszolgáltatások**. A klasszikus webszolgáltatások oldaláról a következőket teheti:

* Kattintson a web service kapcsolódó végpontok megtekintéséhez.
* Webszolgáltatás törlése.

Ha Ön kezeli a klasszikus webszolgáltatások, kezelheti a végpontok mindegyike külön-külön. Amikor egy webszolgáltatás, a webszolgáltatások lapon kattint, megnyílik a szolgáltatáshoz tartozó végpontok listáját. 

A klasszikus webszolgáltatás végpont lapon hozzáadhat és a szolgáltatás a végpontok törlése. Végpontok hozzáadásával kapcsolatos további információkért lásd: [végpontok létrehozása](create-endpoint.md).

Kattintson a végpontok a web service gyors útmutató lap megnyitásához. A rövid útmutató lapon kétféle menü, amelyek lehetővé teszik, hogy a webszolgáltatás kezelése:

* **IRÁNYÍTÓPULT** – lehetővé teszi a webes szolgáltatás használati adatok megtekintéséhez.
* **KONFIGURÁLÁS** – lehetővé teszi, hogy adja hozzá a leíró szöveg hibanaplózás kapcsolhatja be és ki, a kulcsot a Web service társított storage-fiók frissítéséhez és engedélyezését és letiltását mintaadatok.

### <a name="monitoring-how-the-web-service-is-being-used"></a>A web service használatának figyelése
Kattintson a **IRÁNYÍTÓPULT** fülre.

Az irányítópulton megtekintheti a webes szolgáltatás teljes használatának egy időszakon belül. Az időszak megtekintéséhez a jobb felső sarokban a használati diagramot a időszak legördülő menüből kiválaszthatja. Az irányítópult a következő információkat jelenít meg:

* **Kérelmek időalapú** kérések száma lépés grafikonon jeleníti meg a kijelölt időszakra. Ha használat kiugrások azonosítása segíthet.
* **Kérés-válasz kérelmek** a szolgáltatás megkapta a kijelölt időszak és ezek közül hány sikertelen kérés-válasz hívások teljes számát jeleníti meg.
* **Átlagos kérelem-válasz számítási idő** átlagát a fogadott kérések végrehajtásához szükséges időt jeleníti meg.
* **Batch-kérelmek** a szolgáltatás megkapta a kijelölt időszak és ezek közül hány sikertelen kérések összesített számát jeleníti meg.
* **Átlagos késés feladat** átlagát a fogadott kérések végrehajtásához szükséges időt jeleníti meg.
* **Hibák** bekövetkezett hibák összesített számát jeleníti meg a webszolgáltatás-hívások.
* **Költségek Services** jeleníti meg a szolgáltatáshoz társított a számlázási csomag díjai.

### <a name="configuring-the-web-service"></a>A webszolgáltatás konfigurálása
Kattintson a **KONFIGURÁLÁSA** menüpont.

Az alábbi tulajdonságok frissíthetők:

* **Leírás** lehetővé teszi, hogy adja meg a webszolgáltatás leírását. Leírás megadása nem kötelező kitölteni.
* **Naplózás** lehetővé teszi, hogy engedélyezi vagy letiltja a naplózást a végpont a hiba. A naplózás további információkért lásd: Enable [Machine Learning webszolgáltatások naplózása](web-services-logging.md).
* **Engedélyezze a mintaadatok** lehetővé teszi, hogy adja meg a mintaadatokat, amellyel tesztelheti a kérés-válasz szolgáltatás. Ha a webszolgáltatás a Machine Learning Studióban létrehozott, a mintaadatok átveszi a rendszer az adatokat a használt a modell betanításához. Ha a szolgáltatás programozott módon létrehozott, az adatok a példaadatokat a JSON-csomag részeként megadott származik.


