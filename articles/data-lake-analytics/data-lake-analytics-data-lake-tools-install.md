---
title: Az Azure Data Lake Tools for Visual Studio telepítése
description: Ez a cikk az Azure Data Lake Tools for Visual Studio telepítését ismerteti.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70914094"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>A Data Lake Tools for Visual Studio telepítése

Ismerje meg, hogyan hozhat létre Azure Data Lake Analytics-fiókokat a Visual Studio használatával. Az [U-SQL-ben](data-lake-analytics-u-sql-get-started.md) megadhatja a feladatokat, és elküldheti a feladatokat a Data Lake Analytics szolgáltatásnak. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

## <a name="prerequisites"></a>Előfeltételek

* **Visual Studio**: Az Express kivételével minden kiadás támogatott.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK for .NET** 2.7.1-es vagy újabb verzió. Telepítse a [Webplatform-telepítővel](https://www.microsoft.com/web/downloads/platform.aspx).
* **Egy Data Lake Analytics-fiók.** Fiók létrehozásához lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Az Azure Data Lake-eszközök telepítése a Visual Studio 2017-hez vagy a Visual Studio 2019-hez

Az Azure Data Lake Tools for Visual Studio a Visual Studio 2017 15.3-as vagy újabb verzióinak támogatását támogatja. Az eszköz az **adattárolási és -feldolgozási** és **azure-fejlesztési** számítási feladatok része. A Visual Studio telepítésének részeként engedélyezze a két számítási feladat egyikét.

Engedélyezze az **Adattárolási és -feldolgozási** munkaterhelést az alábbi módon:

![Adattárolási és feldolgozási munkaterhelés engedélyezése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Engedélyezze az **Azure fejlesztési** számítási feladatát az alábbi módon:

![Válassza ki az Azure fejlesztési munkaterhelését](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Az Azure Data Lake Tools for Visual Studio 2013 és 2015 telepítése

Töltse le és telepítse [a Microsoft Azure Data Lake és a Stream Analytics Tools for Visual Studio ](https://aka.ms/adltoolsvs)alkalmazást. A telepítés után a Visual Studio a következő módosításokat hajtvégre:

* A **Server Explorer** > **Azure-csomópont** egy Data Lake **Analytics-csomópontot** tartalmaz.
* Az **Eszközök** menü tartalmaz egy **Data Lake** elemet.

## <a name="next-steps"></a>További lépések

* A diagnosztikai adatok naplózásához olvassa el az [Azure Data Lake Analytics diagnosztikai naplóinak elérése című témakört.](data-lake-analytics-diagnostic-logs.md)
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Webhelynaplók elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md).
* A csúcspont végrehajtási nézetének használatához olvassa [el A Csúcspont végrehajtási nézet használata a Visual Studio Data Lake Tools alkalmazásban című témakört.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
