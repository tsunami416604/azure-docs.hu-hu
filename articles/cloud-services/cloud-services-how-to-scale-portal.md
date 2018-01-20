---
title: "Automatikus méretezése a felhőszolgáltatást a portálon |} Microsoft Docs"
description: "Megtudhatja, hogyan használhatja a portált egy felhőalapú szolgáltatás webes szerepkör vagy a feldolgozói szerepkör automatikus skálázási szabályok konfigurálása az Azure-ban."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 701d4404-5cc0-454b-999c-feb94c1685c0
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 264f68b879653f4168637e19cc886524c4af8557
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Az automatikus skálázás egy felhőalapú szolgáltatás, a portál konfigurálása

Egy felhőalapú szolgáltatás feldolgozói szerepkör terjedő skálán bejövő vagy kimenő műveletet kiváltó feltételek állíthat be. A feltételek a szerepkör a Processzor, lemez vagy a szerepkör a hálózati terhelést alapulhatnak. Beállíthat egy feltételt, egy üzenet-várólista vagy valamilyen más Azure-erőforrás az Ön előfizetéséhez rendelve metrikája alapján is.

> [!NOTE]
> Ez a cikk a felhőalapú szolgáltatás webes és feldolgozói szerepkörök összpontosít. A virtuális gép (klasszikus) közvetlenül létrehozásakor tárolja egy felhőalapú szolgáltatás. A normál virtuális gépek méretezheti társítja azt egy [rendelkezésre állási csoport](../virtual-machines/windows/classic/configure-availability-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) és manuálisan kapcsolja be és ki.

## <a name="considerations"></a>Megfontolandó szempontok
Az alkalmazás skálázás konfigurálása előtt gondolja át a következő információkat:

* Skálázás alapvető használati van hatással.

    Nagyobb szerepkörpéldányokat több magot használja. Csak a vonatkozó felső korlátját: belül az előfizetéshez tartozó méretezheti. Tegyük fel, hogy előfizetéséhez maximális hossza 20 magok. Ha egy alkalmazás két közepes méretű felhőszolgáltatások (4 mag összesen) futtatja, csak legfeljebb más felhőalapú szolgáltatás központi az előfizetésben a fennmaradó 16 maggal által. Méretek kapcsolatos további információkért lásd: [Felhőszolgáltatások méretével](cloud-services-sizes-specs.md).

* Méretezheti a várólista állapotüzenet küszöbértéke alapján. Üzenetsorok használatával kapcsolatos további információkért lásd: [használata a Queue Storage szolgáltatás](../storage/queues/storage-dotnet-how-to-use-queues.md).

* További erőforrások, az Ön előfizetéséhez rendelve is méretezheti.

* Ahhoz, hogy az alkalmazás magas rendelkezésre állású, akkor győződjön meg arról, hogy két vagy több szerepkör osztályt telepítették. További információkért lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).

* Automatikus méretezési csak akkor fordul elő, ha a szerepkörök **készen** állapotát.  


## <a name="where-scale-is-located"></a>Ahol a méretezési
Miután kiválasztotta a felhőalapú szolgáltatás, rendelkeznie kell a cloud service panelen látható.

1. A felhőalapú szolgáltatás panelen a a **szerepkörök és példányok** csempére, válassza ki a felhőalapú szolgáltatás nevét.   
   **FONTOS**: Ügyeljen arra, hogy a felhő szerepkör-szolgáltatás, nem a példányon, amely alatt a szerepkör kattintson.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Válassza ki a **méretezési** csempére.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatikus méretezése
A szerepkörhöz tartozó skálázási beállítások vagy két mód konfigurálható **manuális** vagy **automatikus**. Manuális módon teheti meg, beállíthatja a példányainak abszolút számát. Automatikus azonban lehetővé teszi, hogy a készlet meghatározó szabályok és hogyan milyen sokkal meg kell méretezni.

Állítsa be a **szerint** lehetőséggel **ütemezés és a teljesítmény szabályok**.

![Cloud services skálázási beállításokat a profil és a szabály](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Egy meglévő profilt.
2. Vegye fel a szülő profil szabályt.
3. Egy másik profil hozzáadásához.

Válassza ki **profil hozzáadása**. A profil határozza meg, melyik skála használni kívánt módot: **mindig**, **ismétlődési**, **rögzített dátum**.

Miután konfigurálta a profil és a szabályokat, válassza ki a **mentése** ikonra a bal felső.

#### <a name="profile"></a>Profil
A minimális és maximális példányainak a méretezési készlet, és emellett Ha a tartomány active.

* **Mindig**

    Ez a tartomány elérhető példányok mindig készüljön.  

    ![A felhőalapú szolgáltatás, amely mindig](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Recurrence**

    Válasszon olyan napokat a hét méretezése.

    ![Cloud service méretezéssel ismétlődési ütemezés](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Rögzített dátum**

    Rögzített dátumtartomány méretezése a szerepkört.

    ![CLoud service méretezési fix dátummal](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Miután konfigurálta a profil, válassza ki a **OK** gombra a profil panel alján.

#### <a name="rule"></a>Szabály
Szabályok profil adnak, és megfelelnek a skála kiváltó feltétel.

A szabály eseményindító a felhőszolgáltatás (CPU-használat, lemezre tevékenységi vagy hálózati tevékenységek), amelyhez egy feltételes értéket adhat hozzá metrika alapul. Az üzenet-várólista vagy valamilyen más Azure-erőforrás az Ön előfizetéséhez rendelve metrikája alapján eseményindító továbbá akkor is.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Miután konfigurálta a szabályt, válassza ki a **OK** gombra a szabály panel alján.

## <a name="back-to-manual-scale"></a>Vissza a manuális méretezési
Keresse meg a [méretezési beállításainak](#where-scale-is-located) és állítsa be a **szerint** lehetőséggel **manuálisan megadott példányszám**.

![Cloud services skálázási beállításokat a profil és a szabály](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Ez a beállítás eltávolítja az automatikus skálázás a szerepkörből, és állíthat be a példányszám közvetlenül.

1. A skála (Manuális vagy automatikus) lehetőséget.
2. Egy szerepkör példány csúszkát beállítása a példányok számára.
3. Méretezhető, hogy a szerepkör példánya.

Miután konfigurálta a skálázási beállításokat, válassza ki a **mentése** ikonra a bal felső.
