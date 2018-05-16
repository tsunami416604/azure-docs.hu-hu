---
title: Az ügynök Proxy összekötőjének telepítése probléma |} Microsoft Docs
description: Az alkalmazásproxy-ügynök összekötő telepítésekor előfordulhat, hogy szembesülhetnek kapcsolatos problémák elhárítása
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 70d2f159c5485009e36aa80dfa8b73f9b3cf4ec1
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>A probléma az ügynök Proxy összekötőjének telepítése

Microsoft AAD alkalmazásproxy-összekötő által használt kimenő kapcsolatokat és a belső tartomány közötti a felhő elérhető végpontot a kapcsolatot létesíteni egy belső tartomány-összetevő.

## <a name="general-problem-areas-with-connector-installation"></a>Általános problémás területek Connector telepítése

Az összekötő telepítése nem sikerül, az alapvető ok esetén általában a következő területek közül:

1.  **Kapcsolat** – sikeres telepítés, az új összekötő kell regisztrálni, és létrehozza a jövőbeli megbízhatósági tulajdonságok befejezéséhez. Ehhez a AAD alkalmazásproxy felhő szolgáltatáshoz való csatlakozáskor.

2.  **Megbízhatósági kapcsolat kialakítása** – az új összekötőt hoz létre egy önaláírt tanúsítványt, és regisztrálja a felhőalapú szolgáltatáshoz.

3.  **A rendszergazda hitelesítési** – a telepítés során a felhasználónak meg kell adnia az összekötő telepítéséhez rendszergazdai hitelesítő adataival.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Ellenőrizze a kapcsolatot a Felhőbeli Proxy szolgáltatás és a Microsoft Login lap

**Cél:** ellenőrizze, hogy az összekötő számítógéphez csatlakozni tud-e az AAD alkalmazásproxy regisztrációs végpont, valamint a Microsoft bejelentkezési oldalt.

1.  Nyisson meg egy böngészőt, és nyissa meg a következő weblapra: <https://aadap-portcheck.connectorporttest.msappproxy.net> , és győződjön meg arról, hogy a kapcsolat központi Amerikai Egyesült Államok és USA keleti régiója adatközpontokhoz 80-as és 443-as porttal működik-e.

2.  Ha ezeket a portokat bármelyike nem sikeres (zöld pipa nem rendelkezik), ellenőrizze, hogy rendelkezik-e a tűzfal- vagy háttéradatbázis proxy \*. a 80-as és 443-as helyesen definiálva portokkal msappproxy.net.

3.  Nyisson meg egy böngészőt (külön lapon), és nyissa meg a következő weblapra: <https://login.microsoftonline.com>, győződjön meg arról, hogy ez az oldal bejelentkezhet.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Ellenőrizze a gép és a háttérkiszolgáló összetevői támogatják az alkalmazásproxy megbízható tanúsítvány

**Cél:** győződjön meg arról, hogy az összekötő számítógéphez, a háttér-proxy és a tűzfal képesek támogatni a jövőbeli megbízhatóságához az összekötő által létrehozott tanúsítványt.

>[!NOTE]
>Az összekötő megpróbál létrehozni egy SHA512 cert TLS1.2 által támogatott. Ha a számítógép vagy a háttértűzfalat, proxy nem támogatja a TLS1.2, a telepítés sikertelen.
>
>

**A probléma megoldása:**

1.  Ellenőrizze a gép támogatja-e TLS1.2 – 2012 R2 után minden Windows-verziók támogatnia kell a TLS 1.2-es. Ha az összekötő gép 2012 R2 verzióját vagy előtt, győződjön meg arról, hogy a következő KB telepítve vannak-e a gépen: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  A hálózati rendszergazdától, és kérje meg, hogy ellenőrizze, hogy a háttér-proxy és tűzfal nem blokkolja SHA512 a kimenő forgalom.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Ellenőrizze, hogy az összekötő telepítéséhez használt felügyeleti

**Cél:** ellenőrizze, hogy a felhasználó megpróbálja telepíteni az összekötőt a rendszergazda a helyes hitelesítő adatokkal. A felhasználó jelenleg sikeres a telepítéshez egy globális rendszergazdának kell lennie.

**Győződjön meg arról, hogy a hitelesítő adatok helyesek:**

Csatlakozás <https://login.microsoftonline.com> és ugyanezeket a hitelesítő adatokat. Győződjön meg arról, hogy a bejelentkezés sikeres. A felhasználói szerepkör megnyitásával ellenőrizheti **Azure Active Directory**  - &gt; **felhasználók és csoportok**  - &gt; **minden felhasználó**. 

Válassza ki a felhasználói fiókot, majd "Directory szerepkör" az eredményül kapott menüjében. Győződjön meg arról, hogy a kiválasztott "Globális rendszergazda". Ha nem érhető el a oldalakon mentén ezeket a lépéseket, nem egy globális rendszergazdaként.

## <a name="next-steps"></a>További lépések
[Az Azure AD-alkalmazásproxy összekötők ismertetése](manage-apps/application-proxy-connectors.md)
