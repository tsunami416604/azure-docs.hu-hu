---
title: Labor beállítása a mobilalkalmazások fejlesztésének tanítására az Android Studio segítségével
titleSuffix: Azure Lab Services
description: Ismerje meg, hogyan állíthat be egy tesztkörnyezetet az Android Studio t használó mobilalkalmazás-fejlesztési osztály tanítására.  A cikk azt is megvitatja, hogy az Android Studio használata az Azure-ban egy virtuális gépen.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849785"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Labor beállítása az androidos mobilalkalmazás-fejlesztés oktatására az Android Studio segítségével

Ez a cikk bemutatja, hogyan állíthat be egy bevezető mobilalkalmazás-fejlesztési osztályt.  Ez az osztály azokra az Android mobilalkalmazásokra összpontosít, amelyek közzétehetők a [Google Play Áruházban.](https://play.google.com/store/apps)  A diákok megtanulják, hogyan kell használni [az Android Studio](https://developer.android.com/studio) alkalmazásokat.  [Az Android visual Studio emulátor](https://visualstudio.microsoft.com/vs/msft-android-emulator/) az alkalmazás helyi tesztelésére szolgál.

## <a name="lab-configuration"></a>Labor konfigurációja

A tesztkörnyezet beállításához azure-előfizetésre és laborfiókra van szükség a kezdéshez. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. Miután megkapja az Azure-előfizetést, létrehozhat egy új laborfiókot az Azure Lab Servicesben. Az új tesztkörnyezet-fiók létrehozásáról további információt a [tesztkörnyezet-fiók beállításához](tutorial-setup-lab-account.md)szükséges oktatóanyag című témakörben talál.  Meglévő tesztkörnyezet-fiókot is használhat.

Kövesse a [beállított tantermi labor oktatóanyag](tutorial-setup-classroom-lab.md) egy új labor, majd alkalmazza a következő beállításokat:

| Virtuális gép mérete | Kép |
| -------------------- | ----- |
| Közepes (beágyazott virtualizáció) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Sablongép konfigurációja

Amikor a sablongép létrehozása befejeződött, [indítsa el a gépet, és csatlakozzon hozzá](how-to-create-manage-template.md#update-a-template-vm) a következő feladatok elvégzéséhez:

1. Hyper-V szerepkör hozzáadása
2. Töltse le és telepítse a Java-t.  
3. Töltse le és telepítse a Visual Studio Emulátor t Androidra.
4. Töltse le és telepítse az Android Studio alkalmazást.
5. Konfigurálja a Visual Studio Emulátort az Android Studio számára.

## <a name="add-hyper-v-role"></a>Hyper-V szerepkör hozzáadása

A Hyper-V-t engedélyezni kell a Visual Studio Emulátor androidos sikeres telepítéséhez.  Kövesse a beágyazott virtualizálás engedélyezésére vonatkozó utasításokat [egy sablon virtuálisgép-cikkben.](how-to-enable-nested-virtualization-template-vm.md)

## <a name="install-java"></a>Java telepítése

Android Studio java szükséges.  A Java legújabb verziójának letöltéséhez kövesse az alábbi lépéseket.

1. Keresse meg a [Java letöltési lapját.](https://www.java.com/download/) Kattintson a **Java letöltés gombra.**
2. A 64 bites Windows for Java weboldalon kattintson az Elfogadom és az **Ingyenes letöltés indítása**gombra.
3. Amikor megjelenik **a Java telepítő** telepítője, kattintson a Telepítés **gombra.**
4. Várjon, amíg a telepítő címe **megváltozik**a Java telepítő – teljes .  Kattintson **a Bezárás** gombra.

## <a name="install-visual-studio-emulator-for-android"></a>Visual Studio Emulátor telepítése Androidra

Az Android-alkalmazások helyi teszteléséhez egy Android-eszköz virtualizált verzióját kell használnia.  Van néhány Android emulátorok elérhető, amely lehetővé teszi a fejlesztő, hogy teszteljék az alkalmazást a gép.  A Visual Studio Android-emulátort használjuk, mert ez egy emulátor, amely támogatja a beágyazott virtualizációt.  Mivel a Lab Service virtuális gép már egy virtuális gép, szükségünk van egy emulátor, amely támogatja a beágyazott virtualizáció.  Az Android Studio beépített emulátora nem támogatja a beágyazott virtualizációt.  Az emulátorok mely beágyazott virtualizációt támogatják, olvassa el [az emulátor teljesítményének hardveres gyorsítását (Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

Az alábbi utasításokat követve töltse le és telepítse az Android visual studio emulátort.

1. Nyissa meg a [Visual Studio Android-emulátor](https://visualstudio.microsoft.com/vs/msft-android-emulator/) kezdőlapját.
2. Kattintson **az Emulátor letöltése** gombra.
3. A vs_emulatorsetup.exe letöltésekor futtassa a végrehajtható fájlt.
4. Amikor megjelenik a Visual Studio beállítási párbeszédpanele, kattintson a **Telepítés** gombra.
5. Várja meg, amíg a telepítő befejeződik.  A számítógép újraindításához és a telepítés befejezéséhez kattintson az **Újraindítás gombra.**

Indítsa el először az emulátort, mielőtt az androidos stúdióval telepítené az alkalmazást.  Az Android visual studio emulátorról a [Visual Studio Android-emulátor dokumentációjában olvashat bővebben.](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)

## <a name="install-android-studio"></a>Az Android Studio telepítése

Kövesse az alábbi utasításokat az [Android Studio](https://developer.android.com/studio)letöltéséhez és telepítéséhez.

1. Keresse meg az [Android Studio letöltési oldalát.](https://developer.android.com/studio#downloads)  
    > [!NOTE]
    > Ez a webhely nem támogatja az Internet Explorer böngészőt.
2. Kattintson a Windows (64 bites) végrehajtható Android Studio csomagjára.
3. Olvassa el a jogi kifejezéseket írt a pop-up.  Ha készen áll a folytatásra, jelölje be, **hogy elolvastam és elfogadom a fenti feltételeket,** és kattintson az **Android Studio for Windows** letöltése gombra.
4. Miután az Android Studio telepítő futtatható letöltött, futtassa a futtatható.
5. Az **Android Studio telepítőjének** Üdvözli az Android Studio telepítőjének **üdvözlőlapján** kattintson a **Tovább**gombra.
6. A **Konfigurációs beállítások** lapon kattintson a **Tovább**gombra.
7. A **Start menü mappa kiválasztása** lapon kattintson a **Telepítés gombra.**
8. Várja meg, amíg a telepítés befejeződik.
9. A **Telepítés befejeződött** lapon kattintson a **Tovább gombra.**
10. Az **Android Studio beállításának befejezése** lapon.  Kattintson a **Befejezés** gombra.
11. Az Android Studio automatikusan elindul a telepítés befejezése után.
12. Az **Android-beállítások importálása...** párbeszédpanelen válassza a **Beállítások importálásának ne történt et.** Kattintson az **OK** gombra.
13. Az Android Studio **telepítővarázslójának** **üdvözlőlapján** kattintson a **Tovább**gombra.
14. A **Telepítés típusa** lapon válassza a **Normál**lehetőséget. Kattintson a **Tovább** gombra.
15. A **Felhasználói felület téma kiválasztása** lapon válassza ki a kívánt témát. Kattintson a **Tovább** gombra.
16. A **Beállítások ellenőrzése** lapon kattintson a **Tovább**gombra.
17. Az **Összetevők letöltése** lapon várja meg, amíg az összes összetevő letöltődik.  Kattintson a **Befejezés** gombra.

    > [!IMPORTANT]
    > A HAXM telepítése várhatóan sikertelen lesz.  A HAXM nem támogatja a beágyazott virtualizációt, ezért telepítettük a Visual Studio Emulátort Androidra a cikk korábbi korábbi cikkében.

18. Az **Üdvözli az Android Studio** párbeszédpanel jelenik meg, amikor a telepítővarázsló befejeződött.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Az Android Studio és a Visual Studio emulátorának konfigurálása Androidra

Az Android Studio majdnem készen áll a használatra.  Továbbra is meg kell mondani a Visual Studio Emulátor Android, ahol az Android SDK telepítve van.  Ez a Visual Studio for Android alkalmazásban futó emulátorokat az Android Studio hibakeresésének telepítési céljaiként jeleníti meg.

Meg kell állítanunk egy adott beállításkulcsot, hogy megmondjuk a Visual Studio Emulátor for Android-ot, ahol az Android Sdk található.  A szükséges beállításkulcs beállításához futtassa az alábbi parancsfájlt.  Az alábbi PowerShell-parancsfájl az Android Sdk alapértelmezett telepítési helyét feltételezi.  Ha az Android Sdk-t egy másik `$androidSdkPath` helyre telepítette, módosítsa a parancsfájl futtatása előtt az értéket.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Indítsa újra a Visual Studio Emulátort Android és Android Studio rendszerre, hogy az új beállítás használatban van.

Indítsa el a szükséges verziót a Visual Studio emulátorában.  Ez jelenik meg, mint a telepítési cél az Android app androidos stúdióban.  Az Android Studio projekt minimális verziójának támogatnia kell az Android Visual Studio Emulátorban futó verziót.  Most már készen áll arra, hogy projekteket hozzon létre és debugoljon az Android Studio és a Visual Studio Emulátor segítségével.  Ha bármilyen problémája van, olvassa el az Android emulátor hibaelhárításcímű témakört.

## <a name="cost"></a>Költségek

Ha meg szeretné becsülni a labor költségét, kövesse az alábbi példát.
Egy 25 fős, 20 órás ütemezett óraidővel és 10 óra kvótával rendelkező, házi feladatra vagy feladatokra vonatkozó kvótával rendelkező 25 fős osztály esetében a labor  

25 \* diák (20 ütemezett + 10 kvóta) óra * 55 Labor egység * 0,01 USD óránként = 412,5 USD

További részletek a díjszabásról az [Azure Lab Services díjszabása.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="next-steps"></a>További lépések

A következő lépések gyakoriak a tesztkörnyezet beállításában.

- [Sablon létrehozása és kezelése](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemezés beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail regisztrációs linkek diákok](how-to-configure-student-usage.md#send-invitations-to-users)
