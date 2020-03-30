---
title: Azure VMware-megoldás a CloudSimple-től – VCenter beállítása a magánfelhőben a vRealize automatizáláshoz
description: Ez a témakör azt ismerteti, hogy miként állítható be egy VMware vCenter-kiszolgáló a CloudSimple private cloud szolgáltatásban a VMware vRealize Automation végpontjaként
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024840"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>VCenter beállítása a VMware-hez való privát felhőben vMware vRealize Automation

VMware vCenter-kiszolgálót a CloudSimple Private Cloud szolgáltatásban a VMware vRealize Automation végpontjaként állíthat be.

## <a name="before-you-begin"></a>Előkészületek

A vCenter-kiszolgáló konfigurálása előtt végezze el az alábbi feladatokat:

* Konfiguráljon [egy helyek közötti VPN-kapcsolatot](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) a helyszíni környezet és a magánfelhő között.
* [Konfigurálja a helyszíni DNS-kérelmek DNS-továbbítását](on-premises-dns-setup.md) a magánfelhő DNS-kiszolgálóinak.
* Nyújtson be egy [támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) egy vRealize Automation IaaS felügyeleti felhasználó létrehozásához az alábbi táblázatban felsorolt engedélykészlettel.

| Attribútum értéke | Engedély |
------------ | ------------- |  
| Adattár |  Hely felszabadítása <br> Adattár tallózása |
| Adattár-fürt | Adattárfürt konfigurálása |
| Mappa | Mappa létrehozása <br>Mappa törlése |
| Globális |  Egyéni attribútumok kezelése<br>Egyéni attribútum beállítása |
| Network (Hálózat) | Hálózat hozzárendelése |
| Engedélyek | Engedélyek módosítása |
| Erőforrás | Virtuális gép hozzárendelése erőforráskészlethez<br>Kikapcsolt virtuális gép áttelepítése<br>Powered áttelepítése virtuális gépen |
| Virtuális gép leltára |  Létrehozás meglévő<br>Új létrehozása<br>Áthelyezés<br>Eltávolítás | 
| Virtuális gép interakciója |  CD-adathordozó konfigurálása<br>Konzollal való interakció<br>Eszközkapcsolat<br>Kikapcsolás<br>Bekapcsolás<br>Alaphelyzetbe állítás<br>Felfüggesztés<br>Eszközök telepítése | 
| Virtuális gép konfigurációja |  Meglévő lemez hozzáadása<br>Új lemez hozzáadása<br>Hozzáadás vagy eltávolítás<br>Lemez eltávolítása<br>Speciális<br>Processzorok számának módosítása<br>Erőforrás módosítása<br>Virtuális lemez bővítése<br>Lemezváltozások nyomon követése<br>Memory (Memória)<br>Eszközbeállítások módosítása<br>Átnevezés<br>A Jegyzet beállítása (5.0-s és újabb verzió)<br>Beállítások<br>Felcserélő fájl elhelyezése |
| Kiépítés |  Testreszabás<br>Klónozó sablon<br>Virtuális gép klónozása<br>Sablon üzembe helyezése<br>Testreszabási specifikációk olvasása |
| Virtuális gép állapota | Pillanatkép létrehozása<br>Pillanatkép eltávolítása<br>Visszaállítás a pillanatképhez |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Telepítse a vRealize Automation alkalmazást a helyszíni környezetben

1. Jelentkezzen be a vRealize Automation IaaS kiszolgálói készülékbe a CloudSimple-támogatás által az Ön számára létrehozott IaaS-rendszergazdaként.
2. Telepítsen egy vSphere-ügynököt a vRealize Automation végponthoz.
    1. Nyissa meg https://*vra-url*:5480/installer, ahol a *vra-url* az az URL- cím, amelyet a vRealize Automation felügyeleti felhasználói felületének eléréséhez használ.
    2. A telepítő letöltéséhez kattintson az **IaaS telepítőre.**<br>
    A telepítőfájl elnevezési konvenciója setup_*vra-url*@5480.exe.
    3. Indítsa el a telepítőt. Az üdvözlőképernyőn kattintson a **Tovább** gombra.
    4. Fogadja el a eula-t, és kattintson a **Tovább**gombra.
    5. Adja meg a bejelentkezési adatokat, kattintson **a Tanúsítvány elfogadása**gombra, majd a **Tovább**gombra.
    ![vRA hitelesítő adatok](media/configure-vra-endpoint-login.png)
    6. Válassza **az Egyéni telepítés** és **proxyügynökök lehetőséget,** majd kattintson a **Tovább**gombra.
    ![vRA telepítési típus](media/configure-vra-endpoint-install-type.png)
    7. Adja meg az IaaS-kiszolgáló bejelentkezési adatait, és kattintson a **Tovább**gombra. Active Directory használata esetén adja meg a felhasználónevet **tartomány\felhasználó** formátumban. Ellenkező esetben **user@domain** használja a formátumot.
    ![vRA bejelentkezési adatok](media/configure-vra-endpoint-account.png)
    8. A proxybeállításokhoz írja be a **vSphere** for **Agent type parancsot.** Adja meg az ügynök nevét.
    9. Írja be az IaaS-kiszolgáló teljes tartománynát a **Manager-szolgáltatásgazda** és a **Model Manager webszolgáltatás-állomás** mezőkbe. Kattintson a **Teszt** gombra az egyes teljes qdn-értékek kapcsolatának teszteléséhez. Ha a teszt sikertelen, módosítsa a DNS-beállításokat úgy, hogy az IaaS-kiszolgáló állomásneve feloldódjon.
    10. Adja meg a virtuális központ kiszolgálóvégpontjának nevét a magánfelhőhöz. Rögzítse a nevet a konfigurációs folyamat későbbi részében történő használatra.

        ![vRA telepítési proxy](media/configure-vra-endpoint-proxy.png)

    11. Kattintson a **Tovább** gombra.
    12. Kattintson a **Telepítés gombra.**

## <a name="configure-the-vsphere-agent"></a>A vSphere-ügynök konfigurálása

1. Nyissa meg https://*vra-url*/vcac kapcsolót, és jelentkezzen be **ConfigurationAdmin**néven.
2. Válassza **ki az infrastruktúra** > **végpontjainak** > **végpontjait.**
3. Válassza az **Új** > **virtuális** > **vSphere**lehetőséget.
4. Adja meg az előző eljárásban megadott vSphere végpont nevét.
5. Cím **esetén**adja meg a Private Cloud vCenter Server URL-címét https://*vcenter-fqdn*/sdk formátumban, ahol a *vcenter-fqdn* a vCenter-kiszolgáló neve.
6. Adja meg a hitelesítő adatokat a vRealize Automation IaaS felügyeleti felhasználó, amely CloudSimple támogatás által létrehozott.
7. Kattintson a **Kapcsolat tesztelése** gombra a felhasználói hitelesítő adatok érvényesítéséhez. Ha a teszt sikertelen, ellenőrizze az URL-címet, a fiókadatokat és a [végpont nevét,](#verify-the-endpoint-name) és tesztelje újra.
8. Sikeres teszt után kattintson az **OK** gombra a vSphere-végpont létrehozásához.
    ![vRA-végpontkonfigurációs hozzáférés](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>A végpont nevének ellenőrzése

A megfelelő vCenter-kiszolgálói végpontnév azonosításához tegye a következőket:

1. Nyisson meg egy parancssort az IaaS-készüléken.
2. Módosítsa a könyvtárat C:\Program Files (x86)\VMware\vCAC\Agents\agent-name név re, ahol az *ügynöknév* a vCenter-kiszolgáló végpontjának a hozzárendelt neve.
3. Futtassa a következő parancsot.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

A kimenet hasonló a következőhöz. A `managementEndpointName` mező értéke a végpont neve.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
