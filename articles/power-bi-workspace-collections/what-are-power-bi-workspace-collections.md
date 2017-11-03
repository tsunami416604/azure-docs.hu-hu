---
title: "Mik azok a Power BI munkaterület gyűjtemények?"
description: "A Power BI Embedded lehetővé teszi vagy való integrálására Power BI-jelentések a webes alkalmazásokat, nem kell egyéni megoldások."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 7df172895bb926f1715370b941964e2c29ab393d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-power-bi-workspace-collections"></a>Mik azok a Power BI munkaterület gyűjtemények?

A **Power BI munkaterület gyűjtemények**, akkor integrálható a Power BI-jelentéseket jobbra a webhelyen vagy az alkalmazásokat.

![Alkalmazásdiagram](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

A Power BI munkaterület gyűjtemények egy **Azure szolgáltatás** , amely lehetővé teszi, hogy ISV-k és az alkalmazás fejlesztők felület Power BI adatok élmények az alkalmazásokban. Fejlesztőként alkalmazások létrehozott, és ezeket az alkalmazásokat a saját felhasználók és a szolgáltatások meghatározott készletét rendelkezik. Ezek az alkalmazások is fordulhat elő a rendelkezik néhány beépített elemek, például diagramokat és jelentéseket, hogy most már a Microsoft Power BI munkaterület gyűjtemények alapján kell kapcsolni. A Power BI-fiókkal, az alkalmazás használatához nincs szükség. Továbbra is jelentkezzen be a korábbiakhoz hasonlóan, az alkalmazás és a programtelepítés megtekintésének és módosításának a Power BI reporting élmény anélkül, hogy további licencelést.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Microsoft Power BI munkaterület gyűjtemények a licencelés

Az a **Microsoft Power BI munkaterület gyűjtemények** használati modell licencelési a Power bi-ban érték a felhasználó felelőssége.  Ehelyett **munkamenetek** , amely nem használ-e a látványelemek az alkalmazás fejlesztője által beszerzett, és az előfizetéséhez ezeket az erőforrásokat birtokló van szó. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Microsoft Power BI munkaterület gyűjtemények fogalmi modellt

![A munkaterület gyűjtemények alkalmazási folyamatot](media/what-are-power-bi-workspace-collections/model.png)

Mint minden más szolgáltatáshoz, az Azure-ban, a Power BI munkaterület gyűjtemények erőforrások törlődnek, az a [Azure Resource Manager API-k](https://msdn.microsoft.com/library/mt712306.aspx). Ebben az esetben az erőforrást, amely kiépítése van egy **Power BI-Munkaterületcsoport**.

## <a name="workspace-collection"></a>Munkaterület-csoport

A **munkaterület-csoportok** a legfelső szintű Azure tárolók erőforrásokat tartalmazó, 0 vagy több **munkaterületek**.  A **munkaterület** **gyűjtemény** rendelkezik az összes Azure standard tulajdonságait, valamint a következőket:

* **Hívóbetűk** – a Power BI API-k (ezt egy későbbi szakasz ismerteti) biztonságosan meghívásakor használt kulcsokat.
* **Felhasználók** – kezelheti a Power BI-Munkaterületcsoport az Azure portálon keresztül vagy Azure Resource Manager API-t rendszergazdai jogosultsággal rendelkező felhasználók Azure Active Directory (AAD).
* **Régió** – létesítési részeként egy **munkaterület-csoportok**, egy a régiót építhető ki. További információkért lásd: [Azure-régiókat](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Munkaterület

A **munkaterület** egy olyan tároló, a Power BI tartalom, ilyenek például adatkészleteket és jelentéseket. A **munkaterület** üres létrehozásakor. Meg fogja írni a tartalom a Power BI Desktop segítségével, és fogja programozott módon telepítheti a pbix-fájlt a munkaterület használatával történő a [Power BI importálási API](https://msdn.microsoft.com/library/mt711504.aspx). A dataset programozott módon is létrehozhat és majd hozza létre a jelentéseket a Power BI Desktop használata helyett az alkalmazáson belül.

## <a name="using-workspace-collections-and-workspaces"></a>Munkaterület gyűjtemények és munkaterületek

**Munkaterület gyűjtemények** és **munkaterületek** olyan tárolók, amely használja, és bármelyik legjobb módja megfelel az Ön által létrehozott alkalmazás tervét rendezve tartalom. Számos különböző módja, hogy sikerült-e elrendezése a tartalmakra lesz. Dönthet egy munkaterület belül összes tartalmat helyezze, majd később alkalmazási jogkivonatok további megkönnyíti a tartalom az ügyfelek között. Dönthet úgy is, külön munkaterületek helyezése összes ügyfeleinek, hogy néhány elkülönítése. Vagy régiónként, nem pedig vevő felhasználók rendszerezéséhez dönthet. Ez a rugalmas kialakítás lehetővé teszi annak meghatározását a legjobb módon nem rendszerezhetik a tartalmat.

## <a name="cached-datasets"></a>Gyorsítótárazott adatkészletek

Gyorsítótárazott adatkészlet is használható.  Azonban nem lehet frissíteni a gyorsítótárazott adatokat, ha rendelkezik lett betöltve a **Microsoft Power BI munkaterület gyűjtemények**. A gyorsítótárazott dataset azt jelenti, hogy importálta az adatokat a Power BI Desktop DirectQuery használata helyett.

## <a name="authentication-and-authorization-with-app-tokens"></a>Hitelesítési és engedélyezési az alkalmazási jogkivonatok

**Microsoft Power BI munkaterület gyűjtemények** megfelel a az alkalmazás a szükséges felhasználói hitelesítési és engedélyezési végrehajtásához. Nincs olyan explicit követelmény, hogy a végfelhasználók számára legyen-e a felhasználók az Azure Active Directory (Azure AD).  Ehelyett az alkalmazás fejezi ki, hogy **Microsoft Power BI munkaterület gyűjtemények** engedélyezés a Power BI-jelentés megjelenítéséhez **alkalmazás a hitelesítési tokenek (alkalmazási jogkivonatok)**.  Ezek **alkalmazási jogkivonatok** jönnek létre, amikor az alkalmazás szeretne jelentést készít igény szerint.

![Alkalmazás token használati diagramja](media/what-are-power-bi-workspace-collections/app-tokens.png)

**Alkalmazás a hitelesítési tokenek (alkalmazási jogkivonatok)** hitelesítése használt **Microsoft Power BI munkaterület gyűjtemények**.  Három típusú **alkalmazási jogkivonatok**:

1. Jogkivonatok - felhasznált létesítésekor egy új létesítési **munkaterület** be egy **munkaterület-csoport**
2. Fejlesztői jogkivonatokat - használni, amikor közvetlenül a **Power BI REST API-k**
3. Jogkivonatok -, amikor a beágyazott iframe a jelentés megjelenítéséhez használt beágyazás

Ezeket a jogkivonatokat használják a különböző fázisait ügyfélkapcsolati **Microsoft Power BI munkaterület gyűjtemények**.  A jogkivonatok tervezték, hogy delegálhatóak engedélyek az alkalmazásból a Power bi-bA. További információkért lásd: [App Token Flow](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Hozzon létre vagy szerkeszt jelentéseket az alkalmazáson belül

Most szerkesztheti a meglévő jelentéseket, vagy hozzon létre új jelentéseket közvetlenül az alkalmazás a Power BI Desktop használata nélkül. Ehhez szükséges, hogy létezik-e a DataSet adatkészlet belül a munkaterületen.

## <a name="see-also"></a>Lásd még:

[Microsoft Power BI munkaterület gyűjtemények gyakori helyzetek](scenarios.md)  
[Ismerkedés a Microsoft Power BI munkaterület gyűjtemények](get-started.md)  
[Bevezetés a minta használatába](get-started-sample.md)  
[Jelentés beágyazása](embed-report.md)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[A csharp nyelvű Power bi Git-tárház](https://github.com/Microsoft/PowerBI-CSharp)  
[Power bi-csomópont Git-tárház](https://github.com/Microsoft/PowerBI-Node)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)
