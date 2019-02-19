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
ms.openlocfilehash: 673f4935dce28b30c10e6abf4c7d22e00c1dd73a
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343910"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Az Azure Stream Analytics tools for Visual Studio telepítése
Az Azure Stream Analytics-eszközök a Visual Studio 2017-et, 2015-öt és 2013-at támogatják. Ez a cikk ismerteti, hogyan kell telepíteni, és távolítsa el az eszközöket.

Az eszközök használatával további információkért lásd: [Stream Analytics tools for Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Telepítés
### <a name="recommended-visual-studio-2019-and-2017"></a>Ajánlott: Visual Studio 2019 and 2017
* Töltse le [Visual Studio 2019 (előzetes verzió 2 vagy újabb) és a Visual Studio 2017 (15.3-as vagy újabb)](https://www.visualstudio.com/). Az Enterprise (Ultimate/Premium), Professional és Community kiadások mind támogatottak. Az Express kiadás nem támogatott. A Visual Studio 2017 Mac gépeken nem támogatott. 
* Stream Analytics tools részét képezik a **Azure-fejlesztési** és **adattárolási és feldolgozási** számítási feladatokat a Visual Studio 2017-ben. A Visual Studio telepítésének részeként engedélyezze a két számítási feladat egyikét.

Engedélyezze a **adattárolási és feldolgozási** számítási feladat látható módon:

![Adatok tárolása és -feldolgozási munkaterhelés van kiválasztva](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Engedélyezze a **Azure-fejlesztési** számítási feladat látható módon:

![Azure development workload munkafolyamatának van kiválasztva](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)

* Az Eszközök menüben válassza a **bővítmények és frissítések**. A telepített bővítmények, majd kattintson a Keresés az Azure Data Lake és Stream Analytics tools **frissítés** a legújabb bővítmény telepítéséhez. 

![A Visual Studio bővítmények és frissítések](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-extensions-updates.png)

### <a name="visual-studio-2015-2013"></a>Visual Studio 2015, 2013
* Telepítse a Visual Studio 2015 vagy Visual Studio 2013 Update 4. Az Enterprise (Ultimate/Premium), Professional és Community kiadások mind támogatottak. Az Express kiadás nem támogatott. 
* Telepítse a Microsoft Azure SDK for .NET 2.7.1-es vagy újabb használatával a [webplatform-telepítő](https://www.microsoft.com/web/downloads/platform.aspx).
* Telepítés [Azure Stream Analytics tools for Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Frissítés

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 and 2017
Az új verzió emlékeztető megjelenik-e a Visual Studio értesítést.

![A Visual Studio új verzió emlékeztető](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-new-version-reminder-vs-tools.png)

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 and 2013
A telepített Stream Analytics tools for Visual Studio automatikusan keressen új verzióit. Kövesse az utasításokat a legújabb verzió telepítéséhez a felugró ablakban. 


## <a name="uninstall"></a>Eltávolítás

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 and 2017
Kattintson duplán a Visual Studióban, és válassza ki **módosítás**. Törölje a **az Azure Data Lake és Stream Analytics Tools** jelölőnégyzetet, vagy a **adattárolási és feldolgozási** számítási feladat vagy a **Azure-fejlesztési** számítási feladatok.

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 and 2013
Nyissa meg a Vezérlőpultot, és távolítsa el **a Microsoft Azure Data Lake and Stream Analytics tools for Visual Studio**.





