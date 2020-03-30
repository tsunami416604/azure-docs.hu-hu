---
title: Adott RDP-hibaüzenetek az Azure virtuális gépeihez | Microsoft dokumentumok
description: A Távoli asztali kapcsolat Azure-beli Windows-alapú virtuális géphez való használata során megjelenő konkrét hibaüzenetek ismertetése
keywords: Távoli asztal hiba,távoli asztali kapcsolathiba,nem lehet csatlakozni a virtuális géphez,távoli asztali hibaelhárítás
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 851c5eb4ebfee4e4a4836a07b51578dd2b0c68cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266870"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Azure-beli Windows virtuális gépek konkrét RDP-hibaüzeneteinek elhárítása
Előfordulhat, hogy egy adott hibaüzenetet kap, ha távoli asztali kapcsolatot használ egy Windows virtuális gép (VM) az Azure-ban. Ez a cikk a leggyakoribb hibaüzeneteket, valamint a megoldásukhoz szükséges hibaelhárítási lépéseket ismerteti. Ha problémái vannak a virtuális géphez az RDP használatával való csatlakozással, de nem talál konkrét hibaüzenetet, olvassa el a [Távoli asztal hibaelhárítási útmutatóját.](troubleshoot-rdp-connection.md)

Az egyes hibaüzenetekkel kapcsolatos tudnivalókat az alábbi témakörben talál:

* [A távoli munkamenet le lett választva, mert nincs elérhető távoli asztali licenckiszolgáló a licenc biztosításához.](#rdplicense)
* [A Távoli asztal nem találja a számítógép "nevét"](#rdpname).
* [Hitelesítési hiba történt. A helyi biztonsági hatósággal nem lehet kapcsolatba lépni.](#rdpauth)
* [Windows biztonsági hiba: A hitelesítő adatok nem működtek](#wincred).
* [Ez a számítógép nem tud csatlakozni a távoli számítógéphez](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A távoli munkamenet lelett választva, mert nincs elérhető távoli asztali licenckiszolgáló a licenc biztosításához.
Ok: A Távoli asztali kiszolgáló szerepkör 120 napos licencelési türelmi ideje lejárt, és licenceket kell telepítenie.

Kerülő megoldásként mentse az RDP-fájl helyi példányát a portálról, és futtassa ezt a parancsot a PowerShell parancssorában a csatlakozáshoz. Ez a lépés csak a kapcsolat licencelését tiltja le:

        mstsc <File name>.RDP /admin

Ha valójában nincs szüksége kettőnél több egyidejű távoli asztali kapcsolatra a virtuális géphez, a Kiszolgálókezelő segítségével eltávolíthatja a Távoli asztali kiszolgáló szerepkört.

További információt az Azure [vm sikertelen " Nincs elérhető távoli asztali licenckiszolgálók" című blogbejegyzésben talál.](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/)

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>A Távoli asztal nem találja a számítógép "nevét".
Ok: A számítógép Távoli asztali ügyfele nem tudja feloldani a számítógép nevét az RDP-fájl beállításaiban.

Lehetséges megoldások:

* Ha egy szervezet intranetjén van, győződjön meg arról, hogy a számítógép hozzáfér a proxykiszolgálóhoz, és https-forgalmat tud küldeni hozzá.
* Ha helyileg tárolt RDP-fájlt használ, próbálja meg a portál által létrehozott fájlt használni. Ez a lépés biztosítja, hogy a megfelelő DNS-nevet a virtuális gép, vagy a felhőszolgáltatás és a virtuális gép végpontportja. Itt van egy minta RDP fájl által generált portál:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Az RDP-fájl címrésze:

* A virtuális gép -t tartalmazó felhőszolgáltatás teljesen minősített tartományneve ("tailspin-azdatatier.cloudapp.net" ebben a példában).
* A távoli asztali forgalom végpontjának külső TCP-portja (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Hitelesítési hiba történt. A helyi biztonsági hatósággal nem lehet kapcsolatba lépni.
Ok: A cél virtuális gép nem találja a biztonsági hatóság a hitelesítő adatok felhasználónév része.

Ha a felhasználónév *securityauthority*\\*felhasználónév* (például CORP\User1) formában található, a *SecurityAuthority* rész vagy a virtuális gép számítógépneve (a helyi biztonsági hatóság számára) vagy egy Active Directory tartománynév.

Lehetséges megoldások:

* Ha a fiók helyi a virtuális gép, győződjön meg arról, hogy a virtuális gép neve helyesen van beírva.
* Ha a fiók Active Directory-tartományban található, ellenőrizze a tartománynév helyesírását.
* Ha Active Directory tartományi fiókról van szó, és a tartománynév helyesen van beírva, ellenőrizze, hogy van-e tartományvezérlő az adott tartományban. Gyakori probléma az Azure virtuális hálózatok, amelyek tartományvezérlők, hogy a tartományvezérlő nem érhető el, mert még nem indult el. Kerülő megoldásként tartományi fiók helyett helyi rendszergazdai fiókot is használhat.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows biztonsági hiba: A hitelesítő adatok nem működtek.
Ok: A cél virtuális gép nem tudja érvényesíteni a fiók nevét és jelszavát.

A Windows-alapú számítógépek ellenőrizhetik a helyi vagy tartományi fiók hitelesítő adatait.

* Helyi fiókok esetén használja a *ComputerName*\\*UserName* szintaxist (példa: SQL1\Admin4798).
* Tartományi fiókok esetén használja a *DomainName*\\*UserName* szintaxist (példa: CONTOSO\peterodman).

Ha a virtuális gép egy tartományvezérlő egy új Active Directory erdőben, a helyi rendszergazdai fiók, amely bejelentkezett, egy egyenértékű fiókot ugyanazzal a jelszóval az új erdő és a tartomány. A helyi fiók ezután törlődik.

Ha például a DC1\DCAdmin helyi fiókkal jelentkezett be, majd a virtuális gépet tartományvezérlőként léptette elő a corp.contoso.com tartomány új erdőjében, a DC1\DCAdmin helyi fiók törlődik, és egy új tartományi fiók (CORP\DCAdmin) ugyanazzal a jelszóval jön létre.

Győződjön meg arról, hogy a fióknév olyan név, amelyet a virtuális gép érvényes fiókként ellenőrizhet, és hogy a jelszó helyes.Make sure that the account name is a name that the virtual machine can verify as a valid account, and that the password is correct.

Ha módosítania kell a helyi rendszergazdai fiók jelszavát, olvassa [el a Jelszó alaphelyzetbe állítása vagy a Windows virtuális gépek Távoli asztal szolgáltatása című témakört.](reset-rdp.md)

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Ez a számítógép nem tud csatlakozni a távoli számítógéphez.
Ok: A csatlakozáshoz használt fiók nem rendelkezik távoli asztali bejelentkezési jogokkal.

Minden Windows-számítógépen van egy Helyi távoli asztali felhasználó csoport, amely azokat a fiókokat és csoportokat tartalmazza, amelyek távolról jelentkezhetnek be. A helyi rendszergazdák csoport tagjai is rendelkeznek hozzáféréssel, még akkor is, ha ezek a fiókok nem szerepelnek a Távoli asztal felhasználói helyi csoportban. Tartományhoz csatlakozó gépek esetén a helyi rendszergazdák csoport a tartomány rendszergazdáit is tartalmazza.

Győződjön meg arról, hogy a fiók, amelyet a csatlakozáshoz használ, rendelkezik távoli asztali bejelentkezési jogokkal. Kerülő megoldásként használjon tartományi vagy helyi rendszergazdai fiókot a Távoli asztalon keresztüli csatlakozáshoz. A kívánt fiók hozzáadásához használja a Microsoft Management Console beépülő modult (**Rendszereszközök > helyi felhasználók és csoportok > csoportok > Távoli asztali felhasználók )** beépülő modult.

## <a name="next-steps"></a>További lépések
Ha a hibák egyike sem történt, és ismeretlen probléma merült fel az RDP használatával való csatlakozással kapcsolatban, olvassa el a [Távoli asztal hibaelhárítási útmutatóját.](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* A virtuális gépeken futó alkalmazások elérésének hibaelhárítási lépéseit az [Azure virtuális gépeken futó alkalmazások hozzáférése hibaelhárítása](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című témakörben található.
* Ha problémái vannak a Secure Shell (SSH) használatával egy Linux-alapú virtuális géphez való csatlakozáshoz az Azure-ban, olvassa [el az SSH-kapcsolatok hibaelhárítása linuxos virtuális géphez az Azure-ban című témakört.](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

