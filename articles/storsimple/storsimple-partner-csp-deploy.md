---
title: A Microsoft Azure StorSimple és a felhőalapú megoldások szolgáltatóján Program áttekintése |} A Microsoft Docs
description: Áttekintés a StorSimple és a CSP StorSimple partnerek számára.
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
ms.openlocfilehash: 0dac86a696599a391cb243ad11e16931e00b8921
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238930"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>A StorSimple Virtual Array üzembe helyezése a Cloud Solution Provider Program számára

## <a name="overview"></a>Áttekintés

A StorSimple Virtual Array központilag telepítheti a Cloud Solution Provider (CSP) partnerek ügyfeleik számára. CSP-partnerként létrehozhat egy StorSimple-Eszközkezelő szolgáltatás. Ez a szolgáltatás használható üzembe helyezése és kezelése a StorSimple Virtual Array és a kapcsolódó megosztások, kötetek és biztonsági mentések majd.

Ez a cikk bemutatja, hogyan CSP-partner egy ügyfél vagy egy új előfizetés hozzáadása egy meglévő ügyfél és hozzon létre egy szolgáltatást, hogy a CSP a StorSimple Virtual Array üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

- A CSP program keretében regisztrált.
- Rendelkezik érvényes [Partnerközpont](https://partnercenter.microsoft.com/) bejelentkezési hitelesítő adatait. A hitelesítő adatok engedélyezi, hogy jelentkezzen be a Partner portálra, adja hozzá az új ügyfeleket, keresse meg az ügyfelek vagy a Partner irányítópulton nyissa meg egy felhasználói fiókot. A CSP a StorSimple-rendszergazdaként működjön az Azure Portalon a vásárló nevében.
                             
## <a name="add-a-customer"></a>Ügyfél hozzáadása

Ha egy ügyfél ad hozzá, automatikusan létrejön egy előfizetést. Ügyfél hozzáadása (és automatikusan létrehoz egy előfizetés), hajtsa végre az alábbi lépéseket a partnerfiókhoz.

1. Nyissa meg a [Partnerközpont](https://partnercenter.microsoft.com/) , és jelentkezzen be a CSP hitelesítő adatok használatával. Kattintson a **irányítópult**.

     ![A Partner Center irányítópultján](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. A bal oldali ablaktáblán kattintson **ügyfelek**. Kattintson a jobb oldali ablaktáblában **adja hozzá az ügyfelek**. Adja meg az ügyfél adatait. Kattintson a **tovább: előfizetések** ügyfél-előfizetés létrehozásához.

    ![Ügyfél hozzáadása](./media/storsimple-partner-csp-deploy/image2.png)

3.  Válassza ki **Microsoft Azure** kínálnak. Görgessen a lap aljára, és kattintson a **felülvizsgálati**.

    ![Tekintse át az előfizetési adatok](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Tekintse át az adatokat, és kattintson a **küldés**.

    ![Előfizetés beküldése](./media/storsimple-partner-csp-deploy/image4.png)

5. Mentse a későbbiekben megerősítő információkat.

    ![Mentés megerősítése](./media/storsimple-partner-csp-deploy/image5.png)

6. Keresse meg vagy nyissa meg az ügyfél, az előzőekben adott hozzá. Kattintson a **cégnév** a részletek feltárásához.

    ![Az ügyfél keresése](./media/storsimple-partner-csp-deploy/image6.png)  

7. A bal oldali ablaktáblán válassza **szolgáltatásfelügyelet**. A jobb oldali ablaktáblában alatt **felügyelheti a szolgáltatásokat**, kattintson a **Microsoft Azure felügyeleti portálján** , az ügyfelek az Azure-rendszergazdaként jelentkezzen be.

    ![Bejelentkezés az Azure Portalra](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple-Eszközkezelő létrehozásához kattintson a **+ új** , és keresse meg, vagy lépjen **virtuális StorSimple fizikaieszköz-sorozat**. További információért ugorjon [egy StorSimple-Eszközkezelő szolgáltatás üzembe helyezése](storsimple-virtual-array-manage-service.md).

    ![StorSimple-Eszközkezelő szolgáltatás létrehozása](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Előfizetés hozzáadása

Bizonyos esetekben előfordulhat, hogy egy meglévő ügyfél, és a egy előfizetés hozzá kell adnia. Előfizetés hozzáadása egy meglévő ügyfél, hajtsa végre az alábbi lépéseket a partnerportálon.

1. Nyissa meg a [Partnerközpont](https://partnercenter.microsoft.com/) , és jelentkezzen be a CSP hitelesítő adatok használatával. Kattintson a **irányítópult**.

     ![A Partner Center irányítópultján](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. A bal oldali ablaktáblán kattintson **ügyfelek**. Keresse meg vagy nyissa meg az ügyfél-előfizetés hozzáadása szeretne. Kattintson a ![Kibontás pipa ikon](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) ikonra kattintva bontsa ki a vállalat nevét az ügyfél számára a sort. A részletekért kattintson **előfizetések hozzáadása**.

    ![Ügyfelek](./media/storsimple-partner-csp-deploy/image10.png)

3. Ellenőrizze **Microsoft Azure** számára a **ajánlatok az első** az előfizetést, és kattintson a **küldés**. Ez egy új előfizetést hoz létre.

    ![Új előfizetés hozzáadása](./media/storsimple-partner-csp-deploy/image11.png)

6. Új előfizetés létrehozása után kattintson a **<--ügyfelek** a bal oldali ablaktáblában való visszatéréshez a **ügyfelek** lapot. Keresse meg az ügyfél, akik számára létrehozott egy előfizetést. Kattintson a **cégnév** a részletek feltárásához.

    ![Az ügyfél keresése](./media/storsimple-partner-csp-deploy/image6.png)  

7. A bal oldali ablaktáblán válassza **szolgáltatásfelügyelet**. A jobb oldali ablaktáblában alatt **felügyelheti a szolgáltatásokat**, kattintson a **Microsoft Azure felügyeleti portálján** , az ügyfelek az Azure-rendszergazdaként jelentkezzen be.

    ![Bejelentkezés az Azure Portalra](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple-Eszközkezelő létrehozásához kattintson a **+ új** , és keresse meg, vagy lépjen **virtuális StorSimple fizikaieszköz-sorozat**. További információért ugorjon [egy StorSimple-Eszközkezelő szolgáltatás üzembe helyezése](storsimple-virtual-array-manage-service.md).

    ![StorSimple-Eszközkezelő szolgáltatás létrehozása](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>További lépések

- Ha a CSP a StorSimple kapcsolatban további kérdése van, lépjen a [StorSimple CSP: gyakran ismételt kérdések](storsimple-partner-csp-faq.md).
- Ha készen áll a StorSimple üzembe helyezése, lépjen a [a CSP a StorSimple üzembe helyezése](storsimple-partner-csp-deploy.md).
