---
title: Automatikus felhőszolgáltatások méretezése a portálon |} A Microsoft Docs
description: Útmutató az automatikus méretezési szabályok egy felhőalapú szolgáltatás webes szerepkört vagy feldolgozói szerepkör konfigurálása az Azure-ban a portál használatával.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 701d4404-5cc0-454b-999c-feb94c1685c0
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 29ee71e7946145e50cc875df96b674abec3e12df
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004336"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Automatikus skálázást a portál egy felhőalapú szolgáltatás konfigurálása

Feltételek beállítható egy felhőalapú szolgáltatás feldolgozói szerepkört, azt egy méretezési csoportban lévő vagy műveletet. A feltételek a szerepkör alapulhat a Processzor, lemez vagy a szerepkör a hálózati terhelés. Beállíthat egy feltételt, üzenetsor vagy valamilyen más Azure-erőforrás az Ön előfizetéséhez rendelve a metrika alapján.

> [!NOTE]
> Ez a cikk a felhőalapú szolgáltatás webes és feldolgozói szerepkörök összpontosít. Amikor közvetlenül hoz létre egy virtuális gép (klasszikus), a cloud service-ben üzemel. A társítás méretezhetők egy standard virtuális gép egy [rendelkezésre állási csoport](../virtual-machines/windows/classic/configure-availability-classic.md) és manuálisan kapcsolja be vagy ki.

## <a name="considerations"></a>Megfontolandó szempontok
Méretezés az alkalmazás konfigurálása előtt gondolja át az alábbi adatokat:

* Skálázás alapvető használati érinti.

    Nagyobb szerepkörpéldányok több magot használja. Az előfizetéshez tartozó magok keretein belül csak egy alkalmazás skálázhatja. Például hogy előfizetése rendelkezik-e 20 magos korlát. Ha a két közepes méretű cloud services (4 mag összesen) futtatja az alkalmazást, csak méretezhetők fel más felhőszolgáltatási környezetek az előfizetésében a fennmaradó 16 maggal. -Méretekkel kapcsolatos további információkért lásd: [Felhőszolgáltatás-méretek](cloud-services-sizes-specs.md).

* Méretezheti a várólista állapotüzenet küszöbértéke alapján. Üzenetsorok használatával kapcsolatos további információkért lásd: [a Queue Storage szolgáltatás használata](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Az előfizetéssel társított egyéb erőforrások is méretezheti.

* Ahhoz, hogy az alkalmazás magas rendelkezésre állású, győződjön meg, hogy a két vagy több szerepkörpéldányt telepítették. További információkért lásd: [szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/).

* Automatikus méretezés csak akkor fordul elő, ha minden szerepkör **készen** állapota.  


## <a name="where-scale-is-located"></a>Ahol a méretezési csoport megtalálható
Miután kiválasztotta a felhőalapú szolgáltatás, rendelkeznie kell a felhőalapú szolgáltatás panelje látható.

1. A cloud service panelen a a **szerepkörök és példányok** csempére, válassza ki a felhőszolgáltatás nevét.   
   **FONTOS**: Ellenőrizze, hogy kattintson a cloud service szerepkörre, nem a szerepkör-példány, amely alatt a szerepkör.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Válassza ki a **méretezési** csempére.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Az automatikus méretezés
A bármelyik kétféle segítségével konfigurálhat a szerepkör méretezési beállításainak **manuális** vagy **automatikus**. Manuális módon alakul, megadhatja az abszolút példányszámát. Automatikus azonban lehetővé teszi, hogy a szabályok, amelyek szabályozzák, hogy hogyan és hogyan lehet sokkal, horizontális.

Állítsa be a **a skálázás** beállítást **ütemezési és teljesítményszabályok**.

![Cloud services méretezési beállítások profil és a szabály](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Egy meglévő profilt.
2. A szülő-profilt egy szabály hozzáadásához.
3. Adjon hozzá egy másik profilhoz.

Válassza ki **profil hozzáadásához**. A profil határozza meg, melyik a méretezési csoport használni kívánt módot: **mindig**, **ismétlődési**, **rögzített dátum**.

Miután konfigurálta a profilt és szabályokat, válassza ki a **mentése** ikonra a felső.

#### <a name="profile"></a>Profil
A profil beállítja a méretezési csoport, minimális és maximális példányok és is amikor ez skálatartomány aktív.

* **Mindig**

    Mindig tartsa ezt a tartományt az elérhető példányok.  

    ![Felhőalapú szolgáltatás, amely mindig méretezése](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Ismétlődés**

    Válasszon olyan méretezése a hét napjait.

    ![Cloud service méretezés ismétlődési ütemezés](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Rögzített dátum**

    Rögzített dátumtartomány méretezése a szerepkört.

    ![CLoud service méretezés egy rögzített dátumon](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Miután konfigurálta a profil, válassza ki a **OK** gombot a profil panel alján.

#### <a name="rule"></a>Szabály
Szabályok kerülnek egy profilt, és egy feltételt, amely elindítja a méretezési csoport képviseli.

A szabály az eseményindító alapján történik a felhőszolgáltatás (CPU-használat, lemez-tevékenység vagy hálózati tevékenység), amelyhez feltételes értéket adhat hozzá egy metrikát. Emellett akkor is az eseményindító üzenetsor vagy valamilyen más Azure-erőforrás az Ön előfizetéséhez rendelve a metrika alapján.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Miután konfigurálta a szabály, válassza ki a **OK** gombra a szabály panel alján.

## <a name="back-to-manual-scale"></a>Vissza a Manuális méretezés
Keresse meg a [beállítások méretezése](#where-scale-is-located) és állítsa be a **a skálázás** beállítást **manuálisan megadott példányszám**.

![Cloud services méretezési beállítások profil és a szabály](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Ez a beállítás eltávolítja a szerepkört az automatikus skálázást, és állíthat be a példányszám közvetlenül.

1. A méretezési csoport (Manuális vagy automatikus) lehetőséget.
2. Egy szerepkör példány csúszkát be, hogy a példányok számára.
3. Méretezhető, hogy a szerepkör példányai.

Miután konfigurálta a beállításait, válassza ki a **mentése** ikonra a felső.
