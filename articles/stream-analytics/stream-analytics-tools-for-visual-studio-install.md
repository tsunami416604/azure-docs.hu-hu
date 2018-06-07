---
title: Visual Studio Azure Stream Analytics eszközök beállítása
description: Ez a cikk ismerteti a telepítési követelmények és az Azure Stream Analytics-eszközök Visual Studio beállításának módjáról.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 94ed603990859d12f709e4a6121e3736221cf10a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651178"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Telepítheti az Azure Stream Analytics tools for Visual Studio
Azure Stream Analytics-eszközök Visual Studio 2017, 2015 és 2013 támogatja. A cikkből megtudhatja, hogyan kell telepíteni, és távolítsa el az eszközöket.

Az eszközök további információkért lásd: [Stream Analytics tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Telepítés
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Töltse le [(15.3 vagy újabb) a Visual Studio 2017](https://www.visualstudio.com/). Enterprise (Ultimate/prémium), Professional és Community Edition kiadásai támogatottak. Express kiadás nem támogatott. 
* Stream Analytics eszközök részét képezik a **Azure fejlesztési** és **adattárolás és feldolgozási** a Visual Studio 2017 munkaterhelések. A Visual Studio telepítésének részeként engedélyezze a két számítási feladat egyikét.

Engedélyezze a **adattárolás és feldolgozási** munkaterhelés látható módon:

![Adatok tárolási és feldolgozási terhelés van kiválasztva.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Engedélyezze a **Azure fejlesztési** munkaterhelés látható módon:

![Azure fejlesztési munkaterhelés van kiválasztva.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Telepítse a Visual Studio 2015-öt vagy a Visual Studio 2013 Update 4. Enterprise (Ultimate/prémium), Professional és Community Edition kiadásai támogatottak. Express kiadás nem támogatott. 
* Telepítse a Microsoft Azure SDK for .NET 2.7.1-es verzió vagy újabb használatával a [webplatform-telepítő](http://www.microsoft.com/web/downloads/platform.aspx).
* Telepítés [Azure Stream Analytics tools for Visual Studio](http://aka.ms/asatoolsvs).

## <a name="update"></a>Frissítés

### <a name="visual-studio-2017"></a>Visual Studio 2017
Az új verzió emlékeztető megjelennek a Visual Studio értesítést. 

### <a name="visual-studio-2013-and-visual-studio-2015"></a>A Visual Studio 2013 és a Visual Studio 2015-höz
A telepített Stream Analytics tools for Visual Studio automatikusan új verziókkal kereséséhez. Kövesse a megjelenő ablakban található útmutatást követve telepítse a legújabb verziót. 


## <a name="uninstall"></a>Eltávolítás

### <a name="visual-studio-2017"></a>Visual Studio 2017
Kattintson duplán a Visual Studio telepítő, és válassza ki **módosítás**. Törölje a jelet a **Azure Data Lake és a Stream Analytics eszközök** vagy jelölőnégyzetet a **adattárolás és feldolgozási** munkaterhelés vagy az **Azure fejlesztési** munkaterhelés.

### <a name="visual-studio-2013-and-visual-studio-2015"></a>A Visual Studio 2013 és a Visual Studio 2015-höz
Nyissa meg a Vezérlőpultot, és távolítsa el **Microsoft Azure Data Lake és a Stream Analytics a Visual Studio eszközök**.





