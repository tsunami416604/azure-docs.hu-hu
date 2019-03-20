---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: d8e249a788d80108d46a61f316b795ab04e541a2
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203515"
---
## <a name="setting-up-your-ios-application"></a>Az iOS-alkalmazás beállítása

Ez a szakasz részletesen bemutatja egy új projekt létrehozásához bemutatják, hogyan integrálható az iOS-alkalmazásokba (Swift) rendelkező *Bejelentkezés Microsoft-* , akkor kérdezheti le jogkivonatot igénylő webes API-k.

> Töltse le ezt a mintát XCode-projektben ehelyett inkább? [Töltse le a projekt](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip) , és ugorjon a [konfigurációs lépés](#register-your-application) konfigurálása a kódminta végrehajtása előtt.

## <a name="install-carthage-to-download-and-build-msal"></a>Töltse le és hozhat létre az MSAL Carthage telepítése

A Csomagkezelő Carthage használható az MSAL előzetes verzió ideje alatt – arra, hogy a könyvtár módosításához a Microsoft az fenntartásához integrálható az xcode-ban.

- Töltse le és telepítse a legújabb kiadását Carthage [Itt](https://github.com/Carthage/Carthage/releases "Carthage letöltési URL-cím").

## <a name="creating-your-application"></a>Az alkalmazás létrehozása

1. Nyissa meg az xcode-ban, és válassza ki **hozzon létre egy új Xcode-projektben**.
2. Válassza ki **iOS > egynézetes alkalmazás** válassza **tovább**.
3. Adja meg a termék nevét, és válassza ki **tovább**.
4. Adja meg az alkalmazás létrehozása, és kattintson a *létrehozása*

## <a name="build-the-msal-framework"></a>A MSAL keretrendszer összeállítása

Kövesse az alábbi lekéréséhez, és ezután hozhat létre az MSAL könyvtárakban Carthage legújabb verziójának utasításokat:

1. Nyissa meg a bash terminált, és nyissa meg az alkalmazás gyökérmappájában.
2. Másolás a alatt, és illessze be a bash terminálon "Cartfile" fájl létrehozásához:

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
Másolja és illessze be az alábbi. Ez a parancs lekéri a függőségek egy Carthage/kivételek mappába, majd összeállítja az MSAL könyvtár:
</li>
</ol>

```bash
carthage update
```

> A fenti folyamat segítségével töltse le és a Microsoft-hitelesítési tár (MSAL) hozhat létre. Az MSAL beszerzéséhez, gyorsítótárazási és védi az Azure Active Directory 2.0-s verziójú API-k elérésére használt felhasználói jogkivonatok frissítése kezeli.

## <a name="add-the-msal-framework-to-your-application"></a>A MSAL keretrendszer hozzáadása az alkalmazáshoz

1. Az xcode-ban nyissa meg a **általános** fülre.
2. Nyissa meg a **csatolt keretrendszerek és könyvtárak** szakaszt, és válassza **+**.
3. Válassza ki **más hozzáadása...** .
4. Válassza ki **Carthage > hozhat létre > iOS > MSAL.framework** majd **nyílt**. Megtekintheti az `MSAL.framework` hozzáadja a listához.
5. Nyissa meg a **hozhat létre fázisok** lapon jelölje be a **+** ikonra, és válassza ki **új futtatása parancsfájl fázis**.
6. Adja hozzá a következő tartalmát a *terület parancsfájl*:

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Adja hozzá a következőt <code>Input Files</code> kattintva <code>+</code>:
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>Az alkalmazás felhasználói felület létrehozása

A projektsablon részeként Main.storyboard fájl automatikusan kell létrehozni. Kövesse az alábbi utasításokat az alkalmazás felhasználói felületének létrehozásához:

1.  CTRL + kattintson `Main.storyboard` a helyi menü megjelenítéséhez, majd: `Open As` > `Source Code`
2.  Cserélje le a `<scenes>` csomópont az alábbi kódra:

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```
