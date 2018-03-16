---
title: "Hozzon létre egy ajánlatot az Azure piactéren nem technikai jellegű előfeltételei |} Microsoft Docs"
description: "A létrehozása és telepítése egy ajánlatot az Azure piactéren mások beszerzési vonatkozó követelmények megértése érdekében."
services: marketplace-publishing
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: mbaldwin
ms.openlocfilehash: 5c30e62bf345843fe83b3f17b728e1a937d19ce3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Hozzon létre egy ajánlatot az Azure piactéren általános előfeltételei
Az ajánlat létrehozási folyamat során eljutni szükséges általános, üzleti folyamat-központú Előfeltételek megismerésében.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Győződjön meg arról, hogy a Microsoft egy értékesítő néven regisztrált
Részletes információkra van szüksége egy értékesítő fiók regisztrálása a Microsoft, látogasson el [fióklétrehozás és a regisztrációs](marketplace-publishing-accounts-creation-registration.md).

* **Ha a vállalata már regisztrálva van egy értékesítő a fejlesztői központjában, és hozzon létre egy új ajánlatot szeretné** , majd jelentkezzen be a közzétételi portál azonosítójú az e-mailek mely fejlesztői központban való regisztrálása történik. Ez a lépés akkor szükséges, így a fejlesztői központban és a közzétételi portal kapcsolódnak egymáshoz.
* **Ha a vállalata már regisztrálva van egy értékesítő a fejlesztői központjában, és módosítani szeretné egy meglévő ajánlatot** majd vagy a közzétételi bejelentkezési portál, a rendszergazdai fiókkal vagy egy olyan fiókkal, amely a közzétételi egy társadminisztrátor meg van adva portálon. Az alábbi táblázat a lépéseket adhat hozzá társadminisztrátori fiókot.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Lépések végrehajtásával adja hozzá a társadminisztrátornak a közzétételi portálon
Rendszergazdák a közzétételi portal hozzá más tagjai a vállalat számára az alkalmazás dolgozik, a közzétételi lévő co-rendszergazdaként portálon. **Feltételezve, hogy a rendszergazda** az alábbi lépések a hozzáadása egy co-rendszergazda segítségét.

> [!NOTE]
> Az új felhasználók számára, mielőtt hozzáadja a társadminisztrátornak a közzétételi portal, győződjön meg arról, hogy létrejött legalább egy alkalmazást a közzététel a portálon. Erre azért szükség, mint a **KÖZZÉTEVŐK** lap jelenik meg, csak a legalább egy alkalmazás létrehozása a közzététel után portálon.
> 
> 

1. Győződjön meg arról, hogy a társadminisztrátornak e-mail azonosítója egy Microsoft account(MSA). Ha nem, regisztrálja, a felügyelt Szolgáltatásfiók használatát az [hivatkozás](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Győződjön meg arról, hogy van-e a rendszergazdai fiók alatt egy co-rendszergazda hozzáadása előtt legalább egy alkalmazás
3. Miután a fenti lépések befejezése után jelentkezzen be a közzétételt a társadminisztrátornak e-mail azonosítója, és a kimenő napló portált.
4. Most már a közzétételi bejelentkezni a rendszergazda e-mail azonosítójú portál.
5. Navigáljon a közzétevők -> Válassza ki a fiók -> rendszergazdák hozzáadása a társadminisztrátor (az alábbi képernyőfelvételen) ->
   
    ![rajz](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Győződjön meg arról, hogy a különböző szakaszaiban a közzétételi folyamat (pl. fejlesztői központhoz, portal közzététele) a megadott e-mail azonosítók kell-e figyelni a Microsoft bármely kommunikációs.
7. Fejlesztői központ regisztrációs ne egy olyan fiókkal, egyetlen személy társított. Ez a függőség eltávolításához magánszemély javasolt.
8. Ha szembesülhetnek a fejlesztői központban regisztrációs merül fel, majd léptesse elő a jegy használatát az [hivatkozás](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Törli a helyadatok közzétételére egy társadminisztrátor lépéseket portál
**Feltételezve, hogy a rendszergazda** az alábbi lépések a törli a co-rendszergazda segítségét.

1. Jelentkezzen be a közzétételt a rendszergazda e-mail azonosítójú portál.
2. Navigáljon a **közzétevők** -> Válassza ki a fiók -> **rendszergazdák** -> **Társadminisztrátoroknak**.
3. Kattintson a **X** mellett a társadminisztrátornak kívánt tot törlése (az alábbi képernyőfelvételen) gombra.
   
    ![rajz](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> Nem kell végrehajtania a vállalat az adó és a bank adatai, ha azt tervezi, hogy csak szabad ajánlatok közzététele (vagy a saját licenc).
> 
> A vállalati regisztráció el kell végezni a kezdéshez. Azonban a vállalat működik-e az adó és a bank információk a Microsoft Developer-fiók, amíg a fejlesztők is indítsa el a virtuálisgép-lemezkép létrehozása a [közzétételi Portáljára](https://publish.windowsazure.com), első azt hitelesített, és vizsgálja, hogy az Azure tesztelési környezetben. Szüksége lesz a teljes értékesítő fiók jóváhagyási csak az utolsó lépése annak az ajánlat közzététele az Azure piactéren.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Szerezzen be a "használatalapú" Azure-előfizetés
Ez az az előfizetést, a virtuális gép képek létrehozására és a képek keresztül kézi használhatja a [Azure piactér](https://azure.microsoft.com/marketplace/). Ha nem rendelkezik egy meglévő előfizetéshez, majd jelentkezzen a https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Értékesít-feladó" országok
> [!WARNING]
> Ahhoz, hogy a szolgáltatások az Azure piactéren eladásra, győződjön meg arról, hogy a regisztrált entitás jóváhagyott "értékesít-feladó" országok egyikéből. Ez a korlátozás kifizetés és adózás okból is. A közeljövőben bontsa ki a országok listája, így kövessen bennünket aktívan azt olyan eszközökre. A teljes listát lásd a szakasz 1.b. a [Azure piactér részvételét házirendek](http://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## <a name="next-steps"></a>További lépések
Ha a nem technikai jellegű előfeltételek teljesülnek, ezután előfeltételei az ajánlat adott műszaki. A hivatkozásra a cikk a megfelelő ajánlattípus szeretné létrehozni az Azure piactéren.

* [Virtuális gép műszaki Előfeltételek](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Megoldás sablon műszaki Előfeltételek](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Lásd még
* [Első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)

