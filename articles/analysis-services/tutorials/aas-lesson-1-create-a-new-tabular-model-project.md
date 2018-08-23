---
title: 'Azure Analysis Services-oktatóanyag – 1. lecke: Új táblázatosmodell-projekt létrehozása | Microsoft Docs'
description: A lecke az új Azure Analysis Services-oktatóprojektek létrehozását ismerteti.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b9d3e9df62ffd31a6a01ea427fec8d5a3d168bfa
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42058411"
---
# <a name="create-a-tabular-model-project"></a>Táblázatosmodell-projekt létrehozása

Ebben a leckében használhatja a Visual Studio az Analysis Services-projektek vagy SQL Server Data Tools (SSDT) hozzon létre egy új táblázatosmodell-projektet az 1400-as kompatibilitási szinten. A projekt létrehozása után megkezdheti az adatok hozzáadását és a modell létrehozását. Ez a lecke röviden bemutatja a Visual Studio táblázatosmodell-létrehozási környezetét is.  
  
A lecke elvégzésének várható időtartama: **10 perc**.  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a témakör a táblázatos modellek létrehozását ismertető oktatóanyag első leckéje. A lecke elvégzéséhez néhány előfeltételnek teljesülnie kell. További információ: [Azure Analysis Services – Adventure Works-oktatóanyag](../tutorials/aas-adventure-works-tutorial.md).  
  
## <a name="create-a-new-tabular-model-project"></a>Új táblázatosmodell-projekt létrehozása  
  
#### <a name="to-create-a-new-tabular-model-project"></a>Új táblázatosmodell-projekt létrehozása  
  
1.  A Visual Studio **File** (File) menüjében kattintson a **New** (Új) > **Project** (Projekt) elemre.  
  
2.  Az **Új projekt** párbeszédpanelen bontsa ki a **Telepítve** > **Üzleti intelligencia** > **Analysis Services** elemet, majd kattintson az **Analysis Services rendszerbeli táblázatos projekt** elemre.  
  
3.  A **Név** mezőbe gépelje be az **AW internetes értékesítés** nevet, majd adja meg a projektfájlok helyét.  
  
    Alapértelmezés szerint a **Megoldás neve** megegyezik a projekt nevével, de megadhat más megoldásnevet is.  
  
4.  Kattintson az **OK** gombra.  
  
5.  A **Táblázatos modell tervezője** párbeszédpanelen válassza az **Integrált munkaterület** lehetőséget.  
  
    A modell létrehozása során a munkaterület egy táblázatos modelladatbázist futtat, amelynek a neve megegyezik a projektével. Az integrált munkaterület azt jelenti, hogy a Visual Studio egy beépített példányt használ, így nem kell telepítenie egy különálló Analysis Services-kiszolgálópéldányt csak a modell létrehozásához.
      
6.  A **Kompatibilitási szint** mezőben válassza az **SQL Server 2017 / Azure Analysis Services (1400)** lehetőséget.   
 
    ![aas-lesson1-tmd](../tutorials/media/aas-lesson1-tmd.png)
      
    Ha nem látja az SQL Server 2017 / Azure Analysis Services (1400) lehetőséget a Kompatibilitási szint listában, akkor nem az SSDT legújabb verzióját használja. A legújabb verzió beszerzése: [Az SQL Server Data Tools telepítése](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
      
  
## <a name="understanding-the-visual-studio-tabular-model-authoring-environment"></a>A Visual Studio táblázatosmodell-létrehozási környezetben ismertetése  
Az új táblázatosmodell-projekt létrehozása után szánjon néhány percet a Visual Studio táblázatosmodell-létrehozási környezetének felfedezésére.  
  
A létrehozott projekt megnyílik a Visual Studióban. A jobb oldalon, a **Táblázatosmodell-tallózóban** láthatja a modellben található objektumok fanézetét. Mivel még nem importált adatokat, a mappák üresek. A jobb gombbal egy objektummappára kattintva műveleteket hajthat végre, a menüsorhoz hasonló módon. Az oktatóanyag elvégzése során a Táblázatosmodell-tallózóval navigálhat a modellprojekt különböző objektumai között.

![aas-lesson1-tme](../tutorials/media/aas-lesson1-tme.png)

Kattintson a **Megoldáskezelő** fülre. Itt láthatja a **Model.bim** fájlt. Ha nem látható a tervezőablak a bal oldalon (egy üres ablak a Model.bim lappal), a **Megoldáskezelő** **AW Internetes értékesítés projekt** területén kattintson duplán a **Model.bim** fájlra. A Model.bim fájl tartalmazza a modellprojekt metaadatait. 

![aas-lesson1-se](../tutorials/media/aas-lesson1-se.png)
  
Kattintson a **Model.bim** fájlra. A **Tulajdonságok** ablakban megtekintheti a modell tulajdonságait. Ezek közül is a legfontosabb a **DirectQuery mód** tulajdonság. Ez a tulajdonság határozza meg, hogy a modell üzembe helyezése Memóriában tárolt (Ki) vagy DirectQuery módban (Be) történik. A jelen oktatóanyagban Memóriában tárolt módban fogja létrehozni és üzembe helyezni a modellt.

![aas-lesson1-properties](../tutorials/media/aas-lesson1-properties.png)
  
Modellprojekt létrehozásakor bizonyos modelltulajdonságok beállítása automatikusan megtörténik az **Eszközök** menü **Beállítások** párbeszédpaneljén megadható Adatmodellezési beállításoknak megfelelően. Az Adatok biztonsági mentése, a Munkaterület megőrzése és a Munkaterület-kiszolgáló tulajdonságok azt adják meg, milyen módon és hol történjen a munkaterület-adatbázis (a modell-létrehozási adatbázis) biztonsági mentése, memóriában való megőrzése és felépítése. Szükség esetén később módosíthatja ezeket a beállításokat, de egyelőre hagyja őket változatlanul.  

A **Megoldáskezelő** területén kattintson a jobb gombbal az **AW internetes értékesítés** projektre, majd kattintson a **Tulajdonságok** elemre. Megnyílik az **AW internetes értékesítés tulajdonságlapjai** párbeszédpanel. Néhány tulajdonságot ezek közül a modell üzembe helyezése során adhat meg.  
  
Analysis Services-projektek vagy az SSDT telepítésekor a Visual Studio-környezet több új menüelemmel bővült. Kattintson a **Modell** menüre. Itt importálhat adatokat, frissítheti a munkaterület adatait, az Excelben navigálhat a modellben, létrehozhat perspektívákat és szerepköröket, kiválaszthatja a modellnézetet, valamint számítási beállításokat adhat meg. Kattintson a **Tábla** menüre. Itt kapcsolatokat hozhat létre és kezelhet, megadhatja a dátumtáblázat beállításait, partíciókat hozhat létre és szerkesztheti a tábla beállításait. Ha az **Oszlop** menüre kattint, hozzáadhat és törölhet oszlopokat a táblából, rögzítheti az oszlopokat, és megadhatja a rendezési sorrendet. A Visual Studio is további gombokat ad hozzá a menüsorhoz. A leghasznosabb ezek közül az AutoSzum funkció, amellyel standard összesítésmérték hozható létre egy kijelölt oszlophoz. Az eszköztár többi gombja gyors hozzáférést biztosít a gyakran használt funkciókhoz és parancsokhoz.  
  
Fedezze fel a táblázatos modellek létrehozásával kapcsolatos különféle funkciók párbeszédpaneljeit és helyét. Habár néhány elem egyelőre nem aktív, jó képet kaphat a táblázatosmodell-létrehozási környezetről.  
  

## <a name="whats-next"></a>A következő lépések
[2. lecke: Az adatok beszerzése](../tutorials/aas-lesson-2-get-data.md).

  
  
  
