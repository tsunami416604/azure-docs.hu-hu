---
title: Azure virtuális gépek meghatározott RDP hibaüzenetek |} Microsoft Docs
description: Megérteni a vonatkozó hibaüzeneteket, amelyek jelenhet meg tett kísérlet során használja Windows virtuális gépek távoli asztali kapcsolatot az Azure-ban
keywords: Távoli asztali hiba, a távoli asztali kapcsolat hiba, nem lehet csatlakozni a virtuális gép, távoli asztal – hibaelhárítás
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
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 8c027db917216d273b5fb36f8920d6fa2fb4c9a9
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072150"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>A Windows Azure-ban az adott RDP hibaüzenetek hibaelhárítása
Egy adott hibaüzenet jelenhet meg, az Azure-ban a távoli asztali kapcsolatot egy Windows rendszerű virtuális gép (VM) használatakor. Ez a cikk részletesen néhány, a hibát, és azok megoldását hibaelhárítási Gyakori hibaüzenetek. Ha csatlakozni a virtuális gép RDP Funkciót használnak a do azonban nem egy adott hibaüzenetet kapja, tekintse meg a [hibaelhárítási útmutatója a távoli asztal](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

További információ a vonatkozó hibaüzeneteket tekintse át a következőket:

* [A távoli munkamenet meg lett szakítva, mert nincs távoli asztali licenckiszolgálókat kínálnak az licenc](#rdplicense).
* [A távoli asztal nem találja a számítógépen "name"](#rdpname).
* [Hitelesítési hiba történt. Nem lehet kapcsolódni a helyi biztonsági szervezet](#rdpauth).
* [Windows biztonsági hiba: A hitelesítő adatok nem működött](#wincred).
* [Ez a számítógép nem tud kapcsolódni a távoli számítógép](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A távoli munkamenet meg lett szakítva, mert nincs távoli asztali licenckiszolgálókat licenc kínálnak.
OK: A 120 napos türelmi időszak a távoli asztal kiszolgálói szerepkör lejárt, és telepítenie kell licenceket.

A probléma megoldásához az RDP-fájlt a helyi másolatot mentenek a portálról, és futtassa ezt a parancsot egy PowerShell parancssorba való csatlakozáshoz. Ezzel a lépéssel letiltja az adott kapcsolathoz csak licencelési:

        mstsc <File name>.RDP /admin

Ha a virtuális gép több mint két egyidejű távoli asztali kapcsolatok ténylegesen nem szükséges, a Kiszolgálókezelő használatával távolítsa el a távoli asztal-kiszolgálói szerepkört.

További információkért lásd a következő blogbejegyzésben [Azure virtuális Gépen sikertelen, és "Nincs távoli asztali licenckiszolgálókat érhető el"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>A távoli asztal nem találja a számítógépen "name".
OK: A távoli asztali ügyfél a számítógépen nem oldható fel az RDP-fájl beállításait a számítógép nevét.

A lehetséges megoldásokról:

* Ha egy szervezet intraneten, ellenőrizze, hogy a számítógép hozzáfér a proxykiszolgáló-e, és küldhet HTTPS-forgalmat az.
* Ha helyileg tárolt RDP-fájlt használ, próbálja meg azt, amelyik a portálon állítja elő. Ez a lépés biztosítja, hogy rendelkezik-e a virtuális gép vagy a felhőszolgáltatás és a virtuális gép végpont portja helytelen DNS-nevét. Íme egy minta RDP-fájlt a portálon állítja elő:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

A cím része az RDP-fájlt tartalmaz:

* A teljesen minősített tartománynév a felhőalapú szolgáltatás, amely tartalmazza a virtuális gép ("Dejójáték-azdatatier.cloudapp.net" Ebben a példában).
* A külső TCP-portot a végpont a távoli asztal forgalmat (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Hitelesítési hiba történt. Nem lehet kapcsolódni a helyi biztonsági szervezet.
OK: A cél virtuális gép nem található a biztonsági jogosultságokat a felhasználó hitelesítő adatait részét.

A felhasználói név esetén a képernyő *SecurityAuthority*\\*felhasználónév* (Példa: corp\felhasználó1), a *SecurityAuthority* részére vagy a virtuális gép számítógép neve (a helyi biztonsági szervezet) vagy egy Active Directory-tartomány nevét.

A lehetséges megoldásokról:

* Ha a fiók nem helyi, a virtuális géphez, győződjön meg arról, hogy a virtuális gép nevét helyesen írta-e.
* Ha a fiók az Active Directory-tartomány, a helyesírás a tartomány neve.
* Ha egy Active Directory tartományi fiók, és a tartomány nevét helyesen írta-e, ellenőrizze, hogy a tartományvezérlő elérhető az adott tartományban. Ez a jelenség a tartományvezérlőnek, hogy a tartományvezérlő nem érhető el, mivel nincs elindítva az Azure virtuális hálózatokhoz. A probléma megoldásához használhatja egy helyi rendszergazdai fiók helyett egy olyan tartományi fiók.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows biztonsági hiba: A hitelesítő adatok nem működik.
OK: A cél virtuális gép nem lehet érvényesíteni a fióknevet és jelszót.

Egy Windows-alapú számítógép azt is ellenőrzi a helyi fiókkal vagy egy olyan tartományi fiók hitelesítő adatait.

* A helyi fiókok használata a *számítógépnév*\\*felhasználónév* szintaxis (Példa: SQL1\Admin4798).
* A tartományi fiókokat, használja a *tartománynév*\\*felhasználónév* szintaxis (Példa: CONTOSO\peterodman).

A virtuális Gépet egy olyan tartományvezérlőre, új Active Directory-erdőben található léptette be, ha a helyi rendszergazdai fiókkal jelentkezett be, ugyanezt a jelszót az új erdőben és tartományban egyenértékű fiókja lesz konvertálva. Majd a helyi fiókot törölték.

Például ha a helyi fiókkal DC1\DCAdmin bejelentkezve, és majd elő a virtuális gép a corp.contoso.com tartományhoz tartozó új erdő tartományvezérlőjeként, az DC1\DCAdmin helyi fiók lekérdezi törlődik, és egy új tartományi fiók (CORP\DCAdmin) hozza létre a jelszót.

Győződjön meg arról, hogy a fiók nevét a nevet, amely a virtuális gép ellenőrizheti egy érvényes fiókot, és a jelszó helyességéről.

Ha módosítania kell a helyi rendszergazdai fiók jelszavát, lásd: [egy jelszót, vagy a Windows virtuális gépekhez távoli asztal szolgáltatás alaphelyzetbe állításával](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Ez a számítógép nem tud kapcsolódni a távoli számítógépen.
OK: A való csatlakozáshoz használt fiók nem jogosult a távoli asztal bejelentkezhet.

Minden Windows-számítógép a távoli asztali felhasználók helyi csoport, amely tartalmazza a fiókokat és csoportokat, amelyek jelentkezhet be távolról rendelkezik. A helyi Rendszergazdák csoport tagjai is hozzáférhetnek, annak ellenére, hogy ezek a fiókok nem szerepelnek a távoli asztali felhasználók helyi csoporthoz. Tartományhoz csatlakozó gépek esetén a helyi Rendszergazdák csoport a tartomány a tartományi rendszergazdák is tartalmaz.

Győződjön meg arról, hogy a fiók összekötése használata távoli asztal bejelentkezési jogokkal. A probléma megoldásához használja a tartomány vagy helyi rendszergazdai fiók távoli asztali kapcsolaton keresztül csatlakozni. A távoli asztali felhasználók helyi csoporthoz a kívánt fiók hozzáadásához használja a Microsoft Management Console beépülő modult (**Rendszereszközök > helyi felhasználók és csoportok > csoportok > Remote Desktop Users**).

## <a name="next-steps"></a>További lépések
Ha ezek a hibák egyike sem történt, és az ismeretlen csatlakozás RDP segítségével adja ki, tekintse meg a [hibaelhárítási útmutatója a távoli asztal](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Című témakörben leírt lépéseket a virtuális gép futó alkalmazásokhoz való hozzáférés [egy Azure virtuális gépen futó alkalmazáshoz való hozzáférés hibáinak elhárítása](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Ha a Secure Shell (SSH) használatával szeretne csatlakozni egy Linux virtuális Gépet az Azure-ban, olvassa el a problémákat [hibaelhárítása SSH kapcsolatok egy Linux virtuális Gépet az Azure-ban](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

