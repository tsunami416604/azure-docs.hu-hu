---
title: Tesztkörnyezet létrehozása a mobileszköz-fejlesztés Android Studio
titleSuffix: Azure Lab Services
description: Megtudhatja, hogyan állíthat be egy labort a Android Studio-t használó adatmobil alkalmazásfejlesztés osztályának tanításához.  A cikk a Android Studio Azure-beli virtuális gépeken való használatakor végrehajtott módosításokat is ismerteti.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: ff996a096cddbb85f1e1c84cd051c18a7ab4ad79
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444981"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Tesztkörnyezet beállítása az adatmobil alkalmazások fejlesztéséhez Android Studio

Ez a cikk bemutatja, hogyan állíthat be bevezető mobileszköz-fejlesztési osztályt.  Ez az osztály a [Google Play áruház](https://play.google.com/store/apps)közzétett androidos mobil alkalmazásokra koncentrál.  A tanulók megtudhatják, hogyan hozhat létre alkalmazásokat a [Android Studio](https://developer.android.com/studio) használatával.  Az [Androidhoz készült Visual Studio Emulator](https://visualstudio.microsoft.com/vs/msft-android-emulator/) az alkalmazás helyi tesztelésére szolgál.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja

A tesztkörnyezet beállításához Azure-előfizetésre és labor-fiókra van szükség a kezdéshez. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/). Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services. Az új Labor-fiókok létrehozásával kapcsolatos további információkért lásd: [oktatóanyag a labor-fiók beállításához](tutorial-setup-lab-account.md).  Használhat meglévő labor-fiókot is.

Kövesse a [tantermi labor beállítása oktatóanyagot](tutorial-setup-classroom-lab.md) , és hozzon létre egy új labort, majd alkalmazza a következő beállításokat:

| Virtuális gép mérete | Kép |
| -------------------- | ----- |
| Közepes (beágyazott virtualizálás) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Sablon Számítógép-konfigurációja

A sablon létrehozása után [indítsa el a gépet, és kapcsolódjon](how-to-create-manage-template.md#update-a-template-vm) hozzá a következő feladatok elvégzéséhez:

1. Hyper-V szerepkör hozzáadása
2. Töltse le és telepítse a Java-t.  
3. Töltse le és telepítse az Androidhoz készült Visual Studio Emulatort.
4. Android Studio letöltése és telepítése.
5. Konfigurálja a Visual Studio Emulatort a Android Studiohoz.

## <a name="add-hyper-v-role"></a>Hyper-V szerepkör hozzáadása

A Hyper-V-nek engedélyezve kell lennie az Androidhoz készült Visual Studio Emulator sikeres telepítéséhez.  Kövesse a [beágyazott virtualizálás engedélyezése sablon virtuális gépekhez](how-to-enable-nested-virtualization-template-vm.md) című cikkben található utasításokat.

## <a name="install-java"></a>Java telepítése

A Android Studio Java-t igényel.  A Java legújabb verziójának letöltéséhez kövesse az alábbi lépéseket.

1. Navigáljon a [Java letöltési oldalára](https://www.java.com/download/). Kattintson a **Java-Letöltés** gombra.
2. Az 64-bites Windows for Java weblapon kattintson az **Elfogadom gombra, és indítsa el az ingyenes letöltést**.
3. Amikor megjelenik a **Java telepítőjének** telepítője, kattintson a **telepítés**gombra.
4. Várjon, amíg a telepítő címe **Java-telepítésre változik – Befejezés**.  Kattintson a **Bezárás** gombra.

## <a name="install-visual-studio-emulator-for-android"></a>A Visual Studio Emulator telepítése Android rendszerre

Az Android-alkalmazások helyi teszteléséhez az Android-eszköz virtualizált verzióját kell használnia.  Van néhány elérhető Android-emulátor, amely lehetővé teszi, hogy a fejlesztő tesztelje alkalmazását a gépről.  Visual Studio Emulatort használunk az Androidhoz, mivel ez egy olyan emulátor, amely támogatja a beágyazott virtualizálás szolgáltatást.  Mivel a labor szolgáltatás virtuális gépe már virtuális gép, a beágyazott virtualizálás támogató Emulátorra van szükségünk.  A Android Studio beépített emulátora nem támogatja a beágyazott virtualizálás szolgáltatást.  Ha szeretné megtekinteni, hogy mely emulátorok támogatják a beágyazott virtualizálás szolgáltatást, tekintse meg a következő témakört: [Hardvergyorsítás az emulátor teljesítményéhez (Hyper-& V](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration)

Az Androidhoz készült Visual Studio Emulator letöltéséhez és telepítéséhez kövesse az alábbi utasításokat.

1. Navigáljon az [Androidhoz készült Visual Studio Emulator](https://visualstudio.microsoft.com/vs/msft-android-emulator/) weboldalára.
2. Kattintson az **emulátor letöltése** gombra.
3. vs_emulatorsetup.exe letöltésekor futtassa a végrehajtható fájlt.
4. Amikor megjelenik a Visual Studio telepítése párbeszédpanel, kattintson a **telepítés** gombra.
5. Várjon, amíg a telepítő befejeződik.  Kattintson az **Újraindítás most** gombra a számítógép újraindításához és a telepítés befejezéséhez.

Először indítsa el az emulátort, mielőtt a Android Studio használatával telepítené az alkalmazást.  További információ az Androidhoz készült Visual Studio Emulatorról: [Visual Studio Emulator for Android dokumentáció](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android).

## <a name="install-android-studio"></a>Android Studio telepítése

[Android Studio](https://developer.android.com/studio)letöltéséhez és telepítéséhez kövesse az alábbi utasításokat.

1. Navigáljon [Android Studio letöltési oldalra](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Ez a hely nem támogatja az Internet Explorert.
2. Kattintson a Windows (64 bites) végrehajtható Android Studio csomagra.
3. Olvassa el az előugró ablakban írt jogi feltételeket.  Ha készen áll a folytatásra, tekintse **meg az elolvastam és elfogadom a fenti feltételeket és kikötéseket** jelölőnégyzetet, majd kattintson a **Windows rendszerhez készült letöltési Android Studio** gombra.
4. A Android Studio telepítő végrehajtható fájljának letöltése után futtassa a végrehajtható fájlt.
5. A **Android Studio telepítő** telepítőjének **üdvözli a Android Studio** lapon kattintson a **tovább**gombra.
6. A **konfigurációs beállítások** lapon kattintson a **tovább**gombra.
7. A **Start menü mappájának kiválasztása** lapon kattintson a **telepítés**gombra.
8. Várjon, amíg a telepítés befejeződik.
9. A **telepítés kész** lapon kattintson a **tovább**gombra.
10. A **Android Studio telepítésének befejezése** lapon.  Kattintson a **Befejezés** gombra.
11. A Android Studio automatikusan elindul a telepítés befejeződése után.
12. Az **Android-beállítások importálása...** párbeszédpanelen válassza a **Ne importálja a beállításokat**lehetőséget. Kattintson az **OK** gombra.
13. A **Android Studio telepítővarázslójának** **kezdőlapján kattintson a** **tovább**gombra.
14. A **telepítés típusa** lapon válassza a **standard**lehetőséget. Kattintson a **Tovább** gombra.
15. A **felhasználói felület kiválasztása téma** lapon válassza a kívánt téma lehetőséget. Kattintson a **Tovább** gombra.
16. A **Beállítások ellenőrzése** lapon kattintson a **tovább**gombra.
17. Az **összetevők letöltése** lapon várjon, amíg az összes összetevő le nem töltődik.  Kattintson a **Befejezés** gombra.

    > [!IMPORTANT]
    > A HAXM telepítése várhatóan sikertelen lesz.  A HAXM nem támogatja a beágyazott virtualizálás használatát, ezért a jelen cikk korábbi részében telepítette a Visual Studio Emulator for Android alkalmazást.

18. Az **üdvözli a Android Studio** párbeszédablak megjelenik a telepítővarázsló befejezésekor.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>A Android Studio és a Visual Studio Emulator konfigurálása Android rendszerhez

Android Studio majdnem készen áll a használatra.  Továbbra is meg kell adni az Androidhoz készült Visual Studio Emulatort, amelyen telepítve van az Android SDK.  Így az Androidhoz készült Visual Studio alkalmazásban futó emulátorok a Android Studio hibakereséséhez üzembe helyezési célként jelennek meg.

Be kell állítania egy adott beállításkulcsot, amely közli a Visual Studio Emulatort az Androidhoz, ahol az Android SDK található.  A szükséges beállításkulcs beállításához futtassa az alábbi szkriptet.  Az alábbi PowerShell-szkript feltételezi az Android SDK alapértelmezett telepítési helyét.  Ha az Android SDK-t egy másik helyen telepítette, módosítsa a értéket a `$androidSdkPath` parancsfájl futtatása előtt.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Indítsa újra a Visual Studio Emulatort az Androidhoz, és Android Studio, hogy az új beállítás legyen használatban.

Indítsa el a szükséges verziót a Visual Studio Emulatorban.  Az Android-alkalmazás üzembe helyezési célpontként jelenik meg az Android Studióban.  A Android Studio projekt minimális verziójának támogatnia kell az Androidhoz készült Visual Studio Emulatorban futó verziót.  Most már készen áll a projektek létrehozására és hibakeresésére a Android Studio és a Visual Studio Emulator for Android használatával.  Ha problémák merülnek fel, tekintse meg az Android Emulator hibaelhárítását ismertető témakört.

## <a name="cost"></a>Költségek

Ha a labor költségeit szeretné megbecsülni, kövesse az alábbi példát.
Egy 25 tanulós osztály esetében 20 órányi ütemezett időponttal és 10 órányi feladattal vagy hozzárendeléssel kapcsolatban a labor díja  

25 tanuló \* (20 ütemezett + 10 kvóta) óra * 55 labor egység * 0,01 USD/óra = 412,5 USD

További részletek a díjszabásról: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>További lépések

A következő lépések közösek a laborok beállításához.

- [Sablon létrehozása és kezelése](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users)
