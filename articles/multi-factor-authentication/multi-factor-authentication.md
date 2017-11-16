---
title: "További tudnivalók a kétlépéses ellenőrzést, az Azure MFA |} Microsoft Docs"
description: "Mi az Azure multi-factor Authentication, MFA, további információt a multi-factor Authentication ügyfél és a különböző módszereket és verziók miért érdemes használni. "
keywords: "az MFA, multi-factor Authentication áttekintése, mi az az mfa bemutatása"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: c40d7a34-1274-4496-96b0-784850c06e9b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2017
ms.author: joflore
ms.openlocfilehash: a928344dfc41687c92e7845bc304a05122ab8a92
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-multi-factor-authentication"></a>Mi az az Azure Multi-Factor Authentication?
Kétlépéses ellenőrzés, hogy egynél több ellenőrzési módszert igényel, és a kritikus fontosságú második biztonsági réteget ad hozzá felhasználói bejelentkezéseket és tranzakciókat hitelesítési mód. Működését tekintve a igénylő bármely két vagy több, az alábbi hitelesítési módszerek:

* Valami tudja (általában jelszót)
* Valami (megbízható eszközzel rendelkezik, amely nem egyszerű ismétlődik, például a telefon)
* Valami áll (biometria)

<center>![Felhasználónév és jelszó](./media/multi-factor-authentication/pword.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![tanúsítványok](./media/multi-factor-authentication/phone.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Phone intelligens](./media/multi-factor-authentication/hware.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![intelligens kártya](./media/multi-factor-authentication/smart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![virtuális intelligens kártya](./media/multi-factor-authentication/vsmart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![felhasználónév és jelszó](./media/multi-factor-authentication/cert.png)</center>

Az Azure Multi-Factor Authentication (MFA) a Microsoft kétlépéses hitelesítési megoldása. Az Azure MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáférésekkel szemben, miközben a felhasználói igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát teszi lehetővé. Számos (például telefonos megerősítést, szöveges üzenetet vagy mobilalkalmazást használó) ellenőrzési módszerének köszönhetően erős hitelesítést biztosít.

## <a name="why-use-azure-multi-factor-authentication"></a>Miért érdemes használni az Azure multi-factor Authentication?
Ma, több mint legalább egyszer személyek egyre csatlakoznak. Intelligens telefonok, táblagépek, laptopok és számítógépekhez, személyek több lehetőségek állnak a fiókok és alkalmazások hozzáférni bárhonnan és bármikor maradhat.

Az Azure multi-factor Authentication egy könnyen használható, méretezhető és megbízható megoldás, amely egy második hitelesítési eljárást is védelme érdekében a felhasználók.

| ![Egyszerű használat](./media/multi-factor-authentication/simple.png) | ![Méretezhető](./media/multi-factor-authentication/scalable.png) | ![Mindig védve](./media/multi-factor-authentication/protected.png) | ![Megbízható](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **Könnyen használható.** |**Méretezhető** |**Mindig védve** |**Megbízható** |

* **Könnyen használható** -Azure multi-factor Authentication egy egyszerű beállítása és használata. A további védelem a Azure multi-factor Authentication lehetővé teszi, hogy a felhasználók a saját eszközök kezelésére. Ajánlott az összes sok esetben azt is beállítható néhány egyszerű kattintással.
* **Méretezhető** -Azure multi-factor Authentication használja ki a felhőt, és integrálható a helyszíni AD és az egyéni alkalmazások. Ez a védelem még akkor is ki van bővítve a nagy mennyiségű, a kritikus fontosságú forgatókönyvek.
* **Mindig védett** -Azure multi-factor Authentication használata a legmagasabb iparági szabványoknak megfelelő erős hitelesítést nyújt.
* **Megbízható** -Microsoft Azure multi-factor Authentication 99,9 %-os rendelkezésre állását garantálja. A szolgáltatás nem érhető el tekintendő, ha nem jelenik meg, vagy a kétlépéses ellenőrzéshez hitelesítési kérelmeket.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>Következő lépések

- További tudnivalók [Azure multi-factor Authentication működése](multi-factor-authentication-how-it-works.md)

- Olvassa el a különböző [a Azure multi-factor Authentication verziók és használat](multi-factor-authentication-versions-plans.md)
