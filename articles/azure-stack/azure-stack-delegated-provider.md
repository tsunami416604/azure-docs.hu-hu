---
title: Ajánlat delegálása az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan helyezze más személyek felelős az ajánlatok létrehozásához, és regisztrál a felhasználók az Ön számára.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: 161a17f2360767dacc4f418a078c695d7cb8daa7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449749"
---
# <a name="delegate-offers-in-azure-stack"></a>Ajánlat delegálása az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack-operátorokról, milyen gyakran szeretné ajánlatok létrehozásához, és regisztráló felhasználók felelős mások. Például ha Ön a szolgáltató, érdemes lehet a viszonteladók számára a felhasználók regisztrálása és kezelheti azokat az Ön nevében is. Vagy, ha Ön egy vállalat egy központi informatikai csoport része, érdemes a felhasználói bejelentkezési akár delegálása más informatikai munkatársakkal.

Delegálás megkönnyíti a éri el és több felhasználót, ezt úgy teheti meg saját maga felügyeli. Az alábbi ábrán látható egy szintjének összecsukása delegálás, de az Azure Stack egynél több szintet támogat. Delegált szolgáltatók (DPs) is engedélyezheti – delegálja más szolgáltatók esetén legfeljebb öt szintnél.

![Delegálás szintjei](media/azure-stack-delegated-provider/image1.png)

## <a name="understand-delegation-roles-and-steps"></a>Delegálás szerepkörök és lépéseket ismertetése

### <a name="delegation-roles"></a>Delegálás szerepkörök

A következő szerepkörök delegálása részét képezik:

* A *Azure Stack-operátorokról* kezeli az Azure Stack-infrastruktúra és a egy ajánlat sablont hoz létre. Az operátor másokat, hogy azok a felhasználók rendelkezésére ajánlatok irányelvmodulnak delegálja.

* A delegált Azure Stack-operátorok nevezzük *delegált szolgáltatók*. Más szervezetek, például az Azure Active Directory (Azure AD) mások is tartoznak.

* *Felhasználók* iratkozzon fel az ajánlatok, és alkalmazza őket a számítási feladatok létrehozása a virtuális gépek, az adattárolás, kezelése és így tovább.

### <a name="delegation-steps"></a>Delegálás lépések

Delegálás beállítása két alapvető lépésből áll:

1. *Hozzon létre egy meghatalmazott szolgáltatót* előfizetés egy ajánlatra, amely csak rendelkezik az előfizetés szolgáltatás-terv alapján a felhasználó által. Felhasználók, akik előfizetnek az ajánlatra ezután ajánlatok kiterjesztése, és az ajánlatok felhasználókat regisztrálhat.

1. *Ajánlat delegálása a delegált szolgáltató*. Ajánlatunkat egy sablont a Mi a delegált szolgáltatói kínálnak. A delegált szolgáltatói mostantól az ajánlat igénybe vehet, és felkínálható más felhasználók számára.

A következő ábrán látható a delegálás beállításának lépéseit.

![A delegált szolgáltatói létrehozása, és lehetővé teszi számukra a felhasználói regisztrációhoz](media/azure-stack-delegated-provider/image2.png)

**Delegált szolgáltatói követelményei**

Delegált szolgáltatói funkcionálni, a felhasználónak kell kapcsolatot hozhat létre a fő szolgáltatónál hozzon létre egy előfizetést. Ebben az előfizetésben a delegált szolgáltató nevében fő szolgáltató jelenlegi ajánlatok joggal rendelkező azonosítja.

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
   >Fontos megérteni, hogy eltérően az Azure Stack operátorait delegált szolgáltatói nem hozhat létre egy ajánlatot az alapcsomagok és kiegészítő csomagok. Delegált szolgáltatók csak válassza ki azokat a delegált ajánlatok, azokat nem lehet módosítani azokat az ajánlatokat.

1. Győződjön meg arról, az ajánlat nyilvános kiválasztásával **Tallózás**, majd **kínál**. Az ajánlat, majd válassza ki és **állapot módosítása**.

1. A delegált szolgáltatói elérhetővé teszi ezeket az ajánlatokat saját portálon keresztül URL-CÍMÉT. Ezek az ajánlatok már csak a delegált portálon jelennek meg. Keresse meg, és módosítsa az URL-cím:

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

## <a name="multiple-tier-delegation"></a>Többszintű delegálás

Többszintű delegálás delegálása más entitásokra vonatkozó ajánlatot a delegált szolgáltatói lehetővé teszi. Például a mélyebb viszonteladói létrehozása csatornák where:

* Az Azure Stack kezelő szolgáltatót a terjesztőhöz ajánlat irányelvmodulnak delegálja.
* A viszonteladó a terjesztőn delegáltakat.

Több rétegből álló ajánlat delegálás létrehozásához, a delegált szolgáltatói az ajánlat a következő szolgáltató az irányelvmodulnak delegálja. A folyamat megegyezik a delegált szolgáltatói számára az Azure Stack-operátorokról volt. További információkért lásd: [Azure Stack-operátorokról hoz létre a delegált ajánlatot](#cloud-operator-creates-the-delegated-offer).

## <a name="next-steps"></a>További lépések

[Virtuális gép létrehozása](azure-stack-provision-vm.md)