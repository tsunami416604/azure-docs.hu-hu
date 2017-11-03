---
title: "A Machine Learning-munkaterület kezelése |} Microsoft Docs"
description: "Azure Machine Learning munkaterületekhez való hozzáférés kezelése és szolgáltatások telepítésére és kezelésére ML API webkiszolgáló"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: garye
ms.openlocfilehash: 2f90234bdd5c917a502d24cd16256bc11c7fbed0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Azure Machine Learning-munkaterület kezelése

> [!NOTE]
> A Machine Learning webszolgáltatások portálon webszolgáltatások kezeléséről további információért lásd: [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md).
> 
> 

Gépi tanulás munkaterületeket, vagy az Azure-portálon, vagy a klasszikus Azure-portálon kezelheti.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A munkaterület az Azure-portálon kezelheti:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) Azure-előfizetéshez rendszergazdai fiók használatával.
2. Az oldal tetején a keresési mezőbe, írja be a "számítógép tanulási munkaterületek", és válassza ki **Machine Learning munkaterületek**.
3. Kattintson a kezelni kívánt munkaterületre.

A szabványos erőforrás-kezelési információkat és a rendelkezésre álló lehetőségek mellett a következő műveletek végezhetők el:

- Nézet **tulajdonságok** – ezen a lapon a munkaterületet, és az erőforrás-információkat jeleníti meg, és módosíthatja a előfizetésbe és erőforráscsoportba csoport, a munkaterület kapcsolódnak.
- **Tárolási kulcsok újraszinkronizálásra** -a munkaterület fenntartja a tárfiók kulcsait. Ha a tárfiók kulcsait megváltozik, ezt követően kattinthat **kulcsok újraszinkronizálásra** a kulcsok szinkronizálása a munkaterületen.

A munkaterület társított webszolgáltatások kezeléséhez használja a Machine Learning webszolgáltatások portálon. Lásd: [kezelése az Azure Machine Learning webszolgáltatások portál használatával egy webszolgáltatás-bővítmény](manage-new-webservice.md) kapcsolatos részletes információkért.

> [!NOTE]
> Központi telepítése, illetve új webszolgáltatások kezelése az előfizetést, amelyhez a webszolgáltatások telepítése egy közreműködő vagy a rendszergazda szerepkört kell hozzárendelni. Ha meghívni egy másik felhasználót egy machine learning munkaterülettel, hozzá kell rendelnie azokat egy közreműködő vagy a rendszergazda szerepkört az előfizetés ahhoz, azok központilag telepíthetne vagy kezelhetne webszolgáltatások. 
> 
>A hozzáférési engedélyek beállítása további információkért lásd: [access-hozzárendelések megtekintése a felhasználók és csoportok az Azure portálon - nyilvános előzetes](../../active-directory/role-based-access-control-manage-assignments.md).

## <a name="use-the-azure-classic-portal"></a>Használja a klasszikus Azure portált

A klasszikus Azure portál használatával, a Machine Learning-munkaterületekkel képes kezelni:

* A munkaterület használatának figyelése
* Engedélyezi vagy megtagadja a hozzáférést a munkaterület konfigurálása
* A munkaterületen létrehozott webszolgáltatások kezelése
* A munkaterület törlése

Az irányítópult fület emellett az munkaterület használatának áttekintése és a munkaterület adatok gyors áttekintő.  

> [!TIP]
> Az Azure Machine Learning Studióban az a **WEBSZOLGÁLTATÁSOK** lapon adhat hozzá, frissítés, vagy egy Machine Learning webszolgáltatás-bővítmény törlése.
> 
> 

A munkaterület a klasszikus Azure portálon kezelheti:

1. Jelentkezzen be a [a klasszikus Azure portálon](https://manage.windowsazure.com/) használatával a Microsoft Azure fiók – az Azure-előfizetéssel társított fiókot használja.
2. A Microsoft Azure-szolgáltatások panelen kattintson a **MACHINE LEARNING**.
3. Kattintson a kezelni kívánt munkaterületre.

A munkaterület lap rendelkezik a három lappal:

* **IRÁNYÍTÓPULT** -lehetővé teszi a használati adatok megtekintése a munkaterület és információk
* **KONFIGURÁLÁS** -lehetővé teszi a munkaterülethez való hozzáférés kezelése
* **WEBSZOLGÁLTATÁSOK** -webszolgáltatások erről a munkaterületről közzétett kezelése

### <a name="to-monitor-how-the-workspace-is-being-used"></a>A munkaterület használatának figyelése
Kattintson a **IRÁNYÍTÓPULT** fülre.

Az irányítópultról összesített használatát a munkaterület tekintheti meg és munkaterület adatok gyors áttekintő beolvasása.

* A **számítási** diagram megjeleníti a számítási erőforrásokat, a munkaterület használja. Módosíthatja a nézetben megjelenítendő relatív vagy abszolút értékek, és módosíthatja az időtartam, a diagram.
* **Használati áttekintése** megjeleníti a munkaterületen használja az Azure storage.
* **Gyors áttekintő** munkaterület információkat és hasznos hivatkozásokat összegzését tartalmazza.

> [!NOTE]
> A **bejelentkezés az ML Studio** a hivatkozás megnyitja a Machine Learning Studio a Microsoft-Account jelenleg bejelentkezett. Jelentkezzen be a klasszikus Azure portálon munkaterület létrehozásához használt Microsoft Account automatikusan nincs engedélye a munkaterületet. Nyissa meg a munkaterületet, hogy be kell jelentkeznie Microsoft Account a munkaterület tulajdonosa van definiálva, vagy meghívót kapott a tulajdonos a munkaterület csatlakozni szeretne.
> 
> 

### <a name="to-grant-or-suspend-access-for-users"></a>Vagy felhasználók hozzáférésének felfüggesztése
Kattintson a **KONFIGURÁLÁSA** fülre.

A konfiguráció lapon a következőket teheti:

* A Machine Learning-munkaterület elérését felfüggesztése MEGTAGADÁS kattintva. Felhasználók nem fognak tudni megnyitni a munkaterületen a Machine Learning Studióban. Állítsa vissza a hozzáférést, kattintson az Engedélyezés gombra.

A Machine Learning Studio munkaterületének hozzáférő további fiókok kezeléséhez kattintson **bejelentkezés az ML Studio** a a **IRÁNYÍTÓPULT** lap (lásd a portáladatbázis előző megjegyzést vonatkozó **bejelentkezés az ML Studio**). Ekkor megnyílik a munkaterületen a Machine Learning Studióban. Itt kattintson a **beállítások** fülre, majd **felhasználók**. Kattinthat **több felhasználók MEGHÍVÁSA** felhasználók hozzáférésének a munkaterületet, vagy válasszon ki egy felhasználót, és kattintson a **eltávolítása**.

### <a name="to-manage-web-services-in-this-workspace"></a>A munkaterület webszolgáltatások kezelése
Kattintson a **WEBSZOLGÁLTATÁSOK** fülre.

Ez megjeleníti a munkaterületről közzétett webes szolgáltatások listáját.
Egy webszolgáltatás-bővítmény kezeléséhez kattintson a neve, nyissa meg a webszolgáltatás oldalát a listában.

Egy webszolgáltatás-bővítmény definiált egy vagy több végpontot is rendelkezhet.

* Az "Alapértelmezett" végpont mellett további végpontok adhat meg. A végpont hozzáadásához kattintson **végpontok kezelése** az irányítópult megnyitásához az Azure Machine Learning webszolgáltatások portál alján.
* Törli a végpont (a "Alapértelmezett" végpont nem törölhetők), jelölje be a jelölőnégyzetet a végpont sor elején, és kattintson a **törlése**. A végpont ezzel eltávolítja a webes szolgáltatás.
  
  > [!NOTE]
  > Ha egy alkalmazás a végpont törlésekor használ a webszolgáltatási végpontot, az alkalmazás egy hibaüzenetet fog kapni a következő alkalommal próbál meg elérni a szolgáltatást.
  > 
  > 

Kattintson egy webszolgáltatási végpontot a megnyitáshoz nevére. 

Az irányítópulton megtekintheti a webszolgáltatás összesített használatát egy meghatározott időtartamra vonatkozóan. Kiválaszthatja a időszak időszak legördülő menüjében a használati diagramok jobb felső sarkában lévő megtekintéséhez. Az irányítópult a következő információkat tartalmazza:

* **Kérelmek keresztül idő** kérések száma lépés grafikonját jeleníti meg a kijelölt időszakra. Segíthet megállapítani, hogy a használati igényeiben jelentkező problémát.
* **Kérés-válasz kérelmek** jeleníti meg a szolgáltatás által fogadott keresztül, a kijelölt időszak és azok számát nem sikerült a kérés-válasz hívások száma.
* **Átlagos kérés-válasz számítási ideje** a fogadott kérések végrehajtásához szükséges idő átlagát jeleníti meg.
* **Kötegelt kérelmek** a szolgáltatás által fogadott keresztül, a kijelölt időszak és azok számát nem sikerült kötegelt kérelmek teljes számát jeleníti meg.
* **Átlagos késés feladat** a fogadott kérések végrehajtásához szükséges idő átlagát jeleníti meg.
* **Hibák** bekövetkezett hibák összesített számát jeleníti meg a webszolgáltatás hívásokat.
* **Szolgáltatási költségei** a számlázási terv a szolgáltatással kapcsolatos díjakat jeleníti meg.

A konfigurálás lapról frissítheti a következő tulajdonságokkal:

* **Leírás** lehetővé teszi, hogy adja meg a webszolgáltatás leírását. Leírás megadása kötelező.
* **Naplózás** lehetővé teszi a engedélyezheti vagy tilthatja le a hiba a végpont a bejelentkezés során. A naplózást további információkért lásd: Enable [Machine Learning webszolgáltatások naplózása](web-services-logging.md).
* **Engedélyezze a mintaadatok** lehetővé teszi, hogy adja meg a mintaadatokat a kérés-válasz szolgáltatás teszteléséhez használható. Ha a webszolgáltatás a Machine Learning Studióban létrehozott, a mintaadatok forrása az adatokat a használt modellje betanításához. Ha a szolgáltatás programozott módon létrehozott, az adatok a példaadatokat a JSON-csomag részeként megadott származik.

