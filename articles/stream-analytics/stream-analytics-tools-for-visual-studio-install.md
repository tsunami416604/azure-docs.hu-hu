---
title: A Visual Studio Azure Stream Analytics eszközök telepítője
description: Ez a cikk ismerteti a telepítési követelmények és az Azure Stream Analytics-eszközök Visual Studio beállításának módjáról.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/19/2017
ms.openlocfilehash: 511658fc0e2b480987455007dac5f55cd7850feb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Telepítheti az Azure Stream Analytics tools for Visual Studio
Azure Stream Analytics-eszközök mostantól támogatja a Visual Studio 2017, 2015 és 2013. Ez a dokumentum ismerteti, hogyan kell telepíteni, és távolítsa el az eszközök.

Az eszközök további információkért lásd: [Stream Analytics tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Telepítés
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Töltse le [(15.3 vagy újabb) a Visual Studio 2017](https://www.visualstudio.com/). Enterprise (Ultimate/prémium), Professional és Community Edition kiadásai támogatottak. Express kiadás nem támogatott. 
* Stream Analytics eszközök részét képezik a **Azure fejlesztési** és **adattárolás és feldolgozási** a Visual Studio 2017 munkaterhelések. A Visual Studio telepítésének részeként engedélyezze a két számítási feladat egyikét.

Engedélyezze a **adattárolás és feldolgozási** munkaterhelés látható módon:

![Adatok tárolási és feldolgozási terhelés](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

Engedélyezze a **Azure fejlesztési** munkaterhelés látható módon:

![Azure fejlesztési munkaterhelés](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Telepítse a Visual Studio 2015-öt vagy a Visual Studio 2013 Update 4. Enterprise (Ultimate/prémium), Professional és Community Edition kiadásai támogatottak. Express kiadás nem támogatott. 
* Telepítse a Microsoft Azure SDK for .NET 2.7.1-es verzió vagy újabb használatával a [webplatform-telepítő](http://www.microsoft.com/web/downloads/platform.aspx).
* Telepítés [Azure Stream Analytics tools for Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Frissítés

### <a name="visual-studio-2017"></a>Visual Studio 2017
Az új verzió emlékeztető megjelennek a Visual Studio értesítést. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
A telepített Stream Analytics tools for Visual Studio automatikusan új verziókkal kereséséhez. Kövesse a megjelenő ablakban található útmutatást követve telepítse a legújabb verziót. 


## <a name="uninstall"></a>Eltávolítás

### <a name="visual-studio-2017"></a>Visual Studio 2017
Kattintson duplán a Visual Studio telepítő, és válassza ki **módosítás**. Törölje a jelet a **Azure Data Lake és a Stream Analytics eszközök** vagy jelölőnégyzetet a **adattárolás és feldolgozási** munkaterhelés vagy az **Azure fejlesztési** munkaterhelés.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Nyissa meg a Vezérlőpultot, és távolítsa el **Microsoft Azure Data Lake és a Stream Analytics a Visual Studio eszközök**.





