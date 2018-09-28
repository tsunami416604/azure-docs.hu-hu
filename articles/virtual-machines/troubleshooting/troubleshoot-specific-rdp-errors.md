---
title: RDP-hibaüzenetek az Azure virtuális gépek |} A Microsoft Docs
description: Ismerje meg, konkrét hibaüzeneteket, amelyek során előfordulhat, hogy kap egy Windows virtuális gép távoli asztali kapcsolatot használják az Azure-ban
keywords: Távoli asztali hiba, a távoli asztali kapcsolat hiba, nem lehet csatlakozni a virtuális gép távoli asztali hibaelhárítása
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 7d4deb0618b0e0802306e344fea6d259c0597fe2
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413806"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Adott Azure-beli Windows virtuális gép RDP-hibaüzenetek elhárítása
Egy adott hibaüzenet jelenhet meg az Azure-beli Windows virtuális gépek (VM) a távoli asztali kapcsolat használata esetén. Ez a cikk a Gyakori hibaüzenetek észlelt, és a hibaelhárítási lépéseket a problémák megoldásához némelyike részletezi. Ha problémái vannak a virtuális Géphez csatlakozni RDP-vel de ne nem egy adott hibaüzenetet kapja, lásd: a [hibaelhárítási útmutató a távoli asztal](troubleshoot-rdp-connection.md).

Információ a konkrét hibaüzeneteket tekintse meg a következőket:

* [A távoli munkamenet megszakadt, mert nem találhatók távoli asztali licenckiszolgáló kiszolgálók érhető el, amely licencet biztosíthatna](#rdplicense).
* [Távoli asztal nem találja a számítógép "name"](#rdpname).
* [Hitelesítési hiba történt. Nem lehet kapcsolódni a helyi biztonsági szervezet](#rdpauth).
* [Windows biztonsági hiba: nem működött a hitelesítő adatok](#wincred).
* [A számítógép nem lehet kapcsolódni a távoli számítógép](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A távoli munkamenet meg lett szakítva, mert nem találhatók távoli asztali licenckiszolgáló kiszolgálók, amely licencet biztosíthatna érhető el.
OK: Lejárt a 120 napos türelmi időszak a távoli asztal kiszolgálói szerepkör és a licencek telepítenie kell.

Áthidaló megoldásként mentse az RDP-fájl helyi másolatát a portálról, és futtassa a következő parancsot a PowerShell-parancssorba való csatlakozáshoz. Ebben a lépésben letiltja, hogy a kapcsolat csak licencelési:

        mstsc <File name>.RDP /admin

A virtuális gép több mint két egyidejű távoli asztali kapcsolatok ténylegesen nem szükségesek, ha a Kiszolgálókezelő segítségével távolítsa el a távoli asztal kiszolgálói szerepkör.

További információkért tekintse meg a következő blogbejegyzésben: [Azure virtuális gép meghibásodása esetén "Távoli asztali licencet kiszolgáló nélkül elérhető"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Távoli asztal nem találja a "name" számítógépen.
OK: A számítógép a távoli asztali ügyfél nem oldható fel a beállításait, az RDP-fájlt a számítógép nevét.

Lehetséges megoldások:

* Ha Ön a szervezet intraneten, győződjön meg arról, hogy a számítógép hozzáfér a proxykiszolgáló és HTTPS-forgalom elküldheti azt.
* Ha helyileg tárolt RDP-fájlt használ, próbáljon a portál által létrehozott egy. Ez a lépés biztosítja, hogy a virtuális gépet, vagy a felhőszolgáltatás és a virtuális gép a végponti port helyes-e DNS-nevét. Íme egy példa a portál által létrehozott RDP-fájlt:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

A cím része az RDP-fájl tartalmaz:

* A felhőalapú szolgáltatás, amely tartalmazza a virtuális gép ("tailspin-azdatatier.cloudapp.net" Ebben a példában) teljesen minősített tartománynevét.
* A külső TCP-port a végpont a távoli asztali forgalmat (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Hitelesítési hiba történt. Nem lehet kapcsolódni a helyi biztonsági szervezet.
OK: A cél virtuális gép nem található a biztonsági szervezettől a felhasználó hitelesítő adatait részét.

Ha a felhasználónév neve a *SecurityAuthority*\\*felhasználónév* (Példa: corp\felhasználó1), a *SecurityAuthority* rész nem a virtuális gép számítógép neve (a helyi biztonsági szervezettől) vagy egy Active Directory-tartomány nevét.

Lehetséges megoldások:

* Ha a fiók helyi a virtuális géphez, győződjön meg arról, hogy helyesen írta-e be a virtuális gép nevét.
* Ha a fiókot az Active Directory-tartományban, helyesírás-ellenőrzés az a tartomány neve.
* Ha egy Active Directory tartományi fiók, és a tartomány nevét helyesen írta-e, ellenőrizze, hogy a tartományvezérlő érhető el ebben a tartományban. Egy gyakori probléma az Azure virtuális hálózatok, amelyek tartalmazzák a tartományvezérlők, hogy a tartományvezérlő akkor nem érhető el, mert még nem lett elindítva. Áthidaló megoldásként használhatja a helyi rendszergazdai fiók egy tartományi fiók helyett.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows biztonsági hiba: nem működött a hitelesítő adatait.
OK: A céloldali virtuális Gépet nem lehet érvényesíteni a fiók nevét és jelszavát.

A Windows-alapú számítógépek ellenőrizheti a helyi vagy tartományi fiók hitelesítő adatait.

* Helyi fiókok esetében használja a *ComputerName*\\*felhasználónév* szintaxis (Példa: SQL1\Admin4798).
* A tartományi fiókokat, használja a *DomainName*\\*felhasználónév* szintaxis (Példa: CONTOSO\peterodman).

A virtuális gép egy olyan tartományvezérlőre, egy új Active Directory-erdőben léptette elő, ha a helyi rendszergazdai fiók, amely jelentkezett be ugyanezt a jelszót az új erdőben és tartományban egyenértékű fiók alakítja át. A helyi fiók majd törlődik.

Például ha a helyi DC1\DCAdmin fiókkal jelentkezik be, és ezután a corp.contoso.com tartományhoz tartozó új erdő tartományvezérlőjeként hitelesítenie kelljen magát a virtuális gép, törli a DC1\DCAdmin helyi fiók és egy új tartományi fiók (CORP\DCAdmin) létrehozott ugyanazt a jelszót.

Győződjön meg arról, hogy a fiók nevét, amely a virtuális gép ellenőrizheti egy érvényes fiók nevét, és, hogy helyesen szerepel-e a jelszó.

Ha a helyi rendszergazdai fiók jelszavát módosítani szeretné, tekintse meg [jelszó vagy a Windows virtuális gépek távoli asztali szolgáltatás visszaállítása](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Ez a számítógép nem tud kapcsolódni a távoli számítógépen.
OK: Való csatlakozáshoz használt fiók nem rendelkezik távoli asztali bejelentkezési jogokkal.

Minden Windows-számítógép, távoli asztali felhasználók helyi csoport, amely tartalmazza a fiókokat és csoportokat, hogy be tud jelentkezni, távolról. A helyi Rendszergazdák csoport tagjai is rendelkezik hozzáféréssel, annak ellenére, hogy azok a fiókok nem jelennek meg a felhasználók a távoli asztal helyi csoport. A tartományhoz csatlakoztatott gépek esetében a helyi Rendszergazdák csoport a tartomány a tartományi rendszergazdák is tartalmaz.

Győződjön meg arról, hogy a használatával csatlakozzon a fiók rendelkezik-e a távoli asztali bejelentkezési jogokkal. Áthidaló megoldásként használja a tartomány vagy helyi rendszergazdai fiók távoli asztali kapcsolaton keresztül csatlakozni. A távoli asztali felhasználók helyi csoporthoz a kívánt fiók hozzáadásához használja a Microsoft Management Console beépülő modul (**Rendszereszközök > helyi felhasználók és csoportok > csoportok > Remote Desktop Users**).

## <a name="next-steps"></a>További lépések
Ha ezek a hibák egyike sem történt, és egy ismeretlen probléma kapcsolódás RDP-vel, tekintse meg a [hibaelhárítási útmutató a távoli asztal](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Című témakörben leírt lépéseket egy virtuális gépen futó alkalmazásokhoz való hozzáférésüket [-beli virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Ha problémái vannak az Azure-beli Linuxos virtuális gép kapcsolódni, lásd: Secure Shell (SSH) használatával [hibaelhárítása SSH kapcsolatok az Azure-beli Linuxos VM](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

