---
title: A WinRM konfigurálása az Azure virtuális gép létrehozása után | Azure Piactér
description: A Windows távkezelés (WinRM) konfigurálása az Azure által üzemeltetett virtuális gépek létrehozása után.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: dsindona
ms.openlocfilehash: 673fe1f31f6a8602225e7cde3bf1eb4c3b28b8a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278144"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>A Rendszerfelügyeleti webszolgáltatások konfigurálása a virtuális gép létrehozása után

Ez a cikk bemutatja, hogyan konfigurálhat egy meglévő Azure által üzemeltetett virtuális gépet (VM) a WinRM HTTPS-en keresztüli engedélyezéséhez.  Ez a konfiguráció csak windows-alapú virtuális gépekre vonatkozik, és a következő kétlépéses folyamatot igényli:

1. Engedélyezze a Portforgalmat a WinRM HTTPS protokollon keresztüli számára.  Ezt a beállítást az Azure Portalon konfigurálja a virtuális géphez.
2. Konfigurálja a virtuális gép, hogy a WinRM a megadott PowerShell-parancsfájlok futtatásával.


## <a name="enabling-port-traffic"></a>Portforgalom engedélyezése

A Https-alapú WinRM protokoll az 5986-os portot használja, amely alapértelmezés szerint nincs engedélyezve az Azure Marketplace-en kínált előre konfigurált Windows virtuális gépeken. A protokoll engedélyezéséhez az alábbi lépésekkel adjon hozzá egy új szabályt a hálózati biztonsági csoporthoz (NSG) az [Azure Portalon.](https://portal.azure.com)  Az NSG-kről további információt a [Biztonsági csoportok című témakörben talál.](https://docs.microsoft.com/azure/virtual-network/security-overview)

1.  Keresse meg a **virtuális gépek >**   < *virtuális* gépek>   ** név> Beállítások/Hálózat panelt.**
2.  Kattintson az NSG nevére (ebben a példában **a testvm11002-re)** a tulajdonságainak megjelenítéséhez:

    ![Hálózati biztonsági csoport tulajdonságai](./media/nsg-properties.png)
 
3. A **Beállítások csoportban**válassza **a Bejövő biztonsági szabályok lehetőséget** a panel megjelenítéséhez.
4. Kattintson **a +Hozzáadás** `WinRM_HTTPS` gombra az 5986-os TCP-portra kért új szabály létrehozásához.

    ![Bejövő hálózati biztonsági szabály hozzáadása](./media/nsg-new-rule.png)

5. Kattintson **az OK gombra,** ha befejezte az értékek beszállítását.  A bejövő biztonsági szabályok listájának a következő új bejegyzéseket kell tartalmaznia.

    ![Bejövő hálózati biztonsági szabályok felsorolása](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>A virtuális gép konfigurálása a WinRM engedélyezéséhez 

Az alábbi lépésekkel engedélyezheti és konfigurálhatja a Windows távfelügyeleti szolgáltatást a Windows virtuális gépen.   

1. Hozzon létre egy távoli asztali kapcsolatot az Azure által üzemeltetett virtuális géppel.  További információt a [Windows rendszerű Azure-alapú virtuális gépekhez való csatlakozás és bejelentkezés című témakörben talál.](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)  A fennmaradó lépések a virtuális gépen lesznek futtatva.
2. Töltse le a következő fájlokat, és mentse őket a virtuális gép egy mappájába:
    - [WinRM.ps1 konfigurálása](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Nyissa meg a **PowerShell-konzolt** emelt szintű jogosultságokkal (**Futtatás rendszergazdaként**). 
4. Futtassa a következő parancsot, megadva a szükséges paramétert: a virtuális gép teljesen minősített tartományneve (FQDN): <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Powershell konfigurációs parancsfájl 1](./media/powershell-file1.png)

    Ez a parancsfájl attól függ, hogy a másik két fájl ugyanabban a mappában van-e.


## <a name="next-steps"></a>További lépések

Miután konfigurálta a WinRM konfigurálta, készen áll [a virtuális gép üzembe helyezésére az összetevő virtuális gépekről.](./cpp-deploy-vm-vhd.md)
