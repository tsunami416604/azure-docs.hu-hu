---
title: További tudnivalók a kétlépéses ellenőrzést, az Azure MFA-ban |} A Microsoft Docs
description: Mi az Azure multi-factor Authentication, miért érdemes használni az MFA, és a különböző módszerek és a verzió érhető el.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: d58d81d85dac7e5cd520b8d8a3fb5d91650e0cbe
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161605"
---
# <a name="what-is-azure-multi-factor-authentication"></a>Mi az az Azure Multi-Factor Authentication?

A kétlépéses ellenőrzés egy hitelesítési mód, amely egynél több ellenőrzési módszert igényel, és a egy kritikus fontosságú második biztonsági réteget ad hozzá felhasználói bejelentkezéseket és tranzakciókat. Azzal, hogy bármely két vagy több, a következő ellenőrzési módszerek sorát működik:

* Valami tudja (általában jelszót)
* Hiba (megbízható eszközzel rendelkezik, amely nem könnyen lettek duplikálva, például telefon)
* Hiba (biometrikus adatok) áll

<center>![Felhasználónév és jelszó](./media/multi-factor-authentication/pword.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![tanúsítványok](./media/multi-factor-authentication/phone.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Phone intelligens](./media/multi-factor-authentication/hware.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![intelligens kártya](./media/multi-factor-authentication/smart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Virtuális intelligens kártya](./media/multi-factor-authentication/vsmart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![felhasználónév és jelszó](./media/multi-factor-authentication/cert.png)</center>

Az Azure Multi-Factor Authentication (MFA) a Microsoft kétlépéses hitelesítési megoldása. Az Azure MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáférésekkel szemben, miközben a felhasználói igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát teszi lehetővé. Számos (például telefonos megerősítést, szöveges üzenetet vagy mobilalkalmazást használó) ellenőrzési módszerének köszönhetően erős hitelesítést biztosít.

## <a name="why-use-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication előnyei
Jelenleg több mint minden eddiginél emberek egyre kapcsolódnak. Okostelefonon, táblagépek, laptopok és számítógépeket, a felhasználók több lehetőség áll rendelkezésére a fiókok és alkalmazások elérése bárhonnan és bármikor maradhat.

Az Azure multi-factor Authentication egy könnyen használható, méretezhető és megbízható megoldás, amely egy második védelme érdekében a felhasználók hitelesítési módszert kínál a.

| ![Egyszerű használat](./media/multi-factor-authentication/simple.png) | ![Méretezhető](./media/multi-factor-authentication/scalable.png) | ![Mindig védve](./media/multi-factor-authentication/protected.png) | ![Megbízható](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **Könnyen használható** |**Méretezhető** |**Mindig védve** |**Megbízható** |

* **Könnyen használható** – az Azure multi-factor Authentication az beállítása és használata egyszerű. Az extra védelem az Azure multi-factor Authentication lehetővé teszi a felhasználók kezelhetik saját eszközeiket. Az összes sok esetben ajánlott, állíthatja néhány egyszerű kattintással.
* **Méretezhető** – Azure multi-factor Authentication a felhő hatékonyságát és integrálható a helyszíni AD és az egyéni alkalmazásokra. Ez a védelem akkor is ki van bővítve a nagy teljesítményű, üzleti szempontból alapvető fontosságú feladatokhoz.
* **Mindig védve** – Azure multi-factor Authenticationt biztosít erős hitelesítés, a legmagasabb iparági szabványok használatával.
* **Megbízható** – Microsoft Azure multi-factor Authentication 99,9 %-os rendelkezésre állását garantálja. A szolgáltatás akkor tekinthető nem érhető el, ha nem tudja fogadni vagy feldolgozni a kétlépéses ellenőrzési kérelmeket.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>További lépések

- Ismerje meg [Azure multi-factor Authentication működése](concept-mfa-howitworks.md)

- További információ a különböző [-verzió és -felhasználási módszert az Azure multi-factor Authentication](concept-mfa-licensing.md)
