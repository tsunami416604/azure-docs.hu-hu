---
title: A DevOps kiadási folyamatának folyamatos figyelése az Azure Pipelines és az Azure Application Insights használatával | Microsoft dokumentumok
description: Utasításokat tartalmaz a folyamatos figyelés gyors beállításához az Application Insights segítségével
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: e565101218b975ef2bd29b8a32a4aa1bf4300b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655395"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Folyamatos figyelés hozzáadása a kiadási folyamathoz

Az Azure Pipelines integrálható az Azure Application Insights szolgáltatással, így a szoftverfejlesztési életciklus során lehetővé teszi a DevOps kiadási folyamat folyamatos figyelését. 

A folyamatos figyelés, kiadási folyamatok is bele figyelési adatokat Application Insights és más Azure-erőforrások. Amikor a kiadási folyamat észleli az Application Insights-riasztást, a folyamat leválaszthatja vagy visszaállíthatja a központi telepítést, amíg a riasztás meg nem oldódik. Ha az összes ellenőrzés átmegy, a központi telepítések automatikusan folytathatják a teszteléstől az éles környezetig, manuális beavatkozás nélkül. 

## <a name="configure-continuous-monitoring"></a>Folyamatos figyelés konfigurálása

1. Az [Azure DevOps](https://dev.azure.com)alkalmazásban válasszon ki egy szervezetet és egy projektet.
   
1. A projektlap bal oldali menüjében válassza a **Folyamatok felszabadítása** > **lehetőséget.** 
   
1. Az **Új** gomb melletti nyíllegördülőmenün válassza az **Új kiadási folyamat lehetőséget.** Vagy ha még nincs folyamat, válassza az **Új folyamat** lehetőséget a megjelenő lapon.
   
1. A **Sablon kiválasztása** ablaktáblában keresse meg és válassza az **Azure App Service központi telepítését folyamatos figyeléssel,** majd válassza az **Alkalmaz**lehetőséget. 

   ![Új Azure-folyamatok kiadási folyamat](media/continuous-monitoring/001.png)

1. Az **1.** **View stage tasks.**

   ![Fázisfeladatok megtekintése](media/continuous-monitoring/002.png)

1. Az **1.** 

    | Paraméter        | Érték |
   | ------------- |:-----|
   | **Fázis neve**      | Adjon meg egy művésznevet, vagy hagyja az **1.** |
   | **Azure-előfizetés** | Legördülő menüből válassza ki a használni kívánt csatolt Azure-előfizetést.|
   | **App type** (Alkalmazás típusa) | Legördülő menüből válassza ki az alkalmazás típusát. |
   | **App Szolgáltatás neve** | Adja meg az Azure App Service nevét. |
   | **Az Application Insights erőforráscsoport-neve**    | Legördülő menüt, és válassza ki a használni kívánt erőforráscsoportot. |
   | **Az Application Insights erőforrás neve** | Legördülő menüt, és válassza ki az Application Insights erőforrást a kiválasztott erőforráscsoporthoz.

1. A folyamat alapértelmezett riasztási szabály beállításait menteni, válassza a **Mentés** a jobb felső sarokban az Azure DevOps ablakban. Írjon be egy leíró megjegyzést, majd kattintson **az OK gombra.**

## <a name="modify-alert-rules"></a>Riasztási szabályok módosítása

Az **Azure App Service folyamatos figyelési sablonnal rendelkező központi telepítése** négy riasztási szabálysal rendelkezik: **Elérhetőség**, **Sikertelen kérések**, **Kiszolgálói válaszidő**és **Kiszolgálókivételek.** További szabályokat adhat hozzá, vagy módosíthatja a szabálybeállításokat a szolgáltatási szint igényeinek megfelelően. 

A riasztási szabályok beállításainak módosítása:

1. A kiadási folyamat lapjának bal oldali ablaktáblájában válassza az **Application Insights-riasztások konfigurálása**lehetőséget.

1. Az **Azure Monitor riasztások** ablaktáblán válassza ki a három pontot **...** a **riasztási szabályok**mellett.
   
1. A **Riasztási szabályok** párbeszédpanelen jelölje ki a riasztási szabály melletti legördülő szimbólumot, például **az Elérhetőség lehetőséget.** 
   
1. Módosítsa a **küszöbértéket** és más beállításokat, hogy megfeleljen az Ön igényeinek.
   
   ![Riasztás módosítása](media/continuous-monitoring/003.png)
   
1. Válassza **az OK**gombot, majd az Azure DevOps-ablakjobb felső részén válassza a **Mentés** gombot. Írjon be egy leíró megjegyzést, majd kattintson **az OK gombra.**

## <a name="add-deployment-conditions"></a>Telepítési feltételek hozzáadása

Amikor üzembe helyezési kapukat ad hozzá a kiadási folyamathoz, a beállított küszöbértékeket meghaladó riasztás megakadályozza a nem kívánt kiadás előléptetését. A riasztás feloldása után a központi telepítés automatikusan folytatódhat.

Telepítési kapuk hozzáadása:

1. A fő folyamatlap **Stages (Szakaszok)** szakaszában válassza ki az **Üzembe helyezés előtti feltételek vagy** az üzembe helyezés utáni **feltételek** szimbólumot, attól függően, hogy melyik szakasznak van szüksége folyamatos figyelési kapura.
   
   ![Üzembe helyezés előtti körülmények](media/continuous-monitoring/004.png)
   
1. A **Telepítés előtti feltételek** konfigurációs ablaktáblájában állítsa a **Gates** **-t Engedélyezve értékre.**
   
1. A **Központi telepítési kapuk**csoportban válassza a **Hozzáadás**lehetőséget.
   
1. Válassza **a Lekérdezés i. Figyelő riasztásait** a legördülő menüből. Ez a beállítás lehetővé teszi az Azure Monitor és az Application Insights-riasztások elérését.
   
   ![Azure Monitor-riasztások lekérdezése](media/continuous-monitoring/005.png)
   
1. A **Kiértékelés beállításai csoportban**adja meg a beállításokhoz kívánt értékeket, például **A kapuk újraértékelése** és **a kapuk időkimenő száma**közötti idő. 

## <a name="view-release-logs"></a>Kiadási naplók megtekintése

A kiadási naplókban láthatja a központi telepítési kapu viselkedését és egyéb kiadási lépéseket. A naplók megnyitása:

1. Válassza a folyamatlap bal oldali menüjében a **Felszabadulás lehetőséget.** 
   
1. Bármelyik kiadást kijelöli. 
   
1. A **Szakaszok csoportban**jelölje ki bármelyik szakaszt a kiadások összegzésének megtekintéséhez. 
   
1. A naplók megtekintéséhez jelölje be a **Naplók megtekintése** a kiadási összefoglalóban **jelölőnégyzetet,** jelölje ki a Sikeres vagy **sikertelen hivatkozást** bármely szakaszban, vagy mutasson bármely szakaszra, és válassza a **Naplók**lehetőséget. 
   
   ![Kiadási naplók megtekintése](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>További lépések

Az Azure-folyamatokról az [Azure Pipelines dokumentációjában](https://docs.microsoft.com/azure/devops/pipelines)olvashat bővebben.
