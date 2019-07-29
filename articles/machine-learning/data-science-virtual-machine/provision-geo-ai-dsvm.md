---
title: Az Azure-ban – földrajzi mesterséges intelligenciát használó virtuális gép kiépítése az Azure |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre, és a földrajzi mesterséges Intelligencia adatelemző virtuális gép konfigurálása. A földrajzi mesterséges Intelligencia adatelemzési virtuális gépet használó földrajzi mesterséges Intelligencia és gépi Tanulási megoldások létrehozásához szükséges eszközöket biztosít.
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2018
ms.author: vijetaj
ms.openlocfilehash: 4772bf8341196485a91b3df30801b9714a4a64a8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68591873"
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Az Azure-ban földrajzi mesterséges intelligenciát használó virtuális gép kiépítése 

A földrajzi mesterséges Intelligencia adatelemző virtuális gép (Geo-DSVM) az kiterjesztése, a népszerű [Azure adatelemzési virtuális gép](https://aka.ms/dsvm) úgy, hogy a földrajzi mesterséges Intelligencia és a analytics speciálisan konfigurált. A térinformatikai elemzés, a virtuális gép működteti [ArcGIS Pro](https://www.arcgis.com/features/index.html). Az adatelemző virtuális gép lehetővé teszi, hogy a machine learning-modellek, és még akkor is, a mély tanulási modelleket, földrajzi információk renderelésre van adatok gyors képzése. Támogatott a Windows 2016 DSVM csak. 

A Geo-DSVM több eszközt tartalmaz, beleértve a mesterséges Intelligencia tartalmazza:

- Számos népszerű deep learning keretrendszerek GPU kiadása, például a Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer; 
- eszközök beszerzésére és előfeldolgozási lemezképet, a szöveges adat 
- fejlesztési tevékenységek, például a Microsoft R Server Developer Edition, Anaconda Python, a Jupyter notebooks, a Python és az R, Python és az R, SQL ide-eszközök adatbázisok
- Az ESRI ArcGIS Pro asztali szoftverek Python- és R-felületek, amely az AI-alkalmazások, a térinformatikai adatok használható együtt. 
 

## <a name="create-your-geo-ai-data-science-vm"></a>A földrajzi mesterséges intelligenciát Használó adatelemzési virtuális gép létrehozása

A következő egy példányát a földrajzi mesterséges Intelligencia adatelemző virtuális gép létrehozására szolgáló eljárás: 


1. Keresse meg a virtuális gépet, az ajánlati [az Azure portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Válassza ki a **létrehozás** gombra az alsó kell figyelembe venni a varázslót.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. A varázslóval való létrehozását a Geo-dsvm-hez szükséges **bemenetek** minden a **négy lépést** jobb oldalán Ez az ábra enumerálása. Az alábbiakban a bemeneti adatok konfigurálása az egyes lépéseket:



   - **Alapvető beállítások**

      1. **Név**: A létrehozandó adatelemzési kiszolgáló neve.

      2. **Felhasználónév**: Rendszergazdai fiók bejelentkezési azonosítója.

      3. **Jelszó**: Rendszergazdai fiók jelszava.

      4. **Előfizetés**: Ha egynél több előfizetéssel rendelkezik, válassza ki az egyik, amelyen a gép létrehozása és a számlázás.

      5. **Erőforráscsoport**: Létrehozhat egy újat, vagy egy **üres** meglévő Azure-erőforráscsoportot is használhat az előfizetésében.

      6. **Hely**: Válassza ki a legmegfelelőbb adatközpontot. Általában az adatközpont, amely tartalmazza a legtöbb az adatokat, vagy a leggyorsabb hálózati hozzáféréshez a fizikai helyéhez legközelebbi. Ha kell tennie a deep learning gpu-alapú, választania kell a helyen az Azure-ban, amely rendelkezik a NC sorozatú GPU VM-példányokon. A GPU virtuális gépekkel rendelkező helyszínek jelenleg a következők: USA keleti régiója, USA északi középső régiója, USA déli középső régiója, USA 2. nyugati régiója, **Észak-Európa, Nyugat-Európa**. Ellenőrizze a teljes lista a [régió lap által az Azure-termékekkel](https://azure.microsoft.com/regions/services/) , és keressen **NC sorozat** alatt **számítási**. 


   - **Beállítások**: Válassza ki az NC-sorozat GPU virtuálisgép-méretének egyikét, ha a Geo-DSVM a GPU-val való mélyreható tanulást kíván futtatni. Ellenkező esetben választhat egyet a CPU-alapú példány.  Hozzon létre egy tárfiókot a virtuális géphez. 
   
   - **Összefoglalás**: Győződjön meg arról, hogy minden, a megadott adatok helyesek.

   - **Vásároljon**: A kiépítés elindításához kattintson a **vásárlás** gombra. Egy hivatkozást a szolgáltatás feltételeit. A virtuális gép nem rendelkezik a kiválasztott kiszolgáló méretét a számítási túl további díjakat a **mérete** . lépés. 
 
>[!NOTE]
> A kiépítés körülbelül 20-30 percet vesz igénybe. A kiépítési állapota jelenik meg az Azure Portalon.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>A földrajzi mesterséges Intelligencia adatelemző virtuális gép elérése

 A virtuális gép létrehozása után készen áll az eszközöket, amelyek telepítve és konfigurálva van indíthatja a rajta. Start menü csempék és számos olyan eszközzel asztali ikonok vannak. A távoli asztal is be a rendszergazdai fiók hitelesítő adatait, amely az előző konfigurálta a **alapjai** szakaszban. 

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>ArcGIS Pro segítségével telepítve a virtuális gépen

A Geo-dsvm-hez már előre telepítve van az ArcGIS Pro asztali és a környezet előre konfigurálva a DSVM minden olyan eszközt. Amikor hozzákezd a ArcGIS kéri egy jelentkezzen be az ArcGIS-fiókjába. Ha már rendelkezik az ArcGIS-fiókkal, és rendelkezik a szoftver licencét, használhatja a meglévő hitelesítő adatait.  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Ellenkező esetben regisztráljon új ArcGIS-fiók és licenc vagy beolvasása egy [az ingyenes próbaidőszak](https://www.arcgis.com/features/free-trial.html). 

![Az ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

A fizetős vagy ingyenes próbaverziós ArcGIS-fiókra való regisztráció után engedélyezheti a ArcGIS Pro-t a fiókjához a következő témakörben található utasítások követésével: [első lépések a ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf)használatával. 

Bejelentkezés után az ArcGIS Pro asztali az ArcGIS-fiókját, készen áll a beépített adatelemzési eszközzel, amelyek telepítése és konfigurálása a virtuális gépen, a térinformatikai elemzés és a machine learning-projektek használatának megkezdéséhez.

## <a name="next-steps"></a>További lépések

A földrajzi mesterséges Intelligencia adatelemző virtuális gép-használatának az az alábbi témakörök útmutatást:

* [A földrajzi mesterséges Intelligencia adatelemzési virtuális gép használata](use-geo-ai-dsvm.md)
