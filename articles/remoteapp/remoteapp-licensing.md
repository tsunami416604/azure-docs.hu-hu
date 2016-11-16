---
title: "Azure RemoteApp-licenckezelés | Microsoft Docs"
description: "Ismerje meg az Azure RemoteApp licenckezelését."
services: remoteapp
documentationcenter: 
author: lizap
manager: mbaldwin
ms.assetid: ff8ebd20-61a1-4f10-87a6-234a170534c9
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f4429461139a41644f87185bbb06b1f6a8011345


---
# <a name="how-does-licensing-work-in-azure-remoteapp"></a>Hogyan működik a licenckezelés az Azure RemoteAppban?
> [!IMPORTANT]
> Azure RemoteApp hamarosan megszűnik. A részletekért olvassa el a [bejelentést](https://go.microsoft.com/fwlink/?linkid=821148).
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




<!--HONumber=Nov16_HO2-->


