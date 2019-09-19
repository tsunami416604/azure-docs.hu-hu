---
title: Adott RDP-hibaüzenetek az Azure-beli virtuális gépekhez | Microsoft Docs
description: Az Azure-beli Windows rendszerű virtuális gépekhez való Távoli asztal kapcsolódáskor kapott hibaüzenetek ismertetése
keywords: Távoli asztali hiba, távoli asztali kapcsolati hiba, nem lehet csatlakozni a virtuális géphez, távoli asztal hibaelhárítása
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
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088184"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Adott RDP-hibaüzenetek hibaelhárítása Az Azure-beli Windows rendszerű virtuális gépeken
A Windows rendszerű virtuális gépekhez (VM) az Azure-ban Távoli asztal-csatlakozás használatakor hibaüzenet jelenhet meg. Ez a cikk a leggyakoribb hibaüzenetek némelyikét ismerteti, valamint a hibaelhárítási lépéseket. Ha olyan problémák merülnek fel, amelyek RDP használatával kapcsolódnak a virtuális géphez, de nem találnak konkrét hibaüzenetet, tekintse [meg a távoli asztal hibaelhárítási útmutatóját](troubleshoot-rdp-connection.md).

Az adott hibaüzenetekről a következő témakörben talál további információt:

* [A távoli munkamenet le lett választva, mert nincs elérhető távoli asztal licenckiszolgálóra a licenc biztosításához](#rdplicense).
* [Távoli asztal nem találja a "Name" nevű számítógépet](#rdpname).
* [Hitelesítési hiba történt. Nem lehet felvenni a kapcsolatot](#rdpauth)a helyi biztonsági szolgáltatóval.
* [Windows biztonsági hiba: A hitelesítő adatai nem működnek](#wincred).
* [Ez a számítógép nem tud kapcsolódni a távoli számítógéphez](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A távoli munkamenet le lett választva, mert nincs elérhető Távoli asztal licenckiszolgálóra a licenc biztosításához.
Ok: A Távoli asztal kiszolgálói szerepkör 120 napos licencelési türelmi időszaka lejárt, és licenceket kell telepítenie.

Megkerülő megoldásként mentse az RDP-fájl egy helyi példányát a portálról, és futtassa ezt a parancsot egy PowerShell-parancssorból a kapcsolódáshoz. Ez a lépés letiltja a csak a következő kapcsolatok licencelését:

        mstsc <File name>.RDP /admin

Ha valójában nem szükséges több mint két egyidejű Távoli asztal kapcsolat a virtuális géppel, a Kiszolgálókezelő használatával távolíthatja el a Távoli asztal kiszolgálói szerepkört.

További információ: az Azure-beli [virtuális gép "nincs távoli asztal elérhető licenckiszolgálóra"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/)nevű blogbejegyzést.

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Távoli asztal nem találja a "Name" nevű számítógépet.
Ok: A számítógép Távoli asztal ügyfele nem tudja feloldani a számítógép nevét az RDP-fájl beállításaiban.

Lehetséges megoldások:

* Ha a szervezet intranetén található, győződjön meg arról, hogy a számítógépe rendelkezik hozzáféréssel a proxykiszolgálóhoz, és HTTPS-forgalmat tud küldeni a kiszolgálónak.
* Ha helyileg tárolt RDP-fájlt használ, próbálja meg használni a portál által generált ilyet. Ez a lépés biztosítja, hogy a virtuális gép, illetve a felhőalapú szolgáltatás és a virtuális gép végpont-portjának helyes DNS-neve legyen. Íme egy példa a portál által generált RDP-fájlra:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Az RDP-fájl címzési része a következő:

* A virtuális gépet tartalmazó felhőalapú szolgáltatás teljes tartományneve (ebben a példában a "tailspin-azdatatier.cloudapp.net").
* A végpont külső TCP-portja Távoli asztal forgalomhoz (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Hitelesítési hiba történt. Nem lehet felvenni a kapcsolatot a helyi biztonsági szolgáltatóval.
Ok: A célként megadott virtuális gép nem találja a biztonsági szolgáltatót a hitelesítő adatok Felhasználónév részében.

Ha a Felhasználónév a *SecurityAuthority*\\*Felhasználónév* formában van (például: Corp \ Felhasználó1) a *SecurityAuthority* része vagy a virtuális gép számítógépneve (a helyi biztonsági hatóságnál) vagy egy Active Directory tartománynév.

Lehetséges megoldások:

* Ha a fiók helyi a virtuális gép számára, győződjön meg arról, hogy a virtuális gép neve helyesen van-e írva.
* Ha a fiók egy Active Directory tartományban található, ellenőrizze a tartománynév helyesírását.
* Ha Active Directory tartományi fiók, és a tartománynevet helyesen írta be, ellenőrizze, hogy a tartományvezérlő elérhető-e az adott tartományban. Ez egy gyakori probléma az Azure-beli virtuális hálózatokban, amelyek olyan tartományvezérlőket tartalmaznak, amelyekben a tartományvezérlő nem érhető el, mert nem indult el. Megkerülő megoldásként tartományi fiók helyett helyi rendszergazdai fiókot is használhat.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows biztonsági hiba: A hitelesítő adatai nem működnek.
Ok: A cél virtuális gép nem tudja érvényesíteni a fiók nevét és jelszavát.

A Windows-alapú számítógépek ellenőrizhetik egy helyi fiók vagy egy tartományi fiók hitelesítő adatait.

* Helyi fiókok esetén használja a *számítógépnév*\\*felhasználóneve* szintaxisát (például: SQL1\Admin4798).
* Tartományi fiókok esetén használja a *tartománynév*\\*felhasználóneve* szintaxisát (például: CONTOSO\peterodman).

Ha előléptette a virtuális gépet egy új Active Directory erdőben lévő tartományvezérlőre, akkor a szolgáltatásba bejelentkezett helyi rendszergazdai fiók az új erdőben és tartományban ugyanazzal a jelszóval egyenértékű fiókba lesz konvertálva. Ekkor a helyi fiók törlődik.

Ha például bejelentkezett a helyi fiók DC1\DCAdmin, majd előléptette a virtuális gépet tartományvezérlőként egy új erdőben a corp.contoso.com tartományhoz, a DC1\DCAdmin helyi fiókja törölve lesz, és egy új tartományi fiók (CORP\DCAdmin) ugyanazzal a jelszóval lett létrehozva.

Győződjön meg arról, hogy a fiók neve olyan név, amelyet a virtuális gép érvényes fiókként tud ellenőrizni, és a jelszó helyes.

Ha módosítania kell a helyi rendszergazdai fiók jelszavát, tekintse meg a [jelszó alaphelyzetbe állítása vagy a távoli asztal szolgáltatás használata Windows rendszerű virtuális gépekhez](reset-rdp.md)című témakört.

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Ez a számítógép nem tud kapcsolódni a távoli számítógéphez.
Ok: A kapcsolódáshoz használt fiók nem rendelkezik Távoli asztal bejelentkezési jogosultságokkal.

Minden Windows-számítógép rendelkezik egy Távoli asztal felhasználó helyi csoporttal, amely tartalmazza azokat a fiókokat és csoportokat, amelyek távolról is bejelentkezhetnek. A helyi Rendszergazdák csoport tagjai is hozzáférhetnek, bár ezek a fiókok nem szerepelnek a Távoli asztal felhasználók helyi csoportjában. Tartományhoz csatlakozó gépek esetén a helyi Rendszergazdák csoport a tartományhoz tartozó tartományi rendszergazdákat is tartalmazza.

Győződjön meg arról, hogy a kapcsolódáshoz használt fiók Távoli asztal bejelentkezési jogosultságokkal rendelkezik. Megkerülő megoldásként használjon egy tartományi vagy helyi rendszergazdai fiókot Távoli asztalhoz való kapcsolódáshoz. Ha a kívánt fiókot hozzá szeretné adni a Távoli asztal felhasználók helyi csoportjához, használja a Microsoft Management Console beépülő modult (**Rendszereszközök > helyi felhasználók és csoportok > csoportok > Távoli asztal felhasználók**).

## <a name="next-steps"></a>További lépések
Ha a hibák egyike sem fordult elő, és az RDP-vel való kapcsolódással kapcsolatban ismeretlen probléma merült fel, tekintse meg a [Távoli asztal hibaelhárítási útmutatóját](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* A virtuális gépen futó alkalmazások elérésével kapcsolatos hibaelhárítási lépéseket lásd: az Azure-beli [virtuális gépen futó alkalmazásokhoz való hozzáférés hibaelhárítása](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Ha a Secure Shell (SSH) segítségével csatlakozik egy Linux rendszerű virtuális géphez az Azure-ban, tekintse meg az [SSH-kapcsolatok hibaelhárítása Az Azure-ban linuxos virtuális géppel](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című témakört.

