---
title: Az Azure Stack Development Kit (ASDK) telepítése |} A Microsoft Docs
description: Az Azure Stack Development Kit (ASDK) telepítését ismerteti.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 09/10/2018
ms.openlocfilehash: 363e0868542f56df8c37639b2af7ac295be97da2
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249905"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Az Azure Stack Development Kit (ASDK) telepítése
Miután [a ASDK számítógép előkészítése](asdk-prepare-host.md), a ASDK is üzembe helyezhetők az alábbi lépéseket követve ebben a cikkben a CloudBuilder.vhdx lemezképpel.

## <a name="install-the-asdk"></a>Telepítse a ASDK
A jelen cikkben ismertetett lépések bemutatják, hogyan helyezhet üzembe a grafikus felhasználói felületen (GUI) megadott letöltésével és futtatásával ASDK a **asdk-installer.ps1** PowerShell-parancsfájlt.

> [!NOTE]
> Az Azure Stack Development Kit telepítő felhasználói felületének, egy nyílt forráskódú szkript alapján a WCF és a PowerShell használatával.


1. Miután a számítógép sikeresen CloudBuilder.vhdx lemezképpel indul el, jelentkezzen be rendszergazdai hitelesítő adatok használatával megadni, ha, [előkészítve a development kit gazdaszámítógép](asdk-prepare-host.md) ASDK telepítéséhez. Ez legyen ugyanaz, mint a development kit gazdagép helyi rendszergazdai hitelesítő adatokkal.
2. Nyisson meg egy rendszergazda jogú PowerShell-konzolt, és futtassa a  **&lt;rendszermeghajtó betűjele > \AzureStack_Installer\asdk-installer.ps1** szkriptet (amely most már lehet a CloudBuilder.vhdx kép C:\ meghajtón található). Kattintson az **Install** (Telepítés) gombra.

    ![](media/asdk-install/1.PNG) 

3. Az identitásszolgáltató **típus** legördülő mezőben válassza ki **Azure felhő** vagy **az AD FS**. A **helyi rendszergazdai jelszó** (amelynek meg kell egyeznie a jelenlegi konfigurált helyi rendszergazda jelszavát) a helyi rendszergazdai jelszót a **jelszó** mezőbe, majd kattintson a  **Tovább**.
    - **Azure Cloud**: Konfigurálja az Azure Active Directory (Azure AD) Identitásszolgáltatóként. Használja ezt a beállítást, van szükségük internetkapcsolatra, a teljes neve az Azure AD directory-bérlő formájában *domainname*. onmicrosoft.com vagy egy Azure AD ellenőrzött egyéni tartományának neve, és a globális rendszergazdai hitelesítő adatait a megadott a könyvtár. Az üzembe helyezést követően az Azure Active Directory globális rendszergazdának, nem szükséges. Egyes műveletek azonban szükség lehet a globális rendszergazdai hitelesítő adatok. Például egy erőforrás-szolgáltató szoftvertelepítő parancsfájl vagy egy új szolgáltatást igénylő engedélyt kell adni. Ideiglenesen újbóli regisztrációra a fiók globális rendszergazdai jogosultságokkal, vagy használjon egy külön globális rendszergazdai fiókkal, amely a tulajdonosa a *szolgáltatói előfizetés alapértelmezett*.
    - **AD FS**: Az alapértelmezett stamp címtárszolgáltatás használja Identitásszolgáltatóként. Jelentkezzen be az alapértelmezett fiók azurestackadmin@azurestack.local, és a jelszó használata a telepítés során megadottal.

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > A legjobb eredmények érdekében még akkor is, ha az AD FS használata Identitásszolgáltatóként, kapcsolat nélküli Azure Stack-környezet használni kívánt célszerű telepíteni a ASDK, ha az internethez csatlakozik. Ezzel a módszerrel a development kit telepítést része a Windows Server 2016 próbaverzió a központi telepítéskor aktiválható.
4. Válassza ki a csomagot használja, és kattintson egy hálózati adaptert **tovább**.

    ![](media/asdk-install/3.PNG)

5. Válassza ki a DHCP vagy statikus hálózati konfiguráció a BGPNAT01 virtuális gépen.
    > [!TIP]
    > A BGPNAT01 virtuális gép a peremhálózati útválasztó, amely NAT- és VPN-képességeket biztosít az Azure Stackhez.

    - **DHCP** (alapértelmezett): A virtuális gépet a DHCP-kiszolgáló az IP-hálózati konfiguráció beolvasása.
    - **Statikus**: Csak akkor használja ezt a beállítást, ha a DHCP nem rendelhető hozzá az Azure Stackhez az Internet eléréséhez érvényes IP-címet. **Statikus IP-címet kell megadni a CIDR-formátumban (például 10.0.0.5/24) alhálózati maszk hossza**.
    - Írjon be egy érvényes **idő a kiszolgáló IP-címe** címet. Ez szükséges mező állítja be a kiszolgálót a csomag által használható. Ez a paraméter egy érvényes idő-kiszolgáló IP-címét meg kell adni. Kiszolgálók nevei nem támogatottak.

      > [!TIP]
      > Egy idő-kiszolgáló IP-cím található, a Microsoft [pool.ntp.org](http://pool.ntp.org) , illetve pingelni time.windows.com. 

    - **Igény szerint**, akkor állítsa be a következő értékeket:
        - **VLAN-AZONOSÍTÓT**: Beállítja a VLAN-azonosítót. Csak akkor használja ezt a beállítást, ha a gazdagép és a AzS-BGPNAT01 konfigurálnia kell a VLAN-Azonosítót a fizikai hálózat (és internet) eléréséhez. 
        - **DNS-továbbító**: DNS-kiszolgáló létrehozása az Azure Stack üzembe helyezés részeként. Ahhoz, hogy a számítógépek belül a megoldás a stamp kívül nevek feloldása, adja meg a meglévő infrastruktúra DNS-kiszolgáló. A stamp a DNS-kiszolgáló ezen a kiszolgálón ismeretlen névfeloldási kérelmeket továbbítja.

    ![](media/asdk-install/4.PNG)

6. Az a **ellenőrzése a hálózati kártya tulajdonságai** lapon látni fog egy folyamatjelző sávot. Ellenőrzési befejeződése után kattintson a **tovább**.

    ![](media/asdk-install/5.PNG)

9. A **összefoglalás** kattintson **telepítés** ASDK telepítésének elindítása a development kit gazdagépen.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Itt is másolhatja a PowerShell telepítő-parancsokat a csomag telepítéséhez használt. Ez akkor hasznos, ha a átállítására lenne szükség [ismételt üzembe helyezése a PowerShell használatával a gazdagépen ASDK](asdk-deploy-powershell.md).

10. Az Azure AD központi telepítésének hajt végre, ha meg kéri, adja meg az Azure AD globális rendszergazdai fiók hitelesítő adatait, a telepítő indítása után néhány percet.

    ![](media/asdk-install/7.PNG)

11. Az üzembe helyezési folyamat eltarthat néhány óráig, amely idő alatt a számítógép automatikusan újraindul egyszer. Ha azt szeretné, a telepítési folyamat állapotának monitorozásához, jelentkezzen be felhasználóként azurestack\AzureStackAdmin a development kit gazdagép újraindítása után. Az üzembe helyezés sikeres, a PowerShell-konzolon jeleníti meg: **HAJTSA VÉGRE: A művelet "Telepítés"**. 
    > [!IMPORTANT]
    > Ha bejelentkezik egy helyi rendszergazdaként után a gép csatlakozik a tartományhoz, akkor nem jelenik meg az üzembe helyezés folyamatban. Futtassa újra a központi telepítés, ne inkább jelentkezhessen be azurestack\AzureStackAdmin ellenőrzése, hogy fut-e.

    ![](media/asdk-install/8.PNG)

Gratulálunk, sikeresen telepítette a ASDK!

Ha a központi telepítés valamilyen okból nem sikerül, akkor [ismételt üzembe helyezése](asdk-redeploy.md) teljesen új vagy használja az alábbi PowerShell-parancsokat, a azonos emelt szintű PowerShell-ablakban, indítsa újra a telepítésben az utolsó sikeres lépésből:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>További lépések
[Üzembe helyezés utáni konfigurálásra](asdk-post-deploy.md)
