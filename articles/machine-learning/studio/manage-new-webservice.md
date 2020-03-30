---
title: Webszolgáltatások kezelése
titleSuffix: ML Studio (classic) - Azure
description: A Machine Learning új és klasszikus webszolgáltatásait a Microsoft Azure Machine Learning Web Services portálon kezelheti. Mivel a klasszikus webszolgáltatások és az új webszolgáltatások különböző mögöttes technológiákon alapulnak, mindegyikhez némileg eltérő felügyeleti lehetőségek állnak.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 2277aa3de5955efe5a3e4cb938fa557352f89006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217972"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Webszolgáltatás kezelése az Azure Machine Learning Studio (klasszikus) webszolgáltatási portálon

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

A Machine Learning új és klasszikus webszolgáltatásait a Microsoft Azure Machine Learning Web Services portálon kezelheti. Mivel a klasszikus webszolgáltatások és az új webszolgáltatások különböző mögöttes technológiákon alapulnak, mindegyikhez némileg eltérő felügyeleti lehetőségek állnak.

A Machine Learning Web Services portálon a következőket teheti:

* A webszolgáltatás használatának figyelése.
* Konfigurálja a leírást, frissítse a webszolgáltatás kulcsait (csak új), frissítse a tárfiók kulcsát (csak új), engedélyezze a naplózást, és engedélyezze vagy tiltsa le a mintaadatokat.
* Törölje a webszolgáltatást.
* Számlázási csomagok létrehozása, törlése vagy frissítése (csak új).
* Végpontok hozzáadása és törlése (csak klasszikus)

>[!NOTE]
>A [klasszikus (klasszikus) Machine Learning Studióban](https://studio.azureml.net) is kezelheti a klasszikus webszolgáltatásokat a **Webservices** lapon.

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Az Új erőforrás-kezelő alapú webszolgáltatások kezelésére vonatkozó engedélyek

Az új webszolgáltatások Azure-erőforrásként vannak üzembe helyezve. Így rendelkeznie kell a megfelelő engedélyekkel az új webszolgáltatások telepítéséhez és kezeléséhez.  Új webszolgáltatások üzembe helyezéséhez vagy kezeléséhez hozzá kell rendelnie egy közreműködői vagy rendszergazdai szerepkört azon az előfizetésen, amelyre a webszolgáltatás telepítve van. Ha meghívja egy másik felhasználót egy gépi tanulási munkaterületre, hozzá kell rendelnie őket egy közreműködői vagy rendszergazdai szerepkörhöz az előfizetésen, mielőtt üzembe helyezhetik vagy kezelhetik a webszolgáltatásokat. 

Ha a felhasználó nem rendelkezik a megfelelő engedélyekkel az Erőforrások eléréséhez az Azure Machine Learning webservices portálon, a következő hibaüzenet jelenik meg, amikor egy webszolgáltatás üzembe helyezésére próbál:

*A webszolgáltatás telepítése nem sikerült. Ez a fiók nem rendelkezik elegendő hozzáféréssel a munkaterületet tartalmazó Azure-előfizetéshez. Ahhoz, hogy egy webszolgáltatás az Azure-ban, ugyanazt a fiókot meg kell hívni a munkaterületre, és hozzáférést kell biztosítani az Azure-előfizetés, amely tartalmazza a munkaterületet.*

A munkaterület létrehozásáról az [Azure Machine Learning Studio (klasszikus) munkaterület létrehozása és megosztása című témakörben talál](create-workspace.md)további információt.

A hozzáférési engedélyek beállításáról a [Hozzáférés kezelése az RBAC és az Azure Portal használatával című témakörben](../../role-based-access-control/role-assignments-portal.md)talál további információt.


## <a name="manage-new-web-services"></a>Új webszolgáltatások kezelése
Az új webszolgáltatások kezelése:

1. Jelentkezzen be a [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portálra a Microsoft Azure-fiókjával – használja az Azure-előfizetéshez társított fiókot.
2. Kattintson a menü **Web Services parancsára.**

Ez az előfizetéshez telepített webszolgáltatások listáját jeleníti meg. 

Webszolgáltatás kezeléséhez kattintson a Webszolgáltatások gombra. A Webszolgáltatások lapon a következőket teheti:

* A webszolgáltatásra kattintva kezelheti azt.
* A webszolgáltatás számlázási tervére kattintva frissítheti azt.
* Webszolgáltatás törlése.
* Másolja a webszolgáltatást, és telepítse egy másik régióba.

Amikor egy webszolgáltatásra kattint, megnyílik a webszolgáltatás rövid útmutató lapja. A webszolgáltatás rövid útmutató lapján két menübeállítás áll rendelkezésre, amelyek lehetővé teszik a webszolgáltatás kezelését:

* **DASHBOARD** - Lehetővé teszi a webszolgáltatás használatának megtekintését.
* **CONFIGURE** - Lehetővé teszi leíró szöveg hozzáadását, a webszolgáltatáshoz társított tárfiók kulcsának frissítését, valamint a mintaadatok engedélyezését vagy letiltását.

### <a name="monitoring-how-the-web-service-is-being-used"></a>A webszolgáltatás használatának figyelése
Kattintson a **DASHBOARD** fülre.

Az irányítópulton megtekintheti a webszolgáltatás általános használatát egy adott időszakban. A megtekinteni kívánt időszakot a használati diagramok jobb felső részén, az Időszak legördülő menüben választhatja ki. Az irányítópult a következő információkat jeleníti meg:

* **A kérelmek idővel** lépésgrafikont jelenít meg a kérelmek számáról a kiválasztott időszakban. Ez segíthet azonosítani, ha a használat kiugrásai tapasztalhatók.
* **A kérelem-válasz kérelmek** a szolgáltatás által a kiválasztott időszakban fogadott kérés-válasz hívások teljes számát jeleníti meg, és hogy közülük hány sikertelen volt.
* **Átlagos kérelem-válasz számítási idő** a fogadott kérelmek végrehajtásához szükséges idő átlagát jeleníti meg.
* **A kötegelt kérelmek** megjeleníti a szolgáltatás által a kiválasztott időszakban fogadott kötegelt kérelmek teljes számát, és hogy közülük hány sikertelen volt.
* **Az átlagos feladatkésés** a fogadott kérelmek végrehajtásához szükséges idő átlagát jeleníti meg.
* **A hibák** a webszolgáltatás hívásain előforduló hibák összesített számát jelenítik meg.
* **A Szolgáltatási költségek** a szolgáltatáshoz társított számlázási csomag díjait jeleníti meg.

### <a name="configuring-the-web-service"></a>A webszolgáltatás konfigurálása
Kattintson a **CONFIGURE** menügombra.

A következő tulajdonságokat frissítheti:

* **A Leírás** lehetővé teszi a webszolgáltatás leírásának megadását.
* **A Cím** lehetővé teszi a webszolgáltatás címének megadását
* **A kulcsok** lehetővé teszik az elsődleges és másodlagos API-kulcsok elforgatását.
* **A tárfiók kulcslehetővé** teszi a webszolgáltatás változásaihoz társított tárfiók kulcsának frissítését. 
* **A Mintaadatok engedélyezése** lehetővé teszi, hogy mintaadatokat adjon meg, amelyek segítségével tesztelheti a kérelem-válasz szolgáltatást. Ha a webszolgáltatás a Machine Learning Studio (klasszikus), a minta adatok a modell betanításához használt adatokból származik. Ha a szolgáltatást programozott módon hozta létre, az adatok a JSON-csomag részeként megadott példaadatokból származnak.

### <a name="managing-billing-plans"></a>Számlázási csomagok kezelése
Kattintson a Webes szolgáltatások rövid útmutató lapján a **Sémák** menüre. Az adott webszolgáltatáshoz társított tervre kattintva kezelheti a tervet.

* **Az Új** lehetővé teszi új terv létrehozását.
* **A Csomag hozzáadása/eltávolítása példány** lehetővé teszi egy meglévő terv kapacitáshozzáadásának "horizontális felskálázását".
* **A Frissítés/DownGrade** lehetővé teszi egy meglévő terv kapacitásnövelési "felskálázását".
* **A Törlés** lehetővé teszi a terv törlését.

Kattintson egy tervre az irányítópult megtekintéséhez. Az irányítópult pillanatképet vagy tervhasználatot biztosít egy kiválasztott időszakban. A megtekinteni kívánt időszak kiválasztásához kattintson az irányítópult jobb felső részén található **Időszak** legördülő menüre. 

A terv irányítópultja a következő információkat tartalmazza:

* **A Terv leírása** a tervhez kapcsolódó költségekre és kapacitásra vonatkozó információkat jeleníti meg.
* **A Csomag kihasználtsága** a tervvel szemben felszámított tranzakciók és számítási órák számát jeleníti meg.
* **A Web Services** megjeleníti a sémát használó webszolgáltatások számát.
* **A Legnépszerűbb webszolgáltatás hívásokkal megjeleníti** a csomaggal szemben felszámított hívások at végző négy legfontosabb webszolgáltatást.
* **A Legjobb webszolgáltatások a Compute Hrs szerint** a négy legfontosabb webszolgáltatást jeleníti meg, amelyek a tervvel szemben felszámított számítási erőforrásokat használják.

## <a name="manage-classic-web-services"></a>Klasszikus webszolgáltatások kezelése
> [!NOTE]
> Az ebben a szakaszban található eljárások az Azure Machine Learning Web Services portálon keresztül a klasszikus webszolgáltatások kezeléséhez fontosak. A klasszikus webszolgáltatások kezelése a Machine Learning Studio (klasszikus) és az Azure portalon keresztül, [az Azure Machine Learning Studio (klasszikus) munkaterület kezelése című témakörben található.](manage-workspace.md)
> 
> 

A klasszikus webszolgáltatások kezelése:

1. Jelentkezzen be a [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) portálra a Microsoft Azure-fiókjával – használja az Azure-előfizetéshez társított fiókot.
2. Kattintson a menü **Klasszikus webszolgáltatások parancsára.**

Klasszikus webszolgáltatás kezeléséhez kattintson a **Klasszikus webszolgáltatások gombra.** A Klasszikus webszolgáltatások lapon a következőket teheti:

* Kattintson a webszolgáltatásra a társított végpontok megtekintéséhez.
* Webszolgáltatás törlése.

Klasszikus webszolgáltatás kezelése kor az egyes végpontokat külön-külön kezelheti. Amikor a WebServices lapon egy webszolgáltatásra kattint, megnyílik a szolgáltatáshoz társított végpontok listája. 

A Klasszikus webszolgáltatás végpontlapján végpontokat adhat hozzá és törölhet a szolgáltatáson. A végpontok hozzáadásáról a [Végpontok létrehozása című](create-endpoint.md)témakörben talál további információt.

Kattintson a végpontok egyikére a webszolgáltatás rövid útmutatólapjának megnyitásához. A Gyorsútmutató lapon két menübeállítás teszi lehetővé a webszolgáltatás kezelését:

* **DASHBOARD** - Lehetővé teszi a webszolgáltatás használatának megtekintését.
* **CONFIGURE** - Lehetővé teszi leíró szöveg hozzáadását, a hibanaplózás be- és kikapcsolását, a webszolgáltatáshoz társított tárfiók kulcsának frissítését, valamint a mintaadatok engedélyezését és letiltását.

### <a name="monitoring-how-the-web-service-is-being-used"></a>A webszolgáltatás használatának figyelése
Kattintson a **DASHBOARD** fülre.

Az irányítópulton megtekintheti a webszolgáltatás általános használatát egy adott időszakban. A megtekinteni kívánt időszakot a használati diagramok jobb felső részén, az Időszak legördülő menüben választhatja ki. Az irányítópult a következő információkat jeleníti meg:

* **A kérelmek idővel** lépésgrafikont jelenít meg a kérelmek számáról a kiválasztott időszakban. Ez segíthet azonosítani, ha a használat kiugrásai tapasztalhatók.
* **A kérelem-válasz kérelmek** a szolgáltatás által a kiválasztott időszakban fogadott kérés-válasz hívások teljes számát jeleníti meg, és hogy közülük hány sikertelen volt.
* **Átlagos kérelem-válasz számítási idő** a fogadott kérelmek végrehajtásához szükséges idő átlagát jeleníti meg.
* **A kötegelt kérelmek** megjeleníti a szolgáltatás által a kiválasztott időszakban fogadott kötegelt kérelmek teljes számát, és hogy közülük hány sikertelen volt.
* **Az átlagos feladatkésés** a fogadott kérelmek végrehajtásához szükséges idő átlagát jeleníti meg.
* **A hibák** a webszolgáltatás hívásain előforduló hibák összesített számát jelenítik meg.
* **A Szolgáltatási költségek** a szolgáltatáshoz társított számlázási csomag díjait jeleníti meg.

### <a name="configuring-the-web-service"></a>A webszolgáltatás konfigurálása
Kattintson a **CONFIGURE** menügombra.

A következő tulajdonságokat frissítheti:

* **A Leírás** lehetővé teszi a webszolgáltatás leírásának megadását. A leírás kötelező mező.
* **A naplózás** lehetővé teszi a hibanaplózás engedélyezését vagy letiltását a végponton. A naplózásról a [Machine Learning webszolgáltatások naplózásának](web-services-logging.md)engedélyezése című témakörben talál további információt.
* **A Mintaadatok engedélyezése** lehetővé teszi, hogy mintaadatokat adjon meg, amelyek segítségével tesztelheti a kérelem-válasz szolgáltatást. Ha a webszolgáltatás a Machine Learning Studio (klasszikus), a minta adatok a modell betanításához használt adatokból származik. Ha a szolgáltatást programozott módon hozta létre, az adatok a JSON-csomag részeként megadott példaadatokból származnak.


