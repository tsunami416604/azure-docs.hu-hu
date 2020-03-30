---
title: StorSimple virtuális tömb telepítése a felhőalapú megoldásszolgáltatói programhoz
description: Áttekintés a StorSimple és a StorSimple-partnerek kripta- alkalmazásáról.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: 7f1927a67127766c72be463c283225135b2a2aad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77466909"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>StorSimple virtuális tömb telepítése a felhőalapú megoldásszolgáltatói programhoz

## <a name="overview"></a>Áttekintés

A StorSimple virtuális tömbet a felhőszolgáltató (CSP) partnerei telepíthetik ügyfeleik számára. A csp-partner létrehozhat egy StorSimple Eszközkezelő szolgáltatást. Ez a szolgáltatás ezután a StorSimple virtuális tömb és a kapcsolódó megosztások, kötetek és biztonsági mentések üzembe helyezésére és kezelésére használható.

Ez a cikk azt ismerteti, hogy a csp-partner hogyan adhat hozzá ügyfelet vagy új előfizetést egy meglévő ügyfélhez, majd hozhat létre egy szolgáltatást a StorSimple virtuális tömb központi telepítéséhez a csp-ben.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

- Ön beiratkozott a KP programba.
- Érvényes [Partnerközpont](https://partnercenter.microsoft.com/) bejelentkezési hitelesítő adataival rendelkezik. A hitelesítő adatok lehetővé teszik, hogy jelentkezzen be a Partner portálon új ügyfelek hozzáadásához, ügyfelek kereséséhez, vagy egy ügyfélfiókhoz való navigáláshoz a Partner irányítópultjáról. A csp storsimple-rendszergazdaként működhet az ügyfél nevében az Azure Portalon.
                             
## <a name="add-a-customer"></a>Vevő hozzáadása

Ha vevőt ad hozzá, a rendszer automatikusan létrehoz egy előfizetést. Ügyfél hozzáadásához (és előfizetés automatikus létrehozásához) hajtsa végre a következő lépéseket a Partner portálon.

1. Nyissa meg a [Partnerközpontot,](https://partnercenter.microsoft.com/) és jelentkezzen be a kripta-hitelesítő adatokkal. Kattintson **az Irányítópult gombra.**

     ![Irányítópult a Partnerközpontban](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. A bal oldali ablaktáblában kattintson a **Vevők**elemre. A jobb oldali ablaktáblában kattintson a **Vevők hozzáadása**elemre. Adja meg a vevő adatait. Kattintson a **Tovább: Előfizetések** elemre az ügyfél-előfizetés létrehozásához.

    ![Vevő hozzáadása](./media/storsimple-partner-csp-deploy/image2.png)

3.  Válassza a **Microsoft Azure-ajánlat** lehetőséget. Görgessen a lap aljára, és kattintson a **Véleményezés**gombra.

    ![Előfizetési adatok áttekintése](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Tekintse át az információkat, és kattintson a **Küldés gombra.**

    ![Előfizetés beküldése](./media/storsimple-partner-csp-deploy/image4.png)

5. Mentse a megerősítési információkat későbbi használatra.

    ![Megerősítés mentése](./media/storsimple-partner-csp-deploy/image5.png)

6. Keresse meg vagy keresse meg az imént felvett ügyfelet. Kattintson a **Vállalat nevére** a részletek részletezéséhez.

    ![Az ügyfél keresése](./media/storsimple-partner-csp-deploy/image6.png)  

7. A bal oldali ablaktáblában válassza a **Szolgáltatáskezelés**lehetőséget. A jobb oldali ablaktáblában a **Szolgáltatások felügyelete**csoportban kattintson a **Microsoft Azure Management Portal** elemre, ha azure-rendszergazdaként szeretne bejelentkezni az ügyfél számára.

    ![Bejelentkezés az Azure Portalra](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple eszközkezelő létrehozásához kattintson **a + Új** gombra, és keresse meg a **StorSimple virtuális eszközsorozatot,** vagy keresse meg azt. További információ: [Deploy a StorSimple Device Manager szolgáltatás.](storsimple-virtual-array-manage-service.md)

    ![StorSimple Eszközkezelő szolgáltatás létrehozása](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Előfizetés hozzáadása

Bizonyos esetekben előfordulhat, hogy egy meglévő ügyfél, és hozzá kell adnia egy előfizetést. Ha előfizetést szeretne hozzáadni egy meglévő ügyfélhez, hajtsa végre az alábbi lépéseket a Partner portálon.

1. Nyissa meg a [Partnerközpontot,](https://partnercenter.microsoft.com/) és jelentkezzen be a kripta-hitelesítő adatokkal. Kattintson **az Irányítópult gombra.**

     ![Irányítópult a Partnerközpontban](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. A bal oldali ablaktáblában kattintson a **Vevők**elemre. Keresse meg vagy keresse meg azt az ügyfelet, akihez előfizetést szeretne hozzáadni. Kattintson ![a](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) Csekk kibontása ikonra a vevő cégnevének sorának kibontásához. A részletekben kattintson az **Előfizetések hozzáadása gombra.**

    ![Ügyfelek](./media/storsimple-partner-csp-deploy/image10.png)

3. Ellenőrizze a **Microsoft Azure-ban** az előfizetés **legnépszerűbb ajánlatait,** és kattintson a **Küldés**gombra. Ez létrehoz egy új előfizetést.

    ![Új előfizetés hozzáadása](./media/storsimple-partner-csp-deploy/image11.png)

6. Új előfizetés létrehozása után kattintson **a< – A** bal oldali ablaktáblában lévő ügyfelek **gombra** kattintva térjen vissza az Ügyfelek lapra. Keresse meg azt az ügyfelet, akinek az imént létrehozott egy előfizetést. Kattintson a **Vállalat nevére** a részletek részletezéséhez.

    ![Az ügyfél keresése](./media/storsimple-partner-csp-deploy/image6.png)  

7. A bal oldali ablaktáblában válassza a **Szolgáltatáskezelés**lehetőséget. A jobb oldali ablaktáblában a **Szolgáltatások felügyelete**csoportban kattintson a **Microsoft Azure Management Portal** elemre, ha azure-rendszergazdaként szeretne bejelentkezni az ügyfél számára.

    ![Bejelentkezés az Azure Portalra](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple eszközkezelő létrehozásához kattintson **a + Új** gombra, és keresse meg a **StorSimple virtuális eszközsorozatot,** vagy keresse meg azt. További információ: [Deploy a StorSimple Device Manager szolgáltatás.](storsimple-virtual-array-manage-service.md)

    ![StorSimple Eszközkezelő szolgáltatás létrehozása](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>További lépések

- Ha további kérdései vannak a StorSimple-vel kapcsolatban a kriptagépben, látogasson el a [StorSimple webhelyre a KTP-ben: Gyakori kérdések](storsimple-partner-csp-faq.md).
- Ha készen áll a StorSimple üzembe helyezésére, nyissa [meg a StorSimple központi telepítését a CSP-ben.](storsimple-partner-csp-deploy.md)
