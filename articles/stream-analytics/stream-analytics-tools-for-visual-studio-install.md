---
title: Az Azure Stream Analytics-eszközök beállítása a Visual Studio számára
description: Ez a cikk ismerteti a telepítési követelményeket, és hogyan állíthatja be az Azure Stream Analytics-eszközöket a Visual Studio számára.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: fa3990d2239da71fb27f4c9a06699f758fcfe7a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354367"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Az Azure Stream Analytics-eszközök telepítése a Visual Studio-hoz

A Visual Studio 2019 és a Visual Studio 2017 támogatja az Azure Data Lake és a Stream Analytics-eszközöket. Ez a cikk az eszközök telepítését és eltávolítását ismerteti.

Az eszközök használatáról további információt a [Rövid útmutató: Azure Stream Analytics-feladat létrehozása](stream-analytics-quick-create-vs.md)a Visual Studio használatával című témakörben talál.

## <a name="install"></a>Telepítés

A Visual Studio Enterprise (Ultimate/Premium), professional és community kiadások támogatják az eszközöket. Az Express edition és a Visual Studio for Mac nem támogatja őket.

A Visual Studio 2019-et ajánljuk.

### <a name="install-for-visual-studio-2019-and-2017"></a>Telepítés a Visual Studio 2019-hez és 2017-hez<a name="recommended-visual-studio-2019-and-2017"></a>

Az Azure Data Lake és a Stream Analytics Tools az **Azure fejlesztési** és **adattárolási és feldolgozási** számítási feladatainak részét képezi. Engedélyezze a két számítási feladat egyikét a telepítés során. Ha a Visual Studio már telepítve van, válassza **az Eszközök** > **be- és szolgáltatások** lehetőséget a munkaterhelések hozzáadásához.

Töltse le [a Visual Studio 2019 (Preview 2 vagy újabb) vagy a Visual Studio 2017 (15.3 vagy újabb)](https://www.visualstudio.com/) alkalmazást, és kövesse a telepítéshez szükséges utasításokat.

Válassza ki az **Adattárolási és feldolgozási** munkaterhelést az ábrán látható módon:

![Az adattárolási és feldolgozási munkaterhelés van kiválasztva](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Válassza ki az **Azure fejlesztési** számítási feladatait az alábbi módon:

![Az Azure fejlesztési munkaterhelése van kiválasztva](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

A munkaterhelés hozzáadása után frissítse az eszközöket. Ez az eljárás a Visual Studio 2019-re vonatkozik:

1. Válassza **a Bővítmények** > **bővítmények kezelése lehetőséget.**

1. A **Bővítmények kezelése**csoportban válassza a **Frissítések** lehetőséget, és válassza az Azure Data Lake és a **Stream Analytics-eszközök lehetőséget.**

1. A legújabb bővítmény telepítéséhez válassza a **Frissítés** lehetőséget.

![Visual Studio-bővítmények és -frissítések](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Telepítés a Visual Studio 2015-höz és 2013-hoz<a name="visual-studio-2015-2013"></a>

A Visual Studio Enterprise (Ultimate/Premium), professional és community kiadások támogatják az eszközöket. Az expressz kiadás nem támogatja őket.

* Telepítse a Visual Studio 2015 vagy a Visual Studio 2013 4.
* Telepítse a Microsoft Azure SDK-t a .NET 2.7.1-es vagy újabb verziójához a [webes platform telepítőjével.](https://www.microsoft.com/web/downloads/platform.aspx)
* Telepítse a [Microsoft Azure Data Lake és a Stream Analytics Tools for Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504)alkalmazást.

## <a name="update"></a>Frissítés<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

A Visual Studio 2019 és a Visual Studio 2017 esetében egy új verzióemlékeztető jelenik meg Visual Studio értesítésként.

A Visual Studio 2015 és a Visual Studio 2013 esetében az eszközök automatikusan ellenőrzik az új verziókat. A legújabb verzió telepítéséhez kövesse az utasításokat.

## <a name="uninstall"></a>Eltávolítás

Eltávolíthatja az Azure Data Lake és a Stream Analytics-eszközöket. A Visual Studio 2019 vagy a Visual Studio 2017 esetén válassza **az Eszközök** > **beszerezni az eszközöket és a szolgáltatásokat**lehetőséget. A **Módosítás területen**törölje a jelet az Azure Data Lake és a Stream Analytics Tools **jelölőnégyzetből.** Úgy tűnik, vagy az **adatok tárolási és feldolgozási** számítási feladatok vagy az **Azure fejlesztési** számítási feladatok alatt jelenik meg.

A Visual Studio 2015-ből vagy a Visual Studio 2013-ból való eltávolításhoz nyissa meg a **Vezérlőpult** > **programok és szolgáltatások című témakört.** Távolítsa el a **Microsoft Azure Data Lake és a Stream Analytics Tools for Visual Studio**alkalmazást.
