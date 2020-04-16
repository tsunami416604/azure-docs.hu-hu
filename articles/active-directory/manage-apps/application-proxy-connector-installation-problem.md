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
ms.openlocfilehash: 4d773e6302edf0b799e6dfccc702750a9cc74f60
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406688"
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

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>A számítógép- és háttérösszetevők támogatásának ellenőrzése az alkalmazásproxy megbízhatósági tanúsítványához

**Célkitűzés:** Ellenőrizze, hogy az összekötő gép, a háttérproxy és a tűzfal támogatja-e az összekötő által a jövőbeli megbízhatósági kapcsolathoz létrehozott tanúsítványt, és hogy a tanúsítvány érvényes-e.

>[!NOTE]
>Az összekötő megpróbál létrehozni egy SHA512 tanúsítványt, amelyet a TLS1.2 támogat. Ha a számítógép vagy a háttértűzfal és a proxy nem támogatja a TLS1.2-t, a telepítés sikertelen lesz.
>
>

**Tekintse át a szükséges előfeltételeket:**

1.  Ellenőrizze, hogy a készülék támogatja-e a TLS1.2-t – A 2012 Utáni R2 minden windowsos verziónak támogatnia kell a TLS 1.2-t. Ha a csatlakozógép a 2012 R2 vagy korábbi verzióból származik, győződjön meg arról, hogy a következő kb-ek vannak telepítve a számítógépre:<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Lépjen kapcsolatba a hálózati rendszergazdával, és ellenőrizze, hogy a háttérproxy és a tűzfal nem blokkolja-e az SHA512-t a kimenő forgalom számára.

**Az ügyféltanúsítvány ellenőrzése:**

Ellenőrizze az aktuális ügyféltanúsítvány ujjlenyomatát. A tanúsítványtároló a %ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml mappában található.

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

Itt vannak a lehetséges **IsInUserStore** értékek és jelentés:

- **false** - Az ügyféltanúsítvány a Register-AppProxyConnector paranccsal kezdeményezett telepítés vagy regisztráció során jött létre. A személyes tárolóban tárolja a helyi gép tanúsítványtárolójában. 

A tanúsítvány ellenőrzéséhez kövesse a lépéseket:

1. **Certlm.msc futtatása**
2. A felügyeleti konzolon bontsa ki a Személyes tárolót, és kattintson a Tanúsítványok
3. Keresse meg a **connectorregistrationca.msappproxy.net** által kiállított tanúsítványt

- **true** - Az automatikusan megújított tanúsítvány a hálózati szolgáltatás felhasználói tanúsítványtárolójában található személyes tárolóban tárolódik. 

A tanúsítvány ellenőrzéséhez kövesse a lépéseket:

1. [PsTools.zip letöltése](https://docs.microsoft.com/sysinternals/downloads/pstools)
2. Bontsa ki a [PsExec-et](https://docs.microsoft.com/sysinternals/downloads/psexec) a csomagból, és futtassa a **psexec -i -u "nt authority\network service" cmd.exe** parancsot egy rendszergazda jogú parancssorból.
3. A **certmgr.msc** futtatása az újonnan megjelenő parancssorban
2. A felügyeleti konzolon bontsa ki a Személyes tárolót, és kattintson a Tanúsítványok
3. Keresse meg a **connectorregistrationca.msappproxy.ne által kiállított tanúsítványt

**Az ügyféltanúsítvány megújítása:**

Ha egy összekötő több hónapig nem csatlakozik a szolgáltatáshoz, a tanúsítványok elavultak lehetnek. A tanúsítvány megújításának sikertelensége lejárt tanúsítványhoz vezet. Ez leállítja az összekötő szolgáltatást. Az 1000-es esemény az összekötő felügyeleti naplójában kerül rögzítésre:

"Az összekötő újraregisztrálása nem sikerült: Az összekötő megbízhatósági tanúsítványa lejárt. Futtassa a PowerShell-parancsmag Register-AppProxyConnector azon a számítógépen, amelyen az összekötő fut, hogy újra regisztrálja az összekötőt."

Ebben az esetben távolítsa el és telepítse újra az összekötőt a regisztráció aktiválásához, vagy futtassa a következő PowerShell-parancsokat:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Ha többet szeretne megtudni a Register-AppProxyConnector parancsról, olvassa [el a Felügyelet nélküli telepítési parancsfájl létrehozása az Azure AD alkalmazásproxy-összekötőhöz című témakört.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-register-connector-powershell)

## <a name="verify-admin-is-used-to-install-the-connector"></a>Annak ellenőrzése, hogy a rendszergazda az összekötő telepítéséhez használható-e

**Célkitűzés:** Ellenőrizze, hogy az összekötőt telepíteni próbáló felhasználó megfelelő hitelesítő adatokkal rendelkező rendszergazda-e. A telepítés sikeresedéséhez a felhasználónak jelenleg legalább alkalmazásrendszergazdának kell lennie.

**A hitelesítő adatok helyességének ellenőrzése:**

Csatlakozzon `https://login.microsoftonline.com` és használja ugyanazokat a hitelesítő adatokat. Ellenőrizze, hogy a bejelentkezés sikeres-e. A felhasználói szerepkört az **Azure Active Directory**  - &gt; **felhasználói és csoportjai**  - &gt; **minden felhasználónak**a segítségével ellenőrizheti. 

Válassza ki a felhasználói fiókot, majd a "Könyvtárszerepkör" lehetőséget az eredményül kapott menüben. Ellenőrizze, hogy a kijelölt szerepkör "Alkalmazás-rendszergazda"-e. Ha a lépések mentén nem tud hozzáférni az oldalakhoz, nem rendelkezik a szükséges szerepkörvel.

## <a name="next-steps"></a>További lépések
[Az Azure AD alkalmazásproxy-összekötők megismerése](application-proxy-connectors.md)
