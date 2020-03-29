---
title: Felhőszolgáltatás automatikus méretezése a portálon | Microsoft dokumentumok
description: Megtudhatja, hogyan konfigurálhatja a portál automatikus méretezési szabályok at egy felhőalapú szolgáltatás webes szerepkör vagy feldolgozói szerepkör az Azure-ban.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 5880544137855a2ea5bcd6d6e4bada46563564ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360837"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Az automatikus méretezés konfigurálása egy felhőszolgáltatáshoz a portálon

Feltételeket lehet beállítani egy felhőszolgáltatás-feldolgozói szerepkörhöz, amely egy skálázást aktivál a műveletben vagy a műveletben. A szerepkör feltételei alapulhatnak a szerepkör processzorán, lemezén vagy hálózati terhelésén. Egy üzenet-várólistára vagy az előfizetéshez társított más Azure-erőforrás metrikája alapján is beállíthat feltételt.

> [!NOTE]
> Ez a cikk a Cloud Service webes és feldolgozói szerepkörökre összpontosít. Amikor közvetlenül hoz létre egy virtuális gépet (klasszikus), az egy felhőszolgáltatásban található. A szabványos virtuális gépek méretezése egy [rendelkezésre állási készlettel](../virtual-machines/windows/classic/configure-availability-classic.md) társítva, és manuálisan be- vagy kikapcsolhatja őket.

## <a name="considerations"></a>Megfontolandó szempontok
Az alkalmazás méretezésének konfigurálása előtt vegye figyelembe a következő információkat:

* A méretezést befolyásolja az alapvető használat.

    A nagyobb szerepkörpéldányok több magot használnak. Egy alkalmazást csak az előfizetés magjainak határán belül méretezhet. Tegyük fel például, hogy az előfizetés legfeljebb 20 maggal rendelkezik. Ha két közepes méretű felhőszolgáltatással (összesen 4 maggal) rendelkező alkalmazást futtat, csak a fennmaradó 16 maggal skálázhatja fel az előfizetésben lévő egyéb felhőszolgáltatás-üzembe helyezéseket. A méretekről a [Felhőszolgáltatások méretei](cloud-services-sizes-specs.md)című témakörben talál további információt.

* A várólista-üzenetek küszöbértéke alapján skálázható. A várólisták használatáról a [Várólista-tárolási szolgáltatás használata című](../storage/queues/storage-dotnet-how-to-use-queues.md)témakörben talál további információt.

* Az előfizetéshez társított egyéb erőforrásokat is méretezhet.

* Az alkalmazás magas rendelkezésre állásának engedélyezéséhez győződjön meg arról, hogy két vagy több szerepkörpéldányok üzembe helyezése. További információt a [Szolgáltatásiszint-szerződések című témakörben talál.](https://azure.microsoft.com/support/legal/sla/)

* Az automatikus méretezés csak akkor történik meg, ha az összes szerepkör **Kész** állapotban van.  


## <a name="where-scale-is-located"></a>Hol található a skála?
Miután kiválasztotta a felhőszolgáltatás, rendelkeznie kell a felhőalapú szolgáltatás panel látható.

1. A felhőszolgáltatás panelen a **Szerepkörök és példányok** csempén válassza ki a felhőszolgáltatás nevét.   
   **FONTOS:** Győződjön meg arról, hogy a felhőszolgáltatási szerepkörre kattint, nem pedig a szerepkör alatti szerepkörpéldányra.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Jelölje ki a **méretezési** csempét.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatikus skála
A szerepkörméretezési beállításokat két **manuális** vagy **automatikus**móddal állíthatja be. Kézikönyv, ahogy elvárható, akkor állítsa be az abszolút száma példányok. Az Automatikus azonban lehetővé teszi olyan szabályok beállítását, amelyek meghatározzák, hogy hogyan és mennyit kell méreteznie.

Állítsa be a **Méretezés ütemezési** **és teljesítményszabályokat.**

![A felhőszolgáltatások méretezési beállításai profillal és szabállyal](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Egy meglévő profil.
2. Adjon hozzá egy szabályt a fölérendelt profilhoz.
3. Adjon hozzá egy másik profilt.

Válassza **a Profil hozzáadása lehetőséget.** A profil határozza meg, hogy melyik módot szeretné használni a skálához: **mindig**, **ismétlődés**, **rögzített dátum**.

Miután konfigurálta a profilt és a szabályokat, kattintson a **mentés** ikonra a tetején.

#### <a name="profile"></a>Profil
A profil a skálázás minimális és maximális példányait állítja be, és akkor is, ha ez a léptéktartomány aktív.

* **Mindig**

    Mindig tartsa elérhetővé ezt a példánytartományt.  

    ![Felhőszolgáltatás, amely mindig méretezhető](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Ismétlődés**

    Válassza ki a hét napjainak egy sor átméretezését.

    ![A felhőszolgáltatás méretezése ismétlődési ütemezéssel](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Rögzített dátum**

    Rögzített dátumtartomány a szerepkör méretezéséhez.

    ![CLoud szolgáltatás skála rögzített dátummal](./media/cloud-services-how-to-scale-portal/select-fixed.png)

A profil konfigurálása után válassza az **OK** gombot a profilpanel alján.

#### <a name="rule"></a>Szabály
A szabályok egy profilhoz kerülnek, és olyan feltételt jelölnek, amely elindítja a skálát.

A szabály eseményindító alapul a felhőszolgáltatás (CPU-használat, lemeztevékenység vagy hálózati tevékenység), amelyhez feltételes értéket adhat hozzá metrika. Emellett az eseményindító egy üzenet-várólistára vagy az előfizetéshez társított más Azure-erőforrás metrikája alapján is rendelkezhet.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

A szabály konfigurálása után válassza az **OK** gombot a szabálypanel alján.

## <a name="back-to-manual-scale"></a>Vissza a kézi méretezéshez
Keresse meg a [méretezési beállításokat,](#where-scale-is-located) és állítsa be a **Méretezés beállítás szerint** egy **példányszámot, amelyet manuálisan adok meg.**

![A felhőszolgáltatások méretezési beállításai profillal és szabállyal](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Ez a beállítás eltávolítja az automatikus skálázást a szerepkörből, és közvetlenül beállíthatja a példányok számát.

1. A skála (manuális vagy automatizált) opció.
2. Egy szerepkörpéldány csúszkája, amely a példányokat méretezésre állítja.
3. A méretezhető szerepkör példányai.

A méretezési beállítások konfigurálása után válassza a **mentés** ikont a tetején.



