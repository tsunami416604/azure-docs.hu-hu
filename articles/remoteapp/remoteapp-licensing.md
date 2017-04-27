---
title: "Azure RemoteApp-licenckezelés | Microsoft Docs"
description: "Ismerje meg az Azure RemoteApp licenckezelését."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: ff8ebd20-61a1-4f10-87a6-234a170534c9
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 43d0dbb905b2f2b9d98fb3bf8c073ba1c4b6f4c4
ms.lasthandoff: 03/31/2017


---
# <a name="how-does-licensing-work-in-azure-remoteapp"></a>Hogyan működik a licenckezelés az Azure RemoteAppban?
> [!IMPORTANT]
> Az Azure RemoteApp 2017. augusztus 31-ét követően megszűnik. A részletekért olvassa el a [bejelentést](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Az eddigiekben beállította az Azure RemoteAppot, létrehozta a sablonokat, és készen áll arra, hogy alkalmazásokat tegyen közzé a felhasználóknak. Egy dolog azonban még hátra van: a licencelés. Hogyan működik a RemoteApp és rajta keresztül megosztott alkalmazások licenckezelése?

A RemoteApp használatához nincs szükség semmilyen Windows-licencre vagy távoli asztali ügyféllicencre. Az előfizetés gondoskodik a RemoteApp oldaláról. (A [díjszabások](https://azure.microsoft.com/pricing/details/remoteapp) részleteit itt tekintheti meg.)

Ha az előfizetéshez mellékelt rendszerképet használ, a rendszerképen telepített bármelyik alkalmazást megoszthatja, különálló licenc nélkül. Ha például a Windows Server 2012 R2 sablon rendszerképet használja a gyűjtemény összeállításához, megoszthatja a System Center Endpoint Protectiont a felhasználóival. Ezen szabály alól csak az Office 365 ProPlus jelent kivételt, amelyhez különálló előfizetés szükséges, illetve az Office 2013, amely nem osztható meg termelési környezetben használt gyűjteményben.

Ha a RemoteApphoz mellékelt Office 365 sablon rendszerképet kívánja használni, egy *meglévő* Office 365 ProPlus-előfizetéssel kell rendelkeznie. Ugyanez érvényes minden Office 365-alkalmazásra, amelyet egy egyéni sablon használatával tesz közzé. Az alkalmazásokat a saját előfizetésével kell aktiválnia. Ez a próba- és a fizetett előfizetésekre is vonatkozik. Ha a próbaidőszak során szeretné használni az Office 365 sablon rendszerképet, *és még nem rendelkezik előfizetéssel*, látogasson el az Office 365 oldalára, és [regisztráljon](https://go.microsoft.com/fwlink/p/?LinkID=403802) egy próba-előfizetésre. További információkért lásd a [Hogyan működik együtt a RemoteApp és az Office?](remoteapp-o365.md) (Hogyan működik együtt a RemoteApp és az Office) témakört.

Ha a próbaidőszak alatt nem kíván Office 365 próba-előfizetést beszerezni, használja a RemoteApphoz mellékelt Office 2013 Professional Plus sablon rendszerképet. Ez a sablon csak 30 napig használható, és alakítható át fizetett gyűjteménnyé.

Más alkalmazások esetében meg kell győződnie arról, hogy rendelkezik az alkalmazás megosztásához szükséges licenccel.

Ez így érthető, ugye? Bármilyen alkalmazást közzétehet, amelynek megosztására jogosultsága van. És meg kell győződnie róla, hogy valóban jogosult a programok megosztására.

Vegye figyelembe, hogy felhőalapú gyűjtemény esetében nem használhat ügyféllicencet vagy mennyiségi licencelési megállapodást. A hibrid gyűjteményekben lévő alkalmazások aktiválására *használhat* mennyiségi licencelési megállapodást (kivéve az Office esetében). Ehhez csak telepítenie kell ezeket a sablon rendszerképre a mennyiségi licencelési adathordozóról. A licencek távoli asztali környezetben való telepítéséhez kövesse az alkalmazás gyártójának információit.


