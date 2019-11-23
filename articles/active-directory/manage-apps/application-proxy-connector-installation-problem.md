---
title: Probléma az alkalmazásproxy-ügynök összekötő telepítésekor | Microsoft Docs
description: Az alkalmazásproxy-ügynök összekötő telepítésekor esetlegesen felmerülő problémák elhárítása
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a35558b81d064680981bcf403a3584e3a3d00e4f
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311742"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Probléma az alkalmazásproxy-ügynök összekötőjének telepítésekor

A Microsoft HRE alkalmazásproxy-összekötő egy belső tartomány-összetevő, amely kimenő kapcsolatokat használ a felhőben elérhető végpont és a belső tartomány közötti kapcsolat létrehozásához.

## <a name="general-problem-areas-with-connector-installation"></a>Általános problémás területek az összekötő telepítésével

Ha egy összekötő telepítése nem sikerül, a kiváltó ok általában az alábbi területek egyike:

1.  **Kapcsolat** – a sikeres telepítés befejezéséhez az új összekötőnek regisztrálnia kell és meg kell teremtenie a jövőbeli megbízhatósági tulajdonságokat. Ezt úgy teheti meg, hogy csatlakozik a HRE Application proxy Cloud Service-hez.

2.  **Megbízhatósági kapcsolat létesítése** – az új összekötő létrehoz egy önaláírt tanúsítványt, és regisztrálja magát a Cloud Service-ben.

3.  **A rendszergazda hitelesítése** – a telepítés során a felhasználónak rendszergazdai hitelesítő adatokat kell megadnia az összekötő telepítésének befejezéséhez.

> [!NOTE]
> Az összekötő telepítési naplói megtalálhatók a% TEMP% mappában, és további információkat is megadhatnak a telepítési hibák okának megadásáról.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>A Cloud Application proxy szolgáltatás és a Microsoft bejelentkezési oldal kapcsolatának ellenőrzése

**Cél:** Ellenőrizze, hogy az összekötő számítógép tud-e csatlakozni a HRE-alkalmazásproxy-regisztrációs végponthoz és a Microsoft bejelentkezési oldalához.

1.  Az összekötő-kiszolgálón futtassa a port tesztet a [Telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) vagy más port tesztelési eszköz használatával annak ellenőrzéséhez, hogy a 443-es és a 80-es portok nyitva vannak-e.

2.  Ha a portok bármelyike nem sikeres, ellenőrizze, hogy a tűzfal vagy a háttér-proxy hozzáfér-e a szükséges tartományokhoz és portokhoz. lásd: a helyszíni [környezet előkészítése](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Nyisson meg egy böngészőt (külön lapon), és lépjen a következő weblapra: <https://login.microsoftonline.com>, és ellenőrizze, hogy be tud-e jelentkezni az adott oldalra.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>A számítógép-és háttér-összetevők támogatásának ellenőrzése az alkalmazásproxy megbízhatósági tanúsítványában

**Cél:** Győződjön meg arról, hogy az összekötő gép, a háttér-proxy és a tűzfal támogatja az összekötő által a jövőbeli megbízhatóság érdekében létrehozott tanúsítványt.

>[!NOTE]
>Az összekötő megpróbál létrehozni egy, a TLS 1.2 által támogatott SHA512-tanúsítványt. Ha a gép vagy a háttérrendszer tűzfala és proxyja nem támogatja a TLS 1.2-et, a telepítés sikertelen lesz.
>
>

**A probléma megoldásához:**

1.  Ellenőrizze, hogy a gép támogatja-e a TLS 1.2-et – a 2012 R2 utáni összes Windows-verziónak támogatnia kell a TLS 1,2 Ha az összekötő-számítógép 2012 R2 vagy korábbi verziójú, akkor győződjön meg arról, hogy a következő Tudásbázis vannak telepítve a gépre: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Forduljon a hálózati rendszergazdához, és kérje meg, hogy ellenőrizze, hogy a háttér-proxy és a tűzfal nem blokkolja-e a kimenő forgalom SHA512.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Ellenőrizze, hogy a rendszergazda az összekötő telepítéséhez használt-e

**Cél:** Ellenőrizze, hogy az összekötőt telepítő felhasználó a megfelelő hitelesítő adatokkal rendelkező rendszergazda-e. A felhasználónak jelenleg legalább egy alkalmazás-rendszergazdának kell lennie ahhoz, hogy a telepítés sikeres legyen.

**A hitelesítő adatok helyességének ellenőrzése:**

Kapcsolódjon <https://login.microsoftonline.com>hoz, és használja ugyanazokat a hitelesítő adatokat. Győződjön meg arról, hogy a bejelentkezés sikeres. A felhasználói szerepkört úgy tekintheti meg, hogy **Azure Active Directory** -&gt; **felhasználókat és csoportokat** -&gt; **minden felhasználó számára**. 

Válassza ki a felhasználói fiókot, majd a "címtárbeli szerepkör" lehetőséget az eredményül kapott menüben. Győződjön meg arról, hogy a kiválasztott szerepkör az "Application Administrator". Ha nem fér hozzá a fenti lépésekhez tartozó lapokhoz, nem rendelkezik a szükséges szerepkörrel.

## <a name="next-steps"></a>Következő lépések
[Az Azure AD-alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)
