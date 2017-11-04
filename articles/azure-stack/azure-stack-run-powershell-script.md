---
title: "Az Azure verem szoftverfejlesztői készlet telepítése |} Microsoft Docs"
description: "Ismerje meg, készítse elő a Azure verem Development Kit, és a PowerShell-parancsfájl a telepítés futtatása."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/17/2017
ms.author: erikje
ms.openlocfilehash: b67cabf0ecdb48f137bfcfbce95eee568a1c298d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Az Azure verem szoftverfejlesztői készlet telepítése

*A következőkre vonatkozik: Azure szoftverfejlesztői készletet*

Központi telepítése a [Azure verem szoftverfejlesztői készlet](azure-stack-poc.md), hajtsa végre az alábbi lépéseket:

1. [A központi telepítési csomag](https://azure.microsoft.com/overview/azure-stack/try/?v=try) a Cloudbuilder.vhdx eléréséhez.
2. [Készítse elő a cloudbuilder.vhdx](#prepare-the-development-kit-host) szoftverfejlesztői készlet telepíteni szeretné a számítógép (development kit fogadó) konfigurálása a asdk-installer.ps1 parancsfájl futtatásával. Ez a lépés után a development kit gazdagép fog indulni, a Cloudbuilder.vhdx.
3. [A csomag telepítése](#deploy-the-development-kit) a development kit gazdagépen.

> [!NOTE]
> A legjobb eredmények elérése érdekében akkor is, ha szeretné használni az Azure-verem leválasztott környezet esetén célszerű telepíteni, amíg csatlakozik az internethez. Ily módon a központi telepítéskor akkor lehet aktiválni a Windows Server 2016 próbaverzióját.
> 
> 

## <a name="download-and-extract-the-development-kit"></a>Töltse le és a csomag kibontása
1. A letöltés megkezdése előtt győződjön meg arról, hogy a számítógép megfelel-e a következő előfeltételek teljesülését:

   * A számítógépnek rendelkeznie kell legalább 60 GB szabad lemezterület szükséges.
   * [.NET-keretrendszer 4.6 (vagy újabb verzió)](https://aka.ms/r6mkiy) kell telepíteni.

2. [Ugrás az első lépések lap](https://azure.microsoft.com/overview/azure-stack/try/?v=try), adja meg a adatait, és kattintson a **Submit**.
3. A **a szoftverfrissítések letöltéséhez**, kattintson a **Azure verem szoftverfejlesztői készlet**.
4. Futtassa a letöltött AzureStackDownloader.exe fájlt.
5. Az a **Azure verem Development Kit letöltő** ablak, végezze el az 1 – 5.
6. A letöltés befejezése után kattintson **futtatása** a MicrosoftAzureStackPOC.exe elindításához.
7. Tekintse át a licencszerződés képernyőjén és a önkibontó varázsló információt, és kattintson a **következő**.
8. Tekintse át az adatvédelmi nyilatkozat képernyő, és a önkibontó varázsló információt, és kattintson a **következő**.
9. Válassza ki a célhelyet ki kell olvasni, kattintson a fájlok **következő**.
   * Az alapértelmezett érték: <drive letter>:\<aktuális mappa > \Microsoft Azure verem
10. Tekintse át a rendeltetési hely képernyő és a önkibontó varázsló információkat, majd a **kibontása** CloudBuilder.vhdx (~ 25 GB) és ThirdPartyLicenses.rtf fájlok kibontásához. Ez a folyamat eltarthat egy ideig.

> [!NOTE]
> A fájlok kibontása után törölheti a exe és bin fájlok visszanyeréséhez a számítógépen. Vagy áthelyezheti egy másik helyre, ezért, ha meg kell telepíteni, akkor ezeket a fájlokat nem kell újból letöltheti a fájlokat.
> 
> 

## <a name="prepare-the-development-kit-host"></a>Készítse elő a development kit állomás
1. Győződjön meg arról, hogy fizikailag a development kit állomással lépnek kapcsolatba, de (például KVM) fizikai konzol hozzáféréssel rendelkeznek. Ilyen hozzáféréssel kell rendelkeznie, a 13 alatt a fejlesztési kit gazdagép újraindítása után.
2. Ellenőrizze, hogy a development kit gazdagép megfelel-e a [minimális követelmények](azure-stack-deploy.md). Használhatja a [telepítési ellenőrző Azure verem](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) annak a követelmények ellenőrzéséhez.
3. Jelentkezzen be helyi rendszergazdaként a development kit gazdagépre.
4. Másolja, vagy helyezze át a CloudBuilder.vhdx fájlt a C:\ meghajtó (C:\CloudBuilder.vhdx) gyökérkönyvtárában.
5. Futtassa a következő töltse le a development kit telepítőfájl (asdk-installer.ps1) a c:\AzureStack_Installer mappába a development kit állomáson.
    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
    $LocalPath = 'c:\AzureStack_Installer'

    # Create folder
    New-Item $LocalPath -Type directory

    # Download file
    Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
    ```
6. Nyisson meg egy emelt szintű PowerShell-konzolt > a C:\AzureStack_Installer\asdk-installer.ps1 parancsprogrammal > kattintson **környezet előkészítése**.
7. Az a **Cloudbuilder válasszon vhdx** oldalon a telepítő, keresse meg és válassza ki az előző lépésben letöltött cloudbuilder.vhdx-fájlt.
8. Választható lehetőség: Ellenőrizze a **illesztőprogramok hozzáadásával** mezőben megadhat egy további illesztőprogramokat, a gazdagépen használni kívánt tartalmazó mappát.
9. Az a **választható beállítások** lapján adja meg a helyi rendszergazdai fiók a development kit állomás. Ezeket a hitelesítő adatokat nem ad meg, ha a telepítés során a gazdagép KVM elérésére lesz szüksége.
10. A is a **választható beállítások** lap, lehetősége van a állítsa be a következőket:
    - **Számítógépnév**: Ez a beállítás egy development kit állomás neve. A név FQDN követelményeknek kell megfelelniük, és lehet 15 karakternél rövidebb szerepnevet. Az alapértelmezett érték a Windows által generált véletlenszerű számítógép nevét.
    - **Időzóna**: időzónáját adja meg a development kit gazdagép. Az alapértelmezett érték (UTC-8:00) csendes-óceáni idő (Egyesült Államok és Kanada).
    - **Statikus IP-konfiguráció**: Beállítja azt a környezetet, hogy statikus IP-cím. Ellenkező esetben amikor a telepítő újraindítja a cloudbuilder.vhx, a hálózati adapterek vannak konfigurálva a DHCP.
11. Kattintson a **Tovább** gombra.
12. Ha az előző lépésben a statikus IP-konfigurációt, akkor most le kell:
    - Válasszon olyan hálózati adaptert. Győződjön meg arról is elérheti az adapter kattintás előtt **következő**.
    - Győződjön meg arról, hogy a **IP-cím**, **átjáró**, és **DNS** értékek megfelelőek, és kattintson a **következő**.
13. Kattintson a **következő** az előkészítési folyamat elindításához.
14. Az előkészítési azt jelzi, ha **befejezve**, kattintson a **következő**.
15. Kattintson a **Újraindítás most** a cloudbuilder.vhdx indul, és a telepítési folyamat folytatásához.

## <a name="deploy-the-development-kit"></a>A csomag telepítése
1. Jelentkezzen be helyi rendszergazdaként a development kit gazdagépre. Az előző lépésben megadott hitelesítő adatokat használja.

    > [!IMPORTANT]
    > Az Azure Active Directory-környezetekben Azure verem kell elérnie az interneten, közvetlenül vagy transzparens proxyra. A központi telepítés pontosan egy hálózati adapter támogatja a hálózatkezeléshez. Ha több hálózati adapterrel rendelkezik, győződjön meg arról, hogy csak egy engedélyezve van (és összes többi le vannak tiltva) a következő szakaszban az üzembe helyezési parancsfájl futtatása előtt.
    
2. Nyisson meg egy emelt szintű PowerShell-konzolt > a \AzureStack_Installer\asdk-installer.ps1 parancsprogrammal (amely lehet egy másik meghajtót a a Cloudbuilder.vhdx) > kattintson **telepítése**.
3. Az a **típus** mezőben válassza **Azure felhőbe** vagy **az AD FS**.
    - **Azure-felhőbe**: Azure Active Directory az identitásszolgáltató. A paraméter segítségével adjon meg egy adott könyvtár ahol az AAD-fiókba globális rendszergazdai jogosultságokkal rendelkezik. Az AAD-címtárában bérlő teljes neve. Például. onmicrosoft.com. 
    - **Az AD FS**: az alapértelmezett stamp címtárszolgáltatás az identitásszolgáltató esetén az alapértelmezett fiókot a bejelentkezéshez azurestackadmin@azurestack.local, és a jelszó használata a telepítés részeként megadott.
4. A **helyi rendszergazda jelszavát**, a a **jelszó** mezőbe, írja be a helyi rendszergazda jelszavát (amelynek meg kell egyeznie az aktuális beállított helyi rendszergazdai jelszót), és kattintson a **Következő**.
5. Válasszon olyan hálózati adaptert kíván használni a csomagot, és kattintson a **következő**.
6. Válassza ki a DHCP vagy statikus hálózati BGPNAT01 virtuális gép konfigurációja.
    - **DHCP** (alapértelmezett): A virtuális gép IP-hálózati konfigurációja lekérdezi a DHCP-kiszolgálótól.
    - **Statikus**: csak akkor használja ezt a beállítást, ha a DHCP nem rendelhető hozzá egy érvényes IP-címet az Internet eléréséhez Azure verem. Egy statikus IP-címet meg kell adni az alhálózati maszk hosszal (például 10.0.0.5/24).
7. Szükség esetén állítsa be a következő értékeket:
    - **VLAN-azonosító**: Beállítja a VLAN-azonosítót. Csak akkor használja ezt a beállítást, ha a gazdagép és a AzS-BGPNAT01 konfigurálnia kell a VLAN-Azonosítót a fizikai hálózaton (és az Internet) elérésére. 
    - **DNS-továbbító**: A DNS-kiszolgáló jön létre az Azure-verem központi telepítésének részeként. Lehetővé teszi a számítógépek belül a megoldás a stamp kívül nevek feloldására, adja meg a meglévő infrastruktúra DNS-kiszolgáló. A stamp a DNS-kiszolgáló ismeretlen névfeloldási erre a kiszolgálóra továbbítja.
    - **Kiszolgálót**: Ez a kötelező mező beállítja azt a kiszolgálót, és IP-címnek kell lennie. Egy IP-címének, látogasson el a [pool.ntp.org](http:\\pool.ntp.org) vagy time.windows.com pingelését. 
8. Kattintson a **Tovább** gombra. 
9. A a **hálózati kártya kártya tulajdonságainak ellenőrzése** lapon egy folyamatjelző sáv megjelenik. 
    - Ha a felirat látható **egy frissítést nem lehet letölteni**, kövesse az utasításokat az oldalon.
    - Ha felirat látható **befejezve**, kattintson a **következő**.
10. A **összegzés** kattintson **telepítés**.
11. Ha az Azure Active Directory központi telepítésének használata esetén, a rendszer kéri, Azure Active Directory globális rendszergazdai fiók hitelesítő adatait.
12. A telepítési folyamat is igénybe vehet néhány órát, amely során a rendszer automatikusan újraindítja a számítógépet egyszer.
   
   > [!IMPORTANT]
   > Ha azt szeretné, a telepítési folyamat figyelésére, jelentkezzen be azurestack\AzureStackAdmin felhasználóként. Ha jelentkezik be, és egy helyi rendszergazdai után a számítógép csatlakozik a tartományhoz, akkor nem látható a telepítés előrehaladását. Futtassa újra a központi telepítés, ne helyette bejelentkezés azurestack\AzureStackAdmin ellenőrzése, hogy fut-e.
   > 
   > 
   
    Ha a telepítés sikeres, a PowerShell-konzolon jeleníti meg: **COMPLETE: "Telepítés" művelet**.
   
Ha nem sikerül a telepítés, a következő futtassa újra a PowerShell-parancsfájlt használhatja ugyanazon emelt szintű PowerShell-ablakából:

```powershell
cd c:\CloudDeployment\Setup
.\InstallAzureStackPOC.ps1 -Rerun
```

Ezt a parancsfájlt a központi telepítés az utolsó lépésben sikeresen telepített újraindul.

Is [újratelepíteni](azure-stack-redeploy.md) teljesen.


## <a name="reset-the-password-expiration-to-180-days"></a>A jelszavak alaphelyzetbe 180 nap

Győződjön meg arról, hogy a jelszót ahhoz, hogy a development kit gazdagép nem jár le túl korán, központi telepítését követően tegye a következőket:

A jelszó-elévülési szabályzatának módosítása a Powershellből:
1. A Powershell-ablakban futtassa a parancsot; Set-ADDefaultDomainPasswordPolicy - MaxPasswordAge 180.00:00:00-identitás azurestack.local

A jelszó-elévülési szabályzatának manuális módosítása:
1. Nyissa meg a development kit gazdagép **csoportházirend-kezelő** , és keresse meg **csoportházirend-kezelő** – **erdő: azurestack.local** – **tartományok** – **azurestack.local**.
2. Kattintson a jobb gombbal **alapértelmezett tartományházirend** kattintson **szerkesztése**.
3. Navigáljon a a Csoportházirendkezelés-szerkesztő, **számítógép konfigurációja** – **házirendek** – **Windows-beállítások** – **biztonsági beállítások**– **Fiókházirendek** – **jelszóházirend**.
4. A jobb oldali ablaktáblában kattintson duplán a **jelszó maximális élettartama**.
5. Az a **jelszó maximális élettartama tulajdonságok** párbeszédpanelen módosítsa a **a jelszó lejár** 180 értékét, majd kattintson az **OK**.


## <a name="next-steps"></a>Következő lépések

[A PowerShell telepítése](azure-stack-powershell-configure-quickstart.md)

[Azure verem regisztrálása az Azure-előfizetéshez](azure-stack-register.md)

[Csatlakozás az Azure Stackhez](azure-stack-connect-azure-stack.md)

