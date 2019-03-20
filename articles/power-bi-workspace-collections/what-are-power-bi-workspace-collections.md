---
title: Mik azok a Power BI munkaterületi gyűjtemények?
description: Power BI Embedded lehetővé teszi a Power BI-jelentések integrálása a webes vagy mobilalkalmazásaiba, így nem kell egyéni megoldásokat hozhat létre.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 6650eed6bf2559e9fb76b573189179a7f5df81ca
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896704"
---
# <a name="what-are-power-bi-workspace-collections"></a>Mik azok a Power BI munkaterületi gyűjtemények?

A **Power BI-Munkaterületcsoportok**, integrálható a Power BI-jelentéseket közvetlenül a webes vagy mobilalkalmazásaiba.

![Alkalmazásdiagram](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

A Power BI-Munkaterületcsoportok vannak egy **Azure-szolgáltatás** , amely lehetővé teszi a független szoftverszállítók és fejlesztők a Power BI alkalmazásokba surface adatkezelési élményt. A fejlesztők létrehozott alkalmazásokat, és ezeket az alkalmazásokat a saját felhasználók és szolgáltatások külön készlete rendelkezik. Ezeknek az alkalmazásoknak akkor is szeretné, hogy bizonyos beépített elemek, például diagramokat és jelentéseket, most is működteti a Microsoft Power BI munkaterületi gyűjtemények. Nem kell az alkalmazás használatához a Power BI-fiókra. Jelentkezzen be az alkalmazás, csakúgy, mint korábban, és a programtelepítés megtekintésének és módosításának a Power BI jelentéskészítési funkciók anélkül, hogy bármilyen további licencelési folytathatja.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>A Microsoft Power BI-Munkaterületcsoportok licencelése

Az a **a Microsoft Power BI-Munkaterületcsoportok** használati modell licencelés, a Power BI jelenleg nem a felhasználó felelőssége.  Ehelyett **munkamenetek** szerzi be a fejlesztő az alkalmazás, amely a vizualizációkat, és az előfizetés, amely ezeket az erőforrásokat tulajdonosa terhelik. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>A Microsoft Power BI-Munkaterületcsoportok fogalmi modellhez

![Alkalmazás-munkaterület-csoportok folyamat](media/what-are-power-bi-workspace-collections/model.png)

Mint bármely más szolgáltatást az Azure-ban, a Power BI munkaterületi gyűjtemények erőforrások használatával hozhatók létre a [Azure Resource Manager API-k](https://msdn.microsoft.com/library/mt712306.aspx). Ebben az esetben az erőforrás üzembe van egy **Power BI-Munkaterületcsoport**.

## <a name="workspace-collection"></a>Munkaterület-csoport

A **munkaterület-csoport** a legfelső szintű Azure tároló-erőforrások, amely tartalmaz 0 vagy több **munkaterületek**.  A **munkaterület** **gyűjtemény** összes, a standard szintű Azure-tulajdonság, valamint a következő:

* **Hozzáférési kulcsok** – biztonságos hívása a Power BI API-k (egy későbbi szakaszban leírtak szerint) során használt kulcsokat.
* **Felhasználók** – a Power BI-munkaterület-csoport az Azure Portalon keresztül vagy az Azure Resource Manager API-val kezelheti a rendszergazdai jogokkal rendelkező felhasználók Azure Active Directory (AAD).
* **Régió** – üzembe helyezésének részeként egy **munkaterület-csoport**, kiválaszthatja a kiépítendő terület. További információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Munkaterület

A **munkaterület** egy olyan tároló, a Power BI tartalom, ilyenek például adatkészleteket és jelentéseket. A **munkaterület** üres első létrehozásakor. Tartalmak a Power BI Desktop használatával fogjuk hoz létre, és be a munkaterület használatával programozott módon fogja telepíteni a pbix-fájlt a [Power BI importálási API](https://msdn.microsoft.com/library/mt711504.aspx). Szoftveresen is létrehozhat az adatkészlet és majd hozza létre a jelentéseket a Power BI Desktop használata helyett az alkalmazáson belül.

## <a name="using-workspace-collections-and-workspaces"></a>Munkaterület-csoportok és a-munkaterületek használatával

**Munkaterület-csoportok** és **munkaterületek** használt és vannak rendezve, bármelyik legjobb módja megfelel a kialakítás hoz létre az alkalmazás tartalmának tárolói. Számos különböző módon, hogy a bennük található tartalom sikerült rendezze el lesz. Dönthet úgy összes tartalmat helyezze belül egy munkaterületet, majd később alkalmazási jogkivonatok többek között az ügyfelek a tartalom vertikálisan tovább particionálhatja. Dönthet úgy, hogy néhány elkülönülését Helyezzen minden, az ügyfelek külön munkaterületre is. Vagy dönthet úgy rendezheti a felhasználókat, régiónként, nem pedig ügyfél által. Ez a rugalmas kialakítás lehetővé teszi, hogy kiválaszthatja a legjobb módszer rendezze a tartalmat.

## <a name="cached-datasets"></a>Gyorsítótárazott adatkészletek

Gyorsítótárazott adatkészletek is használható.  Azonban nem lehet frissíteni a gyorsítótárazott adatok, miután betöltötte az **a Microsoft Power BI-Munkaterületcsoportok**. Gyorsítótárazott adatkészlet azt jelenti, hogy importálta az adatokat a Power BI Desktop DirectQuery használata helyett.

## <a name="authentication-and-authorization-with-app-tokens"></a>Hitelesítés és engedélyezés az alkalmazás-jogkivonatokról

**A Microsoft Power BI-Munkaterületcsoportok** megfelel az alkalmazásnak, hogy az összes szükséges felhasználói hitelesítés és engedélyezés a. Esetében nem explicit követelmény, hogy a végfelhasználók számára legyen-e az ügyfelek az Azure Active Directory (Azure AD).  Ehelyett az alkalmazás fejezi ki, hogy **a Microsoft Power BI-Munkaterületcsoportok** megjelenítése a Power BI-jelentések használatával engedélyezési **alkalmazás hitelesítési jogkivonatok (alkalmazás-Jogkivonatokról)**.  Ezek **alkalmazási jogkivonatok** jönnek létre, akkor az alkalmazás szeretne megjeleníteni a jelentést.

![Alkalmazás tokenek használatát a diagram](media/what-are-power-bi-workspace-collections/app-tokens.png)

**Alkalmazás hitelesítési jogkivonatok (alkalmazás-Jogkivonatokról)** elleni hitelesítéséhez **a Microsoft Power BI-Munkaterületcsoportok**.  Három típusú **alkalmazási jogkivonatok**:

1. Jogkivonatok – egy új üzembe helyezésekor használt kiépítési **munkaterület** be egy **munkaterület-csoport**
2. Fejlesztési jogkivonatok - hívások közvetlenül az használniuk, amikor a **Power BI REST API-k**
3. Beágyazási tokenek - használja, amikor a beágyazott IFRAME eleme a jelentés megjelenítése

Ezek a jogkivonatok használhatók a különböző fázisait az interakciók **a Microsoft Power BI-Munkaterületcsoportok**.  A jogkivonatok úgy tervezték, hogy delegálhatóak engedélyek az alkalmazásból a Power bi-bA. További információkért lásd: [alkalmazás Token Flow](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Hozzon létre vagy szerkeszt jelentéseket az alkalmazáson belül

Most már szerkesztheti a meglévő jelentéseket, vagy hozzon létre új jelentéseket az alkalmazás közvetlenül a Power BI Desktop használata nélkül. Ehhez az szükséges, hogy létezik-e egy adatkészlet belül a munkaterülethez.

## <a name="see-also"></a>Lásd még

[A Microsoft Power BI-Munkaterületcsoportok gyakori helyzetek](scenarios.md)  
[Ismerkedés a Microsoft Power BI munkaterületi gyűjteményekkel](get-started.md)  
[Bevezetés a minta használatába](get-started-sample.md)  
[Jelentés beágyazása](embed-report.md)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git Repo](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git Repo](https://github.com/Microsoft/PowerBI-Node)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](https://community.powerbi.com/)
