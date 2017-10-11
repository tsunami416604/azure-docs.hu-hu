---
title: "A mobilitási szolgáltatást a fizikai kiszolgáló telepítése Azure replikációs |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan telepíthet a mobilitási szolgáltatás ügynöke fizikai kiszolgálók replikálása Azure-bA az Azure Site Recovery szolgáltatással."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: d73267d7a64221a3138af19e9a2d5dd15809b927
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="step-9-install-the-mobility-service"></a>9. lépés: A mobilitási szolgáltatás telepítése


Ez a cikk ismerteti, hogyan kell telepíteni a mobilitási szolgáltatás összetevőt, a helyszíni windowsos/Linuxos fizikai kiszolgálók replikálása Azure-ba, amikor használatával a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás az Azure portálon.

A mobilitási szolgáltatást a gépen végbemenő adatírásokat, és továbbítja őket a folyamatkiszolgálónak. Az Azure-bA replikálni kívánt összes kiszolgálón kell telepíteni.

Telepítheti a mobilitási szolgáltatást manuálisan, vagy a Site Recovery folyamat kiszolgálóról ügyfélleküldéses telepítést használ, ha engedélyezve van a replikáció, vagy egy eszköz, például a System Center Configuration Manager segítségével. Ha ügyfélleküldéses telepítést használ, a szolgáltatás telepítve van a kiszolgálón replikációs engedélyezésekor.

Ebben a cikkben, vagy az alsó megjegyzések és kérdéseket küldje a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="install-manually"></a>Manuális telepítés

1. Ellenőrizze a [Előfeltételek](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites) manuális telepítésre.
2. Hajtsa végre a [ezeket az utasításokat](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) manuális telepítésre a portál használatával.
3. Ha inkább a parancssorból telepíti, hajtsa végre az [ezeket az utasításokat](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt).

## <a name="install-from-the-process-server"></a>A folyamatkiszolgáló telepítése

Ha azt szeretné, majd a mobilitási szolgáltatás telepítési adatok a folyamatkiszolgálótól, ha engedélyezi a gépek replikálása, egy a gép eléréséhez használható a folyamatkiszolgáló-fiók szükséges. A fiók csak szolgál a leküldéses telepítés.

1. Ha még nem hozott létre egy fiókot, ehhez használja ezeket az irányelveket:

    - A tartományi vagy helyi fiók használható
    - A Windows Ha nem használ egy olyan tartományi fiók szeretné tiltsa le a távoli felhasználói hozzáférés-vezérlés a helyi számítógépen. Ehhez a nyilvántartásában **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adja hozzá a DWORD bejegyzést **LocalAccountTokenFilterPolicy**, 1 értékű.
    - Ha azt szeretné, a beállításjegyzék-bejegyzés hozzáadása a Windows a a CLI-t, írja be:

        ```
        REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.
        ```

    - A Linux a fióknak kell lennie a forráskiszolgálón Linux legfelső szintű.

2. Hajtsa végre [ezeket az utasításokat](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) Ha azt szeretné, hogy a mobilitási szolgáltatás leküldéses Windows vagy Linux rendszerű virtuális gépeken.

## <a name="other-installation-methods"></a>Egyéb telepítési módszerek

- [További tudnivalók](site-recovery-install-mobility-service-using-sccm.md) a mobilitási szolgáltatást a Configuration Manager telepítése
- [További tudnivalók](site-recovery-automate-mobility-service-install.md) telepítése az Azure Automation DSC Szolgáltatásban.


## <a name="next-steps"></a>Következő lépések

Ugrás a [10. lépés: replikálás engedélyezése](physical-walkthrough-enable-replication.md)
