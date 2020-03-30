---
title: Probléma az alkalmazásproxy-összekötő telepítésekor | Microsoft dokumentumok
description: Az alkalmazásproxy-ügynök-összekötő telepítésekor felmerülő problémák elhárítása
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
ms.openlocfilehash: 466e1ce0efbdec3f5475634f3857d02554d93d98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049130"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Probléma az alkalmazásproxy-ügynök összekötőjének telepítésekor

A Microsoft AAD alkalmazásproxy-összekötő egy belső tartományi összetevő, amely kimenő kapcsolatokat használ a felhőben elérhető végpont és a belső tartomány közötti kapcsolat létrehozásához.

## <a name="general-problem-areas-with-connector-installation"></a>Általános problémás területek a csatlakozó telepítésével

Ha egy összekötő telepítése sikertelen, a kiváltó ok általában az alábbi területek egyike:

1.  **Kapcsolat** – a sikeres telepítés befejezéséhez az új összekötőnek regisztrálnia kell és létre kell hoznia a jövőbeli megbízhatósági tulajdonságokat. Ez az AAD alkalmazásproxy felhőszolgáltatáshoz való csatlakozással történik.

2.  **Bizalmi alapú létrehozás** – az új összekötő létrehoz egy önaláírt tanúsítványt, és regisztrálja a felhőszolgáltatásra.

3.  **A rendszergazda hitelesítése** – a telepítés során a felhasználónak rendszergazdai hitelesítő adatokat kell megadnia az összekötő telepítésének befejezéséhez.

> [!NOTE]
> Az összekötő telepítési naplói a %TEMP% mappában találhatók, és további információt nyújtanak arról, hogy mi okozza a telepítési hibát.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>A felhőalkalmazás-proxy szolgáltatás és a Microsoft bejelentkezési lapjához való kapcsolódás ellenőrzése

**Célkitűzés:** Ellenőrizze, hogy az összekötő gép csatlakozhat-e az AAD alkalmazásproxy regisztrációs végpontjához és a Microsoft bejelentkezési lapjához.

1.  Az összekötő kiszolgálón futtasson porttesztet [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) vagy más portvizsgáló eszközzel annak ellenőrzésére, hogy a 443-as és a 80-as port nyitva van-e.

2.  Ha a portok bármelyike nem sikerül, ellenőrizze, hogy a tűzfal vagy a háttérproxy hozzáfér-e a szükséges tartományokhoz és portokhoz, lásd: A helyszíni környezet előkészítése című [témakört.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)

3.  Nyisson meg egy böngészőt (külön lapot), `https://login.microsoftonline.com`és lépjen a következő weblapra: , győződjön meg arról, hogy be tud jelentkezni az adott oldalra.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>A számítógép- és háttérösszetevők támogatásának ellenőrzése az alkalmazásproxy megbízhatósági tanúsítványához

**Célkitűzés:** Ellenőrizze, hogy az összekötő gép, a háttérproxy és a tűzfal támogatja-e az összekötő által a jövőbeli megbízhatósági kapcsolathoz létrehozott tanúsítványt.

>[!NOTE]
>Az összekötő megpróbál létrehozni egy SHA512 tanúsítványt, amelyet a TLS1.2 támogat. Ha a számítógép vagy a háttértűzfal és a proxy nem támogatja a TLS1.2-t, a telepítés sikertelen lesz.
>
>

**A probléma megoldása:**

1.  Ellenőrizze, hogy a készülék támogatja-e a TLS1.2-t – A 2012 Utáni R2 minden windowsos verziónak támogatnia kell a TLS 1.2-t. Ha a csatlakozógép a 2012 R2 vagy korábbi verzióból származik, győződjön meg arról, hogy a következő kb-ek vannak telepítve a számítógépre:<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Lépjen kapcsolatba a hálózati rendszergazdával, és ellenőrizze, hogy a háttérproxy és a tűzfal nem blokkolja-e az SHA512-t a kimenő forgalom számára.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Annak ellenőrzése, hogy a rendszergazda az összekötő telepítéséhez használható-e

**Célkitűzés:** Ellenőrizze, hogy az összekötőt telepíteni próbáló felhasználó megfelelő hitelesítő adatokkal rendelkező rendszergazda-e. A telepítés sikeresedéséhez a felhasználónak jelenleg legalább alkalmazásrendszergazdának kell lennie.

**A hitelesítő adatok helyességének ellenőrzése:**

Csatlakozzon `https://login.microsoftonline.com` és használja ugyanazokat a hitelesítő adatokat. Ellenőrizze, hogy a bejelentkezés sikeres-e. A felhasználói szerepkört az **Azure Active Directory**  - &gt; **felhasználói és csoportjai**  - &gt; **minden felhasználónak**a segítségével ellenőrizheti. 

Válassza ki a felhasználói fiókot, majd a "Könyvtárszerepkör" lehetőséget az eredményül kapott menüben. Ellenőrizze, hogy a kijelölt szerepkör "Alkalmazás-rendszergazda"-e. Ha a lépések mentén nem tud hozzáférni az oldalakhoz, nem rendelkezik a szükséges szerepkörvel.

## <a name="next-steps"></a>További lépések
[Az Azure AD alkalmazásproxy-összekötők megismerése](application-proxy-connectors.md)
