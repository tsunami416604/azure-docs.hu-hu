---
title: A WinRM beállítása után az Azure virtuális gép létrehozása |} A Microsoft Docs
description: Azt ismerteti, hogyan konfigurálhatja a Windows Rendszerfelügyeleti (webszolgáltatások WinRM) egy Azure-ban üzemeltetett virtuális gép a létrehozása után.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 6350d5bde737c46cf14a9aef75a7ec57260a6afa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197140"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>A WinRM beállítása után a virtuális gép létrehozása

Ez a cikk ismerteti, hogyan konfigurálhatja egy meglévő Azure-ban üzemeltetett virtuális gép (VM) engedélyezéséhez a WinRM HTTPS-kapcsolaton keresztül.  Ez a konfiguráció csak a Windows-alapú virtuális gépek vonatkozik, és megköveteli a következő két lépésből álló folyamat:

1. Forgalom engedélyezése a Rendszerfelügyeleti webszolgáltatások HTTPS protokollon keresztül.  Ez a beállítás a virtuális gép fogja beállítani az Azure Portalon.
2. Konfigurálja a virtuális Gépet, a Rendszerfelügyeleti webszolgáltatások engedélyezése a megadott PowerShell-parancsfájlok futtatásakor.


## <a name="enabling-port-traffic"></a>Forgalom engedélyezése

A WinRM HTTPS protokollon keresztül-portot használja 5896, amely az Azure piactéren kínált előre konfigurált Windows virtuális gépek alapértelmezés szerint nincs engedélyezve. Ez a protokoll engedélyezéséhez használja az alábbi lépéseket a hálózati biztonsági csoport (NSG) az új szabály hozzáadása a [az Azure portal](https://portal.azure.com).  NSG-kkel kapcsolatos további információkért lásd: [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  A panelre való **virtuális gépek >**  <*virtuális gép neve*>  **> Beállítások/hálózati**.
2.  Kattintson az NSG neve (ebben a példában **testvm11002**) a tulajdonságainak megjelenítéséhez:

    ![Hálózati biztonsági csoport tulajdonságai](./media/nsg-properties.png)
 
3. A **beállítások**válassza **bejövő biztonsági szabályok** megjelenítéséhez ezt a panelt.
4. Kattintson a **+ Hozzáadás** nevű új szabály létrehozása `WinRM_HTTPS` TCP portra az 5986-os.

    ![Bejövő hálózati biztonsági szabály felvétele](./media/nsg-new-rule.png)

5. Kattintson a **OK** oktatóanyag végére értékeket biztosítja.  A bejövő biztonsági szabályok listájának tartalmaznia kell a következő új bejegyzéseket.

    ![Bejövő hálózati biztonsági szabályok listája](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>WinRM engedélyezése a virtuális gép konfigurálása 

A következő lépések segítségével engedélyezheti és konfigurálhatja a Windows távoli felügyelet funkció a Windows virtuális Gépen.   

1. Az Azure-ban üzemeltetett virtuális gépek távoli asztali kapcsolatot létesíteni.  További információkért lásd: [hogyan csatlakozhat, és jelentkezzen be a Windows rendszert futtató Azure virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  A virtuális gép futni fog a hátralévő lépéseket.
2. Töltse le a következő fájlokat, és mentse őket egy mappába a virtuális Gépen:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Nyissa meg a **PowerShell-konzolt** emelt szintű jogosultságokkal (**Futtatás rendszergazdaként**). 
4. Futtassa a következő parancsot, és biztosítja a szükséges paraméter: a virtuális gép teljesen minősített tartománynevét (FQDN): <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![PowerShell-konfigurációs parancsprogram 1](./media/powershell-file1.png)

    Ez a szkript attól függ, hogy a többi két fájl ugyanabban a mappában folyamatban.


## <a name="next-steps"></a>További lépések

Miután konfigurálta a Rendszerfelügyeleti webszolgáltatások, készen áll [annak alkotó VHD-ből a virtuális gép üzembe helyezése](./cpp-deploy-vm-vhd.md).
