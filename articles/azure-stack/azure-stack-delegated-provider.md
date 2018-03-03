---
title: "Azure-készletben ajánlatok delegálása |} Microsoft Docs"
description: "Megtudhatja, hogyan helyezze más személyek ajánlatok létrehozása és a felhasználók regisztráljanak az Ön feladata."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: 287bc04660664facbe99d2cb80ae6c92e41c4111
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="delegate-offers-in-azure-stack"></a>Ajánlat delegálása az Azure Stackben

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure-verem operátorként érdemes, amelyre az ajánlatok létrehozása és a felhasználók regisztráljanak mások. Például ha egy szolgáltató, érdemes viszonteladók ügyfél regisztrálhat, és kezelheti azokat az Ön nevében. Vagy ha a vállalat egy központi informatikai csoport része, érdemes lehet a leányvállalatai a beavatkozás nélküli felhasználók regisztrálni.

Delegálás segít a feladatok éri el és felügyeli a felhasználók több, mint közvetlenül lehetővé teszi. Az alábbi ábrán a delegálás egy szinttel, de Azure verem több szintet támogat. Delegált szolgáltatók (terjesztési pontok) pedig delegálhatja más szolgáltatók legfeljebb öt szintnél.

![A delegálás szintek](media/azure-stack-delegated-provider/image1.png)

Azure verem operátorok delegálhatja létrehozása ajánlatok és a felhasználók más felhasználók számára a delegálás funkcióinak használatával.

## <a name="roles-and-steps-in-delegation"></a>Szerepkörök és a delegálás lépései
Delegálás megértéséhez, vegye figyelembe, hogy három szerepkörök pillanatképkészlethez állnak rendelkezésre:

* A *Azure verem operátor* kezeli az Azure-verem infrastruktúra, egy ajánlat sablont hoz létre, és delegálja másokkal, azok a felhasználók számára biztosítani.

* Az átadott Azure verem operátorok nevezzük *szolgáltatókat megszereznie*. Egyéb szervezetekhez, például az egyéb Azure Active Directory (Azure AD) felhasználó is tartoznak.

* *Felhasználók* iratkozzon fel az ajánlatok és használhatja őket a munkaterhelések létrehozása a virtuális gépek, az adattárolás, kezelése és így tovább.

Az alábbi képen látható, két lépésben történik delegálás beállítása.

1. *Azonosítsa a delegált szolgáltatók*. Ezt megteheti egy ajánlatot csak az előfizetések szolgáltatást tartalmazó terv alapján történő feliratkozással. Ez az ajánlat előfizetni felhasználók szerezzen be néhány az Azure-verem operátor képessége, beleértve a ajánlatok kiterjesztése, valamint feliratkozott azokat a felhasználókat.

2. *A feladat egy ajánlatot a delegált szolgáltató*. Ezt az ajánlatot a delegált szolgáltató kínálhat sablonját funkcionál. A delegált szolgáltató is igénybe vehet a ajánlat most. Válassza ki azt a nevet (de ne módosítsa a szolgáltatások és kvóták), és nyújtsanak az ügyfeleknek.

![Hozza létre a delegált szolgáltatót, és lehetővé teszi a felhasználók feliratkozáshoz](media/azure-stack-delegated-provider/image2.png)

Delegált szolgáltatók összekötőként, a felhasználóknak meg kell kapcsolatot hozhat létre a fő szolgáltatónál. Más szóval szükségük előfizetés létrehozása. Ebben a forgatókönyvben az előfizetés, a fő szolgáltató nevében található ajánlatok joggal rendelkező delegált szolgáltatók azonosítja.

Ez a kapcsolat létrejötte után az Azure-verem operátor delegálhatja ajánlatot a delegált szolgáltató. A delegált szolgáltató most vonatkozó ajánlatot tenni, nevezni (de nem módosíthatja a tartalmát), és nyújtsanak az ügyfeleinek.

Az alábbi szakaszok azt ismertetik, hogyan arra, hogy egy delegált szolgáltató delegálása ajánlatot, és győződjön meg arról, hogy felhasználók iratkozhatnak fel azt.

## <a name="set-up-roles"></a>Szerepkörök beállítása

Munkahelyi delegált szolgáltató megtekintéséhez szükséges további Azure AD-fiókok Azure verem operátor fiókja mellett. Ha ez a két fiók nem rendelkezik, hozzon létre őket. A fiókok Azure AD-felhasználó is tartozik, és nevezzük a delegált szolgáltató és a felhasználó.

| **Szerepkör** | **Szervezeti jogok** |
| --- | --- |
| Delegált szolgáltató |Felhasználó |
| Felhasználó |Felhasználó |

## <a name="identify-the-delegated-providers"></a>A delegált szolgáltatók azonosítása
1. Jelentkezzen be Azure verem kezelőként.

2. Az ajánlat, amely lehetővé teszi a felhasználók számára válik delegált szolgáltatók létrehozása:
   
   a.  [Hozzon létre egy csomagot](azure-stack-create-plan.md).
       A tervnek mindenképp tartalmaznia kell csak az előfizetések szolgáltatást. Ebben a cikkben egy nevű díjcsomagot az **PlanForDelegation**.
   
   b.  [Hozzon létre egy ajánlatot](azure-stack-create-offer.md) a terv alapján. Ebben a cikkben az ajánlatot nevű **OfferToDP**.
   
   c.  Az ajánlat létrehozásának befejezése után a meghatalmazott-szolgáltató felvétele előfizetőként az ajánlat. Ehhez jelölje **előfizetések** > **Hozzáadás** > **új bérlő előfizetés**.
   
   ![A delegált szolgáltató előfizetőként hozzáadása](media/azure-stack-delegated-provider/image3.png)

> [!NOTE]
> Az összes Azure verem ajánlatokat, ha van a beállítást, amellyel nyilvános és lehetővé teszi a felhasználók az ajánlatot iratkozzon fel, vagy titkos tartása, és ha engedélyezi, hogy az Azure-verem operátor kezelése a regisztrációs. Delegált szolgáltatók rendszerint egy kis csoport. Azt szeretné, hogy ki van bevezetett, így a legtöbb esetben ez az ajánlat titkos tartása teljesen logikus.
> 
> 

## <a name="azure-stack-operator-creates-the-delegated-offer"></a>Az Azure verem operátor hoz létre delegált vonatkozó ajánlatot

A delegált szolgáltató most hozott létre. A következő lépés, ha a terv és az ajánlat, amely delegálni fog, és amelyek az ügyfelek. Célszerű határozza meg az ajánlat kívánt a felhasználók látható legyen, a meghatalmazott szolgáltató nem módosíthatók, a csomagok és a kvóták tartalmazza.

1. Azure verem kezelőként [hozzon létre egy csomagot](azure-stack-create-plan.md) és [ajánlatot](azure-stack-create-offer.md) alapján. Ebben a cikkben az ajánlatot nevű **DelegatedOffer.**
   
   > [!NOTE]
   > Ez az ajánlat nem kell lennie a nyilvános. Ha úgy dönt, hogy azt nyilvános. A legtöbb esetben azonban csak a szeretné delegált szolgáltatók számára, hogy jogosult az elérésére. A következő lépésekben ismertetett személyes ajánlat delegálását, követően a delegált szolgáltató hozzáfér.
   > 
   > 
1. Az ajánlat delegálni. Ugrás a **DelegatedOffer.** Ezt követően a a **beállítások** ablaktáblán válassza előbb **szolgáltatókat megszereznie** > **Hozzáadás**.

2. A delegált szolgáltató előfizetésének a legördülő listából, majd válassza ki és **delegált**.

> ![A delegált szolgáltató felvétele](media/azure-stack-delegated-provider/image4.png)
> 
> 

## <a name="delegated-provider-customizes-the-offer"></a>Delegált szolgáltató testreszabása vonatkozó ajánlatot

Jelentkezzen be a felhasználói portálra a delegált szolgáltatóként, és ezután hozzon létre egy új ajánlatot a delegált ajánlat egy sablon segítségével.

1. Válassza ki **új** > **bérlői kínál + tervek** > **kínálnak**.

    ![Hozzon létre egy új ajánlatot](media/azure-stack-delegated-provider/image5.png)


1. Adjon nevet az ajánlatra. Ebben a cikkben az **ResellerOffer**. Válassza ki a használandó kiinduló azt, majd válassza ki a delegált ajánlat **létrehozása**.
   
   ![A név hozzárendelése](media/azure-stack-delegated-provider/image6.png)

    >[!NOTE] 
    > Vegye figyelembe a különbség az ajánlat létrehozása, az Azure-verem operátor által tapasztalt képest. A delegált szolgáltató összeállítani az alap tervek és bővítmények tervek ajánlatot. Csak választható ajánlatokat, amelyek rájuk bízott, és nem módosítja azokat az ajánlatokat.

1. Az ajánlat kiválasztásával nyilvánosságra **Tallózás**, majd **kínál**. Az ajánlat, majd válassza ki és **állapotváltoztatási**.

2. Ezekről az ajánlatokról saját portálon keresztül mutatja meg a delegált szolgáltató URL-CÍMÉT. Ezekről az ajánlatokról láthatók csak a delegált portálon keresztül. Található, és módosítsa az URL-címet:
   
    a.  Válassza ki **Tallózás** > **további szolgáltatások** > **előfizetések**. Ezután válassza ki a delegált szolgáltató előfizetést. Például **DPSubscription** > **tulajdonságok**.
   
    b.  Másolja a portál URL-cím egy külön helyre, például a Jegyzettömböt.
   
    ![Válassza ki a delegált szolgáltató előfizetést](media/azure-stack-delegated-provider/dpportaluri.png)  
   
   Most létrehozott egy delegált ajánlat delegált-szolgáltatóként. Jelentkezzen ki a delegált szolgáltatóként. Zárja be a böngészőablakot használja.

## <a name="sign-up-for-the-offer"></a>Az ajánlat regisztráció
1. Egy új böngészőablakot, keresse meg a delegált portál URL-címet, amelyet az előző lépésben mentett. Jelentkezzen be a portálra egy olyan felhasználó nevében. 
   
   >[!NOTE]
   >A delegált ajánlatok nem láthatók, kivéve, ha a meghatalmazott portál. 

2. Az irányítópulton, válassza ki a **egy előfizetés**. Láthatja, hogy csak a delegált ajánlatokat a delegált szolgáltató által létrehozott jelennek meg a felhasználó számára:

> ![Megtekintheti, és válassza ki a ajánlatok](media/azure-stack-delegated-provider/image8.png)
> 
> 

Az ajánlat delegálási folyamat be nem fejeződik. A felhasználó most regisztrálhat az ajánlat által az előfizetés első.

## <a name="multiple-tier-delegation"></a>Többrétegű delegálás

Többrétegű delegálása lehetővé teszi, hogy a delegálása egyéb entitások ajánlatot a delegált szolgáltató. Ez lehetővé teszi, például mélyebb viszonteladóhoz csatornák, amelyben a szolgáltatót, amelyet a Azure verem kezel delegálja a terjesztőhöz ajánlatot létrehozását. A terjesztő, viszonteladók gazdakiszolgálója. Az Azure verem delegálás legfeljebb öt szintet támogat.

Több rétegből álló ajánlat delegálás létrehozásához, a meghatalmazott szolgáltató pedig gazdakiszolgálója ajánlatot a következő szolgáltató. A folyamat megegyezik a delegált szolgáltató, akkor az Azure-verem operátorhoz (lásd: [Azure verem operátor hoz létre a delegált ajánlat](#cloud-operator-creates-the-delegated-offer)).

## <a name="next-steps"></a>További lépések
[A virtuális gép kiépítése](azure-stack-provision-vm.md)

