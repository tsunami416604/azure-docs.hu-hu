---
title: A Microsoft Azure StorSimple és szolgáltatón Program áttekintése |} Microsoft Docs
description: Áttekintése a StorSimple és a CSP a StorSimple-partnerek számára.
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
ms.openlocfilehash: c8cb51093142146fc7d43b51a62d949f6cc38988
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875654"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Cloud Solution Provider programot a StorSimple virtuális tömb telepíteni

## <a name="overview"></a>Áttekintés

A StorSimple virtuális tömb is telepíthető a Cloud Solution Provider (CSP) partnerek biztosítják az ügyfelek számára. Kriptográfiai Szolgáltató partner hozhat létre a StorSimple eszköz Manager szolgáltatást. Ez a szolgáltatás telepítéséhez és kezeléséhez a StorSimple virtuális tömb és a kapcsolódó megosztások, kötetek és biztonsági mentések majd használható.

Ez a cikk ismerteti, hogyan CSP partner az ügyfél vagy egy új előfizetés hozzáadása egy meglévő ügyfél és majd hozzon létre egy StorSimple virtuális tömb CSP a telepítendő szolgáltatást.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

- A kriptográfiai Szolgáltató program alatt csatlakozott.
- Rendelkezik érvényes [Partnerközpontjában](http://partnercenter.microsoft.com/) bejelentkezési hitelesítő adatokat. A hitelesítő adatok lehetővé teszik a jelentkezzen be a partnerportálra, új felhasználók hozzáadása, az ügyfelek kereséséhez, vagy keresse meg a Partner irányítópultról egy felhasználói fiókot. A kriptográfiai Szolgáltató működhessen, a StorSimple-rendszergazdák az Azure-portálon az ügyfél nevében.
                             
## <a name="add-a-customer"></a>Az ügyfél hozzáadása

Ha ad hozzá, az ügyfél, a rendszer automatikusan létrehoz egy előfizetést. Az ügyfél hozzáadása (és automatikusan az előfizetés létrehozása), hajtsa végre az alábbi lépéseket a Partner portál.

1. Lépjen a [Partnerközpontjában](http://partnercenter.microsoft.com/) , jelentkezzen be a kriptográfiai Szolgáltató hitelesítő adataival. Kattintson a **irányítópult**.

     ![A Partner Center irányítópult](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Kattintson a bal oldali ablaktáblán, **ügyfelek**. Kattintson a jobb oldali ablaktáblában **adja hozzá az ügyfelek**. Adja meg az ügyfél adatait. Kattintson a **tovább: előfizetések** hozhat létre olyan ügyfél-előfizetést.

    ![Ügyfél hozzáadása](./media/storsimple-partner-csp-deploy/image2.png)

3.  Válassza ki **Microsoft Azure** kínálnak. Görgessen a lap alján, és kattintson a **felülvizsgálati**.

    ![Tekintse át az előfizetési adatok](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Tekintse át az adatokat, és kattintson a **Submit**.

    ![Küldje el az előfizetéshez](./media/storsimple-partner-csp-deploy/image4.png)

5. Mentse a megerősítő adatokat későbbi felhasználás céljából.

    ![Mentés megerősítése](./media/storsimple-partner-csp-deploy/image5.png)

6. Található, vagy navigáljon arra az ügyfél, az előzőekben adott hozzá. Kattintson a **vállalatnév** részleteit feltárásához.

    ![Keresse meg az ügyfél](./media/storsimple-partner-csp-deploy/image6.png)  

7. Válassza ki a bal oldali ablaktáblán, **szolgáltatásfelügyelet**. A jobb oldali ablaktáblában a **felügyelje a szolgáltatásokat**, kattintson a **Microsoft Azure Management Portal** jelentkezhet be a felhasználó Azure rendszergazdaként.

    ![Jelentkezzen be Azure-portálon](./media/storsimple-partner-csp-deploy/image9.png)

8. A StorSimple Device Manager létrehozásához kattintson a **+ új** , és keresse meg, vagy navigáljon arra **StorSimple virtuális eszköz adatsorozat**. További információkért látogasson el [StorSimple Device Manager szolgáltatás telepítése](storsimple-virtual-array-manage-service.md).

    ![StorSimple Device Manager szolgáltatás létrehozása](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Előfizetés hozzáadása

Bizonyos esetekben előfordulhat, hogy egy meglévő ügyfél, és hozzá kell adnia egy előfizetést. Előfizetés hozzáadása egy meglévő ügyfél, hajtsa végre az alábbi lépéseket a Partner portál.

1. Lépjen a [Partnerközpontjában](http://partnercenter.microsoft.com/) , jelentkezzen be a kriptográfiai Szolgáltató hitelesítő adataival. Kattintson a **irányítópult**.

     ![A Partner Center irányítópult](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Kattintson a bal oldali ablaktáblán, **ügyfelek**. Található, vagy keresse meg az ügyfélnek hozzá szeretne adni egy előfizetés. Kattintson a ![kibontott pipa ikon](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) ikonra kattintva bontsa ki a vállalat nevét az ügyfél számára a sort. Kattintson a részletek **előfizetéseket**.

    ![Ügyfelek](./media/storsimple-partner-csp-deploy/image10.png)

3. Ellenőrizze **Microsoft Azure** a a **ajánlatok felső** a előfizetés, majd kattintson a **Submit**. Ez egy új előfizetést hoz létre.

    ![Új előfizetés hozzáadása](./media/storsimple-partner-csp-deploy/image11.png)

6. Új előfizetés létrehozása után kattintson **<--ügyfelek** való visszatéréshez bal oldali ablaktáblájában a **ügyfelek** lap. Keresse meg az ügyfél, amely most létrehozott egy előfizetést. Kattintson a **vállalatnév** részleteit feltárásához.

    ![Keresse meg az ügyfél](./media/storsimple-partner-csp-deploy/image6.png)  

7. Válassza ki a bal oldali ablaktáblán, **szolgáltatásfelügyelet**. A jobb oldali ablaktáblában a **felügyelje a szolgáltatásokat**, kattintson a **Microsoft Azure Management Portal** jelentkezhet be a felhasználó Azure rendszergazdaként.

    ![Jelentkezzen be Azure-portálon](./media/storsimple-partner-csp-deploy/image9.png)

8. A StorSimple Device Manager létrehozásához kattintson a **+ új** , és keresse meg, vagy navigáljon arra **StorSimple virtuális eszköz adatsorozat**. További információkért látogasson el [StorSimple Device Manager szolgáltatás telepítése](storsimple-virtual-array-manage-service.md).

    ![StorSimple Device Manager szolgáltatás létrehozása](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Következő lépések

- Ha kérdései további a StorSimple a CSP-hez, folytassa a [CSP a StorSimple: gyakran ismételt kérdések](storsimple-partner-csp-faq.md).
- Ha készen áll a központi telepítése a StorSimple, folytassa a [központi telepítése a CSP a StorSimple](storsimple-partner-csp-deploy.md).
