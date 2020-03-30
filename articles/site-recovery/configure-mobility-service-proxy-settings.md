---
title: A Mobility Service Proxy beállításainak konfigurálása az Azure és az Azure katasztrófa-helyreállítási célokra | Microsoft dokumentumok
description: A mobilitási szolgáltatás konfigurálásának részleteit tartalmazza, ha az ügyfelek proxyt használnak a forráskörnyezetben.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503126"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>A mobility service proxy beállításainak konfigurálása az Azure és az Azure vész-helyreállítási

Ez a cikk útmutatást nyújt a hálózati konfigurációk testreszabásához a cél Azure virtuális gépen (VM), amikor replikálja és helyreállíta az Azure virtuális gépeket egyik régióból a másikba az [Azure Site Recovery](site-recovery-overview.md)használatával.

Ez a dokumentum célja, hogy lépéseket adjon az Azure Site Recovery Mobility Service proxybeállításainak konfigurálásához az Azure-tól az Azure-ig történő vész-helyreállítási forgatókönyvben. 

A proxyk olyan hálózati átjárók, amelyek engedélyezik/lehetővé teszik a hálózati kapcsolatokvégpontok számára. A proxy általában az ügyfélgépen kívüli gép, amely megpróbálja elérni a hálózati végpontokat. A megkerülési lista lehetővé teszi, hogy az ügyfél közvetlenül a végpontokhoz létesítsen kapcsolatokat anélkül, hogy a proxyn keresztül haladna. A hálózati rendszergazdák szükség esetén proxyhoz is beállíthatnak felhasználónevet és jelszót, hogy csak a hitelesített ügyfelek használhassák a proxyt. 

## <a name="before-you-start"></a>Előkészületek

Ismerje meg, hogyan biztosít a Site Recovery vészhelyreállítást ebben a [forgatókönyvben.](azure-to-azure-architecture.md)
Ismerje meg a [hálózati útmutatást,](azure-to-azure-about-networking.md) amikor replikálja és helyreállíta az Azure-beli virtuális gépeket egyik régióból a másikba az [Azure Site Recovery](site-recovery-overview.md)használatával.
Győződjön meg arról, hogy a proxy megfelelően van beállítva a szervezet igényei nek megfelelően.

## <a name="configure-the-mobility-service"></a>A mobilitási szolgáltatás konfigurálása

A Mobility Service csak a nem hitelesített proxykat támogatja. A Site Recovery végpontokkal való kommunikációhoz két módon adhat meg proxyrészleteket. 

### <a name="method-1-auto-detection"></a>1. módszer: Automatikus észlelés

A Mobility Service automatikusan észleli a proxybeállításokat a környezeti beállításokból vagy az IE-beállításokból (csak Windows) a replikáció engedélyezése során. 

- Windows operációs rendszer: A replikáció engedélyezése során a Mobilszolgáltatás észleli az Internet Explorer helyi rendszerfelhasználóhoz konfigurált proxybeállításait. A helyi rendszerfiók proxyjának beállításához a rendszergazda a psexec segítségével elindíthatja a parancssort, majd az Internet Explorer t. 
- Windows operációs rendszer: A proxybeállítások környezeti változókként vannak konfigurálva http_proxy és no_proxy. 
- Linux operációs rendszer: A proxybeállítások az /etc/profile vagy /etc/environment kapcsolókban vannak konfigurálva, http_proxy, no_proxy. 
- Az automatikusan észlelt proxybeállításoka Mobility Service proxy config file ProxyInfo.conf 
- A ProxyInfo.conf alapértelmezett helye 
    - Windows: C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux: /usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>2. módszer: Egyéni alkalmazásproxy-beállítások megadása

Ebben az esetben az ügyfél egyéni alkalmazásproxy-beállításokat biztosít a Mobility Service config file ProxyInfo.conf fájlban. Ez a módszer lehetővé teszi az ügyfelek számára, hogy proxy csak a Mobility Service vagy egy másik proxy az Azure Site Recovery Mobility Service, mint egy proxy (vagy nem proxy) a többi alkalmazás a gépen.

## <a name="proxy-template"></a>Proxysablon
A ProxyInfo.conf a következő sablonthttp://1.2.3.4 tartalmazza [proxy] Address= Port=5678 BypassList=hypervrecoverymanager.windowsazure.com,login.microsoftonline.com,blob.core.windows.net. A BypassList nem támogatja a helyettesítő karaktereket, mint a "*.windows.net", de windows.net adni elég jó ahhoz, hogy megkerülje. 

## <a name="next-steps"></a>Következő lépések:
- Olvassa el az Azure virtuális gépek replikálásával [kapcsolatos hálózati útmutatót.](site-recovery-azure-to-azure-networking-guidance.md)
- Üzembe helyezése vész-helyreállítási [replikálásával Azure virtuális gépek.](site-recovery-azure-to-azure.md)