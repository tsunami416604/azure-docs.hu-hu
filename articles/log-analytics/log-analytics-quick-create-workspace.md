---
title: "Az Azure Log Analytics-munkaterület létrehozása |} Microsoft Docs"
description: "Útmutató felügyeleti megoldások és az adatgyűjtésről a felhőalapú és helyszíni környezetben való engedélyezéséhez a Naplóelemzési munkaterület létrehozása."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: magoedte
ms.openlocfilehash: 8259a97d28effa7bfa9cfb9d7cd9cd2a14c9d906
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>A Naplóelemzési munkaterület létrehozása az Azure-portálon
Az Azure-portálon állíthatja be a Naplóelemzési munkaterület, amely olyan saját adattárház, az adatforrások és a megoldások egyedi Naplóelemzési környezet.  Ebben a cikkben ismertetett lépések szükségesek, ha azt tervezi, a következő forrásokból származó adatok gyűjtése a:

* Az előfizetés az Azure-erőforrások
* A helyszíni System Center Operations Manager által felügyelt számítógépek
* A System Center Configuration Manager eszközgyűjtemények 
* Diagnosztikai vagy naplóadatok az Azure Storage-ból

Egyéb források, például az Azure virtuális gépek és a Windows vagy Linux rendszerű számítógépek a környezetben az alábbi témakörökben található:

*  [Azure virtuális gépek kapcsolatos adatok gyűjtése](log-analytics-quick-collect-azurevm.md) 
*  [Linux rendszerű számítógépekkel kapcsolatos adatok gyűjtése](log-analytics-quick-collect-linux-computer.md)
*  [Windows-számítógépekkel kapcsolatos adatok gyűjtése](log-analytics-quick-collect-windows-computer.md)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure-portal"></a>Jelentkezzen be Azure-portálon
Jelentkezzen be az Azure portálon, a [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Munkaterületek létrehozása
1. Az Azure portálon kattintson **további szolgáltatások** bal alsó sarokban található. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **Analytics jelentkezzen**.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Kattintson a **létrehozása**, majd válassza ki a következő elemeket kell:

  * Adjon meg egy nevet az új **OMS-munkaterület**, például a *DefaultLAWorkspace*. 
  * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
  * A **erőforráscsoport**, meglévő erőforrás használandó kiválasztott csoport már telepítő, vagy hozzon létre egy újat.  
  * Válasszon ki egy használható **hely**.  További információkért lásd: amely [Naplóelemzési is elérhető régiók](https://azure.microsoft.com/regions/services/).
  * Három különböző választhat **tarifacsomagok** Naplóelemzési, de a gyors üzembe helyezés, válassza ki kívánja a **szabad** réteg.  Az adott szintek kapcsolatos további információkért lásd: [napló Analytics díjszabás](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Miután megadta a szükséges adatokat a a **OMS-munkaterület** ablaktáblán kattintson a **OK**.  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 

## <a name="next-steps"></a>Következő lépések
Most, hogy a munkaterület érhető el, konfigurálja a figyelési telemetriai adatok gyűjteménye, napló indítunk el az adatok elemzéséhez, és adja hozzá a további adatok és elemzési insights-kezelési megoldás. 

* Adatok gyűjtése az Azure Diagnostics vagy az Azure storage Azure-erőforrások engedélyezéséről [gyűjtése Azure szolgáltatás naplók és a metrikák Naplóelemzési használható](log-analytics-azure-storage.md).  
* [Vegye fel a System Center Operations Manager adatforrásként](log-analytics-om-agents.md) adatokat gyűjteni az Operations Manager felügyeleti csoportjának ügynök és a Naplóelemzési munkaterület tárház tárolja. 
* Csatlakozás [Configuration Manager](log-analytics-sccm.md) számítógépek, amelyek tagjai a hierarchiában lévő gyűjtemények importálása.  
* Tekintse át a [megoldások](/log-analytics-add-solutions.md) érhető el, és hogyan lehet hozzáadni vagy eltávolítani egy megoldást a munkaterületről.

