---
title: "Automatikus méretezése a felhőszolgáltatást a klasszikus portálon |} Microsoft Docs"
description: "(klasszikus) Megtudhatja, hogyan használja a klasszikus portált automatikus skálázási szabályok felhő szerepkör-szolgáltatás webes vagy feldolgozói szerepkör konfigurálása az Azure-ban."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: eb167d70-4eba-42a4-b157-d8d0688abf4b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 90d55bbac6e113d6add848ace67cf0749e26342b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-classic-portal"></a>Az automatikus skálázás egy felhőalapú szolgáltatás, a klasszikus portál konfigurálása
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-how-to-scale-portal.md)
> * [klasszikus Azure portál](cloud-services-how-to-scale.md)

A klasszikus Azure portálon méretezési oldalán konfigurálhatja a webes vagy feldolgozói szerepkör automatikus méretezési beállításait. Beállíthatja azt is megteheti, manuális skálázás automatikus skálázás szabályalapú helyett.

> [!NOTE]
> Ez a cikk a felhőalapú szolgáltatás webes és feldolgozói szerepkörök összpontosít. A virtuális gép (klasszikus) közvetlenül létrehozásakor tárolja egy felhőalapú szolgáltatás. Ezen információk némelyikét a virtuális gépek az ilyen típusú vonatkozik. Skálázás egy rendelkezésre állási csoportot a virtuális gépek csak leáll őket be- és kikapcsolását a skálázási szabályok konfigurálása. További információ a virtuális gépek és a rendelkezésre állási csoportok: [a virtuális gépek rendelkezésre állásának kezelése](../virtual-machines/windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

Az alkalmazás skálázás konfigurálása előtt gondolja át a következő információkat:

* Skálázás alapvető használati van hatással.

    Nagyobb szerepkörpéldányokat több magot használja. Csak a vonatkozó felső korlátját: belül az előfizetéshez tartozó méretezheti. Tegyük fel, hogy előfizetéséhez maximális hossza 20 magok. Ha egy alkalmazás két közepes méretű felhőszolgáltatások (4 mag összesen) futtatja, csak legfeljebb más felhőalapú szolgáltatás központi az előfizetésben a fennmaradó 16 maggal által. Méretek kapcsolatos további információkért lásd: [Felhőszolgáltatások méretével](cloud-services-sizes-specs.md).

* Létrehoz egy sort kell, és társíthatja egy szerepkör előtt az alkalmazást egy állapotüzenet küszöbértéke a alapján méretezheti. További információkért lásd: [használata a Queue Storage szolgáltatás](../storage/queues/storage-dotnet-how-to-use-queues.md).

* A felhőalapú szolgáltatáshoz kapcsolódó erőforrások méretezheti. Erőforrások csatolása kapcsolatos további információkért lásd: [hogyan: erőforrás összekapcsolása egy felhőalapú szolgáltatás](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

* Ahhoz, hogy az alkalmazás magas rendelkezésre állású, akkor győződjön meg arról, hogy két vagy több szerepkör osztályt telepítették. További információkért lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).

## <a name="schedule-scaling"></a>Ütemezés skálázás
Alapértelmezés szerint az összes szerepkör ne hajtsa végre egy adott ütemezés. Ezért szereplő beállítások megváltozott vonatkoznak a minden alkalommal, és minden nap a év során. Ha azt szeretné, beállíthatja manuális vagy automatikus skálázás az alábbi mód:

* Hétköznapokon
* Hétvégéket
* Hét éjszakák
* Hét reggelente
* Konkrét dátumokat
* Adott dátumtartományok

Az ütemezési beállítás konfigurálva van a [a klasszikus Azure portálon](https://manage.windowsazure.com/) meg a  
**A felhőalapú szolgáltatások** > **\[a felhőalapú szolgáltatás\]** > **méretezési** > **\[üzemi és átmeneti\]**  lap.

Kattintson a **ütemezés beállítása** gomb az egyes szerepkörökhöz módosítani kívánja.

![A felhőalapú szolgáltatás ütemezés alapján automatikus skálázás][scale_schedules]

## <a name="manual-scale"></a>Manuális méretezési
Az a **méretezési** lapon manuálisan megnövelhető, vagy egy felhőalapú szolgáltatásban futó példányok számának csökkentéséhez. Ez a beállítás minden létrehozott ütemezést, vagy minden alkalommal, ha nem hozott létre ütemezés.

1. A a [a klasszikus Azure portálon](https://manage.windowsazure.com/), kattintson a **Felhőszolgáltatások**, majd kattintson a nevére, a felhőalapú szolgáltatás, az irányítópult megnyitásához.
   
   > [!TIP]
   > Ha nem látja a felhőalapú szolgáltatás, előfordulhat, hogy módosítani szeretné a **éles** való **átmeneti** vagy fordítva.

2. Kattintson a **méretezési**.
3. Válassza ki a kívánt méretezési beállításainak módosítása ütemezést. *Nem ütemezett időpontokban* az alapértelmezett beállítás, ha még nem definiált ütemezések.
4. Keresés a **a metrika a skála** válassza ki azt **NONE**. Ez a beállítás az alapértelmezés az összes szerepkör.
5. A felhőalapú szolgáltatás minden szerepkörhöz használandó példányok számának módosítása a csúszkán.
   
    ![Manuálisan méretezhető, a felhő szerepkör-szolgáltatás][manual_scale]
   
    Ha több példány van szüksége, előfordulhat, hogy módosítani szeretné a [a felhő virtuálisgép-méret](cloud-services-sizes-specs.md).
6. Kattintson a **Save** (Mentés) gombra.  
   Szerepkörpéldányokat hozzáadásakor vagy eltávolításakor a kiválasztott beállítások alapján.

> [!TIP]
> Amikor látja ![][tip_icon] az egér mozgatásával és kaphat arról, hogy milyen egy adott beállítás.

## <a name="automatic-scale---cpu"></a>Automatikus méretezési - Processzor
Ebben a módban arányosan, ha a CPU-használat átlagos aránya a megadott küszöbérték alatti vagy feletti kerül. Szerepkörpéldányokat létrehozott vagy törölt, ha ez történik.

1. A a [a klasszikus Azure portálon](https://manage.windowsazure.com/), kattintson a **Felhőszolgáltatások**, majd kattintson a nevére, a felhőalapú szolgáltatás, az irányítópult megnyitásához.
   
   > [!TIP]
   > Ha nem látja a felhőalapú szolgáltatás, előfordulhat, hogy módosítani szeretné a **éles** való **átmeneti** vagy fordítva.

2. Kattintson a **méretezési**.
3. Válassza ki a kívánt méretezési beállításainak módosítása ütemezést. *Nem ütemezett időpontokban* az alapértelmezett beállítás, ha még nem definiált ütemezések.
4. Keresés a **a metrika a skála** válassza ki azt **CPU**.
5. Most egy minimális és maximális számos szerepkörök példányok, a cél CPU-használat (való terjedő skálán be), és felfelé és lefelé méretezési szerint hány példányban is konfigurálhat.

![Bővítse a cpu-terhelés a felhőalapú szolgáltatás szerepkör][cpu_scale]

> [!TIP]
> Amikor látja ![][tip_icon] az egér mozgatásával és kaphat arról, hogy milyen egy adott beállítás.

## <a name="automatic-scale---queue"></a>Automatikus méretezési - várólista
Ebben a módban automatikusan méretezi, ha a várólistán lévő üzenetek száma a megadott küszöbérték alatti vagy feletti kerül. Szerepkörpéldányokat létrehozott vagy törölt, ha ez történik.

1. A a [a klasszikus Azure portálon](https://manage.windowsazure.com/), kattintson a **Felhőszolgáltatások**, majd kattintson a nevére, a felhőalapú szolgáltatás, az irányítópult megnyitásához.
   
   > [!TIP]
   > Ha nem látja a felhőalapú szolgáltatás, előfordulhat, hogy módosítani szeretné a **éles** való **átmeneti** vagy fordítva.

2. Kattintson a **méretezési**.
3. Keresés a **a metrika a skála** válassza ki azt **VÁRÓLISTA**.
4. Most egy minimális és maximális tartományt szerepkörök példányok, a várólista és minden példányt, és hány példányban felfelé és lefelé méretezési által feldolgozandó üzenetek száma is konfigurálhat.

![Bővítse a felhőalapú szolgáltatás szerepkör üzenet-várólista][queue_scale]

> [!TIP]
> Amikor látja ![][tip_icon] az egér mozgatásával és kaphat arról, hogy milyen egy adott beállítás.

## <a name="scale-linked-resources"></a>Méretezhető kapcsolt erőforrásokban
Gyakran, ha egy szerepkör méretezni, célszerű az alkalmazás által is használt adatbázis méretezése. Ha az adatbázis csatolása a felhőalapú szolgáltatás, a megfelelő hivatkozásra kattintva hozzáférhet a skálázási beállításokat az adott erőforráshoz.

1. A a [a klasszikus Azure portálon](https://manage.windowsazure.com/), kattintson a **Felhőszolgáltatások**, majd kattintson a nevére, a felhőalapú szolgáltatás, az irányítópult megnyitásához.
   
   > [!TIP]
   > Ha nem látja a felhőalapú szolgáltatás, előfordulhat, hogy módosítani szeretné a **éles** való **átmeneti** vagy fordítva.

2. Kattintson a **méretezési**.
3. Keresés a **kapcsolódó erőforrások** szakaszt, és kattintson **az adatbázis kezelésére**.
   
   > [!NOTE]
   > Ha nem látja a **kapcsolódó erőforrások** szakaszban, valószínűleg nem rendelkezik társított erőforrásokat.

![][linked_resource]

[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
