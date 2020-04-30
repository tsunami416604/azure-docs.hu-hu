---
title: A Windows rendszerű virtuális asztalok elleni védelem Azure Firewall használata
description: Ismerje meg, hogyan használhatja a Azure Firewall a Windows rendszerű virtuális asztali környezetek elleni védelemhez
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: victorh
ms.openlocfilehash: c5d7281d50c151722303b48b2b28a597eec72d79
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254173"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>A Azure Firewall használata a Windows rendszerű virtuális asztali környezetek elleni védelemhez

A Windows Virtual Desktop (WVD) az Azure-on futó asztali és app Virtualization szolgáltatás. Amikor a végfelhasználó egy Windows rendszerű virtuális asztali környezethez csatlakozik, a munkamenetet egy gazdagép futtatja. A címkészlet olyan Azure-beli virtuális gépek gyűjteménye, amelyek munkamenet-gazdagépként regisztrálják a Windows rendszerű virtuális asztalt. Ezek a virtuális gépek a virtuális hálózaton futnak, és a virtuális hálózat biztonsági ellenőrzése alá esnek. A WVD szolgáltatásnak kimenő internet-hozzáférésre van szükségük ahhoz, hogy megfelelően működjenek, és a végfelhasználók számára is szükség lehet a kimenő internet-hozzáférésre. Azure Firewall segíthet a környezet zárolásában és a kimenő forgalom szűrésében.

[![Windows rendszerű virtuális](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) asztali architektúra](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

A jelen cikkben található irányelvek alapján további védelmet biztosíthat a WVD gazdagép-készletnek a Azure Firewall használatával.

## <a name="prerequisites"></a>Előfeltételek


 - Egy üzembe helyezett WVD-környezet és-címkészlet.

   További információ: [oktatóanyag: állomáslista létrehozása az Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) - [szel és egy Azure Resource Manager sablonnal rendelkező gazdagép létrehozása](../virtual-desktop/create-host-pools-arm-template.md).

A WVD-környezetekről a [Windows rendszerű virtuális asztali környezet](../virtual-desktop/environment-setup.md)című témakörben olvashat bővebben.

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Gazdagép-készlet kimenő hozzáférése a Windows rendszerű virtuális asztalhoz

A Windows rendszerű virtuális asztali környezethez létrehozott Azure-beli virtuális gépeknek a megfelelő működéshez számos teljes tartománynevet (FQDN) kell elérniük. A Azure Firewall egy Windows rendszerű virtuális asztali FQDN-címkét biztosít a konfiguráció egyszerűsítéséhez. A következő lépésekkel engedélyezheti a kimenő WVD platform forgalmát:

- A Azure Firewall üzembe helyezése és a WVD-készlet alhálózati felhasználó által megadott útvonalának (UDR) konfigurálása az összes forgalom a Azure Firewall keresztüli átirányításához. Az alapértelmezett útvonal most a tűzfalra mutat.
- Hozzon létre egy alkalmazás-szabálygyűjtemény-gyűjteményt, és adjon hozzá egy szabályt az *WindowsVirtualDesktop* FQDN címke engedélyezéséhez. A forrás IP-címtartomány a gazdagépek virtuális hálózata, a protokoll pedig **https**, a cél pedig **WindowsVirtualDesktop**.

- A WVD-készlethez szükséges tárolási és Service Bus-fiókok készlete központi telepítési jellemző, ezért még nincs rögzítve a WindowsVirtualDesktop FQDN címkében. Ezt a következő módszerek egyikével kezelheti:

   - HTTPS-hozzáférés engedélyezése a gazdaszámítógép-alhálózatról * xt.blob.core.windows.net, * eh.servicebus.windows.net és * xt.table.core.windows.net. Ezek a helyettesítő karakterek teljes tartománynevei lehetővé teszik a szükséges hozzáférést, de kevésbé korlátozóak.
   - A következő log Analytics-lekérdezéssel sorolja fel a szükséges teljes tartományneveket, majd explicit módon engedélyezze őket a tűzfal alkalmazási szabályaiban:
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- Hozzon létre egy hálózati szabálygyűjtemény-gyűjteményt, és adja hozzá a következő szabályokat:

   - DNS engedélyezése – a 53-es TCP-és UDP-portok esetében engedélyezze a magánhálózati IP-cím hozzáadását.
   - A KMS engedélyezése – engedélyezi a WVD virtuális gépekről a Windows Activation Service 1688-es TCP-portra irányuló forgalmat. További információ a cél IP-címekről: a [Windows aktiválása sikertelen a kényszerített bújtatási forgatókönyvben](../virtual-machines/troubleshooting/custom-routes-enable-kms-activation.md#solution).

> [!NOTE]
> Előfordulhat, hogy egyes központi telepítések nem igényelnek DNS-szabályokat, például Azure Active Directory tartományvezérlők továbbítják a DNS-lekérdezéseket Azure DNS a következő helyen: 168.63.129.16.

## <a name="host-pool-outbound-access-to-the-internet"></a>A gazdagép kimenő hozzáférése az internethez

A szervezet igényeitől függően érdemes lehet biztonságos kimenő internet-hozzáférést biztosítani a végfelhasználók számára. Azokban az esetekben, amikor az engedélyezett célhelyek listája megfelelően van definiálva (például [Office 365-hozzáférés](https://docs.microsoft.com/Office365/Enterprise/office-365-ip-web-service)), a szükséges hozzáférés konfigurálásához Azure Firewall alkalmazás-és hálózati szabályok használhatók. Ez a végfelhasználói forgalmat közvetlenül az internetre irányítja a legjobb teljesítmény érdekében.

Ha egy meglévő helyszíni biztonságos webes átjáróval szeretné szűrni a kimenő felhasználói internetes forgalmat, a WVD-gazdagépen futó webböngészőket vagy más alkalmazásokat explicit proxy-konfigurációval is konfigurálhatja. Lásd például a következő témakört: [a Microsoft Edge parancssori kapcsolóinak használata a proxybeállítások konfigurálásához](https://docs.microsoft.com/deployedge/edge-learnmore-cmdline-options-proxy-settings). Ezek a proxybeállítások csak a végfelhasználói internet-hozzáférést befolyásolják, így a WVD platform kimenő forgalma közvetlenül Azure Firewallon keresztül érhető el.

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

Előfordulhat, hogy a követelményektől függően további tűzfalszabályok konfigurálására van szükség:

- NTP-kiszolgáló elérése

   Alapértelmezés szerint a Windows rendszerű virtuális gépek a 123-as UDP-porton keresztül csatlakoznak a time.windows.com-hez az idő szinkronizálása érdekében. Hozzon létre egy hálózati szabályt a hozzáférés engedélyezéséhez, vagy a környezetben használt időkiszolgálóhoz.


## <a name="next-steps"></a>További lépések

- További információ a Windows rendszerű virtuális asztali környezetről: [Mi az a Windows virtuális asztal?](../virtual-desktop/overview.md)