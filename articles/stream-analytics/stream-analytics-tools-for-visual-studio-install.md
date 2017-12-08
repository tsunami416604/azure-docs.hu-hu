---
title: "A Visual Studio Azure Stream Analytics eszközök telepítési utasításait |} Microsoft Docs"
description: "A Visual Studio Azure Stream Analytics eszközök telepítési útmutatója"
keywords: A Visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: 80ce672ae91231e432f7ac9da49df29bb03efeca
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>A Stream Analytics-eszközök Visual Studio telepítési útmutatója
Azure Stream Analytics-eszközök mostantól támogatja a Visual Studio 2017, 2015 és 2013. Ebben a dokumentumban bemutatása után hogyan kell telepíteni, és távolítsa el az eszközöket.

Ismerje meg, hogyan használható [Stream Analytics tools for Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Telepítés
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Töltse le [(15.3 vagy újabb) a Visual Studio 2017](https://www.visualstudio.com/). Enterprise (Ultimate/prémium), Professional és Community Edition kiadásai támogatottak. Express kiadás nem támogatott. 
* Stream Analytics eszközök részét képezik a **Azure fejlesztési** és **adattárolás és feldolgozási** a Visual Studio 2017 munkaterhelések. Engedélyezi vagy az egyik ilyen két terhelések a Visual Studio-telepítés részeként.

Engedélyezze a **adattárolás és feldolgozási** munkaterhelés látható módon:

![Adatok tárolási és feldolgozási terhelés](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

Engedélyezze a **Azure fejlesztési** munkaterhelés látható módon:

![Azure fejlesztési munkaterhelés](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>A Visual Studio 2013, 2015
* Telepítse a Visual Studio 2015-öt vagy a Visual Studio 2013 Update 4. Enterprise (Ultimate/prémium), Professional és Community Edition kiadásai támogatottak. Express kiadás nem támogatott. 
* Telepítse a Microsoft Azure SDK for .NET 2.7.1-es verzió vagy újabb használatával a [webplatform-telepítő](http://www.microsoft.com/web/downloads/platform.aspx).
* Telepítés [Azure Stream Analytics tools for Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Frissítés

### <a name="visual-studio-2017"></a>Visual Studio 2017
Az új verzió emlékeztető megjelennek a Visual Studio értesítést. 

### <a name="visual-studio-2013-2015"></a>A Visual Studio 2013, 2015
A telepített Stream Analytics tools for Visual Studio automatikusan új verziókkal kereséséhez. Kövesse a megjelenő ablakban található útmutatást követve telepítse a legújabb verziót. 


## <a name="uninstall"></a>Eltávolítás

### <a name="visual-studio-2017"></a>Visual Studio 2017
Kattintson duplán a Visual Studio telepítő, és válassza ki **módosítás**. Törölje a jelet a **Azure Data Lake és a Stream Analytics eszközök** vagy jelölőnégyzetet a **adattárolás és feldolgozási** munkaterhelés vagy az **Azure fejlesztési** munkaterhelés.

### <a name="visual-studio-2013-2015"></a>A Visual Studio 2013, 2015
Nyissa meg a Vezérlőpultot, és távolítsa el **Microsoft Azure Data Lake és a Stream Analytics a Visual Studio eszközök**.





