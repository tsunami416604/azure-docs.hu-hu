---
title: Az Azure Machine Learning webszolgáltatások portálon |} Microsoft Docs
description: Azure Machine Learning munkaterületekhez való hozzáférés kezelése és szolgáltatások telepítésére és kezelésére ML API webkiszolgáló
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: b62cf2ca-dd2a-4a83-bb54-469f948fb026
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.openlocfilehash: d558db176f2fa6888ead1dd3228f31d942049fc4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Kezelheti egy webszolgáltatás-bővítmény, az Azure Machine Learning webszolgáltatások portál használatával
A Machine Learning új és a Microsoft Azure Machine Learning webszolgáltatások portálon klasszikus webszolgáltatások segítségével kezelheti. Klasszikus webszolgáltatások és új webes szolgáltatások különböző alapul szolgáló technológiák alapján, mert némileg eltérő felügyeleti képességek állnak a rendelkezésére az egyes őket.

A Machine Learning webszolgáltatások portál a következőket teheti:

* A webszolgáltatás használatának figyelése
* Konfigurálja a leírás, a kulcsok frissítése a webes szolgáltatás (csak új), frissítse a tárolási fiók kulcs (csak új), engedélyezése, naplózása és adatátvitel engedélyezése vagy letiltása a minta.
* A webes szolgáltatás törléséhez.
* Létrehozás, törlés vagy frissítés számlázási tervek (csak új).
* Hozzáadni és törölni végpontjai (csak klasszikus)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Új erőforrás-kezelő kezeléséhez szükséges jogokat alapú webszolgáltatások

Új webes szolgáltatások, Azure-erőforrások vannak telepítve. Mint ilyen telepítéséhez és kezeléséhez új webes szolgáltatásokat a megfelelő engedélyekkel kell rendelkeznie.  Központi telepítése, illetve új webszolgáltatások kezelése az előfizetést, amelyhez a webszolgáltatások telepítése egy közreműködő vagy a rendszergazda szerepkört kell hozzárendelni. Ha meghívni egy másik felhasználót egy machine learning munkaterülettel, hozzá kell rendelnie azokat egy közreműködő vagy a rendszergazda szerepkört az előfizetés ahhoz, azok központilag telepíthetne vagy kezelhetne webszolgáltatások. 

Ha a felhasználó nem rendelkezik megfelelő engedélyekkel az Azure Machine Learning webszolgáltatások portálon erőforrások eléréséhez, hibaüzenetet kapnak, egy webszolgáltatás-bővítmény telepítése közben:

*A webes szolgáltatás telepítése sikertelen volt. Ez a fiók nem rendelkezik megfelelő hozzáférési jogosultsággal az Azure-előfizetés, amely tartalmazza a munkaterületen. Ahhoz, hogy egy webszolgáltatás-bővítmény telepítése az Azure-ba, ugyanazt a fiókot meg kell hívni a munkaterületet, és hozzáférést kell biztosítani az Azure-előfizetés, amely tartalmazza a munkaterületen.*

A munkaterület létrehozása további információkért lásd: [létrehozása és a megosztáshoz egy Azure Machine Learning munkaterülettel](create-workspace.md).

A hozzáférési engedélyek beállítása további információkért lásd: [access-hozzárendelések megtekintése a felhasználók és csoportok az Azure portálon - nyilvános előzetes](../../active-directory/role-based-access-control-manage-assignments.md).


## <a name="manage-new-web-services"></a>Új webes szolgáltatások kezelése
Az új webes szolgáltatások kezelésére:

1. Jelentkezzen be a [Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/quickstart) portálra a Microsoft Azure fiók – az Azure-előfizetéssel társított fiókot használja.
2. A menüben kattintson a **webszolgáltatások**.

Ez megjeleníti az előfizetéshez tartozó telepített webes szolgáltatás. 

Egy webszolgáltatás-bővítmény kezeli, kattintson a webszolgáltatásokat. A Web Services oldalról a következőket teheti:

* Kattintson a webszolgáltatás a kezeléséhez.
* Kattintson a számlázási tervezi meg a webszolgáltatás a frissítést.
* Egy webszolgáltatás-bővítmény törlése.
* Másolja át egy webszolgáltatás, és telepítse azt egy másik régióban.

Ha egy webszolgáltatás-bővítmény gombra kattint, a szolgáltatás gyors üzembe helyezés nyílik meg. A szolgáltatás gyors üzembe helyezés weblap rendelkezik két menü elemeit, amelyek lehetővé teszik a webes szolgáltatás kezelése:

* **IRÁNYÍTÓPULT** -lehetővé teszi webes szolgáltatás használata.
* **KONFIGURÁLÁS** – lehetővé teszi a leíró szöveget, frissítse a kulcsot a tárfiók a webszolgáltatás társított és adatátvitel engedélyezése vagy letiltása minta.

### <a name="monitoring-how-the-web-service-is-being-used"></a>A webszolgáltatás használatának figyelése
Kattintson a **IRÁNYÍTÓPULT** fülre.

Az irányítópulton megtekintheti a webszolgáltatás összesített használatát egy meghatározott időtartamra vonatkozóan. Kiválaszthatja a időszak időszak legördülő menüjében a használati diagramok jobb felső sarkában lévő megtekintéséhez. Az irányítópult a következő információkat tartalmazza:

* **Kérelmek keresztül idő** kérések száma lépés grafikonját jeleníti meg a kijelölt időszakra. Segíthet megállapítani, hogy a használati igényeiben jelentkező problémát.
* **Kérés-válasz kérelmek** jeleníti meg a szolgáltatás által fogadott keresztül, a kijelölt időszak és azok számát nem sikerült a kérés-válasz hívások száma.
* **Átlagos kérés-válasz számítási ideje** a fogadott kérések végrehajtásához szükséges idő átlagát jeleníti meg.
* **Kötegelt kérelmek** a szolgáltatás által fogadott keresztül, a kijelölt időszak és azok számát nem sikerült kötegelt kérelmek teljes számát jeleníti meg.
* **Átlagos késés feladat** a fogadott kérések végrehajtásához szükséges idő átlagát jeleníti meg.
* **Hibák** bekövetkezett hibák összesített számát jeleníti meg a webszolgáltatás hívásokat.
* **Szolgáltatási költségei** a számlázási terv a szolgáltatással kapcsolatos díjakat jeleníti meg.

### <a name="configuring-the-web-service"></a>A webszolgáltatás konfigurálása
Kattintson a **KONFIGURÁLÁSA** menüjét.

Frissítheti a következő tulajdonságokkal:

* **Leírás** lehetővé teszi, hogy adja meg a webszolgáltatás leírását.
* **Cím** lehetővé teszi, hogy adjon meg egy címet a webszolgáltatás
* **Kulcsok** lehetővé teszi az elsődleges és másodlagos API-kulcsokat elforgatása.
* **Tárfiók kulcsa** lehetővé teszi a tárfiók a webes szolgáltatás módosításait társított kulcs. 
* **Engedélyezze a mintaadatok** lehetővé teszi, hogy adja meg a mintaadatokat a kérés-válasz szolgáltatás teszteléséhez használható. Ha a webszolgáltatás a Machine Learning Studióban létrehozott, a mintaadatok forrása az adatokat a használt modellje betanításához. Ha a szolgáltatás programozott módon létrehozott, az adatok a példaadatokat a JSON-csomag részeként megadott származik.

### <a name="managing-billing-plans"></a>Számlázási tervek kezelése
Kattintson a **tervek** menüpont a web services gyors üzembe helyezés lapról. A terv webszolgáltatás társított terv kezelésére is kattinthat.

* **Új** hozhat létre egy új tervet.
* **Hozzáadása terv példány** teszi lehetővé "horizontális felskálázás" egy meglévő terv kapacitását.
* **Frissítés/alacsonyabb szintre való visszalépést** teszi lehetővé "vertikális felskálázás" egy meglévő terv kapacitását.
* **Törlés** lehetővé teszi a csomag törlése.

Kattintson egy tervet az irányítópult megtekintéséhez. Az irányítópult lehetővé teszi az pillanatképét vagy a terv használata egy kijelölt időtartamra vonatkozóan. Válassza ki az időszakot, megtekintéséhez, kattintson a **időszak** jobb felső sarkában irányítópult a legördülő menüből. 

A terv irányítópult a következő adatokat tartalmazza:

* **Terv leírása** a költségeket és a kapacitás, a terv társított információit jeleníti meg.
* **Tervezze meg használati** tranzakciók és számítási órák, amelyek alapján a terv felszámítani számát jeleníti meg.
* **Webszolgáltatások** Web services által használt a terv számát jeleníti meg.
* **Webes szolgáltatás által hívások felső** létesített hívások, amely alapján a terv van szó első négy webes szolgáltatások jeleníti meg.
* **Webszolgáltatások TOP számítási kezdéssel alapján** jeleníti meg az első négy webszolgáltatások által használt számítási erőforrásokat, amelyek alapján a terv van szó.

## <a name="manage-classic-web-services"></a>Klasszikus webszolgáltatások kezelése
> [!NOTE]
> A jelen szakaszban ismertetett eljárások szempontjából kezelése a klasszikus webszolgáltatások az Azure Machine Learning webszolgáltatások portálon keresztül. A Machine Learning Studio és az Azure-portálon keresztül klasszikus webszolgáltatások kezeléséről további információért lásd: [kezelése az Azure Machine Learning-munkaterület](manage-workspace.md).
> 
> 

A klasszikus webszolgáltatások kezelése:

1. Jelentkezzen be a [Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net/quickstart) portálra a Microsoft Azure fiók – az Azure-előfizetéssel társított fiókot használja.
2. A menüben kattintson a **klasszikus webszolgáltatások**.

A klasszikus webszolgáltatás kezeléséhez kattintson **klasszikus webszolgáltatások**. A klasszikus webszolgáltatások oldalról a következőket teheti:

* Kattintson a webszolgáltatás számára a társított végpontok megtekintéséhez.
* Egy webszolgáltatás-bővítmény törlése.

Ha Ön kezeli a klasszikus webes szolgáltatás, kezelhetők a végpontok külön-külön. Ha egy webszolgáltatás-bővítmény a Web Services oldalon gombra kattint, a szolgáltatáshoz tartozó végpontok listáját nyitja meg. 

A klasszikus webszolgáltatás végpont lapon vegye fel, és a szolgáltatás végpontok törlése. Végpontok hozzáadásával kapcsolatos további információkért lásd: [végpontok létrehozása](create-endpoint.md).

Kattintson a végpontok a webes szolgáltatás gyors üzembe helyezési oldal megnyitásához. A gyors üzembe helyezés lapon két módon menü, amely lehetővé teszi a webszolgáltatás felügyeletét:

* **IRÁNYÍTÓPULT** -lehetővé teszi webes szolgáltatás használata.
* **KONFIGURÁLÁS** - lehetővé teszi a leíró szöveget hibanaplózás kapcsolja be- és kikapcsolható, frissítse a kulcsot a tárfiók a webszolgáltatás társított és engedélyezése és letiltása mintaadatok.

### <a name="monitoring-how-the-web-service-is-being-used"></a>A webszolgáltatás használatának figyelése
Kattintson a **IRÁNYÍTÓPULT** fülre.

Az irányítópulton megtekintheti a webszolgáltatás összesített használatát egy meghatározott időtartamra vonatkozóan. Kiválaszthatja a időszak időszak legördülő menüjében a használati diagramok jobb felső sarkában lévő megtekintéséhez. Az irányítópult a következő információkat tartalmazza:

* **Kérelmek keresztül idő** kérések száma lépés grafikonját jeleníti meg a kijelölt időszakra. Segíthet megállapítani, hogy a használati igényeiben jelentkező problémát.
* **Kérés-válasz kérelmek** jeleníti meg a szolgáltatás által fogadott keresztül, a kijelölt időszak és azok számát nem sikerült a kérés-válasz hívások száma.
* **Átlagos kérés-válasz számítási ideje** a fogadott kérések végrehajtásához szükséges idő átlagát jeleníti meg.
* **Kötegelt kérelmek** a szolgáltatás által fogadott keresztül, a kijelölt időszak és azok számát nem sikerült kötegelt kérelmek teljes számát jeleníti meg.
* **Átlagos késés feladat** a fogadott kérések végrehajtásához szükséges idő átlagát jeleníti meg.
* **Hibák** bekövetkezett hibák összesített számát jeleníti meg a webszolgáltatás hívásokat.
* **Szolgáltatási költségei** a számlázási terv a szolgáltatással kapcsolatos díjakat jeleníti meg.

### <a name="configuring-the-web-service"></a>A webszolgáltatás konfigurálása
Kattintson a **KONFIGURÁLÁSA** menüjét.

Frissítheti a következő tulajdonságokkal:

* **Leírás** lehetővé teszi, hogy adja meg a webszolgáltatás leírását. Leírás megadása kötelező.
* **Naplózás** lehetővé teszi a engedélyezheti vagy tilthatja le a hiba a végpont a bejelentkezés során. A naplózást további információkért lásd: Enable [Machine Learning webszolgáltatások naplózása](web-services-logging.md).
* **Engedélyezze a mintaadatok** lehetővé teszi, hogy adja meg a mintaadatokat a kérés-válasz szolgáltatás teszteléséhez használható. Ha a webszolgáltatás a Machine Learning Studióban létrehozott, a mintaadatok forrása az adatokat a használt modellje betanításához. Ha a szolgáltatás programozott módon létrehozott, az adatok a példaadatokat a JSON-csomag részeként megadott származik.


