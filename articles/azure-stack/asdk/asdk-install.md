---
title: Telepítse az Azure verem szoftverfejlesztői készlet (ASDK) |} Microsoft Docs
description: Az Azure verem Development Kit (ASDK) telepítését ismerteti.
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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 74a81901c8ad38a84357a9f3c2e1d948aa81e8bc
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084331"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Telepítse az Azure verem szoftverfejlesztői készlet (ASDK)
Miután [a ASDK számítógép előkészítése](asdk-prepare-host.md), a ASDK a CloudBuilder.vhdx lemezképpel ebben a cikkben az alábbi lépéseket követve telepíthető.

## <a name="install-the-asdk"></a>A ASDK telepítése
A cikkben ismertetett bemutatják a telepítése egy grafikus felhasználói felülete (GUI) letöltése és futtatása használatával ASDK a **asdk-installer.ps1** PowerShell-parancsfájlt.

> [!NOTE]
> Az Azure verem szoftverfejlesztői készlet telepítő felhasználói felületének megnyitása forrása parancsfájl a WCF és a PowerShell alapján.


1. Után a számítógép sikeresen elindul a CloudBuilder.vhdx lemezképpel, jelentkezzen be a rendszergazdai hitelesítő adatok használatával megadott mikor meg [előkészítve a development kit gazdaszámítógép](asdk-prepare-host.md) ASDK telepítéséhez. Ez legyen ugyanaz, mint a development kit állomás helyi rendszergazdai hitelesítő adatokat.
2. Nyisson meg egy emelt szintű PowerShell-konzolt, és futtassa a  **&lt;meghajtó betűjele > \AzureStack_Installer\asdk-installer.ps1** parancsfájlt (a CloudBuilder.vhdx kép C:\ meghajtón most lehet). Kattintson az **Install** (Telepítés) gombra.

    ![](media/asdk-install/1.PNG) 

3. Az identitásszolgáltató **típus** legördülő mezőben válassza **Azure felhőbe** vagy **AD FS**. A **helyi rendszergazdai jelszó** írja be a helyi rendszergazdai jelszót (amelynek meg kell egyeznie az aktuális beállított helyi rendszergazda jelszavát) az **jelszó** mezőbe, majd kattintson a  **Következő**.
    - **Azure-felhőbe**: konfigurálja az Azure Active Directory (Azure AD) identitás-szolgáltatóként. Használja ezt a beállítást, lesz szüksége az internethez, a teljes nevet, egy Azure ad directory-bérlő formájában *tartománynév*. onmicrosoft.com vagy egy Azure AD ellenőrizte az egyéni tartománynevet és globális rendszergazdai hitelesítő adatokat a megadott könyvtár. 
    - **Az AD FS**: az alapértelmezett stamp directory szolgáltatás használható-e az identitás-szolgáltatóként. Az alapértelmezett fiók a bejelentkezéshez azurestackadmin@azurestack.local, és a jelszó használata a telepítés részeként megadott.

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > A legjobb eredmények elérése érdekében akkor is, ha az AD FS segítségével az identitás-szolgáltatóként leválasztott Azure verem környezetben használni kívánt célszerű telepíteni a ASDK csatlakozik az internethez. Ily módon a development kit telepítés részét képező Windows Server 2016 próbaverzió akkor lehet aktiválni, a központi telepítéskor.
4. Válasszon olyan hálózati adaptert kíván használni a csomagot, és kattintson a **következő**.

    ![](media/asdk-install/3.PNG)

5. Válassza ki a DHCP vagy statikus hálózati BGPNAT01 virtuális gép konfigurációja.
    > [!TIP]
    > A BGPNAT01 virtuális gép, a peremhálózati útválasztó, amely Azure verem NAT- és VPN-képességeket biztosít.

    - **DHCP** (alapértelmezett): A virtuális gép IP-hálózati konfigurációja lekérdezi a DHCP-kiszolgálótól.
    - **Statikus**: csak akkor használja ezt a beállítást, ha a DHCP nem rendelhető hozzá egy érvényes IP-címet az Internet eléréséhez Azure verem. **Egy statikus IP-címet kell megadni az alhálózati maszk hosszának CIDR-formátumban (például 10.0.0.5/24)**.
    - Írja be egy érvényes **idő a kiszolgáló IP-címe** cím. Ez a kötelező mező beállítja azt a kiszolgálót a szoftverfejlesztői készlet által használható. Ez a paraméter egy érvényes idő kiszolgáló IP-címet meg kell adni. Kiszolgálók nevei nem támogatottak.

      > [!TIP]
      > Egy IP-címének, látogasson el a [pool.ntp.org](http://pool.ntp.org) vagy time.windows.com pingelését. 

    - **Opcionálisan**, akkor állítsa be a következő értékeket:
        - **VLAN-azonosító**: Beállítja a VLAN-azonosítót. Csak akkor használja ezt a beállítást, ha a gazdagép és a AzS-BGPNAT01 konfigurálnia kell a VLAN-Azonosítót a fizikai hálózaton (és az internet) elérésére. 
        - **DNS-továbbító**: A DNS-kiszolgáló jön létre az Azure-verem központi telepítésének részeként. Lehetővé teszi a számítógépek belül a megoldás a stamp kívül nevek feloldására, adja meg a meglévő infrastruktúra DNS-kiszolgáló. A stamp a DNS-kiszolgáló ismeretlen névfeloldási erre a kiszolgálóra továbbítja.

    ![](media/asdk-install/4.PNG)

6. A a **hálózati kártya kártya tulajdonságainak ellenőrzése** lapon egy folyamatjelző sáv megjelenik. Ha ellenőrzés befejeződött, kattintson a **következő**.

    ![](media/asdk-install/5.PNG)

9. A **összegzés** kattintson **telepítés** ASDK telepítés elindításához a development kit gazdaszámítógépen.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Itt is másolhatja a telepítés PowerShell-parancsokat, a csomag telepítéséhez használt. Ez akkor hasznos, ha bármikor át kívánja [telepítse újra az állomáson, PowerShell-lel ASDK](asdk-deploy-powershell.md).

10. Az Azure AD-telepítés helyreállítást hajt végre, ha a program megkéri adja meg Azure AD globális rendszergazdai fiók hitelesítő adatait a telepítő indítása után néhány perc múlva.

    ![](media/asdk-install/7.PNG)

11. A telepítés néhány órát, amely idő alatt a számítógép automatikusan újraindul egyszer vesz igénybe. Ha azt szeretné, a telepítési folyamat figyelésére, bejelentkezés azurestack\AzureStackAdmin a development kit gazdagép újraindítása után. Ha a telepítés sikeres, a PowerShell-konzolon jeleníti meg: **COMPLETE: "Telepítés" művelet**. 
    > [!IMPORTANT]
    > Ha jelentkezik be, és egy helyi rendszergazdai után a számítógép csatlakozik a tartományhoz, akkor nem látható a telepítés előrehaladását. Futtassa újra a központi telepítés, ne helyette bejelentkezés azurestack\AzureStackAdmin ellenőrzése, hogy fut-e.

    ![](media/asdk-install/8.PNG)

Gratulálunk, sikeresen telepítette a ASDK!

Ha valamilyen okból nem sikerül a telepítés, akkor [újratelepíteni](asdk-redeploy.md) teljesen új vagy használata a következő PowerShell-parancsok, azonos emelt szintű PowerShell-ablakából, az utolsó sikeres lépésben a központi telepítés újraindítása:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>További lépések
[POST központi telepítés konfigurálása](asdk-post-deploy.md)
