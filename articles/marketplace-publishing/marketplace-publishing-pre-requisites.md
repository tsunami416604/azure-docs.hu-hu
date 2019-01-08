---
title: Ajánlat létrehozása az Azure Marketplace-en nem technikai jellegű előfeltételei |} A Microsoft Docs
description: Létrehozása és telepítése egy ajánlatot az Azure piactéren, mások beszerzési követelményeinek megismerése.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: 4b925522186d2d9ae537431c1d96d39b107ad967
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073169"
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Ajánlat létrehozása az Azure piactér általános előfeltételei
Általános, üzleti folyamat-központú szükséges előfeltételek megismerésében, melyek szükségesek ahhoz, hogy az ajánlat létrehozási folyamata folytatná.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Győződjön meg arról, hogy egy Microsoft-értékesítői regisztrált
A Microsoft-értékesítői fiókjával regisztrálás részletes utasításokért ugorjon [fióklétrehozás és a regisztrációs](marketplace-publishing-accounts-creation-registration.md).

* Ha a vállalata már regisztrálva van egy értékesítő a fejlesztői központban, és szeretne létrehozni egy új ajánlatunk, majd jelentkezzen be a közzétételi portált az azonos e-mail-azonosító, mely fejlesztői központ-regisztráció történik. Ez a lépés akkor szükséges, úgy, hogy a fejlesztői központban és a közzétételi portál kapcsolódnak egymáshoz.
* Ha a vállalata már regisztrálva van egy értékesítő a fejlesztői központban és, egy meglévő ajánlatra módosítani szeretné, akkor vagy jelentkezzen be a közzétételi portál, a rendszergazdai fiókkal vagy egy olyan fiókkal, egy a közzétételi társfelügyeletű hozzáadott portálon. Az alábbi táblázat a lépéseket, coadmin fiók hozzáadása.

## <a name="steps-to-add-a-coadmin-in-the-publishing-portal"></a>A közzétételi portálon egy társfelügyeletű hozzáadásának lépéseit
A rendszergazdák a közzétételi portál adhat hozzá más tagjaival a vállalat számára az alkalmazás dolgozik, mint egy coadmin, az a közzétételi portálon. **Feltételezve, hogy a rendszergazdaként** a egy társfelügyeletű hozzáadásának lépéseit az alábbi vannak.

> [!NOTE]
> Az új felhasználók hozzáadása előtt társadminisztrátorként való a közzététel portálon győződjön meg arról, hogy legalább egy alkalmazás létrehozott a közzétételi portálon. Erre azért szükség, a **KÖZZÉTEVŐK** lap jelenik meg, csak után legalább egy alkalmazás létrehozása a közzétételi portál.
> 
> 

1. Győződjön meg arról, hogy a társfelügyeletű e-mail-azonosító egy Microsoft account(MSA). Ha nem, regisztrálja az msa-t ez másként [hivatkozás](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Győződjön meg arról, hogy nincs-e a rendszergazdai fiók alatt egy társfelügyeletű hozzáadása előtt legalább egy alkalmazást.
3. Miután végzett a fenti lépéseket, jelentkezzen be a közzétételi portálra a társfelügyeletű e-mail-azonosító és majd jelentkezzen ki.
4. Most jelentkezzen be a közzétételi portál, a rendszergazda e-mail-azonosítóval.
5. Keresse meg a kiadók -> Válassza ki a fiók -> rendszergazdák hozzáadása a társfelügyeletű (az alábbi képernyőképen) ->
   
    ![rajz](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Győződjön meg arról, hogy a közzétételi folyamat (például fejlesztői központ, közzétételi portál) a különböző szakaszaiban a megadott e-mail-azonosítóknak figyelt bármely Microsoft közti kommunikációhoz.
7. Fejlesztői központ-regisztráció ne egy olyan fiókkal társított egyetlen személy. Ez a javaslat egy egyéni függőség eltávolítása.
8. Fejlesztői központ regisztrációs problémák között, ha ez egy jegyet előléptetése majd [hivatkozás](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-coadmin-in-the-publishing-portal"></a>Egy a közzétételi társfelügyeletű törlésére vonatkozó lépéseket portál
**Feltételezve, hogy a rendszergazdaként** egy társfelügyeletű törlésének lépéseivel az alábbi vannak.

1. Jelentkezzen be a közzétételi portál, a rendszergazda e-mail-azonosítóval.
2. Navigáljon a **közzétevők** -> Válassza ki a fiók -> **rendszergazdák** -> **Társrendszergazdák**.
3. Kattintson a **X** mellett a társfelügyeletű azt szeretné, hogy tot törlése (az alábbi képernyőképen) gombra.
   
    ![rajz](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> Nem kell végrehajtania a vállalat adózási és banki információt, ha azt tervezi, csak az ingyenes ajánlatok közzététele (vagy a saját licenc használata).
> 
> Első lépések a vállalati regisztráció kell végrehajtani. Azonban a vállalat a adózási és banki információt, a Microsoft Developer-fiók működik, amíg a fejlesztők is elkezdhet dolgozni a virtuálisgép-lemezkép létrehozása a [közzétételi portál](https://publish.windowsazure.com), azt, és a tesztelés azt az Azure átmeneti környezetben. Szüksége lesz a teljes értékesítői fiók jóváhagyás csak az utolsó lépés az ajánlat az Azure piactéren való közzétételéhez.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Egy "használatalapú" Azure-előfizetés beszerzése
Az előfizetést, amely használatával a Virtuálisgép-rendszerképek létrehozása és a képek kézi értéke a [Azure Marketplace-en](https://azure.microsoft.com/marketplace/). Ha nem rendelkezik egy meglévő előfizetéshez, majd jelentkezzen https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Értékesítő" országokat
> [!WARNING]
> Annak érdekében, hogy a szolgáltatások az Azure piactéren értékesítheti, győződjön meg arról, hogy a regisztrált entitást a jóváhagyott "értékesítő" országokat közül. Ez a korlátozás küldenie jóváhagyásra cége kifizetési és adózási okból is. A Microsoft aktívan kíváncsi, bontsa ki a közeljövőben országok listája, úgyhogy érdemes figyelnie. A teljes listát lásd: 1.b. a szakasz a [Azure piactér részvételi szabályzatának](https://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## <a name="next-steps"></a>További lépések
Ha a nem technikai jellegű előfeltételek teljesülnek, ezután ajánlat-specifikus technikai előfeltételek szükségesek. Kattintson a hivatkozásra kattintva a cikket a megfelelő ajánlattípusra szeretné létrehozni az Azure Marketplace-en.

* [Virtuális gép technikai előfeltételeket](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Megoldás sablon technikai előfeltételeket](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Lásd még
* [Első lépések: Ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)

