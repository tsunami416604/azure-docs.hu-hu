---
title: A probléma az alkalmazásproxy-ügynök Összekötőjével telepítése |} A Microsoft Docs
description: Az alkalmazásproxy-ügynök Összekötőjével telepítésekor előfordulhat, hogy között hibáinak elhárítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 98dc3981e0f34ebfe7d8881d77b84cfe252e3f4f
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357121"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>A probléma az alkalmazásproxy-ügynök összekötő telepítése

A Microsoft AAD alkalmazásproxy-összekötő egy belső tartomány-összetevő, amely a kimenő kapcsolatokat használ a kapcsolat a felhőben elérhető végpontok és a belső tartomány létrehozásához.

## <a name="general-problem-areas-with-connector-installation"></a>Általános problémás területek-összekötő telepítése

Az összekötő telepítése nem sikerül, az alapvető ok általában a következő területek közül:

1.  **Kapcsolat** – sikeres telepítés, az új összekötő igényeinek regisztrálja, és létrehozza a jövőbeli megbízhatósági tulajdonságok végrehajtásához. Ez a AAD alkalmazásproxy felhőszolgáltatáshoz való csatlakozással történik.

2.  **Megbízhatósági kapcsolat kialakítása** – az új összekötőt hoz létre egy önaláírt tanúsítványt, és regisztrálja a felhőszolgáltatáshoz.

3.  **A rendszergazda a hitelesítési** – a telepítés során a felhasználónak meg kell adnia az összekötő-telepítés befejezéséhez rendszergazdai hitelesítő adataival.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Ellenőrizze a kapcsolatot a felhőben az alkalmazásproxy-szolgáltatás és a Microsoft Login lap

**Cél:** ellenőrizze, hogy az összekötő gépek csatlakozhatnak az AAD alkalmazásproxy regisztrációs végpont, valamint a Microsoft bejelentkezési oldalára.

1.  Nyisson meg egy böngészőt, és nyissa meg a következő weblapot: <https://aadap-portcheck.connectorporttest.msappproxy.net> , és győződjön meg arról, hogy a kapcsolat az USA középső RÉGIÓJA és USA keleti Régiójában adatközpontokhoz a 80-as és 443-as porton működik.

2.  Ha bármelyik ezeket a portokat nem sikerült (nem kell egy zöld pipa), ellenőrizze, hogy rendelkezik-e a tűzfal- vagy háttéradatbázis proxy \*. msappproxy.net a 80-as és 443-as helyesen adta meg.

3.  Nyisson meg egy böngészőt (külön lapon), és nyissa meg a következő weblapot: <https://login.microsoftonline.com>, győződjön meg arról, hogy lehet-e bejelentkezni erre az oldalra.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Ellenőrizze a gép és a háttérkiszolgáló összetevők Application Proxy megbízhatósági cert támogatása

**Cél:** ellenőrizze, hogy az összekötő gép, a háttér-proxy és a tűzfal is támogatja a jövőbeli megbízhatóságához az összekötő által létrehozott tanúsítvány.

>[!NOTE]
>Az összekötő megpróbál létrehozni egy SHA512 cert TLS1.2 által támogatott. Ha a gép vagy a háttértűzfalat, és a proxy nem támogatja a TLS1.2, a telepítés sikertelen.
>
>

**A probléma megoldásához:**

1.  Ellenőrizze a gép támogatja a TLS1.2 – a 2012 R2 összes Windows-verziókhoz támogatnia kell a TLS 1.2. Ha az összekötő gép verziója a 2012 R2 vagy korábbi, győződjön meg arról, hogy a következő Tudásbázis telepítve vannak-e a gépen: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Lépjen kapcsolatba a hálózat rendszergazdájával, és kérje meg, ellenőrizze, hogy a háttérrendszer proxy és tűzfal nem blokkolja SHA512 a kimenő forgalom számára.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Ellenőrizze, hogy az összekötő telepítéséhez használt rendszergazdai

**Cél:** ellenőrizze, hogy a felhasználó, aki próbálja telepíteni az összekötőt a rendszergazda a helyes hitelesítő adatokkal. A felhasználó jelenleg a telepítés sikeres globális rendszergazdájának kell lennie.

**Annak ellenőrzése, hogy a hitelesítő adatok helyesek:**

Csatlakozás <https://login.microsoftonline.com> és ugyanezeket a hitelesítő adatokat. Győződjön meg arról, hogy a bejelentkezés sikeres. A felhasználói szerepkör ellenőrizheti a **Azure Active Directory**  - &gt; **felhasználók és csoportok**  - &gt; **minden felhasználó**. 

Válassza ki a felhasználói fiókot, majd "címtárbeli szerepkör" az eredményül kapott menüben. Ellenőrizze, hogy a kiválasztott szerepkör "Globális rendszergazda". Ha nem érhető el bármelyik, a lépések mentén, Ön nem globális rendszergazda.

## <a name="next-steps"></a>További lépések
[Az Azure AD-alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)
