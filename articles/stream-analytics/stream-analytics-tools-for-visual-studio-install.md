---
title: Állítsa be az Azure Stream Analytics tools for Visual Studio
description: Ez a cikk ismerteti a telepítési követelményeknek, és hogyan állíthatja be az Azure Stream Analytics tools for Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: e87fc5b91e9e1d5f4f8449e84b17bcdab9c0b6b2
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39713594"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Az Azure Stream Analytics tools for Visual Studio telepítése
Az Azure Stream Analytics tools támogatja a Visual Studio 2017, 2015 vagy 2013. Ez a cikk ismerteti, hogyan kell telepíteni, és távolítsa el az eszközöket.

Az eszközök használatával további információkért lásd: [Stream Analytics tools for Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Telepítés
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Töltse le [(15.3-as vagy újabb) a Visual Studio 2017](https://www.visualstudio.com/). Az Enterprise (Ultimate/Premium), Professional és Community kiadások mind támogatottak. Az Express kiadás nem támogatott. 
* Stream Analytics tools részét képezik a **Azure-fejlesztési** és **adattárolási és feldolgozási** számítási feladatokat a Visual Studio 2017-ben. A Visual Studio telepítésének részeként engedélyezze a két számítási feladat egyikét.

Engedélyezze a **adattárolási és feldolgozási** számítási feladat látható módon:

![Adatok tárolása és -feldolgozási munkaterhelés van kiválasztva](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Engedélyezze a **Azure-fejlesztési** számítási feladat látható módon:

![Azure development workload munkafolyamatának van kiválasztva](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Telepítse a Visual Studio 2015 vagy Visual Studio 2013 Update 4. Az Enterprise (Ultimate/Premium), Professional és Community kiadások mind támogatottak. Az Express kiadás nem támogatott. 
* Telepítse a Microsoft Azure SDK for .NET 2.7.1-es vagy újabb használatával a [webplatform-telepítő](http://www.microsoft.com/web/downloads/platform.aspx).
* Telepítés [Azure Stream Analytics tools for Visual Studio](http://aka.ms/asatoolsvs).

## <a name="update"></a>Frissítés

### <a name="visual-studio-2017"></a>Visual Studio 2017
Az új verzió emlékeztető megjelenik-e a Visual Studio értesítést. 

### <a name="visual-studio-2013-and-visual-studio-2015"></a>A Visual Studio 2013 és a Visual Studio 2015-ben
A telepített Stream Analytics tools for Visual Studio automatikusan keressen új verzióit. Kövesse az utasításokat a legújabb verzió telepítéséhez a felugró ablakban. 


## <a name="uninstall"></a>Eltávolítás

### <a name="visual-studio-2017"></a>Visual Studio 2017
Kattintson duplán a Visual Studióban, és válassza ki **módosítás**. Törölje a **az Azure Data Lake és Stream Analytics Tools** jelölőnégyzetet, vagy a **adattárolási és feldolgozási** számítási feladat vagy a **Azure-fejlesztési** számítási feladatok.

### <a name="visual-studio-2013-and-visual-studio-2015"></a>A Visual Studio 2013 és a Visual Studio 2015-ben
Nyissa meg a Vezérlőpultot, és távolítsa el **a Microsoft Azure Data Lake and Stream Analytics tools for Visual Studio**.





