---
title: Ajánlat delegálása az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan helyezze más személyek felelős az ajánlatok létrehozásához, és regisztrál a felhasználók az Ön számára.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: 112586d3ee5f49eab9adb72d41a210e2dd9828d8
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055096"
---
# <a name="delegate-offers-in-azure-stack"></a>Ajánlat delegálása az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack-operátorokról, milyen gyakran szeretné mások regisztráló felhasználók és előfizetések létrehozása. Például ha Ön a szolgáltató, érdemes lehet a viszonteladók számára a felhasználók regisztrálása és kezelheti azokat az Ön nevében is. Vagy, ha Ön egy vállalat egy központi informatikai csoport része, érdemes a felhasználói bejelentkezési akár delegálása más informatikai munkatársakkal.

Delegálás megkönnyíti a érhet el és kezelheti a több felhasználót, ezt úgy teheti meg saját maga is az alábbi ábrán látható módon. 

![Delegálás szintjei](media/azure-stack-delegated-provider/image1.png)

Delegálás a delegált szolgáltatói kezeli a-ajánlat (delegált ajánlat), és a végfelhasználók számára, hogy az ajánlat bevonása nélkül-előfizetések szerzi be a rendszergazda. 

## <a name="understand-delegation-roles-and-steps"></a>Delegálás szerepkörök és lépéseket ismertetése

### <a name="delegation-roles"></a>Delegálás szerepkörök

A következő szerepkörök delegálása részét képezik:

* A *Azure Stack-operátorokról* kezeli az Azure Stack-infrastruktúra és a egy ajánlat sablont hoz létre. Az operátor másokat, hogy a bérlő ajánlatok biztosítása irányelvmodulnak delegálja.

* A felhasználók közül ez a delegált Azure Stack-operátorok *tulajdonosa* vagy *közreműködői* előfizetésekben rights nevű *delegált szolgáltatók*. Más szervezetek számára, mint a többi Azure Active Directory (Azure AD) bérlő is tartoznak.

* *Felhasználók* iratkozzon fel az ajánlatok, és alkalmazza őket a számítási feladatok létrehozása a virtuális gépek, az adattárolás, kezelése és így tovább.

### <a name="delegation-steps"></a>Delegálás lépések

Delegálás beállítása két alapvető lépésből áll:

1. *Delegált szolgáltatói előfizetés létrehozása* a felhasználót, hogy az ajánlat csak az előfizetések szolgáltatást tartalmazó előfizetéssel. Felhasználók, akik előfizetnek az ajánlatra majd kiterjesztheti a delegált ajánlatok más felhasználók számára szolgáltatásra azokat az ajánlatokat aláírásával.

2. *Ajánlat delegálása a delegált szolgáltató*. Ez az ajánlat lehetővé teszi, hogy a delegált szolgáltatói előfizetések létrehozása vagy bővítése az ajánlatot azok a felhasználók számára. A delegált szolgáltatói mostantól az ajánlat igénybe vehet, és felkínálható más felhasználók számára.

A következő ábrán látható a delegálás beállításának lépéseit.

![A delegált szolgáltatói létrehozása, és lehetővé teszi számukra a felhasználói regisztrációhoz](media/azure-stack-delegated-provider/image2.png)

**Delegált szolgáltatói követelményei**

Delegált szolgáltatói funkcionálni, a felhasználónak kell kapcsolatot hozhat létre a fő szolgáltatónál hozzon létre egy előfizetést. Ehhez az előfizetéshez, hogy a jogot arra, hogy a delegált ajánlatokkal nevében fő szolgáltató jelenleg a delegált szolgáltatói azonosítja.

Ez a kapcsolat létrejötte után az Azure Stack-operátorokról is engedélyezheti – delegálja az ajánlatot a delegált szolgáltatói. A delegált szolgáltatói is az ajánlat igénybe, nevezze át a következőre (de nem módosíthatja az anyag), és felkínálható az ügyfeleinek.

## <a name="delegation-walkthrough"></a>Delegálás forgatókönyv

A következő szakaszok a gyakorlati útmutató delegált szolgáltatói beállítása, az ajánlat delegálása és annak ellenőrzésére, hogy felhasználók regisztrálhatnak a delegált ajánlatot.

### <a name="set-up-roles"></a>Szerepkörök beállítása

Ez az útmutató használatához két Azure AD-fiókokat az Azure Stack operátori fiókon kívül kell. Ha ez a két fiók nem rendelkezik, meg kell létrehoznia őket. A fiókok Azure AD-felhasználók is tartozhat, és nevezzük a delegált szolgáltatói és a felhasználó.

| **Szerepkör** | **Szervezeti jogok** |
| --- | --- |
| Delegált szolgáltatói |Felhasználó |
| Felhasználó |Felhasználó |

### <a name="identify-the-delegated-provider"></a>A delegált szolgáltatói azonosítása

1. Jelentkezzen be az Azure Stack operátorait szerint a felügyeleti portálon.

1. Hozhat létre, amely lehetővé teszi a felhasználók, delegált szolgáltatói válik ajánlatot:

   a.  [Hozzon létre egy csomagot](azure-stack-create-plan.md).
       Ez a csomag csak az előfizetések szolgáltatás tartalmaznia kell. Ebben a cikkben egy nevű csomag **PlanForDelegation** példaként.

   b.  [Hozzon létre egy ajánlatot](azure-stack-create-offer.md) a terv alapján. Ebben a cikkben egy ajánlatot nevű **OfferToDP** példaként.

   c.  Adja hozzá a delegált szolgáltatói előfizetőként ehhez az ajánlathoz kiválasztásával **előfizetések** > **Hozzáadás** > **új bérlői előfizetéshez**.

   ![Adja hozzá a delegált szolgáltatói előfizetőként](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Hogy az összes Azure Stack ajánlatokhoz a lehetőség az, hogy az ajánlat nyilvános, és hogy a felhasználók regisztrálhatnak azt, vagy privát tartja, és ami lehetővé teszi az előfizetés kezelése az Azure Stack-operátorokról. Delegált szolgáltatók rendszerint egy kisebb csoportot. Szabályozza, ki, így a legtöbb esetben logikus tartja az ajánlat privát engednek szeretné.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Az Azure Stack-operátorokról hoz létre a delegált ajánlatot

A következő lépés, ha a csomag és ajánlat, amely delegálni fog, és hogy a felhasználók használni fogják. Célszerű az ajánlat definiálása, pontosan, ahogy látja, mivel a delegált szolgáltatói nem lehet módosítani a tervek és kvóták tartalmazza a felhasználók.

1. Az Azure Stack operátorait szerint [hozzon létre egy csomagot](azure-stack-create-plan.md) és [ajánlat](azure-stack-create-offer.md) a terv alapján. Ebben a cikkben egy ajánlatot nevű **DelegatedOffer** példaként.

   > [!NOTE]
   > Ez az ajánlat nem kell lennie a nyilvános, de azt teheti, hogy nyilvános Ha szeretné. Azonban a legtöbb esetben célszerű csak akkor hozzáférjenek az ajánlatot a delegált szolgáltatók. Miután egy privát ajánlat delegálása, a következő lépésben ismertetett módon, a delegált szolgáltatói van hozzáférése hozzájuk.

1. Delegálni az ajánlatot. Lépjen a **DelegatedOffer**. A **beállítások**válassza **delegált szolgáltatók** > **Hozzáadás**.

1. A delegált szolgáltatói előfizetés kiválasztása a legördülő listából, majd **delegált**.

   ![Delegált szolgáltató hozzáadása](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Delegált szolgáltatói testreszabja az ajánlatot

Jelentkezzen be a felhasználói portál átadott szolgáltatója, és majd hozzon létre egy új ajánlatot a delegált ajánlatot sablonként való használatával.

1. Válassza ki **új** > **bérlői kínál + csomagok** > **ajánlat**.

    ![Új ajánlat létrehozása](media/azure-stack-delegated-provider/image5.png)

1. Rendeljen egy nevet az ajánlatra. Ez a cikk **ResellerOffer** példaként. Válassza ki, amelyen a alapjaként, majd válassza a delegált ajánlatot **létrehozás**.

   ![Rendeljen egy nevet](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Fontos tudni, hogy delegált szolgáltatók csak választhatja a számukra delegált ajánlatokkal. Azokat az ajánlatokat, akkor nem végezhet módosításokat. Csak az Azure Stack operátorait módosíthatja ezekhez az ajánlatokhoz, például a tervek és kvóták, módosítása. Delegált szolgáltatói nem hozhatnak létre egy ajánlatot az alapcsomagok és kiegészítő csomagok. 

3. A delegált szolgáltatói teheti ezeket az ajánlatokat saját portálon keresztül nyilvános URL-CÍMÉT. Ahhoz, hogy az ajánlat nyilvános, válassza ki a **Tallózás**, majd **kínál**. Az ajánlat, majd válassza ki és **állapot módosítása**.

4. A nyilvános delegált ajánlatokkal. most már csak a delegált portálon jelennek meg. Keresse meg, és módosítsa az URL-cím:

    a.  Válassza ki **Tallózás** > **további szolgáltatások** > **előfizetések**. Ezután válassza ki a delegált szolgáltatói előfizetés. Ha például **DPSubscription** > **tulajdonságok**.

    b.  Másolja a portál URL-címet egy külön helyen, például a Jegyzettömbben.

    ![A delegált szolgáltatói előfizetés kiválasztása](media/azure-stack-delegated-provider/dpportaluri.png)  

   A delegált ajánlatot a delegált szolgáltatói létrehozása befejeződött. Jelentkezzen ki, mint a delegált szolgáltatói, és zárja be a böngészőablakot használ.

### <a name="sign-up-for-the-offer"></a>Iratkozzon fel az ajánlatra

1. Egy új böngészőablakban nyissa meg a delegált portált, amely az előző lépésben mentett URL-CÍMÉT. Jelentkezzen be a portálra felhasználóként.

   >[!NOTE]
   >A delegált ajánlatokkal, kivéve, ha a delegált a portálon nem láthatók.

1. Az irányítópulton, válassza ki a **előfizetés beszerzése**. Látni fogja, hogy csak a delegált ajánlatokkal. a delegált szolgáltató által létrehozott jelennek meg a felhasználó számára.

   ![Megtekintheti, és a kívánt ajánlatok kiválasztása](media/azure-stack-delegated-provider/image8.png)

Ajánlat delegálása, a folyamat befejeződött. Most egy felhasználó regisztrálhat Ez az ajánlat hozzá tartozó előfizetés lekérésével.

## <a name="move-subscriptions-between-delegated-providers"></a>Áthelyezése előfizetések között a delegált szolgáltatók

Ha szükséges, egy előfizetés áthelyezhetők Directory ugyanazt bérlőhöz tartozó új vagy meglévő delegált szolgáltatói előfizetések között. Ez a PowerShell-parancsmag használatával [Move-AzsSubscription](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin).

Ez a következő esetekben hasznos:
- Készítse elő egy új csoport egy tagja, és a delegált szolgáltatói szerepkör a hozzárendelni kívánt, a csapat tagja felhasználói-előfizetések az alapértelmezett szolgáltatója előfizetés korábban létrehozott.
- Az azonos Directory-bérlő (Azure Active Directory) delegált szolgáltatók több előfizetéssel rendelkezik, és felhasználói-előfizetések mozoghat azok között. Ez lehet a esetben, ha egy csapat egyik tagja áthelyezi a csapatok között osztható ki új kell előfizetésüket.


## <a name="next-steps"></a>További lépések

[Virtuális gép létrehozása](azure-stack-provision-vm.md)